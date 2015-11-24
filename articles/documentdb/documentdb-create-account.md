<properties 
	pageTitle="データベース アカウントの作成 - 無料試用版 | Microsoft Azure" 
	description="JSON の管理された NoSQL ドキュメント データベースである Azure DocumentDB 用のオンライン データベース クリエーターを使用して、データベース アカウントを作成する方法について説明します。無料試用版を今すぐ入手してください。"
	keywords="無料試用版, オンライン データベース クリエーター, データベースの作成, データベース作成, documentdb, Azure, Microsoft Azure"
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="11/18/2015" 
	ms.author="mimig"/>

# DocumentDB データベース アカウントの作成

Microsoft Azure DocumentDB を使用するには、Azure プレビュー ポータルを使用して DocumentDB データベース アカウントを作成する必要があります。

DocumentDB を初めて使用する場合は、 Scott Hansleman による [こちら](http://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) の 4 分間のビデオを視聴し、オンライン ポータルで最もよく使用されるタスクの実行方法を確認してください。

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## 次のステップ

DocumentDB アカウントを作成できたら、次の手順として DocumentDB データベースを作成します。データベースを作成するには、次のいずれかを使用します。

- GitHub の [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) リポジトリの [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) プロジェクトにある C# .NET のサンプル。
- プレビュー ポータル。「[Azure プレビュー ポータルを使用して DocumentDB データベースを作成する](documentdb-create-database.md)」の説明を参照してください。
- 包括的なチュートリアル: [.NET](documentdb-get-started.md)、[.NET MVC](documentdb-dotnet-application.md)、[Java](documentdb-java-application.md)、[Node.js](documentdb-nodejs-application.md)、または [Python](documentdb-python-application.md)。
- [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)。DocumentDB には、.NET、Java、Python、Node.js、および JavaScript API の SDK があります。 


データベースを作成した後に、データベースに [1 つまたは複数のコレクションを追加](documentdb-create-collection.md)し、それらのコレクションに[ドキュメントを追加する](documentdb-view-json-document-explorer.md)必要があります。

コレクションにドキュメントを用意した後で、プレビュー ポータルの[クエリ エクスプローラー](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)、またはいずれかの [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) を使用することで、[DocumentDB SQL](documentdb-sql-query.md) を使用してドキュメントに対して[クエリを実行](documentdb-sql-query.md#executing-queries)することができます。

DocumentDB の詳細については、以下の資料を参照してください。

-	[DocumentDB のラーニング パス](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-	[DocumentDB のリソース モデルと概念](documentdb-resources.md)

 

<!---HONumber=Nov15_HO4-->