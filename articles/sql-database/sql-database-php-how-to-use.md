<properties 
	pageTitle="SQL データベースの使用方法 (PHP) - Azure の機能ガイド" 
	description="PHP から Azure SQL Database を作成してそれに接続する方法について説明します。" 
	services="sql-database" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="jeffreyg" 
	editor="mollybos"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="tomfitz"/>

#PHP から Azure SQL データベースにアクセスする方法 

## 概要

このガイドでは、PHP から SQL データベースを使用する方法の基本について説明します。サンプルは PHP で記述されています。紹介するシナリオは、**SQL データベースの作成**および **SQL データベースへの接続**です。このガイドでは、[管理ポータル][management-portal]からの SQL データベースの作成を取り上げます。運用ポータルからのこれらのタスクの実行については、[PHP と SQL データベースの始め方に関するページ][prod-portal-instructions]を参照してください。詳細については、「[次のステップ](#NextSteps)」を参照してください。

##SQL Database とは

SQL データベースは、Azure 用のリレーショナル データベース管理システムを提供し、SQL Server テクノロジを基盤としています。SQL データベースを使用すると、リレーショナル データベース ソリューションを簡単に準備してクラウドに展開することができ、分散したデータ センターを活用して、組み込みのデータ保護と自己復旧機能のメリットによるエンタープライズ クラスの可用性、拡張性、およびセキュリティを確保できます。

##<a id="Concepts"></a>概念
SQL データベースは SQL Server テクノロジに基づいているため、PHP から SQL データベースへのアクセスは PHP から SQL Server へのアクセスによく似ています。アプリケーションをローカルで開発した後 (SQL Server を使用)、接続文字列を変更するだけで SQL データベースに接続できます。ただし、SQL データベースと SQL Server のいくつかの違いがアプリケーションに影響する場合があります。詳細については、「[ガイドラインと制限事項 (SQL データベース)][limitations]」を参照してください。

PHP から SQL データベースにアクセスするための推奨されるアプローチは、[Microsoft Drivers for PHP for SQL Server][download-drivers] を使用することです(この記事の例ではこれらのドライバーを使用します)。 Microsoft Drivers for PHP for SQL Server は Windows 上でのみ動作します。

##<a id="Setup">方法: 環境を設定する</a>

開発環境を設定するための推奨される方法は、[Microsoft Web プラットフォーム インストーラー][wpi-installer]を使用することです。Web プラットフォーム インストーラーでは、Web 開発プラットフォームの要素を選択するだけで、それらの要素は自動的にインストールされて設定されます。Web プラットフォーム インストーラーをダウンロードし、WebMatrix、PHP for WebMatrix、および SQL Server Express のインストールを選択することで、完全な開発環境が自動的に設定されます。

または、手動で環境を設定することもできます。

* PHP のインストールと IIS の構成: [http://php.net/manual/en/install.windows.iis7.php][manual-config]
* SQL Server Express のダウンロードとインストール: [http://www.microsoft.com/download/details.aspx?id=29062][install-sql-express]
* [Microsoft Drivers for PHP for SQL Server][download-drivers] のダウンロードとインストール。

##<a id="CreateServer"></a>方法: SQL データベースを作成する

Azure SQL データベースを作成するには、次の手順に従います。

1. [管理ポータル][management-portal]にログインします。
2. ポータルの左下にある **[新規]** をクリックします。

	![新しい Azure の Web サイトの作成][new-website]

3. **[データ サービス]**、**[SQL データベース]**、**[簡易作成]** の順にクリックします。データベースの名前、既存のデータベース サーバーと新しいデータベース サーバーのどちらを使用するか、リージョン、管理者のログイン名とパスワードを指定します。

	![新しい SQL データベースのカスタム作成][quick-create]


サーバーとデータベースの情報を表示するには、管理ポータルで **[SQL データベース]** をクリックします。**[データベース]** または **[サーバー]** をクリックすると、該当する情報が表示されます。

![サーバーとデータベースの情報の表示][sql-dbs-servers]

##<a id="ConnectionInfo"></a>方法: SQL データベース接続情報を取得する

SQL データベースの接続情報を取得するには、ポータルで **[SQL データベース]** をクリックし、データベースの名前をクリックします。

![データベース情報の表示][go-to-db-info]

次に、**[ADO.Net、ODBC、PHP、および JDBC の SQL データベース接続文字列を表示する]** をクリックします。

![Show connection strings][show-connection-string]

表示されたウィンドウの [PHP] セクションで、**サーバー**、**データベース**、**ユーザー名**の値を書き留めます。パスワードは SQL データベースの作成時に使用したパスワードになります。

##<a id="Connect"></a>方法: SQL データベース インスタンスに接続する

次の例は、**SQLSRV** および **PDO_SQLSRV** 拡張を使用して、`testdb` という SQL データベースに接続する方法を示しています。**SQLSRV** と **PDO_SQLSRV** API の詳細については、[Microsoft Drivers for PHP for SQL Server のドキュメント][driver-docs]を参照してください。前のセクションで取得した情報が必要になります。`SERVER_ID` を 10 桁のサーバー ID (前のセクションで取得したサーバー値の先頭の 10 文字) に置き換え、正しい値 (ユーザー名およびパスワード) を `$user` および `$pwd` 変数に割り当てます。

#####SQLSRV

	$server = "tcp:<value of SERVER from section above>";
	$user = "<value of USERNAME from section above>"@SERVER_ID;
	$pwd = "password";
	$db = "testdb";

	$conn = sqlsrv_connect($server, array("UID"=>$user, "PWD"=>$pwd, "Database"=>$db));

	if($conn === false){
		die(print_r(sqlsrv_errors()));
	}

#####PDO_SQLSRV

	$server = "tcp:<value of SERVER from section above>";
	$user = "<value of USERNAME from section above>"@SERVER_ID;
	$pwd = "password";
	$db = "testdb";

	try{
		$conn = new PDO( "sqlsrv:Server= $server ; Database = $db ", $user, $pwd);
		$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
	}
	catch(Exception $e){
		die(print_r($e));
	}


##<a id="NextSteps"></a>次のステップ
ここで説明したように、SQL データベースの使用は SQL Server の使用によく似ています。ここで示したように SQL データベースへの接続を確立したら、**SQLSRV** または **PDO_SQLSRV** API を使用して、データの取得、更新、削除、挿入が可能になります。ただし、SQL データベースと SQL Server のいくつかの違いがアプリケーションに影響する場合があります。詳細については、「[ガイドラインと制限事項 (SQL データベース)][limitations]」を参照してください。

Azure での PHP と SQL データベースの使用方法を示すサンプルは、<https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-sqlazure> から入手できます。

[download-drivers]: http://www.microsoft.com/download/en/details.aspx?id=20098
[limitations]: http://msdn.microsoft.com/library/windowsazure/ff394102.aspx
[odbc-php]: http://www.php.net/odbc
[manual-config]: http://php.net/manual/en/install.windows.iis7.php
[install-drivers]: http://php.net/manual/en/sqlsrv.requirements.php
[driver-docs]: http://msdn.microsoft.com/library/dd638075(SQL.10).aspx
[access-php-odbc]: http://social.technet.microsoft.com/wiki/contents/articles/accessing-sql-azure-from-php.aspx
[install-sql-express]: http://www.microsoft.com/download/details.aspx?id=29062
[management-portal]: https://manage.windowsazure.com
[prod-portal-instructions]: http://blogs.msdn.com/b/brian_swan/archive/2010/02/12/getting-started-with-php-and-sql-azure.aspx
[new-website]: ./media/sql-database-php-how-to-use/plus-new.png
[custom-create]: ./media/sql-database-php-how-to-use-sql-database/create_custom_sql_db-2.png
[database-settings]: ./media/sql-database-php-how-to-use-sql-database/new-sql-db.png
[create-server]: ./media/sql-database-php-how-to-use-sql-database/db-server-settings.png
[sql-dbs-servers]: ./media/sql-database-php-how-to-use/sql-dbs-portal.png
[wpi-installer]: http://go.microsoft.com/fwlink/?LinkId=253447
[go-to-db-info]: ./media/sql-database-php-how-to-use/go-to-db-info.png
[show-connection-string]: ./media/sql-database-php-how-to-use/show-connection-string-2.png
[quick-create]: ./media/sql-database-php-how-to-use/create-new-sql.png
 

<!---HONumber=July15_HO4-->