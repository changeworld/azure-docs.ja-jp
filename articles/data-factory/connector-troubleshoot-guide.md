---
title: Azure Data Factory コネクタのトラブルシューティング
description: Azure Data Factory でのコネクタに関する問題のトラブルシューティングを行う方法について説明します。
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 12/30/2020
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: e6591762ed6a7e2b462a209730276f3198d86ae8
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821470"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Azure Data Factory コネクタのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコネクタの一般的なトラブルシューティング方法について説明します。
  
## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code-azurebloboperationfailed"></a>エラー コード:AzureBlobOperationFailed

- **メッセージ**: `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **原因**:BLOB ストレージ操作で問題が発生しました。

- **推奨事項**:エラーの詳細を確認してください。 BLOB のヘルプ ドキュメントを参照してください: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes 。 支援が必要な場合は、ストレージ チームにお問い合わせください。


### <a name="invalid-property-during-copy-activity"></a>コピー アクティビティ中プロパティが無効です

- **メッセージ**: `Copy activity <Activity Name> has an invalid "source" property. The source type is not compatible with the dataset <Dataset Name> and its linked service <Linked Service Name>. Please verify your input against.`

- **原因**:データセットで定義された型が、コピー アクティビティで定義されているソース/シンクの種類と一致しません。

- **解決方法**:データセットまたはパイプライン JSON 定義を編集して、型の一貫性を保ち、デプロイを再実行してください。


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>エラー メッセージ: 要求のサイズが大きすぎます。

- **現象**: 既定の書き込みバッチ サイズを使用して Azure Cosmos DB にデータをコピーすると、 *"**要求のサイズが大きすぎます**"* というエラーが表示されます。

- **原因**: Cosmos DB では、単一の要求のサイズが 2 MB に制限されます。 式は、"要求サイズ = 1 つのドキュメント サイズ * 書き込みバッチ サイズ" となります。 ドキュメントのサイズが大きい場合、既定の動作の結果として要求サイズが過度に大きくなります。 書き込みバッチ サイズを調整することができます。

- **解決方法**: コピー アクティビティのシンクで、"書き込みバッチ サイズ" の値を小さくします (既定値は 10000)。

### <a name="error-message-unique-index-constraint-violation"></a>エラー メッセージ: 一意なインデックスの制約違反

- **現象**: Cosmos DB にデータをコピーすると、次のエラーが発生します。

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **原因**:原因として、次の 2 つが考えられます。

    - 書き込み動作として **Insert** を使用する場合、このエラーは、ソース データに同じ ID を持つ行またはオブジェクトが存在することを意味します。
    - 書き込み動作として **Upsert** を使用し、コンテナーに別の一意のキーを設定した場合、このエラーは、ソース データの行およびオブジェクトの ID はそれぞれ異なりますが、定義された一意のキーとして同じ値が存在することを意味します。

- **解決方法**: 

    - 原因 1 の場合は、書き込み動作として **Upsert** を設定します。
    - 原因 2 の場合は、各ドキュメントの定義された一意のキーの値がそれぞれ異なることを確認してください。

### <a name="error-message-request-rate-is-large"></a>エラー メッセージ:要求率が大きいです

- **現象**: Cosmos DB にデータをコピーすると、次のエラーが発生します。

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **原因**: 使用されている要求ユニットが、Cosmos DB で構成されている使用可能な RU を超えています。 Cosmos DB での RU の計算方法については、[こちら](../cosmos-db/request-units.md#request-unit-considerations)を参照してください。

- **解決方法**: 次に示す 2 つのソリューションがあります。

    - Cosmos DB での値が大きくなるように、**コンテナー RU を増やします**。これにより、コピー アクティビティのパフォーマンスは向上しますが、Cosmos DB でのコストは大きくなります。 
    - **writeBatchSize** をより小さい値 (1000 など) に減らし、**parallelCopies** を 1 などのより小さい値に設定します。これにより、コピーの実行パフォーマンスは現在よりも低下しますが、Cosmos DB でのコストは増加しません。

### <a name="column-missing-in-column-mapping"></a>列マッピングでの列の欠落

- **現象**: 列マッピングのために Cosmos DB のスキーマをインポートするときに、一部の列が欠落しています。 

- **原因**: ADF では、最初の 10 の Cosmos DB ドキュメントからスキーマが推論されます。 そのようなドキュメントに値を持たない列またはプロパティがある場合、それらは ADF によって検出されないため、表示されません。

- **解決方法**: 次のようにクエリをチューニングして、空の値の場合でも結果セットに列が表示されるようにすることができます (想定: 最初の 10 のドキュメント内で "impossible" 列が欠落しています)。 あるいは、マッピング用の列を手動で追加することもできます。

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>エラー メッセージ:リーダーの GuidRepresentation が CSharpLegacy です

- **現象**: UUID フィールドを使用して Cosmos DB MongoAPI/MongoDB からのデータをコピーすると、次のエラーが発生します。

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **原因**:BSON で UUID を表すには、UuidStardard と UuidLegacy という 2 つの方法があります。 既定では、UuidLegacy はデータの読み取りに使用されます。 MongoDB 内の UUID データが UuidStandard の場合は、エラーが発生します。

- **解決方法**: MongoDB 接続文字列に、オプション "**uuidRepresentation=standard**" を追加します。 詳細については、[MongoDB 接続文字列](connector-mongodb.md#linked-service-properties)に関する記事を参照してください。
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL API)

### <a name="error-code--cosmosdbsqlapioperationfailed"></a>エラー コード:CosmosDbSqlApiOperationFailed

- **メッセージ**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **原因**:CosmosDbSqlApi 操作で問題が発生しました。

- **推奨事項**:エラーの詳細を確認してください。 [CosmosDb のヘルプ ドキュメント](https://docs.microsoft.com/azure/cosmos-db/troubleshoot-dot-net-sdk)を参照してください。 支援が必要な場合は、CosmosDb チームにお問い合わせください。


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>エラー メッセージ:基になる接続が閉じられました。SSL/TLS のセキュリティで保護されているチャネルに対する信頼関係を確立できませんでした。

- **現象**:コピー アクティビティが次のエラーにより失敗します。 

    ```
    Message: ErrorCode = `UserErrorFailedFileOperation`, Error Message = `The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`.
    ```

- **原因**:TLS ハンドシェイク中に証明書の検証に失敗しました。

- **解決方法**:対処法:ADLS Gen1 の TLS 検証をスキップするには、段階的なコピーを使用します。 この問題を再現し、netmon トレースを収集してから、ネットワーク チームに連絡し、ローカル ネットワーク構成を確認する必要があります。

    ![ADLS Gen1 のトラブルシューティング](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>エラー メッセージ:リモート サーバーがエラー(403) 禁止されています

- **現象**: コピー アクティビティが次のエラーにより失敗します。 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **原因**: 原因の 1 つとして、使用するサービス プリンシパルまたはマネージド ID に、特定のフォルダーまたはファイルにアクセスするためのアクセス許可が付与されていないことが考えられます。

- **解決方法**: コピーする必要のあるフォルダーとサブフォルダーのすべてに対して、対応するアクセス許可を付与します。 [こちらのドキュメント](connector-azure-data-lake-store.md#linked-service-properties)をご覧ください。

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>エラー メッセージ: サービス プリンシパルを使用してアクセス トークンを取得できませんでした。 ADAL エラー: service_unavailable

- **現象**: コピー アクティビティが次のエラーにより失敗します。

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **原因**:Azure Active Directory によって所有されているサービス トークン サーバー (STS) が使用できる場合 (つまり、ビジー状態のため要求を処理できない場合)、HTTP エラー 503 が返されます。 

- **解決方法**: 数分後にコピー アクティビティを再実行します。


## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>エラー コード:ADLSGen2OperationFailed

- **メッセージ**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **原因**:ADLS Gen2 は、操作が失敗したことを示すエラーをスローします。

- **推奨事項**:ADLS Gen2 によってスローされた詳細なエラー メッセージを確認してください。 一時的な障害が原因の場合は、再試行してください。 さらに支援が必要な場合は、Azure Storage サポートにお問い合わせください。その際、エラー メッセージに含まれる要求 ID をお知らせください。

- **原因**:エラー メッセージに "Forbidden" が含まれている場合、お使いのサービスプリンシパルまたはマネージド ID に、ADLS Gen2 にアクセスするための十分なアクセス許可がない可能性があります。

- **推奨事項**:ヘルプ ドキュメントを参照してください: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication 。

- **原因**:エラー メッセージに "InternalServerError" が含まれている場合、エラーは ADLS Gen2 によって返されたものです。

- **推奨事項**:一時的な障害が原因の可能性があります。再試行してください。 問題が解決しない場合は、Azure Storage サポートにお問い合わせください。その際、エラー メッセージに含まれる要求 ID をお知らせください。

### <a name="request-to-adls-gen2-account-met-timeout-error"></a>ADLS Gen2 アカウントへの要求でタイムアウト エラーが発生しました

- **メッセージ**:エラー コード = `UserErrorFailedBlobFSOperation`、エラー メッセージ = `BlobFS operation failed for: A task was canceled`。

- **原因**:この問題は、ADLS Gen2 シンク タイムアウト エラーが原因であり、これは、主にセルフホステッド IR マシンで発生します。

- **推奨事項**: 

    - 可能であれば、セルフホステッド IR マシンとターゲット ADLS Gen2 アカウントを同じリージョンに配置します。 これにより、ランダム タイムアウト エラーを回避し、パフォーマンスを向上させることができます。

    - ExpressRoute などの特別なネットワーク設定があるかどうかを調べ、ネットワークに十分な帯域幅があることを確認します。 全体的な帯域幅が低い場合は、セルフホステッド IR 同時実行ジョブ設定を小さくすることをお勧めします。これにより、複数の同時実行ジョブでのネットワーク リソースの競合を回避できます。

    - ファイル サイズが中くらいまたは小さい場合に、このようなタイムアウト エラーを軽減するには、バイナリ以外のコピーにはさらに小さいブロック サイズを使用します。 [Blob Storage Put Block](https://docs.microsoft.com/rest/api/storageservices/put-block) を参照してください。

       カスタム ブロック サイズを指定するには、.json エディターでプロパティを編集できます。
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-file-storage"></a>Azure File Storage

### <a name="error-code--azurefileoperationfailed"></a>エラー コード:AzureFileOperationFailed

- **メッセージ**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **原因**:Azure ファイル ストレージ操作で問題が発生しました。

- **推奨事項**:エラーの詳細を確認してください。 Azure ファイルのヘルプ ドキュメント https://docs.microsoft.com/rest/api/storageservices/file-service-error-codes を参照してください。 支援が必要な場合は、ストレージ チームにお問い合わせください。


## <a name="azure-synapse-analyticsazure-sql-databasesql-server"></a>Azure Synapse Analytics/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>エラー コード:SqlFailedToConnect

- **メッセージ**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **原因**:Azure SQL:エラー メッセージに "SqlErrorNumber=47073" が含まれている場合、接続設定で公衆ネットワーク アクセスが拒否されていることを意味します。

- **推奨事項**:Azure SQL ファイアウォールで、[Deny public network access]\(公衆ネットワーク アクセスを拒否する\) オプションを [いいえ] に設定します。 詳細については、https://docs.microsoft.com/azure/azure-sql/database/connectivity-settings#deny-public-network-accessを参照してください。

- **原因**:Azure SQL:エラー メッセージに "SqlErrorNumber=[エラー コード]" などの SQL エラー コードが含まれている場合、Azure SQL トラブルシューティング ガイドを参照してください。

- **推奨事項**:詳細については、 https://docs.microsoft.com/azure/azure-sql/database/troubleshoot-common-errors-issues を参照してください。

- **原因**:ポート 1433 がファイアウォールの許可リストに含まれているかどうかを確認します。

- **推奨事項**:リファレンス ドキュメント https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by- を参照してください。

- **原因**:エラー メッセージに "SqlException" が含まれている場合、SQL Database は、何らかの特定の操作が失敗したことを示すエラーをスローします。

- **推奨事項**:詳細については、次の参照ドキュメントで SQL エラー コードを検索してください: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors 。 さらに支援が必要な場合は、Azure SQL サポートにお問い合わせください。

- **原因**:これが一時的な問題である場合 (たとえば、不安定なネットワーク接続など)、アクティビティ ポリシーに再試行を追加して軽減してください。

- **推奨事項**:リファレンス ドキュメント https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities#activity-policy を参照してください。

- **原因**:"Client with IP address '...' is not allowed to access the server" (IP アドレスが '...' のクライアントはサーバーへのアクセスが許可されていません) という内容がエラー メッセージに含まれており、Azure SQL データベースに接続しようとしている場合、その原因は通常、Azure SQL データベースのファイアウォールの問題です。

- **推奨事項**:Azure SQL Server のファイアウォールの構成で、"Allow Azure services and resources to access this server" (このサーバーへのアクセスを Azure のサービスとリソースに許可する) オプションを有効にします。 参照ドキュメント: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure 。


### <a name="error-code--sqloperationfailed"></a>エラー コード:SqlOperationFailed

- **メッセージ**: `A database operation failed. Please search error to get more details.`

- **原因**:エラー メッセージに "SqlException" が含まれている場合、SQL Database は、何らかの特定の操作が失敗したことを示すエラーをスローします。

- **推奨事項**:SQL エラーが明確でない場合は、データベースを最新の互換性レベル '150' に変更してみてください。 最新バージョンの SQL エラーをスローすることができます。 [詳細なドキュメント](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat)を参照してください。

    SQL の問題のトラブルシューティングの場合、詳細については、次の参照ドキュメントで SQL エラー コードを検索してください: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors 。 さらに支援が必要な場合は、Azure SQL サポートにお問い合わせください。

- **原因**:エラー メッセージに "PdwManagedToNativeInteropException" が含まれている場合、通常、ソースとシンクで列のサイズが一致しないことが原因です。

- **推奨事項**:ソースの列とシンクの列の両方のサイズを確認してください。 さらに支援が必要な場合は、Azure SQL サポートにお問い合わせください。

- **原因**:エラー メッセージに "InvalidOperationException" が含まれている場合、通常、無効な入力データが原因です。

- **推奨事項**:問題が発生している行を特定するには、コピー アクティビティでフォールト トレランス機能を有効にしてください。これにより、問題のある行をストレージにリダイレクトして、さらに調査することができます。 参照ドキュメント: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance 。


### <a name="error-code--sqlunauthorizedaccess"></a>エラー コード:SqlUnauthorizedAccess

- **メッセージ**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **原因**:資格情報が正しくないか、ログイン アカウントが SQL Database にアクセスできません。

- **推奨事項**:SQL Database にアクセスするための十分なアクセス許可がログイン アカウントにあることを確認してください。


### <a name="error-code--sqlopenconnectiontimeout"></a>エラー コード:SqlOpenConnectionTimeout

- **メッセージ**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **原因**:SQL Database の一時的な障害の可能性があります。

- **推奨事項**:接続タイムアウトの値を大きくして、リンクされたサービス接続文字列の更新を再試行してください。


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>エラー コード:SqlAutoCreateTableTypeMapFailed

- **メッセージ**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **原因**:自動作成テーブルがソースの要件を満たすことができません。

- **推奨事項**:"マッピング" で列のデータ型を更新するか、ターゲット サーバーで手動でシンク テーブルを作成してください。


### <a name="error-code--sqldatatypenotsupported"></a>エラー コード:SqlDataTypeNotSupported

- **メッセージ**: `A database operation failed. Check the SQL errors.`

- **原因**:SQL ソースで問題が発生し、エラーが SqlDateTime オーバーフローに関連している場合、データ値が論理型の範囲 (1/1/1753 12:00:00 AM - 12/31/9999 11:59:59 PM) を超えています。

- **推奨事項**:ソースの SQL クエリで型を文字列にキャストするか、コピー アクティビティの列マッピングで列の型を "String" に変更してください。

- **原因**:SQL シンクで問題が発生し、エラーが SqlDateTime オーバーフローに関連している場合、データ値がシンク テーブルの許容範囲を超えています。

- **推奨事項**:シンク テーブルで、対応する列の型を "datetime2" 型に更新してください。


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>エラー コード:SqlInvalidDbStoredProcedure

- **メッセージ**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **原因**:指定されたストアド プロシージャは無効です。 ストアド プロシージャが何もデータを返さないことが原因である可能性があります。

- **推奨事項**:SQL ツールによってストアド プロシージャを検証してください。 ストアド プロシージャがデータを返せることを確認してください。


### <a name="error-code--sqlinvaliddbquerystring"></a>エラー コード:SqlInvalidDbQueryString

- **メッセージ**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **原因**:指定された SQL クエリは無効です。 クエリが何もデータを返さないことが原因である可能性があります

- **推奨事項**:SQL ツールによって SQL クエリを検証してください。 クエリがデータを返せることを確認してください。


### <a name="error-code--sqlinvalidcolumnname"></a>エラー コード:SqlInvalidColumnName

- **メッセージ**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **原因**:列が見つかりません。 構成が不適切な可能性があります。

- **推奨事項**:クエリの列、データセットの "構造"、およびアクティビティの "マッピング" を検証してください。


### <a name="error-code--sqlbatchwritetimeout"></a>エラー コード:SqlBatchWriteTimeout

- **メッセージ**: `Timeouts in SQL write operation.`

- **原因**:SQL Database の一時的な障害の可能性があります。

- **推奨事項**:再試行してください。 問題が解決しない場合は、Azure SQL サポートにお問い合わせください。


### <a name="error-code--sqlbatchwritetransactionfailed"></a>エラー コード:SqlBatchWriteTransactionFailed

- **メッセージ**: `SQL transaction commits failed`

- **原因**:例外の詳細が常にトランザクションのタイムアウトを示している場合は、統合ランタイムとデータベース間のネットワーク待機時間が既定のしきい値の 30 秒を超えています。

- **推奨事項**:'接続タイムアウト' の値を 120 以上にして SQL のリンクされたサービス接続文字列を更新し、アクティビティを再実行してください。

- **原因**:例外の詳細が sqlconnection の破損を断続的に示している場合は、単に一時的なネットワーク障害または SQL Database 側の問題である可能性があります

- **推奨事項**:アクティビティを再試行し、SQL Database 側のメトリックを確認してください。


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>エラー コード:SqlBulkCopyInvalidColumnLength

- **メッセージ**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **原因**:BCP クライアントから無効な列の長さを受信したため、SQL 一括コピーが失敗しました。

- **推奨事項**:問題が発生している行を特定するには、コピー アクティビティでフォールト トレランス機能を有効にしてください。これにより、問題のある行をストレージにリダイレクトして、さらに調査することができます。 参照ドキュメント: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance 。


### <a name="error-code--sqlconnectionisclosed"></a>エラー コード:SqlConnectionIsClosed

- **メッセージ**: `The connection is closed by SQL Database.`

- **原因**:同時実行数が多いときに SQL 接続が SQL Database によって閉じられ、サーバーが接続を終了します。

- **推奨事項**:リモート サーバーで SQL 接続を閉じます。 再試行してください。 問題が解決しない場合は、Azure SQL サポートにお問い合わせください。


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>エラー メッセージ: 文字列から uniqueidentifier に変換中、変換が失敗しました。

- **現象**:ステージング コピーおよび PolyBase を使用して、(SQL Server などの) 表形式のデータ ソースから Azure Synapse Analytics にデータをコピーすると、次のエラーが発生します。

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **原因**:Azure Synapse Analytics の PolyBase によって、空の文字列を GUID に変換できません。

- **解決方法**:コピー アクティビティ シンクの PolyBase の設定で、"**使用型の既定の**" オプションを false に設定します。


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>エラー メッセージ:予期されたデータ型: DECIMAL(x,x)、問題のある値

- **現象**:ステージング コピーおよび PolyBase を使用して、(SQL Server などの) 表形式のデータ ソースから Azure Synapse Analytics にデータをコピーすると、次のエラーが発生します。

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **原因**:Azure Synapse Analytics の PolyBase では、空の文字列 (null 値) を decimal 型の列に挿入することはできません。

- **解決方法**:コピー アクティビティ シンクの PolyBase の設定で、"**使用型の既定の**" オプションを false に設定します。


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>エラー メッセージ:Java の例外メッセージ:HdfsBridge::CreateRecordReader

- **現象**:PolyBase を使用して Azure Synapse Analytics にデータをコピーすると、次のエラーが発生します。

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **原因**: 原因として、スキーマ (列の合計幅) が大きすぎる (1 MB を超えている) ことが考えられます。 すべての列のサイズを追加して、ターゲットの Azure Synapse Analytics テーブルのスキーマを確認します。

    - Int -> 4 バイト
    - Bigint-> 8 バイト
    - Varchar(n)、char(n)、binary(n)、varbinary(n) -> n バイト
    - Nvarchar(n)、nchar(n) -> n*2 バイト
    - Date -> 6 バイト
    - Datetime/(2)、smalldatetime -> 16 バイト
    - Datetimeoffset -> 20 バイト
    - Decimal -> 19 バイト
    - Float -> 8 バイト
    - Money -> 8 バイト
    - Smallmoney -> 4 バイト
    - Real -> 4 バイト
    - Smallint -> 2 バイト
    - Time -> 12 バイト
    - Tinyint -> 1 バイト

- **解決方法**: 
    - 列幅を 1 MB 未満に縮小します。
    - または、PolyBase を無効にして一括挿入アプローチを使用します。


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>エラー メッセージ:HTTP 条件ヘッダーを使用して指定した条件が満たされません。

- **現象**:SQL クエリを使用して Azure Synapse Analytics からデータをプルすると、次のエラーが発生します。

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **原因**:Azure Synapse Analytics では、Azure Storage 内の外部テーブルに対するクエリに関する問題が発生しています。

- **解決方法**:SSMS で同じクエリを実行し、同じ結果が表示されるかどうかを確認します。 表示される場合は、Azure Synapse Analytics に対するサポート チケットを作成し、ご利用の Azure Synapse Analytics サーバーとデータベース名を入力してさらにトラブルシューティングを行います。
            

### <a name="low-performance-when-load-data-into-azure-sql"></a>データを Azure SQL に読み込むときにパフォーマンスが低下します

- **現象**:Azure SQL にデータをコピーすると、低速になります。

- **原因**:問題の根本原因は、ほとんどの場合、Azure SQL 側のボトルネックによってトリガーされます。 以下のいくつかの原因が考えられます。

    - Azure DB 層が十分高くありません。

    - Azure DB の DTU 使用率はほぼ 100% です。 [パフォーマンスを監視](https://docs.microsoft.com/azure/azure-sql/database/monitor-tune-overview)して、DB 層をアップグレードすることを検討できます。

    - インデックスが正しく設定されていません。 データが読み込まれる前にすべてのインデックスを削除し、読み込みの完了後に再作成します。

    - WriteBatchSize は、スキーマ行のサイズに適合するのに十分な大きさではありません。 問題のプロパティを拡大してみてください。

    - 一括埋め込みではなく、ストアド プロシージャが使用されているため、パフォーマンスが低下することが予想されます。 

- **解決方法**:[コピー アクティビティのパフォーマンス](https://docs.microsoft.com/azure/data-factory/copy-activity-performance-troubleshooting)については、TSG を参照してください


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>パフォーマンス レベルが低いため、コピーに失敗します

- **現象**:Azure SQL にデータをコピーするときに、次のエラー メッセージが表示されます: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **原因**:Azure SQL s1 が使用されており、この場合、IO 限度に達しています。

- **解決方法**:問題を修正するには、Azure SQL のパフォーマンス レベルをアップグレードします。 


### <a name="sql-table-cannot-be-found"></a>SQL テーブルが見つかりません 

- **現象**:ハイブリッドからオンプレミスの SQL Server テーブルにデータをコピーするときに次のエラーが発生します: `Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **原因**:現在の SQL アカウントには、.NET SqlBulkCopy.WriteToServer によって発行された要求を実行するための十分なアクセス許可がありません。

