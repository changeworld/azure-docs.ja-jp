<properties 
	pageTitle="SQL データベースの管理方法" 
	description="Azure SQL Database の管理方法について説明します。" 
	headerExpose="" 
	footerExpose="" 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor="" 
	services="sql-database" 
	documentationCenter=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="jeffreyg"/>


## 概要

この記事では、Azure SQL データベースで単純な管理タスクを実行する方法を示します。 

## 方法:Management Studio を使用して Azure 上の SQL データベースに接続する

Management Studio は、単一のワークスペースで複数の SQL Server インスタンスと複数のサーバーを管理できる管理ツールです。内部設置型の SQL Server インスタンスが既に存在する場合は、タスクをサイド バイ サイドで実行するために、内部設置型インスタンスと Azure 上の論理サーバーの両方に対して接続を開くことができます。

Management Studio には、構文チェック機能や、再利用の目的でスクリプトと名前付きクエリを保存する機能など、現時点では管理ポータルで使用できない機能が含まれています。SQL データベースは、単純な表形式のデータ ストリーム (TDS) エンドポイントです。Management Studio を含め、TDS に対して使用できるどのツールも、SQL データベースを操作するときに有効に使用できます。内部設置型サーバーを対象にして作成したスクリプトは、SQL データベース論理サーバーで実行されます。 

この手順では、Management Studio を使用して、Azure 上の論理サーバーに接続します。この手順では SQL Server Management Studio Version 2008 R2 または 2012 が必要です。Management Studio のダウンロードまたは接続に関するヘルプが必要な場合は、このサイトの「[Management Studio を使用した SQL データベースの管理][]」をご覧ください。

接続する前に、ローカル システムのポート 1433 で送信要求を許可するファイアウォールの例外を作成する必要が生じることがあります。既定でセキュリティにより保護されたコンピューターでは通常、ポート 1433 が開かれていません。 

## 内部設置型サーバーのファイアウォールの構成

1. セキュリティが強化された Windows ファイアウォールで、新しい送信の規則を作成します。

2. **[ポート]** をクリックし、TCP 1433 を指定して、**[接続を許可する]** を選択し、**[パブリック]** プロファイルが選択されていることを確認します。

3.  *WindowsAzureSQLDatabase (tcp-out) port 1433* など、わかりやすい名前を指定します。 


## 論理サーバーに接続する

1. Management Studio の [サーバーに接続する] で、データベース エンジンが選択されていることを確認し、 *servername*.database.widnows.net という形式で論理サーバー名を入力します。

	サーバー ダッシュ ボードの [URL の管理] で、管理ポータルで使用されている完全修飾サーバー名を取得することもできます。

2. [認証] で **[SQL Server 認証]** を選択し、論理サーバーを構成したときに作成した管理者ログインを入力します。

3. **[オプション]** をクリックします。 

4. [データベースへの接続] で **master** を指定します。


## 内部設置型サーバーに接続する

1. Management Studio の [サーバーに接続する] で、データベース エンジンが選択されていることを確認し、 *servername**instancename* という形式でローカル インスタンスの名前を入力します。サーバーがローカルであり、既定のインスタンスを使用している場合は、「 *localhost*」と入力します。

2. [認証] で **[Windows 認証]** を選択し、sysadmin ロールのメンバーである Windows アカウントを入力します。


## 方法:Azure 上の SQL データベースにログインとユーザーを追加する

データベースをデプロイした後に、ログインを構成し、アクセス許可を割り当てる必要があります。次の手順では、2 つのスクリプトを実行します。

最初のスクリプトでは、master データベースに接続してログインを作成するスクリプトを実行します。ログインは、読み取り操作と書き込み操作をサポートするため、と "sa" アクセス許可なしでシステム クエリを実行する機能などの操作タスクを委任するために使用されます。

作成するログインは、SQL Server 認証ログインであることが必要です。Windows ユーザーの ID を使用する、または ID を要求するスクリプトが既に存在している場合は、そのスクリプトは SQL データベースで動作しません。

2 つ目のスクリプトは、データベース ユーザーにアクセス許可を割り当てます。このスクリプトでは、既に Azure に読み込まれているデータベースに接続します。

## ログインを作成する

1. Management Studio で、Azure 上の論理サーバーに接続し、データベース フォルダーを開き、**master** を右クリックして、**[新しいクエリ]** をクリックします。

2. 次の Transact SQL ステートメントを使用して、ログインを作成します。パスワードを、有効なパスワードに置き換えます。それぞれを個別に選択し、**[実行]** をクリックします。残りのログインに対して、同じ手順を繰り返します。

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on master, execute each line separately
    -- use this login to manage other logins on this server
    CREATE LOGIN sqladmin WITH password='&lt;ProvidePassword&gt;'; 
    CREATE USER sqladmin FROM LOGIN sqladmin;
    EXEC sp_addrolemember 'loginmanager', 'sqladmin';

    -- use this login to create or copy a database
    CREATE LOGIN sqlops WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlops FROM LOGIN sqlops;
    EXEC sp_addrolemember 'dbmanager', 'sqlops';
</pre></div>


## データベース ユーザーを作成する

1. データベース フォルダーを展開し、**school** を右クリックして、**[新しいクエリ]** をクリックします。

2. 次の Transact SQL ステートメントを使用し、データベース ユーザーを追加します。パスワードを、有効なパスワードに置き換えます。 

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on a regular database, execute each line separately
    -- use this login for read operations
    CREATE LOGIN sqlreader WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlreader FROM LOGIN sqlreader;
    EXEC sp_addrolemember 'db_datareader', 'sqlreader';

    -- use this login for write operations
    CREATE LOGIN sqlwriter WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlwriter FROM LOGIN sqlwriter;
    EXEC sp_addrolemember 'db_datawriter', 'sqlwriter';

    -- grant DMV permissions on the school database to 'sqlops'
    GRANT VIEW DATABASE STATE to 'sqlops';
</pre></div>

## ログインを表示とテストする

1. [新しいクエリ] ウィンドウで **master** に接続し、次のステートメントを実行します。 

        SELECT * from sys.sql_logins;

2. Management Studio で **school** データベースを右クリックして、**[新しいクエリ]** をクリックします。

3. [クエリ] メニューの **[接続]** をポイントし、**[接続の変更]** をクリックします。

4.  *sqlreader* としてログインします。

5. 次のステートメントをコピーし、実行してみます。オブジェクトが存在しないことを示すエラーが表示されます。

        INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
        VALUES (1061, 30, 9);

6. 2 番目のクエリ ウィンドウを開き、接続コンテキストを  *sqlwriter* に変更します。今度は、同じクエリが正常に実行されます。

ここまでで、複数のログインを作成してテストしました。詳細については、「[SQL データベースにおけるデータベースとログインの管理][]」と「[動的管理ビューを使用した Azure SQL データベースの監視][]」をご覧ください。

[SQL データベースにおけるデータベースとログインの管理]: http://msdn.microsoft.com/library/windowsazure/ee336235.aspx
[動的管理ビューを使用した Azure SQL データベースの監視]: http://msdn.microsoft.com/library/windowsazure/ff394114.aspx
[Management Studio を使用した SQL データベースの管理]: http://www.windowsazure.com/develop/net/common-tasks/sql-azure-management/






<!--HONumber=47-->
 