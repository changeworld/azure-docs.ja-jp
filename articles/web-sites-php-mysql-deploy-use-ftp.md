<properties urlDisplayName="Web w/ MySQL + FTP" pageTitle="MySQL と FTP を使用した PHP Web サイト - Azure チュートリアル" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in MySQL and use FTP deployment to Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure Website and Deploy Using FTP" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/14/2014" ms.author="tomfitz" />


#PHP-MySQL Azure Website を作成して FTP でデプロイする

このチュートリアルでは、PHP-MySQL Azure Website を作成する方法と、FTP を使用してそれをデプロイする方法について説明します。このチュートリアルは、コンピューターに [PHP][install-php]、[MySQL][install-mysql]、Web サーバー、および FTP クライアントがインストールされていることを前提としています。このチュートリアルの手順は、Windows、Mac、Linux など、任意のオペレーティング システムで使用できます。このチュートリアルを完了すると、Azure で動作する PHP/MySQL Web サイトが完成します。
 
学習内容:

* Azure 管理ポータルを使用して Azure Website と MySQL データベースを作成する方法。Azure の Web サイトでは PHP が既定で有効になっているため、特に何もしなくても PHP コードを実行できます。
* FTP を使用して Azure にアプリケーションを発行する方法。
 
このチュートリアルでは、登録用の単純な Web アプリケーションを PHP で作成します。このアプリケーションは Azure の Web サイトでホストします。完成したアプリケーションのスクリーンショットは次のようになります。

![Azure PHP Web Site][running-app]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##Azure Website の作成と FTP 発行の設定

Azure Website と MySQL データベースを作成するには、次の手順に従います。

1. [Azure 管理ポータル][management-portal]にログインします。
2. ポータルの左下にある **[+ 新規]** アイコンをクリックします。

	![Create New Azure Web Site][new-website]

3. **[Web サイト]** をクリックし、**[カスタム作成]** をクリックします。

	![Custom Create a new Web Site][custom-create]
	
	**[URL]** ボックスに値を入力し、**[データベース]** ボックスの一覧の **[新しい MySQL データベースを作成します]** を選択して、**[リージョン]** ボックスの一覧で Web サイトのデータ センターを選択します。ダイアログの下部にある矢印をクリックします。

	![Fill in Web Site details][website-details]

4. データベースの **[名前]** ボックスに値を入力し、**[リージョン]** ボックスの一覧でデータベースのデータ センターを選択して、法律条項に同意することを示すチェック ボックスをオンにします。ダイアログの下部にあるチェックマークをクリックします。

	![Create new MySQL database][new-mysql-db]

	Web サイトが作成されると、"**Web サイト '[サイト名]' の作成が正常に完了しました**" というテキストが表示されます。これで、FTP 発行を有効にする準備ができました。

5. Web サイトの一覧に表示されている Web サイトの名前をクリックして、Web サイトの**クイックスタート** ダッシュボードを開きます。

	![Open web site dashboard][go-to-dashboard]


6. **クイック スタート** ページの下部で、**[展開資格情報のリセット]** をクリックします。 

	![Reset deployment credentials][reset-deployment-credentials]

7. FTP 発行を有効にするには、ユーザー名とパスワードを指定する必要があります。作成するユーザー名とパスワードはメモしておいてください

	![Create publishing credentials][portal-git-username-password]

##アプリケーションの作成とローカル テスト

Registration アプリケーションは、名前と電子メール アドレスを入力してイベントに登録するための、単純な PHP アプリケーションです。それまでの登録者情報がテーブルに表示されます。登録情報は MySQL データベースに保存されます。アプリケーションは、次の 2 つのファイルで構成されます。

* **index.php**: 登録用のフォームと登録者情報が含まれたテーブルを表示します。
* **createtable.php**: アプリケーション用の MySQL テーブルを作成します。このファイルは 1 度しか使用されません。

アプリケーションを作成してローカルで実行するには、次の手順に従います。ここに示す手順は、ローカル コンピューターに PHP、MySQL、および Web サーバーがセットアップされており、[MySQL 用 PDO 拡張機能][pdo-mysql]が有効になっていることを前提としています。

