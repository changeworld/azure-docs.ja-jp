---
title: 診断設定を使用して Azure Cache for Redis のデータを監視する
titleSuffix: Azure Cache for Redis
description: 診断設定を使用して、Azure Cache for Redis に接続されている IP アドレスを監視する方法を説明します。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: how-to
ms.date: 09/30/2021
ms.custom: template-how-to
ms.openlocfilehash: 2662ba2f2e87f79b034ae8138bba31fc9358d8d6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130236173"
---
# <a name="monitor-azure-cache-for-redis-data-using-diagnostic-settings"></a>診断設定を使用して Azure Cache for Redis のデータを監視する

Azure の診断設定は、リソース ログの収集に使用されます。 Azure のリソース ログは、リソースによって出力され、そのリソースの操作に関する豊富なデータを提供します。 これらのログは、要求ごとに取得され、"データ プレーン ログ" とも呼ばれます。 これらのログの内容は、リソースの種類によって異なります。

Azure Cache for Redis により、Azure 診断設定を使用して、キャッシュへのすべてのクライアント接続に関する情報がログに記録されます。 この診断設定のログ記録と分析は、キャッシュに接続しているユーザーと、それらの接続のタイムスタンプを把握するのに役立ちます。 このデータは、セキュリティ侵害の範囲を特定するため、およびセキュリティ監査の目的に使用できます。

構成が済むと、キャッシュでの IP アドレスによる受信クライアント接続のログ記録が開始されます。 また、一意の各 IP アドレスから発信された接続の数もログに記録されます。 ログは累積ではありません。 それらは、10 秒間隔で取得された特定の時点のスナップショットを表します。

Azure Cache for Redis インスタンスの診断設定を有効にして、次の宛先にリソース ログを送信できます。

- **イベント ハブ** - 仮想ネットワークが有効になっていると、診断設定でイベント ハブ リソースにアクセスすることはできません。 イベント ハブ リソースへのアクセスを許可するには、イベント ハブの設定 **[信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可しますか?]** を有効にします。 イベント ハブは、キャッシュと同じリージョンにある必要があります。
- **ストレージ アカウント** - キャッシュと同じリージョンに存在する必要があります。

診断の要件の詳細については、[診断設定](../azure-monitor/essentials/diagnostic-settings.md?tabs=CMD)に関する記事を参照してください。

診断ログをいずれかの宛先に送信すると、ストレージ アカウントとイベント ハブの使用に対して通常のデータレートが課金されます。 この場合、請求は Azure Cache for Redis ではなく Azure Monitor で行われます。
価格の詳細については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。

## <a name="create-diagnostics-settings-via-the-azure-portal"></a> Azure portal を使用して診断設定を作成する

1. [Azure Portal ](https://portal.azure.com)にサインインします。

1. Azure Cache for Redis アカウントに移動します。 左側の **[監視]** セクションで、 **[診断設定]** ペインを開きます。 次に、 **[診断設定を追加する]** を選択します。

   :::image type="content" source="media/cache-monitor-diagnostic-settings/cache-monitor-diagnostic-setting.png" alt-text="診断の選択":::

1. **[診断設定]** ペインの **[カテゴリの詳細]** で、 **[ConnectedClientList]** を選択します。

   |カテゴリ  | 定義  | キー プロパティ   |
   |---------|---------|---------|
   |ConnectedClientList |  キャッシュに接続されているクライアントの IP アドレスと数。一定の間隔でログに記録されます。 | `connectedClients` および入れ子になった `ip`、`count`、`privateLinkIpv6` |
  
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
   | name | 診断設定の名前。 |
   | resourceUri | subscriptions/<サブスクリプション ID>/resourceGroups/<リソース グループ>/providers/Microsoft.Cache/Redis/<キャッシュ名> |
   | api-version | 2017-05-01-preview |
   | Content-Type | application/json |

### <a name="body"></a>本文

```json
{
    "properties": {
      "storageAccountId": "/subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/apptest/providers/Microsoft.Storage/storageAccounts/appteststorage1",
      "eventHubAuthorizationRuleId": "/subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/microsoft.eventhub/namespaces/mynamespace/eventhubs/myeventhub/authorizationrules/myrule",
      "eventHubName": "myeventhub",
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

Azure CLI を使用して診断設定を作成するには、`az monitor diagnostic-settings create` コマンドを使用します。 このコマンドとパラメーターの詳細については、「[プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。

```azurecli

az monitor diagnostic-settings create 
    --resource /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/Microsoft.Cache/Redis/myname
    --name constoso-setting
    --logs '[{"category": "ConnectedClientList","enabled": true,"retentionPolicy": {"enabled": false,"days": 0}}]'    
    --event-hub MyEventHubName 
    --event-hub-rule /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/microsoft.eventhub/namespaces/mynamespace/authorizationrules/RootManageSharedAccessKey 
    --storage-account /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/Microsoft.Storage/storageAccounts/myuserspace


```

## <a name="next-steps"></a>次の手順

Azure portal、CLI、または PowerShell を使用して診断設定を作成する方法の詳細については、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。