<properties 
	pageTitle="DocumentDB でデータベースを作成する方法 |Microsoft Azure" 
	description="JSON の NoSQL ドキュメント データベースである Azure DocumentDB 用のオンライン サービス ポータルを使用して、データベースを作成する方法について説明します。無料評価版を今すぐ入手してください。" 
	keywords="データベースの作成方法" 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2015" 
	ms.author="mimig"/>

# DocumentDB 用のデータベースの作成方法

Microsoft Azure DocumentDB を使用するには、[DocumentDB アカウント](documentdb-create-account.md)、データベース、コレクション、およびドキュメントを用意する必要があります。このトピックでは、Microsoft Azure プレビュー ポータルで DocumentDB データベースを作成する方法について説明します。

![[参照] ボタン、[参照] ブレードの [DocumentDB アカウント]、[DocumentDB アカウント] ブレードの DocumentDB アカウントが強調表示されているデータベースの作成方法を示すスクリーン ショット](./media/documentdb-create-database/docdb-database-creation-1-3.png)

1.  [Azure プレビュー ポータル](https://portal.azure.com/)で、ジャンプバーの **[DocumentDB アカウント]** をクリックします。 

2.  **[DocumentDB アカウント]** ブレードで、DocumentDB NoSQL データベースを追加するデータベースのあるアカウントを選択します。アカウントが何も表示されていない場合は、[DocumentDB アカウントを作成する](documentdb-create-account.md)必要があります。

3. **[DocumentDB アカウント]** ブレードで、**[データベースの追加]** をクリックします。

4. **[データベースの追加]** ブレードで、新しいデータベースの ID を入力します。名前が検証されると、緑色のチェック マークが **[ID]** ボックスに表示されます。

5. 画面下部の **[OK]** をクリックすると、新しいデータベースが作成されます。

7. 新しいデータベースは、**[DocumentDB アカウント]** ブレードの **[データベース]** レンズに表示されます。
 
	![[DocumentDB アカウント] ブレードの新しいデータベースのスクリーン ショット](./media/documentdb-create-database/docdb-database-creation-7.png)

## DocumentDB データベースを作成するその他の方法

データベースは必ずしもプレビュー ポータルを使用して作成する必要はなく、[DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) を使用して作成することもできます。DocumentDB .NET SDK を使用してデータベースを作成する方法を示す C# コードのサンプルについては、DatabaseManagement プロジェクトの [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DatabaseManagement/Program.cs) ファイルを参照してください。このファイルは、[GitHub.com](https://github.com) の [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) リポジトリから入手できます。

## 次のステップ

DocumentDB データベースの作成方法を学習したら、次の手順は[コレクションの作成](documentdb-create-collection.md)です。

コレクションを作成できたら、プレビュー ポータルのドキュメント エクスプローラーを使用して[ JSON ドキュメントを追加](../documentdb-view-json-document-explorer.md)したり、DocumentDB データ移行ツールを使用してコレクションに[ドキュメントをインポート](documentdb-import-data.md)したり、いずれかの [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) を使用して CRUD 操作を実行できるようになります。DocumentDB には、.NET、Java、Python、Node.js、および JavaScript API の SDK があります。ドキュメントを作成、削除、更新、および削除する方法を示している .NET コードのサンプルについては、GitHub.com の azure-documentdb-net リポジトリにある DocumentManagement プロジェクトの [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) を参照してください。

コレクションにドキュメントを用意した後で、プレビュー ポータルの[クエリ エクスプローラー](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)、またはいずれかの [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) を使用することで、[DocumentDB SQL](documentdb-sql-query.md) を使用してドキュメントに対して[クエリを実行](documentdb-sql-query.md#executing-queries)することができます。

<!---HONumber=Nov15_HO4-->