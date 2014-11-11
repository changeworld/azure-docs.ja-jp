<properties linkid="develop-php-sql-database" urlDisplayName="SQL Database" pageTitle="How to use SQL Database (PHP) - Azure feature guides" metaKeywords="Azure SQL Database PHP, SQL Database PHP" description="Learn how to create and connect to an Azure SQL Database from PHP." metaCanonical="" services="sql-database" documentationCenter="PHP" title="How to Access Azure SQL Database from PHP" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# PHP から Azure SQL データベースにアクセスする方法

このガイドでは、PHP から Azure SQL データベースを使用する方法の基本について説明します。サンプルは PHP で記述されています。紹介するシナリオは、**SQL データベースの作成**および **SQL データベースへの接続**です。このガイドでは、[管理ポータル][管理ポータル]からの SQL データベースの作成を取り上げます。運用ポータルからのこれらのタスクの実行については、[PHP と SQL データベースの始め方に関するページ][PHP と SQL データベースの始め方に関するページ]を参照してください。詳細については、「[次のステップ][次のステップ]」を参照してください。

## Azure SQL データベースとは

Azure SQL データベースは、Azure 用のリレーショナル データベース管理システムを提供し、SQL Server テクノロジを基盤としています。SQL データベースを使用すると、リレーショナル データベース ソリューションを簡単に準備してクラウドに展開することができ、分散したデータ センターを活用して、組み込みのデータ保護と自己復旧機能のメリットによるエンタープライズ クラスの可用性、拡張性、およびセキュリティを確保できます。

## 目次

-   [概念][概念]
-   [方法: 環境を設定する][方法: 環境を設定する]
-   [方法: SQL データベースを作成する][方法: SQL データベースを作成する]
-   [方法: SQL データベース接続情報を取得する][方法: SQL データベース接続情報を取得する]
-   [方法: SQL データベース インスタンスに接続する][方法: SQL データベース インスタンスに接続する]
-   [次のステップ][次のステップ]

## <span id="Concepts"></span></a>概念

Azure SQL データベースは SQL Server テクノロジに基づいているため、PHP から SQL データベースへのアクセスは PHP から SQL Server へのアクセスによく似ています。アプリケーションをローカルで開発した後 (SQL Server を使用)、接続文字列を変更するだけで SQL データベースに接続できます。ただし、SQL データベースと SQL Server のいくつかの違いがアプリケーションに影響する場合があります。詳細については、「[ガイドラインと制限事項 (SQL データベース)][ガイドラインと制限事項 (SQL データベース)]」を参照してください。

PHP から SQL データベースにアクセスするための推奨されるアプローチは、[Microsoft Drivers for PHP for SQL Server][Microsoft Drivers for PHP for SQL Server] を使用することです (この記事の例ではこれらのドライバーを使用します)。Microsoft Drivers for PHP for SQL Server は Windows 上でのみ動作します。

## <span id="Setup"></span></a>方法: 環境を設定する

開発環境を設定するための推奨される方法は、[Microsoft Web プラットフォーム インストーラー][Microsoft Web プラットフォーム インストーラー]を使用することです。Web プラットフォーム インストーラーでは、Web 開発プラットフォームの要素を選択するだけで、それらの要素は自動的にインストールされて設定されます。Web プラットフォーム インストーラーをダウンロードし、WebMatrix、PHP for WebMatrix、および SQL Server Express のインストールを選択することで、完全な開発環境が自動的に設定されます。

または、手動で環境を設定することもできます。

-   PHP のインストールと IIS の構成: [][]<http://php.net/manual/en/install.windows.iis7.php></a>
-   SQL Server Express のダウンロードとインストール: [][1]<http://www.microsoft.com/ja-jp/download/details.aspx?id=29062></a>
-   Microsoft Drivers for PHP for SQL Server のダウンロードとインストール: [][2]<http://php.net/manual/en/sqlsrv.requirements.php></a>

## <span id="CreateServer"></span></a>方法: SQL データベースの作成

Azure SQL データベースを作成するには、次の手順に従います。

1.  [管理ポータル][管理ポータル]にログインします。
2.  ポータルの左下にある **[新規]** をクリックします。

    ![新しい Azure の Web サイトの作成][新しい Azure の Web サイトの作成]

3.  **[データ サービス]**、**[SQL データベース]**、**[カスタム作成]** の順にクリックします。

    ![新しい SQL データベースのカスタム作成][新しい SQL データベースのカスタム作成]

4.  **[名前]** ボックスにデータベースの名前を入力し、**[エディション]** (WEB または BUSINESS) を選択して、データベースの **[最大サイズ]** を選択します。**[照合順序]** を選択し、**[新しい SQL データベース サーバー]** を選択します。ダイアログの下部にある矢印をクリックします。(以前に SQL データベースを作成した場合は、**[サーバー]** ボックスの一覧から既存のサーバーを選択できることに注意してください)

    ![SQL データベースの設定の指定][SQL データベースの設定の指定]

