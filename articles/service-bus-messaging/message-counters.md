---
title: Azure Service Bus - メッセージ数
description: Azure Resource Manager および Azure Service Bus NamespaceManager API を使用して、キューおよびサブスクリプションに保持されているメッセージの数を取得します。
ms.topic: article
ms.date: 09/20/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 74038f38f1faca90e4fab7a0d4f94229a1b44429
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128576063"
---
# <a name="get-message-counters"></a>メッセージ数を取得する
この記事では、次に挙げるキューまたはサブスクリプションのメッセージ数を取得する複数の方法を説明します。 現在展開されているものより、処理により多くのリソースを必要とするバックログをキューが生成するかどうか判断するために、アクティブなメッセージの数を把握することが有効です。 

| カウンタ | 説明 |
| ----- | ---------- | 
| ActiveMessageCount | キューまたはサブスクリプションにあるメッセージのうち、アクティブな状態で配信準備ができているものの数。 |
| ScheduledMessageCount | スケジュールされた状態のメッセージの数。 |
| DeadLetterMessageCount | 配信不能キューにあるメッセージの数。 |
| TransferMessageCount | 別のキューまたはトピックへの転送を待つメッセージの数。 |
| TransferDeadLetterMessageCount | 別のキューまたはトピックへの転送に失敗して転送不能キューに入ったメッセージの数。 |

アプリケーションで、キューの長さに基づいてリソースを拡張する必要がある場合は、正確に測定された間隔で実行する必要があります。 メッセージ カウンターの取得は、メッセージ ブローカー内で多くのリソースを必要とする操作であり、頻繁に実行すると、エンティティ パフォーマンスに直接的な悪影響を及ぼします。

> [!NOTE]
> Service Bus トピックに送信されたメッセージは、そのトピックのサブスクリプションに転送されます。 したがって、これらのメッセージはサブスクリプションに正常に転送されているため、そのトピック自体のアクティブなメッセージ数は 0 です。 サブスクリプションにあるメッセージ数を取得し、それが 0 より大きいことを確認してください。 サブスクリプションにメッセージがあるとしても、実際には、それらはトピックが所有しているストレージに格納されています。 サブスクリプションを見れば、メッセージ数は 0 以外の数になっているはずです (このエンティティ全体で合計最大 323 MB のスペースを使用)。


## <a name="using-azure-portal"></a>Azure Portal の使用
名前空間に移動してキューを選びます。 そのキューの **概要** ページでメッセージ数を確認できます。

:::image type="content" source="./media/message-counters/queue-overview.png" alt-text="キューの概要ページのメッセージ数":::

名前空間に移動してトピックを選び、そのトピックのサブスクリプションを選びます。 そのキューの **概要** ページでメッセージ数を確認できます。

:::image type="content" source="./media/message-counters/subscription-overview.png" alt-text="サブスクリプションの概要ページのメッセージ数":::

## <a name="using-azure-cli"></a>Azure CLI の使用
次の例に示すとおり、[`az servicebus queue show`](/cli/azure/servicebus/queue#az_servicebus_queue_show) コマンドでキューのメッセージ数の詳細を取得できます。 

```azurecli-interactive
az servicebus queue show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --query countDetails
```

出力例を次に示します。

```bash
ActiveMessageCount    DeadLetterMessageCount    ScheduledMessageCount    TransferMessageCount    TransferDeadLetterMessageCount
--------------------  ------------------------  -----------------------  ----------------------  --------------------------------
0                     0                         0                        0                       0
```

次の例に示すとおり、[`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) コマンドでサブスクリプションのメッセージ数の詳細を取得できます。 

```azurecli-interactive
az servicebus topic subscription show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysub \
    --query countDetails
```

## <a name="using-azure-powershell"></a>Azure PowerShell の使用
PowerShell では、キューのメッセージ数の詳細を次の手順で取得できます。

```azurepowershell-interactive
$queueObj=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
                    -NamespaceName mynamespace `
                    -QueueName myqueue 

$queueObj.CountDetails
```

出力例を次に示します。

```bash
ActiveMessageCount             : 7
DeadLetterMessageCount         : 1
ScheduledMessageCount          : 3
TransferMessageCount           : 0
TransferDeadLetterMessageCount : 0
```

サブスクリプションのメッセージ数の詳細は次の手順で取得できます。

```azurepowershell-interactive
$topicObj= Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
                -NamespaceName mynamespace `
                -TopicName mytopic `
                -SubscriptionName mysub

$topicObj.CountDetails
```

`ActiveMessageCount`、`DeadLetterMessageCount`、`ScheduledMessageCount`、`TransferDeadLetterMessageCount`、`TransferMessageCount` のプロパティを持つ `MessageCountDetails` オブジェクトが返されます。 

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
