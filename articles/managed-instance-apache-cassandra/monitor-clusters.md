---
title: Azure Monitor
description: Azure Monitor
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: dcb4dcd6037f8efcbac33e970566babf10c7d262
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858448"
---
# <a name="monitor-azure-managed-instance-for-apache-cassandra-using-azure-monitor"></a>Azure Monitor を使用した Azure Managed Instance for Apache Cassandra の監視

Azure Managed Instance for Apache Cassandra には、[Azure Monitor](../azure-monitor/overview.md) を使用したメトリックと診断ログが用意されています。 

## <a name="azure-metrics"></a>Azure メトリック

Azure Managed Instance for Apache Cassandra のメトリックは、クラスター リソースに移動し、[メトリック] タブを選択すると視覚化できます。その後、使用可能なメトリックと集計から選択できます。

:::image type="content" source="./media/azure-monitor/metrics.png" alt-text="メトリックの視覚化":::

## <a name="diagnostic-settings-in-azure"></a>Azure での診断設定

Azure の診断設定は、リソース ログの収集に使用されます。 Azure のリソース ログは、リソースによって出力され、そのリソースの操作に関する豊富なデータを提供します。 これらのログは、要求ごとにキャプチャされ、"データ プレーン ログ" とも呼ばれます。 データ プレーン操作の例としては、delete、insert、readFeed などがあります。 これらのログの内容は、リソースの種類によって異なります。

プラットフォーム メトリックとアクティビティ ログは自動的に収集されますが、リソース ログを収集したり、それらを Azure Monitor の外部に転送したりするには、診断設定を作成する必要があります。 Azure Managed Instance for Apache Cassandra クラスター リソースの診断設定を有効にして、リソース ログを次のソースに送信できます。
- Log Analytics ワークスペース
  - Log Analytics に送信されるデータを、**Azure Diagnostics (レガシ)** または **リソース固有(プレビュー)** のテーブルに書き込むことができます 
- イベント ハブ
- ストレージ アカウント
  
> [!NOTE]
> リソース固有モードで診断設定を作成することをお勧めします。

## <a name="create-diagnostic-settings-via-the-azure-portal"></a><a id="create-setting-portal"></a> Azure portal を使用して診断設定を作成する

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure Managed Instance for Apache Cassandra クラスター リソースに移動します。 

    :::image type="content" source="./media/azure-monitor/cluster.png" alt-text="クラスターの選択":::
 
1. **[監視]** セクションの **[診断設定]** ペインを開き、 **[診断設定の追加]** オプションを選択します。

    :::image type="content" source="./media/azure-monitor/settings.png" alt-text="診断設定の追加":::


1. **[診断設定]** ウィンドウで、設定の名前を選択し、 **[Categories details]\(カテゴリの詳細\)** を選択します。 **[CassandraAudit]** カテゴリには、監査と CQL の操作が記録されます。 **[CassandraLogs]** カテゴリには、Cassandra サーバーの操作が記録されます。 次に、希望する宛先にログを送信します。 **Log Analytics ワークスペース** にログを送信する場合は、[対象テーブル] で **[リソース固有]** を選択してください。 

    :::image type="content" source="./media/azure-monitor/preferred-categories.png" alt-text="カテゴリの選択":::

   > [!WARNING]
   > Log Analytics ワークスペースにログを送信する場合、ログが最初に表示されるのに最大 **20 分** かかる場合があります。 それまでは、リソース固有のテーブル (以下の Azure Managed Instance for Apache Cassandra の下に表示されています) は表示されません。  


1. 診断ログが設定され、データが流れ出すと、 **[ログ]** タブに移動して、Azure Data Explorer を使用して使用可能な診断ログのクエリを実行できます。 Azure Monitor と Kusto クエリ言語の詳細については、[こちらの記事](../azure-monitor/logs/log-query-overview.md)をご覧ください。 

    :::image type="content" source="./media/azure-monitor/query.png" alt-text="クエリ ログ":::

## <a name="create-diagnostic-setting-via-azure-cli"></a><a id="create-setting-cli"></a>Azure CLI を使用して診断設定を作成する
Azure CLI を使用して診断設定を作成するには、[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) コマンドを使用します。 パラメーターの説明については、このコマンドのドキュメントを参照してください。

```azurecli-interactive
    logs='[{"category":"CassandraAudit","enabled":true,"retentionPolicy":{"enabled":true,"days":3}},{"category":"CassandraLogs","enabled":true,"retentionPolicy":{"enabled":true,"days":3}}]'
    resourceId='/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDB/cassandraClusters/{CLUSTER_NAME}'
    workspace='/subscriptions/{SUBSCRIPTION_ID}/resourcegroups/{RESOURCE_GROUP}/providers/microsoft.operationalinsights/workspaces/{WORKSPACE_NAME}'

    az monitor diagnostic-settings create  --name tvk-doagnostic-logs-cassandra --resource $resourceId --logs  $logs --workspace $workspace --export-to-resource-specific true
```

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
                "category": "CassandraAudit",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "CassandraLogs",
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


## <a name="next-steps"></a>次のステップ

* Azure portal、CLI、または PowerShell を使用して診断設定を作成する方法の詳細については、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。