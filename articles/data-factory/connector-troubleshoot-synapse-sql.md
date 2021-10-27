---
title: Azure Synapse Analytics、Azure SQL Database、および SQL Server のコネクタのトラブルシューティング
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory および Azure Synapse Analytics の Azure Synapse Analytics、Azure SQL Database、および SQL Server コネクタの問題のトラブルシューティングの方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/13/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 84208764621bd03959db7d695bf0616dcccc4491
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130064782"
---
# <a name="troubleshoot-the-azure-synapse-analytics-azure-sql-database-and-sql-server-connectors-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory および Azure Synapse Analytics の Azure Synapse Analytics、Azure SQL Database、および SQL Server コネクタのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory と Azure Synapse の Azure Synapse Analytics、Azure SQL Database、および SQL Server コネクタの一般的な問題のトラブルシューティングのための提案を示します。

## <a name="error-code-sqlfailedtoconnect"></a>エラー コード:SqlFailedToConnect

- **メッセージ**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **原因と推奨事項**: このエラーはさまざまな原因により発生する可能性があります。 考えられる原因の分析および関連する推奨事項については、以下の一覧を確認してください。

    | 原因分析                                               | 推奨                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Azure SQL では、エラー メッセージに "SqlErrorNumber=47073" という文字列が含まれている場合、接続設定で公衆ネットワーク アクセスが拒否されていることを意味します。 | Azure SQL ファイアウォールで、 **[Deny public network access]\(公衆ネットワーク アクセスを拒否する\)** オプションを *[いいえ]* に設定します。 詳細については、「[Azure SQL の接続の設定](../azure-sql/database/connectivity-settings.md#deny-public-network-access)」を参照してください。 |
    | Azure SQL の場合、エラー メッセージに "SqlErrorNumber = [エラー コード]" などの SQL エラー コードが含まれている場合は、『Azure SQL DB トラブルシューティング ガイド』を参照してください。 | 推奨事項については、「[Azure SQL Database および Azure SQL Managed Instance の接続に関する問題とその他のエラーのトラブルシューティング](../azure-sql/database/troubleshoot-common-errors-issues.md)」を参照してください。 |
    | ポート 1433 がファイアウォール許可リストに含まれているかどうかを確認します。 | 詳細については、[SQL Server で使用されるポート](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-)に関する記事を参照してください。 |
    | エラー メッセージに "SqlException" という文字列が含まれている場合、SQL Database のエラーは、何らかの特定の操作が失敗したことを示します。 | 詳細については、「[データベース エンジンのエラー](/sql/relational-databases/errors-events/database-engine-events-and-errors)」の SQL エラー コードを参照してください。 詳細については、Azure SQL サポートにお問い合わせください。 |
    | これが一時的な問題 (不安定なネットワーク接続など) の場合は、アクティビティ ポリシーに再試行を追加して軽減します。 | 詳細については、[パイプラインとアクティビティ](./concepts-pipelines-activities.md#activity-policy)に関するページを参照してください。 |
    | "Client with IP address '...' is not allowed to access the server" (IP アドレスが '...' のクライアントはサーバーへのアクセスが許可されていません) という文字列がエラー メッセージに含まれており、Azure SQL Database に接続しようとしている場合、通常、このエラーの原因は Azure SQL Database のファイアウォールの問題です。 | Azure SQL Server のファイアウォールの構成で、 **[Azure サービスおよびリソースにこのサーバーへのアクセスを許可する]** オプションを有効にします。 詳細については、[Azure SQL Database と Azure Synapse の IP ファイアウォール規則](../azure-sql/database/firewall-configure.md)に関するページを参照してください。 |
    
## <a name="error-code-sqloperationfailed"></a>エラー コード:SqlOperationFailed

- **メッセージ**: `A database operation failed. Please search error to get more details.`

- **原因と推奨事項**: このエラーはさまざまな原因により発生する可能性があります。 考えられる原因の分析および関連する推奨事項については、以下の一覧を確認してください。

    | 原因分析                                               | 推奨                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | エラー メッセージに "SqlException" という文字列が含まれている場合、SQL Database により、何らかの特定の操作が失敗したことを示すエラーがスローされます。 | SQL エラーが明確でない場合は、データベースを最新の互換性レベル '150' に変更してみてください。 最新バージョンの SQL エラーをスローすることができます。 詳細については、[ドキュメント](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat)を参照してください。 <br/> SQL の問題のトラブルシューティングの詳細については、「[データベース エンジンのエラー](/sql/relational-databases/errors-events/database-engine-events-and-errors)」の SQL エラー コードを参照してください。 詳細については、Azure SQL サポートにお問い合わせください。 |
    | エラー メッセージに "PdwManagedToNativeInteropException" という文字列が含まれている場合は、通常、ソースとシンクの列のサイズが一致していないことが原因です。 | ソースの列とシンクの列の両方のサイズを確認してください。 詳細については、Azure SQL サポートにお問い合わせください。 |
    | エラー メッセージに "InvalidOperationException" という文字列が含まれている場合は、通常、入力データが無効であることが原因です。 | 問題が発生している行を特定するには、Copy アクティビティでフォールト トレランス機能を有効にしてください。これにより、問題のある行をストレージにリダイレクトして、さらに調査することができます。 詳細については、[コピー アクティビティのフォールト トレランス](./copy-activity-fault-tolerance.md)に関する記事を参照してください。 |


## <a name="error-code-sqlunauthorizedaccess"></a>エラー コード:SqlUnauthorizedAccess

- **メッセージ**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **原因**:資格情報が正しくないか、ログイン アカウントが SQL データベースにアクセスできません。

- **推奨事項**:SQL Database にアクセスするための十分なアクセス許可がログイン アカウントにあることを確認します。


## <a name="error-code-sqlopenconnectiontimeout"></a>エラー コード:SqlOpenConnectionTimeout

- **メッセージ**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **原因**:この問題は、SQL Database の一時的な障害である可能性があります。

- **推奨事項**:接続タイムアウトの値を大きくして、リンク サービス接続文字列の更新操作を再試行してください。


## <a name="error-code-sqlautocreatetabletypemapfailed"></a>エラー コード:SqlAutoCreateTableTypeMapFailed

- **メッセージ**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **原因**:自動作成テーブルが、ソースの要件を満たすことができません。

- **推奨事項**:"*マッピング*" で列のデータ型を更新するか、ターゲット サーバーで手動でシンク テーブルを作成してください。


## <a name="error-code-sqldatatypenotsupported"></a>エラー コード:SqlDataTypeNotSupported

- **メッセージ**: `A database operation failed. Check the SQL errors.`

- **原因**:SQL ソースで問題が発生し、エラーが SqlDateTime オーバーフローに関連している場合、データ値が論理型の範囲 (1/1/1753 12:00:00 AM - 12/31/9999 11:59:59 PM) を超えています。

- **推奨事項**:ソースの SQL クエリで型を文字列にキャストするか、Copy アクティビティの列マッピングで列の型を *String* に変更してください。

- **原因**:SQL シンクで問題が発生し、エラーが SqlDateTime オーバーフローに関連している場合、データ値はシンク テーブルで許容される範囲を超えています。

- **推奨事項**:シンク テーブルで、対応する列の型を *datetime2* 型に更新してください。


## <a name="error-code-sqlinvaliddbstoredprocedure"></a>エラー コード:SqlInvalidDbStoredProcedure

- **メッセージ**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **原因と推奨事項**: このエラーはさまざまな原因により発生する可能性があります。 考えられる原因の分析および関連する推奨事項については、以下の一覧を確認してください。

  | 原因分析                                               | 推奨                                             |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | 指定されたストアド プロシージャは無効です。 | SQL ツールを使用してストアド プロシージャを検証してください。 ストアド プロシージャがデータを返せることを確認してください。  |
  | 検索アクティビティでは、なんらかの値を返すストアド プロシージャが必要ですが、ストアド プロシージャ コードでは値は返されません。 | ストアド プロシージャで返されるデータがないことが予想される場合は、ストアド プロシージャ アクティビティを使用します。 |

## <a name="error-code-sqlinvaliddbquerystring"></a>エラー コード:SqlInvalidDbQueryString

- **メッセージ**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **原因**:指定された SQL クエリは無効です。 クエリがデータを返さないという原因が考えられます。

- **推奨事項**:SQL ツールを使用して SQL クエリを検証してください。 クエリがデータを返せることを確認してください。


## <a name="error-code-sqlinvalidcolumnname"></a>エラー コード:SqlInvalidColumnName

- **メッセージ**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **原因**:構成が正しくない可能性があるため、列が見つかりません。

- **推奨事項**:クエリの列、データセットの "*構造*"、およびアクティビティの "*マッピング*" を検証してください。


## <a name="error-code-sqlbatchwritetimeout"></a>エラー コード:SqlBatchWriteTimeout

- **メッセージ**: `Timeouts in SQL write operation.`

- **原因**:この問題は、SQL Database の一時的な障害が原因である可能性があります。

- **推奨事項**:操作を再試行してください。 問題が解決しない場合は、Azure SQL サポートにお問い合わせください。


## <a name="error-code-sqlbatchwritetransactionfailed"></a>エラー コード:SqlBatchWriteTransactionFailed

- **メッセージ**: `SQL transaction commits failed.`

- **原因**:例外の詳細が常にトランザクションのタイムアウトを示す場合は、統合ランタイムとデータベース間のネットワーク待機時間が、既定のしきい値の 30 秒を超えています。

- **推奨事項**:SQL リンク サービス接続文字列を、120 以上の "*接続タイムアウト*" 値を使用して更新し、アクティビティを再実行します。

- **原因**:SQL 接続が切断されているという例外の詳細が断続的に示される場合は、一時的なネットワーク障害または SQL Database 側の問題である可能性があります。

- **推奨事項**:アクティビティを再試行し、SQL Database 側のメトリックを確認してください。


## <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>エラー コード:SqlBulkCopyInvalidColumnLength

- **メッセージ**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **原因**:一括コピー プログラム ユーティリティ (bcp) クライアントから無効な列長を受け取ったため、SQL 一括コピーに失敗しました。

- **推奨事項**:問題が発生した行を特定するには、Copy アクティビティのフォールト トレランス機能を有効にします。 これにより、問題のある行がストレージにリダイレクトされ、さらに調査できるようになります。 詳細については、[コピー アクティビティのフォールト トレランス](./copy-activity-fault-tolerance.md)に関する記事を参照してください。


## <a name="error-code-sqlconnectionisclosed"></a>エラー コード:SqlConnectionIsClosed

- **メッセージ**: `The connection is closed by SQL Database.`

- **原因**:SQL 接続は、同時実行が多い場合に SQL データベースによって閉じられ、サーバーは接続を終了します。

- **推奨事項**:接続を再試行します。 問題が解決しない場合は、Azure SQL サポートにお問い合わせください。

## <a name="error-code-sqlserverinvalidlinkedservicecredentialmissing"></a>エラー コード: SqlServerInvalidLinkedServiceCredentialMissing

- **メッセージ**: `The SQL Server linked service is invalid with its credential being missing.`

- **原因**: リンク サービスが適切に構成されていません。

- **推奨事項**: SQL サーバーのリンク サービスを検証して修正します。 

## <a name="error-code-sqlparallelfailedtodetectpartitioncolumn"></a>エラー コード: SqlParallelFailedToDetectPartitionColumn

- **メッセージ**: `Failed to detect the partition column with command '%command;', %message;.`

- **原因**: テーブルに主キーまたは一意のキーがありません。

- **推奨事項**: テーブルを確認して、主キーまたは一意のインデックスが作成されていることを確認します。 

## <a name="error-code-sqlparallelfailedtodetectphysicalpartitions"></a>エラー コード: SqlParallelFailedToDetectPhysicalPartitions

- **メッセージ**: `Failed to detect the physical partitions with command '%command;', %message;.`

- **原因**: テーブルの物理パーティションが作成されていません。 データベースを確認してください。

- **推奨事項**: この問題を解決するには、「[パーティション テーブルとパーティション インデックスの作成](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes?view=sql-server-ver15&preserve-view=true)」を参照してください。

## <a name="error-code-sqlparallelfailedtogetpartitionrangesynapse"></a>エラー コード: SqlParallelFailedToGetPartitionRangeSynapse

- **メッセージ**: `Failed to get the partitions for azure synapse with command '%command;', %message;.`

- **原因**: テーブルの物理パーティションが作成されていません。 データベースを確認してください。

- **推奨事項**: この問題を解決するには、「[専用 SQL プールでのテーブルのパーティション分割](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-partition.md)」を参照してください。

## <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>エラー メッセージ: 文字列から uniqueidentifier に変換中、変換が失敗しました。

- **現象**:ステージング コピーおよび PolyBase を使用して、(SQL Server などの) 表形式のデータ ソースから Azure Synapse Analytics にデータをコピーすると、次のエラーが発生します。

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **原因**:Azure Synapse Analytics PolyBase では、空の文字列を GUID に変換することはできません。

- **解決方法**:Copy アクティビティ シンクの PolyBase の設定で、 **[既定の種類を使用します]** オプションを *false* に設定します。


## <a name="error-message-expected-data-type-decimalxx-offending-value"></a>エラー メッセージ:予期されたデータ型: DECIMAL(x,x)、問題のある値

- **現象**:ステージング コピーおよび PolyBase を使用して、(SQL Server などの) 表形式のデータ ソースから Azure Synapse Analytics にデータをコピーすると、次のエラーが発生します。

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **原因**:Azure Synapse Analytics の PolyBase では、空の文字列 (null 値) を decimal 型の列に挿入することはできません。

- **解決方法**:Copy アクティビティ シンクの PolyBase の設定で、 **[既定の種類を使用します]** オプションを false に設定します。


## <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>エラー メッセージ:Java の例外メッセージ:HdfsBridge::CreateRecordReader

- **現象**:PolyBase を使用して Azure Synapse Analytics にデータをコピーすると、次のエラーが発生します。

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **原因**:この原因として、スキーマ (列幅の合計) が大きすぎる (1 MB を超える) ことが考えられます。 すべての列のサイズを追加して、ターゲットの Azure Synapse Analytics テーブルのスキーマを確認します。

    - Int = 4 バイト
    - Bigint = 8 バイト
    - Varchar(n)、char(n)、binary(n)、varbinary(n) = n バイト
    - Nvarchar(n)、nchar(n) = n*2 バイト
    - Date = 6 バイト
    - Datetime/(2)、smalldatetime = 16 バイト
    - Datetimeoffset = 20 バイト
    - Decimal = 19 バイト
    - Float = 8 バイト
    - Money = 8 バイト
    - Smallmoney = 4 バイト
    - Real = 4 バイト
    - Smallint = 2 バイト
    - Time = 12 バイト
    - Tinyint = 1 バイト

- **解決方法**: 
    - 列幅を 1 MB 未満に縮小します。
    - または、PolyBase を無効にして一括挿入アプローチを使用します。


## <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>エラー メッセージ:HTTP 条件ヘッダーを使用して指定した条件が満たされません。

- **現象**:SQL クエリを使用して Azure Synapse Analytics からデータをプルすると、次のエラーが発生します。

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **原因**:Azure Storage で外部テーブルのクエリを実行中に、Azure Synapse Analytics で問題が発生しました。

- **解決方法**:SQL Server Management Studio (SSMS) で同じクエリを実行し、同じ結果が得られるかどうかを確認します。 表示される場合は、Azure Synapse Analytics に対するサポート チケットを作成し、ご利用の Azure Synapse Analytics サーバーとデータベース名を入力します。


## <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>パフォーマンス レベルが低いため、コピーに失敗します

- **現象**:Azure SQL Database にデータをコピーすると、次のエラーが表示されます。`Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **原因**:Azure SQL Database s1 は入出力 (I/O) の制限に達しています。

- **解決方法**:問題を修正するには、Azure SQL Database のパフォーマンス レベルをアップグレードします。 


## <a name="sql-table-cant-be-found"></a>SQL テーブルが見つかりません 

- **現象**:ハイブリッドからデータをオンプレミスの SQL Server テーブルにコピーすると、次のエラーが表示されます。`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **原因**:現在の SQL アカウントには、.NET SqlBulkCopy.WriteToServer によって発行された要求を実行するための十分なアクセス許可がありません。

- **解決方法**:より高い特権の SQL アカウントに切り替えます。


## <a name="error-message-string-or-binary-data-is-truncated"></a>エラー メッセージ:文字列またはバイナリ データが切り捨てられました

- **現象**:オンプレミスの Azure SQL Server テーブルにデータをコピーすると、エラーが発生します。 

- **原因**:Cx SQL テーブル スキーマ定義に、想定よりも短い列が 1 つ以上あります。

- **解決方法**:この問題を解決するには、次の方法を試してください。

    1. 問題が発生している行のトラブルシューティングを行うには、SQL シンクの[フォールト トレランス](./copy-activity-fault-tolerance.md)を適用します。特に "redirectIncompatibleRowSettings" を適用します。

        > [!NOTE]
        > フォールト トレランスには、追加の実行時間が必要になる場合があります。これにより、コストが高くなる可能性があります。

    2. SQL テーブル スキーマ列の長さでリダイレクトされたデータを再確認し、更新が必要な列を調べます。

    3. それに応じてテーブル スキーマを更新します。

## <a name="error-code-faileddboperation"></a>エラー コード: FailedDbOperation

- **メッセージ**: `User does not have permission to perform this action.`

- **推奨事項**: PolyBase を使用してデータを読み込むときに、Azure Synapse Analytics コネクタで構成されているユーザーが、ターゲット データベースに対する 'CONTROL' アクセス許可を持っていることを確認します。 詳しくは、[こちらの文書](./connector-azure-sql-data-warehouse.md#required-database-permission)を参照してください。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

- [コネクタのトラブルシューティング ガイド](connector-troubleshoot-guide.md)
- [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
- [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
- [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
