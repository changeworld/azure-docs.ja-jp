<properties linkid="develop-php-website-with-sql-database-and-git" urlDisplayName="SQL と Git を使用した Web サイト" pageTitle="SQL データベースと Git を使用した PHP Web サイト - Azure チュートリアル" metaKeywords="" description="SQL データベースにデータを保存する PHP Web サイトを作成し、Azure への Git 展開を使用する方法を示すチュートリアル。" metaCanonical="" services="web-sites,sql-database" documentationCenter="PHP" title="SQL データベースを使用する PHP Web サイトを作成して Git で展開する" authors="waltpo" solutions="" manager="" editor="mollybos" />



#SQL データベースを使用する PHP Web サイトを作成して Git で展開する

このチュートリアルでは、Azure SQL データベースを使用する Azure の PHP Web サイトを作成する方法と、Git を使用してそれを展開する方法を説明します。このチュートリアルは、コンピューターに [PHP][install-php]、[SQL Server Express][install-SQLExpress]、[Microsoft Drivers for SQL Server for PHP][install-drivers]、Web サーバー、および [Git][install-git] がインストールされていることを前提としています。このチュートリアルを完了すると、Azure で動作する PHP-SQL データベース Web サイトが完成します。

> [WACOM.NOTE]
> <a href="http://www.microsoft.com/web/downloads/platform.aspx">Microsoft Web プラットフォーム インストーラー</a>を使用すると、PHP、SQL Server Express、Microsoft Drivers for SQL Server for PHP、およびインターネット インフォメーション サービス (IIS) をインストールおよび構成できます。

学習内容:

* Azure の管理ポータルを使用して Azure の Web サイトおよび SQL データベースを作成する方法。Azure の Web サイトでは PHP が既定で有効になっているため、特に何もしなくても PHP コードを実行できます。
* Git を使用して Azure にアプリケーションを発行および再発行する方法。
 
このチュートリアルでは、登録用の単純な Web アプリケーションを PHP で作成します。このアプリケーションは Azure の Web サイトでホストします。完成したアプリケーションのスクリーンショットは次のようになります。

![Azure の PHP Web サイト][running-app]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]


##Azure の Web サイトの作成と Git 発行の設定

Azure の Web サイトと SQL データベースを作成するには、次の手順に従います。

1. [Azure の管理ポータル][management-portal]にログインします。
2. ポータルの左下にある **[新規]** アイコンをクリックします。
![新しい Azure の Web サイトの作成][new-website]

3. **[Web サイト]** をクリックし、**[カスタム作成]** をクリックします。

	![新しい Web サイトのカスタム作成][custom-create]

	**[URL]** ボックスに値を入力し、**[データベース]** ボックスの一覧の **[新しい SQL データベースを作成する]** を選択して、**[リージョン]** ボックスの一覧で Web サイトのデータ センターを選択します。ダイアログの下部にある矢印をクリックします。

	![Web サイトの詳細の入力][website-details-sqlazure]

4. データベースの**名前**を入力し、**エディション** [([WEB] または [BUSINESS])][sql-database-editions] を選択します。データベースの**最大サイズ**を選択し、**照合順序**を選択します。**[新しい SQL データベース サーバー]** を選択します。ダイアログの下部にある矢印をクリックします。

	![SQL データベースの設定の指定][database-settings]

5. 管理者の名前とパスワードを入力 (およびパスワードを確認) し、新しい SQL データベース サーバーを作成するリージョンを選択して、[Azure サービスにサーバーへのアクセスを許可します] チェック ボックスをオンにします。

	![新しい SQL データベース サーバーの作成][create-server]

	Web サイトが作成されると、"**Web サイト "[サイト名]" の作成が正常に完了しました**" というテキストが表示されます。これで、Git 発行を有効にする準備ができました。

6. Web サイトの一覧に表示されている Web サイトの名前をクリックして、Web サイトのクイック スタート ダッシュボードを開きます。

	![Web サイトのダッシュボードを開く][go-to-dashboard]


