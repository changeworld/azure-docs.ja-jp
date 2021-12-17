---
title: Kubernetes 上の Azure Event Grid - 概要
description: この記事では、Azure Arc を使用して Kubernetes 上で Event Grid を使用することに関する概要について説明します。
author: jfggdl
ms.author: jafernan
ms.subservice: kubernetes
ms.date: 05/25/2021
ms.topic: overview
ms.openlocfilehash: 109c9248bf087cb998fd8debdc644de8cfb1488b
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714907"
---
# <a name="event-grid-on-kubernetes-with-azure-arc-preview---overview"></a>Azure Arc を使用した Kubernetes 上の Event Grid (プレビュー) - 概要
この記事では、Kubernetes 上の Event Grid の概要、使用方法のユース ケース、提供されている機能、および Azure Event Grid との違いについて説明します。

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]

## <a name="what-is-event-grid"></a>Event Grid とは
Event Grid は、イベント駆動型のアーキテクチャを使用するワークロードを統合するために使用するイベント ブローカーです。 イベント駆動型のアーキテクチャでは、システム状態の変化をイベントを使用して通知します。これは、マイクロサービスなどが使用されている分離されたアーキテクチャにおける、一般的な統合アプローチです。 Event Grid では、イベントがサブスクライバーに送信 (プッシュ) されても、そのサブスクライバーがイベントを送信しているパブリッシャーを認識している必要はない、Pub-Sub (Push-Push とも呼ばれる) という通信モデルが提供されています。 このモデルは、クライアントによってメッセージ ブローカーからメッセージがプルされる、Azure Service Bus または Azure Event Hubs で使用されている従来の Push-Pull 型のモデルとは対照的です。そのため、メッセージ ブローカーと消費クライアント間の結合はより強固です。

Event Grid には、2 つのエディションが提供されています。**Azure Event Grid** は Azure 上で利用できるフル マネージドの PaaS サービスで、Azure Arc を使用した Kubernetes 上の Event Grid を使用すると、オンプレミスやクラウドを問わず、Kubernetes クラスターがデプロイできる任意の場所で Event Grid を使用することができます。 

この記事ではわかりやすくするために、使用するエディションにかかわらず、一般的なサービス機能を指す場合には「**Event Grid**」という用語を使用します。 また Azure でホストされているマネージド サービスを指す場合は、「**Azure Event Grid**」と表記します。 簡潔にするため、「**Azure Arc を使用した Kubernetes 上の Event Grid**」についても、「**Kubernetes 上の Event Grid**」と表記します。

