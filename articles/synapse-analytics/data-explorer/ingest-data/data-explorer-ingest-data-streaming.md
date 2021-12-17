---
title: Azure Synapse Data Explorer プールでのストリーミング インジェストの構成
description: Azure Synapse Data Explorer プールを構成し、ストリーミング インジェストによるデータの読み込みを開始する方法について説明します。
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: d7fdc238adda294a570b7656d62368a5d4feae9b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478012"
---
# <a name="configure-streaming-ingestion-on-your-azure-synapse-data-explorer-pool-preview"></a>Azure Synapse Data Explorer プールでのストリーミング インジェストの構成 (プレビュー)

ストリーミング インジェストによるデータの読み込みは、インジェストとクエリの間で待機時間を短くする必要がある場合に役立ちます。 次のシナリオでは、ストリーミング インジェストの使用を検討してください。

- 待機時間を 1 秒未満にする必要がある。
- 各テーブルへのデータ ストリームが比較的小さい (1 秒あたりのレコード数が少ない) が、データ インジェスト ボリューム全体が高い (1 秒あたり数千レコード) 場合に、多数のテーブルの操作処理を最適化する。

各テーブルへのデータ ストリームが高い場合 (1 時間あたり 4 GB を超える場合) は、[バッチ インジェスト](/azure/data-explorer/kusto/management/batchingpolicy?context=/azure/synapse-analytics/context/context)の使用を検討してください。

さまざまなインジェスト方法の詳細については、「[データ取り込みの概要](data-explorer-ingest-data-overview.md)」を参照してください。

## <a name="choose-the-appropriate-streaming-ingestion-type"></a>適切なストリーミング インジェストの種類を選択する

2 種類のストリーミング インジェストがサポートされています。