- **解決方法**:より高い特権の SQL アカウントに切り替えます。


### <a name="error-message-string-or-binary-data-would-be-truncated"></a>エラー メッセージ:文字列データまたはバイナリ データが切り捨てられます

- **現象**:オンプレミス/Azure SQL Server テーブルにデータをコピーするときに、エラーが発生します。 

- **原因**:Cx Sql テーブル スキーマ定義には、想定よりも短い列が 1 つ以上あります。

- **解決方法**:問題を修正するには、次の手順を試してください。

    1. SQL シンクの[フォールト トレランス](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance)を適用します。特に "redirectIncompatibleRowSettings" を適用して、問題が発生している行をトラブルシューティングします。

        > [!NOTE]
        > フォールト トレランスによって実行時間が長くなる可能性があり、そのためにコストが高くなる可能性があることに注意してください。

    2. SQL テーブル スキーマ列の長さでリダイレクトされたデータを再確認し、更新が必要な列を調べます。

    3. それに応じてテーブル スキーマを更新します。


## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="error-code--azuretableduplicatecolumnsfromsource"></a>エラー コード:AzureTableDuplicateColumnsFromSource

- **メッセージ**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink`

- **原因**:これは、結合を使用した SQL クエリ、または非構造化 csv ファイルでは、一般的である可能性があります

- **推奨事項**: ソース列を再確認し、必要に応じて修正します。


## <a name="db2"></a>DB2

### <a name="error-code--db2driverrunfailed"></a>エラー コード:DB2DriverRunFailed

- **メッセージ**: `Error thrown from driver. Sql code: '%code;'`

- **原因**:エラー メッセージに "SQLSTATE=51002 SQLCODE=-805" が含まれている場合は、ドキュメント https://docs.microsoft.com/azure/data-factory/connector-db2#linked-service-properties のヒントを参照してください。

- **推奨事項**:プロパティ "packageCollection" で "NULLID" を設定してみてください


## <a name="delimited-text-format"></a>区切りテキスト形式

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>エラー コード:DelimitedTextColumnNameNotAllowNull

- **メッセージ**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **原因**:アクティビティに 'firstRowAsHeader' を設定すると、最初の行が列名として使用されます。 このエラーは、最初の行に空の値が含まれていることを意味します。 次に例を示します。'ColumnA, ColumnB'。

- **推奨事項**:最初の行を確認し、空の値がある場合は値を修正してください。


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>エラー コード:DelimitedTextMoreColumnsThanDefined

- **メッセージ**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **原因**:問題のある行の列数が、最初の行の列数を超えています。 データの問題、または列区切り記号/引用符の文字設定が正しくないことが原因である可能性があります。

- **推奨事項**:エラー メッセージの行数を取得し、行の列を確認して、データを修正してください。

- **原因**:予想される列数がエラー メッセージで "1" の場合、おそらく指定した圧縮またはフォーマット設定が正しくありません。 そのため、ADF によってファイルが正しく解析されませんでした。

- **推奨事項**:フォーマット設定を調べて、ソース ファイルと一致していることを確認してください。

- **原因**:ソースがフォルダーの場合は、指定されたフォルダーにあるファイルのスキーマが異なる可能性があります。

- **推奨事項**:指定されたフォルダーにあるファイルのスキーマが同じであることを確認してください。


## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>エラー コード:DynamicsCreateServiceClientError

- **メッセージ**: `This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **原因**:これは、Dynamics サーバー側の一時的な問題です。

