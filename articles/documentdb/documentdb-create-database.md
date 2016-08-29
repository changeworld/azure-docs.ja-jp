<properties 
	pageTitle="DocumentDB でデータベースを作成する方法 |Microsoft Azure" 
	description="きわめて高速なグローバル スケールの NoSQL データベースである Azure DocumentDB のオンライン サービス ポータルを使用してデータベースを作成する方法について説明します。" 
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
	ms.date="08/15/2016" 
	ms.author="mimig"/>

# Azure ポータルを使用して DocumentDB のデータベースを作成する方法

Microsoft Azure DocumentDB を使用するには、[DocumentDB アカウント](documentdb-create-account.md)、データベース、コレクション、およびドキュメントを用意する必要があります。このトピックでは、Microsoft Azure ポータルで DocumentDB データベースを作成する方法について説明します。いずれかの SDK を使用してデータベースを作成する方法については、「[DocumentDB データベースを作成するその他の方法](#other-ways-to-create-a-documentdb-database)」を参照してください。

1.  [Azure ポータル](https://portal.azure.com/)で、ジャンプバーの **[DocumentDB アカウント]** をクリックします。**[DocumentDB アカウント]** が表示されない場合は、**[More Services (その他のサービス)]** をクリックし、**[DocumentDB アカウント]** をクリックします。


    ![Screen shot showing how to create a database, highlighting DocumentDB Accounts on the Browse blade, and a DocumentDB account on the DocumentDB Accounts blade](./media/documentdb-create-database/docdb-database-creation-1-2.png)

2.  **[DocumentDB アカウント]** ブレードで、DocumentDB NoSQL データベースを追加するデータベースのあるアカウントを選択します。アカウントが何も表示されていない場合は、[DocumentDB アカウントを作成する](documentdb-create-account.md)必要があります。

3. **[DocumentDB アカウント]** ブレードで、**[データベースの追加]** をクリックします。

    ![Screen shot showing how to create a database, highlighting the Add Database button, the ID box, and the OK button](./media/documentdb-create-database/docdb-database-creation-3-5.png)

4. **[データベースの追加]** ブレードで、新しいデータベースの ID を入力します。名前が検証されると、緑色のチェック マークが **[ID]** ボックスに表示されます。

5. 画面下部の **[OK]** をクリックすると、新しいデータベースが作成されます。

6. 新しいデータベースは、**[DocumentDB アカウント]** ブレードの **[データベース]** レンズに表示されます。
 
	![[DocumentDB アカウント] ブレードの新しいデータベースのスクリーン ショット](./media/documentdb-create-database/docdb-database-creation-6.png)

## DocumentDB データベースを作成するその他の方法

データベースは必ずしもポータルを使用して作成する必要はなく、[DocumentDB SDK](documentdb-sdk-dotnet.md) または [REST API](https://msdn.microsoft.com/library/mt489072.aspx) を使用して作成することもできます。.NET SDK を使用してデータベースを操作する方法については、「[データベースのサンプル](documentdb-dotnet-samples.md#database-examples)」を参照してください。Node.js SDK を使用してデータベースを操作する方法については、「[データベースのサンプル](documentdb-nodejs-samples.md#database-examples)」を参照してください。

## 次のステップ

DocumentDB データベースの作成方法を学習したら、次の手順は[コレクションの作成](documentdb-create-collection.md)です。

コレクションを作成できたら、ポータルのドキュメント エクスプローラーを使用して[ JSON ドキュメントを追加](documentdb-view-json-document-explorer.md)したり、DocumentDB データ移行ツールを使用してコレクションに[ドキュメントをインポート](documentdb-import-data.md)したり、いずれかの [DocumentDB SDK](documentdb-sdk-dotnet.md) を使用して CRUD 操作を実行できるようになります。DocumentDB には、.NET、Java、Python、Node.js、および JavaScript API の SDK があります。ドキュメントを作成、削除、更新、削除する方法が紹介されている .NET コード サンプルについては、「[ドキュメントのサンプル](documentdb-dotnet-samples.md#document-examples)」を参照してください。Node.js SDK を使用してドキュメントを操作する方法については、「[ドキュメントのサンプル](documentdb-nodejs-samples.md#document-examples)」を参照してください。

コレクションにドキュメントを用意した後で、ポータルの[クエリ エクスプローラー](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)、またはいずれかの [SDK](documentdb-sdk-dotnet.md) を使用することで、[DocumentDB SQL](documentdb-sql-query.md) を使用してドキュメントに対して[クエリを実行](documentdb-sql-query.md#executing-sql-queries)することができます。

<!---HONumber=AcomDC_0817_2016-->