<properties urlDisplayName="Web w/ SQL + Git" pageTitle="SQL Database と Git を使用した PHP Web サイト - Azure チュートリアル" metaKeywords="" description="SQL Database にデータを保存する PHP Web サイトを作成し、Azure への Git デプロイを使用する方法を示すチュートリアル。" metaCanonical="" services="web-sites,sql-database" documentationCenter="PHP" title="Create a PHP website with a SQL Database and deploy using Git" authors="tomfitz" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/18/2014" ms.author="tomfitz" />

#SQL Database を使用する PHP Web サイトを作成して Git でデプロイする

このチュートリアルでは、Azure SQL Database を使用する PHP Azure Website を作成する方法と、Git を使用してそれをデプロイする方法を説明します。このチュートリアルは、コンピューターに [PHP][install-php]、[SQL Server Express][install-SQLExpress]、[Microsoft Drivers for SQL Server for PHP][install-drivers]、Web サーバー、および [Git][install-git] がインストールされていることを前提としています。このチュートリアルを完了すると、Azure で動作する PHP-SQL データベース Web サイトが完成します。

> [WACOM.NOTE]
> <a href="http://www.microsoft.com/web/downloads/platform.aspx">Microsoft Web プラットフォーム インストーラー</a>を使用すると、PHP、SQL Server Express、Microsoft Drivers for SQL Server for PHP、およびインターネット インフォメーション サービス (IIS) をインストールおよび構成できます。

学習内容:

* Azure 管理ポータルを使用して Azure Website と SQL Database を作成する方法。Azure の Web サイトでは PHP が既定で有効になっているため、特に何もしなくても PHP コードを実行できます。
* Git を使用して Azure にアプリケーションを発行および再発行する方法。
 
このチュートリアルでは、登録用の単純な Web アプリケーションを PHP で作成します。このアプリケーションは Azure の Web サイトでホストします。完成したアプリケーションのスクリーンショットは次のようになります。

![Azure PHP Web Site][running-app]

> [WACOM.NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。<a href="http://azure.microsoft.com/ja-jp/pricing/member-offers/msdn-benefits-details/">MSDN サブスクライバーの特典を有効</a>にしたり、<a href="http://azure.microsoft.com/ja-jp/pricing/free-trial/">無料評価版にサインアップ</a>することもできます。
> 
> アカウントにサインアップする前に Azure Websites を実際に使ってみるには、<a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a> にアクセスしてください。Azure Websites で、有効期限付きの ASP.NET スターター サイトを無償で簡単に作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

##Azure Website の作成と Git 発行の設定

Azure Websites と SQL データベースを作成するには、次の手順に従います。

1. [Azure の管理ポータル][management-portal]にログインします。
2. ポータルの左下にある **[新規]** アイコンをクリックします。
![Create New Azure Web Site][new-website]

3. **[Web サイト]** をクリックし、**[カスタム作成]** をクリックします。

	![Custom Create a new Web Site][custom-create]

	**[URL]** ボックスに値を入力し、**[データベース]** ボックスの一覧の **[新しい SQL データベースを作成します]** を選択して、**[ソース管理から発行]** を選択します。ダイアログの下部にある矢印をクリックします。

	![Fill in web site details][website-details-sqlazure]

4. データベースの **[名前]** を入力し、**[新しい SQL データベース サーバー]** を選択してログイン資格情報を入力し、リージョンを選択します。ダイアログの下部にある矢印をクリックします。

	![Fill in SQL Database settings][database-settings]

5. ソース コードの **[ローカル Git リポジトリ]** を選択します。

	![where is your source code][where-is-code]

	Git リポジトリが未選択である場合は、ユーザー名とパスワードを指定する必要があります。

6. Web サイトの作成後、サイトのダッシュボードを開いて、**[デプロイの表示]** を選択します。

	![Web site dashboard][go-to-dashboard]

9. アプリケーション ファイルをリポジトリにプッシュするための手順が表示されます。これらの手順は後で必要になるため、メモしておいてください。

	![Git instructions][git-instructions]

##SQL データベース接続情報を取得する

Azure Websites で実行されている SQL データベース インスタンスに接続するには、接続情報が必要になります。SQL データベースの接続情報を取得するには、次の手順に従います。

1. Azure の管理ポータルで、**[リンク済みリソース]** をクリックし、目的のデータベース名をクリックします。

	![Linked Resources][linked-resources]

2. **[接続文字列の表示]** をクリックします。

	![Connection string][connection-string]
	
3. 表示されたダイアログの **[PHP]** セクションから、`サーバー`、`データベース`、および`ユーザー名`の値をメモします。

##アプリケーションの作成とローカル テスト

Registration アプリケーションは、名前と電子メール アドレスを入力してイベントに登録するための、単純な PHP アプリケーションです。それまでの登録者情報がテーブルに表示されます。登録情報は SQL データベース インスタンスに保存されます。アプリケーションは、次の 2 つのファイルで構成されます (下にあるコードをコピーし、貼り付けて使用できます)。

* **index.php**: 登録用のフォームと登録者情報が含まれたテーブルを表示します。
* **createtable.php**: アプリケーション用の SQL データベース テーブルを作成します。このファイルは 1 度しか使用されません。

アプリケーションをローカルで実行するには、次の手順に従います。これらの手順は、ローカル コンピューターに PHP、SQL Server Express、および Web サーバーがセットアップされており、[SQL Server 用 PDO 拡張機能][pdo-sqlsrv]が有効になっていることを前提としています。

1. `registration` という SQL Server データベースを作成します。これには、`sqlcmd` コマンド プロンプトで次のコマンドを実行します。

		>sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
		1> create database registration
		2> GO	


2. Web サーバーのルート ディレクトリで、`registration` というフォルダーを作成し、その中に 2 つのファイル (`createtable.php` と `index.php`) を作成します。

3. `createtable.php` ファイルをテキスト エディターまたは IDE で開き、次のコードを追加します。このコードは、`registration` データベースに `registration_tbl` テーブルを作成するために使用します。

		<?php
		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		try{
			$conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			$sql = "CREATE TABLE registration_tbl(
			id INT NOT NULL IDENTITY(1,1) 
			PRIMARY KEY(id),
			name VARCHAR(30),
			email VARCHAR(30),
			date DATE)";
			$conn->query($sql);
		}
		catch(Exception $e){
			die(print_r($e));
		}
		echo "<h3>Table created.</h3>";
		?>

	ここで、 <code>$user </code> と <code> $pwd </code> の値は、ローカルの SQL Server ユーザー名とパスワードに置き換える必要があります。

