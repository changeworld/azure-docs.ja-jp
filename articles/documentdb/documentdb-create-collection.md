<properties 
	pageTitle="DocumentDB データベース コレクションの作成 |Microsoft Azure" 
	description="JSON の管理された NoSQL ドキュメント データベースである Azure DocumentDB 用のオンライン サービス ポータルを使用して、コレクションを作成する方法について説明します。無料評価版を今すぐ入手してください。" 
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
	ms.date="07/08/2015" 
	ms.author="mimig"/>

# Azure プレビュー ポータルを使用して DocumentDB コレクションを作成する

Microsoft Azure DocumentDB を使用するには、[DocumentDB アカウント](documentdb-create-account.md)、[データベース](documentdb-create-database.md)、コレクション、およびドキュメントを用意する必要があります。このトピックでは、Azure プレビュー ポータルで DocumentDB コレクションを作成する方法について説明します。

コレクションは必ずしもプレビュー ポータルを使用して作成する必要はなく、[DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) を使用して作成することもできます。DocumentDB .NET SDK を使用してコレクションを作成する方法を示す C# コードのサンプルについては、CollectionManagement プロジェクトの [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/CollectionManagement/Program.cs) ファイルを参照してください。このファイルは、[GitHub.com](https://github.com) の [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) リポジトリから入手できます。

1.  [Azure プレビュー ポータル](https://portal.azure.com/)で **[すべてを参照]** をクリックします。

2.  **[参照]** ブレードで、**[DocumentDB アカウント]** をクリックします。

3.  **[DocumentDB アカウント]** ブレードで、コレクションを追加するデータベースのあるアカウントを選択します。アカウントが何も表示されていない場合は、[DocumentDB アカウントを作成する](documentdb-create-account.md)必要があります。
    
    ![Screen shot highlighting the Browse button, DocumentDB Accounts on the Browse blade, and a DocumentDB account on the DocumentDB Accounts blade](./media/documentdb-create-collection/docdb-database-creation-1-3.png)

4. **[データベース]** ブレードで、**[コレクションの追加]** をクリックします。

5. **[コレクションの追加]** ブレードで、新しいコレクションの ID を入力します。名前が検証されると、緑色のチェック マークが [ID] ボックスに表示されます。

6. 新しいコレクションの価格レベルを選択します。作成するそれぞれのコレクションが、課金対象のエンティティになります。使用可能なパフォーマンス レベルの詳細については、[DocumentDB のパフォーマンス レベル](documentdb-performance-levels.md)に関するページを参照してください。

7. 次のいずれかの **[インデックス作成ポリシー]** を選択します。

	- **[既定]**。このポリシーは、文字列に対しては等値クエリを実行し、数値に対しては ORDER BY、範囲、および等値クエリを使用する場合に最適です。**[範囲]** よりもインデックスのストレージ オーバーヘッドが少なくいポリシーです。
	- **[ハッシュ]**。このポリシーは、数値と文字列の両方に対して等値クエリを実行する場合に最適です。インデックスのストレージ オーバーヘッドが最も低いポリシーです。
	- **[範囲]**。このポリシーは、数値と文字列の両方に対してORDER BY、範囲、等値クエリを使用する場合に最適です。**[既定]** または **[ハッシュ]** よりもインデックスのストレージ オーバーヘッドが高いポリシーです。

	インデックス作成ポリシーの詳細については、「[DocumentDB インデックス作成ポリシー](documentdb-indexing-policies.md)」を参照してください。

8. 画面下部の **[OK]** をクリックすると、新しいコレクションが作成されます。

	![Screen shot highlighting the Add Collection button on the Database blade, the ID box on the Add Collection blade, and the OK button](./media/documentdb-create-collection/docdb-collection-creation-4-7.png)

9. 新しいコレクションは、**[データベース]** ブレードの **[コレクション]** レンズに表示されます。
 
	![Screen shot of the new database in the DocumentDB Account blade](./media/documentdb-create-collection/docdb-collection-creation-8.png)

## 次のステップ

コレクションを作成できたら、次の手順は、コレクションへのドキュメントの追加またはインポートです。コレクションへのドキュメントの追加方法には、いくつかの選択肢があります。

- プレビュー ポータルのドキュメント エクスプローラーを使用して[ドキュメントを追加](../documentdb-view-json-document-explorer.md)できます。
- DocumentDB データ移行ツールを使用して[ドキュメントとデータをインポート](documentdb-import-data.md)できます。このツールでは、JSON ファイルと CSV ファイルをインポートできるほか、SQL Server、MongoDB、Azure テーブル ストレージ、その他の DocumentDB コレクションからデータをインポートできます。 
- いずれかの [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) を使用してドキュメントを追加できます。DocumentDB には、.NET、Java、Python、Node.js、および JavaScript API の SDK があります。[GitHub.com](https://github.com) の [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) リポジトリから入手できる DocumentManagement プロジェクトの [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) ファイルに、DocumentDB の .NET SDK を使用したドキュメントに対する CRUD 操作を示します。

コレクションにドキュメントを用意した後で、プレビュー ポータルの[クエリ エクスプローラー](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)、またはいずれかの [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) を使用することで、[DocumentDB SQL](documentdb-sql-query.md) を使用してドキュメントに対して[クエリを実行](documentdb-sql-query.md#executing-queries)することができます。

<!---HONumber=August15_HO6-->