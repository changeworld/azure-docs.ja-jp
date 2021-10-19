---
title: Azure API for FHIR でカスタム検索を実行する方法
description: この記事では、Azure API for FHIR で独自のカスタム検索パラメーターを定義して、データベースで使用する方法について説明します。
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/03/2021
ms.author: cavoeg
ms.openlocfilehash: 28acdb257fe3eee41eb1585e9f1c60a26c91b0ad
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358488"
---
# <a name="defining-custom-search-parameters-for-azure-api-for-fhir"></a>Azure API for FHIR のカスタム検索パラメーターの定義

FHIR 仕様では、すべてのリソースに対する一連の検索パラメーターと、リソース固有の検索パラメーターが定義されています。 ただし、FHIR 仕様によって標準の検索パラメーターとして定義されていないリソース内の要素に対して検索を行いたいシナリオがあります。 この記事では、Azure API for FHIR で使用される独自の[検索パラメーター](https://www.hl7.org/fhir/searchparameter.html)を定義する方法について説明します。

> [!NOTE]
> 検索パラメーターを作成、更新、または削除するたびに、[インデックス再作成ジョブ](how-to-run-a-reindex.md)を実行して、検索パラメーターを運用環境で使用できるようにする必要があります。 以下では、FHIR サーバー全体のインデックスを再作成する前に検索パラメーターをテストする方法について、概要を説明します。

## <a name="create-new-search-parameter"></a>新しい検索パラメーターを作成する

新しい検索パラメーターを作成するには、`SearchParameter` リソースをデータベースに `POST` します。 次のコード例は、[US Core Race SearchParameter](http://hl7.org/fhir/us/core/STU3.1.1/SearchParameter-us-core-race.html) を `Patient` リソースに追加する方法を示しています。

```rest
POST {{FHIR_URL}}/SearchParameter

{
  "resourceType" : "SearchParameter",
  "id" : "us-core-race",
  "url" : "http://hl7.org/fhir/us/core/SearchParameter/us-core-race",
  "version" : "3.1.1",
  "name" : "USCoreRace",
  "status" : "active",
  "date" : "2019-05-21",
  "publisher" : "US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "other",
          "value" : "http://www.healthit.gov/"
        }
      ]
    }
  ],
  "description" : "Returns patients with a race extension matching the specified code.",
  "jurisdiction" : [
    {
      "coding" : [
        {
          "system" : "urn:iso:std:iso:3166",
          "code" : "US",
          "display" : "United States of America"
        }
      ]
    }
  ],
  "code" : "race",
  "base" : [
    "Patient"
  ],
  "type" : "token",
  "expression" : "Patient.extension.where(url = 'http://hl7.org/fhir/us/core/StructureDefinition/us-core-race').extension.value.code"
}

``` 

> [!NOTE]
> 新しい検索パラメーターは、検索パラメーターをデータベースに POST し、**さらに** データベースのインデックスを再作成した後で、FHIR サーバーの機能ステートメントに表示されます。 機能ステートメントでの `SearchParameter` の表示は、FHIR サーバーで検索パラメーターがサポートされているかどうかを確認するための唯一の方法です。 検索パラメーターを検索して検索パラメーターが見つかっても、機能ステートメントに表示されない場合は、まだ検索パラメーターのインデックスを作成する必要があります。 インデックスの再作成操作をトリガーする前に、複数の検索パラメーターを POST できます。

`SearchParameter` の重要な要素は、次のとおりです。

* **url**: 検索パラメーターを記述するための一意のキー。 HL7 などの多くの組織は、上の US Core Race 検索パラメーターに示されているように、定義した検索パラメーターに標準の URL 形式を使用しています。

* **code**: **code** に格納される値は、検索時に使用されるものです。 上の例では、`GET {FHIR_URL}/Patient?race=<code>` を使用して検索し、特定の人種のすべての患者を取得します。 code は、検索パラメーターが適用されるリソースに対して一意である必要があります。

* **base**: 検索パラメーターがどのリソースに適用されるかを記述します。 検索パラメーターがすべてのリソースに適用される場合は、`Resource` を使用できます。それ以外の場合は、関連するすべてのリソースのリストを指定できます。
 
* **type**: 検索パラメーターのデータ型について記述します。 型は、Azure API for FHIR のサポートによって制限されます。 つまり、型が Special の検索パラメーターは定義できず、[複合検索パラメーター](overview-of-search.md)は、サポートされている組み合わせでない限り定義できません。

* **expression**: 検索の値を計算する方法について記述します。 検索パラメーターを記述する場合は、仕様で必要とされていない場合でも、expression を含める必要があります。 これは、expression または xpath 構文が必要であり、Azure API for FHIR では xpath 構文が無視されるためです。

## <a name="test-search-parameters"></a>検索パラメーターをテストする

インデックス再作成ジョブを実行するまで、運用環境で検索パラメーターを使用することはできませんが、データベース全体のインデックスを再作成する前に検索パラメーターをテストする方法はいくつかあります。 

まず、新しい検索パラメーターで、どのような値が返されるかをテストします。 次のコマンドを特定のリソース インスタンスに対して (その ID を入力して) 実行すると、検索パラメーター名と特定の患者に対して格納されている値のペアが、一覧として返されます。 これには、リソースのすべての検索パラメーターが含まれており、スクロールして、作成した検索パラメーターを見つけることができます。 このコマンドを実行しても、FHIR サーバーの動作は変更されません。 

```rest
GET https://{{FHIR_URL}}/{{RESOURCE}}/{{RESOUCE_ID}}/$reindex

```
たとえば、患者のすべての検索パラメーターを検索するには:

```rest
GET https://{{FHIR_URL}}/Patient/{{PATIENT_ID}}/$reindex

```

結果は次のようになります。

```json
{
  "resourceType": "Parameters",
  "id": "8be24e78-b333-49da-a861-523491c3437a",
  "meta": {
    "versionId": "1"
  },
  "parameter": [
    {
      "name": "deceased",
      "valueString": "http://hl7.org/fhir/special-values|false"
    },
    {
      "name": "language",
      "valueString": "urn:ietf:bcp:47|en-US"
    },
    {
      "name": "race",
      "valueString": "2028-9"
    },
...
```
検索パラメーターが期待どおりに表示されていることを確認したら、単一のリソースのインデックスを再作成し、要素を使用して検索をテストできます。 まず、1 つのリソースのインデックスを再作成します。

```rest
POST https://{{FHIR_URL}/{{RESOURCE}}/{{RESOURCE_ID}}/$reindex
```

これを実行すると、特定のリソースで、そのリソースの種類に対して定義したすべての検索パラメーターのインデックスが設定されます。 これにより、FHIR サーバーが更新されます。 これで、"部分インデックスを使用する" ヘッダーを検索し、true に設定できるようになりました。つまり、検索パラメーターのインデックスが作成されたリソースが、その種類のすべてのリソースで検索パラメーターのインデックスが作成されたのではない場合でも、結果として返されるようになります。 

上の例を続行すると、1 人の患者にインデックスを作成して、US Core Race `SearchParameter` を有効にできます。

```rest
POST https://{{FHIR_URL}/Patient/{{PATIENT_ID}}/$reindex
```

次に、特定の人種の患者を検索します。

```rest
GET https://{{FHIR_URL}}/Patient?race=2028-9
x-ms-use-partial-indices: true
```

テストを行い、検索パラメーターが期待どおりに動作していることを確認できたら、インデックス再作成ジョブを実行またはスケジュールして、運用環境のユースケースに対して FHIR サーバーで検索パラメーターを使用できるようにします。

## <a name="update-a-search-parameter"></a>検索パラメーターを更新する

検索パラメーターを更新するには、`PUT` を使用して検索パラメーターの新しいバージョンを作成します。 `PUT` 要求の本文の `id` 要素内と、`PUT` 呼び出し内に、`SearchParameter ID` を含める必要があります。

> [!NOTE]
> 検索パラメーターの ID がわからない場合は、検索できます。 `GET {{FHIR_URL}}/SearchParameter` を使用すると、すべてのカスタム検索パラメーターが返され、スクロールして必要な検索パラメーターを見つけることができます。 検索を名前で制限することもできます。 次の例では、`USCoreRace: GET {{FHIR_URL}}/SearchParameter?name=USCoreRace` を使用して名前を検索できます。

```rest
PUT {{FHIR_ULR}}/SearchParameter/{SearchParameter ID}

{
  "resourceType" : "SearchParameter",
  "id" : "SearchParameter ID",
  "url" : "http://hl7.org/fhir/us/core/SearchParameter/us-core-race",
  "version" : "3.1.1",
  "name" : "USCoreRace",
  "status" : "active",
  "date" : "2019-05-21",
  "publisher" : "US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "other",
          "value" : "http://www.healthit.gov/"
        }
      ]
    }
  ],
  "description" : "New Description!",
  "jurisdiction" : [
    {
      "coding" : [
        {
          "system" : "urn:iso:std:iso:3166",
          "code" : "US",
          "display" : "United States of America"
        }
      ]
    }
  ],
  "code" : "race",
  "base" : [
    "Patient"
  ],
  "type" : "token",
  "expression" : "Patient.extension.where(url = 'http://hl7.org/fhir/us/core/StructureDefinition/us-core-race').extension.value.code"
}

```

結果は更新された `SearchParameter` で、バージョンが増分されます。

> [!Warning]
> データベースで既にインデックスが作成されている SearchParameters を更新する場合は注意してください。 既存の SearchParameter の動作を変更すると、期待される動作に影響を与える可能性があります。 インデックス再作成ジョブをすぐに実行することをお勧めします。

## <a name="delete-a-search-parameter"></a>検索パラメーターを削除する

検索パラメーターを削除する必要がある場合は、次を使用してください。

```rest
Delete {{FHIR_URL}}/SearchParameter/{SearchParameter ID}
```

> [!Warning]
> データベースで既にインデックスが作成されている SearchParameters を削除する場合は注意してください。 既存の SearchParameter の動作を変更すると、期待される動作に影響を与える可能性があります。 インデックス再作成ジョブをすぐに実行することをお勧めします。

## <a name="next-steps"></a>次のステップ

この記事では、検索パラメーターの作成方法を学習しました。 次に、FHIR サーバーのインデックスを再作成する方法を学習できます。

>[!div class="nextstepaction"]
>[インデックス再作成ジョブの実行方法](how-to-run-a-reindex.md)
