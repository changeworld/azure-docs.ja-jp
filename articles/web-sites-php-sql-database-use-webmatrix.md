<properties 
	pageTitle="Azure App Service での WebMatrix を使用した PHP-SQL Web アプリの作成とデプロイ" 
	description="無料の WebMatrix IDE を使用して、SQL Database にデータを保存する Azure App Service に PHP Web サイトを作成およびデプロイする方法を示すチュートリアル。" 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="tomfitz"/>





# Azure App Service での WebMatrix を使用した PHP-SQL Web アプリの作成とデプロイ

このチュートリアルでは、Azure SQL Database を使用する PHP アプリケーションを WebMatrix で作成し、[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps にデプロイする方法を示します。WebMatrix は、Microsoft から提供されている無料の Web 開発ツールで、Web アプリの開発に必要なものがすべて用意されています。WebMatrix では PHP がサポートされており、PHP 開発用 Intellisense が含まれています。

このチュートリアルは、アプリケーションをローカルでテストできるように、コンピューターに [SQL Server Express][install-SQLExpress] がインストールされていることを前提としています。ただし、SQL Server Express をインストールせずにチュートリアルを完了することもできます。その場合は、アプリケーションを直接 Azure App Service Web Apps にデプロイします。

このチュートリアルを完了すると、Azure で動作する SQL Database Web アプリが完成します。
 
学習内容:

* [Azure プレビュー ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)を使用して App Service Web Apps および SQL Database で Web アプリを作成する方法。Web Apps では PHP が既定で有効になっているため、特に何もしなくても PHP コードを実行できます。
* WebMatrix を使用して PHP アプリケーションを作成する方法。
* WebMatrix を使用して Azure にアプリケーションを発行および再発行する方法。
 
このチュートリアルでは、タスク一覧用の単純な Web アプリケーション (Tasklist) を PHP で作成します。アプリケーションは、App Service Web Apps でホストされます。実行中のアプリケーションのスクリーンショットは次のようになります。

![Azure PHP Web Site][running-app]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、「[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

##前提条件

1. Tasklist アプリケーションのファイルを[ダウンロード][tasklist-sqlazure-download]します。Tasklist アプリケーションは、タスク一覧のアイテムの追加、完了済みとしてのマーク付け、および削除を行うための、単純な PHP アプリケーションです。タスク一覧のアイテムは SQL データベースに保存されます (ローカル テストには SQL Server Express を使用します)。アプリケーションは、次のファイルで構成されます。

	* **index.php**: タスクを表示し、一覧にアイテムを追加するためのフォームを提供します。
	* **additem.php**: 一覧にアイテムを追加します。
	* **getitems.php**: データベース内のアイテムをすべて取得します。
	* **markitemcomplete.php**: アイテムのステータスを "完了済み" に変更します。
	* **deleteitem.php**: アイテムを削除します。
	* **taskmodel.php**: データベース内のアイテムの追加、取得、更新、および削除を行う関数が含まれています。
	* **createtable.php**: アプリケーション用の SQL Database テーブルを作成します。このファイルは 1 度しか呼び出されません。

2. `tasklist` という SQL Server データベースを作成します。これには、`sqlcmd` コマンド プロンプトで次のコマンドを実行します。

		>sqlcmd -S <server name>\sqlexpress -U <user name> -P <password>
		1> create database tasklist
		2> GO

	この手順は、アプリケーションをローカルでテストする場合にのみ必要です。

## Web アプリと SQL Database の作成

Azure の Web アプリと SQL データベースを作成するには、次の手順に従います。

1. [Azure プレビュー ポータル](https://portal.azure.com)にログインします。

2. Azure Marketplace を開きます。**[Marketplace]** アイコンをクリックするか、ダッシュボードの左下にある **[新規]** アイコンをクリックし、**[Web + モバイル]** を選択し、下部にある **[Azure Marketplace]** をクリックします。
	
3. Marketplace で、**[Web アプリ]** を選択します。

4. **[Web アプリ + SQL]** アイコンをクリックします。

5. Web アプリ + SQL アプリの説明を読み、**[作成]** を選択します。

6. 各部分 ([リソース グループ]、**[Web アプリ]**、**[データベース]**、**[サブスクリプション]**) をクリックし、必要なフィールドに値を入力するか、値を選択します。
	
	- 好みの URL 名を入力します	
	- データベース サーバーの資格情報を構成します。
	- 最も近いリージョンを選択します

	![configure your app](./media/web-sites-php-sql-database-use-webmatrix/configure-db-settings.png)

7. Web アプリの定義が完了したら、**[作成]** をクリックします。

	Web アプリが作成されると、**[通知]** ボタンが緑色の **[成功]** と点滅し、リソース グループ ブレードが開いてグループの Web アプリと SQL Database 双方が表示されます。

4. リソース グループ ブレードの Web アプリのアイコンをクリックして Web アプリのブレードを開きます。

	![Web アプリ リソース グループ](./media/web-sites-php-sql-database-use-webmatrix/resource-group-blade.png)

##SQL データベース接続情報を取得する

Web アプリにリンクされている SQL データベース インスタンスに接続するには、データベースの作成時に指定した接続情報が必要になります。SQL データベース接続情報を取得するには、次の手順に従います。

1. リソース グループのブレードに戻り、SQL データベース アイコンをクリックします。

2. SQL Database ブレードで **[プロパティ]** をクリックし、**[データベース接続文字列を表示]** をクリックします。

	![データベースのプロパティの表示](./media/web-sites-php-sql-database-use-webmatrix/view-database-properties.png)
	
3. 表示されたダイアログの **[PHP]** セクションから、`Server`、`SQL Database`、および `User Name` の値をメモします。この値は後ほど PHP Web アプリを Azure App Service に発行する際に使用します。

## WebMatrix でのアプリケーションの作成

次に示す数ステップで、Tasklist アプリケーションを作成します。これには、ダウンロードしておいたファイルを追加し、いくつかの変更を行います。ただし、独自の既存ファイルを追加することも、新しいファイルを作成することもできます。

1. [Microsoft WebMatrix](http://www.microsoft.com/web/webmatrix/) を起動します。まだインストールされていない場合は、まずインストールします。
2. 初めて WebMatrix 3 を使用する場合は、Azure へのサインインを求めるメッセージが表示されます。そうでない場合は、**[サインイン]** ボタンをクリックし、**[アカウントの追加]** を選択します。Microsoft アカウントを使用して**サインイン**することを選択します。

	![アカウントの追加](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-add-account.png)

3. Azure アカウントにサインアップしている場合は、Microsoft アカウントを使用してログインできます。

	![Azure へのサインイン](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-sign-in.png)

1. スタート画面で **[新規作成]** ボタンをクリックし、**[テンプレート ギャラリー]** を選択して、テンプレート ギャラリーから新しいサイトを作成します。

	![テンプレート ギャラリーからの新しいサイト](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-site-from-template.png)

4. 使用できるテンプレートから、**[PHP]** を選択します。

	![Site from template][site-from-template]

5. **[空のサイト]** テンプレートを選択します。サイトの名前を指定し、**[次へ]** をクリックします。

	![Provide name for site][site-from-template-2]

3. Azure にサインインする場合は、ここでローカル サイト用の Azure App Service Web Apps インスタンスを作成することができます。現時点では **[スキップ]** を選択します。

	![Azure でのサイトの作成](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-site-from-template-azure.png)

1. WebMatrix によるローカル サイトの構築が終了すると、WebMatrix IDE が表示されます。**[既存を追加]** をクリックしてアプリケーション ファイルを追加します。

	![WebMatrix - Add existing files][edit_addexisting]

	表示されたダイアログで、ダウンロードしておいたファイルの場所に移動し、すべて選択して、[開く] をクリックします。確認のメッセージが表示されたら、`index.php` ファイルの置き換えを選択します。

2. 次に、ローカル SQL Server データベースの接続情報を `taskmodel.php` ファイルに追加する必要があります。`taskmodel.php` ファイルをダブルクリックして開き、`connect` 関数内のデータベース接続情報を更新します (**注**: アプリケーションをローカルでテストせず、Azure App Service Web Apps に直接発行する場合は、「[アプリケーションの発行](#Publish)」を参照してください)。

		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	`taskmodel.php` ファイルを保存します。

3. アプリケーションを実行するには、`items` テーブルを作成する必要があります。`createtable.php` ファイルを右クリックし、**[ブラウザーで起動]** を選択します。これにより、`createtable.php` がブラウザーで起動し、`tasklist` データベースに `items` テーブルを作成するコードが実行されます。

	![WebMatrix - Launch createtable.php in browser][edit_run]

4. これで、アプリケーションのテストをローカルで行うことができます。`index.php` ファイルを右クリックし、**[ブラウザーで起動]** を選択します。アイテムの追加、完了済みとしてのマーク付け、および削除を行うことにより、アプリケーションをテストします。

<a id="Publish"></a>
## アプリケーションの発行

アプリケーションを App Service Web Apps に発行する前に、`taskmodel.php` ファイル内のデータベース接続情報を、先ほど (「[Web アプリと SQL Database の作成](#CreateWebsite)」セクションで) 取得した接続情報に更新する必要があります。

1. `taskmodel.php` ファイルをダブルクリックして開き、`connect` 関数内のデータベース接続情報を更新します。

		// DB connection info
		$host = "value of $serverName";
		$user = "value of UID";
		$pwd = "the SQL password you created when creating the web app";
		$db = "value of Database";
	
	`taskmodel.php` ファイルを保存します。

2. WebMatrix で **[発行]** をクリックします。

	![WebMatrix - Publish][edit_publish]

3. **[Microsoft Azure から既存のサイトを選択]** をクリックします。

	![](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-publish-existing-site.png)

3. 先ほど作成した App Service の Web アプリを選択します。

	![](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-publish-existing-site-choose.png)

3. WebMatrix がサイトを Azure App Service Web Apps に発行するまで **[Continue]** をクリックします。

3. http://[your Web サイト名].azurewebsites.net/createtable.php に移動すると、`items` テーブルを作成できます。

4. 最後に、http://[your web site name].azurewebsites.net/index.php に移動してアプリケーションを起動します。
	
##アプリケーションの変更と再発行

アプリケーションを変更するには、サイトのローカル コピーを編集して、再発行する方法と、**リモート** モードでサイトを直接編集する方法があります。ここでは、`index.php` ファイルの見出しに簡単な変更を加え、それをライブ App Service Web アプリへ直接保存します。

1. WebMatrix で目的のサイトの **[リモート]** タブをクリックし、**[リモート ビューを開く]** を選択します。これによりリモート ファイルが開かれ (Web Apps 上でホスト) 、直接編集できます。![WebMatrix - Open Remote View][OpenRemoteView]
 
2. `index.php` ファイルをダブルクリックして開きます。![WebMatrix - インデックス ファイルを開く][Remote_editIndex]

3. **title** タグと **h1** タグの **My ToDo List** を **My Task List** に変更し、このファイルを保存します。


4. ファイルを保存したら [実行] をクリックし、ライブ App Service Web アプリで変更内容を確認します。![WebMatrix - リモートでサイトを実行][Remote_run]



## 次のステップ

ここまで、WebMatrix から Web アプリを作成して Azure App Service Web Apps にデプロイする方法を学習しました。WebMatrix の詳細については、[WebMatrix の Web サイト](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)を参照してください。

## 変更内容
* Web サイトから App Service への変更ガイドについては、「[Azure App Service および既存の Azure サービスへの影響](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* ポータルからプレビュー ポータルへの変更ガイドについては、[プレビュー ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)を参照してください。




[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[running-app]: ./media/web-sites-php-sql-database-use-webmatrix/tasklist_app_windows.png
[tasklist-sqlazure-download]: http://go.microsoft.com/fwlink/?LinkId=252504
[NewWebSite1]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite1.jpg
[NewWebSite2]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite2.png
[NewWebSite3_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite3_SQL.png
[NewWebSite4_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite4_SQL.png

[NewWebSite6_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite6_SQL.png
[NewWebSite7]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite7.png

[InstallWebMatrix]: ./media/web-sites-php-sql-database-use-webmatrix/InstallWebMatrix.png
[download-site]: ./media/web-sites-php-sql-database-use-webmatrix/download-site-1.png
[site-from-template]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template.png
[site-from-template-2]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template-2.png
[edit_addexisting]: ./media/web-sites-php-sql-database-use-webmatrix/edit_addexisting.png
[edit_run]: ./media/web-sites-php-sql-database-use-webmatrix/edit_run.png
[edit_publish]: ./media/web-sites-php-sql-database-use-webmatrix/edit_publish.png
[OpenRemoteView]: ./media/web-sites-php-sql-database-use-webmatrix/OpenRemoteView.png
[Remote_editIndex]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_editIndex.png
[Remote_run]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_run.png

[preview-portal]: https://manage.windowsazure.com









<!--HONumber=54-->