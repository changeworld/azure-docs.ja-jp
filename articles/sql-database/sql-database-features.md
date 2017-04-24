---
title: "Azure SQL Database の機能の概要 | Microsoft Docs"
description: "このページには、Azure SQL Database の論理サーバーとデータベースについての概要が記載され、各機能へのリンク付きの機能のサポート マトリックスが含まれています。"
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: features
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/03/2017
ms.author: carlrab; jognanay
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: a1ede93b5aacf0d8a5bcf83f208f72be328ee72f
ms.lasthandoff: 04/15/2017


---
# <a name="azure-sql-database-features"></a>Azure SQL Database の機能

次の表は、Azure SQL Database の主な機能と、同等の SQL Server の機能の一覧です。各機能がサポートされているかどうかについての情報と、各プラットフォームでの機能についての詳細情報へのリンクも示されています。 既存のデータベース ソリューションを移行するときに考慮する必要がある Transact-SQL の相違点については、「[SQL Database への移行時に Transact-SQL の相違点を解決する](sql-database-transact-sql-information.md)」を参照してください。

Azure SQL Database には継続的に機能が追加されます。 したがって、Azure のサービス更新情報に関する Web ページにアクセスし、次のフィルターを使用することをお勧めします。

* サービスを [[SQL Database]](https://azure.microsoft.com/updates/?service=sql-database)でフィルター処理します。
* SQL Database の機能について、[General Availability] [(GA) のアナウンス](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) でフィルター処理します。

| **機能** | **SQL Server** | **Azure SQL Database** | 
| --- | :---: | :---: | 
| アクティブ geo レプリケーションを選択するとき | サポートされていません - [AlwaysOn 可用性グループ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)に関する記事を参照してください | [サポートされています](sql-database-geo-replication-overview.md)
| Always Encrypted | [サポートされています](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | サポートされています - [証明書ストア](sql-database-always-encrypted.md)と [Key Vault](sql-database-always-encrypted-azure-key-vault.md) に関する記事を参照してください|
| AlwaysOn 可用性グループ | [サポートされています](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | サポートされていません - [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください |
| データベースの接続 | [サポートされています](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | サポートされていません |
| アプリケーション ロール | [サポートされています](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | [サポートされています](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) |
| 自動スケール | サポートされていません | サポートされています - [サービス レベル](sql-database-service-tiers.md)に関する記事を参照してください |
| Azure Active Directory | サポートされていません | [サポートされています](sql-database-aad-authentication.md) |
| Azure Data Factory | [サポートされています](../data-factory/data-factory-introduction.md) | [サポートされています](../data-factory/data-factory-introduction.md) |
| 監査 | [サポートされています](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [サポートされています](sql-database-auditing.md) |
| BACPAC ファイル (エクスポート) | [サポートされています](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | [サポートされています](sql-database-export.md) |
| BACPAC ファイル (インポート) | [サポートされています](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | [サポートされています](sql-database-import.md) |
| BACKUP | [サポートされています](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | サポートされていません |
| 組み込み関数 | [サポートされています](https://docs.microsoft.com/sql/t-sql/functions/functions) | ほとんどの場合 - [個々の関数] (https://docs.microsoft.com/sql/t-sql/functions/functions) に関する記事を参照してください |
| 変更データのキャプチャ | [サポートされています](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | サポートされていません |
| 変更の追跡 | [サポートされています](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | [サポートされています](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) |
| 照合順序ステートメント | [サポートされています](https://docs.microsoft.com/sql/t-sql/statements/collations) | [サポートされています](https://docs.microsoft.com/sql/t-sql/statements/collations) |
| 列ストア インデックス | [サポートされています](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | [Premium Edition のみ](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| 共通言語ランタイム (CLR) | [サポートされています](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | サポートされていません |
| 包含データベース | [サポートされています](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | [サポートされています](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) |
| 包含ユーザー | [サポートされています](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | [サポートされています](sql-database-manage-logins.md#non-administrator-users) |
| フロー制御言語のキーワード | [サポートされています](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | [サポートされています](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) |
| 複数データベース間のクエリ | [サポートされています](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | 部分的 - [エラスティック クエリ](sql-database-elastic-query-overview.md)に関する記事を参照してください |
| カーソル | [サポートされています](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | [サポートされています](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | 
| データ圧縮 | [サポートされています](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | [サポートされています](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) |
| データベースのバックアップ | [ユーザー管理](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) | [SQL Database サービスによる管理](sql-database-automated-backups.md) |
| データベース メール | [サポートされています](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | サポートされていません |
| データベース ミラーリング | [サポートされています](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | サポートされていません |
| データベースの構成設定 | [サポートされています](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | [サポートされています](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) |
| Data Quality Services (DQS) | [サポートされています](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | サポートされていません |
| データベース スナップショット | [サポートされています](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | サポートされていません |
| データの種類 | [サポートされています](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | [サポートされています](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) |  
| DBCC ステートメント | [サポートされています](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | ほとんどの場合 - [個々のステートメント](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql)に関する記事を参照してください |
| DDL ステートメント | [サポートされています](https://docs.microsoft.com/sql/t-sql/statements/statements) | ほとんどの場合 - [個々のステートメント](https://docs.microsoft.com/sql/t-sql/statements/statements)に関する記事を参照してください
| DDL トリガー | [サポートされています](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | [データベースのみ](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) |
| 分散トランザクション | [MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | 限定された SQL Database 内のシナリオのみ |
| DML ステートメント | [サポートされています](https://docs.microsoft.com/sql/t-sql/queries/queries) | ほとんどの場合 - [個々のステートメント]](https://docs.microsoft.com/sql/t-sql/queries/queries) に関する記事を参照してください |
| DML トリガー | [サポートされています](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) | [サポートされています](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) |
| DMV | [すべて](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | 一部 - [個々の DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) に関する記事を参照してください |
| エラスティック プール | サポートされていません | [サポートされています](sql-database-elastic-pool.md) |
| エラスティック ジョブ | サポートされていません - 「[SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent)」を参照してください | [サポートされています](sql-database-elastic-jobs-getting-started.md) | 
| エラスティック クエリ | サポートされていません - 「[複数データベースにまたがるクエリ](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries)」を参照してください | [サポートされています](sql-database-elastic-query-overview.md) |
| イベント通知 | [サポートされています](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | [サポートされています](sql-database-insights-alerts-portal.md) |
| 式 | [サポートされています](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) | [サポートされています](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |
| 拡張イベント | [サポートされています](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | 一部 - [個々のイベント](sql-database-xevent-db-diff-from-svr.md)に関する記事を参照してください |
| 拡張ストアド プロシージャ | [サポートされています](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | サポートされていません |
| ファイルおよびファイル グループ | [サポートされています](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | [プライマリのみ](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) |
| Filestream | [サポートされています](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | サポートされていません |
| フルテキスト検索 | [サポートされています](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | [サード パーティのワード ブレーカーはサポートされていません](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |
| 関数 | [サポートされています](https://docs.microsoft.com/sql/t-sql/functions/functions) | ほとんどの場合 - [個々の関数](https://docs.microsoft.com/sql/t-sql/functions/functions)に関する記事を参照してください |
| メモリ内の最適化 | [サポートされています](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | [Premium Edition のみ](sql-database-in-memory.md) |
| [ジョブ] | [SQL Server エージェント](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent)に関する記事を参照してください | [エラスティック ジョブ](sql-database-elastic-jobs-getting-started.md)に関する記事を参照してください |
| JSON データのサポート | [サポートされています](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [サポートされています](sql-database-json-features.md) |
| 言語要素 | [サポートされています](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | ほとんどの場合 - [個々の要素](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql)に関する記事を参照してください |  
| リンク サーバー | [サポートされています](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | サポートされていません - [エラスティック クエリ](sql-database-elastic-query-horizontal-partitioning.md)に関する記事を参照してください |
| ログ配布 | [サポートされています](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | サポートされていません - [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください |
| Master Data Services (MDS) | [サポートされています](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | サポートされていません |
| 一括インポートでの最小ログ記録 | [サポートされています](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | サポートされていません |
| システム データの変更 | [サポートされています](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | サポートされていません |
| オンライン インデックス操作 | [サポートされています](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | [サポートされています - トランザクションのサイズがサービス レベルによって制限されます](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) |
| 演算子 | [サポートされています](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | ほとんどの場合 - [個々の演算子](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql)に関する記事を参照してください |
| データベースのポイントインタイム リストア | [サポートされています](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | [サポートされています](sql-database-recovery-using-backups.md#point-in-time-restore) |
| PolyBase | [サポートされています](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | サポートされていません
| ポリシー ベースの管理 | [サポートされています](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | サポートされていません |
| 述語 | [サポートされています](https://docs.microsoft.com/sql/t-sql/queries/predicates) | ほとんどの場合 - [個々の述語](https://docs.microsoft.com/sql/t-sql/queries/predicates)に関する記事を参照してください
| R Services | [サポートされています](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services)
| リソース ガバナー | [サポートされています](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | サポートされていません |
| RESTORE ステートメント | [サポートされています](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | サポートされていません | 
| バックアップからデータベースを復元する | [サポートされています](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | [ビルトイン バックアップからのみ](sql-database-recovery-using-backups.md) |
| 行レベルのセキュリティ | [サポートされています](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | [サポートされています](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) |
| セマンティック検索 | [サポートされています](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | サポートされていません |
| シーケンス番号 | [サポートされています](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | [サポートされています](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) |
| Service Broker | [サポートされています](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | サポートされていません |
| サーバーの構成設定 | [サポートされています](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | サポートされていません - [データベース構成オプション](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)に関する記事を参照してください |
| SET ステートメント | [サポートされています](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | ほとんどの場合 - [個々のステートメント](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql)に関する記事を参照してください 
| Spatial | [サポートされています](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | [サポートされています](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) |
| SQL Server エージェント | [サポートされています](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | サポートされていません - [エラスティック ジョブ](sql-database-elastic-jobs-getting-started.md)に関する記事を参照してください |
| SQL Server Analysis Services (SSAS) | [サポートされています](https://docs.microsoft.com/sql/analysis-services/analysis-services) | サポートされていません - 「[Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/)」を参照してください |
| SQL Server Integration Services (SSIS) | [サポートされています](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | サポートされていません - [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) に関するページを参照してください |
| SQL Server PowerShell | [サポートされています](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | [サポートされています](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) |
| SQL Server プロファイラー | [サポートされています](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | サポートされていません - [拡張イベント](sql-database-xevent-db-diff-from-svr.md)に関する記事を参照してください |
| SQL Server レプリケーション | [サポートされています](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [トランザクション レプリケーションとスナップショット レプリケーションのサブスクライバーのみ](sql-database-cloud-migrate.md) |
| SQL Server Reporting Services (SSRS) | [サポートされています](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | サポートされていません |
| ストアド プロシージャ | [サポートされています](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | [サポートされています](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) |
| システム ストアド関数 | [サポートされています](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | 一部 - [個々の関数](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql)に関する記事を参照してください |
| システム ストアド プロシージャ | [サポートされています](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 一部 - [個々のストアド プロシージャ](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql)に関する記事を参照してください |
| システム テーブル | [サポートされています](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | 一部 - [個々のテーブル](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql)に関する記事を参照してください |
| システム カタログ ビュー | [サポートされています](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 一部 - [個々のビュー](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql)に関する記事を参照してください
| テーブル パーティション | [サポートされています](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | サポートされています - [プライマリ ファイル グループのみ](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) |
| 一時テーブル | [ローカルおよびグローバル](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | [ローカルのみ](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) |
| テンポラル テーブル | [サポートされています](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [サポートされています](sql-database-temporal-tables.md) |
| トランザクション | [サポートされています](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) | [サポートされています](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) |
| variables | [サポートされています](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | [サポートされています](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | 
| Transparent Data Encryption (TDE)  | [サポートされています](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | [サポートされています](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Windows Server フェールオーバー クラスタリング | [サポートされています](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | サポートされていません - [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください |
| XML インデックス | [サポートされています](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | [サポートされています](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) |

## <a name="next-steps"></a>次のステップ

- Azure SQL Database のサービスについては、「[SQL Database とは](sql-database-technical-overview.md)」を参照してください。
- Transact-SQL のサポートと違いについては、「[SQL Database への移行時に Transact-SQL の相違点を解決する](sql-database-transact-sql-information.md)」を参照してください。

