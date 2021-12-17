---
title: Azure Service Bus - メッセージの参照
description: Service Bus メッセージを参照してピークすると、Azure Service Bus クライアントは、キューまたはサブスクリプションのすべてのメッセージを列挙できます。
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: 23ec2187ae58b1b4f83addb80573bdb130ff99bd
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/25/2021
ms.locfileid: "107989398"
---
# <a name="message-browsing"></a>メッセージの読み取り
Service Bus クライアントは、メッセージを読み取るか、ピークすることにより、診断とデバッグの目的で、キューまたはサブスクリプションのすべてのメッセージを列挙できます。

キューまたはサブスクリプションに対するピーク操作では、多くてもメッセージの要求された数しか返されません。 次の表に、ピーク操作によって返されるメッセージの種類を示します。 

| メッセージの種類 | 表示の有無 | 
| ---------------- | ----- | 
| アクティブなメッセージ | はい |
| 配信不能メッセージ | いいえ | 
| ロックされたメッセージ | はい |
| 期限切れのメッセージ |  可能性あり (それらが配信不能になる前) |
| スケジュール設定されたメッセージ | キューの場合は、はい。 サブスクリプションの場合、いいえ |

## <a name="dead-lettered-messages"></a>配信不能メッセージ
キューまたはサブスクリプションの **[配信不能]** メッセージをピークするには、そのキューまたはサブスクリプションに関連付けられている配信不能キューでピーク操作を実行する必要があります。 詳細については、[配信不能キューへのアクセス](service-bus-dead-letter-queues.md#path-to-the-dead-letter-queue)に関する記事を参照してください。

## <a name="expired-messages"></a>期限切れのメッセージ
ピーク操作から返された結果に、期限切れのメッセージが含まれることがあります。 消費され、有効期限の切れたメッセージは、非同期 "ガベージ コレクション" を実行することよって消去されます。 この手順は、メッセージの有効期限が切れた後にただちに実行されるとは限りません。 そのため、ピーク操作は有効期限が既に切れているメッセージを返す可能性があります。 これらのメッセージは、次回キューまたはサブスクリプションで受信操作が呼び出されたときに削除されるか、または配信不能となります。 キューから遅延メッセージを回復しようとする場合、この動作に留意してください。 

期限切れのメッセージは、ピークによって返された場合でも、他の方法では通常の取得ができなくなります。 ピークはログの現在の状態を反映する診断ツールであるため、これらのメッセージが返されるのは設計によるものです。

## <a name="locked-messages"></a>ロックされたメッセージ
ピークによって、他のレシーバーが現在処理している **ロックされた** メッセージも返されます。 ただし、ピークでは切断されたスナップショットが返されるため、メッセージのロック状態はピーク メッセージでは確認できません。

## <a name="peek-apis"></a>Peek API
ピークは、キュー、サブスクリプション、およびそれらの配信不能キューで動作します。 

繰り返し呼び出されると、ピーク操作は、キューまたはサブスクリプション内のすべてのメッセージを、使用可能なシーケンス番号が最小のものから最大の順に列挙します。 これは、メッセージがエンキューされた順序であり、メッセージが最終的に取得される可能性のある順序ではありません。

また、SequenceNumber をピーク操作に渡すこともできます。 これは、ピークの開始位置を特定するために使用されます。 このパラメーターを指定せずに、ピーク操作への後続の呼び出しを行い、さらに列挙することができます。

## <a name="next-steps"></a>次のステップ
Azure Service Bus の機能については、使用する言語のサンプルを試してみてください。 

- [.Net 用の Azure Service Bus クライアント ライブラリのサンプル (最新)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) -- **メッセージの送信と受信** のサンプル
- [Java 用の Azure Service Bus クライアント ライブラリのサンプル (最新)](/samples/azure/azure-sdk-for-java/servicebus-samples/) - **メッセージのピーク** のサンプル
- [Python 用の Azure Service Bus クライアント ライブラリのサンプル](/samples/azure/azure-sdk-for-python/servicebus-samples/)  - **receive_peek.py** のサンプル
- [JavaScript 用の Azure Service Bus クライアント ライブラリのサンプル](/samples/azure/azure-sdk-for-js/service-bus-javascript/)  - **browseMessages.js** のサンプル
- [TypeScript 用の Azure Service Bus クライアント ライブラリのサンプル](/samples/azure/azure-sdk-for-js/service-bus-typescript/) - **browseMessages.ts** のサンプル

以前の .NET および Java クライアント ライブラリのサンプルについては、以下を参照してください。
- [.Net 用の Azure Service Bus クライアント ライブラリのサンプル (レガシ)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) - **メッセージの参照 (ピーク)** のサンプル
- [Java 用の Azure Service Bus クライアント ライブラリのサンプル (レガシ)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus) - **メッセージの参照** のサンプル 