1. `registration` という MySQL データベースを作成します。これには、MySQL コマンド プロンプトで次のコマンドを実行します。

		mysql> create database registration;

2. Web サーバーのルート ディレクトリで、`registration` というフォルダーを作成し、その中に 2 つのファイル (`createtable.php` と `index.php`) を作成します。

3. `createtable.php` ファイルをテキスト エディターまたは IDE で開き、次のコードを追加します。このコードは、`registration` データベースに `registration_tbl` テーブルを作成するために使用します。

		<?php
		// DB connection info
		$host = "localhost";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		try{
			$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			$sql = "CREATE TABLE registration_tbl(
						id INT NOT NULL AUTO_INCREMENT, 
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

	> [WACOM.NOTE] 
	> <code>$user</code> と <code>$pwd</code> の値は、ローカルの MySQL ユーザー名とパスワードに置き換える必要があります。

4. Web ブラウザーを開いて、[http://localhost/registration/createtable.php][localhost-createtable] にアクセスします。このコードは、データベースに `registration_tbl` テーブルを作成するために使用します。

5. **index.php**

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

6. Within the PHP tags, add PHP code for connecting to the database.

		// DB connection info
		$host = "localhost";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		// Connect to database.
		try {
			$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
		}
		catch(Exception $e){
			die(var_dump($e));
		}

	> [WACOM.NOTE]
	> Again, you will need to update the values for <code>$user</code> and <code>$pwd</code> with your local MySQL user name and password.

7. Following the database connection code, add code for inserting registration information into the database.

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

8. Finally, following the code above, add code for retrieving data from the database.

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

You can now browse to [http://localhost/registration/index.php][localhost-index] to test the application.

##Get MySQL and FTP connection information

To connect to the MySQL database that is running in Azure Websites, your will need the connection information. To get MySQL connection information, follow these steps:

1. From your website's dashboard, click the **View connection strings** link on the right side of the page:

	![Get database connection information][connection-string-info]
	
2. Make note of the values for `Database`, `Data Source`, `User Id`, and `Password`.

3. From your website's dashboard, click the **Download publish profile** link at the bottom right corner of the page:

	![Download publish profile][download-publish-profile]

4. Open the `.publishsettings` file in an XML editor. 

3. Find the `<publishProfile >` element with `publishMethod="FTP"` that looks similar to this:

		<publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
			...
		</publishProfile>
	
Make note of the `publishUrl`, `userName`, and `userPWD` attributes.

##Publish Your Application

After you have tested your application locally, you can publish it to your Azure Website using FTP. However, you first need to update the database connection information in the application. Using the database connection information you obtained earlier (in the **Get MySQL and FTP connection information** section), update the following information in **both** the `createdatabase.php` and `index.php` files with the appropriate values:

	// DB connection info
	$host = "value of Data Source";
	$user = "value of User Id";
	$pwd = "value of Password";
	$db = "value of Database";

Now you are ready to publish your application using FTP.

1. Open your FTP client of choice.

2. Enter the *host name portion* from the `publishUrl` attribute you noted above into your FTP client.

3. Enter the `userName` and `userPWD` attributes you noted above unchanged into your FTP client.

4. Establish a connection.

After you have connected you will be able to upload and download files as needed. Be sure that you are uploading files to the root directory, which is `/site/wwwroot`.

After uploading both `index.php` and `createtable.php`, browse to **http://[site name].azurewebsites.net/createtable.php** to create the MySQL table for the application, then browse to **http://[site name].azurewebsites.net/index.php** to begin using the application.
 

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_website.jpg
[custom-create]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/custom_create.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_mysql_db.jpg
[go-to-dashboard]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/go_to_dashboard.png
[reset-deployment-credentials]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/reset-deployment-credentials.png
[portal-git-username-password]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/git-deployment-credentials.png


[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://manage.windowsazure.com
[download-publish-profile]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/download_publish_profile_2.png

<!--HONumber=35_1-->
