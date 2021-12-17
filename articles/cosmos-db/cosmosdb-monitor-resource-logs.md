---
title: Azure の診断設定を使用して Azure Cosmos DB データを監視する
description: Azure の診断設定を使用して、Azure Cosmos DB に格納されているデータのパフォーマンスと可用性を監視する方法について説明します
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/20/2021
ms.author: sngun
ms.openlocfilehash: 55e84478d8744aae05f8f3a0df89aac605d6de12
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124779922"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Azure の診断設定を使用して Azure Cosmos DB データを監視する
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure の診断設定は、リソース ログの収集に使用されます。 Azure のリソース ログは、リソースによって出力され、そのリソースの操作に関する豊富なデータを提供します。 これらのログは、要求ごとにキャプチャされ、"データ プレーン ログ" とも呼ばれます。 データ プレーン操作の例としては、delete、insert、readFeed などがあります。 これらのログの内容は、リソースの種類によって異なります。

プラットフォーム メトリックとアクティビティ ログは自動的に収集されますが、リソース ログを収集したり、それらを Azure Monitor の外部に転送したりするには、診断設定を作成する必要があります。 Azure Cosmos DB アカウントの診断設定を有効にしてリソース ログを次のソースに送信できます。
- Log Analytics ワークスペース
  - Log Analytics に送信されるデータを、**Azure Diagnostics (レガシ)** または **リソース固有(プレビュー)** のテーブルに書き込むことができます 
- イベント ハブ
- ストレージ アカウント
  
