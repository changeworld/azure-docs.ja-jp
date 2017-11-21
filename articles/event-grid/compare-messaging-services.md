---
title: "Azure メッセージング サービスの比較"
description: "Azure Event Grid、Event Hubs、Service Bus を比較します。 さまざまなシナリオでどのサービスを使うべきか、お勧めします。"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/06/2017
ms.author: tomfitz
ms.openlocfilehash: 9a9baa457729bdc4d70a8f9f45701dbdb875d3ce
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2017
---
# <a name="choose-between-azure-services-that-deliver-messages"></a>メッセージを配信する Azure サービスの選択

Azure には、ソリューション全体にわたるイベント メッセージの配信を支援する 3 つのサービスがあります。 これらのサービスを次に示します。

* [Event Grid](/azure/event-grid/)
* [Event Hubs](/azure/event-hubs/)
* [Service Bus](/azure/service-bus-messaging/)

いくつかの類似点もありますが、各サービスはそれぞれ、特定のシナリオ向けに設計されています。 この記事では、これらのサービスの相違点について説明し、アプリケーションでどのサービスを選択すればよいかを把握しやすくします。 多くの場合、メッセージング サービスは補完的で、組み合わせて使用することができます。

## <a name="event-vs-message-services"></a>イベント サービスとメッセージ サービス

イベント配信サービスとメッセージ配信サービスの間には、注意が必要な重要な相違点があります。

### <a name="event"></a>イベント

イベントは、アクションまたは状態変更の軽量な通知です。 イベント データには、発生した事象に関する情報は含まれますが、イベントをトリガーしたデータは含まれていません。 たとえば、あるイベントはサブスクライバーにファイルが作成されたことを通知します。 このイベントにファイルに関する一般情報が含まれることはありますが、ファイル自体は含まれません。 通常、イベントはリアルタイムで動作するイベント ハンドラーをトリガーします。

Event Grid は、イベント処理サービスです。

### <a name="message"></a>メッセージ

メッセージは、サービスによって生成される生データで、別の場所で使用または格納されます。 メッセージには、メッセージ パイプラインをトリガーしたデータが含まれています。 このメッセージは、eコマースの注文からユーザー テレメトリまで、任意の内容にすることができます。 イベント通知とは異なり、メッセージの発行元は何らかの反応を想定している可能性があります。 たとえば、メッセージには生データが含まれていて、システムの次の部分ではそのデータからファイルを作成することが想定されていることがあります。 

Event Hubs と Service Bus は、メッセージング サービスです。

## <a name="comparison-of-services"></a>サービスの比較

| サービス | 目的 | 型 | 使用時の注意 |
| ------- | ------- | ---- | ----------- |
| Event Grid | リアクティブ プログラミング | イベント | 状態の変更に反応する |
| Event Hubs | ビッグ データのパイプライン | メッセージ | テレメトリと分散データ ストリーミング |
| Service Bus | 高価値のエンタープライズ メッセージング | メッセージ | 注文処理や金融取引 |

### <a name="event-grid"></a>Event Grid

Event Grid は、イベント駆動のリアクティブ プログラミングを可能にするイベント バックプレーンです。 発行-サブスクライブ モデルを使用します。 発行元はイベントを発行しますが、どのイベントが処理されるかは想定していません。 サブスクライバーが、どのイベントを処理するかを決定します。

Event Grid は Azure サービスと緊密に統合されており、サード パーティのサービスと統合することもできます。 イベントの使用が簡略化され、定期的にポーリングする必要がなくなるためコストも削減できます。 Event Grid は、Azure および Azure 以外のリソースからのイベントを効率的かつ確実にルーティングします。 イベントは、登録されたサブスクライバーのエンドポイントに配信されます。 イベント メッセージには、サービスやアプリケーションの変更に対応するために必要な情報が含まれています。 Event Grid は、データ パイプラインではないため、更新された実際のオブジェクトは配信しません。

次の特性があります。

* 動的にスケーラブル
* 低コスト
* サーバーレス

### <a name="event-hubs"></a>Event Hubs

Azure Event Hubs は、ビッグ データのパイプラインです。 テレメトリおよびイベント ストリーム データのキャプチャ、保持、再生を容易にします。 データは多数のソースから同時に取得できます。 Event Hubs を使用すると、テレメトリとイベントのデータをさまざまなストリーム処理インフラストラクチャや分析サービスで使用できるようになります。 データ ストリームとしても、バンドルされているイベント バッチとしても使用できます。 このサービスは 1 つのソリューションで、リアルタイム処理用の高速なデータ取得と、格納された生データの繰り返し再生の両方を可能にします。

次の特性があります。

* 待ち時間の短縮
* 1 秒あたり数百万のイベントを受信および処理可能

### <a name="service-bus"></a>Service Bus

Service Bus は、従来のエンタープライズ アプリケーションを対象としています。 これらのエンタープライズ アプリケーションは、トランザクション、順序付け、重複の検出、瞬間的な整合性を必要とします。 Service Bus を使用すると、クラウドネイティブ アプリケーションでビジネス プロセスに対する信頼性の高い状態遷移管理を実現できます。 損失や重複が許されない高価値のメッセージを処理する場合は、Azure Service Bus を使用してください。 Service Bus は、ハイブリッド クラウド ソリューション全体で安全性の高い通信を容易にします。そして、既存のオンプレミス システムをクラウド ソリューションに接続できます。

次の特性があります。

* ポーリングが必要な信頼性の高い非同期メッセージ配信 (サービスとしてのエンタープライズ メッセージング)
* 高度なメッセージング機能 (FIFO、バッチ処理/セッション、トランザクション、配信不能処理、一時的制御、ルーティングとフィルタリング、重複検出など)

## <a name="use-the-services-together"></a>サービスを組み合わせて使用する

場合によっては、異なる役割を実行するために複数のサービスを並行して使用します。 たとえば、eコマース サイトでは、注文の処理に Service Bus、サイトのテレメトリのキャプチャに Event Hubs、イベント (アイテムの出荷など) への応答に Event Grid を使用できます。

別のケースでは、これらのサービスをリンクしてイベントとデータのパイプラインを形成することもできます。 Event Grid を使用して、他のサービスのイベントに応答することもできます。 Event Grid と Event Hubs を使用してデータをデータ ウェアハウスに移行する例については、「[ビッグ データをデータ ウェアハウスにストリーミングする](event-grid-event-hubs-integration.md)」を参照してください。 次の図は、データ ストリーミングのワークフローを示しています。

![データ ストリーミングの概要](./media/compare-messaging-services/overview.png)

## <a name="next-steps"></a>次のステップ

* Azure のメッセージング サービスの詳細については、ブログ記事「[Events, Data Points, and Messages - Choosing the right Azure messaging service for your data (イベント、データ ポイント、メッセージ - データに適した Azure メッセージング サービスの選択)](https://azure.microsoft.com/blog/events-data-points-and-messages-choosing-the-right-azure-messaging-service-for-your-data/)」を参照してください。
* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Grid の概要については、[Event Grid を使用したカスタム イベントの作成とルーティング](custom-event-quickstart.md)に関するページを参照してください。
* Event Hubs の概要については、「[Azure Portal を使用して Event Hubs 名前空間とイベント ハブを作成する](../event-hubs/event-hubs-create.md)」を参照してください。
* Service Bus の概要については、「[Azure Portal を使用して Service Bus 名前空間を作成する](../service-bus-messaging/service-bus-create-namespace-portal.md)」を参照してください。