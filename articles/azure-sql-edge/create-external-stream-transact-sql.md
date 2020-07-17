---
title: CREATE EXTERNAL STREAM (Transact-SQL) - Azure SQL Edge (プレビュー)
description: Azure SQL Edge での CREATE EXTERNAL STREAM ステートメントについて学習します (プレビュー)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: d4ad446d43c90eee1c48ee2ba94585242805fa7d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595401"
---
# <a name="create-external-stream-transact-sql"></a>CREATE EXTERNAL STREAM (Transact-SQL)

EXTERNAL STREAM オブジェクトには、入力と出力両方の 2 つの目的があります。 Azure Event ハブや IoT ハブなどのイベント インジェスト サービスからのストリーミング データをクエリするための入力として、またはストリーミング クエリから結果を格納する場所と方法を指定するための出力として、使用できます。

EXTERNAL STREAM は、Event Hubs や Blob Storage などのサービスの出力と入力の両方として、指定および作成することもできます。 これは、あるストリーミング クエリでは外部ストリームに対する結果が出力として保持されており、別のストリーミング クエリでは入力として同じ外部ストリームから読み取りが行われるチェーン シナリオ用です。 


## <a name="syntax"></a>構文

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
(column definition [,column definitions ] * ) --Used for input - optional 
WITH  
(  
  DATA_SOURCE = <data_source_name>, 
  LOCATION = <location_name>, 
  EXTERNAL_FILE_FORMAT = <external_file_format_name>, --Used for input - optional 
   
  INPUT_OPTIONS =  
    ‘CONSUMER_GROUP=<consumer_group_name>; 
    ‘TIME_POLICY=<time_policy>; 
    LATE_EVENT_TOLERANCE=<late_event_tolerance_value>; 
    OUT_OF_ORDER_EVENT_TOLERANCE=<out_of_order_tolerance_value> 
     
    /* Edge options */ 
  , 
  OUTPUT_OPTIONS =  
    ‘REJECT_POLICY=<reject_policy>; 
    MINIMUM_ROWS=<row_value>; 
    MAXIMUM_TIME=<time_value_minutes>; 
    PARTITION_KEY_COLUMN=<partition_key_column_name>; 
    PROPERTY_COLUMNS=(); 
    SYSTEM_PROPERTY_COLUMNS=(); 
    PARTITION_KEY=<partition_key_name>; 
    ROW_KEY=<row_key_name>; 
    BATCH_SIZE=<batch_size_value>; 
    MAXIMUM_BATCH_COUNT=<batch_value>;  
    STAGING_AREA=<blob_data_source>’ 
     
    /* Edge options */ TAGS=<tag_column_value> 
); 
```


## <a name="arguments"></a>引数


- [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **LOCATION**: データ ソース内の実際のデータまたは場所の名前を指定します。 Edge ハブまたは Kafka ストリーム オブジェクトの場合、場所では、読み取り元または書き込み先の Edge ハブまたは Kafka トピックの名前を指定します。
- **INPUT_OPTIONS**: ストリーミング クエリに対する入力である Event Hubs や IOT Hub などのサービスへのオプションを、キーと値のペアとして指定します
    - CONSUMER_GROUP: Event Hubs や IOT Hub では、1 つのコンシューマー グループ内のリーダーの数が (5 個に) 制限されています。 このフィールドを空白のままにすると、"$Default" コンシューマー グループが使用されます。
      - Event Hubs と IOT Hub に適用されます
    - TIME_POLICY: 遅延イベントまたは順不同イベントが許容範囲の値に到達したときに、イベントを削除するか、イベント時間を調整するかを指定します。
      - Event Hubs と IOT Hub に適用されます
    - LATE_EVENT_TOLERANCE: 許容される最大遅延時間。 遅延は、イベントのタイムスタンプとシステム クロックの間の差を表します。
      - Event Hubs と IOT Hub に適用されます
    - OUT_OF_ORDER_EVENT_TOLERANCE: イベントは、入力からストリーミング クエリに移動した後、順不同に到着してもかまいません。 このようなイベントは、そのまま受け入れることも、または設定した期間だけ一時停止して並べ替えることもできます。
      - Event Hubs と IOT Hub に適用されます
- **OUTPUT_OPTIONS**: ストリーミング クエリに対する出力であるサポートされるサービスへのオプションを、キーと値のペアとして指定します 
  - REJECT_POLICY: DROP | RETRY データ変換エラーが発生したときのデータ エラー処理ポリシーを指定します。 
    - サポートされるすべての出力に適用されます 
  - MINIMUM_ROWS:  
    出力に書き込まれるバッチごとに必要な最小行数。 Parquet では、バッチごとに新しいファイルが作成されます。 
    - サポートされるすべての出力に適用されます 
  - MAXIMUM_TIME:  
    バッチあたりの最大待機時間。 この時間が経過すると、最小行数の要件が満たされていなくても、バッチは出力に書き込まれます。 
    - サポートされるすべての出力に適用されます 
  - PARTITION_KEY_COLUMN:  
    パーティション キーに使用される列。 
    - Event Hubs と Service Bus トピックに適用されます 
  - PROPERTY_COLUMNS:  
    提供されている場合、カスタム プロパティとしてメッセージに添付する出力列の名前のコンマ区切りのリスト。  
    - Event Hubs および Service Bus のトピックとキューに適用されます 
  - SYSTEM_PROPERTY_COLUMNS:  
    Service Bus メッセージに設定されるシステム プロパティ名と出力列の名前と値のペアの JSON 形式のコレクション。 例: { "MessageId":   "column1", "PartitionKey": "column2"} 
    - Service Bus のトピックとキューに適用されます 
  - PARTITION_KEY:  
    パーティション キーが含まれる出力列の名前。 パーティション キーは、エンティティのプライマリ キーの最初の部分を形成する、特定のテーブル内のパーティションの一意の識別子です。 最大サイズが 1 KB の文字列値です。 
    - Table Storage と Azure Functions に適用されます 
  - ROW_KEY:  
    行キーが含まれる出力列の名前。 行キーは、特定のパーティション内のエンティティを示す一意の識別子です。 これにより、エンティティのプライマリ キーの 2 番目の部分が形成されます。 行キーは、最大サイズが 1 KB の文字列値です。 
    - Table Storage と Azure Functions に適用されます 
  - BATCH_SIZE:  
    これは、Table Storage に対するトランザクションの数を表します。最大 100 レコードまで可能です。 Azure Functions の場合、これは呼び出しごとに関数に送信されるバッチ サイズ (バイト単位) を表します。既定値は 256 KB です。 
    - Table Storage と Azure Functions に適用されます 
  - MAXIMUM_BATCH_COUNT:  
    Azure 関数の場合は、呼び出しごとに関数に送信されるイベントの最大数。既定値は 100 です。 SQL Database の場合は、これは一括挿入の各トランザクションで送信されたレコードの最大数を表します。既定値は 10,000 です。 
    - SQL Database と Azure 関数に適用されます 
  - STAGING_AREA: Blob Storage への EXTERNAL DATA SOURCE オブジェクト。SQL Data Warehouse への高スループット データ インジェストに対するステージング領域 
    - SQL Data Warehouse に適用されます


## <a name="examples"></a>例

### <a name="example-1---edgehub"></a>例 1 - EdgeHub

型: 入力または出力<br>
パラメーター:
- 入力または出力
  - エイリアス 
  - イベントのシリアル化の形式 
  - エンコード 
- 入力のみ: 
  - イベントの圧縮タイプ 

構文:

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = 'JSON', 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   EXTERNAL_FILE_FORMAT = myFileFormat, 
   LOCATION = ‘<mytopicname>’, 
   OUTPUT_OPTIONS =   
     ‘REJECT_TYPE: Drop’ 
);
```


### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>例 2 - Azure SQL Database、Azure SQL Edge、SQL Server

型: 出力<br>
パラメーター:
- 出力エイリアス  
- データベース (SQL Database の場合に必要) 
- サーバー (SQL Database の場合に必要) 
- ユーザー名 (SQL Database の場合に必要) 
- パスワード (SQL Database の場合に必要) 
- テーブル 
- すべての入力パーティションを 1 つの書き込みにマージするか、前のクエリ ステップまたは入力パーティション構成を継承します (SQL Database の場合に必要) 
- 最大バッチ カウント 

構文:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Database/Edge 
CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = ‘<DatabaseName>.<SchemaName>.<TableName>’ 
   --Note: If table is container in the database, <TableName> should be sufficient 
   --Note: Do not need external file format in this case 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop 
); 
```

### <a name="example-3---kafka"></a>例 3 - Kafka

型: 入力<br>
パラメーター:

- Kafka ブートストラップ サーバー 
- Kafka トピック名 
- ソース パーティション数 

構文:

```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = ' kafka://<kafkaserver>:<ipaddress>’, 
  CREDENTIAL = kafkaCredName 
 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = ‘<KafkaTopicName>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 5’ 
); 
```

### <a name="example-4---blob-storage"></a>例 4 - Blob Storage

型: 入力または出力<br>
パラメーター:
- 入力または出力:
  - エイリアス 
  - ストレージ アカウント 
  - ストレージ アカウント キー 
  - コンテナー 
  - パスのパターン 
  - 日付の形式 
  - 時刻の形式 
  - イベントのシリアル化の形式 
  - エンコード 
- 入力のみ: 
  - パーティション (入力) 
  - イベントの圧縮の種類 (入力) 
- 出力のみ: 
  - 最小行数 (出力) 
  - 最大時間 (出力) 
  - 認証モード (出力) 

構文:

```sql
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = 'AccountKey'; 
 
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredNameMSI 
WITH IDENTITY = 'Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyBlobStorage_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATE_FORMAT = 'YYYY/MM/DD HH', --Both Date and Time format 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyBlobStorage_tweets, 
    LOCATION = ‘<path_pattern>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 1’, 
  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: (), 
      MINUMUM_ROWS: 100000, 
      MAXIMUM_TIME: 60’ 
); 
```

### <a name="example-5---event-hub"></a>例 5 - イベント ハブ

型: 入力または出力<br>
パラメーター:
- 入力または出力:
  - エイリアス 
  - Service Bus 名前空間 
  - イベント ハブ名 
  - イベント ハブ ポリシー名 
  - イベント ハブ ポリシー キー 
  - イベントのシリアル化の形式 
  - エンコード 
- 入力のみ: 
  - イベント ハブ コンシューマー グループ 
  - イベントの圧縮タイプ 
- 出力のみ: 
  - パーティション キー列 
  - プロパティ列 

構文:

```sql
CREATE DATABASE SCOPED CREDENTIAL eventHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyEventHub_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyEventHub_tweets, 
    LOCATION = ‘<topicname>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
 
    INPUT_OPTIONS =  
      ‘CONSUMER_GROUP: FirstConsumerGroup’, 
          
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: ()’ 
);
```

### <a name="example-6---iot-hub"></a>例 6 - IoT Hub

型: 入力<br>
パラメーター:

- 入力のエイリアス 
- IoT Hub 
- エンドポイント 
- 共有アクセス ポリシー名 
- 共有アクセス ポリシー キー 
- コンシューマー グループ 
- イベントのシリアル化の形式 
- エンコード 
- イベントの圧縮タイプ 

構文:

```sql
CREATE DATABASE SCOPED CREDENTIAL IoTHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyIoTHub_tweets 
WITH 
(     
  LOCATION = ' iot://iot_hub_name.azure-devices.net’, 
  CREDENTIAL = IoTHubCredName 
);  

CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyIoTHub_tweets, 
    LOCATION = ‘<name>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘ENDPOINT: Messaging, 
      CONSUMER_GROUP: ‘FirstConsumerGroup’ 
); 
```

### <a name="example-7---azure-synapse-analytics-formerly-sql-data-warehouse"></a>例 7 - Azure Synapse Analytics (旧称 SQL Data Warehouse)

型: 出力<br>
パラメーター:
- 出力エイリアス 
- データベース 
- サーバー 
- ユーザー名 
- Password 
- テーブル 
- ステージング領域 (COPY 用) 

構文:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTable 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
CREATE EXTERNAL STREAM MySQLTableOutput 
WITH ( 
   DATA_SOURCE = MyTargetSQLTable, 
   LOCATION = ‘<TableName>’ 
   --Note: Do not need external file format in this case 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     STAGING_AREA: staging_area_data_source’, 
); 
```


### <a name="example-8---table-storage"></a>例 8 - Table Storage

型: 出力<br>
パラメーター:
- 出力エイリアス 
- ストレージ アカウント 
- ストレージ アカウント キー 
- テーブル名 
- パーティション キー 
- 行キー 
- バッチ サイズ 

構文:

```sql
CREATE DATABASE SCOPED CREDENTIAL TableStorageCredName 
WITH IDENTITY = ‘Storage account Key’, 
SECRET = '<storage_account_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY: <column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


### <a name="example-9---service-bus-topic-same-properties-as-queue"></a>例 9 - Service Bus トピック (キューと同じプロパティ)

型: 出力<br>
パラメーター:
- 出力エイリアス 
- Service Bus 名前空間 
- トピック名 
- トピック ポリシー名 
- トピック ポリシー キー 
- プロパティ列 
- システム プロパティ列 
- イベントのシリアル化の形式 
- エンコード 

構文:

```sql
CREATE DATABASE SCOPED CREDENTIAL serviceBusCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyServiceBus_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = serviceBusCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM MyServiceBusOutput 
WITH ( 
   DATA_SOURCE = MyServiceBus_tweets, 
   LOCATION = ‘<topic_name>’, 
   EXTERNAL_FILE_FORMAT = myFileFormat 
       OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY_COLUMN: , 
     PROPERTY_COLUMNS: (), 
     SYSTEM_PROPERTY_COLUMNS: ()’ 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
           
); 
```


### <a name="example-10---cosmos-db"></a>例 10 - Cosmos DB

型: 出力<br>
パラメーター:
- 出力エイリアス 
- Account ID 
- アカウント キー 
- データベース 
- コンテナー名 
- ドキュメント ID 


構文:

```sql
CREATE DATABASE SCOPED CREDENTIAL cosmosDBCredName 
WITH IDENTITY = ‘Storage Account Key’, 
SECRET = '<accountKey>'; 
 
