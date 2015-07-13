<properties 
	pageTitle="Azure App Service での WebMatrix を使用した PHP-MySQL Web アプリの作成とデプロイ" 
	description="無料の WebMatrix IDE を使用して、MySQL にデータを保存する Azure App Service に PHP Web サイトを作成およびデプロイする方法を示すチュートリアル。"
	tags="azure-portal" 
	services="app-service\web" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="04/22/2015" 
	ms.author="tomfitz"/>





# Azure App Service での WebMatrix を使用した PHP-MySQL Web アプリの作成とデプロイ

このチュートリアルでは、WebMatrix を使用して PHP-MySQL アプリケーションを作成し、[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps にデプロイする方法を示します。WebMatrix は、Microsoft から提供されている無料の Web 開発ツールで、Web サイトの開発に必要なものがすべて用意されています。WebMatrix では PHP がサポートされており、PHP 開発用 Intellisense が含まれています。

このチュートリアルは、アプリケーションをローカルでテストできるように、コンピューターに [MySQL][install-mysql] がインストールされていることを前提としています。ただし、MySQL をインストールせずにチュートリアルを完了することもできます。その場合は、アプリケーションを直接 Azure App Service Web Apps にデプロイします。

このチュートリアルを完了すると、Web Apps で動作する PHP/MySQL Web サイトが完成します。
 
学習内容:

* [Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)を使用して App Service Web Apps の Web サイトと MySQL データベースを作成する方法。Web Apps では PHP が既定で有効になっているため、特に何もしなくても PHP コードを実行できます。
* WebMatrix を使用して PHP アプリケーションを作成する方法。
* WebMatrix を使用して Web Apps にアプリケーションを発行および再発行する方法。
 
このチュートリアルでは、タスク一覧用の単純な Web アプリケーション (Tasklist) を PHP で作成します。アプリケーションは、App Service Web Apps でホストされます。実行中のアプリケーションのスクリーンショットは次のようになります。

![Azure PHP Web Site][running-app]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、「[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

##前提条件

1. Tasklist アプリケーションのファイルを[ダウンロード][tasklist-mysql-download]します。Tasklist アプリケーションは、タスク一覧のアイテムの追加、完了済みとしてのマーク付け、および削除を行うための、単純な PHP アプリケーションです。タスク一覧のアイテムは MySQL データベースに保存されます。アプリケーションは、次のファイルで構成されます。

	* **additem.php**: 一覧にアイテムを追加します。
	* **createtable.php**: アプリケーション用の MySQL テーブルを作成します。このファイルは 1 度しか呼び出されません。
	* **deleteitem.php**: アイテムを削除します。
	* **getitems.php**: データベース内のアイテムをすべて取得します。
	* **index.php**: タスクを表示し、一覧にアイテムを追加するためのフォームを提供します。
	* **markitemcomplete.php**: アイテムのステータスを "完了済み" に変更します。
	* **taskmodel.php**: データベース内のアイテムの追加、取得、更新、および削除を行う関数が含まれています。

1. "`tasklist`" というローカルの MySQL データベースを作成します。そのためには、WebMatrix の Database ワークスペースから作成するか (このチュートリアルでこの後にインストールします)、MySQL コマンド プロンプトで次のコマンドを実行します。

		mysql> create database tasklist;

	この手順は、アプリケーションをローカルでテストする場合にのみ必要です。

## Web アプリと MySQL データベースの作成

Web アプリと MySQL データベースを作成するには、次のステップに従います。

1. [Azure ポータル](https://portal.azure.com)にログインします。

2. ポータルの左下にある **[新規]** アイコンをクリックします。

	![新しい Azure の Web サイトの作成](./media/web-sites-php-mysql-use-webmatrix/new_website2.png)

3. **[Web + モバイル]** をクリックし、**[Web アプリ + MySQL]** をクリックします。

	![新しい Web サイトのカスタム作成](./media/web-sites-php-mysql-use-webmatrix/create_web_mysql.png)

4. リソース グループの有効な名前を入力します。

    ![リソース グループ名の設定](./media/web-sites-php-mysql-use-webmatrix/set_group.png)

5. 新しい Web アプリについての値を入力します。

    ![Web アプリの作成](./media/web-sites-php-mysql-use-webmatrix/create_wa.png)

6. 法律条項への同意も含めて、新しいデータベースについての値を入力します。

	![Create new MySQL database](./media/web-sites-php-mysql-use-webmatrix/create_db.png)

	Web アプリが作成されると、新しいリソース グループが表示されます。

## MySQL のリモート接続情報の取得

Web Apps で実行されている MySQL データベースに接続するには、接続情報が必要になります。MySQL の接続情報を取得するには、次の手順に従います。

1. リソース グループで、データベースをクリックします。

	![データベースの選択](./media/web-sites-php-mysql-use-webmatrix/select_database.png)

2. データベースのサマリで、**[プロパティ]** を選択します。

    ![プロパティの選択](./media/web-sites-php-mysql-use-webmatrix/select_properties.png)

2. `Database`、`Host`、`User Id`、`Password` の各値をメモします。

    ![プロパティへの注記](./media/web-sites-php-mysql-use-webmatrix/note-properties.png)

## WebMatrix でのアプリケーションの作成

次に示す数ステップで、Tasklist アプリケーションを作成します。これには、ダウンロードしておいたファイルを追加し、いくつかの変更を行います。ただし、独自の既存ファイルを追加することも、新しいファイルを作成することもできます。

1. [Microsoft WebMatrix](http://www.microsoft.com/web/webmatrix/) を起動します。まだインストールされていない場合は、まずインストールします。
2. 初めて WebMatrix 3 を使用する場合は、Azure へのサインインを求めるメッセージが表示されます。そうでない場合は、**[サインイン]** ボタンをクリックし、**[アカウントの追加]** を選択します。Microsoft アカウントを使用して**サインイン**することを選択します。

	![アカウントの追加](./media/web-sites-php-mysql-use-webmatrix/webmatrix-add-account.png)

3. Azure アカウントにサインアップしている場合は、Microsoft アカウントを使用してログインできます。

	![Azure へのサインイン](./media/web-sites-php-mysql-use-webmatrix/webmatrix-sign-in.png)

1. スタート画面で **[新規作成]** ボタンをクリックし、**[テンプレート ギャラリー]** を選択して、テンプレート ギャラリーから新しいサイトを作成します。

	![テンプレート ギャラリーからの新しいサイト](./media/web-sites-php-mysql-use-webmatrix/webmatrix-site-from-template.png)

4. 使用できるテンプレートから、**[PHP]** を選択します。

	![Site from template][site-from-template]

5. **[空のサイト]** テンプレートを選択します。サイトの名前を指定し、**[次へ]** をクリックします。

	![Provide name for site][site-from-template-2]

	サイトが WebMatrix で開き、既定のファイルが配置されます。

	次に示す数ステップで、Tasklist アプリケーションを作成します。これには、ダウンロードしておいたファイルを追加し、いくつかの変更を行います。ただし、独自の既存ファイルを追加することも、新しいファイルを作成することもできます。

6. **[既存を追加]** をクリックしてアプリケーション ファイルを追加します。

	![WebMatrix - Add existing files][edit_addexisting]

	表示されたダイアログで、ダウンロードしておいたファイルの場所に移動し、すべて選択して、[開く] をクリックします。確認のメッセージが表示されたら、`index.php` ファイルの置き換えを選択します。

7. 次に、ローカル MySQL データベースの接続情報を `taskmodel.php` ファイルに追加する必要があります。`taskmodel.php` ファイルをダブルクリックして開き、`connect` 関数内のデータベース接続情報を更新します (**注**: アプリケーションをローカルでテストせず、Azure App Service Web Apps に直接発行する場合は、「[アプリケーションの発行](#Publish)」を参照してください)。

		// DB connection info
		$host = "localhost";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	`taskmodel.php` ファイルを保存します。

8. アプリケーションを実行するには、`items` テーブルを作成する必要があります。`createtable.php` ファイルを右クリックし、**[ブラウザーで起動]** を選択します。これにより、`createtable.php` がブラウザーで起動し、`tasklist` データベースに `items` テーブルを作成するコードが実行されます。

	![WebMatrix - Launch createtable.php in browser][edit_run]

9. これで、アプリケーションのテストをローカルで行うことができます。`index.php` ファイルを右クリックし、**[ブラウザーで起動]** を選択します。アイテムの追加、完了済みとしてのマーク付け、および削除を行うことにより、アプリケーションをテストします。


## アプリケーションの発行

アプリケーションを App Service Web Apps に発行する前に、`taskmodel.php` ファイル内のデータベース接続情報を、先ほど (「[Web アプリと MySQL Database の作成](#CreateWebsite)」セクションで) 取得した接続情報に更新する必要があります。

1. `taskmodel.php` ファイルをダブルクリックして開き、`connect` 関数内のデータベース接続情報を更新します。

		// DB connection info
		$host = "value of Data Source";
		$user = "value of User Id";
		$pwd = "value of Password";
		$db = "value of Database";
	
	`taskmodel.php` ファイルを保存します。

2. WebMatrix で **[発行]** をクリックします。

	![WebMatrix - Publish][edit_publish]

3. **[Windows Azure から既存のサイトを選択]** を選択します。

	![](./media/web-sites-php-mysql-use-webmatrix/webmatrix-publish-existing-site.png)

3. 先ほど作成した App Service の Web アプリを選択します。

	![](./media/web-sites-php-mysql-use-webmatrix/webmatrix-publish-existing-site-choose.png)

3. WebMatrix がサイトを Azure App Service Web Apps に発行するまで **[Continue]** をクリックします。

3. http://[your Web サイト名].azurewebsites.net/createtable.php に移動すると、`items` テーブルを作成できます。

4. 最後に、http://[your web site name].azurewebsites.net/index.php に移動してアプリケーションを使用します。
	
##アプリケーションの変更と再発行

アプリケーションを変更するには、前にダウンロードしたサイトのローカル コピーを編集して、再発行する方法と、リモート モードでサイトを直接編集する方法があります。ここでは、`index.php` ファイルの見出しに簡単な変更を加え、それをライブ サイトへ直接保存します。

1. WebMatrix で目的のサイトの [リモート] タブをクリックし、**[リモート ビューを開く]** を選択します。リモート サイトが開き、直接編集できるようになります。![WebMatrix - Open Remote View][OpenRemoteView]
 
2. `index.php` ファイルをダブルクリックして開きます。![WebMatrix - インデックス ファイルを開く][Remote_editIndex]

3. **title** タグと **h1** タグの **My ToDo List** を **My Task List** に変更し、このファイルを保存します。


4. ファイルを保存したら [実行] をクリックし、ライブ サイトで変更内容を確認します。![WebMatrix - リモートでサイトを実行][Remote_run]


## 次のステップ

* [WebMatrix の Web サイト](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)

## 変更内容
* Web サイトから App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* 古いポータルから新しいポータルへの変更ガイドについては、「[プレビュー ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)」を参照してください。




[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[running-app]: ./media/web-sites-php-mysql-use-webmatrix/tasklist_app_windows.png
[tasklist-mysql-download]: http://go.microsoft.com/fwlink/?LinkId=252506
[NewWebSite1]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite1.jpg
[NewWebSite2]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite2.png
[NewWebSite3]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite3.png
[NewWebSite4]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite4.png
[NewWebSite5]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite5.png
[NewWebSite6]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite6.png
[ConnectionString]: ./media/web-sites-php-mysql-use-webmatrix/ConnectionString.png
[InstallWebMatrix]: ./media/web-sites-php-mysql-use-webmatrix/InstallWebMatrix.png
[download-site]: ./media/web-sites-php-mysql-use-webmatrix/download-site-1.png
[site-from-template]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template.png
[site-from-template-2]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template-2.png
[edit_addexisting]: ./media/web-sites-php-mysql-use-webmatrix/edit_addexisting.png
[edit_run]: ./media/web-sites-php-mysql-use-webmatrix/edit_run.png
[edit_publish]: ./media/web-sites-php-mysql-use-webmatrix/edit_publish.png
[OpenRemoteView]: ./media/web-sites-php-mysql-use-webmatrix/OpenRemoteView.png
[Remote_editIndex]: ./media/web-sites-php-mysql-use-webmatrix/Remote_editIndex.png
[Remote_run]: ./media/web-sites-php-mysql-use-webmatrix/Remote_run.png













[preview-portal]: https://manage.windowsazure.com













 

<!---HONumber=62-->