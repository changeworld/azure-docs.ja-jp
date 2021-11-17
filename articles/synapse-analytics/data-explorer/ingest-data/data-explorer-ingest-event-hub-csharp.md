---
title: C\# インジェストを使用してイベント ハブから Azure Synapse Data Explorer にデータを取り込む (プレビュー)
description: C\# を使用してイベント ハブから Azure Synapse Data Explorer にデータを取り込む (読み込む) 方法について学習します。
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 9e8cf21f2cb54246152015e42d6c0e5b2cabcad5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477982"
---
# <a name="create-an-event-hub-data-connection-for-azure-synapse-data-explorer-by-using-c-preview"></a>C\# を使用して Azure Synapse Data Explorer 用にイベント ハブ データ接続を作成する (プレビュー)

> [!div class="op_single_selector"]
> * [ポータル](data-explorer-ingest-event-hub-portal.md)
> * [ワンクリック](data-explorer-ingest-event-hub-one-click.md)
> * [C\#](data-explorer-ingest-event-hub-csharp.md)
> * [Python](data-explorer-ingest-event-hub-python.md)
> * [Azure Resource Manager テンプレート](data-explorer-ingest-event-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](../includes/data-explorer-ingest-data-intro.md)]

この記事では、C\# を使用して Azure Synapse Data Explorer 用にイベント ハブ データ接続を作成します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- [取り込み用のデータが含まれるイベント ハブ](data-explorer-ingest-event-hub-portal.md#create-an-event-hub)。

> [!NOTE]
> Synapse ワークスペースでデータ流出防止が有効になっているマネージド仮想ネットワークを使用している場合、イベント ハブから データ エクスプローラー プールへのデータの取り込みは機能しません。

- Visual Studio 2019。**無料の** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) をダウンロードして使用します。 Visual Studio のセットアップ中に、 **[Azure の開発]** を有効にしてください。

<!-- * Set [database and table policies](database-table-policies-csharp.md) (optional). -->

[!INCLUDE [data-explorer-ingest-event-hub-table-mapping](../includes/data-explorer-ingest-event-hub-table-mapping.md)]

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>イベント ハブ データ接続を追加する

次の例は、イベント ハブ データ接続をプログラムを使用して追加する方法を示しています。 Azure portal を使用してイベント ハブ データ接続を追加する方法については、[イベント ハブへの接続](data-explorer-ingest-event-hub-portal.md#connect-to-the-event-hub)に関するページを参照してください。

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The Event Hub that is created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;
var compression = "None";
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName,
    new EventHubDataConnection(eventHubResourceId, consumerGroup, location: location, tableName: tableName, mappingRuleName: mappingRuleName, dataFormat: dataFormat, compression: compression));
```

|**設定** | **推奨値** | **フィールドの説明**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | テナント ID。 ディレクトリ ID とも呼ばれます。|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | リソースの作成に使用するサブスクリプション ID。|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ご利用のテナント内のリソースにアクセスできるアプリケーションのクライアント ID。|
| clientSecret | *xxxxxxxxxxxxxx* | ご利用のテナント内のリソースにアクセスできるアプリケーションのクライアント シークレット。|
| resourceGroupName | *testrg* | ご利用のクラスターを含むリソース グループの名前。|
| clusterName | *mykustocluster* | ご利用のクラスターの名前。|
| databaseName | *mykustodatabase* | ご利用のクラスター内のターゲット データベースの名前。|
| dataConnectionName | *myeventhubconnect* | データ接続の任意の名前。|
| tableName | *StormEvents* | ターゲット データベース内のターゲット テーブルの名前。|
| mappingRuleName | *StormEvents_CSV_Mapping* | ターゲット テーブルに関連付けられている列マッピングの名前。|
| dataFormat | *csv* | メッセージのデータ形式。|
| eventHubResourceId | *リソース ID* | インジェスト用のデータを保持しているイベント ハブのリソース ID。 |
| consumerGroup | *$Default* | ご利用のイベント ハブのコンシューマー グループ。|
| location | *米国中部* | データ接続リソースの場所。|
| compression | *Gzip* または *None* | データ圧縮の種類。 |

## <a name="generate-data"></a>データの生成

データを生成してイベント ハブに送信する[サンプル アプリ](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)をご覧ください。

イベントには、そのサイズ制限を上限として、1 つ以上のレコードを含めることができます。 次の例では、2 つのイベントを送信します。それぞれに 5 つのレコードが追加されています。

```csharp
var events = new List<EventData>();
var data = string.Empty;
var recordsPerEvent = 5;
var rand = new Random();
var counter = 0;

for (var i = 0; i < 10; i++)
{
    // Create the data
    var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = rand.Next(-30, 50) };
    var data += JsonConvert.SerializeObject(metric) + Environment.NewLine;
    counter++;

    // Create the event
    if (counter == recordsPerEvent)
    {
        var eventData = new EventData(Encoding.UTF8.GetBytes(data));
        events.Add(eventData);

        counter = 0;
        data = string.Empty;
    }
}

// Send events
eventHubClient.SendAsync(events).Wait();
```

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../includes/data-explorer-data-connection-clean-resources-csharp.md)]

## <a name="next-steps"></a>次の手順

- [Data Explorer を使用して分析する](../../get-started-analyze-data-explorer.md)
- [Data Explorer プールを監視する](../data-explorer-monitor-pools.md)
