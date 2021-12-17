---
title: 診断設定を使用して Azure Cache for Redis のデータを監視する
titleSuffix: Azure Cache for Redis
description: 診断設定を使用して、Azure Cache for Redis に接続されている IP アドレスを監視する方法を説明します。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: how-to
ms.date: 11/3/2021
ms.custom: template-how-to
ms.openlocfilehash: 8fd6ae4eb82c7b8cb1439f621f26eef7860bcc0d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132340226"
---
# <a name="monitor-azure-cache-for-redis-data-using-diagnostic-settings"></a>診断設定を使用して Azure Cache for Redis のデータを監視する

Azure の診断設定は、リソース ログの収集に使用されます。 Azure のリソース ログは、リソースによって出力され、そのリソースの操作に関する豊富なデータを提供します。 これらのログは、要求ごとに取得され、"データ プレーン ログ" とも呼ばれます。 これらのログの内容は、リソースの種類によって異なります。

Azure Cache for Redis により、Azure 診断設定を使用して、キャッシュへのすべてのクライアント接続に関する情報がログに記録されます。 この診断設定のログ記録と分析は、キャッシュに接続しているユーザーと、それらの接続のタイムスタンプを把握するのに役立ちます。 このログ データは、セキュリティ侵害の範囲を特定するため、およびセキュリティ監査の目的に使用できます。

構成が済むと、キャッシュでの IP アドレスによる受信クライアント接続のログ記録が開始されます。 また、一意の各 IP アドレスから発信された接続の数もログに記録されます。 ログは累積ではありません。 それらは、10 秒間隔で取得された特定の時点のスナップショットを表します。

Azure Cache for Redis インスタンスの診断設定を有効にして、次の宛先にリソース ログを送信できます。

- **Log Analytics ワークスペース** - 監視対象のリソースと同じリージョンにする必要はありません。
- **ストレージ アカウント** - キャッシュと同じリージョンに存在する必要があります。
- **イベント ハブ** - 仮想ネットワークが有効になっていると、診断設定でイベント ハブ リソースにアクセスすることはできません。 イベント ハブ リソースへのアクセスを許可するには、イベント ハブの設定 **[信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可しますか?]** を有効にします。 イベント ハブは、キャッシュと同じリージョンにある必要があります。

診断の要件の詳細については、[診断設定](../azure-monitor/essentials/diagnostic-settings.md?tabs=CMD)に関する記事を参照してください。

診断ログをいずれかの宛先に送信すると、ストレージ アカウントとイベント ハブの使用に対して通常のデータレートが課金されます。 この場合、請求は Azure Cache for Redis ではなく Azure Monitor で行われます。 **Log Analytics** にログを送信する場合、Log Analytics データ インジェストのみに料金が発生します。

価格の詳細については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。

## <a name="create-diagnostics-settings-via-the-azure-portal"></a> Azure portal を使用して診断設定を作成する

