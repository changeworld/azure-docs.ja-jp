<properties 
	pageTitle="MySQL と Git を使用した PHP Web サイト - Azure チュートリアル" 
	description="MySQL にデータを保存する PHP Web サイトを作成し、Azure への Git デプロイを使用する方法を示すチュートリアル。" 
	services="web-sites" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/14/2014" 
	ms.author="tomfitz"/>

#PHP-MySQL Azure Web サイトを作成して Git で展開する

このチュートリアルでは、PHP-MySQL Azure Web サイトを作成する方法と、Git を使用してそれを展開する方法を説明します。コンピューターにインストールされている [PHP][install-php]、MySQL コマンド ライン ツール ([MySQL][install-mysql] の一部)、Web サーバー、および [Git][install-git] を使用します。このチュートリアルの手順は、Windows、Mac、Linux など、任意のオペレーティング システムで使用できます。このチュートリアルを完了すると、Azure で動作する PHP/MySQL Web サイトが完成します。
 
学習内容:

* Azure の管理ポータルを使用して Azure Web サイトと MySQL データベースを作成する方法。Azure の Web サイトでは PHP が既定で有効になっているため、特に何もしなくても PHP コードを実行できます。
* Git を使用して Azure にアプリケーションを発行および再発行する方法。
 
このチュートリアルでは、登録用の単純な Web アプリケーションを PHP で作成します。このアプリケーションは Azure Website でホストします。完成したアプリケーションのスクリーンショットは次のようになります。

![Azure PHP web site][running-app]

> [AZURE.NOTE]
> このチュートリアルを完了するには、Azure Websites の機能を有効にした Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成できます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A74E0F923" target="_blank">Azure の無料評価版サイト</a>をご覧ください。
> 
> アカウントにサインアップする前に Azure Websites を実際に使ってみるには、<a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a> にアクセスしてください。Azure Websites で、有効期限付きの ASP.NET スターター サイトを無償で簡単に作成できます。クレジット カードは必要ありません。また、支払いも発生しません。

##開発環境を設定する

このチュートリアルは、コンピューターに [PHP][install-php]、MySQL コマンド ライン ツール ([MySQL][install-mysql] の一部)、Web サーバー、および [Git][install-git] がインストールされていることを前提としています。

> [AZURE.NOTE]
> このチュートリアルを Windows で実行する場合は、<a href="http://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/azurephpsdk.appids">Azure SDK for PHP</a> をインストールすることで、コンピューターを PHP 用に設定し、自動的に IIS (Windows のビルトイン Web サーバー) を構成できます。

##<a id="create-web-site-and-set-up-git"></a>Azure Web サイトの作成と Git 発行の設定

Azure Web サイトと MySQL データベースを作成するには、次の手順に従います。

1. [Azure の管理ポータル][management-portal]にログインします。
2. ポータルの左下にある **[新規]** アイコンをクリックします。

	![Create New Azure web site][new-website]

3. **[Web サイト]** をクリックし、**[カスタム作成]** をクリックします。

	![Custom Create a new web site][custom-create]
	
	**[URL]** ボックスに値を入力し、**[データベース]** ボックスの一覧の **[新しい MySQL データベースを作成します]** を選択して、**[リージョン]** ボックスの一覧で Web サイトのデータ センターを選択します。ダイアログの下部にある矢印をクリックします。

	![Fill in web site details][website-details]

4. データベースの **[名前]** ボックスに値を入力し、**[リージョン]** ボックスの一覧でデータベースのデータ センターを選択して、法律条項に同意することを示すチェック ボックスをオンにします。ダイアログの下部にあるチェックマークをクリックします。

	![Create new MySQL database][new-mysql-db]

	Web サイトが作成されると、"**Web サイト "[サイト名]" の作成が正常に完了しました**" というテキストが表示されます。これで、Git 発行を有効にする準備ができました。

6. Web サイトの一覧に表示されている Web サイトの名前をクリックして、Web サイトの**クイックスタート** ダッシュボードを開きます。

	![Open web site dashboard][go-to-dashboard]


7. **クイック スタート** ページの下部で、**[Git 発行の設定]** をクリックします。 

	![Set up Git publishing][setup-git-publishing]

8. Git 発行を有効にするには、ユーザー名とパスワードを指定する必要があります。作成するユーザー名とパスワードはメモしておいてください(Git リポジトリを設定したことがある場合は、この手順をスキップできます)。

	![Create publishing credentials][credentials]

	リポジトリの設定にかかる時間はわずかです。

9. リポジトリの準備ができると、アプリケーション ファイルをリポジトリにプッシュするための手順が表示されます。これらの手順は後で必要になるため、メモしておいてください。

	![Git instructions][git-instructions]

##MySQL のリモート接続情報の取得

Azure Websites で実行されている MySQL データベースに接続するには、接続情報が必要になります。MySQL の接続情報を取得するには、次の手順に従います。

1. Web サイトのダッシュボードで、ページの右側にある **[接続文字列の表示]** リンクをクリックします。

	![Get database connection information][connection-string-info]
	
