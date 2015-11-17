<properties
	pageTitle="Azure Search のクエリ | Microsoft Azure | ホスト型クラウド検索サービス"
	description="Azure Search の検索クエリを作成し、検索パラメーターを使用して検索結果のフィルター処理、並べ替え、ファセットを行います。"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/10/2015"
	ms.author="heidist"/>

# Azure Search のクエリ
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [Postman](search-chrome-postman.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

埋め込み検索エクスペリエンス用に Azure Search を使用するカスタム ソリューションには、検索クエリを作成して処理のために検索サービスに送るコードが含まれている必要があります。

.NET SDK と REST API のどちらかを使用して、クエリを実行するためのメソッドを記述できます。事前のテストまたは探索では、Fiddler や Chrome Postman のようなツールを使用してクエリをサービスに送ることができます。

<!---HONumber=Nov15_HO3-->