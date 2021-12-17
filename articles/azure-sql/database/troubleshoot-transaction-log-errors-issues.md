---
title: トランザクション ログの問題のトラブルシューティング
titleSuffix: Azure SQL Database and Azure SQL Managed Instance
description: Azure SQL Database または Azure SQL Managed Instance での Azure SQL Database のトランザクション ログの問題をトラブルシューティングする手順について説明します
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.topic: troubleshooting
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 07/23/2021
ms.openlocfilehash: 2bddb630cd2dad83992cfd740fb44b127a770c51
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672801"
---
# <a name="troubleshooting-transaction-log-errors-with-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database および Azure SQL Managed Instance のトランザクション エラーのトラブルシューティング
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

トランザクション ログがいっぱいになり、新しいトランザクションを受け付けることができない場合、エラー 9002 または 40552 が表示されることがあります。 これらのエラーは、Azure SQL Database または Azure SQL Managed Instance によって管理されているデータベースのトランザクション ログが領域のしきい値を超えており、トランザクションを引き続き受け入れることができない場合に発生します。 

これらのエラーは、SQL Server の完全なトランザクション ログに関する問題と似ていますが、Azure SQL Database または Azure SQL Managed Instance で異なる解決策があります。

> [!NOTE]
> **この記事では、Azure SQL Database と Azure SQL Managed Instance に重点を置いています。** Azure SQL Database と Azure SQL Managed Instance は、Microsoft SQL Server データベース エンジンの最新の安定バージョンに基づいているため、トラブルシューティングのオプションやツールに違いがある場合があるものに、コンテンツの多くは似ています。 SQL Server でのトランザクションログのトラブルシューティングの詳細については、「[完全なトランザクション ログのトラブルシューティング (SQL Server エラー 9002)](/sql/relational-databases/logs/troubleshoot-a-full-transaction-log-sql-server-error-9002)」を参照してください。

## <a name="automated-backups-and-the-transaction-log"></a>自動バックアップとトランザクション ログ

データベース ファイルの領域管理に関して、Azure SQL Database と Azure SQL Managed Instance にはいくつかの重要な違いがあります。 

- Azure SQL Database または Azure SQL Managed Instance では、トランザクション ログのバックアップが自動的に作成されます。 頻度、リテンション期間、および詳細については、「[自動バックアップ - Azure SQL Database & SQL Managed Instance](automated-backups-overview.md)」を参照してください。 
- Azure SQL Database では、ディスクの空き領域、データベース ファイルの拡張、ファイルの場所も管理されるため、トランザクション ログの問題の一般的な原因と解決策は SQL Server とは異なります。 
- Azure SQL Managed Instance では、データベース ファイルの場所と名前を管理することはできませんが、管理者はデータベース ファイルとファイルの自動拡張の設定を管理できます。 トランザクション ログの問題の一般的な原因と解決策は、SQL Server と似ています。 

SQL Server と同様に、ログのバックアップが作成されるたびに、各データベースのトランザクション ログが切り捨てられます。 切り捨てにより、ログ ファイルに空き領域が生まれ、新しいトランザクションにアクセスできるようになります。 ログ ファイルをログのバックアップで切り捨てることができない場合、ログ ファイルは新しいトランザクションを収容するために拡張されます。 ログ ファイルが Azure SQL Database または Azure SQL Managed Instance の上限に達すると、新しいトランザクションは受け付けられません。 これは非常にまれなシナリオです。

## <a name="prevented-transaction-log-truncation"></a>トランザクション ログの切り捨てを妨げているもの

特定のケースでログの切り捨てが妨げられている原因を調べるには、`sys.databases` の `log_reuse_wait_desc` を参照してください。 「ログ再利用の待機」によって、通常のログのバックアップによるトランザクション ログの切り捨てが妨げられている状況または原因が通知されます。 詳細については、「[sys.databases &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql)」を参照してください。 