- **推奨事項**:パイプラインを再実行してください。 引き続き失敗する場合は、並列処理を減らしてください。 それでも失敗する場合は、Dynamics のサポートにお問い合わせください。


### <a name="columns-are-missing-when-previewingimporting-schema"></a>スキーマのプレビュー/インポート時に列が見つかりません

- **現象**:スキーマのインポート時またはデータのプレビュー時に、一部の列が欠落していることがわかりました。 エラー メッセージ: `The valid structure information (column name and type) are required for Dynamics source.`

- **原因**:ADF は最初の 10 のレコードに値がない列を表示できないので、この問題は基本的に設計上のものです。 追加した列の形式が正しいことを確認します。 

- **推奨事項**:[マッピング] タブで列を手動で追加します。


### <a name="error-code--dynamicsmissingtargetformultitargetlookupfield"></a>エラー コード:DynamicsMissingTargetForMultiTargetLookupField

- **メッセージ**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **原因**:ターゲット列が、ソースまたは列マッピングに存在しません。

- **推奨事項**:1. ソースにターゲット列が含まれていることを確認します。 2. 列マッピングにターゲット列を追加します。 必ずシンク列を "{fieldName}@EntityReference" のパターンにします。


### <a name="error-code--dynamicsinvalidtargetformultitargetlookupfield"></a>エラー コード:DynamicsInvalidTargetForMultiTargetLookupField