7. クイック スタート ページの下部で、**[ソース管理からのデプロイの設定]** を選択します。

	![Git 発行の設定][setup-git-publishing]

6. ソース コードの位置をたずねるメッセージが表示されたら、**[ローカル Git リポジトリ]** を選択し、矢印をクリックします。

	![ソース コードの位置][where-is-code]

8. Git 発行を有効にするには、ユーザー名とパスワードを指定する必要があります。作成するユーザー名とパスワードはメモしておいてください (Git リポジトリを設定したことがある場合は、この手順をスキップできます)。

	![発行資格情報の作成][credentials]

	リポジトリの設定にかかる時間はわずかです。

9. リポジトリの準備ができると、アプリケーション ファイルをリポジトリにプッシュするための手順が表示されます。これらの手順は後で必要になるため、メモしておいてください。

	![Git の手順][git-instructions]

##SQL データベース接続情報を取得する

Azure の Web サイトで実行されている SQL データベース インスタンスに接続するには、接続情報が必要になります。SQL データベースの接続情報を取得するには、次の手順に従います。

1. Azure 管理ポータルで、**[リンク済みリソース]** をクリックし、目的のデータベース名をクリックします。

	![リンク済みリソース][linked-resources]

2. **[接続文字列の表示]** をクリックします。

	![接続文字列][connection-string]
	
3. 表示されたダイアログの **[PHP]** セクションから、`サーバー`、`データベース`、および`ユーザー名` の値をメモします。

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

	<code>$user</code> と <code>$pwd</code> の値は、ローカルの SQL Server ユーザー名とパスワードに置き換える必要があります。

4. Web ブラウザーを開いて、**http://localhost/registration/createtable.php** にアクセスします。これにより、データベースに `registration_tbl` テーブルが作成されます。

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

    ここでも、<code>$user</code> と <code>$pwd</code> の値は、ローカルの MySQL ユーザー名とパスワードに置き換える必要があります。

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

アプリケーションをローカルでテストした後、Git を使用してそのアプリケーションを Azure の Web サイトに発行できます。ただし、まずアプリケーション内のデータベース接続情報を更新する必要があります。先ほど (「**SQL データベース接続情報を取得する**」セクションで) 取得したデータベース接続情報を使用し、`createdatabase.php` ファイルと `index.php` ファイルの**両方**で、次の情報を適切な値に置き換えます。

	// DB connection info
	$host = "tcp:<value of SERVER>";
	$user = "<value of USERNAME>@<server ID>";
	$pwd = "<your password>";
	$db = "<value of DATABASE>";

> [WACOM.NOTE]
> <code>$host</code> で、SERVER の値は先頭に <code>tcp:</code> を付ける必要があります。<code>$user</code> の値は、USERNAME の値、'@'、サーバー ID の連結です。サーバー ID は、SERVER の値の先頭 10 文字です。


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

アプリケーションへの変更を発行するには、次の手順に従います。

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
[website-details-sqlazure]: ./media/web-sites-php-sql-database-deploy-use-git/website_details_sqlazure.jpg
[database-settings]: ./media/web-sites-php-sql-database-deploy-use-git/database_settings.jpg
[create-server]: ./media/web-sites-php-sql-database-deploy-use-git/create_server.jpg
[go-to-dashboard]: ./media/web-sites-php-sql-database-deploy-use-git/go_to_dashboard.png
[setup-git-publishing]: ./media/web-sites-php-sql-database-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-sql-database-deploy-use-git/git-deployment-credentials.png


[git-instructions]: ./media/web-sites-php-sql-database-deploy-use-git/git-instructions.png
[linked-resources]: ./media/web-sites-php-sql-database-deploy-use-git/linked_resources.jpg
[connection-string]: ./media/web-sites-php-sql-database-deploy-use-git/connection_string.jpg
[management-portal]: https://manage.windowsazure.com/
[sql-database-editions]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee621788.aspx
[where-is-code]: ./media/web-sites-php-sql-database-deploy-use-git/where_is_code.png

