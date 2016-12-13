# Overview
## [SQL Database とは](sql-database-technical-overview.md)
### [サービス階層](sql-database-service-tiers.md)
### [データベース トランザクション ユニット](sql-database-what-is-a-dtu.md)
### [DTU ベンチマークの概要](sql-database-benchmark-overview.md)
### [リソース制限](sql-database-resource-limits.md)
### [機能](sql-database-features.md)
### [SQL Database に関する FAQ](sql-database-faq.md)
## メリット
### [学習と適応](sql-database-learn-and-adapt.md)
### [すばやいスケール](sql-database-scale-on-the-fly.md)
### [マルチテナント アプリの構築](sql-database-build-multi-tenant-apps.md)
### [セキュリティと保護](sql-database-helps-secures-and-protects.md)
### [お客様の環境に対応](sql-database-works-in-your-environment.md)
## 比較
### [VM 上の SQL Database と SQL](sql-database-paas-vs-sql-server-iaas.md)
### [T-SQL の相違点](sql-database-transact-sql-information.md)
### [SQL と NoSQL](../documentdb/documentdb-nosql-vs-sql.md)
## [SQL Database ツール](sql-database-manage-overview.md)
## [SQL Database のチュートリアル](sql-database-explore-tutorials.md)
## [ソリューション クイックスタート](sql-database-solution-quick-starts.md)
## セキュリティ
### [Azure SQL Database の Azure Security Center](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
### [SQL セキュリティ センター](https://msdn.microsoft.com/library/azure/bb510589)

