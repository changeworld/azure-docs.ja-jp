---
title: SAP on Azure の監視データの参考情報
description: SAP on Azure の監視に必要な重要参考情報。
author: mamccrea
ms.topic: reference
ms.author: mamccrea
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.custom: subject-monitoring
ms.date: 08/27/2021
ms.openlocfilehash: 36d90dc91a37d3881f7fb74637b74759904bf3c7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060643"
---
# <a name="monitor-sap-on-azure-preview-data-reference"></a>Monitor SAP on Azure (プレビュー) の監視データの参考情報

この記事では、Azure Monitor for SAP Solutions のパフォーマンスと可用性を分析するために収集するログ データに関する参考情報を掲載しています。 SAP on Azure の監視データの収集と分析の詳細は、[Monitor SAP on Azure](monitor-sap-on-azure.md) に関する記事をご覧ください。

## <a name="metrics"></a>メトリック

Azure Monitor for SAP Solutions ではメトリクスをサポートしていません。

## <a name="azure-monitor-logs-tables"></a>Azure Monitor ログ テーブル

このセクションでは、Azure Monitor for SAP Solutions に関連し、Log Analytics でクエリを実行できる、すべての Azure Monitor Logs Kusto テーブルを挙げます。 Azure Monitor for SAP Solutions ではカスタム ログを使用します。 一部のテーブルのスキーマは、SAP などのサードパーティ プロバイダーにより設定されています。 ここに挙げるのは、Azure Monitor for SAP Solutions の現行のカスタムログです。詳しい情報のためにソースへのリンクも記載します。

### <a name="saphana_hostconfig_cl"></a>SapHana_HostConfig_CL

詳しくは「SAP HANA SQL and System Views Reference」(SAP HANA SQL とシステム ビューの参考情報) の「 [M_LANDSCAPE_HOST_CONFIGURATION System View](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.00/en-US/20b1d83875191014b028e076c874e9d7.html)」(M_LANDSCAPE_HOST_CONFIGURATION システム ビュー) をご覧ください。

### <a name="saphana_hostinformation_cl"></a>SapHana_HostInformation_CL

詳しくは「SAP HANA SQL and System Views Reference」(SAP HANA SQL とシステム ビューの参考情報) の「[M_HOST_INFORMATION System View](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20b10028751910148c1c9de602d771de.html)」(M_HOST_INFORMATION システム ビュー) をご覧ください。

### <a name="saphana_systemoverview_cl"></a>SapHana_SystemOverview_CL

詳しくは「SAP HANA SQL and System Views Reference」(SAP HANA SQL とシステム ビューの参考情報) の「[M_SYSTEM_OVERVIEW System View](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20c61f0675191014a1d5f96d9668855b.html)」(M_SYSTEM_OVERVIEW システム ビュー) をご覧ください。

### <a name="saphana_loadhistory_cl"></a>SapHana_LoadHistory_CL

詳しくは「SAP HANA SQL and System Views Reference」(SAP HANA SQL とシステム ビューの参考情報) の「[M_LOAD_HISTORY_HOST System View](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/3fa52abf1d854edbb7342a69364bcb0e.html)」(M_LOAD_HISTORY_HOST システム ビュー) をご覧ください。

### <a name="saphana_disks_cl"></a>SapHana_Disks_CL

詳しくは「SAP HANA SQL and System Views Reference」(SAP HANA SQL とシステム ビューの参考情報) の「[M_DISKS System View](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20aef7a275191014b37acbc35b4f20a4.html)」(M_DISKS システム ビュー) をご覧ください。

### <a name="saphana_systemavailability_cl"></a>SapHana_SystemAvailability_CL

詳しくは「SAP HANA SQL and System Views Reference」(SAP HANA SQL とシステム ビューの参考情報) の「[M_SYSTEM_AVAILABILITY System View](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.00/en-US/1ef9723a03214bd889c4fb8947765aa4.html)」(M_SYSTEM_AVAILABILITY システム ビュー) をご覧ください。

### <a name="saphana_backupcatalog_cl"></a>SapHana_BackupCatalog_CL

詳細については、次を参照してください。
- 「[M_BACKUP_CATALOG_FILES System View](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20a8437d7519101495a3fa7ad9961cf6.html?q=M_BACKUP_CATALOG)」(M_BACKUP_CATALOG_FILES システム ビュー)
- 「[M_BACKUP_CATALOG System View](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/1.0.12/en-US/20a8100e75191014870ecf908b5d2abf.html)」(M_BACKUP_CATALOG システム ビュー)

### <a name="saphana_systemreplication_cl"></a>SapHana_SystemReplication_CL

