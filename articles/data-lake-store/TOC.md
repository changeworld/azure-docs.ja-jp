# [Data Lake Storage Gen1 のドキュメント](index.md)
# [Data Lake Storage Gen2 (プレビュー) のドキュメントに切り替え](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction)

# 概要
## [Data Lake Storage Gen1 の概要](data-lake-store-overview.md)
## [Azure Storage との比較](data-lake-store-comparison-with-blob-storage.md)
## [ビッグ データの処理](data-lake-store-data-scenarios.md)
## [オープン ソース アプリケーションの使用](data-lake-store-compatible-oss-other-applications.md)
## [ベスト プラクティス](data-lake-store-best-practices.md)

# 作業開始
## [Azure Portal の使用](data-lake-store-get-started-portal.md)
## [Azure PowerShell の使用](data-lake-store-get-started-powershell.md)
## [Azure CLI の使用](data-lake-store-get-started-cli-2.0.md)

# 方法
## データの読み込みと移動
### [Azure Data Factory の使用](../data-factory/load-azure-data-lake-store.md)
### [ストレージ エクスプローラーの使用](data-lake-store-in-storage-explorer.md)
### [AdlCopy を使用](data-lake-store-copy-data-azure-storage-blob.md)
### [DistCp を使用](data-lake-store-copy-data-wasb-distcp.md)
### [Sqoop の使用](data-lake-store-data-transfer-sql-sqoop.md)
### [オフライン ソースからデータをアップロードする](data-lake-store-offline-bulk-data-upload.md)
### [リージョン間での Data Lake Storage Gen1 の移行](data-lake-store-migration-cross-region.md)

## データのセキュリティ保護
### [セキュリティの概要](data-lake-store-security-overview.md)
### [アクセス制御](data-lake-store-access-control.md)
### [保存データのセキュリティ保護](data-lake-store-secure-data.md)
### [暗号化](data-lake-store-encryption.md)
### [仮想ネットワークの統合 (プレビュー)](data-lake-store-network-security.md)

## Data Lake Storage Gen1 での認証
### [認証オプション](data-lakes-store-authentication-using-azure-active-directory.md)
### [エンドユーザー認証](data-lake-store-end-user-authenticate-using-active-directory.md)
#### [Java の使用](data-lake-store-end-user-authenticate-java-sdk.md)
#### [.NET SDK の使用](data-lake-store-end-user-authenticate-net-sdk.md)
#### [REST API の使用](data-lake-store-end-user-authenticate-rest-api.md)
#### [Python の使用](data-lake-store-end-user-authenticate-python.md)
### [サービス間認証](data-lake-store-service-to-service-authenticate-using-active-directory.md)
#### [Java の使用](data-lake-store-service-to-service-authenticate-java.md)
#### [.NET SDK の使用](data-lake-store-service-to-service-authenticate-net-sdk.md)
#### [REST API の使用](data-lake-store-service-to-service-authenticate-rest-api.md)
#### [Python の使用](data-lake-store-service-to-service-authenticate-python.md)

## Data Lake Storage Gen1 の操作
### アカウント管理操作
#### [.NET SDK の使用](data-lake-store-get-started-net-sdk.md)
#### [REST API の使用](data-lake-store-get-started-rest-api.md)
#### [Python の使用](data-lake-store-get-started-python.md)
### ファイルシステム操作
#### [.NET SDK の使用](data-lake-store-data-operations-net-sdk.md)
#### [Java SDK の使用](data-lake-store-get-started-java-sdk.md)
#### [REST API の使用](data-lake-store-data-operations-rest-api.md)
#### [Python の使用](data-lake-store-data-operations-python.md)

## パフォーマンス
### [概要](data-lake-store-performance-tuning-guidance.md)
### [Azure PowerShell の使用](data-lake-store-performance-tuning-powershell.md)
### [HDInsight での Spark の使用](data-lake-store-performance-tuning-spark.md)
### [HDInsight での Hive の使用](data-lake-store-performance-tuning-hive.md)
### [HDInsight での MapReduce の使用](data-lake-store-performance-tuning-mapreduce.md)
### [HDInsight での Storm の使用](data-lake-store-performance-tuning-storm.md)

## Azure サービスと統合する
### HDInsight を使用
#### [Azure Portal の使用](data-lake-store-hdinsight-hadoop-use-portal.md)
#### [Azure PowerShell の使用 (既定のストレージ)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
#### [Azure PowerShell の使用 (追加のストレージ)](data-lake-store-hdinsight-hadoop-use-powershell.md)
#### [Azure テンプレートの使用](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
### [Azure VNET 内の VM からアクセスする](data-lake-store-connectivity-from-vnets.md)
### [Data Lake Analytics で使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
### [Azure Event Hubs で使用する](data-lake-store-archive-eventhub-capture.md)
### [Data Factory で使用する](../data-factory/load-azure-data-lake-store.md)
### [Stream Analytics で使用する](data-lake-store-stream-analytics.md)
### [Power BI で使用する](data-lake-store-power-bi.md)
### [Data Catalog で使用する](data-lake-store-with-data-catalog.md)
### [SQL Data Warehouse で PolyBase を使用する](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)
### [SQL Server Integration Services と共に使用する](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager)
### [その他の Azure 統合オプション](data-lake-store-integrate-with-other-services.md)

## 管理
### [診断ログにアクセスする](data-lake-store-diagnostic-logs.md)
### [高可用性向けの計画](data-lake-store-disaster-recovery-guidance.md)

# リファレンス
## [コード サンプル](https://azure.microsoft.com/resources/samples/?service=data-lake-store)
## [Azure PowerShell](/powershell/module/azurerm.datalakestore)
## [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)
## [Java](/java/api/com.microsoft.azure.datalake.store)
## [Node.js](https://www.npmjs.com/package/azure-arm-datalake-store)
## [Python (アカウント管理)](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python)
## [Python (ファイルシステム管理)](http://azure-datalake-store.readthedocs.io/en/latest)
## [REST](/rest/api/datalakestore)
## [Resource Manager テンプレート](/azure/templates/microsoft.datalakestore/allversions)
## [Azure CLI](https://docs.microsoft.com/cli/azure/dls)

# リソース
## [Azure のロードマップ](https://azure.microsoft.com/updates/?product=data-lake-store)
## [Data Lake Store のブログ](https://blogs.msdn.microsoft.com/azuredatalake/)
## [UserVoice に関するフィードバックを送信する](https://feedback.azure.com/forums/327234-data-lake)
## [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDataLake)
## [料金](https://azure.microsoft.com/pricing/details/data-lake-store/)
## [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
## [Stack Overflow フォーラム](http://stackoverflow.com/questions/tagged/azure-data-lake)
## [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=data-lake-store)
