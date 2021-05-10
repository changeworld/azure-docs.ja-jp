---
title: Service Bus の配信不能キュー | Microsoft Docs
description: Service Bus の配信不能キューについて説明します。 Service Bus キューおよびトピック サブスクリプションでは、配信不能キューと呼ばれるセカンダリ サブキューが提供されます。
ms.topic: article
ms.date: 04/08/2021
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: cb791982b50d7afff7b74d70adfd285bb5e0a11c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773227"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Service Bus の配信不能キューの概要

Azure Service Bus キューおよびトピック サブスクリプションでは、*配信不能キュー* (DLQ) と呼ばれるセカンダリ サブキューが提供されます。 配信不能キューを明示的に作成する必要はなく、削除したり、メイン エンティティとは別に管理したりすることはできません。

この記事では、Service Bus の配信不能キューについて説明します。 説明の多くは、GitHub の[配信不能キューのサンプル](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/DeadletterQueue)に示されています。
 
## <a name="the-dead-letter-queue"></a>配信不能キュー

配信不能キューの目的は、受信者に配信できないメッセージ、または処理できなかったメッセージを保持することです。 したがって、メッセージを DLQ から取り出して、検査することができます。 アプリケーションは演算子を利用して、問題を修正してメッセージを再送信し、エラーが発生していたという事実をログに記録してから修正処置を行います。 

API とプロトコルの観点では、DLQ は他のキューとほとんど同じですが、メッセージを再送信できるのは親エンティティの配信不能操作でのみである点が異なります。 また、有効期間は監視されず、DLQ のメッセージを配信不能にすることはできません。 配信不能キューでは、ピーク ロック配信やトランザクション操作が完全にサポートされます。

DLQ は自動的にクリーンアップされません。 DLQ から明示的に取得し、配信不能メッセージを完了するまで、メッセージは DLQ に残ります。


## <a name="dlq-message-count"></a>DLQ のメッセージ数
配信不能キュー内のメッセージの数をトピック レベルで取得することはできません。 これは、Service Bus が内部エラーをスローしない限り、メッセージはトピック レベルに存在しないためです。 送信者がトピックにメッセージを送信すると、メッセージはミリ秒以内にトピックのサブスクリプションに転送されるため、トピック レベルには存在しなくなります。 したがって、トピックのサブスクリプションに関連付けられている DLQ にメッセージが表示されます。 次の例では、**Service Bus Explorer** で、サブスクリプション "test1" の DLQ に現在 62 件のメッセージがあることが示されています。 

![DLQ のメッセージ数](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Azure CLI コマンド [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) を使用して、DLQ のメッセージ数を取得することもできます。 

## <a name="moving-messages-to-the-dlq"></a>DLQ にメッセージを移動する
Service Bus には、メッセージがメッセージング エンジン自体から DLQ にプッシュされる原因となるアクティビティがいくつかあります。 アプリケーションは明示的にメッセージを DLQ に移動することもできます。 次の 2 つのプロパティ (配信不能の理由と説明) は、配信不能メッセージに追加されます。 アプリケーションでは配信不能理由のプロパティに対して独自のコードを定義できますが、システムでは以下の値が設定されます。

| 配信不能メッセージの理由 | 配信不能メッセージ エラーの説明 |
| --- | --- |
|HeaderSizeExceeded |このストリームのサイズ クォータを超えています。 |
|TTLExpiredException |メッセージの有効期限が切れているため、配信不能です。 詳細については、「[Time to live](#time-to-live)」セクションを参照してください。 |
|Session ID is null. (セッション ID は Null です。) |セッションが有効なエンティティではセッション ID が Null のメッセージは許可されません。 |
|MaxTransferHopCountExceeded | キュー間で転送するときに許容される最大ホップ数。 値は 4 に設定されています。 |
| MaxDeliveryCountExceededExceptionMessage | 最大数の配信試行後、メッセージを使用できませんでした。 詳細については、「[最大配信数](#maximum-delivery-count)」セクションを参照してください。 |

## <a name="maximum-delivery-count"></a>最大配信数
Service Bus のキューとサブスクリプションについてメッセージの配信を試行できる回数には制限があります。 既定値は 10 です。 メッセージがピーク ロック状態で配信されたが、明示的に破棄されたかロックが期限切れになった場合は、メッセージの配信回数が増えます。 配信回数が上限を超えると、メッセージは DLQ に移動されます。 DLQ のメッセージの配信不能理由は、MaxDeliveryCountExceeded に設定されます。 この動作を無効にすることはできませんが、最大配信回数を大きい数に設定することはできます。

## <a name="time-to-live"></a>Time to Live
キューまたはサブスクリプションで配信不能を有効にすると、有効期限が切れたすべてのメッセージは DLQ に移動されます。 配信不能理由コードは、TTLExpiredException に設定されます。

メイン キューまたはサブスクリプションに対してプルを実行しているアクティブな受信者が少なくとも 1 つある場合は、期限切れメッセージは単に消去され、DLQ に移動されるだけです。 遅延メッセージは、有効期限が切れても、削除されたり、配信不能キューに移動されることはありません。 この動作は仕様です。

## <a name="errors-while-processing-subscription-rules"></a>サブスクリプション ルールの処理中のエラー
フィルター評価の例外で配信不能を有効にすると、サブスクリプションの SQL フィルター規則の実行中に発生したエラーは、問題のあるメッセージと共に DLQ にキャプチャされます。 このオプションは、すべてのメッセージ型がサブスクライバーを持つわけではない運用環境では使用しないでください。

## <a name="application-level-dead-lettering"></a>アプリケーション レベルの配信不能処理
システム指定の配信不能処理機能に加え、アプリケーションでは DLQ を使用して許容できないメッセージを明示的に拒否できます。 それらには、何らかのシステムの問題により適切に処理できないメッセージ、誤った形式のペイロードを保持するメッセージ、メッセージ レベルのセキュリティ スキームの使用時に認証に失敗したメッセージなどがあります。

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>ForwardTo または SendVia での配信不能シナリオ
メッセージは、次の条件に該当するときに転送不能キューに送信されます。

- メッセージが、[連結](service-bus-auto-forwarding.md)されている 5 つ以上のキューまたはトピックを通過する。
- 送信先キューまたはトピックが無効または削除されている。
- 送信先キューまたはトピックがエンティティの最大サイズを超えている。

## <a name="path-to-the-dead-letter-queue"></a>配信不能キューへのパス
配信不能キューにアクセスするには、次の構文を使用します。

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```


## <a name="next-steps"></a>次のステップ

Service Bus キューの詳細については、次の記事を参照してください。

* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Azure キューと Service Bus キューの比較](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
