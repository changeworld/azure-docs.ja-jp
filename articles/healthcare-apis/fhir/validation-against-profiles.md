---
title: FHIR の Azure API のプロファイルに対して FHIR リソースを $validate する
description: プロファイルに対して FHIR リソースを $validate する
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/06/2021
ms.author: ginle
ms.openlocfilehash: 679d8b2ac86ec63d33fcd5cd069a3135d33ab981
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2021
ms.locfileid: "109657051"
---
# <a name="how-to-validate-fhir-resources-against-profiles"></a>プロファイルに対して FHIR リソースを検証する方法

HL7 FHIR は、医療データを格納して交換するための、標準的で相互運用可能な方法を定義します。 基本 FHIR 仕様内であっても、FHIR が使用されているコンテキストに基づいて追加のルールまたは拡張機能を定義すると便利な場合があります。 FHIR のそのようなコンテキスト固有の用途では、仕様の追加レイヤーに **fhir プロファイル** が使用されます。

[Fhir プロファイル](https://www.hl7.org/fhir/profiling.html) は、として表されるリソースに対する制約や拡張機能などの追加のコンテキストを記述し `StructureDefinition` ます。 HL7 FHIR 標準では、基本リソースのセットを定義します。これらの標準基本リソースには、汎用定義があります。 FHIR プロファイルを使用すると、制約と拡張機能を使用してリソース定義を絞り込んだり、カスタマイズしたりすることができます。

Azure API for FHIR を使用すると、プロファイルに対してリソースを検証して、リソースがプロファイルに準拠しているかどうかを確認できます。 この記事では、FHIR プロファイルの基本と、 `$validate` リソースを作成および更新するときに、を使用してプロファイルに対してリソースを検証する方法について説明します。

## <a name="fhir-profile-the-basics"></a>FHIR プロファイル: 基本

プロファイルは、リソースに追加のコンテキストを設定します。通常はリソースとして表さ `StructureDefinition` れます。 `StructureDefinition` リソースのコンテンツまたはデータ型に関する一連の規則を定義します。たとえば、リソースに含まれるフィールドや、これらのフィールドが取り得る値などです。 たとえば、プロファイルでカーディナリティを制限できます (たとえば、最大カーディナリティを0に設定して要素を除外します)。要素の内容を1つの固定値に制限するか、リソースに必要な拡張機能を定義します。 また、既存のプロファイルに対する追加の制約を指定することもできます。 は、 `StructureDefinition` 正規の URL によって識別されます。

```rest
http://hl7.org/fhir/StructureDefinition/{profile}
```

このフィールドでは、 `{profile}` プロファイルの名前を指定します。

次に例を示します。

- `http://hl7.org/fhir/StructureDefinition/patient-birthPlace` は、患者の登録済みの住所に関する情報を必要とする基本プロファイルです。
- `http://hl7.org/fhir/StructureDefinition/bmi` は、本体のマスインデックス (BMI) の観測を表す方法を定義する別の基本プロファイルです。
- `http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance` は、患者に関連付けられているリソースに対する最小の期待値を設定し、拡張や値セットなどの必須フィールドを識別する US Core `AllergyIntolerance` プロファイルです。

### <a name="base-profile-and-custom-profile"></a>基本プロファイルとカスタム プロファイル

プロファイルには、基本プロファイルとカスタム プロファイルの 2 種類があります。 基本プロファイルは、リソースが準拠する必要があるベースであり、 や などの基本リソース `StructureDefinition` によって定義 `Patient` されています `Observation` 。 たとえば、Body Mass Index (BMI) プロファイルは `Observation` 次のように開始されます。

```json
{
  "resourceType" : "StructureDefinition",
  "id" : "bmi",
...
}
```

カスタム プロファイルは、基本プロファイルの上に追加の制約のセットで、基本仕様の一部ではないリソース パラメーターを制限または追加します。 カスタム プロファイルは、既存の基本リソースの制約と拡張機能を指定することで、独自のリソース定義をカスタマイズすることができるため便利です。 たとえば、性別に基づいてリソース インスタンスを表示するプロファイルを作成できます。その場合は、プロファイルを持つ既存のプロファイルの上にカスタム プロファイルを `AllergyIntolerance` `Patient` `Patient` 作成 `AllergyIntolerance` します。

> [!NOTE]
> カスタム プロファイルは、基本リソースの上に構築する必要があります。また、基本リソースと競合することはできません。 たとえば、要素のカーディナリティが 1..1 の場合、カスタム プロファイルで省略可能にすることはできません。

カスタム プロファイルは、さまざまな実装ガイドでも指定されています。 一般的な実装ガイドを次に示します。

|名前 |URL
|---- |----
Us Core |<https://www.hl7.org/fhir/us/core/>
CARIN Blue Button |<http://hl7.org/fhir/us/carin-bb/>
Da Vinci Payer Data Exchange |<http://hl7.org/fhir/us/davinci-pdex/>
アルゴノウト |<http://www.fhir.org/guides/argonaut/pd/>

## <a name="accessing-profiles-and-storing-profiles"></a>プロファイルへのアクセスとプロファイルの格納

### <a name="viewing-profiles"></a>プロファイルの表示

要求を使用して、サーバー内の既存のカスタム プロファイルにアクセス `GET` できます。 実装ガイドの有効な正規 URL を持つプロファイルなど、すべての有効なプロファイルには、次のクエリを実行してアクセスできる必要があります。

```rest
GET http://<your FHIR service base URL>/StructureDefinition?url={canonicalUrl} 
```

ここで、フィールドは `{canonicalUrl}` 実際のプロファイルの URL に置き換えられます。

たとえば、US Core リソースプロファイルを表示するには、次のように入力し `Goal` ます。

```rest
GET http://my-fhir-server.azurewebsites.net/StructureDefinition?url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-goal
```

これにより、 `StructureDefinition` US Core 目標プロファイルのリソースが返されます。次のようになります。

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

FHIR サーバーは `StructureDefinition` 基本プロファイルのインスタンスを返しませんが、HL7 の web サイトで簡単に見つけることができます。次に例を示します。

- `http://hl7.org/fhir/Observation.profile.json.html`
- `http://hl7.org/fhir/Patient.profile.json.html`

### <a name="storing-profiles"></a>プロファイルの保存

プロファイルをサーバーに保存するには、次のように `POST` 要求します。

```rest
POST http://<your FHIR service base URL>/{Resource}
```

フィールドはに `{Resource}` よって置き換えられ、 `StructureDefinition` `StructureDefinition` リソース `POST` はまたはの形式でサーバーに対して使用され `JSON` `XML` ます。

ほとんどのプロファイルにはリソースの種類があり `StructureDefinition` ますが、型と型 ( `ValueSet` `CodeSystem` [用語](http://hl7.org/fhir/terminologies.html) リソース) である場合もあります。 たとえば、JSON 形式のプロファイルの場合、サーバーは、と同じように、 `POST` `ValueSet` プロファイルに割り当てられたを持つ格納済みプロファイルを返し `id` `StructureDefinition` ます。 条件と診断の重要度に関する条件を指定する [条件重大度](https://www.hl7.org/fhir/valueset-condition-severity.html) プロファイルをアップロードすると、次のような例が得られます。

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
        "status": "generated",
        "div": "<div>!-- Snipped for Brevity --></div>"
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

### <a name="profiles-in-the-capability-statement"></a>機能ステートメントのプロファイル

には、 `Capability Statement` 構造の定義や値セットなど、サーバー機能のステートメントとして使用される FHIR サーバーのすべての動作が一覧表示されます。 Azure API for FHIR は、アップロードおよび格納されたプロファイルに関する情報を含む機能ステートメントを次の形式で更新します。

- `CapabilityStatement.rest.resource.profile`
- `CapabilityStatement.rest.resource.supportedProfile`

これらには、カーディナリティ、バインド、拡張機能、またはその他の制限に対する制約を含め、リソースの全体的なサポートを説明するプロファイルの仕様がすべて表示されます。 そのため、 `POST` の形式のプロファイル `StructureDefinition` と `GET` リソースメタデータを使用して完全な機能ステートメントを確認すると、 `supportedProfiles` アップロードしたプロファイルのすべての詳細がパラメーターの横に表示されます。

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

FHIR サーバーにアップロードした US Core Patient プロファイルに関する次の情報を含む が `CapabilityStatement` 返されます。

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

や などの FHIR リソース `Patient` は `Observation` 、特定のプロファイルへの準拠を表します。 これにより、FHIR サーバーは、 **関連付けられている** プロファイルまたは指定したプロファイルに対して特定のリソースを検証できます。 プロファイルに対してリソースを検証すると、実装ガイドに記載されている仕様や実装ガイドに記載されている仕様を含め、リソースがプロファイルに準拠していないか `Resource.meta.profile` 確認できます。

リソースを検証するには、2 つの方法があります。 最初に `$validate` 、FHIR サーバーに既に存在するリソースに対して操作を使用できます。 次に、 `POST` リソースまたは操作の一部としてサーバーに `Update` 対して実行 `Create` できます。 どちらの場合も、FHIR サーバー構成を使用して、リソースが目的のプロファイルに準拠していない場合に実行する操作を決定できます。

### <a name="using-validate"></a>$validate の$validate

この `$validate` 操作では、指定されたプロファイルが有効かどうか、およびリソースが指定されたプロファイルに準拠するかどうかを確認します。 [HL7 FHIR](https://www.hl7.org/fhir/resource-operation-validate.html)仕様で説明したように、 や など、 に を `mode` `$validate` 指定 `create` できます `update` 。

- `create`: サーバーは、プロファイルの内容が既存のリソースから一意であり、新しいリソースとして作成可能な内容を確認します
- `update`: プロファイルが、指定された既存のリソースに対する更新プログラムである (変更できないフィールドに変更が加えらないなど) チェックします。

サーバーは常に検証結果 `OperationOutcome` として を返します。

#### <a name="validating-an-existing-resource"></a>既存のリソースの検証

既存のリソースを検証するには、要求で `$validate` を使用 `GET` します。

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

リソースが有効でない場合は、エラー コードと、リソースが無効な理由の詳細を含むエラー メッセージが表示されます。 `4xx`または `5xx` エラーは、検証自体を実行できず、リソースが有効かどうか不明であることを意味します。 `OperationOutcome`エラーメッセージと共に返される例は、次のようになります。

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

上記の例では、リソースが指定されたプロファイルに準拠していませんでした。このプロファイルには、 `Patient` 患者の識別子の値と性別が必要です。

プロファイルをパラメーターとして指定する場合は、検証対象のプロファイルの正規の URL を指定できます。たとえば、次の例では、US Core `Patient` profile と基本プロファイルを使用し `heartrate` ます。

```rest
GET http://<your FHIR service base URL>/{Resource}/{Resource ID}/$validate?profile={canonicalUrl}
```

次に例を示します。

```rest
GET http://my-fhir-server.azurewebsites.net/Patient/a6e11662-def8-4dde-9ebc-4429e68d130e/$validate?profile=http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient
GET http://my-fhir-server.azurewebsites.net/Observation/12345678/$validate?profile=http://hl7.org/fhir/StructureDefinition/heartrate
```

#### <a name="validating-a-new-resource"></a>新しいリソースの検証

サーバーにアップロードする新しいリソースを検証する場合は、次のように `POST` 要求できます。

```rest
POST http://<your FHIR service base URL>/{Resource}/$validate
```

次に例を示します。

```rest
POST http://my-fhir-server.azurewebsites.net/Patient/$validate 
```

この要求により、要求ペイロードで指定する新しいリソースが JSON 形式か XML 形式かにかかわらず作成され、アップロードされたリソースが検証されます。 次に、 `OperationOutcome` 新しいリソースの検証の結果としてが返されます。

### <a name="validate-on-resource-create-or-resource-update"></a>リソースの作成時またはリソースの更新時に検証する

リソースを検証するタイミング (リソースの作成時や更新時など) を選択できます。 これは、[サーバーの構成] 設定で、次のように指定でき `CoreFeatures` ます。

```json
{
   "FhirServer": {
      "CoreFeatures": {
            "ProfileValidationOnCreate": true,
            "ProfileValidationOnUpdate": false
        }
}
```

リソースが指定されたに準拠してい `Resource.meta.profile` て、プロファイルがシステムに存在する場合、サーバーは、上記の構成設定に従って動作します。 指定されたプロファイルがサーバーに存在しない場合、検証要求は無視され、のままになり `Resource.meta.profile` ます。
通常、検証は高価な操作であるため、通常はテストサーバーまたはリソースの小さなサブセットでのみ実行されます。そのため、サーバー側で検証操作をオンまたはオフにするには、これらの方法を使用することが重要です。 サーバー構成でリソースの作成/更新の検証を無効にするように指定した場合、ユーザーは `header` 作成/更新要求のでその動作をオーバーライドできます。

```rest
x-ms-profile-validation: true
```

## <a name="next-steps"></a>次の手順

この記事では、FHIR プロファイルと、$validate を使用してプロファイルに対してリソースを検証する方法について説明しました。 サポートされている他Azure API for FHIR機能の詳細については、以下を参照してください。

>[!div class="nextstepaction"]
>[FHIR でサポートされる機能](fhir-features-supported.md)