# 作業の開始
## データベースとサーバー
### Single Database
#### 詳細情報
##### [Azure Portal のチュートリアル: Azure Portal を使用して Azure SQL Database を作成する](sql-database-get-started.md)
#### すべきこと
##### [Azure Portal のチュートリアル: Azure Portal を使用して Azure SQL Database を作成する](sql-database-get-started.md)
##### [Azure Portal のチュートリアル: PowerShell を使用して Azure SQL Database を作成する](sql-database-get-started-powershell.md)
##### [Azure Portal のチュートリアル: C# を使用して Azure SQL Database を作成する](sql-database-get-started-csharp.md)
### エラスティック データベース プール
#### 詳細情報
##### [エラスティック データベース プール](sql-database-elastic-pool.md)
##### [エラスティック データベース プールを使用する場合](sql-database-elastic-pool-guidance.md)
##### [エラスティック プールの価格](sql-database-elastic-pool-price.md)
#### すべきこと
##### [Azure Portal を使用した作成](sql-database-elastic-pool-create-portal.md)
##### [PowerShell を使用した作成](sql-database-elastic-pool-create-powershell.md)
##### [C# を使用した作成](sql-database-elastic-pool-create-csharp.md)
### スケールアウト
#### 詳細情報
##### [スケールアウト](sql-database-elastic-scale-introduction.md)
##### [エラスティック スケール](sql-database-elastic-scale-get-started.md)
##### [スケーラブルなクラウド データベースの構築](sql-database-elastic-database-client-library.md)
##### [複数データベース間のジョブ](sql-database-elastic-jobs-overview.md)
##### [エラスティック ツールに関する FAQ](sql-database-elastic-scale-faq.md)
##### [Elastic Database ツールの用語集](sql-database-elastic-scale-glossary.md)
##### [スケールアウトされたクラウド データベース間のデータ移動](sql-database-elastic-scale-overview-split-and-merge.md)
#### すべきこと
##### [エラスティック ジョブ](sql-database-elastic-jobs-getting-started.md)
##### [複数データベース間のレポート](sql-database-elastic-query-getting-started.md)
##### [複数データベース間のクエリ](sql-database-elastic-query-getting-started-vertical.md)
##### [エラスティック ジョブのアンインストール](sql-database-elastic-jobs-uninstall.md)
## データの移行と移動
### 詳細情報
#### [データベースを移行する](sql-database-cloud-migrate.md)
#### [トランザクション レプリケーション](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
#### [データ同期](sql-database-get-started-sql-data-sync.md)
#### [SQL Database をコピーする](sql-database-copy.md)
### すべきこと
#### データベースの互換性を確認する
##### [SQL パッケージ ユーティリティ](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
#### データベースの互換性の問題を修正する
##### [SQL Server Data Tools](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
##### [SQL Azure 移行ウィザード](sql-database-cloud-migrate-fix-compatibility-issues.md)
##### [SQL Server Management Studio 移行ウィザード](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
#### データベースをコピーする
##### [Azure Portal を使用したコピー](sql-database-copy-portal.md)
##### [PowerShell を使用したコピー](sql-database-copy-powershell.md)
##### [T-SQL を使用したコピー](sql-database-copy-transact-sql.md)
#### データベースを BACPAC ファイルにエクスポートする
##### [Azure Portal](sql-database-export.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
##### [SQL パッケージ ユーティリティ](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
##### [PowerShell](sql-database-export-powershell.md)
#### データベースを BACPAC ファイルからインポートする
##### [Azure Portal](sql-database-import.md)
##### [PowerShell](sql-database-import-powershell.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
##### [SQL パッケージ ユーティリティ](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
#### [BCP を使用して CSV ファイルから読み込む](sql-database-load-from-csv-with-bcp.md)
## 認証してアクセス権を付与する
### 詳細情報
#### [概要](sql-database-security.md)
#### [セキュリティ ガイドライン](sql-database-security-guidelines.md)
#### [ログインの管理](sql-database-manage-logins.md)
### すべきこと
### [チュートリアル: SQL 認証とアクセス](sql-database-get-started-security.md)
## データの安全性を確保して保護する
### 詳細情報
#### 監査
##### [監査](sql-database-auditing-get-started.md)
##### [監査のためのダウンレベル クライアントのサポートと IP エンドポイントの変更](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [脅威の検出](sql-database-threat-detection-get-started.md)
#### データを暗号化する
##### [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)
##### [Windows 証明書ストア](sql-database-always-encrypted.md)
##### [透過的なデータ暗号化](https://msdn.microsoft.com/library/azure/dn948096)
##### [列の暗号化](https://msdn.microsoft.com/library/azure/ms179331)
#### データをマスクする
##### 動的データ マスク
###### [Azure Portal](sql-database-dynamic-data-masking-get-started.md)
### すべきこと
#### [Azure Portal を使用した動的データ マスク](sql-database-dynamic-data-masking-get-started.md)
## ビジネス継続性
### 詳細情報
#### [概要](sql-database-business-continuity.md)
#### [データベースのバックアップ](sql-database-automated-backups.md)
#### [長期保存](sql-database-long-term-retention.md)
#### [バックアップを使用したデータベースの復元](sql-database-recovery-using-backups.md)
#### [障害復旧の認証要件](sql-database-geo-replication-security-config.md)
#### [ビジネス継続性の設計シナリオ](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [エラスティック プールを使用した障害復旧戦略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [ローリング アップグレード](sql-database-manage-application-rolling-upgrade.md)
### すべきこと
#### 削除済みデータベースの復元
##### [Azure Portal](sql-database-restore-deleted-database-portal.md)
##### [PowerShell](sql-database-restore-deleted-database-powershell.md)
#### ポイントインタイム リストア
##### [Azure Portal](sql-database-point-in-time-restore-portal.md)
##### [PowerShell](sql-database-point-in-time-restore-powershell.md)
#### データベースの geo リストア
##### [Azure Portal](sql-database-geo-restore-portal.md)
##### [PowerShell](sql-database-geo-restore-powershell.md)
#### [1 つのテーブル](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
#### [データ センターの停止から回復する](sql-database-disaster-recovery.md)
#### [障害復旧訓練を実施する](sql-database-disaster-recovery-drills.md)
### データベースのレプリケート
#### [アクティブ geo レプリケーションの概要](sql-database-geo-replication-overview.md)
#### アクティブ geo レプリケーションの構成
##### [Azure Portal](sql-database-geo-replication-portal.md)
##### [PowerShell](sql-database-geo-replication-powershell.md)
##### [T-SQL](sql-database-geo-replication-transact-sql.md)
#### アクティブ geo レプリケーションを使用したフェールオーバー
##### [Azure Portal](sql-database-geo-replication-failover-portal.md)
##### [PowerShell](sql-database-geo-replication-failover-powershell.md)
##### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)
## アプリケーション開発
### 詳細情報
#### [データベース アプリケーションの開発の概要](sql-database-develop-overview.md)
#### [接続ライブラリ](sql-database-libraries.md)
#### [マルチテナント SaaS アプリケーション](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [ADO.NET 4.5 で 1433 以外のポートを使用する](sql-database-develop-direct-route-ports-adonet-v12.md)
#### [アプリケーションの認証に必要な値を取得する](sql-database-client-id-keys.md)
### すべきこと
#### アプリケーションを接続する
##### [.NET](sql-database-develop-dotnet-simple.md)
##### [C と C++](sql-database-develop-cplusplus-simple.md)
##### [Java](sql-database-develop-java-simple.md)
##### [Node.JS](sql-database-develop-nodejs-simple.md)
##### [PHP](sql-database-develop-php-simple.md)
##### [Python](sql-database-develop-python-simple.md)
##### [Ruby](sql-database-develop-ruby-simple.md)
##### [Excel](sql-database-connect-excel.md)
#### [Visual Studio に接続する](sql-database-connect-query.md)
#### [クライアント アプリケーションを構築する](https://www.microsoft.com/sql-server/developer-get-started)
#### [エラー メッセージを処理する](sql-database-develop-error-messages.md)
### サービスとしてのソフトウェアの顧客実装
#### [Daxko/CSI ソフトウェア](sql-database-implementation-daxko.md)
#### [GEP](sql-database-implementation-gep.md)
#### [SnelStart](sql-database-implementation-snelstart.md)
#### [Umbraco](sql-database-implementation-umbraco.md)
## データベース開発
### 詳細情報
#### テンポラル テーブル
##### [テンポラル テーブル](sql-database-temporal-tables.md)
##### [リテンション期間ポリシー](sql-database-temporal-tables-retention-policy.md)
#### [JSON データ](sql-database-json-features.md)
#### [メモリ内](sql-database-in-memory.md)
### すべきこと
#### [SQL Server の開発](https://msdn.microsoft.com/library/ms179422.aspx)
## 監視とチューニング
### 詳細情報
#### [単一データベース](sql-database-single-database-monitor.md)
#### [SQL Database Advisor の概要](sql-database-advisor.md)
#### [Single Database のガイダンス](sql-database-performance-guidance.md)
#### [Azure Portal でのワークロードの洞察](sql-database-performance.md)
#### [バッチ処理を使用する](sql-database-use-batching-to-improve-performance.md)
## SQL Database V11
### [Web および Business Edition の終了](sql-database-web-business-sunset-faq.md)
### [Service Tier Advisor](sql-database-service-tier-advisor.md)
### [エラスティック プール評価ツール](sql-database-elastic-pool-database-assessment-powershell.md)
### [V12 へアップグレード](sql-database-v12-plan-prepare-upgrade.md)
#### [Azure Portal を使用したアップグレード](sql-database-upgrade-server-portal.md)
#### [PowerShell を使用したアップグレード](sql-database-upgrade-server-powershell.md)
#### [価格レベルの推奨](sql-database-service-tier-advisor.md)

