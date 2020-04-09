---
title: Azure Data Explorer に JSON 書式付きデータを取り込む
description: Azure Data Explorer に JSON 書式付きデータを取り込む方法を説明します。
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: bcf6a0ccfc04890052f1a4bab19f97ee4e55f87a
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756622"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>Azure Data Explorer に JSON 書式付きサンプル データを取り込む

この記事では、Azure Data Explorer データベースに JSON 書式付きデータを取り込む方法を示します。 まず、未加工の JSON とマップされた JSON の単純な例を紹介します。次に、複数行の JSON に進み、その後、配列とディクショナリを含むさらに複雑な JSON スキーマに取り組みます。  これらの例では、Kusto クエリ言語 (KQL)、 C#、または Python を使用して JSON 書式付きデータを取り込むプロセスについて詳しく説明しています。 Kusto クエリ言語の `ingest` 制御コマンドは、エンジン エンドポイントに対して直接実行されます。 運用環境のシナリオでは、インジェストは、クライアント ライブラリまたはデータ接続を使用して、データ管理サービスに対して実行されます。 これらのクライアント ライブラリを使用したデータの取り込みに関するチュートリアルについては、「[Azure Data Explorer の Python ライブラリを使用してデータを取り込む](/azure/data-explorer/python-ingest-data)」と「[Azure Data Explorer .NET Standard SDK (プレビュー) を使用してデータを取り込む](/azure/data-explorer/net-standard-ingest-data)」を参照してください。

## <a name="prerequisites"></a>前提条件

[テスト用のクラスターとデータベース](create-cluster-database-portal.md)

## <a name="the-json-format"></a>JSON 形式

Azure Data Explorer は、次の 2 つの JSON ファイル形式をサポートしています。
* `json`:行で区切られた JSON。 入力データの各行には、JSON レコードが 1 つだけ含まれています。
* `multijson`:複数行の JSON。 パーサーは行区切り記号を無視し、前の位置から有効な JSON の末尾までのレコードを読み取ります。

### <a name="ingest-and-map-json-formatted-data"></a>JSON 書式付きデータを取り込んでマップする

JSON 書式付きデータを取り込むには、[インジェスト プロパティ](/azure/kusto/management/data-ingestion/index#ingestion-properties)を使用して "*書式*" を指定する必要があります。 JSON データを取り込むには、JSON ソース エントリをターゲット列にマップする[マッピング](/azure/kusto/management/mappings)が必要です。 データを取り込むときは、事前に定義された `jsonMappingReference` インジェスト プロパティを使用するか、`jsonMapping` インジェスト プロパティを指定します。 この記事では、インジェストに使用するテーブルで事前に定義されている `jsonMappingReference` インジェスト プロパティを使用します。 次の例では、最初に JSON レコードを生データとして 1 列のテーブルに取り込みます。 次に、マッピングを使用して、各プロパティを、そのマップされた列に取り込みます。 

### <a name="simple-json-example"></a>単純な JSON の例

フラットな構造体を持つ単純な JSON の例を次に示します。 データには、複数のデバイスによって収集された温度と湿度の情報が含まれています。 各レコードには、ID とタイムスタンプが付いています。

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>未加工の JSON レコードを取り込む 

この例では、JSON レコードを生データとして 1 列のテーブルに取り込みます。 データ操作、クエリの使用、および更新ポリシーは、データが取り込まれた後に行われます。

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

Kusto クエリ言語を使用して、未加工の JSON 形式でデータを取り込みます。

1. [https://dataexplorer.azure.com](https://dataexplorer.azure.com) にサインインします。

1. **[Add cluster]\(クラスターの追加\)** を選択します。

1. **[Add cluster]** ダイアログ ボックスで `https://<ClusterName>.<Region>.kusto.windows.net/` の形式でラスターの URL を入力して､ **[追加]** を選択します。

1. 次のコマンドを貼り付け、 **[実行]** を選択してテーブルを作成します。

    ```Kusto
    .create table RawEvents (Event: dynamic)
    ```

    このクエリでは、[動的](/azure/kusto/query/scalar-data-types/dynamic)データ型の 1 つの `Event` 列を含むテーブルを作成します。

1. JSON マッピングを作成します。

    ```Kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","path":"$"}]'
    ```

    このコマンドは、マッピングを作成し、JSON ルート パス `$` を `Event` 列にマップします。

1. `RawEvents` テーブルにデータを取り込みます。

    ```Kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=RawEventMapping)
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

C# を使用して、未加工の JSON 形式でデータを取り込みます。

1. `RawEvents` テーブルを作成します。

    ```C#
    var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
    var kustoConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder);

    var table = "RawEvents";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Events", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. JSON マッピングを作成します。
    
    ```C#
    var tableMapping = "RawEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                new JsonColumnMapping {ColumnName = "Events", JsonPath = "$"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```
    このコマンドは、マッピングを作成し、JSON ルート パス `$` を `Event` 列にマップします。

1. `RawEvents` テーブルにデータを取り込みます。

    ```C#
    var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var ingestConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder);
    
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

> [!NOTE]
> データは[バッチ処理ポリシー](/azure/kusto/concepts/batchingpolicy)に従って集約され、その結果、数分の待機時間が発生します。

# <a name="python"></a>[Python](#tab/python)

Python を使用して、未加工の JSON 形式でデータを取り込みます。

1. `RawEvents` テーブルを作成します。

    ```Python
    KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)
    KUSTO_CLIENT = KustoClient(KCSB_DATA)
    TABLE = "RawEvents"

    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Events: dynamic)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. JSON マッピングを作成します。

    ```Python
    MAPPING = "RawEventMapping"
    CREATE_MAPPING_COMMAND = ".create table " + TABLE + " ingestion json mapping '" + MAPPING + """' '[{"column":"Event","path":"$"}]'"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. `RawEvents` テーブルにデータを取り込みます。

    ```Python
    INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(INGEST_URI, AAD_TENANT_ID)
    INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    > [!NOTE]
    > データは[バッチ処理ポリシー](/azure/kusto/concepts/batchingpolicy)に従って集約され、その結果、数分の待機時間が発生します。

