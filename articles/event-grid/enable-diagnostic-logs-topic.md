---
title: Azure Event Grid - Event Grid リソースについての診断ログを有効にする
description: この記事では、Event Grid リソースについての診断ログを有効にする方法に関する詳しい手順を示します。
ms.topic: how-to
ms.date: 11/11/2021
ms.openlocfilehash: 06999716c884dc1c18ce41be288ebfba2b2298e6
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556287"
---
# <a name="enable-diagnostic-logs-for-event-grid-resources"></a>Event Grid リソースについての診断ログを有効にする

この記事では、Event Grid リソースについての診断設定を有効にする詳しい手順を示します。

これらの設定を使用すると、情報をキャプチャして表示できます。次の表に、Event Grid のリソース全体にわたる設定の互換性を示します。

| 診断設定 | 説明 | Event Grid トピック | Event Grid システム トピック | Event Grid ドメイン |
| ------------- | ------------- | --------- | ----------- | ----------- |
| DeliveryFailures | 配信エラー | はい | はい | はい |
| PublishFailures | 発行エラー | はい | いいえ | はい |
| DataPlaneRequests| データ プレーン要求 | はい | いいえ | はい |

> [!IMPORTANT]
> 診断ログのスキーマについては、[診断ログ](diagnostic-logs.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

- プロビジョニングされた Event Grid リソース
- 診断ログのキャプチャに使用するプロビジョニングされた宛先。
  - Log Analytics ワークスペース
  - ストレージ アカウント
  - イベント ハブ
  - パートナー ソリューション

## <a name="enable-diagnostic-logs-for-event-grid-topics-and-event-grid-domains"></a>Event Grid トピックと Event Grid ドメインの診断ログを有効にする

> [!NOTE]
> 下の手順では、Event Grid トピックの診断ログを有効にする詳しい手順を示しています。 ドメインの診断ログを有効にする手順もよく似ています。 手順 2 では、Azure portal 内の Event Grid **ドメイン** に移動します。  

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 診断ログ設定を有効にする Event Grid トピックに移動します。
    1. 上部の検索バーで、"**Event Grid トピック**" を検索します。
        ![カスタム トピックを検索する](./media/enable-diagnostic-logs-topic/search-custom-topics.png)
    2. 診断設定を構成するリストから、 **[トピック]** を選択します。
3. 左側のメニューの **[監視]** の下にある **[診断設定]** を選択します。
4. **[診断設定]** ページで、 **[Add New Diagnostic Setting]\(新しい診断設定の追加\)** を選択します。
    ![[診断設定を追加する] ボタン](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. 診断設定の **[名前]** を指定します。
6. **[ログ]** セクションで **[allLogs]** オプションを選択します。
    ![エラーを選択する](./media/enable-diagnostic-logs-topic/log-failures.png)
7. ログの 1 つまたは複数のキャプチャ先を有効にしてから、前に作成したキャプチャ リソースを選択して構成します。
    - **[ストレージ アカウントへのアーカイブ]** を選択した場合は、 **[ストレージ アカウント - 構成]** を選択してから、Azure サブスクリプションのストレージ アカウントを選択します。
        ![[Azure ストレージ アカウントにアーカイブする] チェックボックスがオンになっており、ストレージ アカウントが選択されている [診断設定] ページ示すスクリーンショット。](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - **[イベント ハブへのストリーム]** を選択した場合は、 **[イベント ハブ - 構成]** を選択してから、Event Hubs 名前空間、イベント ハブ、アクセス ポリシーを選択します。
        ![[イベント ハブへのストリーム] チェックボックスがオンになっている [診断設定] ページを示すスクリーンショット。](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - **[Log Analytics への送信]** を選択した場合は、Log Analytics ワークスペースを選択します。
        ![[Log Analytics への送信] チェックボックスがオンになっている [診断設定] ページを示すスクリーンショット。](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. **[保存]** を選択します。 次に、右上隅にある **[X]** を選択して、ページを閉じます。
9. 次に **[診断設定]** ページに戻り、 **[診断設定]** テーブルに新しいエントリが表示されていることを確認します。
    ![[診断設定] テーブルの新しいエントリが強調表示されている [診断設定] ページを示すスクリーンショット。](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

また、トピックのすべてのメトリックの収集を有効にすることもできます。

## <a name="enable-diagnostic-logs-for-event-grid-system-topics"></a>Event Grid システム トピックについての診断ログを有効にする

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 診断ログ設定を有効にするイベント グリッド トピックに移動します。
    1. 上部の検索バーで、"**Event Grid システム トピック**" を検索します。
        ![システム トピックの検索](./media/enable-diagnostic-logs-topic/search-system-topics.png)
    1. 診断設定を構成する **[システム トピック]** を選択します。
        ![システム トピックを選択する](./media/enable-diagnostic-logs-topic/select-system-topic.png)
3. 左側のペインで **[監視]** の下の **[診断設定]** を選択してから、 **[Add diagnostic setting]\(診断設定の追加\)** を選択します。
    ![診断設定の追加 - ボタン](./media/enable-diagnostic-logs-topic/system-topic-add-diagnostic-settings-button.png)
4. 診断設定の **[名前]** を指定します。
5. **[ログ]** セクションで **[allLogs]** オプションを選択します。
    ![配信エラーを選択する](./media/enable-diagnostic-logs-topic/system-topic-select-delivery-failures.png)
6. ログの 1 つまたは複数のキャプチャ先を有効にしてから、前に作成したキャプチャ リソースを選択して構成します。
    - **[Log Analytics への送信]** を選択した場合は、Log Analytics ワークスペースを選択します。
        ![Log Analytics への送信](./media/enable-diagnostic-logs-topic/system-topic-select-log-workspace.png)
    - **[ストレージ アカウントへのアーカイブ]** を選択した場合は、 **[ストレージ アカウント - 構成]** を選択してから、Azure サブスクリプションのストレージ アカウントを選択します。
        ![Azure ストレージ アカウントにアーカイブする](./media/enable-diagnostic-logs-topic/system-topic-select-storage-account.png)
    - **[イベント ハブへのストリーム]** を選択した場合は、 **[イベント ハブ - 構成]** を選択してから、Event Hubs 名前空間、イベント ハブ、アクセス ポリシーを選択します。
        ![イベント ハブへのストリーム](./media/enable-diagnostic-logs-topic/system-topic-select-event-hub.png)
7. **[保存]** を選択します。 次に、右上隅にある **[X]** を選択して、ページを閉じます。
8. 次に **[診断設定]** ページに戻り、 **[診断設定]** テーブルに新しいエントリが表示されていることを確認します。
    ![一覧の診断設定](./media/enable-diagnostic-logs-topic/system-topic-diagnostic-settings-targets.png)

また、システム トピックのすべての **メトリック** の収集を有効にすることもできます。
    ![システム トピック - すべてのメトリックを有効にする](./media/enable-diagnostic-logs-topic/system-topics-metrics.png)

## <a name="view-diagnostic-logs-in-azure-storage"></a>Azure Storage アカウントの診断ログの表示

1. キャプチャ先としてストレージ アカウントを有効にすると、Event Grid が診断ログの出力を開始します。 ストレージ アカウントで、**insights-logs-deliveryfailures** および **insights-logs-publishfailures** という名前の新しいコンテナーが表示されます。

    ![ストレージ - 診断ログ用のコンテナー](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. いずれかのコンテナー内を移動すると、JSON 形式の BLOB が見つかります。 このファイルには、配信エラーまたは発行エラーのログ エントリが含まれています。 ナビゲーション パスは、Event Grid トピックの **ResourceId** と、ログ エントリが生成されたときのタイムスタンプ (分単位) を表します。 ダウンロード可能な BloB/JSON ファイルは、最終的に次のセクションで説明するスキーマに準拠しています。

    ![ストレージ内の JSON ファイル](./media/enable-diagnostic-logs-topic/select-json.png)(./media/enable-diagnostic-logs-topic/select-json.png)
3. 次の例のようなコンテンツが JSON ファイルで確認できます。

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```

## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用

イベント グリッド トピックの診断設定を有効にする Azure Resource Manager テンプレートの例を次に示します。 このサンプル テンプレートをデプロイすると、次のリソースが作成されます。

- イベント グリッド トピック
- Log Analytics ワークスペース

次に、診断設定がトピック上に作成され、診断情報が Log Analytics ワークスペースに送信されます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "topic_name": {
            "defaultValue": "spegrid0917topic",
            "type": "String"
        },
        "log_analytics_workspace_name": {
            "defaultValue": "splogaw0625",
            "type": "String"
        },
        "location": {
            "defaultValue": "eastus",
            "type": "String"
        },
        "sku": {
            "defaultValue": "Free",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.EventGrid/topics",
            "apiVersion": "2020-10-15-preview",
            "name": "[parameters('topic_name')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Basic"
            },
            "kind": "Azure",
            "identity": {
                "type": "None"
            },
            "properties": {
                "inputSchema": "EventGridSchema",
                "publicNetworkAccess": "Enabled"
            }
        },
        {
            "apiVersion": "2017-03-15-preview",
            "name": "[parameters('log_analytics_workspace_name')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.OperationalInsights/workspaces",
            "properties": {
                "sku": {
                    "name": "[parameters('sku')]"
                }
            }
        },
        {
            "type": "Microsoft.EventGrid/topics/providers/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[concat(parameters('topic_name'), '/', 'Microsoft.Insights/', parameters('log_analytics_workspace_name'))]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.EventGrid/topics', parameters('topic_name'))]",
                "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('log_analytics_workspace_name'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('log_analytics_workspace_name'))]",
                "metrics": [
                    {
                        "category": "AllMetrics",
                        "enabled": true
                    }
                ],
                "logs": [
                    {
                        "category": "DeliveryFailures",
                        "enabled": true
                    },
                    {
                        "category": "PublishFailures",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```

## <a name="enable-diagnostic-logs-for-audit-traces"></a>監査トレースについての診断ログを有効にする

Event Grid では、カテゴリ グループ **[監査]** を選択するか、 **[ログ]** のカテゴリ **[DataPlaneRequests]** を選択すると、データ プレーン操作のための監査トレースを公開できます。監査トレースを使用すると確実に、承認された目的でのみデータ アクセスを許可し、リソース名、操作の種類、ネットワーク アクセス、レベル、リージョンなどのセキュリティ制御に関する情報を収集することができます。 診断設定を有効にする方法の詳細については、[Event Grid トピックと Event Grid ドメインにおける診断ログ](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-event-grid-topics-and-event-grid-domains)に関するページを参照してください。
![監査トレースを選択する](./media/enable-diagnostic-logs-topic/enable-audit-logs.png)

> [!IMPORTANT]
> `DataPlaneRequests` スキーマの詳細については、[診断ログ](diagnostic-logs.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

ログ スキーマと、トピックまたはドメインの診断ログに関するその他の概念情報については、「[診断ログ](diagnostic-logs.md)」を参照してください。