- **メッセージ**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;"`

- **原因**:複数ターゲット検索フィールドのターゲット エンティティとして不正なエンティティ名が指定されています。

- **推奨事項**:複数ターゲット検索フィールドの有効なエンティティ名を指定します。


### <a name="error-code--dynamicsinvalidtypeformultitargetlookupfield"></a>エラー コード:DynamicsInvalidTypeForMultiTargetLookupField

- **メッセージ**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **原因**:ターゲット列の値が文字列ではありません

- **推奨事項**:複数ターゲット検索フィールドに有効な文字列を指定します。


### <a name="error-code--dynamicsfailedtorequetserver"></a>エラー コード:DynamicsFailedToRequetServer

- **メッセージ**: `The dynamics server or the network is experiencing issues. Check network connectivity or check dynamics server log for more details.`

- **原因**:Dynamics サーバーが不安定であるか、またはアクセスできません。または、ネットワークで問題が発生しています。

- **推奨事項**:ネットワーク接続を確認してください。詳細については、Dynamics サーバーのログを確認してください。 さらに支援が必要な場合は、Dynamics サポートにお問い合わせください。


## <a name="excel-format"></a>Excel 形式

### <a name="timeout-or-slow-performance-when-parsing-large-excel-file"></a>大きな Excel ファイルを解析するときのタイムアウトまたはパフォーマンスの低下

