---
title: SQL insights を使用して SQL デプロイを監視する (プレビュー)
description: Azure Monitor の SQL insights の概要
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2021
ms.openlocfilehash: 2b66fe7d268fbb2ad044d0ecc4a5bf0fff5cd17e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297793"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>SQL insights を使用して SQL デプロイを監視する (プレビュー)
SQL Insights は、[Azure SQL ファミリ](../../azure-sql/index.yml)のあらゆる製品を監視するための包括的なソリューションです。 SQL Insights では、[動的管理ビュー](../../azure-sql/database/monitoring-with-dmvs.md)を使用して、正常性の監視、問題の診断、パフォーマンスの調整に必要なデータを公開します。  

SQL Insights は、すべての監視をリモートで実行します。 専用の仮想マシン上の監視エージェントが、SQL リソースに接続し、リモートでデータを収集します。 収集されたデータは [Azure Monitor ログ](../logs/data-platform-logs.md)に格納され、簡単に集計、フィルター処理、傾向分析を行うことができます。 収集されたデータは、SQL Insights [ブック テンプレート](../visualize/workbooks-overview.md)から表示できます。また、[ログ クエリ](../logs/get-started-queries.md)を使用してデータを直接調査することもできます。

## <a name="pricing"></a>価格
SQL Insights には直接コストはかかりません。 すべてのコストは、データを収集する仮想マシン、データを格納する Log Analytics のワークスペース、およびデータに対して構成されたすべてのアラート ルールによって発生します。 

### <a name="virtual-machines"></a>仮想マシン

