---
title: moreLikeThis (プレビュー) クエリ機能
titleSuffix: Azure Cognitive Search
description: moreLikeThis (プレビュー) 機能について説明します。この機能は、Azure Cognitive Search REST API のプレビュー バージョンで使用できます。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b7959beca8a7787a331388b77ebe4060c3675e6d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793467"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>Azure Cognitive Search の moreLikeThis (プレビュー)

> [!Note]
> moreLikeThis はプレビュー段階にあり、運用環境での使用は意図していません。 [REST API バージョン 2019-05-06-Preview](search-api-preview.md) でこの機能を提供します。 現時点で .NET SDK のサポートはありません。

`moreLikeThis=[key]` は、ドキュメント キーで指定されたドキュメントに類似するドキュメントを検索する[ドキュメントの検索 API](https://docs.microsoft.com/rest/api/searchservice/search-documents) のクエリ パラメーターです。 `moreLikeThis` を指定して検索を要求すると、指定したドキュメントの内容を最も適切に説明する検索用語がドキュメントから抽出され、その用語を使用してクエリが生成されます。 生成されたクエリを使用して、検索要求が作成されます。 既定では、`searchFields` パラメーターを使用して指定した制限フィールドを除く、検索可能なすべてのフィールドの内容が考慮されます。 `moreLikeThis` パラメーターは、`search=[string]` 検索パラメーターと一緒に使用することはきません。

既定では、最上位のすべての検索可能フィールドの内容が考慮されます。 代わりに特定のフィールドを指定する場合は、`searchFields` パラメーターを使用します。 

[複合型](search-howto-complex-data-types.md)では、検索可能なサブフィールド上で moreLikeThis を使用することはできません。

## <a name="examples"></a>例 

moreLikeThis クエリの例を次に示します。 このクエリは、説明フィールドの内容が `moreLikeThis` パラメーターに指定されたソース ドキュメントのフィールドに最も類似しているドキュメントを検索します。

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2019-05-06-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```


## <a name="next-steps"></a>次の手順

Web テスト ツールを使用して、この機能を実験します。  この演習では、Postman を使用することをお勧めします。

> [!div class="nextstepaction"]
> [Postman を使用して Azure Cognitive Search REST API を調べる](search-get-started-postman.md)