5.  管理者の名前とパスワードを入力 (およびパスワードを確認) し、新しい SQL データベースを作成するリージョンを選択して、[`Allow Azure Services to access the server`] チェック ボックスをオンにします。

    ![新しい SQL データベース サーバーの作成][新しい SQL データベース サーバーの作成]

サーバーとデータベースの情報を表示するには、管理ポータルで **[SQL データベース]** をクリックします。**[データベース]** または **[サーバー]** をクリックすると、該当する情報が表示されます。

![サーバーとデータベースの情報の表示][サーバーとデータベースの情報の表示]

## <span id="ConnectionInfo"></span></a>方法: SQL データベース接続情報を取得する

SQL データベースの接続情報を取得するには、ポータルで **[SQL データベース]** をクリックし、データベースの名前をクリックします。

![データベース情報の表示][データベース情報の表示]

次に、**[ADO.Net、ODBC、PHP、および JDBC の SQL データベース接続文字列を表示する]** をクリックします。

![Show connection strings][Show connection strings]

表示されたウィンドウの [PHP] セクションで、**サーバー**、**データベース**、**ユーザー名**の値を書き留めます。パスワードは SQL データベースの作成時に使用したパスワードになります。

## <span id="Connect"></span></a>方法: SQL データベース インスタンスに接続する

次の例では、**SQLSRV** および **PDO\_SQLSRV** 拡張を使用して、`testdb` という名前の SQL データベースに接続する方法を示しています。前のセクションで取得した情報が必要になります。`SERVER_ID` を 10 桁のサーバー ID (前のセクションで取得したサーバーの値の先頭 10 文字) に置き換え、正しい値 (ユーザー名およびパスワード) を `$user` および `$pwd` 変数に設定します。

##### SQLSRV

    $server = "tcp:<value of SERVER from section above>";
    $user = "<value of USERNAME from section above>"@SERVER_ID;
    $pwd = "password";
    $db = "testdb";

    $conn = sqlsrv_connect($server, array("UID"=>$user, "PWD"=>$pwd, "Database"=>$db));

    if($conn === false){
        die(print_r(sqlsrv_errors()));
    }

##### PDO\_SQLSRV

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

## <span id="NextSteps"></span></a>次のステップ

ここで説明したように、SQL データベースの使用は SQL Server の使用によく似ています。ここで示したように SQL データベースへの接続を確立したら、**SQLSRV** または **PDO\_SQLSRV** API を使用して、データの取得、更新、削除、挿入が可能になります。**SQLSRV** と **PDO\_SQLSRV** API の詳細については、[Microsoft Drivers for PHP for SQL Server のドキュメント][Microsoft Drivers for PHP for SQL Server のドキュメント]を参照してください。ただし、SQL データベースと SQL Server のいくつかの違いがアプリケーションに影響する場合があります。詳細については、「[ガイドラインと制限事項 (SQL データベース)][ガイドラインと制限事項 (SQL データベース)]」を参照してください。

Azure での PHP と SQL データベースの使用方法を示すサンプルは、<https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-sqlazure> から入手できます。

  [管理ポータル]: https://manage.windowsazure.com
  [PHP と SQL データベースの始め方に関するページ]: http://blogs.msdn.com/b/brian_swan/archive/2010/02/12/getting-started-with-php-and-sql-azure.aspx
  [次のステップ]: #NextSteps
  [概念]: #Concepts
  [方法: 環境を設定する]: #Setup
  [方法: SQL データベースを作成する]: #CreateServer
  [方法: SQL データベース接続情報を取得する]: #ConnectionInfo
  [方法: SQL データベース インスタンスに接続する]: #Connect
  [Microsoft Drivers for PHP for SQL Server]: http://www.microsoft.com/download/en/details.aspx?id=20098
  [Microsoft Web プラットフォーム インストーラー]: http://go.microsoft.com/fwlink/?LinkId=253447
  []: http://php.net/manual/en/install.windows.iis7.php
  [1]: http://www.microsoft.com/ja-jp/download/details.aspx?id=29062
  [2]: http://php.net/manual/en/sqlsrv.requirements.php
  [新しい Azure の Web サイトの作成]: ./media/sql-database-php-how-to-use-sql-database/plus-new.png
  [新しい SQL データベースのカスタム作成]: ./media/sql-database-php-how-to-use-sql-database/create_custom_sql_db-2.png
  [SQL データベースの設定の指定]: ./media/sql-database-php-how-to-use-sql-database/new-sql-db.png
  [新しい SQL データベース サーバーの作成]: ./media/sql-database-php-how-to-use-sql-database/db-server-settings.png
  [サーバーとデータベースの情報の表示]: ./media/sql-database-php-how-to-use-sql-database/sql-dbs-portal.png
  [データベース情報の表示]: ./media/sql-database-php-how-to-use-sql-database/go-to-db-info.png
  [Show connection strings]: ./media/sql-database-php-how-to-use-sql-database/show-connection-string-2.png
  [Microsoft Drivers for PHP for SQL Server のドキュメント]: http://msdn.microsoft.com/ja-jp/library/dd638075(SQL.10).aspx
