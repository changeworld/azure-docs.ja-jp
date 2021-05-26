---
title: Azure API for FHIR でカスタム検索を実行する方法
description: この記事では、データベースで使用する独自のカスタム検索パラメーターを定義する方法について説明します。
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/03/2021
ms.author: cavoeg
ms.openlocfilehash: 5453b11cb49bb48c48e6c949a00654a797c89202
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110476667"
---
# <a name="defining-custom-search-parameters"></a>カスタム検索パラメーターの定義

FHIR 仕様では、リソースに固有のすべてのリソースと検索パラメーターの一連の検索パラメーターが定義されています。 ただし、FHIR 仕様によって定義されていないリソース内の要素を標準の検索パラメーターとして検索することが必要になる場合があります。 この記事では、Azure API for FHIR で使用する独自の [検索パラメーター](https://www.hl7.org/fhir/searchparameter.html) を定義する方法について説明します。

> [!NOTE]
> 検索パラメーターを作成、更新、または削除するたびに、再 [インデックスジョブ](how-to-run-a-reindex.md) を実行して、検索パラメーターを運用環境で使用できるようにする必要があります。 次に、FHIR サーバー全体のインデックスを再作成する前に、検索パラメーターをテストする方法について説明します。

## <a name="create-new-search-parameter"></a>新しい検索パラメーターの作成

新しい検索パラメーターを作成するには、その `POST` `SearchParameter` リソースをデータベースに対して指定します。 次のコード例は、 [US Core レース SearchParameter](http://hl7.org/fhir/us/core/STU3.1.1/SearchParameter-us-core-race.html) をリソースに追加する方法を示して `Patient` います。

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
> 新しい検索パラメーターは、検索パラメーターをデータベースに **ポストし、データベースのインデックスを** 再作成した後に、FHIR サーバーの機能ステートメントに表示されます。 `SearchParameter`機能ステートメントでを表示することは、FHIR サーバーで検索パラメーターがサポートされているかどうかを確認する唯一の方法です。 検索パラメーターを検索しても検索パラメーターが見つからない場合は、検索パラメーターのインデックスを作成する必要があります。 再インデックス操作をトリガーする前に、複数の検索パラメーターをポストできます。

の重要な要素は `SearchParameter` 次のとおりです。

* **url**: 検索パラメーターを説明する一意のキー。 HL7 などの多くの組織は、米国のコアレース検索パラメーターに示されているように、定義した検索パラメーターに標準の URL 形式を使用します。

* **code**: **コード** に格納されている値は、検索時に使用します。 上記の例では、を使用して検索 `GET {FHIR_URL}/Patient?race=<code>` し、特定のレースのすべての患者を取得します。 コードは、検索パラメーターが適用されるリソースに対して一意である必要があります。

* **base:** 検索パラメーターが適用されるリソースについて説明します。 検索パラメーターがすべてのリソースに適用される場合は、 を使用できます。それ以外の場合は `Resource` 、関連するすべてのリソースを一覧表示できます。
 
* **type:** 検索パラメーターのデータ型を記述します。 種類は、サポートされているサービスによってAzure API for FHIR。 つまり、Special 型の検索パラメーターを定義したり、複合検索パラメーター[](overview-of-search.md)を定義したりすることはできません。サポートされている組み合わせである場合を指定してください。

* **expression:** 検索の値を計算する方法について説明します。 検索パラメーターを記述する場合、式は仕様で必須ではない場合でも含める必要があります。 これは、式または xpath 構文が必要であり、xpath 構文を無視Azure API for FHIRが必要です。

## <a name="test-search-parameters"></a>検索パラメーターをテストする

インデックスの再作成ジョブを実行するまでは、実稼働環境で検索パラメーターを使用することはできませんが、データベース全体のインデックスを再作成する前に検索パラメーターをテストする方法はいくつかあります。 

最初に、新しい検索パラメーターをテストして、返される値を確認できます。 (ID を入力して) 特定のリソース インスタンスに対して次のコマンドを実行すると、検索パラメーター名と特定の患者用に格納された値を持つ値ペアの一覧が返されます。 これには、リソースのすべての検索パラメーターが含まれます。スクロールして、作成した検索パラメーターを検索できます。 このコマンドを実行すると、FHIR サーバーの動作は変更されません。 

```rest
GET https://{{FHIR_URL}}/{{RESOURCE}}/{{RESOUCE_ID}}/$reindex

```
たとえば、患者のすべての検索パラメーターを検索するには、次のコマンドを実行します。

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
検索パラメーターが期待した通り表示されているのが分かったら、1 つのリソースのインデックスを再作成して、 要素を使用して検索をテストできます。 最初に、1 つのリソースのインデックスを再作成します。

```rest
POST https://{{FHIR_URL}/{{RESOURCE}}/{{RESOURCE_ID}}/$reindex
```

これを実行すると、そのリソースの種類に対して定義した特定のリソースの検索パラメーターのインデックスが設定されます。 これにより、FHIR サーバーが更新されます。 これで、[部分的なインデックスを使用する] ヘッダーを true に設定できるようになりました。これは、その型のすべてのリソースでインデックスが作成されていない場合でも、リソースのいずれかに検索パラメーターがインデックス付きで含まれる場合に、結果が返されることを意味します。 

上記の例を続行すると、1つの患者にインデックスを作成して、米国コアレースを有効にすることができ `SearchParameter` ます。

```rest
POST https://{{FHIR_URL}/Patient/{{PATIENT_ID}}/$reindex
```

次に、特定のレースを持つ患者を検索します。

```rest
GET https://{{FHIR_URL}}/Patient?race=2028-9
x-ms-use-partial-indices: true
```

検索パラメーターが想定どおりに動作していることをテストした後、再インデックスジョブを実行またはスケジュールして、運用環境のユースケースに対して FHIR サーバーで検索パラメーターを使用できるようにします。

## <a name="update-a-search-parameter"></a>検索パラメーターを更新する

検索パラメーターを更新するには、を使用し `PUT` て検索パラメーターの新しいバージョンを作成します。 `SearchParameter ID` `id` 要求の本文の要素にとを含める必要があり `PUT` `PUT` ます。

> [!NOTE]
> 検索パラメーターの ID がわからない場合は、検索することができます。 を使用する `GET {{FHIR_URL}}/SearchParameter` と、すべてのカスタム検索パラメーターが返され、検索パラメーターをスクロールして必要な検索パラメーターを見つけることができます。 名前で検索を制限することもできます。 次の例では、を使用して名前を検索でき `USCoreRace: GET {{FHIR_URL}}/SearchParameter?name=USCoreRace` ます。

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

結果は更新され、 `SearchParameter` バージョンがインクリメントされます。

> [!Warning]
> データベースで既にインデックスが作成されている SearchParameters を更新するときは注意してください。 既存の SearchParameter の動作を変更すると、期待される動作に影響を与える可能性があります。 再インデックス処理ジョブはすぐに実行することをお勧めします。

## <a name="delete-a-search-parameter"></a>検索パラメーターを削除する

検索パラメーターを削除する必要がある場合は、次のようにします。

```rest
Delete {{FHIR_URL}}/SearchParameter/{SearchParameter ID}
```

> [!Warning]
> データベースで既にインデックスが作成されている SearchParameters を削除する場合は注意してください。 既存の SearchParameter の動作を変更すると、期待される動作に影響を与える可能性があります。 再インデックス処理ジョブはすぐに実行することをお勧めします。

## <a name="next-steps"></a>次のステップ

この記事では、検索パラメーターを作成する方法について学習しました。 次に、FHIR サーバーのインデックスを再作成する方法について説明します。

>[!div class="nextstepaction"]
>[インデックス再作成ジョブの実行方法](how-to-run-a-reindex.md)