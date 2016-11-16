---
title: "Azure Portal を使用した Azure Search インデックスの照会 | Microsoft Docs"
description: "Azure ポータルの Search エクスプローラーで検索クエリを発行します。"
services: search
manager: jhubbard
documentationcenter: 
author: ashmaka
ms.assetid: 8e524188-73a7-44db-9e64-ae8bf66b05d3
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a23372112e17703a3399e1bdc9eaf73b85a1f80d


---
# <a name="query-your-azure-search-index-using-the-azure-portal"></a>Azure ポータルを使用した Azure Search インデックスの照会
> [!div class="op_single_selector"]
> * [概要](search-query-overview.md)
> * [ポータル](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST ()](search-query-rest-api.md)
> 
> 

このガイドでは、Azure ポータルで Azure Search インデックスを照会する方法を説明します。

このチュートリアルを開始する前に、既に [Azure Search インデックスを作成](search-what-is-an-index.md)し、[インデックスにデータを読み込んで](search-what-is-data-import.md)います。

## <a name="i-go-to-your-azure-search-blade"></a>I. Azure Search ブレードに移動する
1.  [Azure ポータル](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Azure Search サービスを選択する

## <a name="ii-select-the-index-you-would-like-to-search"></a>II. 検索するインデックスを選択する
1. [インデックス] タイルから、検索するインデックスを選択します。

![](./media/search-explorer/pick-index.png)

## <a name="iii-click-on-the-search-explorer-tile"></a>III. [Search エクスプローラー] タイルをクリックする
![](./media/search-explorer/search-explorer-tile.png)

## <a name="iii-start-searching"></a>III. 検索を開始する
1. Azure Search インデックスを検索するには、*[クエリ文字列]*フィールドで文字の入力を開始し、**[検索]**をクリックします。
   
   * Search エクスプローラーを使用している場合は、いずれかの [クエリ パラメーター](https://msdn.microsoft.com/library/dn798927.aspx)
2. *[結果]* セクションに、クエリの結果が未加工の JSON として表示されます。これは、Azure Search REST API に対する検索要求を発行したときに、HTTP 応答の本文で返されます。
3. クエリ文字列は適切な要求 URL として自動的に解析され、Azure Search REST API に対して HTTP 要求が送信されます。

![](./media/search-explorer/search-bar.png)




<!--HONumber=Nov16_HO2-->