詳しくは「SAP HANA SQL and System Views Reference」(SAP HANA SQL とシステム ビューの参考情報) の「[M_SERVICE_REPLICATION System View](https://help.sap.com/viewer/c1d3f60099654ecfb3fe36ac93c121bb/2021_2_QRC/en-US/20c43fc975191014b0ece11b47a86c10.html)」(M_SERVICE_REPLICATION システム ビュー) をご覧ください。
 
### <a name="prometheus_osexporter_cl"></a>Prometheus_OSExporter_CL

詳しくは [GitHub の「prometheus / node_exporter」](https://github.com/prometheus/node_exporter)をご覧ください。

### <a name="prometheus_haclusterexporter_cl"></a>Prometheus_HaClusterExporter_CL

詳しくは｢[ClusterLabs/ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)」をご覧ください。

### <a name="mssql_dbconnections_cl"></a>MSSQL_DBConnections_CL

詳細については、次を参照してください。
- [sys.dm_exec_sessions (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) 
- [sys.databases (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql)

### <a name="mssql_systemprops_cl"></a>MSSQL_SystemProps_CL

詳細については、次を参照してください。 
- [sys.dm_os_windows_info (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-windows-info-transact-sql) 
- [sys.database_files (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)
- [sys.dm_exec_sql_text (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)
- [sys.dm_exec_query_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)
- [sys.dm_io_virtual_file_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)
- [sys.dm_db_partition_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql)
- [sys.dm_os_performance_counters (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)
- [sys.dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)
- [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)
- [SQL Server オペレーティングシステム関連の動的管理ビュー (Transact-sql)](/sql/relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql)
- [sys.availability_groups (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-availability-groups-transact-sql)
- [sys.dm_exec_requests (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)
- [sys.dm_xe_session_targets (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-xe-session-targets-transact-sql)
- [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)
- [backupset (Transact-SQL)](/sql/relational-databases/system-tables/backupset-transact-sql)
- [sys.sysのプロセス (Transact-sql)](/sql/relational-databases/system-compatibility-views/sys-sysprocesses-transact-sql)

### <a name="mssql_fileoverview_cl"></a>MSSQL_FileOverview_CL

詳しくは「[sys.database_files (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)」をご覧ください。

### <a name="mssql_memoryoverview_cl"></a>MSSQL_MemoryOverview_CL

詳しくは「[sys.dm_os_memory_clerks (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-clerks-transact-sql)」をご覧ください。

### <a name="mssql_top10statements_cl"></a>MSSQL_Top10Statements_CL

詳細については、次を参照してください。
- [sys.dm_exec_sql_text (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)
- [sys.dm_exec_query_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)

### <a name="mssql_ioperformance_cl"></a>MSSQL_IOPerformance_CL

詳しくは「[sys.dm_io_virtual_file_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)」をご覧ください。

### <a name="mssql_tablesizes_cl"></a>MSSQL_TableSizes_CL

詳しくは「[sys.dm_db_partition_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql)」をご覧ください。

### <a name="mssql_batchrequests_cl"></a>MSSQL_BatchRequests_CL

詳しくは「[sys.dm_os_performance_counters (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)」をご覧ください。

### <a name="mssql_waitpercs_cl"></a>MSSQL_WaitPercs_CL

詳しくは「[sys.dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)」をご覧ください。

### <a name="mssql_pagelifeexpectancy2_cl"></a>MSSQL_PageLifeExpectancy2_CL

詳しくは「[sys.dm_os_performance_counters (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)」をご覧ください。

### <a name="mssql_error_cl"></a>MSSQL_Error_CL

詳しくは「[sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)」をご覧ください。

### <a name="mssql_cpuusage_cl"></a>MSSQL_CPUUsage_CL

詳しくは「[SQL Server オペレーティングシステム関連の動的管理ビュー (Transact-sql)](/sql/relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql)」をご覧ください。

### <a name="mssql_aooverview_cl"></a>MSSQL_AOOverview_CL

詳しくは「[sys.availability_groups (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-availability-groups-transact-sql)」をご覧ください。

### <a name="mssql_aowaiter_cl"></a>MSSQL_AOWaiter_CL

詳しくは「[sys.dm_exec_requests (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)」をご覧ください。

### <a name="mssql_aowaitstats_cl"></a>MSSQL_AOWaitstats_CL

詳しくは「[sys.dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)」をご覧ください。

### <a name="mssql_aofailovers_cl"></a>MSSQL_AOFailovers_CL

詳細については、次を参照してください。
- [sys.dm_xe_session_targets (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-xe-session-targets-transact-sql)
- [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)

### <a name="mssql_bckbackups2_cl"></a>MSSQL_BckBackups2_CL

詳しくは「[backupset (Transact-SQL)](/sql/relational-databases/system-tables/backupset-transact-sql)」をご覧ください。

### <a name="mssql_blockingprocesses_cl"></a>MSSQL_BlockingProcesses_CL

詳しくは「[sys.sysのプロセス (Transact-sql)](/sql/relational-databases/system-compatibility-views/sys-sysprocesses-transact-sql)」をご覧ください。

## <a name="next-steps"></a>次のステップ

- Azure Monitor for SAP Solutions の詳しい使用方法は、[Monitor SAP on Azure](monitor-sap-on-azure.md) に関する記事をご覧ください。
- Azure Monitor の詳細は「[Azure Monitor を使用した Azure リソースの監視](../../../azure-monitor/essentials/monitor-azure-resource.md)」をご覧ください。
