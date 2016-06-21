<properties
	pageTitle="C# クエリによる SQL Database への接続 | Microsoft Azure"
	description="SQL データベースへのクエリおよび接続を実行するプログラムを C# で作成します。IP アドレス、接続文字列、セキュリティで保護されたログイン、および無料版の Visual Studio に関する情報。"
	services="sql-database"
	keywords="C# データベース クエリ, C# クエリ, データベースへの接続, SQL C#"
	documentationCenter=""
	authors="MightyPen"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="04/25/2016"
	ms.author="annemill"/>


# Visual Studio で SQL Database に接続する

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Visual Studio で Azure SQL Database に接続する方法について説明します。

## 前提条件


Visual Studio を使用して SQL Database に接続するには、以下のものが必要です。


- Azure アカウントとサブスクリプション。[無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。


- Azure SQL Database サービス上の **AdventureWorksLT** デモンストレーション データベース。
 - [デモ データベースの作成](sql-database-get-started.md)は数分で完了します。


- Visual Studio 2013 Update 4 以降。現在 Microsoft では、Visual Studio Community を*無料*で提供しています。
 - [Visual Studio Community のダウンロード](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [その他の無料版 Visual Studio の選択肢](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)
 - または、このトピックで後述する[手順](#InstallVSForFree)に従い、[Azure ポータル](https://portal.azure.com/)のガイドを利用して Visual Studio をインストールすることができます。


<a name="InstallVSForFree" id="InstallVSForFree"></a>

&nbsp;

## 手順 1. 無料版の Visual Studio Community をインストールする


Visual Studio のインストールが必要な場合、以下の方法でインストールできます。

- 無料ダウンロードやその他の選択肢が提供されている Visual Studio 製品ページにブラウザーでアクセスして、無料版の Visual Studio Community をインストールする。または
- 次の説明に従い、[Azure ポータル](https://portal.azure.com/)のガイドを利用してダウンロード ページにアクセスする。


### Azure ポータルから Visual Studio へ


1. [Azure ポータル](https://portal.azure.com/) (http://portal.azure.com/) からログインします。

2. **[すべて参照]**、**[SQL データベース]** の順にクリックします。データベースを検索するためのブレードが開きます。

3. ブレード上部付近にあるフィルターのテキスト ボックスに **AdventureWorksLT** データベースの名前を入力します。

4. お使いのサーバー上のデータベースに関する行が表示されたら、その行をクリックします。データベースに関するブレードが開きます。

5. 操作がしやすいように、1 つ前までの各ブレードで最小化のコントロールをクリックします。

6. データベース ブレードの上部付近にある **[Visual Studio で開く]** をクリックします。Visual Studio のインストール場所へのリンクを備えた、Visual Studio に関する新しいブレードが開きます。

	![[Visual Studio で開く] ボタン][20-OpenInVisualStudioButton]

7. **[Community (無料)]** リンク、またはそれに類似したリンクをクリックします。新しい Web ページが追加されます。

8. 新しい Web ページ上のリンクを使用して Visual Studio をインストールします。

9. Visual Studio のインストール後に、**[Visual Studio で開く]** ブレードで **[Visual Studio で開く]** をクリックします。Visual Studio が開きます。

10. Visual Studio では、**[SQL Server オブジェクト エクスプローラー]** ペインで使用するために、ダイアログの接続文字列フィールドに接続文字列を入力するよう求められます。
 - **[Windows 認証]** ではなく **[SQL Server 認証]** を選択します。
 - **AdventureWorksLT** データベースの指定も忘れずに行ってください (ダイアログ ボックスで **[オプション]**、**[接続プロパティ]** の順にクリック)。

11. **SQL Server オブジェクト エクスプローラー**で、データベースのノードを展開します。


## 手順 2. サンプル クエリを実行する

論理サーバーに接続すると、データベースに接続し、サンプル クエリを実行できます。

1. **オブジェクト エクスプローラー**で、サーバー上のアクセス許可を持っているデータベース (**AdventureWorks** サンプル データベースなど) に移動します。
2. データベースを右クリックし、**[新しいクエリ]** を選択します。

	![新しいクエリ。SQL Database サーバーへの接続: SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)

3. クエリ ウィンドウに、次のコードをコピーして貼り付けます。

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. **[実行]** ボタンをクリックします。次のスクリーン ショットに、成功したクエリを示します。

	![成功。Connect to SQL Database server: SVisual Studio](./media/sql-database-connect-query-ssms/5-success.png)

## 次のステップ

[.NET (C#) を使用して SQL Database に接続する](sql-database-develop-dotnet-simple.md)


<!-- Image references. -->

[20-OpenInVisualStudioButton]: ./media/sql-database-connect-query/connqry-free-vs-e.png

<!---HONumber=AcomDC_0615_2016-->