---

## <a name="ingest-mapped-json-records"></a>マップされた JSON レコードを取り込む

この例では、JSON レコード データを取り込みます。 各 JSON プロパティは、テーブル内の 1 つの列にマップされます。 

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. JSON 入力データに類似したスキーマを使用して、新しいテーブルを作成します。 このテーブルは、次のすべての例とインジェスト コマンドで使用します。 

    ```Kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. JSON マッピングを作成します。

    ```Kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'
    ```

    このマッピングでは、テーブル スキーマによって定義されているように、`timestamp` エントリは `datetime` データ型として列 `Time` に取り込まれます。

1. `Events` テーブルにデータを取り込みます。

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=FlatEventMapping)
    ```

    ファイル 'simple.json' には、行区切りの JSON レコードがいくつか含まれています。 形式は `json` であり、インジェスト コマンドで使用されるマッピングは、作成した `FlatEventMapping` です。

# <a name="c"></a>[C#](#tab/c-sharp)

1. JSON 入力データに類似したスキーマを使用して、新しいテーブルを作成します。 このテーブルは、次のすべての例とインジェスト コマンドで使用します。 

    ```C#
    var table = "Events";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Time", "System.DateTime"),
                Tuple.Create("Device", "System.String"),
                Tuple.Create("MessageId", "System.String"),
                Tuple.Create("Temperature", "System.Double"),
                Tuple.Create("Humidity", "System.Double"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. JSON マッピングを作成します。

    ```C#
    var tableMapping = "FlatEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.timestamp"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.deviceId"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.messageId"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.temperature"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.humidity"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

    このマッピングでは、テーブル スキーマによって定義されているように、`timestamp` エントリは `datetime` データ型として列 `Time` に取り込まれます。    

1. `Events` テーブルにデータを取り込みます。

    ```C#
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

    ファイル 'simple.json' には、行区切りの JSON レコードがいくつか含まれています。 形式は `json` であり、インジェスト コマンドで使用されるマッピングは、作成した `FlatEventMapping` です。

# <a name="python"></a>[Python](#tab/python)

1. JSON 入力データに類似したスキーマを使用して、新しいテーブルを作成します。 このテーブルは、次のすべての例とインジェスト コマンドで使用します。 

    ```Python
    TABLE = "RawEvents"
    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. JSON マッピングを作成します。

    ```Python
    MAPPING = "FlatEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. `Events` テーブルにデータを取り込みます。

    ```Python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    ファイル 'simple.json' には、行区切りの JSON レコードがいくつか含まれています。 形式は `json` であり、インジェスト コマンドで使用されるマッピングは、作成した `FlatEventMapping` です。    
