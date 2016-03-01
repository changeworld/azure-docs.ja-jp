<properties
	pageTitle="REST API を使用した Azure Search へのデータのインポート | Microsoft Azure | ホステッド クラウド検索サービス"
	description="REST API を使用して Azure Search のインデックスにデータをアップロードする方法"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/17/2016"
	ms.author="heidist"/>

# REST API を使用した Azure Search へのデータのインポート
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

この記事では、[Azure Search REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) を使用してインデックスにデータを読み込む方法について説明します。内容の一部は、「[ドキュメントの追加、更新、または削除 (Azure Search サービス REST API)](https://msdn.microsoft.com/library/azure/dn798930.aspx)」のものです。詳しいコンテキストについては、元の記事を参照してください。

REST API を使用してインデックスにドキュメントをプッシュするには、POST HTTP 要求をサービスの URL エンドポイントに発行します。

**要求と要求ヘッダー**:

URL では、サービスの名前と適切な API バージョン (このドキュメントが書かれた時点で最新の API バージョンは "2015-02-28") を指定する必要があります。

要求ヘッダーでは、Content-Type を定義し、サービスのプライマリ管理キーを提供する必要があります。

	POST https://[servicename].search.windows.net/indexes/[indexname]/docs/index?api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key]


**要求本文**:


	{
		"value": [
			{
				"@search.action": "upload",
				"hotelId": "1",
				"baseRate": 199.0,
				"description": "Best hotel in town",
				"description_fr": "Meilleur hôtel en ville",
				"hotelName": "Fancy Stay",
				"category": "Luxury",
				"tags": ["pool", "view", "wifi", "concierge"],
				"parkingIncluded": false,
				"smokingAllowed": false,
				"lastRenovationDate": "2010-06-27T00:00:00Z",
				"rating": 5,
				"location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
			},
			{
				"@search.action": "upload",
				"hotelId": "2",
				"baseRate": 79.99,
				"description": "Cheapest hotel in town",
				"description_fr": "Hôtel le moins cher en ville",
				"hotelName": "Roach Motel",
				"category": "Budget",
				"tags": ["motel", "budget"],
				"parkingIncluded": true,
				"smokingAllowed": true,
				"lastRenovationDate": "1982-04-28T00:00:00Z",
				"rating": 1,
				"location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
			}
		]
	}

この例では、検索アクションとして "upload" を使用しています。既存のドキュメントを更新および削除するときは、"merge"、"mergeOrUpload"、"delete" を使用できます。

インデックスの更新が成功すると、状態コード "200 OK" を受け取ります。要求内の少なくとも 1 つの項目のインデックスが正常に作成されなかった場合は、"207" 状態コードを受け取ります。

ドキュメント アクションおよび成功/エラー応答の詳細については、「[こちらのページ](https://msdn.microsoft.com/library/azure/dn798930.aspx)」を参照してください。

<!---HONumber=AcomDC_0224_2016-->