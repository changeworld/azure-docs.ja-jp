---
title: 既知の問題
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance に関する現在の既知の問題と、考えられる回避策または解決策について説明します。
services: sql-database
author: MashaMSFT
ms.author: mathoma
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.custom: references_regions
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/24/2021
ms.openlocfilehash: 31f42cb2dd8405a08477fd6ee9048f42e80e44be
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851289"
---
# <a name="known-issues-with-azure-sql-managed-instance"></a>Azure SQL Managed Instance に関する既知の問題
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

この記事では、[Azure SQL Managed Instance](https://azure.microsoft.com/updates/?product=sql-database&query=sql%20managed%20instance) に関する現在の既知の問題と、解決日または考えられる回避策の一覧を示します。 Azure SQL Managed Instance の詳細については、[概要](sql-managed-instance-paas-overview.md)と[新機能](doc-changes-updates-release-notes-whats-new.md)に関する記事を参照してください。 

 
## <a name="known-issues"></a>既知の問題

|問題  |検出した日  |Status  |解決した日  |
|---------|---------|---------|---------|
|[SQL Server 認証を使用している場合、'@' を含むユーザー名はサポートされません](#when-using-sql-server-authentication-usernames-with--are-not-supported)|2021 年 10 月|||
|[サービス プリンシパルの再作成を提案する、Azure portal の誤解を招くエラー メッセージ](#misleading-error-message-on-azure-portal-suggesting-recreation-of-the-service-principal)|2021 年 9 月|||
|[接続の種類の変更が、フェールオーバー グループ エンドポイント経由の接続に影響しない](#changing-the-connection-type-does-not-affect-connections-through-the-failover-group-endpoint)|2021 年 1 月|回避策あり||
|[@query パラメーターの使用時、プロシージャ sp_send_dbmail が一時的に失敗する可能性がある](#procedure-sp_send_dbmail-may-transiently-fail-when--parameter-is-used)|2021 年 1 月|回避策あり||
|[サーバー信頼グループからマネージド インスタンスを削除した後、分散トランザクションを実行できる](#distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group)|2020 年 10 月|回避策あり||
|[マネージド インスタンスのスケーリング操作の後、分散トランザクションを実行できない](#distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation)|2020 年 10 月|解決済み|2021 年 5 月|
|[以前に削除された論理サーバーと同じ名前の SQL Managed Instance を作成できない](#cannot-create-sql-managed-instance-with-the-same-name-as-logical-server-previously-deleted)|2020 年 8 月|回避策あり||
|Azure SQL の [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql)/[OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)、および SQL Managed Instance の `BACKUP`/`RESTORE` ステートメントで、Azure AD Manage Identity を使用して Azure Storage に対する認証を実行できない|2020 年 9 月|回避策あり||
|[サービス プリンシパルから Azure AD および AKV にアクセスできません](#service-principal-cannot-access-azure-ad-and-akv)|2020 年 8 月|回避策あり||
|[CHECKSUM を使用せずに手動バックアップを復元すると失敗することがある](#restoring-manual-backup-without-checksum-might-fail)|2020 年 5 月|解決済み|2020 年 6 月|
|[既存のジョブを変更、無効化、または有効化するとエージェントが応答しなくなる](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|2020 年 5 月|解決済み|2020 年 6 月|
|[リソース グループに対するアクセス許可が SQL Managed Instance に適用されない](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|2020 年 2 月|解決済み|2020 年 11 月|
|[ポータルを使用したフェールオーバー グループに対する手動フェールオーバーの制限](#limitation-of-manual-failover-via-portal-for-failover-groups)|2020 年 1 月|回避策あり||
|[SQL Agent ロールには、sysadmin 以外のログインに対する明示的な EXECUTE 権限が必要です](#in-memory-oltp-memory-limits-are-not-applied)|2019 年 12 月|回避策あり||
|[エージェント プロセスを再起動すると、SQL Agent ジョブが中断されることがある](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|2019 年 12 月|解決済み|2020 年 3 月|
|[SSDT 内で Azure AD のログインとユーザーがサポートされない](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|2019 年 11 月|回避策なし||
|[インメモリ OLTP のメモリ制限が適用されない](#in-memory-oltp-memory-limits-are-not-applied)|2019 年 10 月|回避策あり||
|[空ではないファイルを削除しようとしたときに誤ったエラーが返される](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|2019 年 10 月|回避策あり||
|[サービス レベルの変更とインスタンスの作成操作が、進行中のデータベースの復元によってブロックされる](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|2019 年 9 月|回避策あり||
|[Business Critical サービス レベルの Resource Governor をフェールオーバー後に再構成しなければならない場合がある](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|2019 年 9 月|回避策あり||
|[サービス レベルのアップグレード後は、複数データベースにまたがる Service Broker のダイアログを再初期化する必要がある](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|2019 年 8 月|回避策あり||
|[Azure AD ログイン タイプの偽装がサポートされない](#impersonation-of-azure-ad-login-types-is-not-supported)|2019 年 7 月|回避策なし||
|[sp_send_db_mail の @query パラメーターはサポートされない](#-parameter-not-supported-in-sp_send_db_mail)|2019 年 4 月|解決済み|2021 年 1 月|
|[geo フェールオーバー後、トランザクション レプリケーションを再構成する必要がある](#transactional-replication-must-be-reconfigured-after-geo-failover)|2019 年 3 月|回避策なし||
|[一時的なデータベースが RESTORE 操作中に使用される](#temporary-database-is-used-during-restore-operation)||回避策あり||
|[TEMPDB の構造と内容は再作成される](#tempdb-structure-and-content-is-re-created)||回避策なし||
|[小さなデータベース ファイルによる記憶域の超過](#exceeding-storage-space-with-small-database-files)||回避策あり||
|[データベース名の代わりに GUID 値が表示される](#guid-values-shown-instead-of-database-names)||回避策あり||
|[エラー ログが非永続的である](#error-logs-arent-persisted)||回避策なし||
|[同じインスタンス内にある 2 つのデータベース上でトランザクション スコープがサポートされない](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||回避策あり|2020 年 3 月|
|[CLR モジュールとリンク サーバーでローカル IP アドレスを参照できないことがある](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||回避策あり||
|Azure Blob Storage からデータベースを復元した後、DBCC CHECKDB を使用してデータベースの整合性が検証されません。||解決済み|2019 年 11 月|
|ソース データベースにインメモリ OLTP オブジェクトが含まれている場合、Business Critical レベルから General Purpose レベルへの組み込みのポイントインタイム データベース復元は成功しません。||解決済み|2019 年 10 月|
|セキュリティで保護された接続を使用する外部 (Azure 以外) メール サーバーのデータベース メール機能||解決済み|2019 年 10 月|
|包含データベースは、SQL Managed Instance 内でサポートされている||解決済み|2019 年 8 月|
||||| 

## <a name="resolved"></a>解決済み

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>CHECKSUM を使用せずに手動バックアップを復元すると失敗することがある

特定の状況で、CHECKSUM なしでマネージド インスタンス上に作成されたデータベースの手動バックアップが復元に失敗することがあります。 このような場合は、成功するまでバックアップの復元を再試行してください。

**回避策**:CHECKSUM を有効にして、マネージド インスタンス上のデータベースの手動バックアップを実行します。

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>既存のジョブを変更、無効化、または有効化するとエージェントが応答しなくなる

状況によっては、既存のジョブを変更したり、無効にしたり、有効にしたりすると、エージェントが応答しなくなることがあります。 この問題は、検出されると自動的に軽減され、エージェント プロセスが再起動されます。

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>リソース グループに対するアクセス許可が SQL Managed Instance に適用されない

リソース グループ (RG) に SQL Managed Instance 共同作成者 Azure ロールが適用されている場合、SQL Managed Instance には適用されず、効果がありません。

**回避策**:ユーザーの SQL Managed Instance 共同作成者ロールをサブスクリプション レベルで設定します。

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>エージェント プロセスを再起動すると、SQL エージェント ジョブが中断されることがある

**(2020 年 3 月に解決済み)** SQL Agent では、ジョブが開始されるたびに新しいセッションが作成され、メモリ消費量が徐々に増加します。 内部メモリの制限に達してスケジュールされたジョブの実行がブロックされることのないように、エージェント プロセスのメモリ使用量がしきい値に達すると、エージェント プロセスが再起動されます。 これにより、再起動の時点で実行中のジョブの実行が中断される可能性があります。

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>sp_send_db_mail の @query パラメーターはサポートされない

[sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) プロシージャの `@query` パラメーターは機能しません。

## <a name="has-workaround"></a>回避策あり

### <a name="changing-the-connection-type-does-not-affect-connections-through-the-failover-group-endpoint"></a>接続の種類の変更が、フェールオーバー グループ エンドポイント経由の接続に影響しない

インスタンスが [自動フェールオーバー グループ](../database/auto-failover-group-overview.md)に参加している場合に、インスタンスの[接続の種類](../managed-instance/connection-types-overview.md)を変更しても、フェールオーバー グループ リスナー エンドポイントを介して確立された接続で有効になりません。

**回避策**: 接続の種類を変更した後に、自動フェールオーバー グループを切断し、再作成します。

### <a name="procedure-sp_send_dbmail-may-transiently-fail-when-query-parameter-is-used"></a>@query パラメーターの使用時、プロシージャ sp_send_dbmail が一時的に失敗する可能性がある

`@query`パラメーターの使用時、プロシージャ `sp_send_dbmail` が一時的に失敗する可能性があります。 この問題が発生した場合は、プロシージャ sp_send_dbmail の実行が 2 回に 1 回、エラー `Msg 22050, Level 16, State 1` とメッセージ `Failed to initialize sqlcmd library with error number -2147467259` で失敗します。 このエラーを正しく確認できるようにするには、パラメーター `@exclude_query_output` を既定値 0 にしてこのプロシージャを呼び出す必要があります。そうしないと、このエラーは伝達されません。
この問題は、`sp_send_dbmail` での権限借用と接続プールの使用方法に関連した既知のバグが原因で発生します。
この問題を回避するには、電子メールを送信するためのコードを、出力パラメーター `@mailitem_id` に依存する再試行ロジックにラップします。 実行が失敗した場合は、このパラメーター値が NULL になり、電子メールを正常に送信するには `sp_send_dbmail` をもう 1 回呼び出す必要があることを示します。 この再試行ロジックの例を次に示します。

```sql
CREATE PROCEDURE send_dbmail_with_retry AS
BEGIN
    DECLARE @miid INT
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
        @mailitem_id = @miid OUTPUT

    -- If sp_send_dbmail returned NULL @mailidem_id then retry sending email.
    --
    IF (@miid is NULL)
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
END
```

### <a name="distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group"></a>サーバー信頼グループからマネージド インスタンスを削除した後、分散トランザクションを実行できる

[サーバー信頼グループ](../managed-instance/server-trust-group-overview.md)は、[分散トランザクション](../database/elastic-transactions-overview.md)を実行するための前提条件である、マネージド インスタンス間の信頼を確立するために使用されます。 サーバー信頼グループからマネージド インスタンスを削除した後、またはグループを削除した後も、分散トランザクションを実行できる場合があります。 分散トランザクションを確実に無効にするために適用できる回避策があります。それは、マネージド インスタンスで[手動フェールオーバーを開始](../managed-instance/user-initiated-failover.md)することです。

### <a name="distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation"></a>マネージド インスタンスのスケーリング操作の後、分散トランザクションを実行できない

サービス レベルや仮想コア数の変更を含む SQL Managed Instance のスケーリング操作を実行すると、バックエンドでサーバー信頼グループの設定がリセットされ、[分散トランザクション](../database/elastic-transactions-overview.md)の実行が無効になります。 回避するには、Azure portal で[サーバー信頼グループ](../managed-instance/server-trust-group-overview.md)を削除して、新しく作成します。

### <a name="cannot-create-sql-managed-instance-with-the-same-name-as-logical-server-previously-deleted"></a>以前に削除された論理サーバーと同じ名前の SQL Managed Instance を作成できない

`<name>.database.windows.com` の DNS レコードは、Azure SQL Database 用の[論理サーバーを Azure に](../database/logical-servers.md)作成したときと、SQL マネージド インスタンスを作成したときに作成されます。 DNS レコードは、一意である必要があります。 そのため、SQL Database 用の論理サーバーを作成してから削除した場合、レコードから名前が解放される前に 7 日間のしきい値期間があります。 その期間は、削除された論理サーバーと同じ名前を持つ SQL マネージド インスタンスを作成することはできません。 回避策としては、SQL マネージド インスタンスに別の名前を使用するか、サポート チケットを作成して論理サーバー名を解放する必要があります。  


### <a name="bulk-insert-and-backuprestore-statements-should-use-sas-key-to-access-azure-storage"></a>BULK INSERT および BACKUP/RESTORE ステートメントで、Azure Storage にアクセスするために SAS キーを使用する必要がある

現在、`DATABASE SCOPED CREDENTIAL` 構文で Managed Identity を使用した Azure Storage に対する認証はサポートされていません。 Microsoft では、一括挿入、`BACKUP` および `RESTORE` ステートメント、または `OPENROWSET` 関数で Azure Storage にアクセスする場合、[データベース スコープの資格情報](/sql/t-sql/statements/create-credential-transact-sql#d-creating-a-credential-using-a-sas-token)に[共有アクセス署名](../../storage/common/storage-sas-overview.md)を使用することをお勧めしています。 次に例を示します。

```sql
CREATE DATABASE SCOPED CREDENTIAL sas_cred WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=2016-12-29T16:55:34Z***************';
GO
CREATE EXTERNAL DATA SOURCE MyAzureBlobStorage
  WITH ( TYPE = BLOB_STORAGE, LOCATION = 'https://****************.blob.core.windows.net/invoices', CREDENTIAL= sas_cred );
GO
BULK INSERT Sales.Invoices FROM 'inv-2017-12-08.csv' WITH (DATA_SOURCE = 'MyAzureBlobStorage');
```

`BULK INSERT` で SAS キーを使用する別の例については、[Shared Access Signature を使用したストレージ認証](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage)に関する記事をご覧ください。 

### <a name="service-principal-cannot-access-azure-ad-and-akv"></a>サービス プリンシパルから Azure AD および AKV にアクセスできません

場合によっては、Azure AD および Azure Key Vault (AKV) サービスへのアクセスに使用されるサービス プリンシパルに問題が存在することがあります。 そのため、この問題は SQL Managed Instance での Azure AD 認証および Transparent Database Encryption (TDE) の使用に影響します。 これは、断続的な接続の問題、または `CREATE LOGIN/USER FROM EXTERNAL PROVIDER` や `EXECUTE AS LOGIN/USER` などのステートメントを実行できない場合に発生する可能性があります。 新しい Azure SQL Managed Instance 上でカスタマー マネージド キーを使用して TDE を設定しても、状況によっては機能しないことがあります。

**回避策**: 更新コマンドを実行する前に SQL Managed Instance 上でこの問題が発生しないようにするには、または更新コマンドの後でこの問題が既に発生している場合は、Azure portal に移動し、SQL Managed Instance の [[Active Directory 管理者]](../database/authentication-aad-configure.md?tabs=azure-powershell#azure-portal) ページにアクセスします。 "Azure Active Directory にアクセスするには、Managed Instance にサービス プリンシパルが必要です。 サービス プリンシパルを作成するには、ここをクリックします" というエラー メッセージが表示されるかどうかを確認します。 このエラー メッセージが表示された場合は、それをクリックし、このエラーが解決されるまで、示されるステップ バイ ステップの手順に従います。

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>ポータルを使用したフェールオーバー グループに対する手動フェールオーバーの制限

フェールオーバー グループが、異なる Azure サブスクリプションやリソース グループのインスタンスにまたがっている場合、フェールオーバー グループのプライマリ インスタンスから手動フェールオーバーを開始することはできません。

**回避策**:Geo セカンダリ インスタンスからポータル経由でフェールオーバーを開始します。

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL エージェント ロールには、sysadmin 以外のログインに対する明示的な EXECUTE 権限が必要です

sysadmin 以外のログインが [SQL Agent の固定データベース ロール](/sql/ssms/agent/sql-server-agent-fixed-database-roles)に追加される場合、これらのログインを機能させるには、明示的な EXECUTE 権限をマスター データベースの 3 つのストアド プロシージャに付与する必要があるという問題が存在します。 この問題が発生した場合は、エラー メッセージ "EXECUTE 権限がオブジェクト <object_name> で拒否されました (Microsoft SQL Server、エラー:229)" が表示されます。

**回避策**:SQL Agent 固定データベース ロール (SQLAgentUserRole、SQLAgentReaderRole、または SQLAgentOperatorRole) にログインを追加した後、これらのロールに追加された各ログインに対して次の T-SQL スクリプトを実行して、一覧表示されているストアド プロシージャに明示的に EXECUTE 権限を付与します。

```sql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name];
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name];
```

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>インメモリ OLTP のメモリ制限が適用されない

Business Critical サービス レベルでは、[メモリ最適化オブジェクトの最大メモリ制限](../managed-instance/resource-limits.md#in-memory-oltp-available-space)が正しく適用されない場合があります。 SQL Managed Instance では、ワークロードが、インメモリ OLTP 操作に対してより多くのメモリを使用できる場合があり、これがインスタンスの可用性と安定性に影響を及ぼすことがあります。 インメモリ OLTP クエリは、制限に達しても、すぐには失敗しない可能性があります。 この問題は、まもなく解決されます。 さらに多くのインメモリ OLTP メモリを使用するクエリは、[制限](../managed-instance/resource-limits.md#in-memory-oltp-available-space)に達するとすぐに失敗するようになります。

**回避策**:[SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) を使用して [インメモリ OLTP ストレージの使用状況を監視](../in-memory-oltp-monitor-space.md)し、使用可能な量を超えるメモリがワークロードによって使用されないようにします。 仮想コアの数に応じてメモリ制限を増やすか、ワークロードを最適化して、使用するメモリを減らします。
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>空ではないファイルを削除しようとしたときに誤ったエラーが返される

SQL Server と SQL Managed Instance では、[ユーザーは空でないファイルを削除できません](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites)。 `ALTER DATABASE REMOVE FILE` ステートメントを使用して空でないデータ ファイルを削除しようとすると、エラー `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` はすぐには返されません。 SQL Managed Instance では引き続きファイルの削除が試行されますが、操作は 30 分後に `Internal server error` で失敗します。

**回避策**:`DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` コマンドを使用して、ファイルの内容を削除します。 ファイル グループ内の唯一のファイルの場合は、ファイルを圧縮する前に、このファイル グループに関連付けられているテーブルまたはパーティションからデータを削除する必要があります。また、必要に応じて、このデータを別のテーブルまたはパーティションに読み込みます。

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>サービス レベルの変更とインスタンスの作成操作が、進行中のデータベースの復元によってブロックされる

進行中の `RESTORE` ステートメント、データ移行サービスの移行プロセス、組み込みのポイントインタイム リストアでは、復元プロセスが完了するまで、サービス レベルの更新や既存のインスタンスのサイズ変更、新しいインスタンスの作成がブロックされます。 

復元プロセスでは、復元プロセスが実行されているのと同じサブネット内のマネージド インスタンスとインスタンス プールでこれらの操作がブロックされます。 インスタンス プール内のインスタンスは影響を受けません。 サービス レベルの作成または変更操作は失敗したり、タイムアウトになったりしません。復元プロセスが完了するかキャンセルされると、処理が続行されます。

**回避策**:サービス レベルの作成または更新操作の優先順位が高い場合は、復元プロセスが完了するか、復元プロセスがキャンセルされるまで待機します。

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Business Critical サービス レベルの Resource Governor をフェールオーバー後に再構成しなければならない場合がある

ユーザー ワークロードに割り当てられるリソースを制限することを可能にする [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) 機能では、フェールオーバー後、またはユーザーが開始したサービス レベルの変更 (たとえば、最大仮想コア数やインスタンスの最大ストレージ サイズの変更) 後に、一部のユーザー ワークロードが誤って分類されることがあります。

**回避策**:[Resource Governor](/sql/relational-databases/resource-governor/resource-governor) を使用している場合、`ALTER RESOURCE GOVERNOR RECONFIGURE` を定期的に、または、インスタンスの開始時に SQL タスクを実行する SQL Agent ジョブの一環として実行します。

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>サービス レベルのアップグレード後は、複数データベースにまたがる Service Broker のダイアログを再初期化する必要がある

サービス レベルの変更操作の後、複数データベースにまたがる Service Broker のダイアログで、他のデータベースのサービスにメッセージが配信されなくなります。 メッセージは "*失われたわけではなく*"、センダーのキューに存在しています。 SQL Managed Instance の仮想コア数やインスタンスのストレージ サイズを変更すると、すべてのデータベースについて、[sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) ビューの `service_broke_guid` 値が変更されます。 [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) ステートメントを使用して作成された、他のデータベースの Service Broker を参照する `DIALOG` から、ターゲット サービスにメッセージが配信されなくなります。

**回避策**:複数データベースにまたがる Service Broker ダイアログのメッセージ交換を使用するアクティビティはすべて、サービス レベルを更新する前に停止し、後で再初期化してください。 サービス レベルの変更後、未配信のままのメッセージがあった場合は、それらのメッセージをソース キューから読み取って、ターゲット キューに再送信します。

### <a name="temporary-database-is-used-during-restore-operation"></a>一時的なデータベースが RESTORE 操作中に使用される

SQL Managed Instance でデータベースが復元されるとき、復元サービスではまず、目的の名前で空のデータベースが作成され、インスタンス上でその名前が割り当てられます。 しばらくすると、このデータベースは削除され、実際のデータベースの復元が開始されます。 

"*復元中*" 状態のデータベースには、名前ではなくランダムな GUID 値が一時的に与えられます。 復元プロセスが終了すると、一時的な名前は、`RESTORE` ステートメントで指定した目的の名前に変更されます。 

初期フェーズでは、ユーザーは空のデータベースにアクセスしたり、さらにはこのデータベースにテーブルを作成したり、データを読み込んだりできます。 この一時的なデータベースは、復元サービスで 2 つ目のフェーズが開始されると削除されます。

**回避策**:復元の完了を確認するまで、復元中のデータベースにはアクセスしないでください。

### <a name="exceeding-storage-space-with-small-database-files"></a>小さなデータベース ファイルによる記憶域の超過

インスタンスが Azure ストレージの制限に達する可能性があるため、`CREATE DATABASE`、`ALTER DATABASE ADD FILE`、および `RESTORE DATABASE` ステートメントが失敗することがあります。

SQL Managed Instance の各 General Purpose インスタンスには、Azure Premium ディスク領域用に予約された最大 35 TB のストレージがあります。 各データベース ファイルは、個別の物理ディスクに配置されます。 ディスク サイズとして、128 GB、256 GB、512 GB、1 TB、4 TB のいずれかを指定できます。 ディスク上の未使用領域については請求されませんが、Azure Premium ディスクのサイズ合計が 35 TB を超えることはできません。 場合によっては、内部の断片化のために、合計で 8 TB を必要としない Managed Instance が、記憶域のサイズに関する 35 TB の Azure での制限を超える場合があります。

たとえば、SQL Managed Instance の General Purpose インスタンスに、4 TB のディスクに配置されているサイズが 1.2 TB の大きなファイルが 1 つあるとします。 また、個別の 128 GB のディスクに配置される、それぞれ 1 GB のファイルが 248 個あるとします。 次の点に注意してください。

- 割り当てられるディスク ストレージの合計サイズは、1 x 4 TB + 248 x 128 GB = 35 TB となります。
- インスタンス上のデータベースの予約済み領域の合計は、1 x 1.2 TB + 248 x 1 GB = 1.4 TB となります。

この例は、特定の状況下では特殊なファイルの配分が原因で、SQL Managed Instance のインスタンスが、想定していないときにアタッチ済み Azure Premium ディスク用に予約されている 35 TB の制限に到達する可能性があることを示しています。

この例では既存のデータベースは引き続き機能し、新しいファイルを追加しない限りは問題なく拡張できます。 すべてのデータベースの合計サイズがインスタンス サイズの上限に到達しない場合でも、新しいディスク ドライブ用の十分な領域がないため、新しいデータベースの作成や復元はできません。 その場合に返されるエラーは明確ではありません。

システム ビューを使用して、[残りのファイルの数を特定](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1)できます。 この制限に達した場合は、[DBCC SHRINKFILE ステートメントを使用して、より小さなファイルをいくつか空にして削除](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file)してみるか、[この制限のない Business Critical レベル](../managed-instance/resource-limits.md#service-tier-characteristics)に切り替えてください。


### <a name="guid-values-shown-instead-of-database-names"></a>データベース名の代わりに GUID 値が表示される

複数のシステム ビュー、パフォーマンス カウンター、エラー メッセージ、XEvent、およびエラー ログ エントリで、実際のデータベース名ではなく GUID データベース識別子が表示されています。 将来、実際のデータベース名に置き換えられるため、これらの GUID 識別子には依存しないでください。

**回避策**: `sys.databases` ビューを使用して、GUID データベース識別子の形式で示されている物理データベース名から実際のデータベース名を解決します。

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4;
```

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>同じインスタンス内にある 2 つのデータベース上でトランザクション スコープがサポートされない

**(2020 年 3 月に解決済み)** `TransactionScope` クラス (.NET) は、同じトランザクション スコープ下では、同一インスタンス内にある 2 つのデータベースに対して 2 つのクエリが送信された場合に機能しません。

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

**回避策 (2020 年 3 月以降は不要)** :2 つの接続を使用する代わりに [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) を使って、接続コンテキスト内で他のデータベースを使用します。

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR モジュールとリンク サーバーでローカル IP アドレスを参照できないことがある

SQL Managed Instance 内の CLR モジュールと、現在のインスタンスを参照しているリンク サーバーまたは分散クエリでは、ローカル インスタンスの IP を解決できないことがあります。 このエラーは一時的な問題です。

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>同じインスタンス内にある 2 つのデータベース上でトランザクション スコープがサポートされない

**(2020 年 3 月に解決済み)** `TransactionScope` クラス (.NET) は、同じトランザクション スコープ下では、同一インスタンス内にある 2 つのデータベースに対して 2 つのクエリが送信された場合に機能しません。

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

**回避策 (2020 年 3 月以降は不要)** :2 つの接続を使用する代わりに [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) を使って、接続コンテキスト内で他のデータベースを使用します。


## <a name="no-resolution"></a>解決策なし

### <a name="when-using-sql-server-authentication-usernames-with--are-not-supported"></a>SQL Server 認証を使用している場合、'@' を含むユーザー名はサポートされません

中央に '@' 記号を含むユーザー名 (例: 'abc@xy') は、SQL Server 認証を使用してログインできません。

### <a name="misleading-error-message-on-azure-portal-suggesting-recreation-of-the-service-principal"></a>サービス プリンシパルの再作成を提案する、Azure portal の誤解を招くエラー メッセージ

Azure SQL Managed Instance の Azure portal の _[Active Directory 管理者]_ ブレードには、サービス プリンシパルが既に存在している場合でも、次のエラー メッセージが表示されることがあります。

"Azure Active Directory にアクセスするには、Managed Instance にサービス プリンシパルが必要です。 サービス プリンシパルを作成するには、ここをクリックします"

マネージド インスタンスのサービス プリンシパルが既に存在するか、マネージド インスタンスの AAD 認証が動作している場合、このエラー メッセージを無視できます。 

サービス プリンシパルが存在するかどうかを確認するには、Azure portal の _[エンタープライズ アプリケーション]_ ページに移動し、_[アプリケーションの種類]_ ドロップダウン リストから _[マネージド ID]_ を選択し、_[適用]_ を選択し、検索ボックスにマネージド インスタンスの名前を入力します。 インスタンス名が結果の一覧に表示された場合、サービス プリンシパルは既に存在しており、それ以上の操作は必要ありません。

エラー メッセージの指示に従い、エラー メッセージのリンクをクリックした場合、マネージド インスタンスのサービス プリンシパルは再作成されています。 その場合、Azure AD 認証が正しく機能するように、新しく作成されたサービス プリンシパルに Azure AD 読み取りアクセス許可を割り当ててください。 これは、Azure PowerShell で[こちらの手順](../database/authentication-aad-configure.md?tabs=azure-powershell#powershell)に従うことで実行できます

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>SSDT 内で Azure AD のログインとユーザーがサポートされない

SQL Server Data Tools では、Azure AD のログインとユーザーが完全にはサポートされません。

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>Azure AD ログイン タイプの偽装がサポートされない

次の Azure Active Directory (Azure AD) プリンシパルの、`EXECUTE AS USER` または `EXECUTE AS LOGIN` を使用した偽装はサポートされていません。
- 別名が付けられた Azure AD ユーザー。 この場合は `15517` エラーが返されます。
- Azure AD アプリケーションまたはサービス プリンシパルに基づく Azure AD のログインおよびユーザー。 この場合は `15517` および `15406` エラーが返されます。

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>geo フェールオーバー後、トランザクション レプリケーションを再構成する必要がある

自動フェールオーバー グループ内のデータベースでトランザクション レプリケーションが有効な場合、SQL Managed Instance 管理者は、別のリージョンへのフェールオーバーが発生した後で、古いプライマリ上のすべてのパブリケーションをクリーンアップしてから、新しいプライマリ上でそれらを再構成する必要があります。 詳細については、「[レプリケーション](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication)」をご覧ください。

### <a name="tempdb-structure-and-content-is-re-created"></a>TEMPDB の構造と内容は再作成される

`tempdb` は常に 12 個のデータ ファイルに分割され、ファイルの構造は変更できません。 ファイルあたりの最大サイズは変更できず、`tempdb` に新しいファイルを追加することはできません。 `Tempdb` は、インスタンスが開始またはフェールオーバーしたときに、常に空のデータベースとして再作成され、`tempdb` で行われたどの変更も保持されません。


### <a name="error-logs-arent-persisted"></a>エラー ログが非永続的である

SQL Managed Instance で利用可能なエラー ログは永続的ではなく、このログのサイズは、最大ストレージ上限には含まれません。 フェールオーバーが発生した場合、エラー ログは自動的に消去される可能性があります。 SQL Managed Instance が複数の仮想マシンで複数回移動されたことが原因で、エラー ログの履歴に欠落部分が生じる可能性があります。

## <a name="contribute-to-content"></a>コンテンツの改善への協力

Azure SQL のドキュメントに投稿するには、[Docs 共同作成者ガイド](/contribute/)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

SQL Managed Instance の更新情報と機能強化の一覧については、[SQL Managed Instance サービスの更新情報](https://azure.microsoft.com/updates/?product=sql-database&query=sql%20managed%20instance)に関するページを参照してください。

すべての Azure サービスの更新情報や機能強化については、[サービスの更新情報](https://azure.microsoft.com/updates)を参照してください。