---

## <a name="ingest-multi-lined-json-records"></a>複数行の JSON レコードを取り込む

この例では、複数行の JSON レコードを取り込みます。 各 JSON プロパティは、テーブル内の 1 つの列にマップされます。 ファイル 'multilined.json' には、インデントされた JSON レコードがいくつか含まれています。 `multijson` 形式は、JSON 構造体でレコードを読み取るようにエンジンに指示します。

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

`Events` テーブルにデータを取り込みます。

```Kusto
.ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=FlatEventMapping)
```

# <a name="c"></a>[C#](#tab/c-sharp)

`Events` テーブルにデータを取り込みます。

```C#
var tableMapping = "FlatEventMapping";
var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
var properties =
    new KustoQueuedIngestionProperties(database, table)
    {
        Format = DataSourceFormat.multijson,
        IngestionMappingReference = tableMapping
    };

ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
```

# <a name="python"></a>[Python](#tab/python)

`Events` テーブルにデータを取り込みます。

```Python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.JSON?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>配列を含む JSON レコードを取り込む

配列データ型は、順序が付けられた値のコレクションです。 JSON 配列の取り込みは、[更新ポリシー](/azure/kusto/management/update-policy)によって行われます。 JSON はそのまま中間テーブルに取り込まれます。 更新ポリシーは、`RawEvents` テーブルに対して定義済みの関数を実行し、結果をターゲット テーブルに再度取り込みます。 次の構造体でデータを取り込みます。

```json
{
    "records": 
    [
        {
            "timestamp": "2019-05-02 15:23:50.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "7f316225-839a-4593-92b5-1812949279b3",
            "temperature": 31.0301639051317,
            "humidity": 62.0791099602725
        },
        {
            "timestamp": "2019-05-02 15:23:51.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "57de2821-7581-40e4-861e-ea3bde102364",
            "temperature": 33.7529423105311,
            "humidity": 75.4787976739364
        }
    ]
}
```

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. `mv-expand` 演算子を使用して、コレクション内の各値が個別の行を受け取るように `records` のコレクションを展開する `update policy` 関数を作成します。 テーブル `RawEvents` をソース テーブルとして使用し、`Events` をターゲット テーブルとして使用します。

    ```Kusto
    .create function EventRecordsExpand() {
        RawEvents
        | mv-expand records = Event
        | project
            Time = todatetime(records["timestamp"]),
            Device = tostring(records["deviceId"]),
            MessageId = tostring(records["messageId"]),
            Temperature = todouble(records["temperature"]),
            Humidity = todouble(records["humidity"])
    }
    ```

1. 関数が受け取るスキーマは、ターゲット テーブルのスキーマと一致している必要があります。 `getschema` 演算子を使用してスキーマを確認します。

    ```Kusto
    EventRecordsExpand() | getschema
    ```

1. 更新ポリシーをターゲット テーブルに追加します。 このポリシーでは、`RawEvents` 中間テーブルに新しく取り込まれたデータに対してクエリが自動的に実行され、その結果が `Events` テーブルに取り込まれます。 中間テーブルが保持されないようにするために、ゼロ保持ポリシーを定義します。

    ```Kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. `RawEvents` テーブルにデータを取り込みます。

    ```Kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=RawEventMapping)
    ```

