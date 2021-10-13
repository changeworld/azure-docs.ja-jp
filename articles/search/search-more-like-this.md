---
title: moreLikeThis (プレビュー) クエリ機能
titleSuffix: Azure Cognitive Search
description: moreLikeThis (プレビュー) 機能について説明します。この機能は、Azure Cognitive Search REST API のプレビュー バージョンで使用できます。
author: bevloh
ms.author: beloh
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/06/2021
ms.openlocfilehash: 1dae66ed27a0934d17503cc8262d39dfc02b46dc
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612068"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>Azure Cognitive Search の moreLikeThis (プレビュー)

> [!IMPORTANT] 
> この機能はパブリック プレビュー段階にあり、[追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の下で提供されます。 この機能は、[プレビュー REST API](/rest/api/searchservice/index-preview) でサポートされます。

`moreLikeThis=[key]` は、ドキュメント キーで指定されたドキュメントに類似するドキュメントを検索する[ドキュメントの検索 API](/rest/api/searchservice/search-documents) のクエリ パラメーターです。 `moreLikeThis` を指定して検索を要求すると、指定したドキュメントの内容を最も適切に説明する検索用語がドキュメントから抽出され、その用語を使用してクエリが生成されます。 生成されたクエリを使用して、検索要求が作成されます。 既定では、`searchFields` パラメーターを使用して指定した制限フィールドを除く、検索可能なすべてのフィールドの内容が考慮されます。 `moreLikeThis` パラメーターは、`search=[string]` 検索パラメーターと一緒に使用することはきません。

既定では、最上位のすべての検索可能フィールドの内容が考慮されます。 代わりに特定のフィールドを指定する場合は、`searchFields` パラメーターを使用します。 

[複合型](search-howto-complex-data-types.md)の検索可能なサブフィールドでは、`MoreLikeThis` を使用できません。

## <a name="examples"></a>例

以下のすべての例では、次のページのホテルのサンプルを使用します ([Azure portal で検索インデックスを作成する方法のクイックスタート](search-get-started-portal.md)に関するページを参照してください)。

### <a name="simple-query"></a>単純なクエリ

次のクエリは、説明フィールドの内容が `moreLikeThis` パラメーターに指定されたソース ドキュメントのフィールドに最も類似しているドキュメントを検索します。

```http
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2020-06-30-Preview
```

この例の要求では、`HotelId` 29 のホテルに類似したホテルを検索します。
HTTP GET を使用する代わりに、HTTP POST を使用して `MoreLikeThis` を呼び出すこともできます。

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>フィルターを適用する

`MoreLikeThis` は、`$filter` などの他の一般的なクエリ パラメーターと組み合わせることができます。 たとえば、クエリを使って、カテゴリが 'バジェット' で評価が 3.5 より高いホテルのみに制限できます。

```http
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2020-06-30-Preview
```

### <a name="select-fields-and-limit-results"></a>フィールドを選択して結果を制限する

`$top` セレクターを使うと、`MoreLikeThis` クエリで返される結果の数を制限できます。 また、`$select` を使ってフィールドを選択することもできます。 ここでは、上位 3 つのホテルが ID、名前、評価と共に選択されています。 

```http
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2020-06-30-Preview
```

## <a name="next-steps"></a>次のステップ

Web テスト ツールを使用して、この機能を実験します。  この演習では、Postman を使用することをお勧めします。

> [!div class="nextstepaction"]
> [Postman を使用して Azure Cognitive Search REST API を調べる](search-get-started-rest.md)