4. Web ブラウザーを開いて、**http://localhost/registration/createtable.php** にアクセスします。このコードは、データベースに `registration_tbl` テーブルを作成するために使用します。

5. **index.php** ファイルをテキスト エディターまたは IDE で開いて、ページの基本的な HTML コードおよび CSS コードを追加します (PHP コードは後で追加します)。

		<html>
		<head>
		<Title>Registration Form</Title>
		<style type="text/css">
			body { background-color: #fff; border-top: solid 10px #000;
			    color: #333; font-size: .85em; margin: 20; padding: 20;
			    font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
			}
			h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
			h1 { font-size: 2em; }
			h2 { font-size: 1.75em; }
			h3 { font-size: 1.2em; }
			table { margin-top: 0.75em; }
			th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
			td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
		</style>
		</head>
		<body>
		<h1>Register here!</h1>
		<p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
		<form method="post" action="index.php" enctype="multipart/form-data" >
		      Name  <input type="text" name="name" id="name"/></br>
		      Email <input type="text" name="email" id="email"/></br>
		      <input type="submit" name="submit" value="Submit" />
		</form>
		<?php

		?>
		</body>
		</html>

6. PHP タグ内に、データベースに接続するための PHP コードを追加します。

		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		// Connect to database.
		try {
			$conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
		}
		catch(Exception $e){
			die(var_dump($e));
		}

    ここでも、 <code>$user </code> と <code> $pwd </code> の値は、ローカルの MySQL ユーザー名とパスワードに置き換える必要があります。

7. データベース接続コードの次に、登録情報をデータベースに挿入するためのコードを追加します。

		if(!empty($_POST)) {
		try {
			$name = $_POST['name'];
			$email = $_POST['email'];
			$date = date("Y-m-d");
			// Insert data
			$sql_insert = "INSERT INTO registration_tbl (name, email, date) 
						   VALUES (?,?,?)";
			$stmt = $conn->prepare($sql_insert);
			$stmt->bindValue(1, $name);
			$stmt->bindValue(2, $email);
			$stmt->bindValue(3, $date);
			$stmt->execute();
		}
		catch(Exception $e) {
			die(var_dump($e));
		}
		echo "<h3>Your're registered!</h3>";
		}