> [!NOTE]
> [REST API を介して診断設定を作成するための手順に従って](cosmosdb-monitor-resource-logs.md#create-diagnostic-setting)診断設定をリソース固有のモードで作成することをお勧めします (Table API を除くすべての API の場合)。 このオプションを使用するとさらなるコスト最適化が得られ、データ処理のビューも向上します。

## <a name="create-diagnostics-settings-via-the-azure-portal"></a><a id="create-setting-portal"></a> Azure portal を使用して診断設定を作成する

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. Azure Cosmos アカウントに移動します。 **[監視]** セクションの **[診断設定]** ペインを開き、 **[診断設定の追加]** オプションを選択します。

   :::image type="content" source="./media/monitor-cosmos-db/diagnostics-settings-selection.png" alt-text="診断の選択":::


3. **[診断設定]** ウィンドウで、フォームに希望のカテゴリを入力します。

### <a name="choose-log-categories"></a>ログのカテゴリの選択

   |カテゴリ  |API   | 定義  | キー プロパティ   |
   |---------|---------|---------|---------|
   |DataPlaneRequests     |  すべての API        |     データ プレーン操作としてバック エンド要求をログに記録します。これは、アカウント内のデータを作成、更新、削除、または取得するために実行される要求です。   |   `Requestcharge`, `statusCode`, `clientIPaddress`, `partitionID`, `resourceTokenPermissionId` `resourceTokenPermissionMode`      |
   |MongoRequests     |    Mongo    |   Azure Cosmos DB の MongoDB 用 API への要求を処理するために、ユーザーがフロントエンドから開始した要求をログに記録します。 このカテゴリを有効にするときは、必ず DataPlaneRequests を無効にしてください。      |  `Requestcharge`, `opCode`, `retryCount`, `piiCommandText`      |
   |CassandraRequests     |   Cassandra      |    Azure Cosmos DB の Cassandra 用 API への要求を処理するために、ユーザーがフロントエンドから開始した要求をログに記録します。 このカテゴリを有効にするときは、必ず DataPlaneRequests を無効にしてください。     |     `operationName`, `requestCharge`, `piiCommandText`    |
   |GremlinRequests     |    Gremlin    |     Azure Cosmos DB の Gremlin 用 API への要求を処理するために、ユーザーがフロントエンドから開始した要求をログに記録します。 このカテゴリを有効にするときは、必ず DataPlaneRequests を無効にしてください。    |   `operationName`, `requestCharge`, `piiCommandText`, `retriedDueToRateLimiting`       |
   |QueryRuntimeStatistics     |   SQL      |     このテーブルは、SQL API アカウントに対して実行されるクエリ操作の詳細を示します。 既定では、クエリ テキストとそのパラメーターは難読化されるため、要求によって利用可能なフル テキスト クエリ ログ記録に個人データがログ記録されることが防止されます。    |    `databasename`, `partitionkeyrangeid`, `querytext`    |
   |PartitionKeyStatistics     |    すべての API     |   論理パーティション キーの統計情報を、パーティション キーの推定ストレージ サイズ (KB) を示すことにより、ログ記録します。 この表は、ストレージ スキューをトラブルシューティングするときに便利です。 この PartitionKeyStatistics ログは、次の条件に該当する場合にのみ出力されます。 <br/><ul><li> 物理パーティション内のドキュメントの少なくとも 1% が同じ論理パーティション キーを持っている。 </li><li> 物理パーティション内のすべてのキーのうち、ストレージ サイズが最も大きい上位 3 つのキーが PartitionKeyStatistics ログによってキャプチャされる。 </li></ul> 前の条件が満たされていない場合、パーティション キーの統計データは使用できません。 アカウントで上記の条件が満たされていなくても問題ありません。通常、これは論理パーティションのストレージ スキューがないことを示しています。 <br/><br/>注: パーティション キーの推定サイズは、物理パーティション内のドキュメントが概ね同じサイズであると想定するサンプリング アプローチを使用して計算されます。 物理パーティション内のドキュメントのサイズが均一でない場合、パーティション キーの推定サイズが正確でなくなる可能性があります。  |   `subscriptionId`, `regionName`, `partitionKey`, `sizeKB`      |
   |PartitionKeyRUConsumption     |   SQL API    |     パーティション キーの RU (秒単位) の合計消費量をログに記録します。 この表は、ホット パーティションのトラブルシューティングで役立ちます。 現在のところ Azure Cosmos DB では、SQL API アカウントのみについて、およびポイントの読み取りまたは書き込みとストアド プロシージャ操作について、パーティション キーが報告されます。   |     `subscriptionId`, `regionName`, `partitionKey`, `requestCharge`, `partitionKeyRangeId`   |
   |ControlPlaneRequests     |   すべての API       |    アカウントの作成、リージョンの追加または削除、アカウントのレプリケーション設定の更新など、コントロール プレーンの操作に関する詳細をログに記録します。     |    `operationName`, `httpstatusCode`, `httpMethod`, `region`       |
   |TableApiRequests     |   テーブル API    |     Azure Cosmos DB の Table 用 API への要求を処理するために、ユーザーがフロントエンドから開始した要求をログに記録します。 このカテゴリを有効にするときは、必ず DataPlaneRequests を無効にしてください。       |    `operationName`, `requestCharge`, `piiCommandText`     |

4. **カテゴリの詳細** を選択したら、適切な宛先にログを送信します。 **Log Analytics ワークスペース** にログを送信する場合は、[対象テーブル] で **[リソース固有]** を選択してください。

    :::image type="content" source="./media/monitor-cosmos-db/diagnostics-resource-specific.png" alt-text="[リソース固有] を選択して有効にします":::

## <a name="create-diagnostic-setting-via-rest-api"></a><a id="create-diagnostic-setting"></a> REST API を介した診断設定の作成
[Azure Monitor REST API](/rest/api/monitor/diagnosticsettings/createorupdate) を、対話型コンソールを介して診断設定を作成するために使用します。
> [!Note]
> **logAnalyticsDestinationType** プロパティを **Dedicated** に設定してリソース固有のテーブルを有効にすることをお勧めします。

### <a name="request"></a>Request

   ```HTTP
   PUT
   https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
   ```

### <a name="headers"></a>ヘッダー

   |パラメーター/ヘッダー  | 値/説明  |
   |---------|---------|
   |name     |  診断設定の名前です。      |
   |resourceUri     |   subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDb/databaseAccounts/{ACCOUNT_NAME}/providers/microsoft.insights/diagnosticSettings/{DIAGNOSTIC_SETTING_NAME}      |
   |api-version     |    2017-05-01-preview     |
   |Content-Type     |    application/json     |

### <a name="body"></a>本文

```json
{
    "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDb/databaseAccounts/{ACCOUNT_NAME}/providers/microsoft.insights/diagnosticSettings/{DIAGNOSTIC_SETTING_NAME}",
    "type": "Microsoft.Insights/diagnosticSettings",
    "name": "name",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": null,
        "serviceBusRuleId": null,
        "workspaceId": "/subscriptions/{SUBSCRIPTION_ID}/resourcegroups/{RESOURCE_GROUP}/providers/microsoft.operationalinsights/workspaces/{WORKSPACE_NAME}",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "logs": [
            {
                "category": "DataPlaneRequests",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "QueryRuntimeStatistics",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "PartitionKeyStatistics",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "PartitionKeyRUConsumption",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ControlPlaneRequests",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ],
        "logAnalyticsDestinationType": "Dedicated"
    },
    "identity": null
}
```

## <a name="create-diagnostic-setting-via-azure-cli"></a>Azure CLI を介した診断設定の作成
Azure CLI を使用して診断設定を作成するには、[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) コマンドを使用します。 パラメーターの説明については、このコマンドのドキュメントを参照してください。

> [!Note]
> SQL API を使用している場合は、**export-to-resource-specific** プロパティを **true** に設定することをお勧めします。

   ```azurecli-interactive
   az monitor diagnostic-settings create --resource /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDb/databaseAccounts/ --name {DIAGNOSTIC_SETTING_NAME} --export-to-resource-specific true --logs '[{"category": "QueryRuntimeStatistics","categoryGroup": null,"enabled": true,"retentionPolicy": {"enabled": false,"days": 0}}]' --workspace /subscriptions/{SUBSCRIPTION_ID}/resourcegroups/{RESOURCE_GROUP}/providers/microsoft.operationalinsights/workspaces/{WORKSPACE_NAME}"
   ```
## <a name="enable-full-text-query-for-logging-query-text"></a><a id="full-text-query"></a> クエリ テキストをログに記録するためにフルテキスト クエリを有効にする

> [!Note]
> この機能を有効にすると、追加のログ コストが発生することがあります。料金の詳細については「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。 トラブルシューティングを行った後に、この機能を無効にすることをお勧めします。

Azure Cosmos DB では、詳細なトラブルシューティングのために機能拡張されたログ記録が提供されます。 フルテキスト クエリを有効にすると、Azure Cosmos DB アカウント内のすべての要求で、難読化解除されたクエリを表示できるようになります。  また、ログ内のこのデータにアクセスして表示するためのアクセス許可を Azure Cosmos DB に付与します。 

1. この機能を有効にするには、Cosmos DB アカウントの `Features` ブレードに移動します。
   
   :::image type="content" source="./media/monitor-cosmos-db/full-text-query-features.png" alt-text="[機能] ブレードに移動します":::

2. `Enable` を選択すると、この設定が数分以内に適用されます。 新しく取り込まれるすべてのログには、要求ごとにフルテキストまたは PIICommand テキストが含まれています。
   
    :::image type="content" source="./media/monitor-cosmos-db/select-enable-full-text.png" alt-text="フルテキストを [有効] にすることを選択します":::

新しく有効にした機能を使用してクエリを実行する方法については、[高度なクエリ](cosmos-db-advanced-queries.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
* リソース固有のテーブルに対してクエリを実行する方法の詳細については、[リソース固有のテーブルを使用したトラブルシューティング](cosmosdb-monitor-logs-basic-queries.md#resource-specific-queries)に関する記事を参照してください。

* AzureDiagnostics テーブルに対してクエリを実行する方法の詳細については、[AzureDiagnostics テーブルを使用したトラブルシューティング](cosmosdb-monitor-logs-basic-queries.md#azure-diagnostics-queries)に関する記事を参照してください。

* Azure portal、CLI、または PowerShell を使用して診断設定を作成する方法の詳細については、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。
