---
title: 動的管理ビューを使用した Azure SQL Database の監視 | Microsoft Docs
description: 動的管理ビューを使用して Microsoft Azure SQL Database を監視することで、一般的なパフォーマンスの問題を検出および診断する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 08/08/2018
ms.openlocfilehash: 97907eee9982fdf6a804bc13edbf8c14efa4ce42
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2018
ms.locfileid: "47161388"
---
# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>動的管理ビューを使用した Azure SQL Database の監視
Microsoft Azure SQL Database では、クエリのブロック、クエリの長時間実行、リソースのボトルネック、不適切なクエリ プランなどが原因で発生するパフォーマンスの問題を、動的管理ビューの一部を使用して診断できます。 このトピックでは、動的管理ビューを使用して一般的なパフォーマンスの問題を検出する方法について説明します。

SQL Database は、次に示す 3 つの動的管理ビューを一部サポートしています。

* データベース関連の動的管理ビュー。
* 実行関連の動的管理ビュー。
* トランザクション関連の動的管理ビュー。

動的管理ビューの詳細については、SQL Server オンライン ブックの「 [動的管理ビューおよび関数 (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) 」を参照してください。

## <a name="permissions"></a>アクセス許可
SQL Database で、動的管理ビューに対してクエリを実行するには、 **VIEW DATABASE STATE** アクセス許可が必要です。 **VIEW DATABASE STATE** アクセス許可は、現在のデータベース内のすべてのオブジェクトに関する情報を返します。
**VIEW DATABASE STATE** アクセス許可を特定のデータベース ユーザーに付与するには、次のクエリを実行します。

```GRANT VIEW DATABASE STATE TO database_user; ```

オンプレミスの SQL Server のインスタンスでは、動的管理ビューにサーバーの状態についての情報が表示されます。 SQL Database では、動的管理ビューには現在の論理データベースに関する情報のみが表示されます。

## <a name="calculating-database-size"></a>データベースのサイズを計算しています
次のクエリは、データベースのサイズ (MB 単位) を返します。

```
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

次のクエリは、データベース内の個々のオブジェクトのサイズ (MB 単位) を返します。

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>接続の監視
[sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) ビューを使用して、特定の Azure SQL Database サーバーに対して確立されている接続についての情報と、各接続の詳細を取得できます。 また、[sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) ビューは、すべてのアクティブなユーザー接続と内部タスクについての情報を取得する場合に役立ちます。
次のクエリは、現在の接続に関する情報を取得します。

```
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> **sys.dm_exec_requests** と **sys.dm_exec_sessions views** を実行するときに、データベースに対するアクセス許可 **VIEW DATABASE STATE** を持っていると、データベースで実行中のすべてのセッションが表示されます。アクセス許可を持っていない場合は、現在のセッションのみが表示されます。
> 
> 

## <a name="monitor-resource-use"></a>リソース使用量の監視

[SQL Database Query Performance Insight](sql-database-query-performance.md) および[クエリ ストア](https://msdn.microsoft.com/library/dn817826.aspx)を使用して、リソースの使用量を監視できます。

また、次の 2 つのビューを使用して使用量を監視することもできます。

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
[sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) ビューは、すべての SQL データベースで使用できます。 **sys.dm_db_resource_stats** ビューには、サービス レベルに関連した最近のリソース使用率データが表示されます。 CPU、データ IO、ログ書き込み、メモリの平均 (%) が 15 秒ごとに記録され、1 時間保持されます。

このビューにはリソース使用率が詳細に表示されるので、現状の分析やトラブルシューティングが目的の場合、最初に **sys.dm_db_resource_stats** を使用してください。 たとえば次のクエリは、現在のデータベースの過去 1 時間の平均リソース使用率と最大リソース使用率を表示します。

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data IO in percent',
        MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

その他のクエリについては、[sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) の例を参照してください。

### <a name="sysresourcestats"></a>sys.resource_stats
**master** データベースの [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) ビューには、特定のサービス レベルとコンピューティング サイズで SQL データベースのパフォーマンス監視に役立つ追加情報があります。 データは 5 分ごとに集められ、約 14 日間保持されます。 このビューは、過去に SQL データベースでリソースがどのように使用されたかを長期にわたり分析する際に役立ちます。

次のグラフは、Premium データベースの CPU リソース使用率を示しています (P2 コンピューティング サイズ、1 週間における毎時間の使用率)。 このグラフは月曜日から始まります。5 営業日が経過した後の週末ではアプリケーションの活動が大幅に減っていることがわかります。

![SQL database resource use](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

このデータから、このデータベースのピーク CPU 負荷は現在のところ、P2 コンピューティング サイズに対して 50% をわずかに超える CPU 利用率になっていることがわかります (火曜日の昼)。 アプリケーションのリソース プロファイルにおいて CPU が支配的要因である場合、P2 がワークロードに常に対処できる最適なコンピューティング サイズであると決定できます。 時間の経過と共にアプリケーションの規模が大きくなると予測される場合、アプリケーションがパフォーマンス レベルの制限に達しないように、リソース バッファーを余分に確保しておくことをお勧めします。 コンピューティング サイズを上げると、要求を効果的に処理するための十分な能力がないデータベースで発生するおそれのある、ユーザーに見えるエラーを回避できます。これは特に待機時間が重要な環境に当てはまります。 たとえば、データベース呼び出しの結果に基づいて Web ページを描画するアプリケーションをサポートしているデータベースなどです。

アプリケーションの種類が異なれば、同じグラフの解釈が異なる場合があります。 たとえば、あるアプリケーションで給与データを毎日処理し、同じグラフを生成する場合、P1 コンピューティング サイズでこの種の "一括ジョブ" モデルに問題なく対応できる可能性があります。 P2 コンピューティング サイズが 200 DTU であるのに対して、P1 コンピューティング サイズは 100 DTU です。 P1 コンピューティング サイズのパフォーマンスは、P2 コンピューティング サイズの半分です。 このため、P2 における 50% の CPU 使用率は、P1 における 100% の CPU 使用率に相当します。 アプリケーションにタイムアウトがない場合、当日に完了するのであれば、ジョブに 2 時間かかっても 2.5 時間かかっても問題ないものと思われます。 このカテゴリのアプリケーションではおそらく、P1 コンピューティング サイズを利用できます。 1 日の中にはリソース使用率が低くなる時間帯があるという事実を利用できます。このため、"大きなピーク" が 1 日のそのような時間帯のいずれかに波及することがあります。 ジョブを毎日定刻に完了できる限り、P1 コンピューティング サイズがこの種のアプリケーションに適している (コストが削減される) 場合があります。

Azure SQL Database では、各サーバーの **master** データベースの **sys.resource_stats** ビューにアクティブなデータベース別のリソース利用情報が公開されます。 テーブルのデータは 5 分おきに集計されます。 Basic、Standard、Premium のサービス レベルでは、データをテーブルに表示するのに 5 分を超える時間がかかる可能性があります。このため、このデータはほぼリアルタイムの分析よりも過去の分析に役に立ちます。 **sys.resource_stats** ビューに照会すると、データベースの最近の履歴が表示され、選択した予約で必要なときに望ましいパフォーマンスが発揮されたかどうかを検証できます。

> [!NOTE]
> 次の例で **sys.resource_stats** に照会するためには、論理 SQL データベース サーバーの **master** データベースに接続する必要があります。
> 
> 

次の例は、このビューのデータが表示されているところです。

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![The sys.resource_stats catalog view](./media/sql-database-performance-guidance/sys_resource_stats.png)

次の例では、**sys.resource_stats** カタログ ビューを使用して、SQL データベースでのリソースの使用状況に関する情報を取得するさまざまな方法を示します:

1. データベース userdb1 の過去 1 週間のリソース使用率を確認するには、このクエリを実行します。
   
        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;
2. ワークロードがコンピューティング サイズにどの程度適合しているかを評価するには、リソース メトリック (CPU、読み取り、書き込み、ワーカー数、セッション数) の各点を分析する必要があります。 次に、**sys.resource_stats** を使用してこれらのリソース メトリックの平均値と最大値を報告するように修正したクエリを示します:
   
        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
3. 各リソース メトリックの平均値と最大値に関するこの情報に基づいて、選択したコンピューティング サイズにワークロードが適合しているかどうかを評価できます。 通常、**sys.resource_stats** からの平均値が目標サイズに対する有効な基準となります。 これを主要なものさしとしてください。 たとえば、S2 コンピューティング サイズで Standard サービス レベルを使用しているとします。 CPU と IO の読み取り/書き込みの平均使用率が 40% を下回り、ワーカーの平均数が 50 を下回り、セッションの平均数が 200 を下回っています。 このワークロードには、S1 コンピューティング サイズが適している可能性があります。 データベースがワーカーとセッションの制限内に収まるかどうかは簡単にわかります。 CPU、読み取り、書き込みに関して、データベースが下位のコンピューティング サイズに適合するかどうかを確認するには、下位コンピューティング サイズの DTU 数を現在のコンピューティング サイズの DTU 数で割り、その計算結果に 100 を掛けます。
   
    **S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
   
    この結果は、2 つのコンピューティング サイズの間の相対的パフォーマンス差異を百分率で表したものになります。 リソースの使用がこの量を超えていない場合、ワークロードは下位のコンピューティング サイズに適合する可能性があります。 ただし、リソース使用率の値を全範囲で見て、どのくらいの頻度でデータベースのワークロードが下位のコンピューティング サイズに適合するかを割合の観点から判断する必要があります。 次のクエリは、この例で計算した 40% のしきい値に基づき、リソース ディメンション別の適合率を出力します。
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    データベースのサービス レベルに基づき、ワークロードが下位のコンピューティング サイズに適合するかどうかを判断できます。 データベース ワークロード目標が 99.9% で、上記のクエリが 3 つすべてのリソース ディメンションに対して 99.9% を超える値を返す場合、そのワークロードはおそらく下位のコンピューティング サイズに適合します。
   
    適合率を見ると、目標を満たすために上位のコンピューティング サイズに移行する必要があるかどうかもわかります。 たとえば、userdb1 では過去 1 週間の CPU 使用率が次のようになっています。
   
   | 平均 CPU 使用率 (%) | 最大 CPU 使用率 (%) |
   | --- | --- |
   | 24.5 |100.00 |
   
    平均 CPU は、コンピューティング サイズの制限の約 4 分の 1 であり、データベースのコンピューティング サイズにうまく適合するでしょう。 ただし最大値は、データベースがコンピューティング サイズの制限に到達することを示しています。 次に上位のコンピューティング サイズに移動する必要がありますか。 ワークロードが 100% に到達する回数を確認し、それをデータベースのワークロード目標と比較します。
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    このクエリが 3 つのリソース ディメンションのいずれにも 99.9% 未満の値を返す場合、次の上位のコンピューティング サイズに移行すること、またはアプリケーションの調整手法を使用して SQL データベースの負荷を減らすことを検討してください。
4. この演習では、予測される将来のワークロードの増加も考慮しています。

エラスティック プールでは、このセクションで説明した手法を使用して、プール内の個々のデータベースを監視できます。 ただし、プールを全体として監視することもできます。 詳細については、[エラスティック プールの監視と管理](sql-database-elastic-pool-manage-portal.md)に関する記事を参照してください。


### <a name="maximum-concurrent-requests"></a>同時要求の最大数
同時要求の数を確認するには、SQL データベースで次の Transact-SQL クエリを実行します。

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

オンプレミス SQL Server データベースのワークロードを分析するには、分析したい特定のデータベースでフィルター処理するようにこのクエリを変更します。 たとえば、MyDatabase という名前のオンプレミス データベースがある場合、次の Transact-SQL クエリはそのデータベースの同時要求の数を返します。

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

これはある時点のスナップショットにすぎません。 ワークロードと同時要求の要件をさらに詳しく理解するには、時間をかけて多くのサンプルを収集する必要があります。

### <a name="maximum-concurrent-logins"></a>同時ログインの最大数
ユーザーやアプリケーションのパターンを分析すれば、ログインの頻度を理解できます。 さらに、テスト環境で実際の負荷を実行し、この上限やこの記事で説明されている他の制限に達していないことを確認できます。 同時ログイン数または履歴を表示できる単一のクエリや動的管理ビュー (DMV) はありません。

複数のクライアントで同じ接続文字列を使用している場合、サービスによってそれぞれのログインが認証されます。 10 人のユーザーが同じユーザー名とパスワードを使ってデータベースに同時に接続した場合、10 件の同時ログインが発生します。 この制限は、ログインと認証の期間のみに適用されます。 同じ 10 人のユーザーがデータベースに順番に接続した場合、同時ログイン数が 1 より大きくなることはありません。

> [!NOTE]
> この制限は現在のところ、エラスティック プールのデータベースには適用されません。
> 
> 

### <a name="maximum-sessions"></a>セッションの最大数
現在アクティブなセッションの数を確認するには、SQL データベースで次の Transact-SQL クエリを実行します。

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

オンプレミス SQL Server のワークロードを分析する場合は、特定のデータベースが対象になるようにクエリを変更してください。 このクエリは、Azure SQL Database への移行を検討している場合に、データベースの潜在的なセッション ニーズを判断するのに役立ちます。

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

ここでも、これらのクエリはある時点の数を返します。 時間をかけて複数のサンプルを集めると、セッションの使用状況を正確に把握できます。

SQL Database 分析の場合、[sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) ビューにクエリを実行し、**active_session_count** 列を確認して、セッションの過去の統計値を取得できます。 

## <a name="monitoring-query-performance"></a>クエリのパフォーマンスの監視
クエリが低速または実行時間が長いと、大量のシステム リソースが消費される可能性があります。 ここでは、動的管理ビューを使用して、いくつかの一般的なクエリ パフォーマンスの問題を検出する方法について説明します。 Microsoft TechNet の [SQL Server 2008 のパフォーマンスに関する問題のトラブルシューティング](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) についての記事が、古いものの今でもトラブルシューティングに役立ちます。

### <a name="finding-top-n-queries"></a>上位 N 個のクエリの検索
次の例では、平均 CPU 時間の上位 5 個のクエリに関する情報を返します。 この例では、論理的に等価なクエリがリソースの累計消費量ごとにグループ化されるように、クエリ ハッシュに応じてクエリを集計します。

```
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
    MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
    ((CASE statement_end_offset
        WHEN -1 THEN DATALENGTH(ST.text)
        ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
     FROM sys.dm_exec_query_stats AS QS
     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>クエリのブロックの監視
クエリが低速または実行時間が長いと、大量のリソースが消費され、結果としてクエリがブロックされる可能性があります。 ブロックの原因には、不適切なアプリケーション設計、不適切なクエリ プラン、有効なインデックスの欠如などがあります。 sys.dm_tran_locks ビューを使用すると、Azure SQL Database で現在ロックされているアクティビティに関する情報を取得できます。 コード例については、SQL Server オンライン ブックの「[sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx)」をご覧ください。

### <a name="monitoring-query-plans"></a>クエリ プランの監視
クエリ プランの効率が悪いと、CPU の消費量が増える可能性があります。 次の例では、[sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) ビューを使用して、累積 CPU 時間が最も多いクエリを特定します。

```
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>関連項目
[SQL Database の概要](sql-database-technical-overview.md)