- **現象**:

    - Excel データセットの作成、接続またはストアからのスキーマのインポート、データのプレビュー、ワークシートの一覧表示または更新を行う際、Excel ファイルのサイズが大きい場合は、タイムアウト エラーが発生することがあります。

    - コピー アクティビティを使用して、サイズの大きい Excel ファイル (>= 100 MB) から他のデータ ストアにデータをコピーすると、パフォーマンスが低下したり、OOM 問題が発生したりする可能性があります。

- **原因**: 

    - スキーマのインポート、データのプレビュー、Excel データセットでのワークシートの一覧表示などの操作では、タイムアウトは 100 秒で静的です。 大きな Excel ファイルでは、これらの操作がタイムアウト値内で完了しないことがあります。

    - ADF コピー アクティビティは、Excel ファイル全体をメモリに読み込み、データを読み取る指定されたワークシートとセルを検索します。 ADF が使用する基盤となる SDK のために、このような動作になります。

- **解決方法**: 

    - スキーマをインポートする場合は、元のファイルのサブセットとなるより小さいサンプル ファイルを生成し、[接続/ストアからスキーマをインポートする] ではなく、[サンプル ファイルからスキーマをインポートする] を選択します。

    - ワークシートを一覧表示する場合は、ワークシートのドロップダウンで [編集] をクリックし、シート名/インデックスを入力します。

    - 大きな Excel ファイル (> 100 MB) を他のストアにコピーするには、Data Flow Excel ソースを使用します。これにより、ストリーミングの読み取りとパフォーマンスが向上します。
    

## <a name="ftp"></a>FTP

### <a name="error-code--ftpfailedtoconnecttoftpserver"></a>エラー コード:FtpFailedToConnectToFtpServer

- **メッセージ**: `Failed to connect to FTP server. Please make sure the provided server informantion is correct, and try again.`

- **原因**:FTP サーバーに使用されているリンク サービスの種類が正しくない可能性があります。たとえば、SFTP リンク サービスを使用して FTP サーバーに接続している場合などです。

- **推奨事項**:ターゲット サーバーのポートを確認します。 既定では、FTP はポート 21 を使用します。


## <a name="http"></a>Http

### <a name="error-code--httpfilefailedtoread"></a>エラー コード:HttpFileFailedToRead

- **メッセージ**: `Failed to read data from http server. Check the error from http server：%message;`

- **原因**:このエラーは、Azure Data Factory が http サーバーと通信しているが、http 要求操作が失敗した場合に発生します。

- **推奨事項**:エラー メッセージの http ステータス コードまたはメッセージを確認し、リモート サーバーの問題を修正します。


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>エラー コード:ArgumentOutOfRangeException

- **メッセージ**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **原因**:ADF では、DateTime 値は、0001-01-01 00:00:00 から 9999-12-31 23:59:59 の範囲でサポートされています。 ただし、Oracle では、より広い範囲の DateTime 値 (紀元前や 59 を超える分/秒など) がサポートされるため、ADF でエラーが発生します。