8. 上のコードの次に、データベースからデータを取得するためのコードを追加します。

		$sql_select = "SELECT * FROM registration_tbl";
		$stmt = $conn->query($sql_select);
		$registrants = $stmt->fetchAll(); 
		if(count($registrants) > 0) {
			echo "<h2>People who are registered:</h2>";
			echo "<table>";
			echo "<tr><th>Name</th>";
			echo "<th>Email</th>";
			echo "<th>Date</th></tr>";
			foreach($registrants as $registrant) {
				echo "<tr><td>".$registrant['name']."</td>";
				echo "<td>".$registrant['email']."</td>";
				echo "<td>".$registrant['date']."</td></tr>";
		    }
		 	echo "</table>";
		} else {
			echo "<h3>No one is currently registered.</h3>";
		}

これで、**http://localhost/registration/index.php** にアクセスしてアプリケーションをテストできます。

##アプリケーションの発行

アプリケーションをローカルでテストした後、Git を使用してそのアプリケーションを Azure Website に発行できます。ただし、まずアプリケーション内のデータベース接続情報を更新する必要があります。先ほど (「**SQL データベースの接続情報の取得**」セクションで) 取得したデータベース接続情報を使用し、`createdatabase.php` ファイルと `index.php` ファイルの**両方**で、次の情報を適切な値に置き換えます。

	// DB connection info
	$host = "tcp:<value of SERVER>";
	$user = "<value of USERNAME>@<server ID>";
	$pwd = "<your password>";
	$db = "<value of DATABASE>";

> [WACOM.NOTE]
>  <code>$host </code>の SERVER の値は、 <code>tcp: </code>で始める必要があります。また、 <code>$user </code> には、USERNAME の値と '@'、さらに、該当するサーバー ID を連結した値を指定します。サーバー ID は、SERVER の値の先頭 10 文字です。


これで、Git 発行を設定してアプリケーションを発行する準備ができました。

> [WACOM.NOTE]
> これらは、上の「Azure の Web サイトの作成と Git 発行の設定」セクションの最後でメモした手順と同じです。


1. GitBash (Git が `PATH` にある場合はターミナル) を開き、ディレクトリをアプリケーションのルート ディレクトリに変更して、次のコマンドを実行します。

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	先ほど作成したパスワードを入力するように求められます。

2. アプリケーション用の MySQL テーブルを作成するには、**http://[site name].azurewebsites.net/createtable.php** に移動します。
3. アプリケーションの使用を開始するには、**http://[site name].azurewebsites.net/index.php** に移動します。

アプリケーションを発行した後、アプリケーションへの変更を開始し、Git を使用してその変更を発行することもできます。 

##アプリケーションへの変更の発行

アプリケーションへの変更を発行するには、次のステップに従います。

1. ローカルでアプリケーションへの変更を行います。
2. GitBash (Git が `PATH` にある場合はターミナル) を開き、ディレクトリをアプリケーションのルート ディレクトリに変更して、次のコマンドを実行します。

		git add .
		git commit -m "comment describing changes"
		git push azure master

	先ほど作成したパスワードを入力するように求められます。

3. 変更内容を確認できるように、**http://[site name].azurewebsites.net/index.php** に移動します。

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/ja-jp/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/ja-jp/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[pdo-sqlsrv]: http://php.net/pdo_sqlsrv
[running-app]: ./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png
[new-website]: ./media/web-sites-php-sql-database-deploy-use-git/new_website.jpg
[custom-create]: ./media/web-sites-php-sql-database-deploy-use-git/custom_create.png
[website-details-sqlazure]: ./media/web-sites-php-sql-database-deploy-use-git/createphpgitsite.png
[database-settings]: ./media/web-sites-php-sql-database-deploy-use-git/setupdb.png
[create-server]: ./media/web-sites-php-sql-database-deploy-use-git/create_server.jpg
[go-to-dashboard]: ./media/web-sites-php-sql-database-deploy-use-git/viewdeploy.png
[setup-git-publishing]: ./media/web-sites-php-sql-database-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-sql-database-deploy-use-git/git-deployment-credentials.png


[git-instructions]: ./media/web-sites-php-sql-database-deploy-use-git/gitsettings.png
[linked-resources]: ./media/web-sites-php-sql-database-deploy-use-git/linked_resources.jpg
[connection-string]: ./media/web-sites-php-sql-database-deploy-use-git/connection_string.jpg
[management-portal]: https://manage.windowsazure.com/
[sql-database-editions]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee621788.aspx
[where-is-code]: ./media/web-sites-php-sql-database-deploy-use-git/setupgit.png

<!--HONumber=35.1-->