CREATE EXTERNAL DATA SOURCE MyCosmosDB_tweets 
WITH 
(     
  LOCATION = ' cosmosdb://accountid.documents.azure.com:443/ database’, 
  CREDENTIAL = cosmosDBCredName 
); 
 
CREATE EXTERNAL STREAM MyCosmosDBOutput 
WITH ( 
   DATA_SOURCE = MyCosmosDB_tweets, 
   LOCATION = ‘<container/documentID>’ 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop', 
     --Note: Do not need external file format in this case 
          
);
```



### <a name="example-11---power-bi"></a>例 11 - Power BI

型: 出力<br>
パラメーター:
- 出力エイリアス 
- データセットの名前 
- テーブル名 


構文:

```sql
CREATE DATABASE SCOPED CREDENTIAL PBIDBCredName 
WITH IDENTITY = ‘Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyPbi_tweets 
WITH 
( 
  LOCATION = 'pbi://dataset/’, 
  CREDENTIAL = PBIDBCredName 
 
); 
 
CREATE EXTERNAL STREAM MyPbiOutput 
WITH ( 
   DATA_SOURCE = MyPbi_tweets, 
   LOCATION = 'tableName', 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop' 
        
);
```

### <a name="example-12---azure-function"></a>例 12 - Azure 関数

型: 出力<br>
パラメーター:
- 関数アプリ 
- 機能 
- Key 
- 最大バッチ サイズ 
- 最大バッチ カウント 

構文:

```sql
CREATE DATABASE SCOPED CREDENTIAL AzureFunctionCredName 
WITH IDENTITY = ‘Function Key’, 
SECRET = '<function_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: 'Drop'      
     PARTITION_KEY: ‘<column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


## <a name="see-also"></a>関連項目

- [ALTER EXTERNAL STREAM (Transact-SQL)](alter-external-stream-transact-sql.md) 
- [DROP EXTERNAL STREAM (Transact-SQL)](drop-external-stream-transact-sql.md) 