- **推奨事項**: 

    `select dump(<column name>)` を実行して、Oracle の値が ADF の範囲内にあるかどうかを確認します。 

    結果でのバイト シーケンスを知りたい場合は、 https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle を確認してください。


## <a name="orc-format"></a>ORC 形式

### <a name="error-code--orcjavainvocationexception"></a>エラー コード:OrcJavaInvocationException

- **メッセージ**: `An error occurred when invoking java, message: %javaException;.`

- **原因**:エラー メッセージに 'OutOfMemory'、'Java heap space'、および 'doubleCapacity' が含まれている場合は、通常、古いバージョンの統合ランタイムでのメモリ管理の問題です。

- **推奨事項**:セルフホステッド統合ランタイムを使用している場合、最新バージョンへのアップグレードをお勧めします。

- **原因**:エラー メッセージに 'OutOfMemory' が含まれている場合は、統合ランタイムにファイルを処理するのに十分なリソースがありません。

- **推奨事項**:統合ランタイムで同時実行を制限してください。 セルフホステッド統合ランタイムの場合は、8 GB 以上のメモリを備えた強力なマシンにスケールアップしてください。

- **原因**:エラー メッセージに 'NullPointerReference' が含まれている場合は、一時的なエラーである可能性があります。

- **推奨事項**:再試行してください。 問題が解決しない場合は、サポートにお問い合わせください。

- **原因**:エラー メッセージに 'BufferOverflowException' が含まれている場合、一時的なエラーである可能性があります。

- **推奨事項**:再試行してください。 問題が解決しない場合は、サポートにお問い合わせください。

- **原因**:エラーメッセージに "java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable cannot be cast to org.apache.hadoop.io.Text" (java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable を org.apache.hadoop.io.Text にキャストできません) が含まれている場合、これは Java ランタイム内の型変換の問題です。 通常、これは、ソース データが Java ランタイムで適切に処理できないことが原因で発生します。

- **推奨事項**:これは、データの問題です。 ORC 形式のデータで、char または varchar の代わりに string を使用してみてください。

### <a name="error-code--orcdatetimeexceedlimit"></a>エラー コード:OrcDateTimeExceedLimit

- **メッセージ**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **原因**:datetime 値が '0001-01-01 00:00:00' である場合は、ユリウス暦とグレゴリオ暦の違いが原因で発生する可能性があります。 https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates.

- **推奨事項**:ティック値を確認し、datetime 値 '0001-01-01 00:00:00' を使用しないようにしてください。


## <a name="parquet-format"></a>Parquet 形式

### <a name="error-code--parquetjavainvocationexception"></a>エラー コード:ParquetJavaInvocationException

- **メッセージ**: `An error occurred when invoking java, message: %javaException;.`

- **原因**:エラー メッセージに 'OutOfMemory'、'Java heap space'、および 'doubleCapacity' が含まれている場合は、通常、古いバージョンの統合ランタイムでのメモリ管理の問題です。

- **推奨事項**:セルフホステッド統合ランタイムを使用していて、バージョンが 3.20.7159.1 より前の場合は、最新バージョンにアップグレードすることをお勧めします。

- **原因**:エラー メッセージに 'OutOfMemory' が含まれている場合は、統合ランタイムにファイルを処理するのに十分なリソースがありません。

- **推奨事項**:統合ランタイムで同時実行を制限してください。 セルフホステッド統合ランタイムの場合は、8 GB 以上のメモリを備えた強力なマシンにスケールアップしてください。

- **原因**:エラー メッセージに 'NullPointerReference' が含まれている場合は、一時的なエラーである可能性があります。

- **推奨事項**:再試行してください。 問題が解決しない場合は、サポートにお問い合わせください。


### <a name="error-code--parquetinvalidfile"></a>エラー コード:ParquetInvalidFile

- **メッセージ**: `File is not a valid Parquet file.`

- **原因**:Parquet ファイルの問題です。

- **推奨事項**:入力が有効な Parquet ファイルであることを確認してください。


### <a name="error-code--parquetnotsupportedtype"></a>エラー コード:ParquetNotSupportedType

- **メッセージ**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **原因**:Parquet 形式は、Azure Data Factory ではサポートされていません。

- **推奨事項**:ソース データを再確認してください。 ドキュメントを参照してください: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs 。


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>エラー コード:ParquetMissedDecimalPrecisionScale

- **メッセージ**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **原因**:数値の有効桁数と小数点以下桁数を解析しようとしていますが、このような情報は提供されていません。

- **推奨事項**:'ソース' は正しい有効桁数と小数点以下桁数を返しません。 問題の列の有効桁数と小数点以下桁数を確認してください。


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>エラー コード:ParquetInvalidDecimalPrecisionScale

- **メッセージ**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **原因**:スキーマが無効です。

- **推奨事項**:問題の列の有効桁数と小数点以下桁数を確認してください。


### <a name="error-code--parquetcolumnnotfound"></a>エラー コード:ParquetColumnNotFound

- **メッセージ**: `Column %column; does not exist in Parquet file.`

- **原因**:送信元スキーマがシンク スキーマと一致しません。

- **推奨事項**:'アクティビティ' の 'マッピング' を確認してください。 ソース列を右シンク列にマップできることを確認してください。


### <a name="error-code--parquetinvaliddataformat"></a>エラー コード:ParquetInvalidDataFormat

- **メッセージ**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **原因**:データを mappings.source に指定された型に変換できません

- **推奨事項**:ソース データを再確認するか、コピー アクティビティの列マッピングでこの列に適切なデータ型を指定してください。 ドキュメントを参照してください: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs 。


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>エラー コード:ParquetDataCountNotMatchColumnCount

- **メッセージ**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **原因**:ソース列の数とシンク列の数が一致しません

- **推奨事項**:'マッピング' でソース列の数がシンク列の数と同じであることを再確認してください。


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>エラー コード:ParquetDataTypeNotMatchColumnType

- **メッセージ**:データ型 %srcType; が、列 '%columnIndex;' の指定された列の型 %dstType; と一致しません。

- **原因**:ソースからのデータを、シンクで定義されている型に変換できません

- **推奨事項**:mapping.sink に正しい型を指定してください。


### <a name="error-code--parquetbridgeinvaliddata"></a>エラー コード:ParquetBridgeInvalidData

- **メッセージ**: `%message;`

- **原因**:制限を超えるデータ値

- **推奨事項**:再試行してください。 問題が解決しない場合は、Microsoft にお問い合わせください。


### <a name="error-code--parquetunsupportedinterpretation"></a>エラー コード:ParquetUnsupportedInterpretation