2.  `Database`、 `Data Source`、 `User Id`、 `Password` の各値を記録します。

##アプリケーションの作成とローカル テスト

Azure Web サイトを作成したので、アプリケーションをローカルで作成し、それをテストした後に展開できます。 

Registration アプリケーションは、名前と電子メール アドレスを入力してイベントに登録するための、単純な PHP アプリケーションです。それまでの登録者情報がテーブルに表示されます。登録情報は MySQL データベースに保存されます。アプリケーションを構成するファイルは 1 つです (下にあるコードをコピーし、貼り付けて使用できます)。

* **index.php**:登録用のフォームと登録者情報が含まれたテーブルを表示します。

アプリケーションを作成してローカルで実行するには、次の手順に従います。ここに示す手順は、ローカル コンピューターに PHP、MySQL コマンド ライン ツール (MySQL の一部)、および Web サーバーがセットアップされており、[MySQL 用 PDO 拡張機能][pdo-mysql]が有効になっていることを前提としています。

1. 先ほどメモしておいた  `Data Source`、 `User Id`、 `Password`、 `Database` の各値を使用して、リモートの MySQL サーバーに接続します。

		mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. MySQL コマンド プロンプトが表示されます。

		mysql>

3. 次の  `CREATE TABLE` コマンドを貼り付けます。これにより、データベース内に  `registration_tbl` テーブルが作成されます。

		mysql> CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. Web サーバーのルート ディレクトリで、 `registration` というフォルダーを作成し、その中に  `index.php` というファイルを作成します。

5. テキスト エディターまたは IDE で **index.php** ファイルを開き、次のコードを追加します。`//TODO:` コメントの箇所を必要に応じて変更します。


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
			// DB connection info
			//TODO: Update the values for $host, $user, $pwd, and $db
			//using the values you retrieved earlier from the portal.
			$host = "value of Data Source";
			$user = "value of User Id";
			$pwd = "value of Password";
			$db = "value of Database";
			// Connect to database.
			try {
				$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
				$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			}
			catch(Exception $e){
				die(var_dump($e));
			}
			// Insert registration info
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
			// Retrieve data
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
		?>
		</body>
		</html>

これで、**http://localhost/registration/index.php** にアクセスしてアプリケーションをテストできます。


##アプリケーションの発行

アプリケーションをローカルでテストした後、Git を使用してそのアプリケーションを Azure Web サイトに発行できます。ローカルの Git リポジトリを初期化して、アプリケーションを発行します。

> [AZURE.NOTE]
> これらは、上の「Azure Web サイトの作成と Git 発行の設定」セクションの最後でポータルに示された手順と同じです。

1. (省略可能) Git リモート リポジトリの URL を忘れた場合やスペルを誤った場合は、ポータルの [展開] タブに移動します。
	
	![Get Git URL][git-instructions]

1. GitBash (Git が  `PATH` にある場合はターミナル) を開き、ディレクトリをアプリケーションのルート ディレクトリに変更して、次のコマンドを実行します。

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	先ほど作成したパスワードを入力するように求められます。

	![Initial Push to Azure via Git][git-initial-push]

2. アプリケーションの使用を開始できるように、**http://[site name].azurewebsites.net/index.php** に移動します (この情報はアカウント ダッシュボードに保存されます)。

	![Azure PHP web site][running-app]

アプリケーションを発行した後、アプリケーションへの変更を開始し、Git を使用してその変更を発行することもできます。 

##アプリケーションへの変更の発行

アプリケーションへの変更を発行するには、次のステップに従います。

1. ローカルでアプリケーションへの変更を行います。
2. GitBash (Git が  `PATH` にある場合はターミナル) を開き、ディレクトリをアプリケーションのルート ディレクトリに変更して、次のコマンドを実行します。

		git add .
		git commit -m "comment describing changes"
		git push azure master

	先ほど作成したパスワードを入力するように求められます。

	![Pushing site changes to Azure via Git][git-change-push]

3. アプリケーションとその変更内容を確認できるように、**http://[site name].azurewebsites.net/index.php** に移動します。

	![Azure PHP web site][running-app]

4. Azure の管理ポータルの  'Deployments' タブで、新しい展開を確認することもできます。

	![List of web site deployments][deployments-list]

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/ja-jp/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/ja-jp/download/details.aspx?id=20098
[install-git]: http://git-scm.com/

[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website.jpg
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/custom_create.png
[website-details]: ./media/web-sites-php-mysql-deploy-use-git/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/new_mysql_db.jpg
[go-to-dashboard]: ./media/web-sites-php-mysql-deploy-use-git/go_to_dashboard.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/git-deployment-credentials.png


[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png
[deployments-list]: ./media/web-sites-php-mysql-deploy-use-git/php-deployments-list.png
[connection-string-info]: ./media/web-sites-php-mysql-deploy-use-git/connection_string_info.png
[management-portal]: https://manage.windowsazure.com
[sql-database-editions]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee621788.aspx


<!--HONumber=42-->
