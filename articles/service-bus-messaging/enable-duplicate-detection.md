---
title: 重複メッセージの検出を有効にする - Azure Service Bus
description: この記事では、Azure portal、PowerShell、CLI、プログラミング言語 (C#、Java、Python、JavaScript) を使用して、重複メッセージの検出を有効にする方法について説明します
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 654d53bb324584d8b17d1e36efe20e5f25d54e39
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110671447"
---
# <a name="enable-duplicate-message-detection-for-an-azure-service-bus-queue-or-a-topic"></a>Azure Service Bus のキューまたはトピックで重複メッセージの検出を有効にする
キューまたはトピックで重複データ検出を有効にすると、Azure Service Bus によって、キューまたはトピックに送信されたすべてのメッセージの履歴が構成された期間に保持されます。 この期間中は、キューまたはトピックに重複メッセージは格納されません。 このプロパティを有効にすると、ユーザーが定義した期間に配信が 1 回だけ行われることが保証されます。 詳細については、「[重複検出](duplicate-detection.md)」を参照してください。 この記事では、Service Bus のキューまたはトピックで重複メッセージの検出を有効にするさまざまな方法について説明します。 


> [!NOTE]
> - Service Bus の Basic レベルでは、重複検出はサポートされません。 Standard と Premium レベルでは、重複検出はサポートされます。 これらのレベルの違いについては、「[Service Bus の価格](https://azure.microsoft.com/pricing/details/service-bus/)」を参照してください。
> - キューまたはトピックが作成された後に、重複データ検出を有効または無効にすることはできません。 それができるのはキューまたはトピックの作成時のみです。 

## <a name="using-azure-portal"></a>Azure Portal の使用
Azure portal で **キュー** を作成する場合は、次の図に示すように **[重複データ検出を有効にする]** を選択します。 キューまたはトピックを作成するときに、重複データ検出ウィンドウのサイズを構成できます。 

:::image type="content" source="./media/enable-duplicate-detection/create-queue.png" alt-text="キューの作成時に重複データ検出を有効にする":::

Azure portal でトピックを作成する場合は、次の図に示すように **[重複データ検出を有効にする]** を選択します。 

:::image type="content" source="./media/enable-duplicate-detection/create-topic.png" alt-text="トピックの作成時に重複データ検出を有効にする":::

作成時に重複データ検出を有効にした場合は、既存のキューまたはトピックに対してこの設定を構成することもできます。 

### <a name="update-duplicate-detection-window-size-for-an-existing-queue-or-a-topic"></a>既存のキューまたはトピックの重複データ検出ウィンドウのサイズを更新する
既存のキューまたはトピックの重複データ検出ウィンドウのサイズを変更するには、 **[概要]** ページで **[重複データ検出ウィンドウ]** の **[変更]** を選択します。  

#### <a name="queue"></a>キュー
:::image type="content" source="./media/enable-duplicate-detection/window-size.png" alt-text="キューの重複データ検出ウィンドウのサイズの設定":::

#### <a name="topic"></a>トピック
:::image type="content" source="./media/enable-duplicate-detection/window-size-topic.png" alt-text="トピックの重複データ検出ウィンドウのサイズの設定":::


## <a name="using-azure-cli"></a>Azure CLI の使用
**重複データ検出を有効にしたキューを作成する** には、`--enable-duplicate-detection` を `true` に設定して [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) コマンドを使用します。 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

**重複データ検出を有効にしたトピックを作成する** には、`--enable-duplicate-detection` を `true` に設定して [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) コマンドを使用します。

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

上記の例では、`--duplicate-detection-history-time-window` パラメーターを使用して重複データ検出ウィンドウのサイズも設定しています。 ウィンドウのサイズは 1 日に設定されています。 既定値は 10 分で、許容される最大値は 7 日です。

**新しい検出ウィンドウのサイズでキューを更新する** には、`--duplicate-detection-history-time-window` パラメーターを指定して [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) コマンドを使用します。 この例では、ウィンドウのサイズは 7 日間に更新されています。 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```

同様に、**新しい検出ウィンドウのサイズでトピックを更新する** には、`--duplicate-detection-history-time-window` パラメーターを指定して [`az servicebus topic update`](/cli/azure/servicebus/topic#az_servicebus_topic_update) コマンドを使用します。 この例では、ウィンドウのサイズは 7 日間に更新されています。

```azurecli-interactive
az servicebus topic update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```
 
## <a name="using-azure-powershell"></a>Azure PowerShell の使用
**重複データ検出を有効にしたキューを作成する** には、`-RequiresDuplicateDetection` を `$True` に設定して [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) コマンドを使用します。 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresDuplicateDetection $True `
    -DuplicateDetectionHistoryTimeWindow P1D
```

**重複データ検出を有効にしたトピックを作成する** には、`-RequiresDuplicateDetection` を `true` に設定して [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) コマンドを使用します。 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -RequiresDuplicateDetection $True
    -DuplicateDetectionHistoryTimeWindow P1D
```

上記の例では、`-DuplicateDetectionHistoryTimeWindow` パラメーターを使用して重複データ検出ウィンドウのサイズも設定しています。 ウィンドウのサイズは 1 日に設定されています。 既定値は 10 分で、許容される最大値は 7 日です。

**新しい検出ウィンドウのサイズでキューを更新する** には、次の例を参照してください。  この例では、ウィンドウのサイズは 7 日間に更新されています。

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
```

**新しい検出ウィンドウのサイズでトピックを更新する** には、次の例を参照してください。 この例では、ウィンドウのサイズは 7 日間に更新されています。

```azurepowershell-interactive
$topic=Get-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic

$topic.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -TopicObj $topic
```

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用
**重複データ検出を有効にしたキューを作成する** には、キューの properties セクションで `requiresDuplicateDetection` を `true` に設定します。 詳細については、[Microsoft.ServiceBus の名前空間/キューのテンプレート リファレンス](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)に関するページを参照してください。 重複データ検出ウィンドウのサイズを設定するには、`duplicateDetectionHistoryTimeWindow` プロパティに値を指定します。 次の例では、1 日に設定されます。  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.ServiceBus/namespaces",
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('serviceBusNamespaceName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "type": "Queues",
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
          }
        }
      ]
    }
  ]
}

```

**重複データ検出を有効にしたトピックを作成する** には、トピックの properties セクションで `requiresDuplicateDetection` を `true` に設定します。 詳細については、[Microsoft.ServiceBus の名前空間/トピックのテンプレート リファレンス](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json)に関するページを参照してください。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "service_BusNamespace_Name": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusTopicName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Topic"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('service_BusNamespace_Name')]",
      "type": "Microsoft.ServiceBus/namespaces",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusTopicName')]",
          "type": "topics",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces/', parameters('service_BusNamespace_Name'))]"
          ],
          "properties": {
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
          }
        }
      ]
    }
  ]
}
```


## <a name="next-steps"></a>次のステップ
Azure Service Bus の機能については、使用する言語のサンプルを試してみてください。 

- [.NET 用の Azure Service Bus クライアント ライブラリのサンプル (最新)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) 
- [Java 用の Azure Service Bus クライアント ライブラリのサンプル (最新)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Python 用の Azure Service Bus クライアント ライブラリのサンプル](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [JavaScript 用の Azure Service Bus クライアント ライブラリのサンプル](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [TypeScript 用の Azure Service Bus クライアント ライブラリのサンプル](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

以前の .NET および Java クライアント ライブラリのサンプルについては、以下を参照してください。
- [.NET 用の Azure Service Bus クライアント ライブラリのサンプル (レガシ)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Java 用の Azure Service Bus クライアント ライブラリのサンプル (レガシ)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus)
