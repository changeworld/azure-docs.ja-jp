<properties
	pageTitle="C# クエリによる SQL Database への接続 | Microsoft Azure"
	description="SQL データベースへのクエリおよび接続を実行するプログラムを C# で作成します。IP アドレス、接続文字列、セキュリティで保護されたログイン、および無料版の Visual Studio に関する情報。"
	services="sql-database"
	keywords="C# データベース クエリ, C# クエリ, データベースへの接続, SQL C#"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="08/17/2016"
	ms.author="stevestein"/>



# Visual Studio で SQL Database に接続する

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Visual Studio で Azure SQL データベースに接続する方法について説明します。

## 前提条件


Visual Studio を使用して SQL データベースに接続するには、以下のものが必要です。


- 接続先の SQL データベース。この記事では、**AdventureWorks** サンプル データベースを使用します。AdventureWorks サンプル データベースを入手するには、[デモ データベースの作成](sql-database-get-started.md)に関するページを参照してください。


- Visual Studio 2013 Update 4 以降。現在 Microsoft では、Visual Studio Community を *無料* で提供しています。
 - [Visual Studio Community のダウンロード](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [その他の無料版 Visual Studio の選択肢](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)




## Azure ポータルから Visual Studio を開く


1. [Azure ポータル](https://portal.azure.com/)にログインします。

2. **[More Services (その他のサービス)]**、**[SQL データベース]** の順にクリックします。
3. *AdventureWorks* データベースを見つけてクリックして、**AdventureWorks** データベース ブレードを開きます。

6. データベース ブレードの上部にある **[ツール]** ボタンをクリックします。

	![新しいクエリ。SQL Database サーバーへの接続: SQL Server Management Studio](./media/sql-database-connect-query/tools.png)

7. **[Visual Studio で開く]** をクリックします (Visual Studio が必要な場合は、ダウンロード リンクをクリックしてください)。

	![新しいクエリ。SQL Database サーバーへの接続: SQL Server Management Studio](./media/sql-database-connect-query/open-in-vs.png)


8. Visual Studio が開かれます。**[サーバーへの接続]** ウィンドウは既に、ポータルで選択したサーバーとデータベースに接続するように設定されています(**[オプション]** をクリックし、接続が正しいデータベースに設定されていることを確認します)。 サーバー管理者パスワードを入力し、**[接続]** をクリックします。


	![新しいクエリ。SQL Database サーバーへの接続: SQL Server Management Studio](./media/sql-database-connect-query/connect.png)


8. 自分のコンピューターの IP アドレス用にファイアウォール規則をセットアップしていない場合は、ここで "*接続できません*" というメッセージが表示されます。ファイアウォール規則を作成するには、「[Azure ポータルを使用して Azure SQL Database のサーバー レベルのファイアウォール規則を作成する](sql-database-configure-firewall-settings.md)」を参照してください。


9. 正常に接続すると、データベースに接続した状態で、**[SQL Server オブジェクト エクスプローラー]** ウィンドウが開かれます。

	![新しいクエリ。SQL Database サーバーへの接続: SQL Server Management Studio](./media/sql-database-connect-query/sql-server-object-explorer.png)


## サンプル クエリの実行

これでデータベースに接続できたので、次の手順では単純なクエリを実行する方法を示します。

2. データベースを右クリックし、**[新しいクエリ]** を選択します。

	![新しいクエリ。SQL Database サーバーへの接続: SQL Server Management Studio](./media/sql-database-connect-query/new-query.png)

3. クエリ ウィンドウに、次のコードをコピーして貼り付けます。

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. クエリを実行するには、**[実行]** ボタンをクリックします。

	![成功。Connect to SQL Database server: SVisual Studio](./media/sql-database-connect-query/run-query.png)

## 次のステップ

- Visual Studio で SQL データベースを開くには、SQL Server Data Tools を使用します。詳細については、「[SQL Server Data Tools](https://msdn.microsoft.com/library/hh272686.aspx)」を参照してください。
- コードを使用して SQL データベースに接続するには、「[.NET (C#) を使用して SQL Database に接続する](sql-database-develop-dotnet-simple.md)」を参照してください。

<!-------HONumber=AcomDC_0824_2016-->