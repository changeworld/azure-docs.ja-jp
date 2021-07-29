---
title: Azure Service Bus メッセージングの概要 | Microsoft Docs
description: この記事では、フル マネージド エンタープライズ統合メッセージ ブローカーである Azure Service Bus の概要を簡単に説明します。
ms.topic: overview
ms.date: 06/11/2021
ms.openlocfilehash: 932fde55bdd315993de5375706f7d5d3621cef6d
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112030715"
---
# <a name="what-is-azure-service-bus"></a>Azure Service Bus とは
Microsoft Azure Service Bus は、メッセージ キューとパブリッシュとサブスクライブ トピックを備えたフル マネージド エンタープライズ統合メッセージ ブローカーです。 Service Bus は、アプリケーションとサービスを相互に分離するために使用され、次のような利点があります。

- 競合する worker 間での作業の負荷分散
- サービスやアプリケーションの境界を越えたデータと制御の安全なルーティングおよび転送
- 高い信頼性を必要とするトランザクション作業の調整 

## <a name="overview"></a>概要
データは、**メッセージ** を使用してさまざまなアプリとサービス間で転送されます。 メッセージは、メタデータで装飾されたコンテナーであり、データを格納します。 データは、次のような一般的な形式でエンコードされた構造化データなど、どのような種類の情報でもかまいません: JSON、XML、Apache Avro、プレーンテキスト。

一般的なメッセージング シナリオの例を次にいくつか示します。

* **メッセージング**。 販売または購入の注文、仕訳帳、在庫移動などのビジネス データを転送します。
* **アプリケーションの切り離し**。 アプリケーションとサービスの信頼性とスケーラビリティを向上します。 プロデューサーとコンシューマーは同時に、オンラインでなくても、すぐに利用できなくてもかまいません。 トラフィックの急増によってサービスに過大な負荷を掛けないように[負荷は平準化されます](/azure/architecture/patterns/queue-based-load-leveling)。 
* "**負荷分散**"。 複数の[競合コンシューマー](/azure/architecture/patterns/competing-consumers)が同時にキューから読み取ることができ、それぞれが特定のメッセージに対する排他的な所有権を取得します。 
* **トピックとサブスクリプション**。 [パブリッシャーとサブスクライバー](/azure/architecture/patterns/publisher-subscriber)の間の 1 対 *n* のリレーションシップを可能にし、サブスクライバーは公開されたメッセージ ストリームから特定のメッセージを選択できます。
* **Transactions**。 複数の操作をすべてアトミック トランザクションのスコープ内で行うことができます。 たとえば、次の操作をトランザクションのスコープ内で実行できます。  

    1. 1 つのキューからメッセージを取得する。
    2. 処理の結果を 1 つ以上の異なるキューにポストする。
    3. 元のキューから入力メッセージを移動する。 
    
    結果は、入力メッセージの正常な解決など、成功した場合にのみ、ダウンストリーム コンシューマーから確認できるようになり、これにより、1 回限りの処理セマンティクスが可能になります。 このトランザクション モデルは、より大きなソリューション コンテキストにおける[補正トランザクション](/azure/architecture/patterns/compensating-transaction) パターンの堅牢な基盤です。 
* **メッセージ セッション**。 厳密なメッセージの順序付けやメッセージの遅延を必要とする、ワークフローと多重転送の高スケールな調整を実装します。

Apache ActiveMQ などの他のメッセージ ブローカーに慣れている場合、Service Bus の概念はご存じの概念と似ています。 Service Bus はサービスとしてのプラットフォーム (PaaS) であるため、重要な違いは、次のアクションについて心配する必要がないことです。 これらの作業は Azure が代わりに行います。 

- ハードウェア障害についての心配 
- オペレーティング システムや製品へのパッチの継続的な適用
- ログの配置とディスク領域の管理
- バックアップの処理
- 予備のマシンへのフェールオーバー

## <a name="compliance-with-standards-and-protocols"></a>標準とプロトコルへの準拠

Service Bus のプライマリ ワイヤ プロトコルは [Advanced Messaging Queuing Protocol (AMQP) 1.0](service-bus-amqp-overview.md) (オープンな ISO/IEC 標準) です。 これにより、Service Bus とオンプレミスのブローカー (ActiveMQ や RabbitMQ など) に対して機能するアプリケーションを作成できます。 [AMQP プロトコル ガイド](service-bus-amqp-protocol-guide.md)には、そうした抽象化を構築する場合に役立つ詳細情報が記載されています。

[Service Bus Premium](service-bus-premium-messaging.md) は、Java/Jakarta EE の [Java Message Service (JMS) 2.0](how-to-use-java-message-service-20.md) API に完全に準拠しています。 また、Service Bus Standard では、キューに重点を置いた JMS 1.1 サブセットをサポートしています。 JMS は、メッセージ ブローカー用の一般的な抽象化であり、多くのアプリケーションやフレームワーク (広く使用されている Spring Framework など) と統合されています。 他のブローカーから Azure Service Bus に切り替える場合に必要なのは、キューとトピックのトポロジを再作成し、クライアント プロバイダーの依存関係と構成を変更することだけです。 例については、[ActiveMQ の移行ガイド](migrate-jms-activemq-to-servicebus.md)を参照してください。

## <a name="concepts-and-terminology"></a>概念と用語 
このセクションでは、Service Bus の概念と用語について説明します。

### <a name="queues"></a>キュー
メッセージは **キュー** に送受信されます。 受信側アプリがメッセージを受信して処理できるようになるまで、キューがメッセージを格納します。

![キュー](./media/service-bus-messaging-overview/about-service-bus-queue.png)

