---
title: Azure の医療 Api で FHIR サービスのプロファイルに対して FHIR リソースを $validate する
description: FHIR サービスのプロファイルに対して FHIR リソースを $validate する
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2021
ms.author: cavoeg
ms.openlocfilehash: b52389b6007c436614840a9bad568a0e81cf7fa2
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779572"
---
# <a name="how-to-validate-fhir-resources-against-profiles"></a>プロファイルに対して FHIR リソースを検証する方法

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

HL7 FHIR は、医療データを格納および交換するための標準的で相互運用可能な方法を定義したものです。 基本の FHIR 仕様の範囲内であっても、FHIR が使用されるコンテキストに基づいて追加のルールまたは拡張を定義すると便利な場合があります。 このようなコンテキスト固有の FHIR 使用法には、仕様の追加レイヤーとして **FHIR プロファイル** が使用されます。

[FHIR プロファイル](https://www.hl7.org/fhir/profiling.html)はリソースに関する制約や拡張などの追加コンテキストを記述したもので、`StructureDefinition` として表されます。 HL7 FHIR 標準には一連の基本リソースが定義されており、これらの標準の基本リソースはジェネリックな定義になっています。 FHIR プロファイルを使用すると、制約と拡張を使用してリソース定義を絞り込み、カスタマイズできます。

Azure の医療 Api (FHIR サービスと呼ばれます) の FHIR サービスは、プロファイルに対してリソースを検証して、リソースがプロファイルに準拠しているかどうかを確認できます。 この記事では、FHIR プロファイルの基本と、リソースの作成時および更新時に、プロファイルに照らしたリソースの検証のために `$validate` を使用する方法について説明します。

## <a name="fhir-profile-the-basics"></a>FHIR プロファイル: 基本

プロファイルによってリソースに追加のコンテキストを設定します。通常それは `StructureDefinition` リソースとして表されます。 `StructureDefinition` を使用して、リソースの内容またはデータ型に関する一連のルール (リソースに含まれるフィールドや、これらのフィールドに設定できる値など) を定義します。 たとえば、プロファイルでカーディナリティを制限 (たとえば要素を除外するために最大カーディナリティを 0 に設定) することや、要素の内容を 1 個の固定値に制限すること、またはリソースに必要な拡張を定義することができます。 また、既存のプロファイルに対して追加の制約を指定することもできます。 `StructureDefinition` は、正規 URL で識別されます。

```rest
http://hl7.org/fhir/StructureDefinition/{profile}
```

ここで、`{profile}` フィールドにはプロファイルの名前を指定します。

次に例を示します。

- `http://hl7.org/fhir/StructureDefinition/patient-birthPlace` は、患者の出生地を表す登録済み住所の情報を必要とする基本プロファイルです。
- `http://hl7.org/fhir/StructureDefinition/bmi` は、肥満度指数 (BMI) の観測値を表す方法を定義するもう 1 つの基本プロファイルです。
- `http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance` は、患者に関連した `AllergyIntolerance` リソースに最小限必要な事項を設定し、拡張や値セットなどの必須フィールドを定めた US Core プロファイルです。

リソースがいずれかのプロファイルに準拠している場合、そのプロファイルはリソースのフィールド `profile` 内に指定されます。

```json
{
  "resourceType" : "Patient",
  "id" : "ExamplePatient1",
  "meta" : {
    "lastUpdated" : "2020-10-30T09:48:01.8512764-04:00",
    "source" : "Organization/PayerOrganizationExample1",
    "profile" : [
      "http://hl7.org/fhir/us/carin-bb/StructureDefinition/C4BB-Patient"
    ]
  },
```

### <a name="base-profile-and-custom-profile"></a>基本プロファイルとカスタム プロファイル

プロファイルには、基本プロファイルとカスタム プロファイルの 2 種類があります。 基本プロファイルは、リソースが準拠する必要がある基本の `StructureDefinition` であり、`Patient` や `Observation` などの基本リソースによって定義されています。 たとえば、肥満度指数 (BMI) の `Observation` プロファイルは次のように始まります。

```json
{
  "resourceType" : "StructureDefinition",
  "id" : "bmi",
...
}
```

カスタム プロファイルは、基本プロファイルをベースにした追加の制約のセットであり、基本仕様の一部ではないリソース パラメーターを制約または追加します。 カスタム プロファイルは、既存の基本リソースに対する制約と拡張を指定することによって自分独自のリソース定義をカスタマイズできるため、便利です。 たとえば、`Patient` の性別に基づいて `AllergyIntolerance` リソースのインスタンスを示すプロファイルを作成する必要がある場合は、既存の `Patient` プロファイルをベースに `AllergyIntolerance` プロファイルを使用してカスタム プロファイルを作成します。

> [!NOTE]
> カスタム プロファイルは、基本リソースをベースにして作成する必要があり、基本リソースと競合することはできません。 たとえば、要素のカーディナリティが 1..1 の場合、カスタム プロファイルでそれを省略可能にすることはできません。

カスタムプロファイルは、さまざまな実装ガイドでも指定されています。 一般的な実装ガイドを次に示します。

|名前 |URL
|---- |----
Us Core |<https://www.hl7.org/fhir/us/core/>
CARIN Blue Button |<http://hl7.org/fhir/us/carin-bb/>
Da Vinci Payer Data Exchange |<http://hl7.org/fhir/us/davinci-pdex/>
Argonaut |<http://www.fhir.org/guides/argonaut/pd/>

## <a name="accessing-profiles-and-storing-profiles"></a>プロファイルへのアクセスとプロファイルの格納

### <a name="storing-profiles"></a>プロファイルの格納

サーバーにプロファイルを格納する場合は、`POST` 要求を行うことができます。

```rest
POST http://<your FHIR service base URL>/StructureDefinition
```

たとえば、`us-core-allergyintolerance` プロファイルを格納する場合は、次のようにします。

```rest
POST https://myworkspace-myfhirserver.fhir.azurehealthcareapis.com/StructureDefinition?url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance
```

ここで US Core Allergy Intolerance プロファイルを格納および取得します。

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

ほとんどのプロファイルはリソースの種類が `StructureDefinition` ですが、`ValueSet` および `CodeSystem` の種類であることもあります。これらは[用語](http://hl7.org/fhir/terminologies.html)のリソースです。 たとえば、JSON 形式で `ValueSet` プロファイルを `POST` すると、サーバーからは、`StructureDefinition` の場合と同様に、格納されているプロファイルがプロファイルに割り当てられた `id` と共に返されます。 病状または診断の重症度の段階を指定した[病状重症度](https://www.hl7.org/fhir/valueset-condition-severity.html)プロファイルをアップロードしたときに返される例を次に示します。

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

`resourceType` は `ValueSet` であり、プロファイルの `url` によって、これが種類 `ValueSet` の `"http://hl7.org/fhir/ValueSet/condition-severity"` であることも指定されています。

### <a name="viewing-profiles"></a>プロファイルの表示

`GET` 要求を使用して、サーバーにある既存のカスタム プロファイルにアクセスできます。 実装ガイドにある有効な正規 URL を持つプロファイルなど、すべての有効なプロファイルには、次のクエリを実行してアクセスできます。

```rest
GET http://<your FHIR service base URL>/StructureDefinition?url={canonicalUrl} 
```

ここで、フィールド `{canonicalUrl}` は、ご自分のプロファイルの正規 URL で置き換えます。

たとえば、US Core `Goal` リソース プロファイルを表示する場合は、次のようにします。

```rest
GET https://myworkspace-myfhirserver.fhir.azurehealthcareapis.com/StructureDefinition?url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-goal
```

これにより、US Core Goal プロファイルの `StructureDefinition` リソースが返されます。これは次のように始まります。

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

FHIR サービスは `StructureDefinition` 基本プロファイルのインスタンスを返しませんが、HL7 web サイトで簡単に見つけることができます。次に例を示します。

- `http://hl7.org/fhir/Observation.profile.json.html`
- `http://hl7.org/fhir/Patient.profile.json.html`


### <a name="profiles-in-the-capability-statement"></a>機能ステートメントのプロファイル

には、 `Capability Statement` 構造の定義や値セットなど、サーバー機能のステートメントとして使用される FHIR サービスのすべての動作が一覧表示されます。 FHIR サービスは、アップロードおよび保存されたプロファイルに関する情報を次の形式で使用して、機能ステートメントを更新します。

- `CapabilityStatement.rest.resource.profile`
- `CapabilityStatement.rest.resource.supportedProfile`

これらは、カーディナリティに関する制約、バインド、拡張、その他の制限を含め、リソースに関するサポート全般を記述するプロファイルのすべての仕様を示します。 したがって、`StructureDefinition` の形式のプロファイルを `POST` し、完全な機能ステートメントを確認するためにリソース メタデータを `GET` すると、`supportedProfiles` パラメーターの横に、アップロードしたプロファイルに関するすべての詳細が表示されます。

たとえば、US Core Patient プロファイルの `POST` を行う場合は、次のように始まります。

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

`metadata` の `GET` 要求を送信します。

```rest
GET http://<your FHIR service base URL>/metadata
```

FHIR サーバーにアップロードした US Core Patient プロファイルに関する次の情報を含む `CapabilityStatement` が返されます。

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

## <a name="validating-resources-against-the-profiles"></a>プロファイルに照らしたリソースの検証

`Patient` や `Observation` などの FHIR リソースによって、特定のプロファイルへの準拠を表すことができます。 これにより、FHIR サービスは、関連付けられているプロファイルまたは指定されたプロファイルに対して、指定されたリソースを **検証** できます。 プロファイルに照らしてリソースを検証すると、ご自分のリソースが、`Resource.meta.profile` または実装ガイドに記述されている仕様を含めてプロファイルに準拠していることを確認できます。

ご自分のリソースを検証するには、2 つの方法があります。 最初に、 `$validate` FHIR サービスに既に存在するリソースに対して操作を使用できます。 次に、リソースの `Update` または `Create` 操作の一部としてサーバーに `POST` できます。 どちらの場合も、FHIR サービス構成を使用して、リソースが目的のプロファイルに準拠していない場合の対処方法を決定できます。

### <a name="using-validate"></a>$validate の使用

`$validate` は、指定されたプロファイルが有効かどうか、およびリソースが指定されたプロファイルに準拠しているかどうかを確認する操作です。 [HL7 FHIR 仕様](https://www.hl7.org/fhir/resource-operation-validate.html)に規定されているとおり、`$validate` の `mode` (`create`、`update` など) も指定できます。

- `create`: プロファイルの内容が既存のリソースと重複しておらず、新しいリソースとしての作成が許容されることがサーバーで確認されます
- `update`: プロファイルが、指定された既存のリソースに対する更新であること (変更できないフィールドに変更が加えられていないなど) が確認されます

サーバーからは検証結果として常に `OperationOutcome` が返されます。

#### <a name="validating-an-existing-resource"></a>既存のリソースの検証

既存のリソースを検証するには、`GET` 要求で `$validate` を使用します。

```rest
GET http://<your FHIR service base URL>/{resource}/{resource ID}/$validate
```

次に例を示します。

```rest
GET https://myworkspace-myfhirserver.fhir.azurehealthcareapis.com/Patient/a6e11662-def8-4dde-9ebc-4429e68d130e/$validate
```

上の例では、既存の `Patient` リソース `a6e11662-def8-4dde-9ebc-4429e68d130e` を検証しています。 有効な場合は、次のような `OperationOutcome` が表示されます。

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

リソースが有効でない場合は、エラー コードと、リソースが無効な理由の詳細を含むエラー メッセージが表示されます。 `4xx` または `5xx` エラーは、検証自体を実行できなかったことを意味し、リソースが有効かどうかは不明です。 エラー メッセージが返された `OperationOutcome` の例を次に示します。

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

上記のこの例では、指定した `Patient` プロファイルでは患者識別子の値と性別が必須ですが、リソースにそれに準拠していませんでした。

プロファイルをパラメーターとして指定する場合は、次に示す `heartrate` の HL7 基本プロファイルの例のように、検証の基準となるプロファイルの正規 URL を指定できます。

```rest
GET https://myworkspace-myfhirserver.fhir.azurehealthcareapis.com/Observation/12345678/$validate?profile=http://hl7.org/fhir/StructureDefinition/heartrate
```

#### <a name="validating-a-new-resource"></a>新しいリソースの検証

サーバーにアップロードしようとしている新しいリソースを検証する場合は、`POST` 要求を実行できます。

```rest
POST http://<your FHIR service base URL>/{Resource}/$validate
```

次に例を示します。

```rest
POST https://myworkspace-myfhirserver.fhir.azurehealthcareapis.com/Patient/$validate 
```

この要求を使用すると、JSON または XML のどちらの形式でも、要求ペイロードに指定する新しいリソースが作成され、アップロードしたリソースが検証されます。 次に、新しいリソースに対する検証の結果として `OperationOutcome` が返されます。

### <a name="validate-on-resource-create-or-resource-update"></a>リソースの CREATE またはリソースの UPDATE に対する検証

リソースの CREATE や UPDATE の時点など、リソースをいつ検証するかに選択できます。 これは、サーバー構成設定の `CoreFeatures` で指定できます。

```json
{
   "FhirServer": {
      "CoreFeatures": {
            "ProfileValidationOnCreate": true,
            "ProfileValidationOnUpdate": false
        }
}
```

リソースが指定された `Resource.meta.profile` に準拠し、プロファイルがシステムに存在する場合、サーバーは上記の構成設定に従って機能します。 指定されたプロファイルがサーバーに存在しない場合、検証要求は無視され、`Resource.meta.profile` に残されます。
通常、検証はコストのかかる操作なので、通常はテスト サーバーまたはリソースの小さなサブセットでのみ実行します。そのため、検証操作をサーバー側でオンまたはオフにするこれらの方法を使用できることが重要です。 サーバー構成でリソースの作成または更新時の検証をオプトアウトするように指定している場合、ユーザーは、作成または更新要求の `header` にそれを指定することで、動作をオーバーライドできます。

```rest
x-ms-profile-validation: true
```

## <a name="next-steps"></a>次のステップ

この記事では、FHIR プロファイルと、$validate を使用してプロファイルに照らしてリソースを検証する方法について説明しました。 FHIR サービスのその他のサポートされている機能の詳細については、次を参照してください。

>[!div class="nextstepaction"]
>[FHIR でサポートされている機能](fhir-features-supported.md)