| インジェストの種類 | 説明 |
| -- | -- |
| **イベント ハブ** または **IoT ハブ** | ハブは、テーブル ストリーミングのデータ ソースとして構成されます。<br />これらを設定する方法については、「[**Event Hub**](data-explorer-ingest-event-hub-overview.md)」を参照してください。 <!--  or [**IoT Hub**](ingest-data-iot-hub.md) data ingestion methods --> |
| **カスタム インジェスト** | カスタム インジェストでは、Azure Synapse Data Explorer [クライアント ライブラリ](/azure/data-explorer/kusto/api/client-libraries?context=/azure/synapse-analytics/context/context)のいずれかを使用するアプリケーションを作成する必要があります。<br />このトピックの情報を参照して、カスタム インジェストを構成することができます。 また、[C# ストリーミング インジェストのサンプル アプリケーション](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample)に関する記事も役に立つ場合があります。 |

次の表を使用して、お使いの環境に適したインジェストの種類を選択してください。

|条件|イベント ハブ/IoT ハブ|カスタム インジェスト|
|---------|---------|---------|
|インジェスト開始からクエリでデータが使用可能になるまでのデータ遅延 | より長い遅延 | より短い遅延 |
|開発のオーバーヘッド | 高速で簡単なセットアップ、開発オーバーヘッドなし | アプリケーションを作成してデータを取り込み、エラーを処理し、データの一貫性を確保するための高い開発オーバーヘッド |

> [!NOTE]
> Synapse ワークスペースでデータ流出防止が有効になっているマネージド仮想ネットワークを使用している場合、イベント ハブから Data Explorer プールへのデータの取り込みは機能しません。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- クエリとデータ インジェストのエンドポイントを取得します。
    [!INCLUDE [data-explorer-get-endpoint](../includes/data-explorer-get-endpoint.md)]

## <a name="performance-and-operational-considerations"></a>操作および運用面の考慮事項

ストリーミング インジェストに影響を与える可能性のある主な要因は、次のとおりです。

- **コンピューティング仕様**: ストリーミング インジェストのパフォーマンスと容量は、Data Explorer プールのサイズを増やしてスケーリングします。 同時インジェスト要求の数は、コアあたり 6 個に制限されています。 たとえば、コンピューティング最適化 (Large) と ストレージ最適化 (Large) など、16 コアのワークロードの種類の場合、サポートされる最大負荷は 96 同時インジェスト要求です。 コンピューティング最適化 (Extra Small) などの 2 つのコアのワークロードの種類では、サポートされる最大負荷は 12 個の同時インジェスト要求です。
- **データ サイズの制限**: ストリーミング インジェスト要求のデータ サイズの制限は 4 MB です。
- **スキーマの更新**: テーブルとインジェスト マッピングの作成や変更など、スキーマの更新には、ストリーミング インジェスト サービスで最大 5 分かかることがあります。 詳細については、「[ストリーミング インジェストとスキーマ変更](/azure/data-explorer/kusto/management/data-ingestion/streaming-ingestion-schema-changes?context=/azure/synapse-analytics/context/context)」を参照してください。
- **SSD 容量**: Data Explorer プールでストリーミング インジェストを有効にすると、データがストリーミング経由で取り込まれていない場合でも、インジェスト データをストリーミングするために Data Explorer プール マシンのローカル SSD ディスクの一部を使用して、ホット キャッシュに使用できるストレージを減らします。

## <a name="enable-streaming-ingestion-on-your-data-explorer-pool"></a>Data Explorer プールでストリーミング インジェストを有効にする

ストリーミング インジェストを使用するには、Data Explorer プールで機能を有効にし、[ストリーミング インジェスト ポリシー](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context)を定義する必要があります。 [Data Explorer プールを作成](#enable-streaming-ingestion-while-creating-a-new-data-explorer-pool)するときに機能を有効にすることも、[既存の Data Explorer プールに追加](#enable-streaming-ingestion-on-an-existing-data-explorer-pool)することもできます。

> [!WARNING]
> ストリーミング インジェストを有効にする前に[制限事項](#limitations)を確認してください。

### <a name="enable-streaming-ingestion-while-creating-a-new-data-explorer-pool"></a>新しい Data Explorer プールを作成しているときにストリーミング インジェストを有効にする

Azure Synapse Studio または Azure portal を使用して新しい Data Explorer プールを作成するときに、ストリーミング インジェストを有効にすることができます。

#### <a name="studio"></a><bpt id="p1">[</bpt>スタジオ<ept id="p1">](#tab/azure-studio)</ept>

「[Synapse Studio を使用した Data Explorer プールの作成](../data-explorer-create-pool-studio.md#create-a-new-data-explorer-pool)」の手順に従って Data Explorer プールを作成するときに、 **[追加設定]** タブで [**ストリーミング インジェスト**  > **が有効**] を選択します。

:::image type="content" source="../media/ingest-data-streaming/create-data-explorer-pool-advanced-settings-studio.png" alt-text="Azure Synapse Data Explorer で Data Explorer プールを作成するときに、ストリーミング インジェストを有効にします。":::

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

「[Azure portal を使用した Data Explorer プールの作成](../data-explorer-create-pool-portal.md#create-a-new-data-explorer-pool)」の手順に従って Data Explorer プールを作成するときに、 **[追加設定]** タブで [**ストリーミング インジェスト**  > **が有効**] を選択します。

:::image type="content" source="../media/ingest-data-streaming/create-data-explorer-pool-advanced-settings-portal.png" alt-text="Azure Synapse Data Explorer で Data Explorer プールを作成するときに、ストリーミング インジェストを有効にします。":::

---

### <a name="enable-streaming-ingestion-on-an-existing-data-explorer-pool"></a>既存の Data Explorer プールでストリーミング インジェストを有効にする

既存の Data Explorer プールがある場合は、Azure portal を使用してストリーミング インジェストを有効にすることができます。

1. Azure portal で、Data Explorer プールに移動します。
1. **[設定]** で **[構成]** を選択します。
1. **[構成]** ウィンドウで、 **[オン]** を選択して **[ストリーミング インジェスト]** を有効にします。
1. **[保存]** を選択します。

### <a name="create-a-target-table-and-define-the-policy"></a>ターゲット テーブルを作成し、ポリシーを定義する

Azure Synapse Studio または Azure portal を使用することにより、ストリーミング インジェスト データを受け取るテーブルを作成し、関連するポリシーを定義します。

#### <a name="studio"></a><bpt id="p1">[</bpt>スタジオ<ept id="p1">](#tab/azure-studio)</ept>

1. Synapse Studio の左側のウィンドウで、 **[開発]** を選択します。
1. **[KQL スクリプト]** で、 **&plus;** (新しいリソースの追加) > **[KQL スクリプト]** を選択します。 右側のウィンドウで、スクリプト名を指定できます。
1. **[接続先]** メニューで、[*contosodataexplorer*] を選択します。
1. **[データベースの使用]** メニューで、 *[TestDatabase]* を選択します。
1. 次のコマンドを貼り付け、 **[実行]** を選択してテーブルを作成します。

    ```kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

1. 次のコマンドのいずれかを **[クエリ] ペイン** にコピーし、 **[実行]** を選択します。 これにより、作成したテーブルまたはそのテーブルを含むデータベースで、[ストリーミング インジェスト ポリシー](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context)が定義されます。

    > [!TIP]
    > データベース レベルで定義されているポリシーは、データベース内の既存および将来のすべてのテーブルに適用されます。

    - 作成したテーブルでポリシーを定義するには、次を使用します。

        ```kusto
        .alter table TestTable policy streamingingestion enable
        ```

    - 作成したテーブルを含むデータベースでポリシーを定義するには、次を使用します。

        ```kusto
        .alter database StreamingTestDb policy streamingingestion enable
        ```

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Azure Synapse Analytics ポータルで、Data Explorer プールに移動します。
1. **[クエリ]** を選択します。
1. ストリーミング インジェスト経由でデータを受け取るテーブルを作成するには、次コマンドを **[クエリ] ペイン** にコピーし、 **[実行]** を選択します。

    ```kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

1. 次のコマンドのいずれかを **[クエリ] ペイン** にコピーし、 **[実行]** を選択します。 これにより、作成したテーブルまたはそのテーブルを含むデータベースで、[ストリーミング インジェスト ポリシー](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context)が定義されます。

    > [!TIP]
    > データベース レベルで定義されているポリシーは、データベース内の既存および将来のすべてのテーブルに適用されます。

    - 作成したテーブルでポリシーを定義するには、次を使用します。

        ```kusto
        .alter table TestTable policy streamingingestion enable
        ```

    - 作成したテーブルを含むデータベースでポリシーを定義するには、次を使用します。

        ```kusto
        .alter database StreamingTestDb policy streamingingestion enable
        ```

---

## <a name="create-a-streaming-ingestion-application-to-ingest-data-to-your-data-explorer-pool"></a>ストリーミング インジェスト アプリケーションを作成して Data Explorer プールにデータを取り込む

希望する言語を使用して、お使いの Data Explorer プールにデータを取り込むためのアプリケーションを作成します。 *poolpath* 変数には、「[前提条件](#prerequisites)」でメモしたクエリ エンドポイントを使用します。

### <a name="c"></a>[C#](#tab/csharp)

```csharp
using Kusto.Data;
using Kusto.Ingest;
using System.IO;
using Kusto.Data.Common;

namespace StreamingIngestion
{
    class Program
    {
        static void Main(string[] args)
        {
            string poolPath = "https://<Poolname>.<WorkspaceName>.kusto.windows.net";
            string appId = "<appId>";
            string appKey = "<appKey>";
            string appTenant = "<appTenant>";
            string dbName = "<dbName>";
            string tableName = "<tableName>";

            // Create Kusto connection string with App Authentication
            var csb =
                new KustoConnectionStringBuilder(poolPath)
                    .WithAadApplicationKeyAuthentication(
                        applicationClientId: appId,
                        applicationKey: appKey,
                        authority: appTenant
                    );

            // Create a disposable client that will execute the ingestion
            using (IKustoIngestClient client = KustoIngestFactory.CreateStreamingIngestClient(csb))
            {
                // Initialize client properties
                var ingestionProperties =
                    new KustoIngestionProperties(
                        databaseName: dbName,
                        tableName: tableName
                    );

                // Ingest from a compressed file
                var fileStream = File.Open("MyFile.gz", FileMode.Open);
                // Create source options
                var sourceOptions = new StreamSourceOptions()
                {
                    CompressionType = DataSourceCompressionType.GZip,
                };
                // Ingest from stream
                var status = client.IngestFromStreamAsync(fileStream, ingestionProperties, sourceOptions).GetAwaiter().GetResult();
            }
        }
    }
}
```

### <a name="python"></a>[Python](#tab/python)

```python
from Azure Synapse Analytics.kusto.data import KustoConnectionStringBuilder

from Azure Synapse Analytics.kusto.ingest import (
    IngestionProperties,
    DataFormat,
    KustoStreamingIngestClient
)

poolPath = "https://<Poolname>.<WorkspaceName>.kusto.windows.net"
appId = "<appId>"
appKey = "<appKey>"
appTenant = "<appTenant>"
dbName = "<dbName>"
tableName = "<tableName>"

csb = KustoConnectionStringBuilder.with_aad_application_key_authentication(
    poolPath,
    appId,
    appKey,
    appTenant
)
client = KustoStreamingIngestClient(csb)

ingestionProperties = IngestionProperties(
    database=dbName,
    table=tableName,
    data_format=DataFormat.CSV
)

# Ingest from file
# Automatically detects gz format
client.ingest_from_file("MyFile.gz", ingestion_properties=ingestionProperties) 
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```nodejs
// Load modules using ES6 import statements:
import { DataFormat, IngestionProperties, StreamingIngestClient } from "azure-kusto-ingest";
import { KustoConnectionStringBuilder } from "azure-kusto-data";

// For earlier version, load modules using require statements:
// const IngestionProperties = require("azure-kusto-ingest").IngestionProperties;
// const KustoConnectionStringBuilder = require("azure-kusto-data").KustoConnectionStringBuilder;
// const {DataFormat} = require("azure-kusto-ingest").IngestionPropertiesEnums;
// const StreamingIngestClient = require("azure-kusto-ingest").StreamingIngestClient;

const poolPath = "https://<Poolname>.<WorkspaceName>.kusto.windows.net";
const appId = "<appId>";
const appKey = "<appKey>";
const appTenant = "<appTenant>";
const dbName = "<dbName>";
const tableName = "<tableName>";
const mappingName = "<mappingName>"; // Required for JSON formatted files

const ingestionProperties = new IngestionProperties({
    database: dbName, // Your database
    table: tableName, // Your table
    format: DataFormat.JSON,
    ingestionMappingReference: mappingName
});

// Initialize client with engine endpoint
const client = new StreamingIngestClient(
    KustoConnectionStringBuilder.withAadApplicationKeyAuthentication(
        poolPath,
        appId,
        appKey,
        appTenant
    ),
    ingestionProperties
);

// Automatically detects gz format
await client.ingestFromFile("MyFile.gz", ingestionProperties);
```

### <a name="go"></a>[Go](#tab/go)

```go
import (
    "context"
    "github.com/Azure Synapse Analytics/azure-kusto-go/kusto"
    "github.com/Azure Synapse Analytics/azure-kusto-go/kusto/ingest"
    "github.com/Azure Synapse Analytics/go-autorest/autorest/Azure Synapse Analytics/auth"
)

func ingest() {
    poolPath := "https://<Poolname>.<WorkspaceName>.kusto.windows.net"
    appId := "<appId>"
    appKey := "<appKey>"
    appTenant := "<appTenant>"
    dbName := "<dbName>"
    tableName := "<tableName>"
    mappingName := "<mappingName>" // Optional, can be nil

    // Creates a Kusto Authorizer using your client identity, secret, and tenant identity.
    // You may also uses other forms of authorization, see GoDoc > Authorization type.
    // auth package is: "github.com/Azure Synapse Analytics/go-autorest/autorest/Azure Synapse Analytics/auth"
    authorizer := kusto.Authorization{
        Config: auth.NewClientCredentialsConfig(appId, appKey, appTenant),
    }

    // Create a client
    client, err := kusto.New(poolPath, authorizer)
    if err != nil {
        panic("add error handling")
    }

    // Create an ingestion instance
    // Pass the client, the name of the database, and the name of table you wish to ingest into.
    in, err := ingest.New(client, dbName, tableName)
    if err != nil {
        panic("add error handling")
    }

    // Go currently only supports streaming from a byte array with a maximum size of 4 MB.
    jsonEncodedData := []byte("{\"a\":  1, \"b\":  10}\n{\"a\":  2, \"b\":  20}")

    // Ingestion from a stream commits blocks of fully formed data encodes (JSON, AVRO, ...) into Kusto:
    if err := in.Stream(context.Background(), jsonEncodedData, ingest.JSON, mappingName); err != nil {
        panic("add error handling")
    }
}
```

### <a name="java"></a>[Java](#tab/java)

```java
import com.microsoft.Azure Synapse Analytics.kusto.data.auth.ConnectionStringBuilder;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.IngestClient;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.IngestClientFactory;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.IngestionProperties;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.result.OperationStatus;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.source.CompressionType;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.source.StreamSourceInfo;
import java.io.FileInputStream;
import java.io.InputStream;

public class FileIngestion {
    public static void main(String[] args) throws Exception {
        String poolPath = "https://<Poolname>.<WorkspaceName>.kusto.windows.net";
        String appId = "<appId>";
        String appKey = "<appKey>";
        String appTenant = "<appTenant>";
        String dbName = "<dbName>";
        String tableName = "<tableName>";

        // Build connection string and initialize
        ConnectionStringBuilder csb =
            ConnectionStringBuilder.createWithAadApplicationCredentials(
                poolPath,
                appId,
                appKey,
                appTenant
            );

        // Initialize client and its properties
        IngestClient client = IngestClientFactory.createClient(csb);
        IngestionProperties ingestionProperties =
            new IngestionProperties(
                dbName,
                tableName
            );

        // Ingest from a compressed file
        // Create Source info
        InputStream zipInputStream = new FileInputStream("MyFile.gz");
        StreamSourceInfo zipStreamSourceInfo = new StreamSourceInfo(zipInputStream);
        // If the data is compressed
        zipStreamSourceInfo.setCompressionType(CompressionType.gz);
        // Ingest from stream
        OperationStatus status = client.ingestFromStream(zipStreamSourceInfo, ingestionProperties).getIngestionStatusCollection().get(0).status;
    }
}
```

---

## <a name="disable-streaming-ingestion-on-your-data-explorer-pool"></a>Data Explorer プールでストリーミング インジェストを無効にする

> [!WARNING]
> ストリーミング インジェストの無効化には数時間かかることがあります。

Data Explorer プールでストリーミング インジェストを無効にする前に、関連するすべてのテーブルとデータベースから[ストリーミング インジェスト ポリシー](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context)を削除します。 ストリーミング インジェスト ポリシーを削除すると、Data Explorer プール内でデータの再配置がトリガーされます。 ストリーミング インジェストのデータは、初期ストレージから列ストア (エクステントまたはシャード) 内の永続的なストレージへ移動されます。 初期ストレージ内のデータ量によっては、このプロセスに数秒から数時間かかることがあります。

### <a name="drop-the-streaming-ingestion-policy"></a>ストリーミング インジェスト ポリシーをドロップする

ストリーミング インジェスト ポリシーをドロップするには、Azure Synapse Studio または Azure portal を使用します。

#### <a name="studio"></a><bpt id="p1">[</bpt>スタジオ<ept id="p1">](#tab/azure-studio)</ept>

1. Synapse Studio の左側のウィンドウで、 **[開発]** を選択します。
1. **[KQL スクリプト]** で、 **&plus;** (新しいリソースの追加) > **[KQL スクリプト]** を選択します。 右側のウィンドウで、スクリプト名を指定できます。
1. **[接続先]** メニューで、[*contosodataexplorer*] を選択します。
1. **[データベースの使用]** メニューで、 *[TestDatabase]* を選択します。
1. 次のコマンドを貼り付け、 **[実行]** を選択してテーブルを作成します。

    ```kusto
    .delete table TestTable policy streamingingestion
    ```

1. Azure portal で、Data Explorer プールに移動します。
1. **[設定]** で **[構成]** を選択します。
1. **[構成]** ウィンドウで、 **[オン]** を選択して **[ストリーミング インジェスト]** を有効にします。
1. **[保存]** を選択します。

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Azure portal で、Data Explorer プールに移動し、 **[クエリ]** を選択します。
1. ストリーミング インジェストポリシーをテーブルからドロップするには、次のコマンドを **[クエリ] ペイン** にコピーし、 **[実行]** を選択します。

    ```Kusto
    .delete table TestTable policy streamingingestion
    ```

1. **[設定]** で **[構成]** を選択します。
1. **[構成]** ウィンドウで、 **[オフ]** を選択して **[ストリーミング インジェスト]** を無効にします。
1. **[保存]** を選択します。

---

## <a name="limitations"></a>制限事項

- データベース自体またはそのいずれかのテーブルに[ストリーミング インジェスト ポリシー](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context)が定義され、有効になっている場合、データベースで[データベース カーソル](/azure/data-explorer/kusto/management/databasecursor?context=/azure/synapse-analytics/context/context)はサポートされません。
- [データ マッピング](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context)は、ストリーミング インジェストで使用するために[事前に作成](/azure/data-explorer/kusto/management/create-ingestion-mapping-command?context=/azure/synapse-analytics/context/context)する必要があります。 個々のストリーミング インジェスト要求は、インライン データ マッピングには対応していません。
- [extent タグ](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context#extent-tagging)は、ストリーミング インジェスト データには設定できません。
- [更新ポリシー](/azure/data-explorer/kusto/management/updatepolicy?context=/azure/synapse-analytics/context/context)。 更新ポリシーは、ソース テーブル内に新しく取り込まれたデータのみを参照でき、データベース内のその他のデータまたはテーブルは参照できません。
- データベースのいずれかのテーブルでストリーミング インジェストが使用されている場合、そのデータベースをフォロワー データベースのリーダーとして、または Azure Synapse Analytics Data Share のデータ プロバイダーとして使用することはできません。

## <a name="next-steps"></a>次の手順

- [Data Explorer を使用して分析する](../../get-started-analyze-data-explorer.md)
- [Data Explorer プールを監視する](../data-explorer-monitor-pools.md)