1. [Azure Portal ](https://portal.azure.com)にサインインします。

1. Azure Cache for Redis アカウントに移動します。 左側の **[監視]** セクションで、 **[診断設定]** ペインを開きます。 次に、 **[診断設定を追加する]** を選択します。

   :::image type="content" source="media/cache-monitor-diagnostic-settings/cache-monitor-diagnostic-setting.png" alt-text="診断の選択":::

1. **[診断設定]** ペインの **[カテゴリの詳細]** で、 **[ConnectedClientList]** を選択します。

   |カテゴリ  | 定義  | キー プロパティ   |
   |---------|---------|---------|
   |ConnectedClientList |  キャッシュに接続されているクライアントの IP アドレスと数。一定の間隔でログに記録されます。 | `connectedClients` および入れ子になった `ip`、`count`、`privateLinkIpv6` |

   その他のフィールドの詳細については、以下の「[リソース ログ](#resource-logs)」を参照してください。

1. **[カテゴリの詳細]** を選択したら、適切な宛先にログを送信します。 右側で情報を選択します。

    :::image type="content" source="media/cache-monitor-diagnostic-settings/diagnostics-resource-specific.png" alt-text="[リソース固有] を選択して有効にします":::

## <a name="create-diagnostic-setting-via-rest-api"></a> REST API を介した診断設定の作成

Azure Monitor REST API を、対話型コンソールを介して診断設定を作成するために使用します。 詳細については、「 [作成または更新](/rest/api/monitor/diagnostic-settings/create-or-update)」を参照してください。

### <a name="request"></a>Request

```http
PUT https://management.azure.com/{resourceUri}/providers/Microsoft.Insights/diagnosticSettings/{name}?api-version=2017-05-01-preview
```

### <a name="headers"></a>ヘッダー

   | パラメーター/ヘッダー | 値/説明 |
   |---------|---------|
   | `name` | 診断設定の名前。 |
   | `resourceUri` | subscriptions/<サブスクリプション ID>/resourceGroups/<リソース グループ>/providers/Microsoft.Cache/Redis/<キャッシュ名> |
   | `api-version` | 2017-05-01-preview |
   | `Content-Type` | application/json |

### <a name="body"></a>本文

```json
{
    "properties": {
      "storageAccountId": "/subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/apptest/providers/Microsoft.Storage/storageAccounts/appteststorage1",
      "eventHubAuthorizationRuleId": "/subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/microsoft.eventhub/namespaces/mynamespace/eventhubs/myeventhub/authorizationrules/myrule",
      "eventHubName": "myeventhub",
      "workspaceId": "/subscriptions/4b9e8510-67ab-4e9a-95a9-e2f1e570ea9c/resourceGroups/insights-integration/providers/Microsoft.OperationalInsights/workspaces/myworkspace",
      "logs": [
        {
          "category": "ConnectedClientList",
          "enabled": true,
          "retentionPolicy": {
            "enabled": false,
            "days": 0
          }
        }
      ]
    }
}
```

## <a name="create-diagnostic-setting-via-azure-cli"></a>Azure CLI を介した診断設定の作成

Azure CLI を使用して診断設定を作成するには、`az monitor diagnostic-settings create` コマンドを使用します。 コマンドとパラメーターの詳細については、「[プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest&branch=main&preserve-view=true#az_monitor_diagnostic_settings_create)」を参照してください。

```azurecli
az monitor diagnostic-settings create 
    --resource /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/Microsoft.Cache/Redis/myname
    --name constoso-setting
    --logs '[{"category": "ConnectedClientList","enabled": true,"retentionPolicy": {"enabled": false,"days": 0}}]'    
    --event-hub MyEventHubName 
    --event-hub-rule /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/microsoft.eventhub/namespaces/mynamespace/authorizationrules/RootManageSharedAccessKey 
    --storage-account /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/Microsoft.Storage/storageAccounts/myuserspace
    --workspace /subscriptions/4b9e8510-67ab-4e9a-95a9-e2f1e570ea9c/resourceGroups/insights-integration/providers/Microsoft.OperationalInsights/workspaces/myworkspace
```

## <a name="resource-logs"></a>リソース ログ

以下のフィールドとプロパティは `ConnectedClientList` ログ カテゴリに出現します。 **Azure Monitor** では、ログは `MICROSOFT.CACHE` という名前のリソース プロバイダーの下にある `ACRConnectedClientList` テーブルに収集されます。

| Azure Storage のフィールドまたはプロパティ | Azure Monitor ログのプロパティ | 説明 |
| --- | --- | --- |
| `time` | `TimeGenerated` | ログが生成されたときのタイムスタンプ (UTC)。 |
| `location` | `Location` | Azure Cache for Redis インスタンスへのアクセスが発生した場所 (リージョン)。 |
| `category` | 該当なし | 使用可能なログ カテゴリ: `ConnectedClientList`。 |
| `resourceId` | `_ResourceId` | ログが有効になっている Azure Cache for Redis リソース。|
| `operationName` | `OperationName` | ログ レコードに関連付けられた Redis 操作。 |
| `properties` | 該当なし | このフィールドの内容については、以下の行を参照してください。 |
| `tenant` | `CacheName` | Azure Cache for Redis インスタンスの名前。 |
| `roleInstance` | `RoleInstance` | クライアントの一覧をログに記録したロール インスタンス。 |
| `connectedClients.ip` | `ClientIp` | Redis クライアントの IP アドレス。 |
| `connectedClients.privateLinkIpv6` | `PrivateLinkIpv6` | Redis クライアントのプライベート リンク IPv6 アドレス (該当する場合)。 |
| `connectedClients.count` | `ClientCount` | 関連付けられた IP アドレスからの Redis クライアント接続の数。 |

### <a name="sample-storage-account-log"></a>ストレージ アカウント ログのサンプル

ログをストレージ アカウントに送信する場合、ログの内容は次のようになります。

```json
{
    "time": "2021-08-05T21:04:58.0466086Z",
    "location": "canadacentral",
    "category": "ConnectedClientList",
    "properties": {
        "tenant": "mycache", 
        "connectedClients": [
            {
                "ip": "192.123.43.36", 
                "count": 86
            },
            {
                "ip": "10.1.1.4",
                "privateLinkIpv6": "fd40:8913:31:6810:6c31:200:a01:104", 
                "count": 1
            }
        ],
        "roleInstance": "1"
    },
    "resourceId": "/SUBSCRIPTIONS/E6761CE7-A7BC-442E-BBAE-950A121933B5/RESOURCEGROUPS/AZURE-CACHE/PROVIDERS/MICROSOFT.CACHE/REDIS/MYCACHE", 
    "Level": 4,
    "operationName": "Microsoft.Cache/ClientList"
}
```

## <a name="log-analytics-queries"></a>Log Analytics クエリ

ここでは、モデルとして使用する基本的なクエリをいくつか示します。

- 指定された IP アドレス範囲内の 1 時間あたりの Azure Cache for Redis クライアント接続:

```kusto
let IpRange = "10.1.1.0/24";
ACRConnectedClientList
// For particular datetime filtering, add '| where TimeGenerated between (StartTime .. EndTime)'
| where ipv4_is_in_range(ClientIp, IpRange)
| summarize ConnectionCount = sum(ClientCount) by TimeRange = bin(TimeGenerated, 1h)
```

- キャッシュに接続している一意の Redis クライアント IP アドレス:

```kusto
ACRConnectedClientList
| summarize count() by ClientIp
```

## <a name="next-steps"></a>次の手順

Azure portal、CLI、または PowerShell を使用して診断設定を作成する方法の詳細については、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。
