<properties
	pageTitle="Service Bus の Premium および Standard メッセージング価格レベルの概要 | Microsoft Azure"
	description="Service Bus の Premium および Standard メッセージング"
	services="service-bus"
	documentationCenter=".net"
	authors="djrosanova"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="10/15/2015"
	ms.author="darosa"/>

# Service Bus の Premium および Standard メッセージング レベル 

Service Bus 仲介型メッセージングには、キューやトピックなどのメッセージング エンティティが含まれており、エンタープライズ メッセージング機能と、クラウド スケールの豊富なパブリッシュ/サブスクライブ セマンティクスが結合されます。Service Bus 仲介型メッセージングは、多くの高度なクラウド ソリューションで、通信のバックボーンとして使用されます。

Service Bus メッセージングに *Premium* レベルを導入して、ミッションクリティカルなアプリケーションのスケール、パフォーマンス、および可用性に関する顧客の一般的な要求に対処しています。機能セットとほぼ同じですが、Azure Service Bus メッセージングのこれら 2 つのレベルは、さまざまなユース ケースに応えるように設計されています。

次の表に、大まかな違いのいくつかを示します。

| Premium | Standard |
|---------------------------------------|--------------------------------|
| 高スループット | 変わりやすいスループット |
| 予測可能なパフォーマンス | 変わりやすい待機時間 |
| 予測可能な料金 | 従量性の変わりやすい料金 |
| ワークロードを上下に設定する機能 | 該当なし |

**Azure Service Bus Premium メッセージング**では、各顧客のワークロードの分離で実行されるように、CPU およびメモリ層でのリソースの分離を提供します。このリソースのコンテナーを、*メッセージング ユニット*と呼びます。各 Premium 名前空間には、1 つ以上のメッセージング ユニットが割り当てられます。Service Bus の Premium 名前空間ごとに、1 個、2 個、または 4 個のメッセージング ユニットを購入できます。1 つのワークロードまたはエンティティは、複数のメッセージング ユニットにまたがることができます。課金は 24 時間単位、すなわち日単位ですが、メッセージング ユニットの数は自由に変更できます。その結果、Service Bus ベースのソリューションのパフォーマンスは、予測可能で反復可能になります。

このパフォーマンスは、より予測可能かつ利用可能なだけでなく、より高速です。Azure Service Bus Premium メッセージングは、[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) に導入されたストレージ エンジンが基盤になっています。Premium メッセージングでのピークのパフォーマンスは、Standard レベルよりもはるかに高速です。

## Premium メッセージングの技術的な相違点

Premium および Standard メッセージング レベルの間でのいくつかの違いを次に示します。

### パーティション分割されたエンティティ

パーティション分割されたエンティティは Premium メッセージングでサポートされますが、Standard および Basic レベルの Service Bus メッセージングと同様には機能しません。Premium メッセージングは SQL をデータ ストアとして使用しないため、共有プラットフォームに関連するリソース競合が発生する可能性がなくなりました。その結果、パーティション分割は必要ありません。さらに、パーティション数は、Standard メッセージングでの 16 から、Premium メッセージングでは 2 に変更されました。2 個のパーティションによって可用性が確保されます。また、Premium の実行時環境にとって、2 個というのはより適切なパーティション数です。詳細については、「[パーティション分割されたメッセージング エンティティ](service-bus-partitioning.md)」を参照してください。

### エクスプレス エンティティ

完全に分離された実行時環境で実行されるため、Premium メッセージングではエクスプレス エンティティが不要になりました。その結果、エクスプレス エンティティは Premium 名前空間でサポートされていません。エクスプレス機能の詳細については、 [Microsoft.ServiceBus.Messaging.QueueDescription.EnableExpress](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enableexpress.aspx) プロパティを参照してください。

## 次のステップ

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

- [Azure Service Bus Premium メッセージングの概要 (ブログの投稿)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Azure Service Bus Premium メッセージングの概要(Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Service Bus メッセージングの概要](service-bus-messaging-overview.md)
- [Azure Service Bus アーキテクチャの概要](service-bus-fundamentals-hybrid-solutions.md)
- [Service Bus キューの使用方法](service-bus-dotnet-how-to-use-queues.md)

<!---HONumber=AcomDC_0218_2016-->