---
title: インデックスのクエリを実行するための Azure portal の Search エクスプローラー - Azure Search
description: Azure Search で Search エクスプローラーなどの Azure portal ツールを使用して、インデックスのクエリを実行します。 検索用語や、高度な構文を含む完全修飾の検索文字列を入力します。
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 11f102fcb2a24f9062313f9a3234c29e70a3dfe0
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315668"
---
# <a name="how-to-use-search-explorer-to-query-indexes-in-azure-search"></a>Azure Search で Search エクスプ ローラーを使用してインデックスのクエリを実行する方法 

この記事では、Azure Portal で **Search エクスプローラー**を使用して、既存の Azure Search インデックスのクエリを実行する方法について説明します。 Search エクスプローラーを使用すると、サービス内の既存のインデックスに対して単純または完全な Lucene クエリ文字列を送信できます。

## <a name="open-the-service-dashboard"></a>サービスのダッシュボードを開く
1. [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) の左側にあるジャンプ バーで **[すべてのリソース]** をクリックします。
2. Azure Search サービスを選択します。

## <a name="select-an-index"></a>インデックスを選択する

**[インデックス]** タイルから、検索するインデックスを選択します。

   ![](./media/search-explorer/pick-index.png)

## <a name="open-search-explorer"></a>Search エクスプローラーを開く

[Search エクスプローラー] タイルをクリックして、検索バーと結果ウィンドウを開きます。

   ![](./media/search-explorer/search-explorer-tile.png)

## <a name="start-searching"></a>検索を開始する

Search エクスプローラーを使用している場合は、[クエリ パラメーター](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)を指定してクエリを作成できます。

1. **[クエリ文字列]** で、クエリを入力し、**[検索]** をクリックします。 

   クエリ文字列は自動的に適切な要求 URL に解析され、Azure Search REST API に対して HTTP 要求を送信します。   
   
   任意の Lucene クエリ構文を使用して要求を作成できます。クエリ構文は、有効であれば単純でも完全でもかまいません。 `*` 文字は、空または未指定の検索に相当し、すべてのドキュメントを任意の順序で返します。

2. **[結果]** には、クエリの結果が未加工の JSON 形式で表示されます。これは、プログラムによって要求を発行したときに HTTP 応答本文で返されるペイロードと同じです。

   ![](./media/search-explorer/search-bar.png)

## <a name="next-steps"></a>次の手順

クエリ構文のその他の情報や例については、次のリソースを参照してください。

 + [単純なクエリ構文](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Lucene クエリ構文](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Lucene クエリ構文例](https://docs.microsoft.com/azure/search/search-query-lucene-examples) 
 + [OData フィルター式の構文](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 