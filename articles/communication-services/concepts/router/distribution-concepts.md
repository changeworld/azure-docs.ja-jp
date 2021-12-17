---
title: Azure Communication Services のジョブ分散に関する概念
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services ジョブ ルーターの分散に関する概念について説明します。
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: fdc64b5d789d017e6fcb8aab2cd48103235535ec
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319246"
---
# <a name="job-distribution-concepts"></a>ジョブ分散に関する概念

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Azure Communication Services のジョブ ルーターでは、柔軟な分散プロセスを使用します。このプロセスでは、ポリシーとジョブ オファーのライフサイクルを使用して worker を割り当てる必要があります。 この記事では、ジョブを配布するさまざまな方法、ジョブ オファー のライフサイクルとは何か、およびこのプロセスが worker に与える影響について説明します。

## <a name="job-distribution-overview"></a>ジョブ分散の概要

ジョブを worker に配布する方法を決定することは、ジョブ ルーターの重要な機能であり、SDK では、環境をカスタマイズするための同様に柔軟で拡張可能なモデルが提供されます。 [分類に関する概念](classification-concepts.md)ガイドで説明しているとおり、ジョブが分類されると、ジョブ ルーターでは、ジョブの特性と分散ポリシーに基づいて適切な worker を探します。 または、worker がビジーの場合、ジョブ ルーターは、worker が対応可能になったときに適切なジョブを探します。 worker の適合性は、3 つの特性 ([利用可能なチャネル](#channel-configurations)、worker の[能力、](#worker-abilities)、[状態](#worker-status)) で決定されます。 適切な worker が見つかると、ジョブを割り当てることができるオープン チャネルがあることを確認するためのチェックが実行されます。

これらの 2 つのアプローチは、ジョブ ルーターがジョブまたは worker の検出を開始する方法における重要な概念です。

### <a name="finding-workers-for-a-job"></a>ジョブに適した worker の検出

ジョブで[分類プロセス](classification-concepts.md)が完了すると、ジョブ ルーターにより、キューで構成された分散ポリシーが適用され、ジョブに関する worker セレクターを満たす 1 人以上の worker が選択され、ジョブを引き受ける worker に対するオファーが生成されます。 

### <a name="finding-a-job-for-a-worker"></a>worker に適したジョブの検出

ジョブ ルーターによる worker に適したジョブの検出をトリガーするシナリオがいくつかあります。

- worker がジョブ ルーターに登録されたとき
- ジョブが閉じられ、チャネルが解放されたとき
- ジョブ オファーが拒否されるか、または取り消されたとき

分散プロセスは、ジョブに対する worker の検出と同じです。 worker が見つかると、[オファー](#job-offer-overview)が生成されます。

## <a name="worker-overview"></a>worker の概要

worker をジョブ ルーターに **登録する** には、SDK を使用し、次の基本情報を指定します。

- worker の ID と名前
- キュー ID
- 合計容量 (数値)
- **チャネル構成** の一覧
- ラベルのセット 

ジョブ ルーターでは、登録された worker が手動または自動で **登録解除** された場合でも、すべての登録済み worker への参照を常に保持します。

### <a name="channel-configurations"></a>チャネル構成

各ジョブには、事前構成されたジョブ ルーター チャネルまたはカスタム チャネルを表すチャネル ID プロパティが必要です。 チャネル構成は、文字列 `channelId` と数値 `capacityCostPerJob` で構成されます。 これらを合わせて、通信の抽象モードとそのモードのコストを表します。 たとえば、ほとんどのユーザーは一度に 1 回の通話しか行えないため、`Voice` チャネルは `100` という高いコストになる可能性があります。 また、チャットなどの特定のワークロードはコンカレンシーが高くなる可能性があります。これは、コストが低くなることを意味します。 チャネル構成は、ジョブを割り当てたり接続したりできる空きスロットと考えることができます。 この点について次の例で説明します。

```csharp
await client.RegisterWorkerAsync(
    id: "EdmontonWorker",
    queueIds: new[] { "XBOX_Queue", "XBOX_Escalation_Queue" },
    totalCapacity: 100,
    labels: new LabelCollection
    {
        { "Location", "Edmonton" },
        { "XBOX_Hardware", 7 },
    },
    channelConfigurations: new List<ChannelConfiguration>
    {
        new (
            channelId: ManagedChannels.AcsVoiceChannel,
            capacityCostPerJob: 100
        ),
        new (
            channelId: ManagedChannels.AcsChatChannel,
            capacityCostPerJob: 33
        )
    }
);
```

上記の worker は、それぞれのチャネルが固有のコストを持つ 2 つのチャネル構成に登録されています。 効果的な結果として、`EdmontonWorker` では、3 つの `ManagedChannels.AcsChatChannel` 同時実行ジョブ、または 1 つの `ManagedChannels.AcsVoiceChannel` ジョブを処理できます。

ジョブ ルーターには、使用可能な次の事前構成済みチャネル ID が含まれています。

- ManagedChannels.AcsChatChannel
- ManagedChannels.AcsVoiceChannel
- ManagedChannels.AcsSMSChannel

新しい抽象チャネルは、次のように、ジョブ ルーター SDK を使用して作成することができます。

```csharp
await client.SetChannelAsync(
    id: "MakePizza",
    name: "Make a pizza"
);

await client.SetChannelAsync(
    id: "MakeDonairs",
    name: "Make a donair"
);

await client.SetChannelAsync(
    id: "MakeBurgers",
    name: "Make a burger"
);
```

その後、worker を登録するときにチャネルを使用して、次のように、そのチャネル ID に一致するジョブを引き受ける能力を表すことができます。

```csharp
await client.RegisterWorkerAsync(
    id: "PizzaCook",
    queueIds: new[] { "PizzaOrders", "DonairOrders", "BurgerOrders" },
    totalCapacity: 100,
    labels: new LabelCollection
    {
        { "Location", "New Jersey" },
        { "Language", "English" },
        { "PizzaMaker", 7 },
        { "DonairMaker", 10},
        { "BurgerMaker", 5}
    },
    channelConfigurations: new List<ChannelConfiguration>
    {
        new (
            channelId: MakePizza,
            capacityCostPerJob: 50
        ),
        new (
            channelId: MakeDonair,
            capacityCostPerJob: 33
        ),
        new (
            channelId: MakeBurger,
            capacityCostPerJob: 25        
        )
    }
);
```

上記の例は、それぞれがジョブあたりの独自のコストを持つ 3 つの抽象チャネルを示しています。 そのため、`PizzaCook` worker では、次のジョブ コンカレンシーの例が可能になります。

| MakePizza | MakeDonair | MakeBurger | スコア |
|--|--|--|--|
| 2         |            |            | 100   |
|           | 3          |            | 99    |
| 1         | 1          |            | 83    |
|           | 2          | 1          | 91    |
|           |            | 4          | 100   |
|           | 1          | 2          | 83    |

### <a name="worker-abilities"></a>worker の能力

worker が使用できる可能性のあるチャネルとは別に、分散プロセスでは、登録済み worker のラベル コレクションを使用して、ジョブへの適合性が判断されます。 PizzaCook の例では、worker のラベル コレクションは、次のもので構成されます。

```csharp
new LabelCollection
    {
        { "Location", "New Jersey" },
        { "Language", "English" },
        { "PizzaMaker", 7 },
        { "DonairMaker", 10},
        { "BurgerMaker", 5}
    }
```

ジョブが送信されると、**worker セレクター** を使用して、その特定の作業単位の要件が定義されます。 ドネルを作るのが得意で英語を話せる人を必要とするジョブの場合、SDK の呼び出しは次のようになります。

```csharp
await client.CreateJobAsync(
    channelId: "MakeDonair",
    channelReference: "ReceiptNumber_555123",
    queueId: "DonairOrders",
    priority: 1,
    workerSelectors: new List<LabelSelector>
    {
        new (
            key: "DonairMaker",
            @operator: LabelOperator.GreaterThanEqual,
            value: 8),
        new (
            key: "English",
            @operator: LabelOperator.GreaterThan,
            value: 5)
    });
```

### <a name="worker-status"></a>worker の状態

ジョブ ルーターでは、worker のチャネル構成に応じて、その worker の同時実行ジョブを処理できるため、可用性の概念は、次の 3 つの状態で表されます。

**アクティブ** - worker はジョブ ルーターに登録されており、ジョブを受け入れる用意がある

**ドレイン中** - worker はジョブ ルーターから登録解除されたが、現在 1 つ以上のアクティブなジョブが割り当てられている

**非アクティブ** - worker はジョブ ルーターから登録解除されており、アクティブなジョブはない

## <a name="job-offer-overview"></a>ジョブ オファーの概要

分散プロセスで、オープン チャネルがあり、適切な状態である適切な worker が見つかると、ジョブ オファーが生成され、イベントが送信されます。 分散ポリシーには、オファーに関する次の構成可能なプロパティが含まれます。

**OfferTTL** - 生成された各オファーの有効期限

**Mode** - `minConcurrentOffers`と `maxConcurrentOffers` の両方のプロパティを含む **分散モード**。

> [!Important]
> worker に対してジョブ オファーが生成されると、ジョブのチャネル ID と一致するチャネル構成の 1 つが使用されます。 このチャネルの使用は、worker でそのチャネルの追加容量が使用可能である場合を除いて、worker が別のオファーを受け取らないことを意味します。 worker がオファーを拒否するか、オファーの有効期限が切れると、チャネルは解放されます。

### <a name="job-offer-lifecycle"></a>ジョブ オファーのライフサイクル

Event Grid サブスクリプションを使用して監視できるジョブ オファーのライフサイクル イベントは、次のとおりです。

- RouterWorkerOfferIssued
- RouterWorkerOfferAccepted
- RouterWorkerOfferDeclined
- RouterWorkerOfferExpired
- RouterWorkerOfferRevoked

> [!NOTE]
> オファーは、worker が SDK を使用して受け入れるか、または拒否することができますが、他のすべてのイベントは内部で生成されます。

## <a name="distribution-modes"></a>分散モード

ジョブ ルーターの分散モードとしては、次のものがあります。

**LongestIdleMode** - キュー内で最も長くアイドル状態である worker に対してオファーを生成します

**RoundRobinMode** - worker のコレクションを指定して、ID 順に選択された最後の worker の次の worker を選択します。

**BestWorkerMode** - ジョブ ルーターの [RuleEngine](router-rule-concepts.md) を使用し、ラベルに基づいて worker を選択します

## <a name="distribution-summary"></a>分散のまとめ

worker の状態、チャネルの構成または容量、分散ポリシーのモード、オファーのコンカレンシーなどのいくつかの要因によっては、ジョブ オファーの生成方法に影響を与える可能性があります。 単純な実装から始めて、要件に応じて複雑にしていくことをお勧めします。
