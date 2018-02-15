---
title: "Azure Service Bus の重複メッセージの検出 | Microsoft Docs"
description: "Service Bus の重複メッセージの検出"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: sethm
ms.openlocfilehash: efc5608d4812edbb3f477dffbc2b495b331bd787
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2018
---
# <a name="duplicate-detection"></a>重複検出

メッセージの送信直後にアプリケーションで致命的なエラーが発生し、アプリケーション インスタンスが再起動され、メッセージが送信されていなかったと誤認識した場合、メッセージは再送信され、同じメッセージがシステム内で 2 回発生します。

また、送信直前にクライアントまたはネットワーク レベルでエラーが発生し、送信されたメッセージがキューにコミットされて、クライアントに確認が返信されない場合もあります。 このシナリオでは、送信操作の結果についてクライアントは確認が取れないままになります。

重複メッセージの検出機能は、送信側が同じメッセージを再送信することを可能にすることで、このような状況を解決し、重複メッセージはキューまたはトピックで削除されます。

重複メッセージ検出を有効にすることにより、アプリケーションによって制御され、指定した時間枠内のキューまたはトピックに送信されたすべてのメッセージの *MessageId* を追跡できます。 時間枠内ですでにログに記録されている *MessageId* が付いた新しいメッセージが送信されると、メッセージは受信済み (送信操作成功) として報告され、新しく送信されたメッセージは瞬時に無視され破棄されます。 メッセージの *MessageId* 以外の部分は考慮されません。

ID のアプリケーションによる制御は、傷害が発生した場合に、アプリケーションが *MessageId* をビジネス プロセス コンテキストに紐付けて予想可能な方法で再構築することができる唯一の方法であるため、不可欠です。

アプリケーション コンテキストの処理過程で複数のメッセージが送信されるビジネス プロセスの場合、*MessageId* は、注文書番号などのアプリケーション レベルのコンテキスト識別子と、メッセージの内容を組み合わせたもの、たとえば、**12345.2017/支払**のようになります。

*MessageId* には常になんらかの GUID を使用でき、重複検出機能を効果的に活用するには、ビジネス プロセスと ID を紐付けることで予想可能な再現性を確立する必要があります。

## <a name="enable-duplicate-detection"></a>重複検出の有効化

この機能は、ポータルでエンティティの作成する際に、既定ではオフになっている、**[重複データ検出を有効にする]** チェック ボックスをオンにすることで有効にできます。 新しいトピックを作成するための設定と同じです。

![][1]

プログラム上では、フル フレームワーク .NET API の [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) プロパティにフラグを設定します。 Azure Resource Manager API では、値は [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) プロパティで設定されます。

重複検出時間の履歴は、既定では、キューおよびトピックで 30 秒に設定され、最大値は 7 日間に設定されています。 この設定は、Azure ポータルの [キューおよびトピックのプロパティ] ウィンドウで変更することができます。

![][2]

プログラム上は、フル .NET Framework API の [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) プロパティを使用して、メッセージ が保持される重複検出時間枠を設定できます。 Azure Resource Manager API では、値は [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) プロパティで設定します。

すべての記録されたメッセージ ID を新しく送信されたメッセージ ID と照合する必要があるため、重複の検出機能を有効化し、時間枠を設定すると、キュー (およびトピック) のスループットに直接影響することに注意してください。

時間枠を小さく設定すると、保持し照合する必要があるメッセージ ID 数が減り、スループットへの影響は小さくなります。 スループットの高いエンティティで重複検出が必要な場合は、時間枠を小さく設定してください。

## <a name="next-steps"></a>次の手順

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus の基礎](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