使用する Event Grid のエディションにかかわらず、Event Grid にイベントを送信する **イベント パブリッシャー** と、Event Grid によって配信されたイベントを受信するエンドポイントを公開する 1 つ以上の **イベント サブスクライバー** が要素としてあります。 Event Grid に発行するすべてのイベントを、すべてのイベント サブスクライバーに配信する必要はありません。 Event Grid では、**イベント サブスクリプション** で定義されている一連の構成設定を使用して、特定の宛先にルーティングする必要があるイベントを選択できます。 イベント サブスクリプションのフィルター機能を使用して、特定のイベントを 1 つのエンドポイントにルーティングしたり、複数のエンドポイントにマルチキャストしたりすることができます。 Event Grid は、再試行ロジックを備えた信頼性の高い配信メカニズムも提供します。 Event Grid はオープン標準にも準拠しており、[Cloud Events 1.0 スキーマ仕様](https://github.com/cloudevents/spec/blob/master/spec.md)をサポートしています。


## <a name="event-grid-on-kubernetes-with-azure-arc"></a>Azure Arc を使用した Kubernetes 上の Event Grid
Azure Arc を使用した Kubernetes 上の Event Grid は、独自の Kubernetes クラスターで Event Grid を実行できるオファリングです。 この機能は、[Azure Arc 対応 Kubernetes](../../azure-arc/kubernetes/overview.md) を使用すると有効になります。 Azure Arc 対応 Kubernetes を使用すると、[サポートされている Kubernetes クラスター](install-k8s-extension.md#supported-kubernetes-distributions)が Azure に接続されます。 接続したら、そこに [Event Grid をインストール](install-k8s-extension.md)することができます。 

### <a name="use-case"></a>使用事例
Kubernetes 上の Event Grid では、さまざまなイベント駆動型の統合シナリオがサポートされています。 ただし、ユーザー ストーリーとしてサポートされる、これを包含する主なシナリオは次の通りです。

「Kubernetes クラスターにデプロイしたシステムの所有者として、イベントを発行し、そのイベントのルーティングを構成することで、システムの状態変化を伝達したいと考えています。これは、私の管理下にあるか否かに関わらず、イベント ハンドラーでシステムのイベントを適切な方法で処理できるようにするためです。」

上記の要件を満たすために役立つ **機能** が、[Event Grid トピック](/rest/api/eventgrid/version2021-06-01-preview/topics)です。

### <a name="event-grid-on-kubernetes-at-a-glance"></a>Kubernetes 上の Event Grid の概要
ユーザーの視点から見ると、Kubernetes 上の Event Grid は、青字で記載されている以下のリソースで構成されています。

:::image type="content" source="./media/overview/event-grid-topics.png" alt-text="リソース" lightbox="./media/overview/event-grid-topics.png":::

* **トピック** は、パブリッシャーから Event Grid にイベントを送信するためのエンドポイントを公開する入力チャネルの一種です。
* **イベント サブスクリプション** は、イベントの送信先となる宛先へのイベントをフィルター処理してルーティングするための構成設定が含まれるリソースです。
* **イベント** は、状態変更を示すアナウンスです。
* **イベント ハンドラー** は、イベントを受信し、何らかの方法でイベントをに対応したり処理するアプリケーションまたはサービスです。 イベント ハンドラーは、**イベント サブスクライバー** と呼ばれることもあります。 上の図におけるイベント ハンドラーは、Kubernetes クラスター (K8) と Azure Service Bus サービスにデプロイされた API です。

これらの概念の詳細については、「[Azure Event Grid の概念](concepts.md)」を参照してください。

### <a name="sample-workload-integration-scenarios-and-destinations"></a>ワークロードの統合シナリオのサンプルと送信先

クラスターで実行されているワークロードを統合できます。 パブリッシャーには、Kubernetes クラスターで実行されている任意のサービスや、パブリッシャーのイベントの送信先となるトピック エンドポイント (Event Grid ブローカーによってホストされる) にアクセスできる任意のワークロードが指定できます。

:::image type="content" source="./media/overview/event-grid-intra-cluster-integration.png" alt-text="クラスター内統合" lightbox="./media/overview/event-grid-intra-cluster-integration.png":::


また、Kubernetes クラスターのいずれかにデプロイされた Event Grid にイベントを送信する、ネットワーク内の他の場所にパブリッシャーをデプロイすることもできます。

:::image type="content" source="./media/overview/event-grid-in-network-integration.png" alt-text="ネットワーク内統合" lightbox="./media/overview/event-grid-in-network-integration.png":::

Kubernetes で Event Grid を使用すると、処理、保管、または視覚化を行うため、イベントを Azure に転送することができます。

:::image type="content" source="./media/overview/event-grid-forward-events.png" alt-text="イベントを Azure に転送する":::

#### <a name="destinations"></a>変換先
イベント ハンドラーの送信先には、パブリックまたはプライベートのネットワークを介して Event Grid からアクセスでき、アクセス権があれば (認証メカニズムによって保護されていない)、任意の HTTPS または HTTP のエンドポイントを指定できます。 イベントの送信先は、イベント サブスクリプションを作成するときに定義します。 詳細は、[イベント ハンドラー](event-handlers.md)に関する記事を参照してください｡ 

## <a name="features"></a>機能
Kubernetes 上の Event Grid では、[Azure Event Grid](../custom-topics.md) で提供されている機能である [Event Grid トピック](/rest/api/eventgrid/version2021-06-01-preview/topics)もサポートされています。 Event Grid トピックは、ユーザーのシステムと、ユーザーが所有する、またはそのシステムからアクセス可能な別のワークロードとの統合が必要になる、[主要な統合ユース ケース](#use-case)の実現をサポートします。

Kubernetes 上の Azure Event Grid で利用できる機能の一部は次のとおりです。

* **[イベント フィルター](filter-events.md)** - イベントの種類、イベントの件名、イベント データに基づいてフィルター処理することで、イベント ハンドラーが関連するイベントのみを受け取るようにできます。
* **ファンアウト**: 複数のエンドポイントを同じイベントにサブスクライブし、複数の場所にイベントのコピーを送信できます。
* **オープン標準準拠**: CNCF の [Cloud Events 1.0 スキーマ仕様](https://github.com/cloudevents/spec/blob/master/spec.md)を使用して、イベントを定義します。
* **信頼性**: Event Grid には、イベントが確実に送信先に届くようにするイベント送信の再試行ロジックが備わっています。

詳細については、[Kubernetes 上の Event Grid でサポートされる機能](features.md)に関するページを参照してください。

## <a name="pricing"></a>価格 
Azure Arc を使用した Kubernetes 上の Event Grid は、プレビュー バージョン中は無料でご利用いただけます。

## <a name="next-steps"></a>次の手順
Kubernetes 上の Event Grid を使用してイベントのルーティングを開始するには、以下の手順に従います。

1. [Azure Arc にクラスターを接続する](../../azure-arc/kubernetes/quickstart-connect-cluster.md)。
1. [Event Grid 拡張機能をインストール](install-k8s-extension.md)します。これは、Kubernetes クラスターに Event Grid をデプロイする実際のリソースです。 拡張機能の詳細については、「[Event Grid 拡張機能](install-k8s-extension.md#event-grid-extension)」セクションを参照してください。 
1. [カスタムの場所を作成する](../../azure-arc/kubernetes/custom-locations.md)。 カスタムの場所はクラスター内の名前空間を表し、トピックとイベント サブスクリプションをデプロイする場所となります。
1. [トピックと 1 つ以上のサブスクリプションを作成します](create-topic-subscription.md)。
1. [イベントを発行します](create-topic-subscription.md)。

使用できるその他のリソースは次のとおりです。

* [データ プレーン SDK](../sdk-overview.md#data-plane-sdks)。
* [データ プレーン SDK を使用してイベントの例を発行する](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/)。
* [Event Grid CLI](/cli/azure/eventgrid)。
* [Management SDK](../sdk-overview.md#management-sdks)。
