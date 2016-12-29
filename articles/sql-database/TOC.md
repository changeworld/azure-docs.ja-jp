# 概要
## [SQL Database とは](sql-database-technical-overview.md)
### [サービス階層](sql-database-service-tiers.md)
### [DTU と eDTU](sql-database-what-is-a-dtu.md)
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
### 詳細情報
#### [サーバー](sql-database-server-overview.md)
#### [単一データベース](sql-database-overview.md)
#### [複数のデータベース](sql-database-elastic-scale-introduction.md)
##### テナントのマッピング
###### [エラスティック クライアント ライブラリ](sql-database-elastic-database-client-library.md)
###### [シャード マップ マネージャー](sql-database-elastic-scale-shard-map-management.md)
###### [データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)
###### [資格情報を管理する](sql-database-elastic-scale-manage-credentials.md)
###### [マルチシャード クエリ実行](sql-database-elastic-scale-multishard-querying.md)
##### エラスティック プール (リソース プール)
###### [エラスティック プールとは](sql-database-elastic-pool.md)
###### [エラスティック プールを使う局面](sql-database-elastic-pool-guidance.md)
###### [エラスティック プールの価格](sql-database-elastic-pool-price.md)
##### シャード データベース
###### [エラスティック ツール用語集](sql-database-elastic-scale-glossary.md)
###### [シャード間でデータを移動する](sql-database-elastic-scale-overview-split-and-merge.md)
###### [エラスティック ツールに関する FAQ](sql-database-elastic-scale-faq.md)
##### エラスティック クエリ (複数データベース間のクエリ)
###### [エラスティック クエリとは](sql-database-elastic-query-overview.md)
##### エラスティック トランザクション (分散トランザクション)
###### [クラウド データベースにまたがるトランザクション](sql-database-elastic-transactions-overview.md)
##### エラスティック ジョブ (複数データベース間のジョブ)
###### [エラスティック ジョブとは](sql-database-elastic-jobs-overview.md)
#### [Azure RemoteApp を使用して SQL Database に接続する](sql-database-ssms-remoteapp.md)
#### [Azure Automation サービスを使用した SQL Database の管理](sql-database-manage-automation.md)
### すべきこと
#### [Azure Portal を使用して単一データベースを作成する](sql-database-get-started.md)
#### [PowerShell を使用して単一データベースを作成する](sql-database-get-started-powershell.md)
#### [C# を使用して単一データベースを作成する](sql-database-get-started-csharp.md)
#### [シャード化されたアプリケーションを作成する](sql-database-elastic-scale-get-started.md)
#### [シャード間でデータを移動する](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [エラスティック ジョブの概要](sql-database-elastic-jobs-getting-started.md)
#### [エラスティック クエリの概要](sql-database-elastic-query-getting-started-vertical.md)
#### [エラスティック クエリを使用してレポートを作成する](sql-database-elastic-query-getting-started.md)
#### [各種スキーマを使用してデータベースにクエリを実行する](sql-database-elastic-query-vertical-partitioning.md)
#### [スケールアウトされたデータベース全体をレポートする](sql-database-elastic-query-horizontal-partitioning.md)
## データの移行と移動
### 詳細情報
#### [データベースを移行する](sql-database-cloud-migrate.md)
#### [トランザクション レプリケーション](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
#### [データ同期](sql-database-get-started-sql-data-sync.md)
#### [SQL Database をコピーする](sql-database-copy.md)
## ファイアウォール規則、認証、承認
### 詳細情報
#### [概要](sql-database-security.md)
#### [セキュリティ ガイドライン](sql-database-security-guidelines.md)
#### [ファイアウォール](sql-database-firewall-configure.md)
#### [ログインの管理](sql-database-manage-logins.md)
### すべきこと
#### [SQL の認証と承認](sql-database-get-started-security.md)
## データの安全性を確保して保護する
### 詳細情報
#### 監査
##### [監査](sql-database-auditing-get-started.md)
##### [監査のためのダウンレベル クライアントのサポートと IP エンドポイントの変更](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [脅威の検出](sql-database-threat-detection-get-started.md)
#### データを暗号化する
##### [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)
##### [透過的なデータ暗号化](https://msdn.microsoft.com/library/azure/dn948096)
##### [列の暗号化](https://msdn.microsoft.com/library/azure/ms179331)
#### データをマスクする
##### 動的データ マスク
###### [Azure Portal](sql-database-dynamic-data-masking-get-started.md)
### すべきこと
#### [Azure Portal を使用した動的データ マスク](sql-database-dynamic-data-masking-get-started.md)
##### [Windows 証明書ストアを使用した Always Encrypted](sql-database-always-encrypted.md)
## ビジネス継続性
### 詳細情報
#### [概要](sql-database-business-continuity.md)
#### [データベースのバックアップ](sql-database-automated-backups.md)
#### [長期保存](sql-database-long-term-retention.md)
#### [バックアップを使用したデータベースの復元](sql-database-recovery-using-backups.md)
#### [1 つのテーブルの回復](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
#### [データ センターの停止から回復する](sql-database-disaster-recovery.md)
#### [障害復旧の認証要件](sql-database-geo-replication-security-config.md)
#### [ビジネス継続性の設計シナリオ](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [エラスティック プールを使用した障害復旧戦略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [ローリング アップグレード](sql-database-manage-application-rolling-upgrade.md)
#### [障害復旧訓練を実施する](sql-database-disaster-recovery-drills.md)
#### [アクティブ geo レプリケーションの概要](sql-database-geo-replication-overview.md)
### すべきこと
#### [SQL Database のバックアップと復元の概要](sql-database-get-started-backup-recovery.md)
## アプリケーション開発
### 詳細情報
#### [データベース アプリケーションの開発の概要](sql-database-develop-overview.md)
#### [接続ライブラリ](sql-database-libraries.md)
#### [マルチテナント SaaS アプリケーション](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [行レベルのセキュリティを使用したマルチテナント SaaS アプリケーションのスケーリング](sql-database-elastic-tools-multi-tenant-row-level-security.md)
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
#### [Entity Framework を使用する](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Dapper でクライアント ライブラリを使用する](sql-database-elastic-scale-working-with-dapper.md)
### 顧客実装
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
#### [メモリ内の最適化](sql-database-in-memory.md)
### すべきこと
#### [SQL Server の開発](https://msdn.microsoft.com/library/ms179422.aspx)
#### [インメモリ OLTP を導入する](sql-database-in-memory-oltp-migration.md)
## 監視とチューニング
### 詳細情報
#### [単一データベース](sql-database-single-database-monitor.md)
#### [SQL Database Advisor の概要](sql-database-advisor.md)
#### [Single Database のガイダンス](sql-database-performance-guidance.md)
#### [Azure Portal でのワークロードの洞察](sql-database-performance.md)
#### [バッチ処理を使用する](sql-database-use-batching-to-improve-performance.md)
#### [拡張イベント](sql-database-xevent-db-diff-from-svr.md)
## SQL Database V11
### [Web および Business Edition の終了](sql-database-web-business-sunset-faq.md)
### [Service Tier Advisor](sql-database-service-tier-advisor.md)
### [エラスティック プール評価ツール](sql-database-elastic-pool-database-assessment-powershell.md)
### [V12 へアップグレード](sql-database-v12-plan-prepare-upgrade.md)
#### [Azure Portal を使用したアップグレード](sql-database-upgrade-server-portal.md)
#### [PowerShell を使用したアップグレード](sql-database-upgrade-server-powershell.md)
# 方法
## 作成と管理
### [Azure Portal を使用して SQL Database を管理する](sql-database-manage-portal.md)
### [PowerShell を使用して SQL Database を管理する](sql-database-manage-powershell.md)
### [SSMS を使用して SQL Database を管理する](sql-database-manage-azure-ssms.md)
### サーバー
#### [サーバーを作成する](sql-database-create-servers.md)
#### [サーバーの設定を表示または更新する](sql-database-view-update-server-settings.md)
### Single Database
#### [単一データベースを作成する](sql-database-create-databases.md)
#### [データベースの設定を表示または更新する](sql-database-view-update-database-settings.md)
### ファイアウォール規則
#### [Azure Portal を使用してファイアウォール規則を作成する](sql-database-configure-firewall-settings.md)
#### [PowerShell を使用してファイアウォール規則を管理する](sql-database-configure-firewall-settings-powershell.md)
#### [REST API を使用してファイアウォール規則を作成する](sql-database-configure-firewall-settings-rest.md)
#### [T-SQL を使用してファイアウォール規則を作成する](sql-database-configure-firewall-settings-tsql.md)
### 複数のデータベース
#### [クライアント アプリケーションでクライアント ライブラリをアップグレードする](sql-database-elastic-scale-upgrade-client-library.md)
#### シャード データベース
##### [セキュリティ構成](sql-database-elastic-scale-split-merge-security-configuration.md)
##### [シャードを追加する](sql-database-elastic-scale-add-a-shard.md)
##### [シャード マップの問題を修正する](sql-database-elastic-database-recovery-manager.md)
##### [スケールアウトされた既存のデータベースをシャード化されたデータベースに移行する](sql-database-elastic-convert-to-use-elastic-tools.md)
##### [シャード マップ マネージャーのパフォーマンス カウンターを作成する](sql-database-elastic-database-perf-counters.md)
#### エラスティック ジョブ
##### [エラスティック ジョブ サービスのインストール方法](sql-database-elastic-jobs-service-installation.md)
##### [PowerShell を使用したエラスティック ジョブの作成と管理](sql-database-elastic-jobs-powershell.md) 
##### [Azure Portal を使用したエラスティック ジョブの作成と管理](sql-database-elastic-jobs-create-and-manage.md)
##### [エラスティック ジョブのアンインストール方法](sql-database-elastic-jobs-uninstall.md)
#### エラスティック プール
##### [Azure Portal を使用した作成](sql-database-elastic-pool-create-portal.md)
##### [PowerShell を使用した作成](sql-database-elastic-pool-create-powershell.md)
##### [C# を使用した作成](sql-database-elastic-pool-create-csharp.md)
##### [Azure Portal を使用した管理](sql-database-elastic-pool-manage-portal.md)
##### [PowerShell を使用した管理](sql-database-elastic-pool-manage-powershell.md)
##### [C# を使用した管理](sql-database-elastic-pool-manage-csharp.md)
##### [T-SQL を使用した管理](sql-database-elastic-pool-manage-tsql.md)
##  認証と承認
### [Add Azure AD 認証](sql-database-aad-authentication.md)
### [多要素認証](sql-database-ssms-mfa-authentication.md)
## データを暗号化する
### [透過的なデータ暗号化](https://msdn.microsoft.com/library/azure/dn948096)
### [列の暗号化](https://msdn.microsoft.com/library/azure/ms179331)
## データベースを移行する
### 互換性を確認する
#### [SQL パッケージ ユーティリティを使用して互換性を確認する](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [SSMS を使用して互換性を確認する](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### 互換性の問題の修正
#### [SSDT を使用して互換性の問題を修正する](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [SSMS を使用して互換性の問題を修正する](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [SMW を使用して互換性の問題を修正する](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [SSMS の移行ウィザードを使用して移行する](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
## 監視と調整
### [Query Performance Insight](sql-database-query-performance.md)
### [SQL Database Advisor](sql-database-advisor-portal.md)
### [DMV](sql-database-monitoring-with-dmvs.md)
### [互換性レベル](sql-database-compatibility-level-query-performance-130.md)
### [パフォーマンスのチューニングのヒント](sql-database-troubleshoot-performance.md)
### サービス プランとパフォーマンス レベルを変更する
#### [Azure Portal を使用してサービス レベルを変更する](sql-database-scale-up.md)
#### [PowerShell を使用してサービス レベルを変更する](sql-database-scale-up-powershell.md)
### [アラートの作成](sql-database-insights-alerts-portal.md)
#### [インメモリ OLTP ストレージの監視](sql-database-in-memory-oltp-monitoring.md)
### クエリ ストア
#### [クエリ ストアを使用したパフォーマンスの監視](https://msdn.microsoft.com/library/dn817826.aspx)
#### [クエリ ストアの使用シナリオ](https://msdn.microsoft.com/library/mt614796.aspx)
#### [クエリ ストアの操作](sql-database-operate-query-store.md)
### 拡張イベント
#### [イベント ファイル ターゲット コード](sql-database-xevent-code-event-file.md)
#### [リング バッファー ターゲット コード](sql-database-xevent-code-ring-buffer.md)
## データの移動
### SQL Database をコピーする
#### [Azure Portal を使用したコピー](sql-database-copy-portal.md)
#### [PowerShell を使用したコピー](sql-database-copy-powershell.md)
#### [T-SQL を使用したコピー](sql-database-copy-transact-sql.md)
### データベースを BACPAC ファイルにエクスポートする
#### [Azure Portal を使用したエクスポート](sql-database-export.md)
#### [SSMS を使用したエクスポート](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [SQL パッケージ ユーティリティを使用したエクスポート](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [PowerShell を使用したエクスポート](sql-database-export-powershell.md)
### データベースを BACPAC ファイルからインポートする
#### [Azure Portal を使用したインポート](sql-database-import.md)
#### [PowerShell を使用したインポート](sql-database-import-powershell.md)
#### [SSMS を使用したインポート](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [SQL パッケージ ユーティリティを使用したインポート](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
### [BCP を使用して CSV ファイルから読み込む](sql-database-load-from-csv-with-bcp.md)
## クエリ
### [SSMS を使用したクエリ](sql-database-connect-query-ssms.md)
## バックアップと復元
### 長期のバックアップ リテンション期間
#### [長期のバックアップ リテンション期間の構成](sql-database-configure-long-term-retention.md)
#### [Recovery Services コンテナー内のバックアップの表示](sql-database-view-backups-in-vault.md)
#### [長期のバックアップ リテンション期間からの復元](sql-database-restore-from-long-term-retention.md)
### 削除済みデータベースの復元
#### [Azure Portal を使用して削除済みデータベースを復元する](sql-database-restore-deleted-database-portal.md)
#### [PowerShell を使用して削除済みデータベースを復元する](sql-database-restore-deleted-database-powershell.md)
### ポイントインタイム リストア
#### [特定の時点への復元](sql-database-point-in-time-restore.md)
#### [最も古い復元ポイントの表示](sql-database-view-oldest-restore-point.md)
### geo リストア
#### [Azure Portal を使用した geo リストア](sql-database-geo-restore-portal.md)
#### [PowerShell を使用した geo リストア](sql-database-geo-restore-powershell.md)
## アクティブ geo レプリケーションを選択するとき
### [Azure Portal を使用した構成](sql-database-geo-replication-portal.md)
### [PowerShell を使用した構成](sql-database-geo-replication-powershell.md)
### [T-SQL を使用した構成](sql-database-geo-replication-transact-sql.md)
### [Azure Portal を使用したフェールオーバー](sql-database-geo-replication-failover-portal.md)
### [PowerShell を使用したフェールオーバー](sql-database-geo-replication-failover-powershell.md)
### [T-SQL を使用したフェールオーバー](sql-database-geo-replication-failover-transact-sql.md)
## トラブルシューティング
### [接続に関する問題](sql-database-troubleshoot-common-connection-issues.md)
### [一時的な接続エラー](sql-database-troubleshoot-connection.md)
### [診断と防止](sql-database-connectivity-issues.md)
### [アクセス許可](sql-database-troubleshoot-permissions.md)
### [データベースの移動](sql-database-troubleshoot-moving-data.md)
# リファレンス
## [PowerShell](/powershell/resourcemanager/azurerm.sql/v2.3.0/azurerm.sql)
## [PowerShell (エラスティック データベース)](/powershell/elasticdatabasejobs/v0.8.33/elasticdatabasejobs)
## [.NET](/dotnet/api/microsoft.azure.management.sql.models)
## [Java](/java/api/com.microsoft.azure.management.sql)
## [Node.JS](https://msdn.microsoft.com/library/mt652093.aspx)
## [Python](https://msdn.microsoft.com/library/mt652092.aspx)
## [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)
## [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
## [T-SQL](https://msdn.microsoft.com/library/bb510741.aspx)
## [REST ()](https://msdn.microsoft.com/library/azure/mt163571.aspx)

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


<!--HONumber=Dec16_HO3-->


