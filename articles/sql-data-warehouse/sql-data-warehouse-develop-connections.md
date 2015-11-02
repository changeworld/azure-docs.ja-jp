<properties
   pageTitle="SQL Data Warehouse への接続 | Microsoft Azure"
   description="ソリューションの開発のために、SQL Data Warehouse に接続する際のヒントです。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/21/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL Data Warehouse への接続 
SQL Data Warehouse に接続するには、認証のためのセキュリティ資格情報を渡す必要があります。接続の確立時に、特定の接続の設定が、クエリ セッションの構築の一環として構成されているのがわかります。

この記事では、SQL Data Warehouse への接続に関する次の点について説明します。

- 認証
- 接続の設定
- セッション ID および要求 ID


## 認証
SQL Data Warehouse に接続するには、次の情報を提供する必要があります。

- 完全修飾サーバー名 
- SQL 認証の指定
- ユーザー名 
- パスワード
- 既定のデータベース (省略可)

ユーザーの認証には SQL 認証を使用しなければならないことに注意してください。現在、信頼済みの認証はサポートされていません。

既定では、ユーザー データベースではなく、マスター データベースに接続されます。ユーザー データベースに接続するには、次の 2 つの操作のいずれかを選択します。

1. SQL Server オブジェクト エクスプ ローラーで、SSDT または アプリケーションの接続文字列を使用してサーバーを登録する際に、 既定のデータベースを指定する。たとえば、ODBC 接続に InitialCatalog パラメーターを含めます。
2. SSDT でセッションを作成する前に、まずユーザー データベースを強調表示する。

> [AZURE.NOTE]SSDT を使用した SQL Data Warehouse への接続に関するガイダンスは、[接続およびクエリ][]の概要についての記事を参照してください。

重要事項を繰り返しますが、接続目的でデータベースを切り替えるための TRANSACT-SQL ステートメント **USE <your DB>** はサポートされていません。

## アプリケーションの接続プロトコル
次のプロトコルのいずれかを使用すると、SQL Data Warehouse に接続できます。

- ADO.NET
- ODBC
- PHP
- JDBC

### ADO.NET 接続文字列の例

```
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### ODBC 接続文字列の例

```
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### PHP の接続文字列の例

```
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( "sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}", "{your_user_name}", "{your_password_here}");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( "Error connecting to SQL Server." );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array("UID" => "{your_user_name}", "pwd" => "{your_password_here}", "Database" => "{your_database}", "LoginTimeout" => 30, "Encrypt" => 1, "TrustServerCertificate" => 0);\r\n$serverName = "tcp:{your_server}.database.windows.net,1433";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### JDBC 接続文字列の決定

```
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## 接続の設定
SQL Data Warehouse では、接続およびオブジェクトの作成中にいくつかの設定が標準化されます。これらをオーバーライドすることはできません。

| データベースの設定 | 値 |
| :----------------- | :--------------------------- |
| ANSI\_NULLS | ON |
| QUOTED\_IDENTIFIERS | ON |
| NO\_COUNT | OFF |
| DATEFORMAT | mdy |
| DATEFIRST | 7 |
| データベースの照合順序 | SQL\_Latin1\_General\_CP1\_CI\_AS |

## セッションと要求
接続が確立してセッションが構築されたら、SQL Data Warehouse にクエリを送信する準備が整ったことになります。

各クエリは、1 つまたは複数の要求 ID によって表示されます。その接続で送信されるすべてのクエリは 1 つのセッションの一部であるため、1 つのセッション ID で示されます。

ただし、SQL Data Warehouse は分散 MPP システムであるため、セッション ID および要求 ID の両方は、SQL Server とは少し異なる方法で表示されます。

セッションおよび要求は、それぞれの ID によって論理的に示されます。
	
| ID | 値の例 |
| :--------- | :------------ |
| セッション ID | SID123456 |
| 要求 ID | QID123456 |

セッション ID には「Session ID」の短縮形である SID がプレフィックスとして、要求 ID には「Query ID」の短縮形である QID がプレフィックスとしてそれぞれ付加されていることに注意してください。

この情報は、クエリのパフォーマンスの監視中にクエリを識別するのに役立ちます。クエリのパフォーマンスの監視は、[Azure ポータル] または動的管理ビューのいずれかから実行できます。

現在のセッションを識別するには、次の関数を使用します。

```
SELECT SESSION_ID()
;
```

ユーザーのデータ ウェアハウスに対して現在実行中のクエリ、または最近実行されたクエリをすべて表示するには、次のようなクエリを使用します。

```
CREATE VIEW dbo.vSessionRequests
AS
SELECT 	 s.[session_id]									AS Session_ID
		,s.[status]										AS Session_Status
		,s.[login_name]									AS Session_LoginName
		,s.[login_time]									AS Session_LoginTime
        ,r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
		,r.[end_compile_time]							AS Request_EndCompileTime
		,r.[end_time]									AS Request_EndTime
		,r.[total_elapsed_time]							AS Request_TotalElapsedDuration_ms
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
        ,DATEDIFF(ms,[start_time],[end_compile_time])	AS Request_CompileDuration_ms
        ,DATEDIFF(ms,[end_compile_time],[end_time])		AS Request_ExecDuration_ms
		,[label]										AS Request_QueryLabel
		,[command]										AS Request_Command
		,[database_id]									AS Request_Database_ID
FROM    sys.dm_pdw_exec_requests r
JOIN    sys.dm_pdw_exec_sessions s	ON	r.[session_id] = s.[session_id]
WHERE   s.[session_id] <> SESSION_ID()
;
```

このクエリが、ソリューションに組み込めるようにビューにカプセル化されていることにご注意ください。ただし、結果を参照するには、ビューを作成して実行する必要があります。

## 次のステップ
接続が完了したら、テーブルの設計を開始できます。 詳細については、[Table design in SQL Data Warehouse (SQL Data Warehouse でのテーブルの デザイン)] を参照してください。

<!--Image references-->

<!--Azure.com references-->
[接続およびクエリ]: ./sql-data-warehouse-get-started-connect.md
[Table design in SQL Data Warehouse (SQL Data Warehouse でのテーブルの デザイン)]: ./sql-data-warehouse-develop-table-design.md

<!--MSDN references-->

<!--Other references-->

<!---HONumber=Oct15_HO4-->