- **メッセージ**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **原因**:サポートされていないシナリオ

- **推奨事項**:'ParquetInterpretFor' を 'sparkSql' にしないでください。


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>エラー コード:ParquetUnsupportFileLevelCompressionOption

- **メッセージ**: `File level compression is not supported for Parquet.`

- **原因**:サポートされていないシナリオ

- **推奨事項**:ペイロード内の 'CompressionType' を削除してください。


### <a name="error-code--usererrorjniexception"></a>エラー コード:UserErrorJniException

- **メッセージ**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **原因**:一部の無効な (グローバル) 引数が設定されているため、JVM を作成できません。

- **推奨事項**:セルフホステッド IR の **各ノード** をホストするマシンにログインします。 システム変数が次のように正しく設定されているかどうかを確認します: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G`。 すべての IR ノードを再起動してから、パイプラインを再実行します。


### <a name="arithmetic-overflow"></a>算術オーバーフロー

- **現象**:Parquet ファイルのコピー中に次のエラー メッセージが表示されます: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **原因**:現時点では、Oracle から Parquet にファイルをコピーするときにサポートされている値は、小数点以下の桁数が 38 桁以内で整数部分の桁数が 20 以下のものだけです。 

- **解決方法**:回避策として、このような問題がある列を VARCHAR2 に変換することができます。


### <a name="no-enum-constant"></a>列挙型定数はありません

- **現象**:Parquet 形式にデータをコピーするときに、次のエラー メッセージが表示されます: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;`、または: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test`。

- **原因**: 

    この問題は、Parquet がこのような形式をサポートしていないため、列名に空白またはサポートされていない文字 (,;{}()\n\t= など) が含まれていることが原因で発生する可能性があります。 

    たとえば、*contoso(test)* のような列名は[コード](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` の丸かっこで囲まれた型を解析します。 このような "test" 型が存在しないため、エラーが発生します。

    サポートされている型を確認するには、[ここで](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)これらを確認できます。

- **解決方法**: 

    - シンク列名に空白があるかどうかを再確認します。

    - 空白が含まれている最初の行が列名として使用されているかどうかを再確認します。

    - 型 OriginalType がサポートされているかどうかを再確認します。 `,;{}()\n\t=` の特殊記号は使用しないようにしてください。 


## <a name="rest"></a>REST

### <a name="error-code--restsinkcallfailed"></a>エラー コード:RestSinkCallFailed

- **メッセージ**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **原因**:このエラーは、Azure Data Factory が http プロトコルを介して Rest エンドポイントと通信し、要求操作が失敗した場合に発生します。

- **推奨事項**:エラー メッセージの http ステータス コードまたはメッセージを確認し、リモート サーバーの問題を修正します。

### <a name="unexpected-network-response-from-rest-connector"></a>REST コネクタからの予期しないネットワーク応答

- **現象**:エンドポイントは、REST コネクタから予期しない応答 (400/401/403/500) を受け取ることがあります。

- **原因**:REST ソース コネクタは、HTTP 要求を構築するときに、リンクされたサービス/データセット/コピー元からの URL および HTTP メソッド/ヘッダー/本文をパラメーターとして使用します。 この問題は、指定された 1 つ以上のパラメーターにおける何らかの誤りが原因で発生している可能性があります。

- **解決方法**: 
    - パラメーターが原因であるかどうかを確認するには、コマンド ウィンドウで "curl" を使用します (**Accept** と **User-Agent** のヘッダーを常に含める必要があります)。
        ```
        curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>
        ```
      コマンドが同じ予期しない応答を返す場合は、予期される応答を返すまで、"curl" を使って上記のパラメーターを修正してください。 

      また、'curl--help' を使用して、より高度な方法でコマンドを使用することもできます。

    - ADF REST コネクタのみが予期しない応答を返す場合、詳細なトラブルシューティングについて、Microsoft サポートにお問い合わせください。
    
    - "curl" は SSL 証明書の検証問題を再現するのに適さない場合があることに注意してください。 一部のシナリオでは、SSL 証明書の検証問題が発生することなく、"curl" コマンドが正常に実行されました。 しかし、同じ URL をブラウザーで実行すると、サーバーとの信頼を確立するためのクライアントの最初の場所では SSL 証明書は実際には返されません。

      上記のケースでは、**Postman** や **Fiddler** などのツールを使用することをお勧めします。


## <a name="sftp"></a>SFTP

#### <a name="error-code--sftpoperationfail"></a>エラー コード:SftpOperationFail

- **メッセージ**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **原因**:SFTP 操作で問題が発生しました。

- **推奨事項**:SFTP からの詳細なエラーを確認します。


### <a name="error-code--sftprenameoperationfail"></a>エラー コード:SftpRenameOperationFail

- **メッセージ**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, please set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **原因**:SFTP サーバーでは、一時ファイルの名前変更はサポートされません。

- **推奨事項**:コピー シンクで "useTempFileRename" を false に設定して、一時ファイルへのアップロードを無効にします。


### <a name="error-code--sftpinvalidsftpcredential"></a>エラー コード:SftpInvalidSftpCredential

- **メッセージ**: `Invalid Sftp credential provided for '%type;' authentication type.`

- **原因**:秘密キーのコンテンツは AKV/SDK からフェッチされますが、正しくエンコードされていません。

- **推奨事項**:  

    秘密キーのコンテンツが AKV からのものであり、お客様が SFTP のリンクされたサービスに直接アップロードした場合、元のキー ファイルは機能します

    https://docs.microsoft.com/azure/data-factory/connector-sftp#using-ssh-public-key-authentication を参照してください。privateKey のコンテンツは、Base64 でエンコードされた SSH 秘密キーのコンテンツです。

    Base64 エンコーディングで **元の秘密キー ファイルのコンテンツ全体** をエンコードし、エンコードされた文字列を AKV に格納してください。 元の秘密キー ファイルは、[ファイルからアップロード] をクリックした場合に SFTP のリンクされたサービスで使用できるファイルです。

    文字列の生成に使用されるいくつかのサンプルを次に示します。

    - C# コードを使用した場合:
    ```
    byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
    string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
    ```

    - Python コードを使用した場合:
    ```
    import base64
    rfd = open(r'{Private Key Path}', 'rb')
    keyContent = rfd.read()
    rfd.close()
    print base64.b64encode(Key Content)
    ```

    - サードパーティの Base64 変換ツールを使用します

        https://www.base64encode.org/ のようなツールをお勧めします。

- **原因**:間違ったキー コンテンツ形式が選択されています

