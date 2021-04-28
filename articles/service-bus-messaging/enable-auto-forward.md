---
title: Azure Service Bus のキューとサブスクリプションの自動転送を有効にする
description: この記事では、Azure portal、PowerShell、CLI、プログラミング言語 (C#、Java、Python、JavaScript) を使用して、キューとサブスクリプションの自動転送を有効にする方法について説明します
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: ef22ae08485dc896c94858db4e422cf89a00ec1f
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755131"
---
# <a name="enable-auto-forwarding-for-azure-service-bus-queues-and-subscriptions"></a>Azure Service Bus のキューとサブスクリプションの自動転送を有効にする
Service Bus の自動転送機能を使用すると、キューまたはサブスクリプションを同じ名前空間に属する別のキューまたはトピックにチェーンできます。 自動転送が有効な場合は、Service Bus は、一方のキューまたはサブスクリプション (転送元) にあるメッセージを自動的に削除し、もう一方のキューまたはトピック (転送先) に追加します。 転送先エンティティには､メッセージを直接送信することもできます｡ 詳細については、「[自動転送を使用した Service Bus エンティティのチェーン](service-bus-auto-forwarding.md)」を参照してください。 この記事では、Service Bus のキューとサブスクリプションで自動転送を有効にするさまざまな方法について説明します。 

> [!IMPORTANT]
> Service Bus の Basic レベルでは、自動転送機能がサポートされていません。 Standard レベルと Premium レベルでは、この機能がサポートされています。 これらのレベルの違いについては、「[Service Bus の価格](https://azure.microsoft.com/pricing/details/service-bus/)」を参照してください。

## <a name="using-azure-portal"></a>Azure Portal の使用
Azure portal で **キュー** またはトピックの **サブスクリプション** を作成する場合は、次の例に示すように **[メッセージをキュー/トピックに転送する]** を選択します。 次に、メッセージをキューまたはトピックのどちらに転送するかを指定します。 この例では、 **[キュー]** オプションが選択され、同じ名前空間のキュー (**myqueue**) が選択されています。

### <a name="create-a-queue-with-auto-forwarding-enabled"></a>自動転送を有効にしたキューを作成する
:::image type="content" source="./media/enable-auto-forward/create-queue.png" alt-text="キューの作成時に自動転送を有効にする":::

### <a name="create-a-subscription-for-a-topic-with-auto-forwarding-enabled"></a>自動転送を有効にしたトピックのサブスクリプションを作成する
:::image type="content" source="./media/enable-auto-forward/create-subscription.png" alt-text="サブスクリプションの作成時に自動転送を有効にする":::

## <a name="using-azure-cli"></a>Azure CLI の使用
**自動転送を有効にしたキューを作成する** には、メッセージの転送先のキューまたはトピックの名前を `--forward-to` に設定して [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) コマンドを使用します。 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --forward-to myqueue2
```

**自動転送を有効にしたトピックのサブスクリプションを作成する** には、メッセージの転送先のキューまたはトピックの名前を `--forward-to` に設定して [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) コマンドを使用します。

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --forward-to myqueue2
```

## <a name="using-azure-powershell"></a>Azure PowerShell の使用
**自動転送を有効にしたキューを作成する** には、メッセージの転送先のキューまたはトピックの名前を `-ForwardTo` に設定して [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) コマンドを使用します。 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -ForwardTo myqueue2
```

**自動転送を有効にしたトピックのサブスクリプションを作成する** には、メッセージの転送先のキューまたはトピックの名前を `-ForwardTo` に設定して [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) コマンドを使用します。

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -ForwardTo myqueue2
```

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用
**自動転送を有効にしたキューを作成する** には、メッセージの転送先のキューまたはトピックの名前をキューの properties セクションの `forwardTo` に設定します。 詳細については、[Microsoft.ServiceBus の名前空間/キューのテンプレート リファレンス](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)に関するページを参照してください。 

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
            "forwardTo": "myqueue2"
          }
        }
      ]
    }
  ]
}

```

**自動転送を有効にしたトピックのサブスクリプションを作成する** には、メッセージの転送先のキューまたはトピックの名前をキューの properties セクションの `forwardTo` に設定します。 詳細については、[Microsoft.ServiceBus の名前空間/トピック/サブスクリプションのテンプレート リファレンス](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json)に関するページを参照してください。 

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
                "forwardTo": "myqueue2"
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

- [Java 用の Azure Service Bus クライアント ライブラリのサンプル](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Python 用の Azure Service Bus クライアント ライブラリのサンプル](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [JavaScript 用の Azure Service Bus クライアント ライブラリのサンプル](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [TypeScript 用の Azure Service Bus クライアント ライブラリのサンプル](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [.NET 用の Azure.Messaging.ServiceBus サンプル](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

以前の .NET および Java クライアント ライブラリのサンプルについては、以下を参照してください。
- [.NET 用の Microsoft.Azure.ServiceBus のサンプル](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Java 用の azure-servicebus のサンプル](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)