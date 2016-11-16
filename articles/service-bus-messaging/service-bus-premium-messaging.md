---
title: "Service Bus の Premium および Standard メッセージング価格レベルの概要 | Microsoft Docs"
description: "Service Bus の Premium および Standard メッセージング"
services: service-bus
documentationcenter: .net
author: djrosanova
manager: timlt
editor: 
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/02/2016
ms.author: darosa;sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5f4fccdafd9b47794bf625ac51dc650b6e8752f1


---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Service Bus の Premium および Standard メッセージング レベル
Service Bus メッセージングには、キューやトピックなどのメッセージング エンティティが含まれており、エンタープライズ メッセージング機能と、クラウド スケールの豊富な発行/サブスクライブ セマンティクスが結合されます。 Service Bus メッセージングは、多くの高度なクラウド ソリューションで、通信のバックボーンとして使用されます。

Service Bus メッセージングに *Premium* レベルを導入して、ミッションクリティカルなアプリケーションのスケール、パフォーマンス、および可用性に関する顧客の一般的な要求に対処しています。 機能セットとほぼ同じですが、Azure Service Bus メッセージングのこれら 2 つのレベルは、さまざまなユース ケースに応えるように設計されています。

次の表に、大まかな違いのいくつかを示します。

| プレミアム | Standard |
| --- | --- |
| 高スループット |変わりやすいスループット |
| 予測可能なパフォーマンス |変わりやすい待機時間 |
| 予測可能な料金 |従量性の変わりやすい料金 |
| ワークロードを上下に設定する機能 |該当なし |
| メッセージ サイズは 256 KB 以上 |メッセージ サイズは 256 KB |

**Service Bus Premium メッセージング**では、各顧客のワークロードが分離した状態で実行されるように、CPU およびメモリ層でリソースが分離されます。 このリソースのコンテナーを、*メッセージング ユニット*と呼びます。 各 Premium 名前空間には、1 つ以上のメッセージング ユニットが割り当てられます。 Service Bus の Premium 名前空間ごとに、1 個、2 個、または 4 個のメッセージング ユニットを購入できます。 1 つのワークロードまたはエンティティは、複数のメッセージング ユニットにまたがることができます。課金は 24 時間単位、すなわち日単位ですが、メッセージング ユニットの数は自由に変更できます。 その結果、Service Bus ベースのソリューションのパフォーマンスは、予測可能で反復可能になります。

このパフォーマンスは、より予測可能かつ利用可能なだけでなく、より高速です。 Service Bus Premium メッセージングは、[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) に導入されたストレージ エンジンが基盤になっています。 Premium メッセージングでのピークのパフォーマンスは、Standard レベルよりもはるかに高速です。

## <a name="premium-messaging-technical-differences"></a>Premium メッセージングの技術的な相違点
Premium および Standard メッセージング レベルの間でのいくつかの違いを次に示します。

### <a name="partitioned-queues-and-topics"></a>パーティション分割されたキューとトピック
パーティション分割されたキューとトピックは Premium メッセージングでサポートされますが、Standard レベルと Basic レベルの Service Bus メッセージングと同様には機能しません。 Premium メッセージングは SQL をデータ ストアとして使用しないため、共有プラットフォームに関連するリソース競合が発生する可能性がなくなりました。 その結果、パーティション分割は必要ありません。 さらに、パーティション数は、Standard メッセージングでの 16 から、Premium メッセージングでは 2 に変更されました。 2 個のパーティションによって可用性が確保されます。また、Premium の実行時環境にとって、2 個というのはより適切なパーティション数です。 パーティション分割の詳細については、「[パーティション分割されたキューとトピック](service-bus-partitioning.md)」を参照してください。

### <a name="express-entities"></a>エクスプレス エンティティ
完全に分離された実行時環境で Premium メッセージングが実行されるため、Premium 名前空間ではエクスプレス エンティティがサポートされません。 エクスプレス機能の詳細については、 [Microsoft.ServiceBus.Messaging.QueueDescription.EnableExpress](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enableexpress.aspx) プロパティを参照してください。

## <a name="next-steps"></a>次のステップ
Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Azure Service Bus Premium メッセージングの概要 (ブログの投稿)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Azure Service Bus Premium メッセージングの概要(Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Service Bus メッセージングの概要](service-bus-messaging-overview.md)
* [Service Bus キューの使用方法](service-bus-dotnet-get-started-with-queues.md)




<!--HONumber=Nov16_HO2-->