- **推奨事項**:  

    現在、PKCS # 8 形式の SSH 秘密キー ("-----BEGIN ENCRYPTED PRIVATE KEY-----" で開始) は、ADF での SFTP サーバーへのアクセスにはサポートされていません。 

    次のコマンドを実行して、キーを従来の SSH キー形式 ("-----BEGIN RSA PRIVATE KEY-----" で開始) に変換します。

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **原因**:資格情報または秘密キーのコンテンツが無効です

- **推奨事項**:WinSCP などのツールを使用して、キー ファイルまたはパスワードが正しいかどうかを再確認します。

### <a name="sftp-copy-activity-failed"></a>SFTP コピー アクティビティが失敗しました

- **現象**:エラー コード:UserErrorInvalidColumnMappingColumnNotFound。 エラー メッセージ: `Column &apos;AccMngr&apos; specified in column mapping cannot be found in source data.`

- **原因**:ソースには、"AccMngr" という名前の列が含まれていません。

- **解決方法**:変換先のデータセット列をマップして、このような "AccMngr" 列があるかどうかを確認することで、データセットがどのように構成されているかを再確認します。


### <a name="error-code--sftpfailedtoconnecttosftpserver"></a>エラー コード:SftpFailedToConnectToSftpServer

- **メッセージ**: `Failed to connect to Sftp server '%server;'.`

- **原因**:エラー メッセージに 'Socket read operation has timed out after 30000 milliseconds' (ソケットの読み取り操作が 30000 ミリ秒後にタイムアウトしました) が含まれている場合、考えられる原因の 1 つは、FTP リンク サービスを使用して SFTP サーバーに接続するなど、SFTP サーバーに誤ったリンク サービスの種類が使用されていることです。

- **推奨事項**:ターゲット サーバーのポートを確認します。 既定では、SFTP はポート 22 を使用します。

- **原因**:エラー メッセージに 'Server response does not contain SSH protocol identification' (サーバーの応答に SSH プロトコルの ID が含まれていません) が含まれている場合、考えられる原因の 1 つは、SFTP サーバーによって接続が調整されたことです。 ADF が複数の接続を作成して、SFTP サーバーから並列でダウンロードするため、SFTP サーバーの帯域幅調整が行われる場合があります。 実際には、サーバーが異なればスロットル調整が発生したときに異なるエラーが返されます。

- **推奨事項**:  

    SFTP データセットの最大コンカレント接続数を 1 に指定して、コピーを再実行します。 成功した場合は、帯域幅調整が原因であることを確認できます。

    低スループットのレベルを上げる場合は、SFTP 管理者に問い合わせてコンカレント接続数の上限を引き上げるか、以下の IP を許可リストに追加してください。

    - マネージド IR を使用している場合は、[Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)を追加してください。
      また、IP 範囲の大きなリストを SFTP サーバー許可リストに追加しない場合は、セルフホステッド IR をインストールすることもできます。

    - セルフホステッド IR を使用している場合は、をインストールしたコンピューターの IP アドレスを許可一覧に追加してください。


## <a name="sharepoint-online-list"></a>SharePoint Online リスト

### <a name="error-code--sharepointonlineauthfailed"></a>エラー コード:SharePointOnlineAuthFailed

- **メッセージ**: `The access token generated failed, status code: %code;, error message: %message;.`

- **原因**:サービス プリンシパル ID およびキーが正しく設定されていない可能性があります。

- **推奨事項**:登録したアプリケーション (サービス プリンシパル ID) とキーを調べて正しく設定されているかどうかを確認します。


## <a name="xml-format"></a>XML 形式

### <a name="error-code--xmlsinknotsupported"></a>エラー コード:XmlSinkNotSupported

- **メッセージ**: `Write data in xml format is not supported yet, please choose a different format!`

- **原因**:コピー アクティビティで XML データセットをシンク データセットとして使用しました

- **推奨事項**:別の形式のデータセットをコピー シンクとして使用します。


### <a name="error-code--xmlattributecolumnnameconflict"></a>エラー コード:XmlAttributeColumnNameConflict

- **メッセージ**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **原因**:属性プレフィックスを使用しました。これが競合の原因になりました。

- **推奨事項**:"attributePrefix" プロパティに別の値を設定します。


### <a name="error-code--xmlvaluecolumnnameconflict"></a>エラー コード:XmlValueColumnNameConflict

- **メッセージ**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **原因**:子要素名の 1 つを、要素値の列名として使用しました。

- **推奨事項**:"valueColumn" プロパティに別の値を設定します。


### <a name="error-code--xmlinvalid"></a>エラー コード:XmlInvalid

- **メッセージ**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **原因**:入力 XML ファイルの形式が正しくありません。

- **推奨事項**:正しい形式になるように XML ファイルを修正します。


## <a name="general-copy-activity-error"></a>一般的なコピー アクティビティのエラー

### <a name="error-code--jrenotfound"></a>エラー コード:JreNotFound

- **メッセージ**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **原因**:セルフホステッド統合ランタイムで Java ランタイムが見つかりません。 Java ランタイムは、特定のソースを読み取るために必要です。

- **推奨事項**:統合ランタイム環境を確認してください。参照ドキュメント: https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>エラー コード:WildcardPathSinkNotSupported

- **メッセージ**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **原因**:シンク データセットでは、ワイルドカードはサポートされません。

- **推奨事項**:シンク データセットを確認し、ワイルドカード値を指定せずにパスを修正してください。


### <a name="fips-issue"></a>FIPS の問題

- **現象**:FIPS 対応のセルフホステッド統合ランタイム マシンでコピー アクティビティが失敗し、エラーメッセージ `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` が表示されます。 これは、Azure Blob、SFTP などのコネクタを使用してデータをコピーするときに発生する可能性があります。

- **原因**:FIPS (Federal Information Processing Standards) では、使用が許可される暗号化アルゴリズムの特定のセットを定義します。 マシンで FIPS モードが有効になっている場合、一部のシナリオでは、コピー アクティビティが依存する一部の暗号化クラスがブロックされます。

- **解決方法**:Windows の FIPS モードの現在の状況について、[この記事](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)を参照し、セルフホステッド統合ランタイム マシンで FIPS を無効にできるかどうかを評価できます。

    一方、Azure Data Factory で FIPS をバイパスし、アクティビティの実行を成功させたい場合は、次の手順を実行できます。

    1. セルフホステッド統合ランタイムがインストールされているフォルダー (通常は `C:\Program Files\Microsoft Integration Runtime\<IR version>\Shared` 下にあります) を開きます。

    2. "diawp.exe.config" を開き、次のように、`<enforceFIPSPolicy enabled="false"/>` を `<runtime>` セクションに追加します。

        ![FIPS の無効化](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. セルフホステッド統合ランタイム マシンを再起動します。


## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 質問ページ](/answers/topics/azure-data-factory.html)
*  [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
