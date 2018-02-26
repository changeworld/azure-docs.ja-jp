# 概要

## [SQL Data Warehouse について](sql-data-warehouse-overview-what-is.md)
## [チート シート](cheat-sheet.md)

# クイック スタート

## [作成と接続 - ポータル](create-data-warehouse-portal.md)
## コンピューティングの一時停止と再開
### [ポータル](pause-and-resume-compute-portal.md)
### [PowerShell](pause-and-resume-compute-powershell.md)
## コンピューティングのスケーリング
### [ポータル](quickstart-scale-compute-portal.md)
### [PowerShell](quickstart-scale-compute-powershell.md)
### [T-SQL](quickstart-scale-compute-tsql.md)


# チュートリアル
## [1 - BLOB からデータを読み込む](load-data-from-azure-blob-storage-using-polybase.md)

# 概念
## サービスの機能
### [MPP アーキテクチャ](massively-parallel-processing-mpp-architecture.md)
### [パフォーマンス レベル](performance-tiers.md)
### [Data Warehouse ユニット](what-is-a-data-warehouse-unit-dwu-cdwu.md)
### [スケールアウト、一時停止、再開](sql-data-warehouse-manage-compute-overview.md)
### [データ ウェアハウスのバックアップ](sql-data-warehouse-backups.md)
### [監査](sql-data-warehouse-auditing-overview.md)
### [容量制限](sql-data-warehouse-service-capacity-limits.md)
### [FAQ](sql-data-warehouse-overview-faq.md)

## セキュリティ
### [概要](sql-data-warehouse-overview-manage-security.md)
### [認証](sql-data-warehouse-authentication.md)


## SQL Data Warehouse への移行
### [概要](sql-data-warehouse-overview-migrate.md)
### [スキーマの移行](sql-data-warehouse-migrate-schema.md)
### [コードの移行](sql-data-warehouse-migrate-code.md)
### [データの移行](sql-data-warehouse-migrate-data.md)

## データの読み込みと移動
### [概要](design-elt-data-loading.md)
### [ベスト プラクティス](guidance-for-loading-data.md)


## 統合
### [概要](sql-data-warehouse-overview-integrate.md)
### [SQL Database エラスティック クエリ](how-to-use-elastic-query-with-sql-data-warehouse.md)


## 監視とチューニング
### [ワークロード管理](resource-classes-for-workload-management.md)
### [列ストア圧縮](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
### [監視](sql-data-warehouse-manage-monitor.md)
### [トラブルシューティング](sql-data-warehouse-troubleshoot.md)

## データ ウェアハウスの開発
### [概要](sql-data-warehouse-overview-develop.md)
### [データ ウェアハウスのコンポーネント](sql-data-warehouse-overview-workload.md)

### テーブル
#### [概要](sql-data-warehouse-tables-overview.md)
#### [CTAS](sql-data-warehouse-develop-ctas.md)
#### [データ型](sql-data-warehouse-tables-data-types.md)
#### [分散テーブル](sql-data-warehouse-tables-distribute.md)
#### [インデックス](sql-data-warehouse-tables-index.md)
#### [ID](sql-data-warehouse-tables-identity.md)
#### [パーティション](sql-data-warehouse-tables-partition.md)
#### [レプリケート テーブル](design-guidance-for-replicated-tables.md)
#### [統計](sql-data-warehouse-tables-statistics.md)
#### [一時](sql-data-warehouse-tables-temporary.md)

### クエリ
#### [動的 SQL](sql-data-warehouse-develop-dynamic-sql.md)
#### [オプションでグループ化する](sql-data-warehouse-develop-group-by-options.md)
#### [ラベル](sql-data-warehouse-develop-label.md)

### T-SQL 言語要素
#### [ループ](sql-data-warehouse-develop-loops.md)
#### [ストアド プロシージャ](sql-data-warehouse-develop-stored-procedures.md)
#### [トランザクション](sql-data-warehouse-develop-transactions.md)
#### [トランザクションのベスト プラクティス](sql-data-warehouse-develop-best-practices-transactions.md)
#### [ユーザー定義スキーマ](sql-data-warehouse-develop-user-defined-schemas.md)
#### [変数の代入](sql-data-warehouse-develop-variable-assignment.md)
#### [ビュー](sql-data-warehouse-develop-views.md)

# ハウツー ガイド
## サービスの機能
### [データ ウェアハウスの復元 - ポータル](sql-data-warehouse-restore-database-portal.md)
### [データ ウェアハウスの復元 - PowerShell](sql-data-warehouse-restore-database-powershell.md)
### [データ ウェアハウスの復元 - REST API](sql-data-warehouse-restore-database-rest-api.md)

## セキュリティ
### [暗号化の有効化 - ポータル](sql-data-warehouse-encryption-tde.md)
### [暗号化の有効化 - T-SQL](sql-data-warehouse-encryption-tde-tsql.md)
### [脅威の検出](sql-data-warehouse-security-threat-detection.md)


## データの読み込みと移動
### [Contoso のパブリック データ](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
### [Azure Data Lake Store](sql-data-warehouse-load-from-azure-data-lake-store.md)
### [BCP](sql-data-warehouse-load-with-bcp.md)
### [Data Factory](sql-data-warehouse-load-with-data-factory.md)
### [AzCopy](sql-data-warehouse-load-from-sql-server-with-polybase.md)
### [RedGate](sql-data-warehouse-load-with-redgate.md)
### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)


## 統合
### [SQL Database エラスティック クエリの構成](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)
### [Azure Stream Analytics ジョブの追加](sql-data-warehouse-integrate-azure-stream-analytics.md)
### [機械学習の使用](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
### [Power BI による視覚化](sql-data-warehouse-get-started-visualize-with-power-bi.md)

## 監視とチューニング
### [ワークロードの分析](analyze-your-workload.md)

## スケールアウト

### [コンピューティング レベルの自動化](manage-compute-with-azure-functions.md)


# リファレンス

## T-SQL
### [完全なリファレンス](https://docs.microsoft.com/sql/t-sql/language-reference/)
### [SQL DW 言語要素](sql-data-warehouse-reference-tsql-language-elements.md)
### [SQL DW ステートメント](sql-data-warehouse-reference-tsql-statements.md)
## [システム ビュー](sql-data-warehouse-reference-tsql-system-views.md)
## [PowerShell コマンドレット](sql-data-warehouse-reference-powershell-cmdlets.md)
## [REST API](sql-data-warehouse-manage-compute-rest-api.md)

# リソース
## [Azure のロードマップ](https://azure.microsoft.com/roadmap/?category=databases)
## [フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSQLDataWarehouse)
## [料金](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)
## [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
## [機能に関する要求](https://feedback.azure.com/forums/307516-sql-data-warehouse/)
## [サービスの更新情報](https://azure.microsoft.com/updates/?product=sql-data-warehouse)
## [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/)
## [サポート](sql-data-warehouse-get-started-create-support-ticket.md)
## [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

## パートナー
### [ビジネス インテリジェンス](sql-data-warehouse-partner-business-intelligence.md)
### [データ統合](sql-data-warehouse-partner-data-integration.md)
### [データ管理](sql-data-warehouse-partner-data-management.md)
