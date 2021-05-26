---
title: $validateのプロファイルに対して FHIR リソースをAzure API for FHIR
description: $validateに対して FHIR リソースを作成する
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/06/2021
ms.author: ginle
ms.openlocfilehash: 2c367dbed14e0dba9a8a95a3ce2709d2415c7cd6
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110466703"
---
# <a name="how-to-validate-fhir-resources-against-profiles"></a>プロファイルに対して FHIR リソースを検証する方法

HL7 FHIR は、医療データを格納および交換するための標準的で相互運用可能な方法を定義します。 基本 FHIR 仕様内でも、FHIR が使用されているコンテキストに基づいて追加のルールまたは拡張機能を定義すると便利です。 このようなコンテキスト固有の FHIR の使用では **、FHIR** プロファイルが仕様の追加レイヤーに使用されます。

[FHIR プロファイルは](https://www.hl7.org/fhir/profiling.html) 、 として表されるリソースに対する制約や拡張機能などの追加コンテキストを記述します `StructureDefinition` 。 HL7 FHIR 標準では一連の基本リソースが定義され、これらの標準の基本リソースにはジェネリック定義があります。 FHIR プロファイルを使用すると、制約と拡張機能を使用してリソース定義を絞り込み、カスタマイズできます。

Azure API for FHIRを使用すると、プロファイルに対してリソースを検証して、リソースがプロファイルに準拠していないか確認できます。 この記事では、FHIR プロファイルの基本と、リソースの作成時および更新時にプロファイルに対するリソースの検証に を `$validate` 使用する方法について説明します。

## <a name="fhir-profile-the-basics"></a>FHIR プロファイル: 基本

プロファイルは、通常はリソースとして表される、リソースに追加のコンテキストを設定 `StructureDefinition` します。 `StructureDefinition` では、リソースの内容またはデータ型に関する一連の規則 (リソースに含むフィールドや、これらのフィールドが受け取る値など) を定義します。 たとえば、プロファイルでは、カーディナリティを制限したり (要素を排除するために最大カーディナリティを 0 に設定したり)、要素の内容を 1 つの固定値に制限したり、リソースに必要な拡張機能を定義したりすることができます。 また、既存のプロファイルに対して追加の制約を指定できます。 は `StructureDefinition` 、正規 URL で識別されます。

```rest
http://hl7.org/fhir/StructureDefinition/{profile}
```

ここで、 フィールド `{profile}` にプロファイルの名前を指定します。

次に例を示します。

- `http://hl7.org/fhir/StructureDefinition/patient-birthPlace` は、患者の出生時に登録された住所に関する情報を必要とする基本プロファイルです。
- `http://hl7.org/fhir/StructureDefinition/bmi` は、本文量インデックス (BMI) の観測値を表す方法を定義するもう 1 つの基本プロファイルです。
- `http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance` は、 `AllergyIntolerance` 患者に関連付けられているリソースの最小予想を設定し、拡張機能や値セットなどの必須フィールドを識別する US コアプロファイルです。

### <a name="base-profile-and-custom-profile"></a>基本プロファイルとカスタムプロファイル

プロファイルには、基本プロファイルとカスタムプロファイルの2種類があります。 基本プロファイルは、リソースが準拠する必要があるベースであり、 `StructureDefinition` やなどの基本リソースによって定義されてい `Patient` `Observation` ます。 たとえば、Body マスインデックス (BMI) プロファイルは `Observation` 次のように開始します。

```json
{
  "resourceType" : "StructureDefinition",
  "id" : "bmi",
...
}
```

カスタムプロファイルは、基本プロファイルに加えて、基本仕様に含まれていないリソースパラメーターを制限したり追加したりするための追加の制約のセットです。 カスタムプロファイルは、既存の基本リソースに制約と拡張を指定することによって独自のリソース定義をカスタマイズできるので便利です。 たとえば、性別に基づいてリソースインスタンスを表示するプロファイルを作成し、 `AllergyIntolerance` `Patient` その場合はプロファイルを持つ既存のプロファイルの上にカスタムプロファイルを作成することができ `Patient` `AllergyIntolerance` ます。

> [!NOTE]
> カスタムプロファイルは基本リソース上に構築する必要があり、基本リソースと競合することはできません。 たとえば、要素のカーディナリティが 1 ..1 の場合、カスタムプロファイルでオプションを設定することはできません。

カスタムプロファイルは、さまざまな実装ガイドでも指定されています。 いくつかの一般的な実装ガイドは次のとおりです。

|名前 |URL
|---- |----
米国コア |<https://www.hl7.org/fhir/us/core/>
青いボタン |<http://hl7.org/fhir/us/carin-bb/>
Da ヴィンチ支払人データ交換 |<http://hl7.org/fhir/us/davinci-pdex/>
Argonaut |<http://www.fhir.org/guides/argonaut/pd/>

## <a name="accessing-profiles-and-storing-profiles"></a>プロファイルへのアクセスとプロファイルの保存

### <a name="storing-profiles"></a>プロファイルの保存

プロファイルをサーバーに保存するには、次のように `POST` 要求します。

```rest
POST http://<your FHIR service base URL>/{Resource}
```

フィールドはに `{Resource}` よって置き換えられ、 `StructureDefinition` `StructureDefinition` リソース `POST` はまたはの形式でサーバーに対して使用され `JSON` `XML` ます。 たとえば、プロファイルを格納する場合は `us-core-allergyintolerance` 、次の操作を行います。

```rest
POST http://my-fhir-server.azurewebsites.net/StructureDefinition?url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance
```

US Core の大企業の容容プロファイルが格納され、取得される場所:

```json
{
    "resourceType" : "StructureDefinition",
    "id" : "us-core-allergyintolerance",
    "text" : {
        "status" : "extensions"
    },
    "url" : "http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance",
    "version" : "3.1.1",
    "name" : "USCoreAllergyIntolerance",
    "title" : "US  Core AllergyIntolerance Profile",
    "status" : "active",
    "experimental" : false,
    "date" : "2020-06-29",
        "publisher" : "HL7 US Realm Steering Committee",
    "contact" : [
    {
      "telecom" : [
        {
          "system" : "url",
          "value" : "http://www.healthit.gov"
        }
      ]
    }
  ],
    "description" : "Defines constraints and extensions on the AllergyIntolerance resource for the minimal set of data to query and retrieve allergy information.",

...
```

ほとんどのプロファイルのリソースの種類は ですが、用語リソースである と の種類 `StructureDefinition` `ValueSet` `CodeSystem` を [指定](http://hl7.org/fhir/terminologies.html) することもできます。 たとえば、JSON 形式のプロファイルの場合、サーバーは、 の場合と同様に、プロファイルに割り当てられた格納されたプロファイル `POST` `ValueSet` `id` を返します `StructureDefinition` 。 条件重大度プロファイルをアップロードするときに取得する例を次に [示します。](https://www.hl7.org/fhir/valueset-condition-severity.html) このプロファイルでは、条件/診断の重大度プロファイルの条件を指定します。

```json
{
    "resourceType": "ValueSet",
    "id": "35ab90e5-c75d-45ca-aa10-748fefaca7ee",
    "meta": {
        "versionId": "1",
        "lastUpdated": "2021-05-07T21:34:28.781+00:00",
        "profile": [
            "http://hl7.org/fhir/StructureDefinition/shareablevalueset"
        ]
    },
    "text": {
        "status": "generated"
    },
    "extension": [
        {
            "url": "http://hl7.org/fhir/StructureDefinition/structuredefinition-wg",
            "valueCode": "pc"
        }
    ],
    "url": "http://hl7.org/fhir/ValueSet/condition-severity",
    "identifier": [
        {
            "system": "urn:ietf:rfc:3986",
            "value": "urn:oid:2.16.840.1.113883.4.642.3.168"
        }
    ],
    "version": "4.0.1",
    "name": "Condition/DiagnosisSeverity",
    "title": "Condition/Diagnosis Severity",
    "status": "draft",
    "experimental": false,
    "date": "2019-11-01T09:29:23+11:00",
    "publisher": "FHIR Project team",
...
```

が で、プロファイルの が 型であるも指定されているの `resourceType` `ValueSet` `url` を確認できます `ValueSet` `"http://hl7.org/fhir/ValueSet/condition-severity"` 。

### <a name="viewing-profiles"></a>プロファイルの表示

要求を使用して、サーバー内の既存のカスタム プロファイルにアクセス `GET` できます。 実装ガイドの有効な正規 URL を持つプロファイルなど、すべての有効なプロファイルには、次のクエリを実行してアクセスできる必要があります。

```rest
GET http://<your FHIR service base URL>/StructureDefinition?url={canonicalUrl} 
```

フィールドが `{canonicalUrl}` プロファイルの正規 URL に置き換えられる場所。

たとえば、US Core リソース プロファイルを表示する `Goal` 場合は、次のようになります。

```rest
GET http://my-fhir-server.azurewebsites.net/StructureDefinition?url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-goal
```

これにより、US `StructureDefinition` Core Goal プロファイルのリソースが返されます。これは次のように開始されます。

```json
{
  "resourceType" : "StructureDefinition",
  "id" : "us-core-goal",
  "url" : "http://hl7.org/fhir/us/core/StructureDefinition/us-core-goal",
  "version" : "3.1.1",
  "name" : "USCoreGoalProfile",
  "title" : "US Core Goal Profile",
  "status" : "active",
  "experimental" : false,
  "date" : "2020-07-21",
  "publisher" : "HL7 US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "url",
          "value" : "http://www.healthit.gov"
        }
      ]
    }
  ],
  "description" : "Defines constraints and extensions on the Goal resource for the minimal set of data to query and retrieve a patient's goal(s).",
...
```

FHIR サーバーは基本プロファイルのインスタンスを返しませんが、HL7 Web サイトで簡単に `StructureDefinition` 見つくことができます。次に例を示します。

- `http://hl7.org/fhir/Observation.profile.json.html`
- `http://hl7.org/fhir/Patient.profile.json.html`


### <a name="profiles-in-the-capability-statement"></a>機能ステートメントのプロファイル

には、構造体の定義や値セットなど、サーバー機能のステートメントとして使用できる FHIR サーバーのすべての動作 `Capability Statement` が一覧表示されます。 Azure API for FHIR機能ステートメントを更新し、アップロードされたプロファイルと格納されたプロファイルに関する情報を次の形式で更新します。

- `CapabilityStatement.rest.resource.profile`
- `CapabilityStatement.rest.resource.supportedProfile`

これらは、カーディナリティ、バインド、拡張機能、その他の制限に関する制約を含め、リソースの全体的なサポートを記述するプロファイルのすべての仕様を示します。 そのため、 `POST` の形式のプロファイル `StructureDefinition` と `GET` リソースメタデータを使用して完全な機能ステートメントを確認すると、 `supportedProfiles` アップロードしたプロファイルのすべての詳細がパラメーターの横に表示されます。

たとえば、 `POST` 米国コアの患者プロファイルの場合、次のようになります。

```json
{
  "resourceType": "StructureDefinition",
  "id": "us-core-patient",
  "url": "http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient",
  "version": "3.1.1",
  "name": "USCorePatientProfile",
  "title": "US Core Patient Profile",
  "status": "active",
  "experimental": false,
  "date": "2020-06-27",
  "publisher": "HL7 US Realm Steering Committee",
...
```

`GET`次の要求を送信し `metadata` ます。

```rest
GET http://<your FHIR service base URL>/metadata
```

`CapabilityStatement`FHIR サーバーにアップロードした米国コア患者プロファイルに関する次の情報を含むが返されます。

```json
...
{
    "type": "Patient",
    "profile": "http://hl7.org/fhir/StructureDefinition/Patient",
    "supportedProfile":[
        "http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient"
    ],
...
```

## <a name="validating-resources-against-the-profiles"></a>プロファイルに対するリソースの検証

やなどの FHIR リソースは、 `Patient` `Observation` 特定のプロファイルへの準拠を表すことができます。 これにより、FHIR サーバーは、関連付けられたプロファイルまたは指定されたプロファイルに対して、指定されたリソースを **検証** することができます。 プロファイルに対してリソースを検証するということは、リソースがプロファイルに準拠しているかどうかを確認することです。これには、「」 `Resource.meta.profile` または「実装ガイド」に記載されている仕様

リソースを検証する方法は2つあります。 最初に、 `$validate` FHIR サーバーに既に存在するリソースに対して操作を使用できます。 次に、 `POST` リソースまたは操作の一部としてサーバーに対して行うことができ `Update` `Create` ます。 どちらの場合も、FHIR サーバー構成を使用して、リソースが目的のプロファイルに準拠していない場合の対処方法を決定できます。

### <a name="using-validate"></a>$validate の使用

操作は、指定された `$validate` プロファイルが有効かどうか、およびリソースが指定されたプロファイルに準拠しているかどうかを確認します。 [HL7 FHIR 仕様](https://www.hl7.org/fhir/resource-operation-validate.html)に記載されているように、やなどのを指定することもでき `mode` `$validate` `create` `update` ます。

- `create`: サーバーは、プロファイルコンテンツが既存のリソースから一意であること、および新しいリソースとして作成できることを確認します。
- `update`: プロファイルが、指名された既存のリソースに対する更新であることを確認します (変更できないフィールドに変更が加えられていないなど)。

サーバーは、常にを `OperationOutcome` 検証結果として返します。

#### <a name="validating-an-existing-resource"></a>既存のリソースの検証

既存のリソースを検証するには、要求でを使用し `$validate` `GET` ます。

```rest
GET http://<your FHIR service base URL>/{resource}/{resource ID}/$validate
```

次に例を示します。

```rest
GET http://my-fhir-server.azurewebsites.net/Patient/a6e11662-def8-4dde-9ebc-4429e68d130e/$validate
```

上の例では、既存のリソース を検証 `Patient` します `a6e11662-def8-4dde-9ebc-4429e68d130e` 。 有効な場合は、次 `OperationOutcome` のような が表示されます。

```json
{
    "resourceType": "OperationOutcome",
    "issue": [
        {
            "severity": "information",
            "code": "informational",
            "diagnostics": "All OK"
        }
    ]
}
```

リソースが有効でない場合は、エラー コードと、リソースが無効な理由の詳細を含むエラー メッセージが表示されます。 または エラーは、検証自体を実行できませんでした。また、リソースが有効かどうか `4xx` `5xx` は不明です。 エラー メッセージ `OperationOutcome` で返される例は次のようになります。

```json
{
    "resourceType": "OperationOutcome",
    "issue": [
        {
            "severity": "error",
            "code": "invalid",
            "details": {
                "coding": [
                    {
                        "system": "http://hl7.org/fhir/dotnet-api-operation-outcome",
                        "code": "1028"
                    }
                ],
                "text": "Instance count for 'Patient.identifier.value' is 0, which is not within the specified cardinality of 1..1"
            },
            "location": [
                "Patient.identifier[1]"
            ]
        },
        {
            "severity": "error",
            "code": "invalid",
            "details": {
                "coding": [
                    {
                        "system": "http://hl7.org/fhir/dotnet-api-operation-outcome",
                        "code": "1028"
                    }
                ],
                "text": "Instance count for 'Patient.gender' is 0, which is not within the specified cardinality of 1..1"
            },
            "location": [
                "Patient"
            ]
        }
    ]
}
```

上記の例では、リソースは、指定されたプロファイルに準拠していなかったので、患者識別子の値と `Patient` 性別が必要でした。

パラメーターとしてプロファイルを指定する場合は、検証するプロファイルの正規 URL を指定できます。たとえば、US Core プロファイルを使用した次の例と、 の基本プロファイル `Patient` を指定します `heartrate` 。

```rest
GET http://<your FHIR service base URL>/{Resource}/{Resource ID}/$validate?profile={canonicalUrl}
```

次に例を示します。

```rest
GET http://my-fhir-server.azurewebsites.net/Patient/a6e11662-def8-4dde-9ebc-4429e68d130e/$validate?profile=http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient
GET http://my-fhir-server.azurewebsites.net/Observation/12345678/$validate?profile=http://hl7.org/fhir/StructureDefinition/heartrate
```

#### <a name="validating-a-new-resource"></a>新しいリソースの検証

サーバーにアップロードする新しいリソースを検証する場合は、次の要求を実行 `POST` できます。

```rest
POST http://<your FHIR service base URL>/{Resource}/$validate
```

次に例を示します。

```rest
POST http://my-fhir-server.azurewebsites.net/Patient/$validate 
```

この要求では、要求ペイロードで指定する新しいリソースが JSON 形式でも XML 形式でも作成され、アップロードされたリソースが検証されます。 次に、新しい `OperationOutcome` リソースの検証の結果として が返されます。

### <a name="validate-on-resource-create-or-resource-update"></a>リソースの CREATE またはリソースの更新に関する検証

リソースの CREATE や UPDATE など、リソースを検証する場合に選択できます。 これは、サーバー構成設定の の下で指定できます `CoreFeatures` 。

```json
{
   "FhirServer": {
      "CoreFeatures": {
            "ProfileValidationOnCreate": true,
            "ProfileValidationOnUpdate": false
        }
}
```

リソースが指定された に準拠し、プロファイルがシステムに存在する場合、サーバーは上記の構成設定に従 `Resource.meta.profile` って機能します。 指定されたプロファイルがサーバーに存在しない場合、検証要求は無視され、 に残されます `Resource.meta.profile` 。
検証は通常、コストのかかる操作なので、通常はテスト サーバーまたはリソースの小さなサブセットでのみ実行されます。そのため、検証操作をサーバー側でオンまたはオフにするこれらの方法を使用することが重要です。 サーバー構成でリソースの作成/更新の検証を無効にするように指定した場合、ユーザーは `header` 作成/更新要求のでその動作をオーバーライドできます。

```rest
x-ms-profile-validation: true
```

## <a name="next-steps"></a>次のステップ

この記事では、FHIR プロファイルと、$validate を使用してプロファイルに対してリソースを検証する方法について説明しました。 FHIR のその他のサポートされている機能の Azure API の詳細については、次を参照してください。

>[!div class="nextstepaction"]
>[FHIR でサポートされる機能](fhir-features-supported.md)