キュー内のメッセージは到着順に並べ替えられ、タイムスタンプが付けられます。 ブローカーが受信すると、常にメッセージはトリプル冗長性を備えたストレージに永続的に保持され、名前空間のゾーンが有効になっている場合は複数の可用性ゾーンにまたがって分散されます。 Service Bus は、受信したことをクライアントにレポートした後に、メッセージをメモリや揮発性ストレージ内に残しません。

メッセージは **プル** モードで配信され、要求された場合のみメッセージが配信されます。 他の一部のクラウド キューにおけるビジーポーリング モデルとは異なり、プル操作は有効期間を長くし、メッセージが使用可能になった時点でのみ完了させることができます。 

### <a name="topics"></a>トピック

**トピック** を使用してメッセージを送受信することもできます。 キューはポイント間通信によく使用されますが、トピックは公開/サブスクライブのシナリオで役立ちます。

![トピック](./media/service-bus-messaging-overview/about-service-bus-topic.png)

トピックには複数の独立したサブスクリプションを含めることができます。これらはトピックにアタッチされますが、その点以外は受信側のキューと同じように動作します。 トピックのサブスクライバーは、そのトピックに送信された各メッセージのコピーを受信できます。 サブスクリプションは名前付きエンティティです。 サブスクリプションは既定では永続的ですが、有効期限が切れたら自動的に削除されるように構成することができます。 Service Bus Premium では、JMS API を介して、接続している間存在する揮発性サブスクリプションを作成することもできます。

サブスクリプションに対してルールを定義できます。 サブスクリプション ルールには、サブスクリプションにコピーするメッセージの条件を定義する "**フィルター**" と、メッセージのメタデータを変更できるオプションの "**アクション**" があります。 詳細については、「[トピック フィルターとアクション](topic-filters.md)」を参照してください。 この機能は次のシナリオで役立ちます。

- トピックに送信されたすべてのメッセージをサブスクリプションに受信させたくない。
- サブスクリプションを通過するときに、追加のメタデータでメッセージをマークアップする。

### <a name="namespaces"></a>名前空間
名前空間はすべてのメッセージング コンポーネントのコンテナーです (キューとトピック)。 複数のキューとトピックを 1 つの名前空間に格納できます。多くの場合、名前空間はアプリケーション コンテナーとして機能します。 

名前空間は他のブローカーの用語ではサーバーに相当しますが、概念が 1 対 1 で対応しているわけではありません。 Service Bus の名前空間は、すべてアクティブな多数の仮想マシンで構成される大規模なクラスターの自分の容量スライスです。 必要に応じて、3つの [Azure 可用性ゾーン](../availability-zones/az-overview.md)にまたがることができます。 そのため、メッセージ ブローカーを大規模に実行する場合の可用性と堅牢性の利点をすべて得ることができます。 また、根底にある複雑さについて心配する必要はありません。 Service Bus はサーバーレスなメッセージングです。

## <a name="advanced-concepts"></a>高度な概念
Service Bus には、メッセージ セッション、計画配信、さらに複雑なメッセージング問題を解決できるトランザクションなど、高度な機能が含まれています。 詳細については、[Azure Service Bus の高度な機能](advanced-features-overview.md)に関するページを参照してください。

## <a name="client-libraries"></a>クライアント ライブラリ

完全にサポートされている Service Bus クライアント ライブラリを Azure SDK 経由で利用できます。

- [.NET 用 Azure Service Bus](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Java 用 Azure Service Bus ライブラリ](/java/api/overview/azure/servicebus?preserve-view=true)
- [Java JMS 2.0 用 Azure Service Bus プロバイダー](how-to-use-java-message-service-20.md)
- [JavaScript および TypeScript 用の Azure Service Bus モジュール](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Python 用 Azure Service Bus ライブラリ](/python/api/overview/azure/servicebus?preserve-view=true)

[Azure Service Bus のプライマリ プロトコルは AMQP 1.0 です](service-bus-amqp-overview.md)。AMQP 1.0 に準拠している任意のプロトコル クライアントから使用できます。 いくつかのオープンソース AMQP クライアントには、Service Bus の相互運用性を明示的に示すサンプルが含まれています。 AMQP 1.0 クライアントで Service Bus の機能を直接使用する方法については、[AMQP 1.0 プロトコル ガイド](service-bus-amqp-protocol-guide.md)をご確認ください。

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="integration"></a>統合

Service Bus は、次のような多くの Microsoft および Azure サービスと完全に統合されています。

* [Event Grid](service-bus-to-event-grid-integration-example.md)
* [Logic Apps](../connectors/connectors-create-api-servicebus.md)
* [Azure Functions](../azure-functions/functions-bindings-service-bus.md)
* [Power Platform](../connectors/connectors-create-api-servicebus.md)
* [Dynamics 365](/dynamics365/fin-ops-core/dev-itpro/business-events/how-to/how-to-servicebus)
* [Azure Stream Analytics](../stream-analytics/stream-analytics-define-outputs.md)

## <a name="next-steps"></a>次のステップ

Service Bus メッセージングの基本的な使い方については、以下の記事を参照してください。

* Azure メッセージング サービスの比較については、「[サービスの比較](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)」を参照してください。
* [.NET](service-bus-dotnet-get-started-with-queues.md)、[Java](service-bus-java-how-to-use-queues.md)、または [JMS](service-bus-java-how-to-use-jms-api-amqp.md) のクイック スタートを試す。
* Service Bus リソースを管理するには、「[Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)」を参照してください。
* Standard レベルと Premium レベルとその価格の詳細については、「[Service Bus の価格](https://azure.microsoft.com/pricing/details/service-bus/)」を参照してください。
* Premium レベルのパフォーマンスと待機時間の詳細については、「[Premium メッセージング](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722)」を参照してください。