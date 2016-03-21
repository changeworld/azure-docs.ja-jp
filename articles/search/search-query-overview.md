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
	ms.date="01/23/2016"
	ms.author="heidist"/>

# Azure Search のクエリ
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

埋め込みの検索エクスペリエンス用に Azure Search を統合するカスタム ソリューションには、検索クエリを作成して処理のために検索エンジンに送るコードが含まれている必要があります。

.NET SDK と REST API のどちらかを使用して、クエリを実行するためのメソッドを記述できます。事前テストまたは探索のために、Azure ポータルに組み込まれている Search エクスプローラーなどのツールを使用してインデックスに対してクエリを実行することも、または Fiddler を使用して、任意の有効な要求を検索サービスに発行することもできます。

コードでクエリを作成する方法を示すサンプルとチュートリアルの一覧は、[ビデオ、チュートリアル、デモ、コード サンプル](search-video-demo-tutorial-list.md)を参照してください。提供されているほぼすべてのサンプルには、Search サービスのクエリを実行するコードが含まれています。

<!---HONumber=AcomDC_0128_2016-->