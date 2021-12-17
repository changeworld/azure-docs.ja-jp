---
title: Azure Service Bus - メッセージ遅延
description: この記事では、Azure Service Bus メッセージの配信を遅延する方法について説明します。 メッセージは、キューまたはサブスクリプションに留まりますが、確保されます。
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 71446f7989a235e79ca3ab208579edd0043dfeb2
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/25/2021
ms.locfileid: "107987256"
---
# <a name="message-deferral"></a>メッセージ遅延
キューまたはサブスクリプション クライアントが処理が必要なメッセージを受信したものの、特別な状況が原因でただちに処理を実行できない場合、メッセージの取得を後の時点に "遅延" させるオプションを使用できます。 メッセージは、キューまたはサブスクリプションに留まりますが、確保されます。

> [!NOTE]
> 遅延メッセージは、[有効期限が切れた後](./service-bus-dead-letter-queues.md#time-to-live)に、配信不能キューに自動的に移動されません。 この動作は仕様によるものです。

## <a name="sample-scenarios"></a>サンプル事例
遅延は、ワークフロー処理のシナリオを対象に作成された機能です。 ワークフロー フレームワークでは、特定の操作を特定の順序で処理することが必要な場合があります。 場合によっては、受信したメッセージの処理を、他のメッセージによって通知される前の所定の作業が完了するまで延期する必要があります。

簡単でわかりやすい例としては、一致する注文書が店舗からフルフィルメント システムに入力される前に、外部の支払いプロバイダーからの支払い通知がシステムに表示される注文処理シーケンスが挙げられます。 このケースでは、関連する注文が届くまで、支払い通知の処理がフルフィルメント システムによって遅延される場合があります。 異なる送信元からのメッセージによってワークフローが進行する集合シナリオでは、リアルタイムの実行順序は正しいのに、結果を反映するメッセージが到着する順序は正しくない場合があります。

つまり遅延は、処理の延期が必要なメッセージをメッセージ ストアに確実に保持しながら、到着した順序から処理できる順序に並べ直すのに役立ちます。

メッセージを処理する特定のリソースが一時的に使用できないためにそのメッセージを処理できない一方で、メッセージの処理をただちに中断できない場合があります。このような場合、数分間そのメッセージを確保する方法は、数分以内に投稿される[スケジュール済みメッセージ](message-sequencing.md)のシーケンス番号を記憶し、スケジュールされたメッセージが到着したときに遅延メッセージを再取得することです。 メッセージ ハンドラーですべての操作についてデータベースが使用されており、そのデータベースが一時的に使用できない場合、遅延は使用しないでください。その代わり、データベースが再び使用できるようになるまで、メッセージの受信を完全に中断してください。 

## <a name="retrieving-deferred-messages"></a>遅延メッセージの取得
遅延メッセージは、他のすべてのアクティブなメッセージと共にメイン キューに留まります (サブキューに含まれる配達不能メッセージとは異なります)。ただし、通常の受信操作を使用してこれらを受信することはできなくなります。 アプリケーションが遅延メッセージを追跡できなくなった場合、[メッセージ参照](message-browsing.md)を使用して検出できます。

遅延メッセージを取得するには、その所有者がそれを遅延させるときにシーケンス番号を記憶する必要があります。 遅延メッセージのシーケンス番号を知っている受信者は、シーケンス番号をパラメーターとして受け取る受信メソッドを使用して、後でメッセージを受信できます。 シーケンス番号の詳細については、「[メッセージのシーケンス処理とタイムスタンプ](message-sequencing.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
Azure Service Bus の機能については、使用する言語のサンプルを試してみてください。 

- [.NET 用の Azure Service Bus クライアント ライブラリのサンプル (最新)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) - **メッセージの解決** のサンプルを参照してください。 
- [Java 用の Azure Service Bus クライアント ライブラリのサンプル (最新)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Python 用の Azure Service Bus クライアント ライブラリのサンプル](/samples/azure/azure-sdk-for-python/servicebus-samples/) - **receive_deferred_message_queue.py** サンプルを参照してください。 
- [JavaScript 用の Azure Service Bus クライアント ライブラリのサンプル](/samples/azure/azure-sdk-for-js/service-bus-javascript/) - **advanced/deferral.js** サンプルを参照してください。 
- [TypeScript 用の Azure Service Bus クライアント ライブラリのサンプル](/samples/azure/azure-sdk-for-js/service-bus-typescript/) - **advanced/deferral.ts** サンプルを参照してください。 

以前の .NET および Java クライアント ライブラリのサンプルについては、以下を参照してください。
- [.NET 用の Azure Service Bus クライアント ライブラリのサンプル (レガシ)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) - **遅延** のサンプルを参照してください。 
- [Java 用の Azure Service Bus クライアント ライブラリのサンプル (レガシ)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)
