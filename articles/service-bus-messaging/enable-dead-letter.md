---
title: Azure Service Bus のキューとサブスクリプションで配信不能を有効にする
description: この記事では、Azure portal、PowerShell、CLI、プログラミング言語 (C#、Java、Python、JavaScript) を使用して、キューとサブスクリプションで配信不能を有効にする方法について説明します
ms.topic: how-to
ms.date: 04/20/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 13082574510a0668076b7191b6b973e5a1a7cd77
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110671480"
---
# <a name="enable-dead-lettering-on-message-expiration-for-azure-service-bus-queues-and-subscriptions"></a>Azure Service Bus のキューとサブスクリプションでメッセージの期限切れによる配信不能を有効にする
Azure Service Bus のキューおよびトピックのサブスクリプションでは、配信不能キュー (DLQ) と呼ばれるセカンダリ サブキューが提供されます。 配信不能キューを明示的に作成する必要はなく、削除したり、メイン エンティティとは別に管理したりすることはできません。 配信不能キューの目的は、受信者に配信できないメッセージ、または処理できなかったメッセージを保持することです。 詳しくは、「[Service Bus の配信不能キューの概要](service-bus-dead-letter-queues.md)」を参照してください。 この記事では、Service Bus のキューとサブスクリプションで配信不能を有効にするさまざまな方法について説明します。 

## <a name="using-azure-portal"></a>Azure Portal の使用
Azure portal で **キュー** またはトピックの **サブスクリプション** を作成するとき、次の例に示すように **[メッセージの期限切れによる配信不能を有効にする]** を選択します。 

### <a name="create-a-queue-with-dead-lettering-enabled"></a>配信不能が有効化されたキューを作成する
:::image type="content" source="./media/enable-dead-letter/create-queue.png" alt-text="キューの作成時に配信不能を有効にする":::

### <a name="create-a-subscription-with-dead-lettering-enabled"></a>配信不能が有効化されたサブスクリプションを作成する
:::image type="content" source="./media/enable-dead-letter/create-subscription.png" alt-text="サブスクリプションの作成時に配信不能を有効にする":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-queue"></a>既存のキューでメッセージの期限切れによる配信不能の設定を更新する
Service Bus キューの **[概要]** ページで、メッセージの期限切れによる **[配信不能]** の設定の現在の値を選択します。 次の例では、現在の値は **[無効]** です。 ポップアップ ウィンドウで、メッセージの期限切れによる配信不能を有効または無効にすることができます。 

:::image type="content" source="./media/enable-dead-letter/queue-configuration.png" alt-text="既存のキューでメッセージの期限切れによる配信不能を有効にする":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-subscription"></a>既存のサブスクリプションでメッセージの期限切れによる配信不能の設定を更新する
Service Bus サブスクリプションの **[概要]** ページで、メッセージの期限切れによる **[配信不能]** の設定の現在の値を選択します。 次の例では、現在の値は **[無効]** です。 ポップアップ ウィンドウで、メッセージの期限切れによる配信不能を有効または無効にすることができます。 

:::image type="content" source="./media/enable-dead-letter/subscription-configuration.png" alt-text="既存のサブスクリプションでメッセージの期限切れによる配信不能を有効にする":::

## <a name="using-azure-cli"></a>Azure CLI の使用
**メッセージの期限切れによる配信不能が有効化されたキューを作成する** には、`--enable-dead-lettering-on-message-expiration` を `true` に設定した [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) コマンドを使用します。 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```

**既存のキューでメッセージの期限切れによる配信不能の設定を有効にする** には、`--enable-dead-lettering-on-message-expiration` を `true` に設定した [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) コマンドを使用します。 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```


**メッセージの期限切れによる配信不能が有効化されたトピックのサブスクリプションを作成する** には、`--enable-dead-lettering-on-message-expiration` を `true` に設定した [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) コマンドを使用します。

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

**トピックのサブスクリプションでメッセージの期限切れによる配信不能の設定を有効にする** には、`--enable-dead-lettering-on-message-expiration` を `true` に設定した [`az servicebus topic subscription update`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_update) コマンドを使用します。

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

## <a name="using-azure-powershell"></a>Azure PowerShell の使用
**メッセージの期限切れによる配信不能が有効化されたキューを作成する** には、`-DeadLetteringOnMessageExpiration` を `$True` に設定した [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) コマンドを使用します。 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -DeadLetteringOnMessageExpiration $True
```

**既存のキューでメッセージの期限切れによる配信不能の設定を有効にする** には、次の例に示されているような [`Set-AzServiceBusQueue`](/powershell/module/az.servicebus/set-azservicebusqueue) コマンドを使用します。

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
``` 

**メッセージの期限切れによる配信不能が有効化されたトピックのサブスクリプションを作成する** には、`-DeadLetteringOnMessageExpiration` を `$True` に設定した [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) コマンドを使用します。 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -DeadLetteringOnMessageExpiration $True
```

**既存のサブスクリプションでメッセージの期限切れによる配信不能の設定を有効にする** には、次の例を参照してください。

```azurepowershell-interactive
$subscription=Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysub

$subscription.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -SubscriptionName mysub `
    -SubscriptionObj $subscription 
```

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用
**メッセージの期限切れによる配信不能が有効化されたキューを作成する** には、キューのプロパティ セクションで `deadLetteringOnMessageExpiration` を `true` に設定します。 詳細については、[Microsoft.ServiceBus の名前空間/キューのテンプレート リファレンス](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)に関するページを参照してください。 

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
            "deadLetteringOnMessageExpiration": true
          }
        }
      ]
    }
  ]
}

```

**メッセージの期限切れによる配信不能が有効化されたトピックのサブスクリプションを作成する** には、キューのプロパティ セクションで `deadLetteringOnMessageExpiration` を `true` に設定します。 詳細については、[Microsoft.ServiceBus の名前空間/トピック/サブスクリプションのテンプレート リファレンス](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json)に関するページを参照してください。 

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
    "serviceBusSubscriptionName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Subscription"
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
            "maxSizeInMegabytes": 1024
          },
          "resources": [
            {
              "apiVersion": "2017-04-01",
              "name": "[parameters('serviceBusSubscriptionName')]",
              "type": "Subscriptions",
              "dependsOn": [
                "[parameters('serviceBusTopicName')]"
              ],
              "properties": {
                "deadLetteringOnMessageExpiration": true
              }
            }
          ]
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