# 方法
## 作成と管理
### サーバーとデータベース
#### [単一データベース](sql-database-manage-portal.md)
#### [Azure Portal](sql-database-get-started.md)
#### [C#](sql-database-get-started-csharp.md)
#### [PowerShell](sql-database-manage-powershell.md)
#### [SQL Server Management Studio](sql-database-manage-azure-ssms.md)
### エラスティック データベース プール
#### [Azure Portal を使用した作成](sql-database-elastic-pool-create-portal.md)
#### [PowerShell を使用した作成](sql-database-elastic-pool-create-powershell.md)
#### [C# を使用した作成](sql-database-elastic-pool-create-csharp.md)
#### [Azure Portal を使用した管理](sql-database-elastic-pool-manage-portal.md)
#### [PowerShell を使用した管理](sql-database-elastic-pool-manage-powershell.md)
#### [C# を使用した管理](sql-database-elastic-pool-manage-csharp.md)
#### [T-SQL を使用した管理](sql-database-elastic-pool-manage-tsql.md)
### シャード データベース
#### [シャード マップ マネージャーを使用する](sql-database-elastic-scale-shard-map-management.md)
#### [分割/結合セキュリティの構成](sql-database-elastic-scale-split-merge-security-configuration.md)
#### [Dapper を使用する](sql-database-elastic-scale-working-with-dapper.md)
#### [Entity Framework を使用する](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)
#### [マルチテナントの行レベル セキュリティ](sql-database-elastic-tools-multi-tenant-row-level-security.md)
#### [資格情報を管理する](sql-database-elastic-scale-manage-credentials.md)
#### [split-merge サービスのデプロイ](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [シャードを追加する](sql-database-elastic-scale-add-a-shard.md)
#### [RecoveryManager クラスを使用したシャード マップに関する問題の解決](sql-database-elastic-database-recovery-manager.md)
###  認証
#### [Add Azure AD 認証](sql-database-aad-authentication.md)
#### [多要素認証](sql-database-ssms-mfa-authentication.md)
### ファイアウォール規則
#### [Azure Portal](sql-database-configure-firewall-settings.md)
#### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
#### [REST API](sql-database-configure-firewall-settings-rest.md)
#### [T-SQL](sql-database-configure-firewall-settings-tsql.md)
### ジョブと自動化
#### [サービスのインストール](sql-database-elastic-jobs-service-installation.md)
#### [Azure Portal でのエラスティック ジョブの作成と管理](sql-database-elastic-jobs-create-and-manage.md)
#### [PowerShell を使用したエラスティック ジョブの作成と管理](sql-database-elastic-jobs-powershell.md)
#### [クライアント ライブラリをアップグレードする](sql-database-elastic-scale-upgrade-client-library.md)
#### [Azure Automation サービスを使用した SQL Database の管理](sql-database-manage-automation.md)
### [ファイアウォール](sql-database-firewall-configure.md)
## データを暗号化する
### [Always Encrypted の概要](sql-database-always-encrypted.md)
### [透過的なデータ暗号化](https://msdn.microsoft.com/library/azure/dn948096)
### [列の暗号化](https://msdn.microsoft.com/library/azure/ms179331)
## 移行
### 互換性を確認する
#### [SQL パッケージ ユーティリティ](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### 互換性の問題の修正
#### [SQL Server Data Tools](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [SQL Azure 移行ウィザード](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [SQL Server Management Studio 移行ウィザードを使用する](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
### [トランザクション レプリケーションを使用する](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
### [スケールアウトされた既存のデータベースを移行して、スケールアウトする](sql-database-elastic-convert-to-use-elastic-tools.md)
## 監視と調整
### [Query Performance Insight](sql-database-query-performance.md)
### [SQL Database Advisor](sql-database-advisor-portal.md)
### [DMV](sql-database-monitoring-with-dmvs.md)
### [互換性レベル](sql-database-compatibility-level-query-performance-130.md)
### [シャード マップ マネージャーのパフォーマンス カウンター](sql-database-elastic-database-perf-counters.md)
### [パフォーマンスのチューニングのヒント](sql-database-troubleshoot-performance.md)
### サービス プランとパフォーマンス レベルを変更する
#### [Azure Portal の使用](sql-database-scale-up.md)
#### [PowerShell の使用](sql-database-scale-up-powershell.md)
### [アラートの作成](sql-database-insights-alerts-portal.md)
### インメモリ OLTP
#### [インメモリ OLTP を導入する](sql-database-in-memory-oltp-migration.md)
#### [インメモリ OLTP ストレージの監視](sql-database-in-memory-oltp-monitoring.md)
### クエリ ストア
#### [クエリ ストアを使用したパフォーマンスの監視](https://msdn.microsoft.com/library/dn817826.aspx)
#### [クエリ ストアの使用シナリオ](https://msdn.microsoft.com/library/mt614796.aspx)
#### [クエリ ストアの操作](sql-database-operate-query-store.md)
### 拡張イベント
#### [拡張イベント](sql-database-xevent-db-diff-from-svr.md)
#### [イベント ファイル ターゲット コード](sql-database-xevent-code-event-file.md)
#### [リング バッファー ターゲット コード](sql-database-xevent-code-ring-buffer.md)
## データの移動
### [SQL Database をコピーする](sql-database-copy.md)
#### [Azure Portal](sql-database-copy-portal.md)
#### [PowerShell](sql-database-copy-powershell.md)
#### [T-SQL](sql-database-copy-transact-sql.md)
### データベースを BACPAC ファイルにエクスポートする
#### [Azure Portal](sql-database-export.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [SQL パッケージ ユーティリティ](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [PowerShell](sql-database-export-powershell.md)
### データベースを BACPAC ファイルからインポートする
#### [Azure Portal](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [SQL パッケージ ユーティリティ](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
### [BCP を使用して CSV ファイルから読み込む](sql-database-load-from-csv-with-bcp.md)
### [スケールアウトされたクラウド データベース間のデータ移動](sql-database-elastic-scale-overview-split-and-merge.md)
## クエリ
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
### [マルチシャード クエリ実行](sql-database-elastic-scale-multishard-querying.md)
### 複数データベース間のクエリ
#### [概要](sql-database-elastic-query-overview.md)
#### [さまざまなスキーマを使用した複数データベース間のクエリ実行](sql-database-elastic-query-vertical-partitioning.md)
#### [複数データベース間のレポート作成](sql-database-elastic-query-horizontal-partitioning.md)
#### [クラウド データベースにまたがる分散トランザクション](sql-database-elastic-transactions-overview.md)
## 復元
### 削除済みデータベースの復元
#### [Azure Portal](sql-database-restore-deleted-database-portal.md)
#### [PowerShell](sql-database-restore-deleted-database-powershell.md)
### ポイントインタイム リストア
#### [Azure Portal](sql-database-point-in-time-restore-portal.md)
#### [PowerShell](sql-database-point-in-time-restore-powershell.md)
### 地理リストア
#### [Azure Portal](sql-database-geo-restore-portal.md)
#### [PowerShell](sql-database-geo-restore-powershell.md)
#### [1 つのテーブル](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
### [データ センターの停止から回復する](sql-database-disaster-recovery.md)
### [障害復旧訓練を実施する](sql-database-disaster-recovery-drills.md)
## レプリケート
### [アクティブ geo レプリケーションの概要](sql-database-geo-replication-overview.md)
### 構成
#### [PowerShell](sql-database-geo-replication-powershell.md)
#### [T-SQL](sql-database-geo-replication-transact-sql.md)
### フェールオーバー
#### [Azure Portal](sql-database-geo-replication-failover-portal.md)
#### [PowerShell](sql-database-geo-replication-failover-powershell.md)
#### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)
## トラブルシューティング
### [接続に関する問題](sql-database-troubleshoot-common-connection-issues.md)
### [一時的な接続エラー](sql-database-troubleshoot-connection.md)
### [診断と防止](sql-database-connectivity-issues.md)
### [アクセス許可](sql-database-troubleshoot-permissions.md)
### [データベースの移動](sql-database-troubleshoot-moving-data.md)


# リファレンス
## [PowerShell](/powershell/resourcemanager/azurerm.sql/v2.3.0/azurerm.sql)
## [PowerShell (エラスティック データベース)](/powershell/elasticdatabasejobs/v0.8.33/elasticdatabasejobs)
## [.NET](https://docs.microsoft.com)
## [Java](/java/api/com.microsoft.azure.management.sql)
## [Node.JS](https://msdn.microsoft.com/library/mt652093.aspx)
## [Python](https://msdn.microsoft.com/library/mt652092.aspx)
## [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)
## [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
## [T-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
## [REST ()](/rest/api/sql/)

# 関連項目
## SQL Database の管理ライブラリ
### [SQL Database の管理ライブラリ パッケージを取得する](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [SQL Server ドライバー](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)

### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)


# リソース
## [料金](https://azure.microsoft.com/pricing/details/sql-database/)
## [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/sql-azure)
## [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=sql-database)
## [サービスの更新情報](https://azure.microsoft.com/updates/?service=sql-database)
## [SQL Server Tools](https://msdn.microsoft.com/library/mt238365.aspx)
## [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
## [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
## [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
## [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
## [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)


<!--HONumber=Dec16_HO1-->


