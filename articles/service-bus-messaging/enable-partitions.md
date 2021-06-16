---
title: Azure Service Bus のキューとトピックでパーティション分割を有効にする
description: この記事では、Azure portal、PowerShell、CLI、プログラミング言語 (C#、Java、Python、JavaScript) を使用して、Azure Service Bus のキューとトピックでパーティション分割を有効にする方法について説明します
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 786a78cfa86c67ba32675fcee2b03c4e3817fbf6
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110671394"
---
# <a name="enable-partitioning-for-an-azure-service-bus-queue-or-a-topic"></a>Azure Service Bus のキューまたはトピックでパーティション分割を有効にする
Service Bus "パーティション" では、キューとトピック、つまり "メッセージング エンティティ" を複数のメッセージ ブローカーとメッセージング ストアにパーティション分割することもできます。 パーティション分割は、パーティション分割されたエンティティの全体のスループットが、単一のメッセージ ブローカーまたはメッセージング ストアのパフォーマンスによって制限されなくなることを意味します。 また、1 つのメッセージング ストアが一時的に停止しても、パーティション分割されたキューまたはトピックは使用することができます。 パーティション分割されたキューとトピックには、トランザクションやセッションのサポートなど、あらゆる高度な Service Bus 機能を含めることができます。 詳細については、「[パーティション分割されたキューとトピック](service-bus-partitioning.md)」を参照してください。 この記事では、Service Bus のキューまたはトピックで重複メッセージの検出を有効にするさまざまな方法について説明します。 

> [!IMPORTANT]
> - パーティション分割は、Basic または Standard SKU のすべてのキューおよびトピックに対するエンティティの作成で使用できます。 Premium メッセージング SKU では使用できませんが、Premium 名前空間のパーティション分割された既存のエンティティは引き続き正常に動作します。
> - 既存のキューまたはトピックで、パーティション分割のオプションを変更することはできません。 オプションを設定できるのは、キューまたはトピックを作成するときのみです。 
> - **Standard** レベルの名前空間では、Service Bus のキューとトピックを 1、2、3、4 または 5 GB のサイズで作成できます (規定のサイズは 1 GB です)。 パーティション分割が有効な場合、Service Bus は指定されたサイズごとにエンティティの 16 個のコピー (16 個のパーティション) を作成します。 そのため、サイズが 5 GB のキューを作成すると、パーティションが 16 個であるため、最大キュー サイズは 5 \* 16 = 80 GB になります。 
> - **Premium** レベルの名前空間では、エンティティのパーティション分割をサポートしていません。 ただし、Service Bus のキューとトピックは、1 GB、2 GB、3 GB、4 GB、5 GB、10 GB、20 GB、40 GB、80 GB で引き続き作成できます (既定値は 1 GB)。 パーティション分割したキューおよびトピックの最大サイズは、[Azure portal](https://portal.azure.com) の **概要** ページで確認できます。


## <a name="using-azure-portal"></a>Azure Portal の使用
Azure portal で **キュー** を作成する場合は、次の図に示すように **[パーティション分割の有効化]** を選択します。 

:::image type="content" source="./media/enable-partitions/create-queue.png" alt-text="キューの作成時にパーティション分割を有効にする":::

Azure portal でトピックを作成する場合は、次の図に示すように **[パーティション分割の有効化]** を選択します。 

:::image type="content" source="./media/enable-partitions/create-topic.png" alt-text="トピックの作成時にパーティション分割を有効にする":::

## <a name="using-azure-cli"></a>Azure CLI の使用
**パーティション分割を有効にしたキューを作成する** には、`--enable-partitioning` を `true` に設定して [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) コマンドを使用します。

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-partitioning true
```

**パーティション分割を有効にしたトピックを作成する** には、`--enable-partitioning` を `true` に設定して [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) コマンドを使用します。

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-partitioning true
```

## <a name="using-azure-powershell"></a>Azure PowerShell の使用
**パーティション分割を有効にしたキューを作成する** には、`-EnablePartitioning` を `$True` に設定して [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) コマンドを使用します。 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -EnablePartitioning $True
```

**パーティション分割を有効にしたトピックを作成する** には、`-EnablePartitioning` を `true` に設定して [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) コマンドを使用します。 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -EnablePartitioning $True
```

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用
**パーティション分割を有効にしたキューを作成する** には、キューの properties セクションで `enablePartitioning` を `true` に設定します。 詳細については、[Microsoft.ServiceBus の名前空間/キューのテンプレート リファレンス](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)に関するページを参照してください。 

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
            "enablePartitioning": true
          }
        }
      ]
    }
  ]
}

```

**重複データ検出を有効にしたトピックを作成する** には、トピックの properties セクションで `enablePartitioning` を `true` に設定します。 詳細については、[Microsoft.ServiceBus の名前空間/トピックのテンプレート リファレンス](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json)に関するページを参照してください。 

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
            "enablePartitioning": true
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
