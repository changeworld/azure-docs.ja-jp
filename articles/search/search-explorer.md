<properties
    pageTitle="Azure ポータルを使用した Azure Search インデックスの照会 | Microsoft Azure | ホステッド クラウド検索サービス"
    description="Azure ポータルの Search エクスプローラーで検索クエリを発行します。"
    services="search"
    documentationCenter=""
	authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"
/>
# Azure ポータルを使用した Azure Search インデックスの照会
> [AZURE.SELECTOR]
- [概要](search-query-overview.md)
- [ポータル](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST ()](search-query-rest-api.md)

このガイドでは、Azure ポータルで Azure Search インデックスを照会する方法を説明します。

このチュートリアルを開始する前に、既に [Azure Search インデックスを作成](search-what-is-an-index.md)し、[インデックスにデータを読み込んでいます](search-what-is-data-import.md)。

## I.Azure Search ブレードに移動する
1. [Azure ポータル](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)の左側のメニューで [すべてのリソース] をクリックします。
2. Azure Search サービスを選択する

## II.検索するインデックスを選択する
1. [インデックス] タイルから、検索するインデックスを選択します。

![](./media/search-explorer/pick-index.png)

## III.[Search エクスプローラー] タイルをクリックする
![](./media/search-explorer/search-explorer-tile.png)

## III.検索を開始する
1. Azure Search インデックスを検索するには、*[クエリ文字列]* フィールドで文字の入力を開始し、**[検索]** をクリックします。
 * Search エクスプローラーを使用している場合は、いずれかの[クエリ パラメーター](https://msdn.microsoft.com/library/dn798927.aspx)を指定できます。

2. *[結果]* セクションに、クエリの結果が未加工の JSON として表示されます。これは、Azure Search REST API に対する検索要求を発行したときに、HTTP 応答の本文で返されます。
3. クエリ文字列は適切な要求 URL として自動的に解析され、Azure Search REST API に対して HTTP 要求が送信されます。

![](./media/search-explorer/search-bar.png)

<!---HONumber=AcomDC_0831_2016-->