1. `Events` テーブル内のデータを確認します。

    ```Kusto
    Events
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

1. `mv-expand` 演算子を使用して、コレクション内の各値が個別の行を受け取るように `records` のコレクションを展開する update 関数を作成します。 テーブル `RawEvents` をソース テーブルとして使用し、`Events` をターゲット テーブルとして使用します。   

    ```C#
    var command =
        CslCommandGenerator.GenerateCreateFunctionCommand(
            "EventRecordsExpand",
            "UpdateFunctions",
            string.Empty,
            null,
            @"RawEvents
                | mv-expand records = Event
                | project
                    Time = todatetime(records['timestamp']),
                    Device = tostring(records['deviceId']),
                    MessageId = tostring(records['messageId']),
                    Temperature = todouble(records['temperature']),
                    Humidity = todouble(records['humidity'])",
            ifNotExists: false);

    kustoClient.ExecuteControlCommand(command);
    ```

    > [!NOTE]
    > 関数が受け取るスキーマは、ターゲット テーブルのスキーマと一致している必要があります。

1. 更新ポリシーをターゲット テーブルに追加します。 このポリシーでは、`RawEvents` 中間テーブルに新しく取り込まれたデータに対してクエリが自動的に実行され、その結果が `Events` テーブルに取り込まれます。 中間テーブルが保持されないようにするために、ゼロ保持ポリシーを定義します。

    ```C#
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. `RawEvents` テーブルにデータを取り込みます。

    ```C#
    var table = "RawEvents";
    var tableMapping = "RawEventMapping";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```
    
1. `Events` テーブル内のデータを確認します。

# <a name="python"></a>[Python](#tab/python)

1. `mv-expand` 演算子を使用して、コレクション内の各値が個別の行を受け取るように `records` のコレクションを展開する update 関数を作成します。 テーブル `RawEvents` をソース テーブルとして使用し、`Events` をターゲット テーブルとして使用します。   

    ```Python
    CREATE_FUNCTION_COMMAND = 
        '''.create function EventRecordsExpand() { 
            RawEvents
            | mv-expand records = Event
            | project
                Time = todatetime(records["timestamp"]),
                Device = tostring(records["deviceId"]),
                MessageId = tostring(records["messageId"]),
                Temperature = todouble(records["temperature"]),
                Humidity = todouble(records["humidity"])
            }'''
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_FUNCTION_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

    > [!NOTE]
    > 関数が受け取るスキーマは、ターゲット テーブルのスキーマと一致している必要があります。

1. 更新ポリシーをターゲット テーブルに追加します。 このポリシーでは、`RawEvents` 中間テーブルに新しく取り込まれたデータに対してクエリが自動的に実行され、その結果が `Events` テーブルに取り込まれます。 中間テーブルが保持されないようにするために、ゼロ保持ポリシーを定義します。

    ```Python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. `RawEvents` テーブルにデータを取り込みます。

    ```Python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. `Events` テーブル内のデータを確認します。

---    

## <a name="ingest-json-records-containing-dictionaries"></a>ディクショナリを含む JSON レコードを取り込む

ディクショナリ構造化 JSON には、キーと値のペアが含まれています。 JSON レコードには、`JsonPath` の論理式を使用してインジェスト マッピングが行われます。 次の構造体でデータを取り込むことができます。

```json
{
    "event": 
    [
        {
            "Key": "timestamp",
            "Value": "2019-05-02 15:23:50.0000000"
        },
        {
            "Key": "deviceId",
            "Value": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71"
        },
        {
            "Key": "messageId",
            "Value": "7f316225-839a-4593-92b5-1812949279b3"
        },
        {
            "Key": "temperature",
            "Value": 31.0301639051317
        },
        {
            "Key": "humidity",
            "Value": 62.0791099602725
        }
    ]
}
```

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. JSON マッピングを作成します。

    ```Kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'
    ```

1. `Events` テーブルにデータを取り込みます。

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=KeyValueEventMapping)
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

1. JSON マッピングを作成します。

    ```C#
    var tableName = "Events";
    var tableMapping = "KeyValueEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.event[?(@.Key == 'timestamp')]"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.event[?(@.Key == 'deviceId')]"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.event[?(@.Key == 'messageId')]"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.event[?(@.Key == 'temperature')]"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.event[?(@.Key == 'humidity')]"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. `Events` テーブルにデータを取り込みます。

    ```C#
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

# <a name="python"></a>[Python](#tab/python)

1. JSON マッピングを作成します。

    ```Python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. `Events` テーブルにデータを取り込みます。

     ```Python
    MAPPING = "KeyValueEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)u
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

---    

## <a name="next-steps"></a>次のステップ

* [データ取り込みの概要](ingest-data-overview.md)
* [クエリを作成する](write-queries.md)
