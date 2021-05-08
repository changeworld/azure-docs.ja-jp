---
title: 英語以外の検索クエリの多言語インデックス作成
titleSuffix: Azure Cognitive Search
description: 複数言語のコンテンツをサポートするインデックスを作成し、そのコンテンツに対するクエリを作成します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 627ec77af4e492b4f22404972729cecdb1c40f06
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801606"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Azure Cognitive Search で複数の言語のインデックスを作成する方法

多言語対応の検索アプリケーションで重要な要件は、ユーザーが使用する言語で検索し、結果を取得できる機能です。 Azure Cognitive Search での多言語対応アプリの言語要件の対応には、特定言語の文字列のみを格納する専用のフィールドを作成し、クエリ時にそれらのフィールドに対してのみフルテキスト検索をして制限する方法があります。

+ フィールド定義で、対象言語の言語規則を呼び出す言語アナライザーを設定します。 サポートされているアナライザーの詳細な一覧を参照するには、[言語アナライザーの追加](index-add-language-analyzers.md)に関するページを参照してください。

+ クエリの要求でフルテキスト検索の範囲を特定のフィールドに設定するパラメーターを設定し、提供する検索機能と互換性がある内容を含まないフィールドの結果をトリミングします。

フィールドの内容の整合性によって、この手法が成功するかどうかが決まります。 Azure Cognitive Search では、クエリ実行の一部として文字列の変換や言語の検出は行いません。 フィールドに目的の文字列が含まれているかどうかは、ユーザーが判断する必要があります。

## <a name="define-fields-for-content-in-different-languages"></a>複数言語のコンテンツのフィールドを定義する

Azure Cognitive Search のクエリは、単一のインデックスを対象にしています。 通常、単一の検索操作で言語固有の文字列を提供したい場合は、それらの値を格納する専用のフィールドを定義します。たとえば、英語の文字列用のフィールド、フランス語用のフィールドなどです。

フィールド定義の "analyzer" プロパティは、[言語アナライザー](index-add-language-analyzers.md)の設定に使用されます。 インデックス作成とクエリ実行の両方に使用されます。

```JSON
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft"
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft"
    },
```

## <a name="build-and-load-an-index"></a>インデックスの作成と読み込み

(言うまでもありませんが) 中間の手順として、クエリを作成する前に[インデックスを作成して設定する](search-get-started-dotnet.md)必要があります。 ここでは、完全を期すためにこの手順に触れました。 インデックスの可用性を判断する 1 つの方法として、[ポータル](https://portal.azure.com)でインデックスの一覧を確認します。

> [!TIP]
> 言語の検出とテキスト変換は、[AI エンリッチメント](cognitive-search-concept-intro.md)と[スキルセット](cognitive-search-working-with-skillsets.md)を使用したデータの取り込み中にサポートされます。 混合言語コンテンツを含む Azure データソースがある場合は、[データのインポート ウィザード](cognitive-search-quickstart-blob.md)を使用して言語の検出機能と翻訳機能を試すことができます。

## <a name="constrain-the-query-and-trim-results"></a>クエリの制限と結果のトリミング

クエリのパラメーターは、検索を特定のフィールドに制限し、実際のシナリオには不要なフィールドの結果をトリミングするために使用されます。 

| パラメーター | 目的 |
|-----------|--------------|
| **searchFields** | フル テキスト検索の対象を、指定したフィールドの一覧にのみ制限します。 |
| **$select** | 応答をトリミングし、指定したフィールドのみを含めます。 既定では、すべての取得可能なフィールドが返されます。 **$select** パラメーターを使用すると、返すフィールドを選択できます。 |

検索対象をフランス語の文字列を含むフィールドに制限することが目標の場合、**searchFields** を使用して、その言語の文字列を含むフィールドをクエリの対象に指定します。

クエリ要求にアナライザーを指定する必要はありません。 フィールド定義の言語アナライザーは、クエリの処理中に常に使用されます。 複数のフィールドを指定して異なる言語アナライザーを呼び出すクエリでは、各フィールドに割り当てられたアナライザーによって個別に用語や語句が処理されます。

既定の検索では、取得可能とマークされているすべてのフィールドが返されます。 そのため、提供したい言語固有の検索機能に従っていないフィールドを除外する場合があります。 具体的に説明すると、フランス語の文字列を含むフィールドに検索を制限する場合、英語の文字列を含むフィールドを結果から除外するのではないでしょうか。 **$select** クエリ パラメーターを使用すると、呼び出し元アプリケーションに返すフィールドを制御できます。

#### <a name="example-in-rest"></a>REST の例

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "animaux acceptés",
    "searchFields": "Tags, Description_fr",
    "select": "HotelName, Description_fr, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

#### <a name="example-in-c"></a>C# の例

```csharp
private static void RunQueries(SearchClient srchclient)
{
    SearchOptions options;
    SearchResults<Hotel> response;

    options = new SearchOptions()
    {
        IncludeTotalCount = true,
        Filter = "",
        OrderBy = { "" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description_fr");
    options.SearchFields.Add("Tags");
    options.SearchFields.Add("Description_fr");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
}
```

## <a name="boost-language-specific-fields"></a>言語固有フィールドを強化する

クエリを発行するエージェントの言語が不明な場合もありますが、その場合は、すべてのフィールドに対して同時にクエリを発行できます。 [スコアリング プロファイル](index-add-scoring-profiles.md)を使用すると、特定言語の結果に対する優先度を定義できます。 次の例では、英語の説明で見つかった一致項目には、他の言語の一致項目に比べて高いスコアが付けられます。

```JSON
  "scoringProfiles": [
    {
      "name": "englishFirst",
      "text": {
        "weights": { "description": 2 }
      }
    }
  ]
```

次に、検索要求にスコアリング プロファイルを含めます。

```http
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
  "search": "pets allowed",
  "searchFields": "Tags, Description",
  "select": "HotelName, Tags, Description",
  "scoringProfile": "englishFirst",
  "count": "true"
}
```

## <a name="next-steps"></a>次の手順

+ [言語アナライザー](index-add-language-analyzers.md)
+ [Azure Cognitive Search でのフルテキスト検索のしくみ](search-lucene-query-architecture.md)
+ [Search Documents REST API](/rest/api/searchservice/search-documents)
+ [AI エンリッチメントの概要](cognitive-search-concept-intro.md)
+ [スキルセットの概要](cognitive-search-working-with-skillsets.md)