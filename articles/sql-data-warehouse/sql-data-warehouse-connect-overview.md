<properties
   pageTitle="Azure SQL Data Warehouse への接続 | Microsoft Azure"
   description="Azure SQL Data Warehouse に接続するための接続の概要"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/20/2016"
   ms.author="sonyama;barbkess"/>

# Azure SQL Data Warehouse への接続

> [AZURE.SELECTOR]
- [概要](sql-data-warehouse-connect-overview.md)
- [認証](sql-data-warehouse-authentication.md)
- [ドライバー](sql-data-warehouse-connection-strings.md)

Azure SQL Data Warehouse への接続の概要です。

## ポータルからの接続文字列の検索

SQL Data Warehouse は Azure SQL サーバーに関連付けられています。接続するには、サーバーの完全修飾名 (**servername**.database.windows.net*) が必要になります。

完全修飾サーバー名を検索するには、次の手順に従います。

1. [Azure ポータル][]にアクセスします。
2. **[SQL データベース]** をクリックし、接続するデータベースをクリックします。この例では、AdventureWorksDW サンプル データベースを使用します。
3. サーバーの完全名を見つけます。

    ![Full server name][1]

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

ただし、SQL Data Warehouse は分散 MPP (超並列処理) システムであるため、セッション ID と要求 ID は SQL Server とは少し異なる方法で示されます。

セッションおよび要求は、それぞれの ID によって論理的に示されます。

| ID | 値の例 |
| :--------- | :------------ |
| セッション ID | SID123456 |
| 要求 ID | QID123456 |

セッション ID には「Session ID」の短縮形である SID がプレフィックスとして、要求 ID には「Query ID」の短縮形である QID がプレフィックスとしてそれぞれ付加されていることに注意してください。

この情報は、クエリのパフォーマンスの監視中にクエリを識別するのに役立ちます。クエリのパフォーマンスの監視は、[Azure ポータル]と動的管理ビューのいずれかを使用して実行できます。

このクエリでは、現在のセッションを識別します。

```sql
SELECT SESSION_ID()
;
```

ユーザーのデータ ウェアハウスに対して現在実行中のクエリ、または最近実行されたクエリをすべて表示するには、次の例を使用できます。これにより、ビューが作成、実行されます。

```sql
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

SELECT * FROM dbo.vSessionRequests;
```

## 次のステップ

Visual Studio またはその他のアプリケーションを使用してデータ ウェアハウスに対するクエリを開始するには、[Visual Studio を使用したクエリ][]に関するページを参照してください。


<!--Arcticles-->

[Visual Studio を使用したクエリ]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Azure ポータル]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0622_2016-->