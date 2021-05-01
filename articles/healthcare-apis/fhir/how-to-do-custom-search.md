---
title: Azure API for FHIR でカスタム検索を実行する方法
description: この記事では、データベースで使用する独自のカスタム検索パラメーターを定義する方法について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/23/2021
ms.author: cavoeg
ms.openlocfilehash: 1482bd7f054c75c4f26b77907d50a6471c389d68
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108322574"
---
# <a name="defining-custom-search-parameters"></a>カスタム検索パラメーターの定義

FHIR 仕様では、リソースに固有のすべてのリソースと検索パラメーターの一連の検索パラメーターが定義されています。 ただし、仕様で定義されていないリソース内のフィールドを標準の検索パラメーターとして検索することが必要になる場合があります。 この記事では、FHIR サーバーで使用する独自の [検索パラメーター](https://www.hl7.org/fhir/searchparameter.html) を定義する方法について説明します。

> [!NOTE]
> 検索パラメーターを作成、更新、または削除するたびに、再 [インデックスジョブ](how-to-run-a-reindex.md) を実行して、データベースでの変更を有効にする必要があります。

> [!Warning]
> 検索パラメーターを更新または削除する場合は、直ちに再 [インデックスジョブ](how-to-run-a-reindex.md)を実行してください。 更新または削除の検索パラメーターを使用すると、データベースが異常な状態になる可能性があります。これは、変更に対してインデックスを作成する必要があるため、アクティブとして表示されます。 

## <a name="create-new-search-parameter"></a>新しい検索パラメーターの作成

新しい検索パラメーターを作成するには、 `POST` データベースの新しい検索パラメーターを使用します。 次のコード例は、 [米国のコアレース検索パラメーター](http://hl7.org/fhir/us/core/STU3.1.1/SearchParameter-us-core-race.html) を患者リソースに追加する方法を示しています。

```json
POST {fhirurl}/SearchParameter
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
> 新しい検索パラメーターは、FHIR サーバーにポストしてデータベースのインデックスを再作成した後、機能ステートメントに表示されます。 再 [インデックスジョブ](how-to-run-a-reindex.md)を実行するまで使用できません。 これは、検索パラメーターがデータベース内でサポートされているかどうかを判断するための唯一の方法です。 検索パラメーターを検索しても、機能ステートメントで検索パラメーターを確認できない場合は、再インデックスジョブを実行します。 再インデックス化操作をトリガーする前に、複数の検索パラメーターを投稿できます。

重要なフィールドの説明:

* **url**: これは、検索パラメーターを記述するための一意のキーです。 HL7 などの多くの組織は、米国のコア競争検索パラメーターに示されているように、定義した Url に標準形式を使用します。

* **code**: ここでの値は、検索時に使用するものです。 上記の例では、を使用して検索し、 `GET {FHIR URL}/Patient?race=2028-9` アジアの患者をすべて取得します。 この値は、適用対象のリソースに対して一意である必要があります。

* **base**: 検索パラメーターが適用されるリソースを示します。 すべてのリソースに適用される場合は、リソースのみを使用できます。それ以外の場合は、関連するすべてのリソースを一覧表示できます。
 
* **type**: 検索パラメーターのデータ型について説明します。

* **式**: 検索パラメーターを記述するときに、仕様では厳密には必要ではありませんが、式を含める必要があります。 これは、式または xpath 構文が必要であり、現在のところ、FHIR の Azure API で xpath 構文が無視されるためです。 ここでは、検索の値を検索する方法について説明します。 

> [!NOTE]
> "Type" は、Azure API for FHIR のサポートによって制限されています。 つまり、型がサポートされていない場合は、特殊な型の検索パラメーターを定義したり、 [複合検索パラメーター](overview-of-search.md) を定義したりすることはできません。

検索パラメーターを追加したら、インデックスの再作成ジョブを実行またはスケジュールして、検索パラメーターを FHIR サーバーで使用できるようにします。

## <a name="update-a-search-parameter"></a>検索パラメーターを更新する

検索パラメーターを更新するには、を使用し `PUT` て検索パラメーターの新しいバージョンを作成します。

`PUT {fhirurl}/SearchParameter/{SearchParameter ID}`

`SearchParameter ID`要求の本文の ID フィールドにを含める必要があり `PUT` `PUT` ます。

> [!NOTE]
> 検索パラメーターの ID がわからない場合は、検索することができます。 を使用する `GET {fhirurl}/SearchParameter` と、すべてのカスタム検索パラメーターが返され、検索パラメーターをスクロールして必要な検索パラメーターを見つけることができます。 名前で検索を制限することもできます。 次の例では、を使用して名前を検索でき `USCoreRace: GET {fhirurl}/SearchParameter?name=USCoreRace` ます。

```json
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
> データベースで既にインデックスが作成されている SearchParameters を更新するときは注意してください。 既存の SearchParameter の動作を変更すると、期待される動作に影響を与える可能性があります。 

## <a name="delete-a-search-parameter"></a>検索パラメーターを削除する

検索パラメーターを削除する必要がある場合は、次のようにします。

`Delete {fhirurl}/SearchParameter/{SearchParameter ID}`

> [!Warning]
> データベースで既にインデックスが作成されている SearchParameters を削除する場合は注意してください。 既存の SearchParameter の動作を変更すると、期待される動作に影響を与える可能性があります。

## <a name="next-steps"></a>次の手順

この記事では、検索パラメーターの作成方法を学習しました。 ジョブのインデックスを再取得する方法については、「」を参照してください。

>[!div class="nextstepaction"]
>[再インデックスジョブを実行する方法](how-to-run-a-reindex.md)