```sql
SELECT [name], log_reuse_wait_desc FROM sys.databases;
```

`sys.databases` の `log_reuse_wait_desc` の次の値によって、データベースのトランザクション ログの切り捨てが妨げられている理由が示されている場合があります。

| log_reuse_wait_desc | 診断 | 対応が必要 |
|--|--|--|
| **NOTHING** | 通常の状態。 ログの切り捨てをブロックしているものはありません。 | いいえ。 |
| **CHECKPOINT** | ログの切り捨てを行うには、チェックポイントが必要です。 まれ。 | 継続しない限り、対応は必要ありません。 継続している場合は、[Azure サポート](https://portal.azure.com/#create/Microsoft.Support)にサポート要求を申請してください。 | 
| **LOG BACKUP** | ログのバックアップが実行されています。 | 継続しない限り、対応は必要ありません。 継続している場合は、[Azure サポート](https://portal.azure.com/#create/Microsoft.Support)にサポート要求を申請してください。 | 
| **ACTIVE BACKUP OR RESTORE** | データベースのバックアップが実行されています。 | 継続しない限り、対応は必要ありません。 継続している場合は、[Azure サポート](https://portal.azure.com/#create/Microsoft.Support)にサポート要求を申請してください。 | 
| **ACTIVE TRANSACTION** | 進行中のトランザクションにより、ログの切り捨てが妨げられています。 | アクティブなトランザクションまたはコミットされていないトランザクションがあるため、ログ ファイルを切り捨てることができません。 次のセクションをご覧ください。| 
| **REPLICATION** | Azure SQL Database では、[変更データ キャプチャ (CDC)](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) 機能が原因である可能性があります。<BR>Azure SQL Managed Instance では、[レプリケーション](../managed-instance/replication-transactional-overview.md)または CDC が原因です。 | Azure SQL Database では、[sys.dm_cdc_errors](/sql/relational-databases/system-dynamic-management-views/change-data-capture-sys-dm-cdc-errors) に問い合わせてエラーを解決します。 解決できない場合は、[Azure サポート](https://portal.azure.com/#create/Microsoft.Support)にサポート要求を申請してください。<BR>Azure SQL Managed Instance では、継続的している場合、CDC またはレプリケーションに関係するエージェントを調査します。 CDC のトラブルシューティングの場合は、[msdb.dbo.cdc_jobs](/sql/relational-databases/system-tables/dbo-cdc-jobs-transact-sql) でジョブを問い合せます。 存在しない場合は、[sys.sp_cdc_add_job](/sql/relational-databases/system-stored-procedures/sys-sp-cdc-add-job-transact-sql) を使用して追加します。 レプリケーションの場合は、[トランザクションのレプリケーションをトラブルシューティングする](/sql/relational-databases/replication/troubleshoot-tran-repl-errors)ことを検討してください。 解決できない場合は、[Azure サポート](https://portal.azure.com/#create/Microsoft.Support)にサポート要求を申請してください。 | 
| **AVAILABILITY_REPLICA** | セカンダリ レプリカへの同期が進行中です。 | 継続しない限り、対応は必要ありません。 継続している場合は、[Azure サポート](https://portal.azure.com/#create/Microsoft.Support)にサポート要求を申請してください。 | 

### <a name="log-truncation-prevented-by-an-active-transaction"></a>アクティブなトランザクションによってログの切り捨てが妨げられている

トランザクション ログで新しいトランザクションが受け入れられない場合の最も一般的なシナリオは、実行時間の長いトランザクションまたはブロックされているトランザクションです。

次のサンプル クエリを実行して、コミットされていないトランザクションまたはアクティブなトランザクションとそのプロパティを確認します。

- [sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql) からトランザクションのプロパティに関する情報が返されます。
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) からセッション接続情報が返されます。
- [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) から要求情報 (アクティブな要求の場合) が返されます。 このクエリは、ブロックされているセッションを識別するためにも使用できます。`request_blocked_by` を確認してください。 ブロックの詳細については、「[ブロッキング情報の収集](understand-resolve-blocking.md#gather-blocking-information)」を参照してください。 
- [sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) または [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) DMV を使用して、現在の要求のテキストまたは入力バッファーのテキストが返されます。 `sys.dm_exec_sql_text` の `text` フィールドによって返されたデータが NULL の場合、要求はアクティブではないが、未処理のトランザクションがあります。 その場合は、`sys.dm_exec_input_buffer` の `event_info` フィールドに、データベース エンジンに渡された最後のコマンド文字列が含まれています。 

```sql
SELECT [database_name] = db_name(s.database_id)
, tat.transaction_id, tat.transaction_begin_time, tst.session_id 
, session_open_transaction_count = tst.open_transaction_count --uncommitted and unrolled back transactions open. 
, transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime())
, input_buffer = ib.event_info
, request_text = CASE  WHEN r.statement_start_offset = 0 and r.statement_end_offset= 0 THEN left(est.text, 4000)
                       ELSE    SUBSTRING ( est.[text],    r.statement_start_offset/2 + 1, 
                                           CASE WHEN r.statement_end_offset = -1 THEN LEN (CONVERT(nvarchar(max), est.[text])) 
                                                ELSE r.statement_end_offset/2 - r.statement_start_offset/2 + 1
                                           END  )  END
, request_status = r.status
, request_blocked_by = r.blocking_session_id
, transaction_state = CASE tat.transaction_state    
                     WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                     WHEN 1 THEN 'The transaction has been initialized but has not started.'
                     WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                     WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                     WHEN 4 THEN 'The commit process has been initiated on the distributed transaction. This is for distributed transactions only. The distributed transaction is still active but further processing cannot take place.'
                     WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                     WHEN 6 THEN 'The transaction has been committed.'
                     WHEN 7 THEN 'The transaction is being rolled back.'
                     WHEN 8 THEN 'The transaction has been rolled back.' END 
, transaction_name = tat.name
, azure_dtc_state    --Applies to: Azure SQL Database only
             =    CASE tat.dtc_state 
                 WHEN 1 THEN 'ACTIVE'
                 WHEN 2 THEN 'PREPARED'
                 WHEN 3 THEN 'COMMITTED'
                 WHEN 4 THEN 'ABORTED'
                 WHEN 5 THEN 'RECOVERED' END
, transaction_type = CASE tat.transaction_type    WHEN 1 THEN 'Read/write transaction'
                                             WHEN 2 THEN 'Read-only transaction'
                                             WHEN 3 THEN 'System transaction'
                                             WHEN 4 THEN 'Distributed transaction' END
, tst.is_user_transaction
, local_or_distributed = CASE tst.is_local WHEN 1 THEN 'Local transaction, not distributed' WHEN 0 THEN 'Distributed transaction or an enlisted bound session transaction.' END
, transaction_uow    --for distributed transactions. 
, s.login_time, s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
, session_cpu_time = s.cpu_time, session_logical_reads = s.logical_reads, session_reads = s.reads, session_writes = s.writes
, observed = sysdatetimeoffset()
FROM sys.dm_tran_active_transactions AS tat 
INNER JOIN sys.dm_tran_session_transactions AS tst  on tat.transaction_id = tst.transaction_id
INNER JOIN Sys.dm_exec_sessions AS s on s.session_id = tst.session_id 
LEFT OUTER JOIN sys.dm_exec_requests AS r on r.session_id = s.session_id
CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib 
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) AS est;
```


### <a name="file-management-to-free-more-space"></a>さらに領域を解放するためのファイル管理

トランザクション ログの切り捨てが妨げられている場合は、データベース ファイルに割り当てられている領域をさらに解放することが解決策の一部である可能性があります。 ただし、トランザクション ログ ファイルの切り捨てがブロックされている根本的な状況を解決することが重要です。 

場合によっては、一時的により多くのディスク領域を作成すると、実行時間の長いトランザクションが完了できるため、通常のトランザクション ログのバックアップでトランザクション ログ ファイルの切り捨てがブロックされる状況を取り除くことができます。 ただし、割り当て領域を解放しても、トランザクション ログが再び拡張されるまでの一時的な救済にしかならない場合があります。 

データベースのファイル領域およびエラスティック プールの管理の詳細については、「[Azure SQL Database でのデータベースのファイル領域の管理](file-space-manage.md)」を参照してください。


### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>エラー 40552:トランザクション ログの使用領域が多すぎるため、セッションを終了しました

`40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.`

この問題を解決するには、次の方法を試してください。

1. この問題は、挿入、更新、または削除の各操作が原因で発生する可能性があります。 不要な書き込みを避けるために、トランザクションを確認してください。 バッチ処理を実装したり、複数の小さなトランザクションに分割したりして、すぐに操作される行の数を減らしてみてください。 詳細については、「 [バッチ処理を使用して SQL Database アプリケーションのパフォーマンスを強化する方法](../performance-improve-use-batching.md)」を参照してください。
2. この問題は、インデックスの再構築操作によって発生する可能性があります。 この問題を回避するには、次の式が真であることを確認してください: (テーブル内の影響を受ける行の数) に (更新されるフィールドの平均サイズ (バイト単位) + 80) を乗算した結果 < 2 ギガバイト (GB)。 大きなテーブルの場合は、パーティションを作成し、テーブルの一部のパーティションでのみインデックスのメンテナンスを実行することを検討してください。 詳細については、「 [パーティション テーブルとパーティション インデックスの作成](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes?view=azuresqldb-current&preserve-view=true)」を参照してください。
3. `bcp.exe` ユーティリティまたは `System.Data.SqlClient.SqlBulkCopy` クラスを使用して一括挿入を実行する場合は、1 回のトランザクションでサーバーにコピーされる行数を `-b batchsize` オプションまたは `BatchSize` オプションで制限してください。 詳細については、「 [bcp Utility](/sql/tools/bcp-utility)」を参照してください。
4. `ALTER INDEX` ステートメントでインデックスを再構築する場合は、`SORT_IN_TEMPDB = ON` および `ONLINE = ON` オプションを使用します。 詳細については、「[ALTER INDEX (Transact-SQL)](/sql/t-sql/statements/alter-index-transact-sql)」を参照してください。

> [!NOTE]
> その他のリソース ガバナーのエラーの詳細については、「[リソース ガバナンス エラー](troubleshoot-common-errors-issues.md#resource-governance-errors)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure SQL Database および Azure SQL Managed Instance の接続に関する問題とその他のエラーのトラブルシューティング](troubleshoot-common-errors-issues.md)
- [SQL Database と SQL Managed Instance での一時的な接続エラーのトラブルシューティング](troubleshoot-common-connectivity-issues.md)


トランザクション ログのサイズについては、次のページを参照してください。 
- 単一データベースに対する仮想コア リソースの制限については、[仮想コア購入モデルを使用した単一データベースに対するリソース制限](resource-limits-vcore-single-databases.md)に関するページを参照してください
- エラスティック プールに対する仮想コア リソースの制限については、[仮想コア購入モデルを使用したエラスティック プールに対するリソース制限](resource-limits-vcore-elastic-pools.md)に関するページを参照してください
- 単一データベースに対する DTU のリソース制限については、[DTU 購入モデルを使用した単一データベースに対するリソース制限](resource-limits-dtu-single-databases.md)に関するページを参照してください
- エラスティック プールに対する DTU リソースの制限については、「[DTU ベースの購入モデルを使用したエラスティック プールのリソース制限](resource-limits-dtu-elastic-pools.md)」を参照してください
- SQL Managed Instance のリソース制限については、[SQL Managed Instance のリソース制限](../managed-instance/resource-limits.md)に関するページを参照してください。
