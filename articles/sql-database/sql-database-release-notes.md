---
title: リリース ノート
description: Azure SQL Database サービスの新機能と機能強化、および Azure SQL Database のドキュメントの改善について説明します
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: sstein
ms.openlocfilehash: 3e5069c779cee0700bff6b2236f3cd36547fd623
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659609"
---
# <a name="sql-database-release-notes"></a>SQL Database リリース ノート

この記事では、現在パブリック プレビュー段階にある SQL Database の機能を示します。 SQL Database の更新情報や機能強化については、[SQL Database サービスの更新情報](https://azure.microsoft.com/updates/?product=sql-database)を参照してください。 他の Azure サービスの更新情報や機能強化については、[サービスの更新情報](https://azure.microsoft.com/updates)を参照してください。

## <a name="features-in-public-preview"></a>パブリック プレビュー段階の機能

### <a name="single-database"></a>[1 つのデータベース](#tab/single-database)

| 機能 | 詳細 |
| ---| --- |
| 新しい Fsv2 シリーズおよび M シリーズ ハードウェア世代| 詳細については、「[Hardware generations](sql-database-service-tiers-vcore.md#hardware-generations)」(ハードウェアの世代) を参照してください。|
| 単一データベースとエラスティック プールでの高速データベース復旧 | 詳しくは、「[高速データベース復旧](sql-database-accelerated-database-recovery.md)」をご覧ください。|
|個別の概算数|詳しくは、[個別の概算数](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)に関する記事をご覧ください。|
|行ストアでのバッチ モード (互換性レベル 150)|詳しくは、「[行ストアでのバッチ モード](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore)」をご覧ください。|
| データの検出と分類  |詳しくは、「[Azure SQL Database および SQL Data Warehouse のデータの検出と分類](sql-database-data-discovery-and-classification.md)」をご覧ください。|
| エラスティック データベース ジョブ | 詳しくは、「[エラスティック ジョブの作成、構成、および管理](elastic-jobs-overview.md)」をご覧ください。 |
| エラスティック クエリ | 詳しくは、[エラスティック クエリの概要](sql-database-elastic-query-overview.md)に関する記事をご覧ください。 |
| エラスティック トランザクション | [クラウド データベースにまたがる分散トランザクション](sql-database-elastic-transactions-overview.md)。 |
|メモリ許可フィードバック (行モード) (互換性レベル 150)|詳しくは、「[行モード メモリ許可フィードバック](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)」をご覧ください。|
| Azure portal のクエリ エディター |詳しくは、「[Azure portal の SQL クエリ エディターを使用した接続とデータの照会](sql-database-connect-query-portal.md)」をご覧ください。|
| 単一データベースとエラスティック プールでの R サービス/機械学習 |詳しくは、「[Machine Learning Services in Azure SQL Database (Azure SQL Database での Machine Learning Services)](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)」をご覧ください。|
|SQL Analytics|詳細については、[Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) に関するページをご覧ください。|
|テーブル変数の遅延コンパイル (互換性レベル 150)|詳しくは、「[テーブル変数の遅延コンパイル](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)」をご覧ください。|
| &nbsp; |

### <a name="managed-instance"></a>[Managed Instance](#tab/managed-instance)

| 機能 | 詳細 |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">インスタンス プール</a> | 比較的小規模な SQL インスタンスをクラウドに移行するための、便利かつ費用対効果に優れた方法です。 |
| <a href="https://aka.ms/managed-instance-aadlogins">インスタンス レベルの Azure AD サーバー プリンシパル (ログイン)</a> | <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN FROM EXTERNAL PROVIDER</a> ステートメントを使って、サーバーレベルのログインを作成します。 |
| [トランザクション レプリケーション](sql-database-managed-instance-transactional-replication.md) | テーブルからの変更をマネージド インスタンス、単一データベース、または SQL Server インスタンスに配置された他のデータベースにレプリケートするか、他のマネージド インスタンスまたは SQL Server インスタンス内で一部の行が変更されたときにテーブルを更新します。 詳しくは、「[Azure SQL Database マネージド インスタンス データベースにレプリケーションを構成する](replication-with-sql-database-managed-instance.md)」をご覧ください。 |
| 脅威の検出 |詳しくは、「[Azure SQL Database Managed Instance で脅威検出を構成する](sql-database-managed-instance-threat-detection.md)」をご覧ください。|
| 長期のバックアップ リテンション期間 | 詳細については、現在は制限付きパブリック プレビュー段階である [Azure SQL Database Managed Instance での長期のバックアップ リテンション期間の構成](sql-database-managed-instance-long-term-backup-retention-configure.md)に関するページを参照してください。 | 

---

## <a name="managed-instance---new-features-and-known-issues"></a>マネージド インスタンス - 新機能と既知の問題

### <a name="managed-instance-h2-2019-updates"></a>2019 年下期のマネージド インスタンスの更新プログラム

- [サービス支援サブネット構成](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) ユーザーがデータ トラフィックを制御しながら、マネージド インスタンスでは管理トラフィックのフローが中断されないようにする、サブネット構成を管理するためのセキュリティで保護された便利な方法
- [Bring Your Own Key (BYOK) による透過的なデータ暗号化 (TDE)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) により、保存時のデータ保護に Bring Your Own Key (BYOK) シナリオが可能になり、組織がキーとデータの管理における職務を分離できるようになります。
- [自動フェールオーバー グループ](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/)では、プライマリ インスタンスから別のリージョンにあるセカンダリ インスタンスへの、すべてのデータベースのレプリケートが有効になります。
- [グローバル トレース フラグ](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/)を使用して、マネージド インスタンスの動作を構成します。

### <a name="managed-instance-h1-2019-updates"></a>2019 年上期のマネージド インスタンスの更新プログラム

2019 年上期のマネージド インスタンスのデプロイ モデルでは、次の機能が有効になっています。
  - <a href="https://aka.ms/sql-mi-visual-studio-subscribers">Visual Studio サブスクライバー向けの Azure の月単位のクレジット</a>と強化された[リージョン制限](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)を備えたサブスクリプションのサポート
  - <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016、SharePoint 2019</a>、および <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance">Dynamics 365 Business Central</a> のサポート
  - 選択した<a href="https://aka.ms/managed-instance-collation">サーバーレベルの照合順序</a>と<a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">タイム ゾーン</a>を使用して、インスタンスを作成します。
  - マネージド インスタンスは現在、<a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">組み込みのファイアウォール</a>によって保護されています。
  - より優れたネットワーク パフォーマンスと、<a href="https://aka.ms/four-cores-sql-mi-update">Gen5 ハードウェア世代における 4 vCores</a> またはポイント イン タイム リストアのための<a href="https://aka.ms/managed-instance-configurable-backup-retention">最大 35 日間のバックアップ保有期間の構成</a>を得るために、[パブリック エンドポイント](sql-database-managed-instance-public-endpoint-configure.md)と [Proxy Override](sql-database-connectivity-architecture.md#connection-policy) 接続を使用するように、インスタンスを構成します。 [長期のバックアップ リテンション期間](sql-database-long-term-retention.md#managed-instance-support) (最長 10 年間) は、現在、制限付きパブリック プレビュー段階です。  
  - 新しい機能を利用すると、<a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">PowerShell を使用してお使いのデータベースを別のデータ センターに geo リストアして</a>、[データベース名を変更し](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/)、[仮想クラスターを削除する](sql-database-managed-instance-delete-virtual-cluster.md)ことができます。
  - 新しい組み込みの[インスタンス共同作成者ロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor)を使用すると、セキュリティ原則による職務の分離 (SoD) のコンプライアンスとエンタープライズ標準によるコンプライアンスを有効にできます。
  - マネージド インスタンスは、中国北部 2 および中国東部 2 と、GA (US Gov テキサス、US Gov アリゾナ) の Azure Government リージョン内で利用できます。 また、次のパブリック リージョンでも利用できます。オーストラリア中部、オーストラリア中部 2、ブラジル南部、フランス南部、アラブ首長国連邦中部、アラブ首長国連邦北部、南アフリカ北部、南アフリカ西部です。

### <a name="known-issues"></a>既知の問題

|問題  |検出した日  |Status  |解決した日  |
|---------|---------|---------|---------|
|[CHECKSUM を使用せずに手動バックアップを復元すると失敗することがある](#restoring-manual-backup-without-checksum-might-fail)|2020 年 5 月|回避策あり| |
|[既存のジョブを変更、無効化、または有効化するとエージェントが応答しなくなる](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|2020 年 5 月|自動的に軽減済み| |
|[リソース グループに対するアクセス許可が Managed Instance に適用されない](#permissions-on-resource-group-not-applied-to-managed-instance)|2020 年 2 月|回避策あり| |
|[ポータルを使用したフェールオーバー グループに対する手動フェールオーバーの制限](#limitation-of-manual-failover-via-portal-for-failover-groups)|2020 年 1 月|回避策あり| |
|[SQL Agent ロールには、sysadmin 以外のログインに対する明示的な EXECUTE 権限が必要です](#in-memory-oltp-memory-limits-are-not-applied)|2019 年 12 月|回避策あり| |
|[エージェント プロセスを再起動すると、SQL Agent ジョブが中断されることがある](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|2019 年 12 月|解決済み|2020 年 3 月|
|[SSDT 内では AAD のログインとユーザーがサポートされない](#aad-logins-and-users-are-not-supported-in-ssdt)|2019 年 11 月|回避策なし| |
|[インメモリ OLTP のメモリ制限が適用されない](#in-memory-oltp-memory-limits-are-not-applied)|2019 年 10 月|回避策あり| |
|[空ではないファイルを削除しようとしたときに誤ったエラーが返される](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|2019 年 10 月|回避策あり| |
|[サービス レベルの変更とインスタンスの作成操作が、進行中のデータベースの復元によってブロックされる](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|2019 年 9 月|回避策あり| |
|[Business Critical サービス レベルの Resource Governor をフェールオーバー後に再構成しなければならない場合がある](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|2019 年 9 月|回避策あり| |
|[サービス レベルのアップグレード後は、複数データベースにまたがる Service Broker のダイアログを再初期化する必要があります](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|2019 年 8 月|回避策あり| |
|[Azure AD ログイン タイプの偽装はサポートされない](#impersonification-of-azure-ad-login-types-is-not-supported)|2019 年 7 月|回避策なし| |
|[sp_send_db_mail の @query パラメーターはサポートされない](#-parameter-not-supported-in-sp_send_db_mail)|2019 年 4 月|回避策なし| |
|[geo フェールオーバー後、トランザクション レプリケーションを再構成する必要がある](#transactional-replication-must-be-reconfigured-after-geo-failover)|2019 年 3 月|回避策なし| |
|[一時的なデータベースが RESTORE 操作中に使用される](#temporary-database-is-used-during-restore-operation)||回避策あり| |
|[TEMPDB の構造と内容は再作成される](#tempdb-structure-and-content-is-re-created)| |回避策なし| |
|[小さなデータベース ファイルによる記憶域の超過](#exceeding-storage-space-with-small-database-files)| |回避策あり| |
|[データベース名の代わりに GUID 値が表示される](#guid-values-shown-instead-of-database-names) ||回避策あり| |
|[エラー ログが非永続的である](#error-logs-arent-persisted)||回避策なし| |
|[CLR モジュールとリンク サーバーでローカル IP アドレスを参照できないことがある](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)| |回避策あり| |
|[同じインスタンス内にある 2 つのデータベース上でトランザクション スコープがサポートされない](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)| |解決済み|2020 年 3 月|
|Azure Blob Storage からデータベースを復元した後、DBCC CHECKDB を使用してデータベースの整合性が検証されません。| |解決済み|2019 年 11 月|
|ソース データベースにインメモリ OLTP オブジェクトが含まれている場合、Business Critical レベルから General Purpose レベルへの組み込みのポイントインタイム データベース復元は成功しません。| |解決済み|2019 年 10 月|
|セキュリティで保護された接続を使用する (Azure 以外の) 外部メール サーバーのデータベース メール機能| |解決済み|2019 年 10 月|
|包含データベースは、マネージド インスタンス内でサポートされています| |解決済み|2019 年 8 月|

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>CHECKSUM を使用せずに手動バックアップを復元すると失敗することがある

特定の状況で、CHECKSUM を使用せずにマネージド インスタンスで作成されたデータベースの手動バックアップが復元に失敗することがあります。 このような場合は、成功するまでバックアップの復元を再試行してください。

**回避策**:CHECKSUM が有効になっているマネージド インスタンス上のデータベースの手動バックアップを実行します。

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>既存のジョブを変更、無効化、または有効化するとエージェントが応答しなくなる

状況によっては、既存のジョブを変更したり、無効にしたり、有効にしたりすると、エージェントが応答しなくなることがあります。 この問題は、検出されるとエージェント プロセスが再起動され、自動的に軽減されます。

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>リソース グループに対するアクセス許可が Managed Instance に適用されない

リソース グループ (RG) に適用された Managed Instance 共同作成者 RBAC ロールが Managed Instance に適用されず、効果がありません。

**回避策**:ユーザーの Managed Instance 共同作成者ロールをサブスクリプション レベルで設定します。

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>ポータルを使用したフェールオーバー グループに対する手動フェールオーバーの制限

フェールオーバー グループが異なる複数の Azure サブスクリプションまたはリソース グループのインスタンスにまたがっている場合、フェールオーバー グループのプライマリ インスタンスから手動フェールオーバーを開始することはできません。

**回避策**:Geo セカンダリ インスタンスからポータル経由でフェールオーバーを開始します。

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL エージェント ロールには、sysadmin 以外のログインに対する明示的な EXECUTE 権限が必要です

sysadmin 以外のログインが [SQL Agent の固定データベース ロール](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles)に追加されると、これらのログインを機能させるには、明示的な EXECUTE 権限を Master ストアド プロシージャに付与する必要があるという問題が存在します。 この問題が発生した場合は、エラー メッセージ "EXECUTE 権限がオブジェクト <object_name> で拒否されました (Microsoft SQL Server、エラー:229)" が表示されます。

**回避策**:次の SQL エージェントの固定データベース ロール:SQLAgentUserRole、SQLAgentReaderRole、または SQLAgentOperatorRole のいずれかにログインを追加した後、これらのロールに追加された各ログインに対して次の T-SQL スクリプトを実行して、一覧表示されているストアド プロシージャに明示的に EXECUTE 権限を付与します。

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>エージェント プロセスを再起動すると、SQL エージェント ジョブが中断されることがある

**(2020 年 3 月に解決済み)** SQL Agent では、ジョブが開始されるたびに新しいセッションが作成され、メモリ消費量が徐々に増加します。 内部メモリの制限に達してスケジュールされたジョブの実行がブロックされることのないように、エージェント プロセスのメモリ使用量がしきい値に達すると、エージェント プロセスが再起動されます。 これにより、再起動の時点で実行中のジョブの実行が中断される可能性があります。

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>インメモリ OLTP のメモリ制限が適用されない

Business Critical サービス レベルでは、[メモリ最適化オブジェクトの最大メモリ制限](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space)が正しく適用されない場合があります。 マネージド インスタンスでは、ワークロードが、インメモリ OLTP 操作に対してより多くのメモリを使用できる場合があり、これがインスタンスの可用性と安定性に影響を及ぼすことがあります。 インメモリ OLTP クエリは、制限に達しても、すぐには失敗しない可能性があります。 この問題は、まもなく解決されます。 多くのインメモリ OLTP メモリを使用するクエリは、[制限](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space)に達するとすぐに失敗するようになります。

**対処法:** [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) を使用して[インメモリ OLTP ストレージの使用状況を監視](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring)し、使用可能な量を超えるメモリがワークロードによって使用されないようにします。 仮想コアの数に応じてメモリ制限を増やすか、ワークロードを最適化して、使用するメモリを減らします。

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>空ではないファイルを削除しようとしたときに誤ったエラーが返される

SQL Server/Managed Instance では、[ユーザーは空でないファイルを削除できません](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites)。 `ALTER DATABASE REMOVE FILE` ステートメントを使用して空でないデータ ファイルを削除しようとすると、エラー `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` はすぐには返されません。 Managed Instance では引き続きファイルの削除が試行されますが、30 分後に操作が失敗し、`Internal server error` になります。

**回避策**:`DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` コマンドを使用して、ファイルの内容を削除します。 ファイル グループ内の唯一のファイルの場合は、ファイルを圧縮する前に、このファイル グループに関連付けられているテーブルまたはパーティションからデータを削除する必要があります。また、必要に応じて、このデータを別のテーブルまたはパーティションに読み込みます。

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>サービス レベルの変更とインスタンスの作成操作が、進行中のデータベースの復元によってブロックされる

進行中の `RESTORE` ステートメント、データ移行サービスの移行プロセス、組み込みのポイントインタイム リストアでは、復元処理が完了するまで、サービス レベルの更新や既存のインスタンスのサイズ変更、新しいインスタンスの作成がブロックされます。 復元プロセスでは、復元プロセスが実行されているのと同じサブネット内のマネージド インスタンスとインスタンス プールでこれらの操作がブロックされます。 インスタンス プール内のインスタンスは影響を受けません。 サービス レベルの操作の作成または変更は、失敗したり、タイムアウトしたりすることはありません。復元処理が完了するかキャンセルされるまで処理が続行されます。

**回避策**:サービス レベルの作成または更新操作の優先順位が高い場合は、復元プロセスが完了するか、復元プロセスがキャンセルされるまで待機します。

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Business Critical サービス レベルの Resource Governor をフェールオーバー後に再構成しなければならない場合がある

ユーザー ワークロードに割り当てられているリソースを制限することを可能にする [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) 機能では、フェールオーバー後またはサービス レベルの変更 (たとえば、仮想コアやインスタンスの最大ストレージ サイズを変更します) をユーザーが開始した後、一部のユーザー ワークロードが間違って分類されることがあります。

**回避策**:[Resource Governor](/sql/relational-databases/resource-governor/resource-governor) を使用している場合、`ALTER RESOURCE GOVERNOR RECONFIGURE` を定期的に、または、インスタンスの開始時に SQL タスクを実行する SQL エージェント ジョブの一環として実行します。

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>サービス レベルのアップグレード後は、複数データベースにまたがる Service Broker のダイアログを再初期化する必要があります。

サービス レベルの変更操作の後、複数データベースにまたがる Service Broker のダイアログで、他のデータベースのサービスにメッセージが配信されなくなります。 メッセージは**失われたわけではなく**、センダーのキューに存在します。 マネージド インスタンスの仮想コアやインスタンスのストレージ サイズを変更すると、すべてのデータベースについて、[sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) ビューの `service_broke_guid` 値が変更されます。 [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) ステートメントを使用して作成された、他のデータベースの Service Broker を参照する `DIALOG` から、ターゲット サービスにメッセージが配信されなくなります。

**対処法:** 複数データベースにまたがる Service Broker ダイアログのメッセージ交換を使用するアクティビティはすべて、サービス レベルを更新する前に停止し、後から再初期化してください。 サービス レベルの変更後、未配信のままのメッセージがあった場合は、それらのメッセージをソース キューから読み取って、ターゲット キューに再送信します。

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>Azure AD ログイン タイプの偽装はサポートされない

`EXECUTE AS USER` または `EXECUTE AS LOGIN` を使用した次の AAD プリンシパルの偽装はサポートされません。
-    別名が付けられた AAD ユーザー。 この場合は `15517` エラーが返されます。
- AAD アプリケーションまたはサービス プリンシパルに基づく AAD のログインおよびユーザー。 この場合は `15517` および `15406` エラーが返されます。

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>sp_send_db_mail の @query パラメーターはサポートされない

[sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) プロシージャの `@query` パラメーターは機能しません。

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>geo フェールオーバー後、トランザクション レプリケーションを再構成する必要がある

自動フェールオーバー グループ内のデータベースでトランザクション レプリケーションが有効な場合、マネージド インスタンス管理者は、別のリージョンへのフェールオーバーが発生した後で、古いプライマリ上のすべてのパブリケーションをクリーンアップしてから、新しいプライマリ上でそれらを再構成する必要があります。 詳細については、「[レプリケーション](sql-database-managed-instance-transact-sql-information.md#replication)」を参照してください。

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>SSDT 内では AAD のログインとユーザーがサポートされない

SQL Server Data Tools では、Azure Active Directory のログインとユーザーが完全にはサポートされません。

### <a name="temporary-database-is-used-during-restore-operation"></a>一時的なデータベースが RESTORE 操作中に使用される

データベースがマネージド インスタンス上で復元されるとき、復元サービスではまず、所望の名前で空のデータベースが作成され、インスタンス上でその名前が割り当てられます。 しばらくすると、このデータベースは削除され、実際のデータベースの復元が開始されます。 *復元*状態のデータベースには、名前ではなくランダムな GUID 値が一時的に与えられます。 復元プロセスが完了すると、`RESTORE` ステートメントに指定されている所望の名前に一時的な名前が変更されます。 初期フェーズでは、ユーザーは空のデータベースにアクセスしたり、さらにはテーブルを作成したり、このデータベースにデータを読み込んだりできます。 この一時的なデータベースは、復元サービスで 2 つ目のフェーズが開始されると削除されます。

**回避策**:復元の完了を確認するまで、復元中のデータベースにはアクセスしないでください。

### <a name="tempdb-structure-and-content-is-re-created"></a>TEMPDB の構造と内容は再作成される

`tempdb` は常に 12 個のデータ ファイルに分割され、ファイルの構造は変更できません。 ファイルあたりの最大サイズは変更できず、`tempdb` に新しいファイルを追加することはできません。 `Tempdb` は、インスタンスが開始またはフェールオーバーしたときに、常に空のデータベースとして再作成され、`tempdb` で行われたどの変更も保持されません。

### <a name="exceeding-storage-space-with-small-database-files"></a>小さなデータベース ファイルによる記憶域の超過

インスタンスが Azure ストレージの制限に達する可能性があるため、`CREATE DATABASE`、`ALTER DATABASE ADD FILE`、および `RESTORE DATABASE` ステートメントが失敗することがあります。

各 General Purpose Managed Instance には、Azure Premium ディスク領域用に予約された 最大 35 TB のストレージがあります。 各データベース ファイルは、個別の物理ディスクに配置されます。 ディスク サイズとして、128 GB、256 GB、512 GB、1 TB、4 TB のいずれかを指定できます。 ディスク上の未使用領域については請求されませんが、Azure Premium ディスクのサイズ合計が 35 TB を超えることはできません。 場合によっては、内部の断片化のために、合計で 8 TB を必要としない Managed Instance が、記憶域のサイズに関する 35 TB の Azure での制限を超える場合があります。

たとえば、General Purpose マネージド インスタンスには、4 TB のディスクに配置されているサイズが 1.2 TB の大きなファイルが 1 つあります。 また、サイズがそれぞれ 1 GB のファイルが 248 個あり、これらは個別の 128 GB ディスクに配置される場合があります。 次の点に注意してください。

- 割り当てられるディスク ストレージの合計サイズは、1 x 4 TB + 248 x 128 GB = 35 TB となります。
- インスタンス上のデータベースの予約済み領域の合計は、1 x 1.2 TB + 248 x 1 GB = 1.4 TB となります。

この例は、特定の状況下では、特殊なファイルの配分のために、Managed Instance が、想定していなかったアタッチ済み Azure Premium ディスクに予約されている 35 TB の制限に到達する可能性があることを示しています。

この例では既存のデータベースは引き続き機能し、新しいファイルを追加しない限りは問題なく拡張できます。 すべてのデータベースの合計サイズがインスタンス サイズの上限に到達しない場合でも、新しいディスク ドライブ用の十分な領域がないため、新しいデータベースの作成や復元はできません。 その場合に返されるエラーは明確ではありません。

システム ビューを使用して、[残りのファイルの数を特定](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1)できます。 この制限に達した場合は、[DBCC SHRINKFILE ステートメントを使用して、より小さなファイルをいくつか空にして削除](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file)してみるか、[この制限のない Business Critical レベル](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)に切り替えてください。

### <a name="guid-values-shown-instead-of-database-names"></a>データベース名の代わりに GUID 値が表示される

複数のシステム ビュー、パフォーマンス カウンター、エラー メッセージ、XEvent、およびエラー ログ エントリで、実際のデータベース名ではなく GUID データベース識別子が表示されています。 将来、実際のデータベース名に置き換えられるため、これらの GUID 識別子には依存しないでください。

**回避策**:sys.databases ビューを使用して、実際のデータベース名を GUID データベース識別子の形式で指定した物理データベース名から解決します

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>エラー ログが非永続的である

マネージド インスタンスで利用可能なエラー ログは永続的ではなく、このログのサイズは、最大ストレージ上限には含まれません。 フェールオーバーが発生した場合、エラー ログは自動的に消去される可能性があります。 Managed Instance が複数の仮想マシンで複数回移動されたことが原因で、エラー ログの履歴に欠落部分が生じる可能性があります。

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

**回避策 (2020 年 3 月以降は不要):** 2 つの接続を使用する代わりに [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) を使って、接続コンテキスト内で他のデータベースを使用します。

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR モジュールとリンク サーバーでローカル IP アドレスを参照できないことがある

Managed Instance に配置された CLR モジュールと、現在のインスタンスを参照しているリンク サーバーまたは分散クエリでは、ローカル インスタンスの IP を解決できないことがあります。 このエラーは一時的な問題です。

**対処法:** 可能であれば、CLR モジュールでコンテキスト接続を使用します。

## <a name="updates"></a>更新プログラム

SQL Database の更新情報や機能強化の一覧については、[SQL Database サービスの更新情報](https://azure.microsoft.com/updates/?product=sql-database)を参照してください。

すべての Azure サービスの更新情報や機能強化については、[サービスの更新情報](https://azure.microsoft.com/updates)を参照してください。

## <a name="contribute-to-content"></a>コンテンツの改善への協力

Azure SQL Database のドキュメント コンテンツに協力するには、「[Microsoft Docs 共同作成者ガイド概要](https://docs.microsoft.com/contribute/)」を参照してください。