仮想マシンについては、[仮想マシンの価格ページ](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)で公開されている料金に基づいて課金されます。 必要な仮想マシンの数は、監視する接続文字列の数によって異なります。 100 接続文字列ごとに Standard_B2s サイズの仮想マシンを 1 つ割り当てることをお勧めします。 詳細については、「[Azure 仮想マシンの要件](sql-insights-enable.md#azure-virtual-machine-requirements)」を参照してください。

### <a name="log-analytics-workspaces"></a>Log Analytics ワークスペース

Log Analytics ワークスペースについては、[Azure Monitor の価格ページ](https://azure.microsoft.com/pricing/details/monitor/)で公開されている料金に基づいて課金されます。 SQL Insights で使用される Log Analytics のワークスペースには、データ インジェスト、データ保持、および (必要に応じて) データ エクスポートのコストが発生します。 

正確な料金は、取り込み、保持、およびエクスポートされたデータ量によって異なります。 このデータの量は、データベース アクティビティと、[監視プロファイル](sql-insights-enable.md#create-sql-monitoring-profile)で定義されているコレクションの設定によって変化します。

### <a name="alert-rules"></a>アラート ルール

Azure Monitor のアラート ルールについては、[Azure Monitor の価格ページ](https://azure.microsoft.com/pricing/details/monitor/)で公開されている料金に基づいて課金されます。 [SQL Insights でアラートを作成](sql-insights-alerts.md)することを選択した場合は、作成されたアラート ルールと送信された通知に対して課金されます。

## <a name="supported-versions"></a>サポートされているバージョン 
SQL insights では、次のバージョンの SQL Server がサポートされます。
- SQL Server 2012 以降

SQL insights では、次の環境で実行されている SQL Server がサポートされます。
- Azure SQL Database
- Azure SQL Managed Instance
- Azure Virtual Machines の SQL Server ([SQL 仮想マシン](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) プロバイダーに登録されている仮想マシンで実行されている SQL Server)
- Azure VM ([SQL 仮想マシン](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) プロバイダーに登録されていない仮想マシンで実行されている SQL Server)

SQL insights では、次のサポートがないか、サポートが制限されています。
- **Azure 以外のインスタンス**: Azure の外部の仮想マシンで実行されている SQL Server はサポートされていません。
- **エラスティック プールの Azure SQL Database**: エラスティック プールまたはエラスティック プール内のデータベースのメトリックを収集することはできません。
- **Azure SQL Database の低サービス レベル**: Basic、S0、S1、S2 の各 [サービス レベル](../../azure-sql/database/resource-limits-dtu-single-databases.md)のデータベースのメトリックは収集できません。
- **Azure SQL Database サーバーレス サービス レベル**: サーバーレス コンピューティング サービス レベルを使用するデータベースのメトリックは収集できます。 ただし、メトリックを収集するプロセスによって自動一時停止の延期期間タイマーがリセットされ、データベースが自動一時停止状態に入れなくなります。
- **セカンダリ レプリカ**: メトリックは、データベースごとに 1 つのセカンダリ レプリカに対してのみ収集できます。 データベースに複数のセカンダリ レプリカがある場合、監視できるのは 1 つだけです。
- **Azure Active Directory による認証**: 監視に対してサポートされている [認証](../../azure-sql/database/logins-create-manage.md#authentication-and-authorization)方法は SQL 認証のみです。 Azure 仮想マシンの SQL Server の場合、カスタム ドメイン コントローラー上の Active Directory を使用した認証はサポートされていません。  

## <a name="regional-availability"></a>リージョン別の提供状況

SQL Insights は、Azure Government と各国のクラウドを除き、Azure Monitor が[利用可能](https://azure.microsoft.com/global-infrastructure/services/?products=monitor)なすべての Azure リージョンで使用できます。

## <a name="opening-sql-insights"></a>SQL insights を開く

SQL insights を開くには:

1. Azure portal で、 **[Azure Monitor]** メニューに移動します。
1. **[分析情報]** セクションで **[SQL (プレビュー)]** を選択します。 
1. タイルを選択すると、監視している SQL リソースのエクスペリエンスが読み込まれます。

:::image type="content" source="media/sql-insights/portal.png" alt-text="Azure portal に SQL 洞察を示すスクリーンショット。":::

詳細については、「[SQL insights を有効にする](sql-insights-enable.md)」と「[SQL Insights のトラブルシューティング](sql-insights-troubleshoot.md)」をご覧ください。

## <a name="collected-data"></a>収集されるデータ
SQL Insights は、すべての監視をリモートで実行します。 SQL Server を実行している仮想マシンにはエージェントがインストールされていません。 

SQL Insights では、専用の監視仮想マシンを使用して、SQL リソースからデータをリモートで収集します。 各監視仮想マシンには、[Azure Monitor エージェント](../agents/azure-monitor-agent-overview.md)と、ワークロードの分析情報 (WLI) 拡張機能がインストールされます。 

WLI 拡張機能には、オープンソースの [Telegraf エージェント](https://www.influxdata.com/time-series-platform/telegraf/)が含まれています。 SQL Insights では、[データ収集ルール](../agents/data-collection-rule-overview.md)を使用して、Telegraf の [SQL Server プラグイン](https://www.influxdata.com/integration/microsoft-sql-server/)のデータ収集設定を指定します。

Azure SQL Database、Azure SQL Managed Instance、および SQL Server 用の、さまざまなデータ セットを使用できます。 使用できるデータを次の表に示します。 [監視プロファイルを作成](sql-insights-enable.md#create-sql-monitoring-profile)するときに、収集するデータ セットと収集の頻度をカスタマイズできます。

表には、次の列があります。
- **フレンドリ名**: 監視プロファイルを作成するときに Azure portal に示されるクエリの名前。
- **構成名**: 監視プロファイルを編集するときに Azure portal に示されるクエリの名前。
- **名前空間**: Log Analytics ワークスペースで検出されたクエリの名前。 この識別子は、**InsighstMetrics** テーブルの `Tags` 列の `Namespace` プロパティに表示されます。
- **DMV**: データ セットを生成するために使用される動的管理ビュー
- **既定で有効**: データが既定で収集されるかどうか
- **既定の収集頻度**: データが既定で収集される頻度

### <a name="data-for-azure-sql-database"></a>Azure SQL Database のデータ 
| フレンドリ名 | 構成名 | 名前空間 | DMV | 既定で有効 | 既定の収集の頻度 |
|:---|:---|:---|:---|:---|:---|
| DB wait stats (DB 待機統計) | AzureSQLDBWaitStats | sqlserver_azuredb_waitstats | sys.dm_db_wait_stats | いいえ | 適用なし |
| DBO wait stats (DBO 待機統計) | AzureSQLDBOsWaitstats | sqlserver_waitstats |sys.dm_os_wait_stats | はい | 60 秒 |
| メモリ クラーク | AzureSQLDBMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | はい | 60 秒 |
| [データベース I/O] | AzureSQLDBDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys.database_files | はい | 60 秒 |
| サーバー プロパティ | AzureSQLDBServerProperties | sqlserver_server_properties | sys.dm_os_job_object<br>sys.database_files<br>sys.[databases]<br>sys.[database_service_objectives] | はい | 60 秒 |
| パフォーマンス カウンター | AzureSQLDBPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases | はい | 60 秒 |
| Resource stats (リソース統計) | AzureSQLDBResourceStats | sqlserver_azure_db_resource_stats | sys.dm_db_resource_stats | はい | 60 秒 |
| リソース管理 | AzureSQLDBResourceGovernance | sqlserver_db_resource_governance | sys.dm_user_db_resource_governance | はい | 60 秒 |
| Requests | AzureSQLDBRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | いいえ | 適用なし |
| スケジューラ| AzureSQLDBSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | いいえ | 適用なし  |

### <a name="data-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance のデータ 

| フレンドリ名 | 構成名 | 名前空間 | DMV | 既定で有効 | 既定の収集の頻度 |
|:---|:---|:---|:---|:---|:---|
| 待機統計 | AzureSQLMIOsWaitstats | sqlserver_waitstats | sys.dm_os_wait_stats | はい | 60 秒 |
| メモリ クラーク | AzureSQLMIMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | はい | 60 秒 |
| [データベース I/O] | AzureSQLMIDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | はい | 60 秒 |
| サーバー プロパティ | AzureSQLMIServerProperties | sqlserver_server_properties | sys.server_resource_stats | はい | 60 秒 |
| パフォーマンス カウンター | AzureSQLMIPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases| はい | 60 秒 |
| Resource stats (リソース統計) | AzureSQLMIResourceStats | sqlserver_azure_db_resource_stats | sys.server_resource_stats | はい | 60 秒 |
| リソース管理 | AzureSQLMIResourceGovernance | sqlserver_instance_resource_governance | sys.dm_instance_resource_governance | はい | 60 秒 |
| Requests | AzureSQLMIRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | いいえ | NA |
| スケジューラ | AzureSQLMISchedulers | sqlserver_schedulers | sys.dm_os_schedulers | いいえ | 適用なし |

### <a name="data-for-sql-server"></a>SQL Server のデータ

| フレンドリ名 | 構成名 | 名前空間 | DMV | 既定で有効 | 既定の収集の頻度 |
|:---|:---|:---|:---|:---|:---|
| 待機統計 | SQLServerWaitStatsCategorized | sqlserver_waitstats | sys.dm_os_wait_stats | はい | 60 秒 | 
| メモリ クラーク | SQLServerMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | はい | 60 秒 |
| [データベース I/O] | SQLServerDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | はい | 60 秒 |
| サーバー プロパティ | SQLServerProperties | sqlserver_server_properties | sys.dm_os_sys_info | はい | 60 秒 |
| パフォーマンス カウンター | SQLServerPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters | はい | 60 秒 |
| ボリューム領域 | SQLServerVolumeSpace | sqlserver_volume_space | sys.master_files | はい | 60 秒 |
| SQL Server CPU | SQLServerCpu | sqlserver_cpu | sys.dm_os_ring_buffers | はい | 60 秒 |
| スケジューラ | SQLServerSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | いいえ | 適用なし |
| Requests | SQLServerRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | いいえ | 適用なし |
| 可用性レプリカの状態 | SQLServerAvailabilityReplicaStates | sqlserver_hadr_replica_states | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | いいえ | 60 秒 |
| 可用性データベース レプリカ | SQLServerDatabaseReplicaStates | sqlserver_hadr_dbreplica_states | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | いいえ | 60 秒 |

## <a name="next-steps"></a>次のステップ

- SQL Insights に関してよく寄せられる質問については、「[よく寄せられる質問](../faq.yml)」を参照してください。
