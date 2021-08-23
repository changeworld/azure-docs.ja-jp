---
title: Azure の診断設定を使用して Azure Cosmos DB データを監視する
description: Azure の診断設定を使用して、Azure Cosmos DB に格納されているデータのパフォーマンスと可用性を監視する方法について説明します
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/20/2021
ms.author: sngun
ms.openlocfilehash: 8c9303097a7b3b545d8fa106dd23f8d5662fc69d
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111964219"
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
> SQL API アカウントの場合は、[REST API を介して診断設定を作成するための手順に従って](cosmosdb-monitor-resource-logs.md#create-diagnostic-setting)診断設定をリソース固有のモードで作成することをお勧めします。 このオプションを使用するとさらなるコスト最適化が得られ、データ処理のビューも向上します。

## <a name="create-using-the-azure-portal"></a>Azure Portal を使用した作成

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. Azure Cosmos アカウントに移動します。 **[診断設定]** ウィンドウを開き、 **[診断設定の追加]** オプションを選択します。

2. **[診断設定]** ウィンドウで、フォームに希望のカテゴリを入力します。

### <a name="choosing-log-categories"></a>ログ カテゴリを選択する

|カテゴリ  |API   | 定義  | キー プロパティ   |
|---------|---------|---------|---------|
|DataPlaneRequests     |  すべての API        |     データ プレーン操作としてバック エンド要求をログに記録します。これは、アカウント内のデータを作成、更新、削除、または取得するために実行される要求です。   |   `Requestcharge`, `statusCode`, `clientIPaddress`, `partitionID`, `resourceTokenPermissionId` `resourceTokenPermissionMode`      |
|MongoRequests     |    Mongo    |   Azure Cosmos DB の MongoDB 用 API への要求を処理するために、ユーザーがフロントエンドから開始した要求をログに記録します。 このカテゴリを有効にするときは、必ず DataPlaneRequests を無効にしてください。      |  `Requestcharge`, `opCode`, `retryCount`, `piiCommandText`      |
|CassandraRequests     |   Cassandra      |    Azure Cosmos DB の Cassandra 用 API への要求を処理するために、ユーザーがフロントエンドから開始した要求をログに記録します。 このカテゴリを有効にするときは、必ず DataPlaneRequests を無効にしてください。     |     `operationName`, `requestCharge`, `piiCommandText`    |
|GremlinRequests     |    Gremlin    |     Azure Cosmos DB の Gremlin 用 API への要求を処理するために、ユーザーがフロントエンドから開始した要求をログに記録します。 このカテゴリを有効にするときは、必ず DataPlaneRequests を無効にしてください。    |   `operationName`, `requestCharge`, `piiCommandText`, `retriedDueToRateLimiting`       |
|QueryRuntimeStatistics     |   SQL      |     このテーブルは、SQL API アカウントに対して実行されるクエリ操作の詳細を示します。 既定では、クエリ テキストとそのパラメーターは難読化されるため、要求によって利用可能なフル テキスト クエリ ログ記録で PII データがログ記録されるのを防ぎます。    |    `databasename`, `partitionkeyrangeid`, `querytext`    |
|PartitionKeyStatistics     |    すべての API     |   論理パーティション キーの統計情報を、パーティション キーのストレージ サイズ (KB) を示すことにより、ログ記録します。 この表は、ストレージ スキューをトラブルシューティングするときに便利です。      |   `subscriptionId`, `regionName`, `partitionKey`, `sizeKB`      |
|PartitionKeyRUConsumption     |   SQL API    |     パーティション キーの RU (秒単位) の合計消費量をログに記録します。 この表は、ホット パーティションのトラブルシューティングで役立ちます。 現在のところ Azure Cosmos DB では、SQL API アカウントのみについて、およびポイントの読み取りまたは書き込みとストアド プロシージャ操作について、パーティション キーが報告されます。   |     `subscriptionId`, `regionName`, `partitionKey`, `requestCharge`, `partitionKeyRangeId`   |
|ControlPlaneRequests     |   すべての API       |    アカウントの作成、リージョンの追加または削除、アカウントのレプリケーション設定の更新など、コントロール プレーンの操作に関する詳細をログに記録します。     |    `operationName`, `httpstatusCode`, `httpMethod`, `region`       |
|TableApiRequests     |   テーブル API    |     Azure Cosmos DB の Table 用 API への要求を処理するために、ユーザーがフロントエンドから開始した要求をログに記録します。 このカテゴリを有効にするときは、必ず DataPlaneRequests を無効にしてください。       |    `operationName`, `requestCharge`, `piiCommandText`     |


## <a name="create-diagnostic-setting-via-rest-api"></a><a id="create-diagnostic-setting"></a> REST API を介した診断設定の作成
[Azure Monitor REST API](/rest/api/monitor/diagnosticsettings/createorupdate) を、対話型コンソールを介して診断設定を作成するために使用します。
> [!Note]
> SQL API を使用している場合は、**logAnalyticsDestinationType** プロパティを **Dedicated** に設定してリソース固有テーブルを有効にすることをお勧めします。

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

## <a name="next-steps"></a>次のステップ
* リソース固有のテーブルに対してクエリを実行する方法の詳細については、[リソース固有のテーブルを使用したトラブルシューティング](cosmosdb-monitor-logs-basic-queries.md#resource-specific-queries)に関する記事を参照してください。

* AzureDiagnostics テーブルに対してクエリを実行する方法の詳細については、[AzureDiagnostics テーブルを使用したトラブルシューティング](cosmosdb-monitor-logs-basic-queries.md#azure-diagnostics-queries)に関する記事を参照してください。

* Azure portal、CLI、または PowerShell を使用して診断設定を作成する方法の詳細については、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。