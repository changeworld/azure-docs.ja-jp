---
title: Azure リソースのレプリケーション タスクを作成する
description: Azure Logic Apps のワークフローに基づいて、レプリケーション タスク テンプレートを使って Azure リソースをレプリケートします。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 42364c1c1fc0f9d2d220b4b9156a4a7ab4a410fa
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337401"
---
# <a name="create-replication-tasks-for-azure-resources-using-azure-logic-apps-preview"></a>Azure Logic Apps を使って Azure リソースのレプリケーション タスクを作成する (プレビュー)

> [!IMPORTANT]
> この機能はプレビュー段階にあり、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」が適用されます。

最大の可用性と信頼性は Azure サービスの運用上の最優先事項ですが、それでもなお、ネットワークや名前解決の問題、エラー、または一時的な無応答のために通信が停止する状況が多数あります。 このような状況は、ディザスター リカバリーの状況で行われる場合があるリージョンのデプロイの完全な破棄が必要な "壊滅的" なものではありません。 しかし、一部のアプリのビジネス シナリオが、長くて数分、または数秒さえ続かない可用性イベントによって影響を受ける可能性があります。

予測できないイベントが Azure リージョン内の Azure リソースに与える影響を軽減するために、これらのリソース内のコンテンツをあるリージョンから別のリージョンにレプリケートして、ビジネスの継続性を維持することができます。 Azure では、あるリージョンのソースから別のリージョンのターゲットにデータ、イベント、またはメッセージを移動する [*レプリケーション タスク*](#replication-task)を作成できます。 そうすることで、ソースがオフラインになってターゲットが引き継がなければならない場合に、ターゲットをすぐに利用できます。

> [!NOTE]
> 同じリージョン内のエンティティ間でコンテンツを移動させるためにレプリケーション タスクを使用することもできますが、リージョン全体が利用できなくなったり、中断が発生したりすると、ソースとターゲットの両方が影響を受けます。

この記事では、Azure Logic Apps を利用したレプリケーション タスクの概要を説明し、Azure Service Bus のキューに対するサンプル レプリケーション タスクを作成する方法を紹介します。 ロジック アプリやワークフローを始めて使用する場合は、「[Azure Logic Apps とは](logic-apps-overview.md)」と「[Azure Logic Apps でのシングルテナント、マルチテナント、統合サービス環境の比較](single-tenant-overview-compare.md)」を確認してください。

<a name="replication-task"></a>

## <a name="what-is-a-replication-task"></a>レプリケーション タスクとは

一般に、レプリケーション タスクは、ソースからデータ、イベント、またはメッセージを受け取り、そのコンテンツをターゲットに移動させた後、ソースからそのコンテンツを削除します。ただし、ソースが Event Hubs エンティティの場合を除きます。 レプリケーション タスクは、通常、コンテンツを変更せずに移動しますが、Azure Logic Apps を利用したレプリケーション タスクは[レプリケーション プロパティ](#replication-properties)も追加します。 ソースとターゲットのプロトコルが異なる場合、これらのタスクはメタデータ構造間のマッピングも実行します。 レプリケーションタスクはステートレスであり、タスクの順次実行または並列実行で状態やその他の副作用を共有することはありません。

利用可能なレプリケーション タスク テンプレートを使用すると、作成した各レプリケーション タスクには、基になる [ステートレス ワークフロー](single-tenant-overview-compare.md#stateful-stateless)が **ロジック アプリ (Standard)** リソースに作成され、レプリケーション タスクの複数のワークフローを含めることができます。 このリソースは、シングルテナントの Azure Logic Apps でホストされます。これは、レプリケーションやフェデレーションのタスクを含むサーバーレス アプリケーションを構成して実行するための、スケーラブルで信頼性の高い実行環境です。 また、シングルテナントの Azure Logic Apps ランタイムは [Azure Functions 機能拡張モデル](../azure-functions/functions-bindings-register.md)を使用しており、Azure Functions ランタイムの拡張機能としてホストされます。 この設計により、ロジック アプリ ワークフローの移植性、柔軟性、パフォーマンス向上に加え、Azure Functions プラットフォームと Azure App Service エコシステムから継承されたその他の機能と利点が提供されます。

レプリケーションとフェデレーションの詳細については、次のドキュメントを参照してください。

- [Event Hubs のマルチサイトとマルチリージョンのフェデレーション](../event-hubs/event-hubs-federation-overview.md)
- [イベント レプリケーション タスクのパターン](../event-hubs/event-hubs-federation-patterns.md)
- [Service Bus メッセージ レプリケーションとリージョン間フェデレーション](../service-bus-messaging/service-bus-federation-overview.md)
- [メッセージ レプリケーション タスクのパターン](../service-bus-messaging/service-bus-federation-patterns.md)

<a name="replication-task-templates"></a>

## <a name="replication-task-templates"></a>レプリケーション タスク テンプレート

現在、レプリケーション タスク テンプレートは [Azure Event Hubs](../event-hubs/event-hubs-about.md) と [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) で使用できます。 次の表に、このプレビューで現在使用できるレプリケーション タスク テンプレートを示します。

| リソースの種類 | レプリケーションのソースとターゲット。 |
|---------------|-------------------------------|
| Azure Event Hubs 名前空間 | - Event Hubs インスタンスから Event Hubs インスタンスへ <br>- Event Hubs インスタンスから Service Bus キューへ <br>- Event Hubs インスタンスから Service Bus トピックへ |
| Azure Service Bus 名前空間 | - Service Bus キューから Service Bus キューへ <br>- Service Bus キューから Service Bus トピックへ <br>- Service Bus トピックから Service Bus トピックへ <br>- Service Bus キューから Event Hubs インスタンスへ <br>- Service Bus トピックから Service Bus キューへ <br>- Service Bus トピックから Event Hubs インスタンスへ |
|||

### <a name="replication-topology-and-workflow"></a>レプリケーション トポロジとワークフロー

Azure Logic Apps (Standard) を利用したレプリケーション タスクのしくみを視覚化するために、次の図では、Event Hubs インスタンスと Service Bus キューのレプリケーション タスクの構造とワークフローを示しています。

#### <a name="replication-topology-for-event-hubs"></a>Event Hubs のレプリケーション トポロジ

次の図は、Event Hubs インスタンス間のトポロジとレプリケーション タスクのワークフローを示しています。

![Event Hubs インスタンス間の "ロジック アプリ (Standard)" ワークフローを利用したレプリケーション タスクのトポロジを示す概念図。](media/create-replication-tasks-azure-resources/replication-topology-event-hubs.png)

Azure Event Hubs のレプリケーションとフェデレーションについては、次のドキュメントを参照してください。

- [Event Hubs のマルチサイトとマルチリージョンのフェデレーション](../event-hubs/event-hubs-federation-overview.md)
- [イベント レプリケーション タスクのパターン](../event-hubs/event-hubs-federation-patterns.md)

#### <a name="replication-topology-for-service-bus"></a>Service Bus のレプリケーション トポロジ

次の図は、Service Bus キュー間のトポロジとレプリケーション タスクのワークフローを示しています。

![Service Bus キュー間の "ロジック アプリ (Standard)" ワークフローを利用したレプリケーション タスクのトポロジを示す概念図。](media/create-replication-tasks-azure-resources/replication-topology-service-bus-queues.png)

Azure Service Bus のレプリケーションとフェデレーションについては、次のドキュメントを参照してください。

- [Service Bus メッセージ レプリケーションとリージョン間フェデレーション](../service-bus-messaging/service-bus-federation-overview.md)
- [メッセージ レプリケーション タスクのパターン](../service-bus-messaging/service-bus-federation-patterns.md)

<a name="replication-properties"></a>

## <a name="metadata-and-property-mappings"></a>メタデータとプロパティのマッピング

Event Hubs の場合、ソース Event Hubs の名前空間から取得された項目 (イベントのサービス割り当てメタデータ、元のエンキュー時間、シーケンス番号、オフセット) は、ターゲット Event Hubs の名前空間の新しいサービス割り当て値に置き換えられます。 ただし、[ヘルパー関数](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication)と Azure 提供サンプルのレプリケーション タスクの場合、元の値はユーザー プロパティ ( `repl-enqueue-time` (ISO8601 文字列)、`repl-sequence`、`repl-offset`) に保持されます。 これらのプロパティは `string` 型であり、それぞれの元のプロパティの文字列化された値が含まれています。 複数回イベントが転送される場合は、直接のソースのサービスによって割り当てられたメタデータが既存のプロパティに追加され、値はセミコロンで区切られます。 詳細については、[サービス割り当てメタデータ - イベント レプリケーション タスクのパターン](../event-hubs/event-hubs-federation-patterns.md#service-assigned-metadata)に関する記事を参照してください。

Service Bus の場合、ソース Service Bus キューまたはトピックから取得された項目 (メッセージのサービス割り当てメタデータ、元のエンキュー時間、シーケンス番号) は、ターゲット Service Bus キューまたはトピック内の新しいサービス割り当て値に置き換えられます。 ただし、Azure 提供サンプルの既定のレプリケーション タスクの場合、元の値はユーザー プロパティ (`repl-enqueue-time` (ISO8601 文字列) と `repl-sequence`) に保持されます。 これらのプロパティは `string` 型であり、それぞれの元のプロパティの文字列化された値が含まれています。 複数回メッセージが転送される場合は、直接のソースのサービスによって割り当てられたメタデータが既存のプロパティに追加され、値はセミコロンで区切られます。 詳細については、[サービス割り当てメタデータ - メッセージ レプリケーション タスクのパターン](../service-bus-messaging/service-bus-federation-patterns.md#service-assigned-metadata)に関する記事を参照してください。

タスクが Service Bus から Event Hubs にレプリケートする場合、そのタスクは `User Properties` プロパティのみを `Properties` プロパティにマップします。 ただし、タスクが Event Hubs から Service Bus にレプリケートする場合、そのタスクは次のプロパティをマップします。

| Event Hubs から | Service Bus へ |
|-----------------|----------------|
| ContentType | ContentType |
| CorrelationId | CorrelationId |
| MessageId | MessageId |
| パーティション キー | PartitionKey SessionId |
| Properties | ユーザー プロパティ |
| ReplyTo | ReplyTo |
| ReplyToGroupName | ReplyToSessionId |
| サブジェクト | ラベル |
| ターゲット | ターゲット |
|||

<a name="order-preservation"></a>

## <a name="order-preservation"></a>順序の維持

Event Hubs の場合、同じ数の[パーティション](../event-hubs/event-hubs-features.md#partitions)間でレプリケーションを行うと、イベントに変更のない 1 対 1 の複製が作成されますが、重複も含まれる可能性があります。 しかし、異なる数のパーティション間でレプリケーションを行うと、パーティション キーに基づいてイベントの相対的な順序のみが維持されますが、重複も含まれる可能性があります。 詳細については、「[ストリームと順序の維持](../event-hubs/event-hubs-federation-patterns.md#streams-and-order-preservation)」を参照してください。

Service Bus の場合、セッションを有効にして、ソースから取得されたものと同じセッション ID を持つメッセージ シーケンスが、同じセッション ID で元のシーケンスのバッチとしてターゲット キューまたはトピックに送信されるようにする必要があります。 詳細については、「[シーケンスと順序の維持](../service-bus-messaging/service-bus-federation-patterns.md#sequences-and-order-preservation)」を参照してください。

レプリケーション タスクを作成できる Azure サービスのマルチサイトおよびマルチリージョン フェデレーションの詳細については、次のドキュメントを参照してください。

- [Event Hubs のマルチサイトとマルチリージョンのフェデレーション](../event-hubs/event-hubs-federation-overview.md)
- [イベント レプリケーション タスクのパターン](../event-hubs/event-hubs-federation-patterns.md)
- [Service Bus メッセージ レプリケーションとリージョン間フェデレーション](../service-bus-messaging/service-bus-federation-overview.md)
- [メッセージ レプリケーション タスクのパターン](../service-bus-messaging/service-bus-federation-patterns.md)

<a name="pricing"></a>

## <a name="pricing"></a>価格

内部的には、レプリケーション タスクは、シングルテナントの Azure Logic Apps でホストされている **ロジック アプリ (Standard)** リソースのステートレス ワークフローで動作します。 このレプリケーション タスクを作成すると、すぐに課金が開始されます。 使用量、使用量計測、課金、価格モデルは、 [Standard ホスティング プラン](logic-apps-pricing.md#standard-pricing)と [Standard プランの価格レベル](logic-apps-pricing.md#standard-pricing-tiers)に従います。

<a name="scale-up"></a>

Event Hubs が受信するイベントや Service Bus が処理するメッセージの数に基づいて、ホスティング プランでは、アクティブなレプリケーション中に最小限の vCPU 使用率と低遅延を維持するために、スケールアップまたはスケールダウンが行われる場合があります。 この動作では、レプリケーション タスクに使用するロジック アプリ リソースを作成するとき、Azure Logic Apps が CPU 使用率の調整や最大化を開始せず、高速なレプリケーション速度を保証できるように、[適切な Standard プランの価格レベルを選ぶ](#scale-out)必要があります。

> [!NOTE]
> アプリが WS1 プランの 1 インスタンスから始まり、2 インスタンスにスケールアウトした場合、プランを終日実行したと仮定すると、コストが WS1 の 2 倍になります。 アプリを WS2 プランにスケールアップし、1 インスタンスを使用した場合のコストは、実質上、WS1 プランの 2 インスタンスと同じです。 同様に、アプリを WS3 プランにスケールアップして 1 インスタンスを使用した場合、コストは実質上、WS2 プランの 2 インスタンスまたは WS1 プランの 4 インスタンスと等しくなります。

<a name="scale-out"></a>

以降の例では、シナリオ (Event Hubs または Service Bus) とさまざまな構成の値に基づき、特定のレプリケーション タスクのシナリオに対してスループットとコストが最良となるホスティング プランの価格レベルと構成オプションを示しています。

> [!NOTE]
> 以降のセクションの例では、イベントまたはメッセージのサイズが 1 KB であると仮定し、プリフェッチ数、Event Hubs の最大イベント バッチ サイズ、Service Bus の最大メッセージ数の既定値に 800 を使用しています。 イベントのサイズによっては、プリフェッチ数、最大イベント バッチ サイズ、最大メッセージ数を調整してもかまいません。 たとえば、イベント サイズまたはメッセージ サイズが 1 KB を超えるような場合は、プリフェッチ数、最大イベント バッチ サイズ、メッセージ数を 800 より小さくした方がいいでしょう。

### <a name="event-hubs-scale-out"></a>Event Hubs スケールアウト

以降の例では、"*同じリージョン内*" の 2 つの Event Hubs 名前空間の間で行われるレプリケーション タスクについて、[パーティション](../event-hubs/event-hubs-features.md#partitions)数、1 秒あたりのイベント数、その他の構成の値に基づき、ホスティング プランの価格レベルと構成オプションを示しています。

このセクションの例では、イベント サイズが 1 KB であると仮定し、プリフェッチ数と最大イベント バッチ サイズの既定値として 800 を使用しています。 イベントのサイズによっては、プリフェッチ数と最大イベント バッチ サイズを調整してもかまいません。 たとえば、イベント サイズが 1 KB を超えるような場合は、プリフェッチ数と最大イベント バッチ サイズを 800 より小さくした方がいいでしょう。

| Pricing tier | [パーティション数] | 1 秒あたりのイベント数 | 最大バースト数* | 常時使用可能なインスタンス* | プリフェッチ数* | 最大イベント バッチ サイズ* |
|--------------|-----------------|-------------------|----------------|-------------------------|-----------------|-----------------|
| **WS1** | 1 | 1000 | 1 | 1 | 800 | 800 |
| **WS1** | 2 | 2000 | 1 | 1 | 800 | 800 |
| **WS2** | 4 | 4000 | 2 | 1 | 800 | 800 |
| **WS2** | 8 | 8000 | 2 | 1 | 800 | 800 |
| **WS3** | 16 | 16000 | 2 | 1 | 800 | 800 |
| **WS3** | 32 | 32000 | 3 | 1 | 800 | 800 |
||||||||

\* 各価格レベルの変更可能な値の詳細については、次の表を参照してください。

| 値 | 説明 |
|-------|-------------|
| **最大バースト数** | 負荷がかかった状況下でスケールアウトするエラスティック ワーカーの "*最大*" 数。 基になるアプリに、"*常時使用可能なインスタンス数*" (この表の次の行を参照) を超えるインスタンスが必要な場合、インスタンスの数が最大バースト制限に達するまでアプリのスケールアウトを続けることができます。 この値を変更する場合は、後出の「[ホスティング プランのスケールアウト設定を編集する](#edit-plan-scale-out-settings)」を参照してください。 <p>**注**: プラン サイズを超えたインスタンスに対する課金は、それらが実行され、かつ割り当てられているときに "*のみ*"、秒あたりの単位で発生します。 アプリは定義された上限まで、プラットフォームによりベスト エフォートでスケールアウトされます。 <p>**ヒント**: 未使用のインスタンスは課金されないので、プラットフォームが必要に応じてスケールアウトして、処理できる負荷を増やせるよう、必要となりうる水準を超える最大値を選択するようお勧めします。 <p>ワークフロー Standard プランと Azure Functions Premium プランにはいくつか共通点があります。詳細については、次のドキュメントを参照してください。 <p>- [プランと SKU の設定 - Azure Functions Premium プラン](../azure-functions/functions-premium-plan.md#plan-and-sku-settings) <br>- [クラウド バーストついて](https://azure.microsoft.com/overview/what-is-cloud-bursting/) |
| **常時使用可能なインスタンス** | アプリのホストとして常時準備が整っていてウォーミングされた最小インスタンス数。 最小数は常に 1 です。 この値を変更する場合は、後出の「[ホスティング プランのスケールアウト設定を編集する](#edit-plan-scale-out-settings)」を参照してください。 <p>**注**: プラン サイズを超えたインスタンスに対する課金は、それらが割り当てられているとき、実行されているかどうかに "*かかわらず*" 発生します。 <p>ワークフロー Standard プランと Azure Functions Premium プランにはいくつか共通点があります。詳細については、次のドキュメントを参照してください。[常時使用可能なインスタンス - Azure Functions Premium プラン](../azure-functions/functions-premium-plan.md#always-ready-instances) |
| **プリフェッチ数** | ロジック アプリ リソースのアプリ設定 `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__prefetchCount` の既定値。基になる `EventProcessorHost` クラスによって使用されるプリフェッチ数を決定します。 このアプリ設定に異なる値を追加または指定するには、「[アプリ設定の管理 - local.settings.json](edit-app-settings-host-settings.md?tabs=azure-portal#manage-app-settings)」を参照してください。以下はその例です。 <p>- **名前**: `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__prefetchCount` <br>- **値**: `800` (上限なし) <p>`prefetchCount` プロパティの詳細については、次のドキュメントを参照してください。 <p>- [host.json 設定 - Azure Functions における Azure Event Hubs のトリガーとバインド](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) <br>- [EventProcessorOptions.PrefetchCount プロパティ](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount) <br>- [アプリケーションの複数のインスタンス間でパーティション負荷のバランスを取る](../event-hubs/event-processor-balance-partition-load.md) <br>- [イベント プロセッサ ホスト](../event-hubs/event-hubs-event-processor-host.md) <br>- [EventProcessorHost クラス](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) |
| **最大イベント バッチ サイズ** | ロジック アプリ リソースのアプリ設定 `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__maxBatchSize` の既定値。各受信ループで受信する最大イベント数を決定します。 このアプリ設定に異なる値を追加または指定するには、「[アプリ設定の管理 - local.settings.json](edit-app-settings-host-settings.md?tabs=azure-portal#manage-app-settings)」を参照してください。以下はその例です。 <p>- **名前**: `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__maxBatchSize` <br>- **値**: `800` (上限なし) <p>`maxBatchSize` プロパティの詳細については、次のドキュメントを参照してください。 <p>- [host.json 設定 - Azure Functions における Azure Event Hubs のトリガーとバインド](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) <br>- [EventProcessorOptions.MaxBatchSize プロパティ](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize) <br>- [イベント プロセッサ ホスト](../event-hubs/event-hubs-event-processor-host.md) |
|||

### <a name="service-bus-scale-out"></a>Service Bus スケールアウト

以降の例では、"*同じリージョン内*" の 2 つの Service Bus 名前空間の間で行われるレプリケーション タスクについて、1 秒あたりのメッセージ数およびその他の構成の値に基づき、ホスティング プランの価格レベルと構成オプションを示しています。

このセクションの例では、メッセージ サイズが 1 KB であると仮定し、プリフェッチ数と最大メッセージ数の既定値として 800 を使用しています。 メッセージのサイズによっては、プリフェッチ数と最大メッセージ数を調整してもかまいません。 たとえば、メッセージ サイズが 1 KB を超えるような場合は、プリフェッチ数と最大メッセージ数を 800 より小さくした方がいいでしょう。

| Pricing tier | 1 秒あたりのメッセージ数 | 最大バースト数* | 常時使用可能なインスタンス* | プリフェッチ数* | 最大メッセージ数* |
|--------------|---------------------|-----------------|-------------------------|-----------------|------------------------|
| **WS1** | 2000 | 1 | 1 | 800 | 800 |
| **WS2** | 2500 | 1 | 1 | 800 | 800 |
| **WS3** | 3500 | 1 | 1 | 800 | 800 |
|||||||

\* 各価格レベルの変更可能な値の詳細については、次の表を参照してください。

| 値 | 説明 |
|-------|-------------|
| **最大バースト数** | 負荷がかかった状況下でスケールアウトするエラスティック ワーカーの "*最大*" 数。 基になるアプリに、"*常時使用可能なインスタンス数*" (この表の次の行を参照) を超えるインスタンスが必要な場合、インスタンスの数が最大バースト制限に達するまでアプリのスケールアウトを続けることができます。 この値を変更する場合は、後出の「[ホスティング プランのスケールアウト設定を編集する](#edit-plan-scale-out-settings)」を参照してください。 <p>**注**: プラン サイズを超えたインスタンスに対する課金は、それらが実行され、かつ割り当てられているときに "*のみ*"、秒あたりの単位で発生します。 アプリは定義された上限まで、プラットフォームによりベスト エフォートでスケールアウトされます。 <p>**ヒント**: 未使用のインスタンスは課金されないので、プラットフォームが必要に応じてスケールアウトして、処理できる負荷を増やせるよう、必要となりうる水準を超える最大値を選択するようお勧めします。 <p>ワークフロー Standard プランと Azure Functions Premium プランにはいくつか共通点があります。詳細については、次のドキュメントを参照してください。 <p>- [プランと SKU の設定 - Azure Functions Premium プラン](../azure-functions/functions-premium-plan.md#plan-and-sku-settings) <br>- [クラウド バーストついて](https://azure.microsoft.com/overview/what-is-cloud-bursting/) |
| **常時使用可能なインスタンス** | アプリのホストとして常時準備が整っていてウォーミングされた最小インスタンス数。 最小数は常に 1 です。 この値を変更する場合は、後出の「[ホスティング プランのスケールアウト設定を編集する](#edit-plan-scale-out-settings)」を参照してください。 <p>**注**: プラン サイズを超えたインスタンスに対する課金は、それらが割り当てられているとき、実行されているかどうかに "*かかわらず*" 発生します。 <p>ワークフロー Standard プランと Azure Functions Premium プランにはいくつか共通点があります。詳細については、次のドキュメントを参照してください。[常時使用可能なインスタンス - Azure Functions Premium プラン](../azure-functions/functions-premium-plan.md#always-ready-instances) |
| **プリフェッチ数** | ロジック アプリ リソースのアプリ設定 `AzureFunctionsJobHost__extensions__serviceBus__prefetchCount` の既定値。基になる `ServiceBusProcessor` クラスによって使用されるプリフェッチ数を決定します。 このアプリ設定に異なる値を追加または指定するには、「[アプリ設定の管理 - local.settings.json](edit-app-settings-host-settings.md?tabs=azure-portal#manage-app-settings)」を参照してください。以下はその例です。 <p>- **名前**: `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__prefetchCount` <br>- **値**: `800` (上限なし) <p>`prefetchCount` プロパティの詳細については、次のドキュメントを参照してください。 <p>- [host.json 設定 - Azure Functions における Azure Service Bus のバインド](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) <br>- [ServiceBusProcessor.PrefetchCount プロパティ](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.prefetchcount) <br>- [ServiceBusProcessor クラス](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) |
| **最大メッセージ数** | ロジック アプリ リソースのアプリ設定 `AzureFunctionsJobHost__extensions__serviceBus__batchOptions__maxMessageCount` の既定値。トリガー時に送信する最大メッセージ数を決定します。 このアプリ設定に異なる値を追加または指定するには、「[アプリ設定の管理 - local.settings.json](edit-app-settings-host-settings.md?tabs=azure-portal#manage-app-settings)」を参照してください。以下はその例です。 <p>- **名前**: `AzureFunctionsJobHost__extensions__serviceBus__batchOptions__maxMessageCount` <br>- **値**: `800` (上限なし) <p>`maxMessageCount` プロパティの詳細については、「[host.json 設定 - Azure Functions における Azure Event Hubs のバインド](../azure-functions/functions-bindings-service-bus.md#hostjson-settings)」のドキュメントを参照してください。|
|||

## <a name="prerequisites"></a>前提条件

- Azure アカウントとサブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

- ソースとターゲットのリソースまたはエンティティ。これらは、geo ディザスター リカバリーのフェールオーバー シナリオをテストできるように、異なる Azure リージョンに配置する必要があります。 これらのエンティティは、使用するタスク テンプレートによって異なります。 この記事の例では、異なる名前空間と Azure リージョンに配置されている 2 つの Service Bus キューを使用します。

- レプリケーション タスクを作成するときに再利用できる **ロジック アプリ (Standard)** リソース。 このように、たとえば、容量、スループット、スケーリングなど、レプリケーション シナリオのニーズに基づいて[ホスティング プランと価格レベル](#pricing)を選択するなどして、自分のレプリケーション タスクに合わせてこのリソースをカスタマイズできます。 このリソースはレプリケーション タスクの作成時に作成できますが、リージョン、ホスティング プラン、価格レベルを変更することはできません。 次の一覧は、以前に作成したロジック アプリ リソースのその他の理由とベスト プラクティスを示しています。

  - このロジック アプリ リソースは、レプリケーション タスクのソースとターゲットのエンティティとは異なるリージョンに作成できます。

    現在、このガイダンスは、Azure リソース内のレプリケーション タスクのネイティブ統合に起因して提供されています。 エンティティ間のレプリケーション タスクを作成し、既存のロジック アプリ リソースを使用するのではなく、新しいものを作成することを選択すると、"新しいロジック アプリはソース エンティティと同じリージョンに作成されます"。 ソース リージョンが利用できなくなった場合、レプリケーション タスクも機能しなくなります。 フェールオーバーのシナリオでは、タスクは、以前はターゲット エンティティであった新しいソースからのデータの読み取りを開始することもできませんが、これは、[アクティブ/パッシブ レプリケーション パターン](../service-bus-messaging/service-bus-federation-overview.md#active-passive-replication)が達成しようとするものです。

  - このロジック アプリ リソースは、既定の属性を使用するのではなく、ホスティング プランと価格レベルを選ぶことで、事前にカスタマイズできます。 そうすることで、レプリケーション タスクが 1 秒間に処理できるイベントやメッセージの数が増え、より高速なレプリケーションが実現します。 レプリケーション タスクを作成するときにこのリソースを作成すると、これらの既定の属性は固定されます。

  - 特にアクティブ/パッシブのレプリケーション パターンを使用する場合は、このロジック アプリ リソースに "レプリケーション タスクのワークフローのみ" が含まれるようにすることができます。 既存のロジック アプリを使用してレプリケーション タスクを作成する場合、このオプションを使用すると、そのロジックアプリ リソースにタスク (ステートレス ワークフロー) が追加されます。

  詳細については、「[Azure portal でシングルテナント Azure Logic Apps (Standard) を使用して統合ワークフローを作成する](create-single-tenant-workflows-azure-portal.md)」を参照してください。

- 省略可能: ターゲット名前空間の接続文字列。 このオプションを使用すると、サブスクリプション間のレプリケーションを設定できるように、ターゲットを別のサブスクリプションに配置できます。

   ターゲット エンティティの接続文字列を見つけるには、これらの手順を実行します。

   1. [Azure portal](https://portal.azure.com) でターゲットの名前空間に移動します。

   1. 名前空間のナビゲーション メニューの **[設定]** の下で **[共有アクセス ポリシー]** を選択します。

   1. 開いた **[共有アクセス ポリシー]** ペインの **[ポリシー]** の下で **[RootManageSharedAccessKey]** を選択します。

   1. 開いた **[SAS ポリシー: RootManageSharedAccessKey]** ペインで、 **[プライマリ接続文字列]** の値をコピーします。

   1. 接続文字列を任意の場所に保存し、後でその文字列を使用してターゲットの名前空間に接続できるようにします。

<a name="naming"></a>

## <a name="naming-conventions"></a>名前付け規則

レプリケーション タスクまたはエンティティをまだ作成していない場合は、それらに使用する名前付け戦略を慎重に検討してください。 名前は簡単に識別でき、区別できるようにしてください。 たとえば、Event Hubs 名前空間を使用している場合、レプリケーション タスクは、ソース名前空間のすべての Event Hubs インスタンスからレプリケートを行います。 Service Bus キューを使用している場合は、次の表で、エンティティとレプリケーション タスクの名前付けの例を確認できます。

| ソース名 | 例 | レプリケーション アプリ | 例 | ターゲット名 | 例 |
|-------------|---------|-----------------|---------|-------------|---------|
| 名前空間: `<name>-sb-<region>` | `fabrikam-sb-weu` | ロジック アプリ: `<name-source-region-target-region>` | `fabrikam-rep-weu-wus` | 名前空間: `<name>-sb-<region>` | `fabrikam-sb-wus` |
| キュー: `<name>` | `jobs-transfer` | ワークフロー: `<name>` | `jobs-transfer-workflow` | キュー: `<name>` | `jobs` |
|||||||

<a name="create-replication-task"></a>

## <a name="create-a-replication-task"></a>レプリケーション タスクを作成する

この例では、Service Bus キューのレプリケーション タスクを作成する方法を示します。

1. [Azure portal](https://portal.azure.com) で、ソースとして使用する Service Bus 名前空間を見つけます。

1. 名前空間ナビゲーション メニューの **[Automation]** セクションで、 **[タスク (プレビュー)]** を選択します。

   ![Azure portal と、[タスク (プレビュー)] が選択されている Azure Service Bus 名前空間メニューを示すスクリーンショット。](./media/create-replication-tasks-azure-resources/service-bus-automation-menu.png)

1. **[タスク]** ペインで、 **[タスクの追加]** を選んで、タスク テンプレートを選択できるようにします。

   ![[タスク の追加] が選択された [タスク (プレビュー)] ペインを示すスクリーンショット。](./media/create-replication-tasks-azure-resources/add-replication-task.png)

1. **[タスクの追加]** ペインの **[テンプレートの選択]** を使用し、作成するレプリケーション タスクのテンプレートで **[選択]** を選びます。 次のページが表示されない場合は、 **[次へ: 認証]** を選びます。

   この例では、続いて、Service Bus のキュー間でコンテンツをレプリケートする **[Replicate from Service Bus queue to queue]\(Service Bus キューからキューにレプリケート\)** タスク テンプレートを選びます。

   ![[Replicate from Service Bus queue to queue]\(Service Bus キューからキューにレプリケート\) テンプレートが選択された [タスクの追加] ペインを示すスクリーンショット。](./media/create-replication-tasks-azure-resources/select-replicate-service-bus-template.png)

1. **[認証]** タブの **[接続]** セクションで、タスクに表示されるすべての接続について **[作成]** を選んで、すべての接続の認証資格情報を指定できるようにします。 各タスクの接続の種類は、タスクによって異なります。

   この例では、ターゲット キューが存在するターゲット Service Bus 名前空間への接続を作成するためのプロンプトを示します。 この接続は、ソース Service Bus 名前空間に既に存在します。

   ![ターゲット Service Bus 名前空間への接続に対して選択された [作成] オプションを示すスクリーンショット。](./media/create-replication-tasks-azure-resources/create-authenticate-connections.png)

1. ターゲットに関する必要な情報を指定し、 **[作成]** を選びます。

   この例では、接続の表示名を指定し、ターゲット キューが存在する Service Bus 名前空間を選びます。

   ![指定された接続の表示名と Service Bus 名前空間が選択された [接続] ペインを示すスクリーンショット。](./media/create-replication-tasks-azure-resources/connect-target-service-bus-namespace.png)

   > [!TIP]
   > 代わりに、接続文字列を使用して接続を作成することもできます。 このオプションを使用すると、サブスクリプション間のレプリケーションを設定できるように、ターゲットを別のサブスクリプションに配置できます。 ターゲット (レプリケーション タスクの作成を開始した場所に基づくソース) は動的に構成されるため、ターゲットのみを接続する必要があります。 接続文字列を使用するには、次の手順を使用します。
   >
   > 1. **[接続]** ペインで、 **[接続文字列を使用して接続する]** を選びます。
   >
   > 2. **[接続文字列]** ボックスに、ターゲットの名前空間の接続文字列を入力します。

   次の例は、正常に作成された接続を示しています。

   ![Service Bus 名前空間への接続が完了した状態の [タスクの追加] ペインを示すスクリーンショット。](./media/create-replication-tasks-azure-resources/connected-service-bus-namespaces.png)

1. すべての接続を完了したら、 **[次へ: 構成]** を選びます。

1. **[構成]** タブで、タスクの名前と、タスクに必要なその他の情報を指定します。

   > [!NOTE]
   > 作成後にタスク名を変更することはできません。そのため、[基になるワークフローを編集](#edit-task-workflow)したとしても、引き続き適用できる名前を検討してください。 基になるワークフローに加えた変更は、タスク テンプレートではなく、作成したタスクにのみ適用されます。
   >
   > たとえば、タスクに `fabrikam-rep-weu-wus` という名前を付けた後で、基になるワークフローを別の目的で編集した場合、タスク名が一致するように変更することはできません。

   1. 既存の **ロジック アプリ (Standard)** リソースにタスク ワークフローを追加するには、 **[ロジック アプリ]** の一覧から既存のロジック アプリを選びます。 代わりに新しい **ロジック アプリ (Standard)** リソースを作成するには、 **[ロジック アプリ]** の一覧から **[新規作成]** を選び、新しいロジック アプリに使用する名前を指定します。

      > [!NOTE]
      > レプリケーション タスクの作成中に新しいロジック アプリ リソースを作成すると、 ロジック アプリは "ソース エンティティと同じリージョン" に作成されますが、これはソース リージョンが利用できなくなった場合に問題となり、フェールオーバー シナリオでは機能しません。 ベストプラクティスとして、ソースとは異なるリージョンに **ロジック アプリ (Standard)** リソースを作成することをお勧めします。 レプリケーション タスクを作成するときは、代わりに既存のロジック アプリを選び、基になるステートレス ワークフローを既存のロジック アプリに追加します。 詳細については、「[前提条件](#prerequisites)」を参照してください。

   1. 完了したら、 **[確認および作成]** を選択します。

   ![タスク名、ソースとターゲットのキュー名、ロジック アプリのリソースに使用する名前など、レプリケーション タスクの情報が表示されている [タスクの追加] ペインを示すスクリーンショット。](./media/create-replication-tasks-azure-resources/configure-replication-task.png)

1. **[確認と作成]** タブで、レプリケーション タスクが動作するために必要な Azure リソースを確認します。

   - レプリケーション タスク用に新しいロジック アプリ リソースを作成することを選んだ場合、このペインには、レプリケーション タスクが動作するために作成する必要な Azure リソースが表示されます。 たとえば、これらのリソースには、ロジック アプリ リソース、ワークフロー、およびその他のランタイム操作の構成情報を含む Azure Storage アカウントが含まれます。 たとえば、Event Hubs の場合、このストレージ アカウントには、チェックポイント情報や、ソース リージョンが中断されたり使用できなくなったりした場合にソース エンティティが停止するストリーム内の位置 ("オフセット") が含まれています。

     次の例は、新しいロジック アプリを作成することを選んだ場合の **[確認と作成]** タブを示しています。

     ![新しいロジック アプリを作成する場合のリソース情報が表示された [確認と作成] ペインを示すスクリーンショット。](./media/create-replication-tasks-azure-resources/validate-replication-task-new-logic-app.png)

   - レプリケーション タスクに既存のロジック アプリ リソースを再利用することを選んだ場合、このペインには、レプリケーションが動作するために再利用される Azure リソースが表示されます。

     次の例は、既存のロジック アプリを再利用することを選んだ場合の **[確認と作成]** タブを示しています。

     ![既存のロジック アプリを再利用する場合のリソース情報が表示された [確認と作成] ペインを示すスクリーンショット。](./media/create-replication-tasks-azure-resources/validate-replication-task-existing-logic-app.png)

   > [!NOTE]
   > ソース、ターゲット、またはその両方が仮想ネットワークの背後にある場合は、タスクの作成後にアクセス許可とアクセスを設定する必要があります。 このシナリオでは、ロジック アプリのワークフローがレプリケーション タスクを実行できるように、アクセス許可とアクセスが必要です。

1. 準備ができたら、 **[作成]** を選択します。

   自動的にライブで実行されている作成したタスクが、 **[タスク]** 一覧に表示されるようになりました。

   > [!TIP]
   > タスクがすぐに表示されない場合は、タスク一覧を最新の状態に更新するか、少し待ってから更新してください。 ツール バーの **[更新]** を選択します。

   ![レプリケーション タスクが作成された [タスク] ペインを示すスクリーンショット。](./media/create-replication-tasks-azure-resources/created-replication-task.png)

1. 使用するリソースが仮想ネットワークの背後にある場合は、ロジック アプリのリソースとワークフローがそれらのリソースにアクセスするためのアクセス許可を忘れずに設定してください。

## <a name="set-up-retry-policy"></a>再試行ポリシーを設定する

レプリケーション関係のどちらかの側で可用性イベントが発生したときにデータが失われないようにするには、堅牢性を確保するために再試行ポリシーを構成する必要があります。 レプリケーション タスクの再試行ポリシーを構成するには、 [Azure Logic Apps の再試行ポリシーに関するドキュメント](logic-apps-exception-handling.md#retry-policies)と、[基になるワークフローを編集](#edit-task-workflow)するための手順を確認してください。

<a name="review-task-history"></a>

## <a name="review-task-history"></a>タスク履歴の確認

この例では、タスクのワークフロー実行の履歴を、その状態、入力、出力、その他の情報とともに表示する方法を示し、Service Bus キュー レプリケーション タスクの例を使用して続行します。

1. [Azure portal](https://portal.azure.com) で、確認するタスク履歴がある Azure リソースまたはエンティティを見つけます。

   この例では、このリソースは Service Bus 名前空間です。

1. リソース ナビゲーション メニューの **[設定]** の下にある **[Automation]** セクションで、 **[タスク (プレビュー)]** を選びます。

1. **[タスク]** ペインで、確認するタスクを見つけます。 このタスクの **[実行]** 列で、 **[表示]** を選択します。

   ![[タスク] ペイン、レプリケーション タスク、および選択された [表示] オプションを示すスクリーンショット。](./media/create-replication-tasks-azure-resources/view-runs-for-task.png)

   この手順では、Standard ロジック アプリのリソースに含まれている、基になるステートレス ワークフローの **[概要]** ペインを開きます。

1. ステートレス ワークフローの実行履歴を表示するには、 **[概要]** ペインのツール バーで、 **[デバッグ モードを有効にする]** を選びます。

   **[実行履歴]** タブには、タスクの以前の実行、進行中の実行、待機中の実行が、その識別子、状態、開始時刻、実行継続時間とともに表示されます。

   ![タスクの実行、その状態、その他の情報を示すスクリーンショット。](./media/create-replication-tasks-azure-resources/run-history-list.png)

   次の表は、実行に対して可能性のある状態を示しています。

   | 状態ラベル | 説明 |
   |--------------|-------------|
   | **取り消し済み** | タスクは実行中に取り消されました。 |
   | **Failed** | タスクには少なくとも 1 つの失敗したアクションがありますが、失敗を処理するための後続のアクションが存在しませんでした。 |
   | **実行中** | タスクは現在実行中です。 |
   | **Succeeded** | アクションはすべて成功しています。 アクションが失敗した場合でもタスクは正常に完了しますが、失敗を処理するための後続のアクションが存在していました。 |
   | **待機中** | 実行はまだ開始されていません。タスクの以前のインスタンスがまだ実行中であるため、一時停止しています。 |
   |||

1. 実行の各ステップの状態などの情報を表示するには、その実行を選択します。

   実行の詳細ペインが開き、基になる実行されたワークフローが表示されます。

   - ワークフローは、常に [*トリガー*](../connectors/apis-list.md#triggers)で開始されます。 このタスクでは、ワークフローは、ソース Service Bus キューにメッセージが到着するのを待機する Service Bus トリガーで開始されます。

   - 各ステップには、状態と実行継続時間が表示されます。 継続時間が 0 秒のステップは、実行が 1 秒未満であったものです。

   ![ワークフローにおける実行、状態、実行継続時間の各ステップを示すスクリーンショット。](./media/create-replication-tasks-azure-resources/run-history-details.png)

1. 各ステップの入力と出力を確認するには、ステップを選択します。これにより、そのステップの入力、出力、およびプロパティの詳細を示すペインが開きます。

   この例は、Service Bus トリガーの入力を示しています。

   ![トリガーの入力、出力、およびプロパティを示すスクリーンショット。](./media/create-replication-tasks-azure-resources/view-trigger-inputs-outputs-properties.png)

アプリ、データ、サービス、およびシステムを、Azure リソースのレプリケーション タスクのコンテキストとは別に統合できるように、独自の自動化されたワークフローを構築する方法については、「[Azure portal でシングルテナント Azure Logic Apps (Standard) を使用して統合ワークフローを作成する](create-single-tenant-workflows-azure-portal.md)」を参照してください。

<a name="monitor"></a>

## <a name="monitor-replication-tasks"></a>レプリケーション タスクを監視する

レプリケーション タスク、または基になるロジック アプリ ワークフローのパフォーマンスと正常性を確認するには、Azure Monitor の機能である [Application Insights](../azure-monitor/app/app-insights-overview.md) を使用します。 [Application Insights のアプリケーション マップ](../azure-monitor/app/app-map.md)は、レプリケーション タスクの監視に使用できる便利なビジュアル ツールです。 このマップは、キャプチャした監視情報から自動的に生成されるため、レプリケーション タスクのソースとターゲットの転送のパフォーマンスと信頼性を調べることができます。 診断結果をすぐに確認したり、ログの詳細を低遅延で視覚化したりするには、Azure Monitor の機能でもある [Live Metrics](../azure-monitor/app/live-stream.md) ポータル ツールを使用します。

<a name="edit-task"></a>

## <a name="edit-the-task"></a>タスクを編集する

タスクを変更するには、次のオプションがあります。

- 接続情報や構成情報などのタスクのプロパティを変更できるように、[タスクを "インライン" で編集](#edit-task-inline)します。

- デザイナーで、[タスクの基になるワークフローを編集](#edit-task-workflow)します。

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>タスクをインラインで編集する

1. [Azure portal](https://portal.azure.com) で、更新するタスクが含まれているリソースを見つけます。

1. リソース ナビゲーション メニューの **[Automation]** セクションで、 **[タスク (プレビュー)]** を選びます。

1. [タスク] 一覧で、更新するタスクを見つけます。 タスクの省略記号 ( **...** ) メニューを開き、 **[インラインで編集]** を選択します。

   ![開かれた省略記号メニューと、選択されている [インラインで編集] オプションを示すスクリーンショット。](./media/create-replication-tasks-azure-resources/edit-task-in-line.png)

   既定では、 **[認証]** タブが表示され、既存の接続が表示されます。

1. 新しい認証資格情報を追加するか、接続に使用する既存の認証資格情報を選択するには、接続の省略記号 ( **...** ) メニューを開き、 **[新しい接続の追加]** または使用可能な場合は別の認証資格情報を選択します。

   > [!NOTE]
   > 編集できるのは、ソース接続ではなく、ターゲット接続のみです。

   ![[認証] タブ、既存の接続、および選択した省略記号メニューを示すスクリーンショット。](./media/create-replication-tasks-azure-resources/edit-connections.png)

1. その他のタスクのプロパティを更新するには **[次へ: 構成]** を選びます。

   この例のタスクでは、異なるソースとターゲットのキューを指定できます。 ただし、タスク名、基になるロジック アプリとワークフローは同じままです。

   ![[構成] タブと編集可能なプロパティを示すスクリーンショット。](./media/create-replication-tasks-azure-resources/edit-task-configuration.png)

1. 終了したら、 **[保存]** を選択します。

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>タスクの基になるワークフローの編集

レプリケーション タスクの背後にある、基になるワークフローを編集できます。この場合、タスク テンプレート自体ではなく、作成したタスクの元の構成が変更されます。 変更を加えて保存すると、編集したタスクは元のタスクと同じ機能を実行しなくなります。 元の機能を実行するタスクが必要な場合は、同じテンプレートを使って新しいタスクを作成する必要が生じる場合があります。 元のタスクを再作成したくない場合は、デザイナーを使ってタスクの背後にあるワークフローを変更することは避けてください。 代わりに、統合のニーズに合わせて **ロジック アプリ (Standard)** のステートレス ワークフローを作成します。 詳細については、「[Azure portal でシングルテナント Azure Logic Apps (Standard) を使用して統合ワークフローを作成する](create-single-tenant-workflows-azure-portal.md)」を参照してください。

1. [Azure portal](https://portal.azure.com) で、更新するタスクが含まれているリソースを見つけます。

1. リソース ナビゲーション メニューの **[Automation]** セクションで、 **[タスク]** を選択します。

1. [タスク] 一覧で、更新するタスクを見つけます。 タスクの省略記号 ( **...** ) メニューを開き、 **[Open in Logic Apps]\(Logic Apps で開く\)** を選択します。

   ![開かれた省略記号メニューと、選択されている [Open in Logic Apps]\(Logic Apps で開く\) オプションを示すスクリーンショット。](./media/create-replication-tasks-azure-resources/open-task-in-designer.png)

   Azure portal によってコンテキストがデザイナーに変わり、ワークフローを編集できるようになります。

   ![デザイナーと基になるワークフローを示すスクリーンショット。](./media/create-replication-tasks-azure-resources/view-task-workflow-designer.png)

   ワークフローのトリガーとアクション、およびトリガーとアクションのプロパティを編集できるようになりました。

1. トリガーまたはアクションのプロパティを表示するには、そのトリガーまたはアクションを選びます。

   ![Service Bus トリガーのプロパティ ペインを示すスクリーンショット。](./media/create-replication-tasks-azure-resources/edit-service-bus-trigger.png)

   この例では、トリガーの **IsSessionsEnabled** プロパティが **[はい]** に変更されています。

1. 変更を保存するには、デザイナーのツール バーで、 **[保存]** を選択します。

   ![デザイナーのツール バーと、選択された [保存] コマンドを示すスクリーンショット。](./media/create-replication-tasks-azure-resources/save-updated-workflow.png)

1. 更新されたワークフローをテストおよび実行するには、更新されたワークフローを含むロジック アプリ リソースを開きます。 ワークフローのナビゲーション メニューで、 **[概要]**  >  **[トリガーの実行]**  >  **[実行]** を選びます。

   実行が完了すると、デザイナーにワークフローの実行の詳細が表示されます。 各ステップの入力と出力を確認するには、ステップを選択します。これにより、そのステップの入力、出力、およびプロパティの詳細を示すペインが開きます。

   この例では、選択した Service Bus トリガーの入力、出力、プロパティ、および更新されたトリガーのプロパティ値を示しています。

   ![トリガーの入力、出力、およびプロパティを含むワークフローの実行の詳細を示すスクリーンショット。](./media/create-replication-tasks-azure-resources/view-updated-run-details-trigger-inputs.png)

1. タスクが継続して実行されないようにワークフローを無効にするには、 **[概要]** ツール バーで **[無効]** を選択します。 詳細については、[シングルテナント ワークフローの無効化または有効化](create-single-tenant-workflows-azure-portal.md#disable-or-enable-workflows)に関する記事を参照してください。

<a name="failover"></a>

## <a name="set-up-failover-for-azure-event-hubs"></a>Azure Event Hubs のフェールオーバーを設定する

同じエンティティ タイプ間で Azure Event Hubs レプリケーションを行う場合、geo ディザスター リカバリーでは、ソース エンティティからターゲット エンティティへのフェールオーバーを実行し、影響を受けるイベント コンシューマーとプロデューサーに対して、新しいソースとなるターゲット エンティティのエンドポイントを使用するように指示する必要があります。 そのため、障害が発生し、ソース エンティティがフェールオーバーした場合、レプリケーション タスクを含むコンシューマーとプロデューサーは新しいソースにリダイレクトされます。 レプリケーション タスクが作成したストレージ アカウントには、チェックポイント情報や、ソース リージョンが中断されたり使用できなくなったりした場合にソース エンティティが停止するストリーム内の位置 (オフセット) が含まれています。

ストレージ アカウントに元のソースからのレガシ情報が含まれておらず、レプリケーション タスクが新しいソース ストリームの開始時からイベントの読み取りとレプリケーションを開始するようにするには、手動で元のソースからレガシ情報をクリーンアップし、レプリケーション タスクを再構成する必要があります。

1. [Azure portal](https://portal.azure.com) で、レプリケーション タスクの背後にあるロジック アプリ リソースまたは基になるワークフローを開きます。

   > [!NOTE]
   > ロジック アプリ リソースには、レプリケーション タスク ワークフローのみを含める必要があります。

1. リソースまたはワークフローのナビゲーション メニューで、 **[概要]** を選びます。 **[概要]** ツール バーで、ワークフローに対して **[無効]** を選択するか、ロジック アプリ リソースに対して **[停止]** を選択します。

1. ソース エンティティからのチェックポイントとストリーム オフセット情報の格納先として、レプリケーション タスクの基になるロジック アプリ リソースで使用されているストレージ アカウントを見つけるために、次の手順に従います。

   1. ロジック アプリのリソース メニューにある **[設定]** で、 **[構成]** を選択します。

   1. **[構成]** ペインの **[アプリケーション設定]** タブで、 **[AzureWebJobsStorage]** アプリ設定を選択します。

      この設定は、ロジック アプリ リソースによって使用される接続文字列とストレージ アカウントを指定します。

      > [!NOTE]
      > アプリ設定がリストに表示されない場合は、 **[値を表示する]** を選択してください。

   1. ストレージ アカウントの名前を確認できるよう **[AzureWebJobsStorage]** アプリ設定を選択します。

   この例は、このストレージ アカウント (ここでは `storagefabrikamreplb0c`) の見つけ方を示しています。

   ![基になるロジック アプリ リソースの [構成] ペインのスクリーンショット。"AzureWebJobsStorage" のアプリ設定と、ストレージ アカウント名を含んだ接続文字列が表示されています。](./media/create-replication-tasks-azure-resources/find-storage-account-name.png)

   1. 該当するストレージ アカウント リソースが存在することを確認するために、Azure portal の検索ボックスに名前を入力し、ストレージ アカウントを選択します。その例を次に示します。

   ![Azure portal の検索ボックスにストレージ アカウントの名前が入力された画面のスクリーンショット。](./media/create-replication-tasks-azure-resources/find-storage-account.png)

1. 次に、ソース エンティティのチェックポイントとオフセット情報が格納されたフォルダーを削除します。次の手順に従ってください。

   1. まだ最新バージョンをお持ちでない場合は、最新の [Azure Storage Explorer デスクトップ クライアント](https://azure.microsoft.com/features/storage-explorer/)をダウンロードし、インストールして開きます。

      > [!NOTE]
      > 現時点では、削除のクリーンアップ タスクに Azure Storage Explorer クライアントを使用する必要があります。Azure portal の管理エクスペリエンス、ストレージ エクスプローラー、ブラウザー、エディターは "*使用できません*"。
      >
      > PowerShell の [`Remove-AzStorageDirectory` コマンド](/powershell/module/az.storage/remove-azstoragedirectory)でコンテナーのフォルダーを削除することはできますが、このコマンドが作用するのは "*空の*" フォルダーのみです。

   1. まだ Azure アカウントにサインインしていない場合はサインインし、ストレージ アカウント リソースの Azure サブスクリプションが選択されていることを確認します。 詳細については、「[Storage Explorer の概要](../vs-azure-tools-storage-manage-with-storage-explorer.md)」を参照してください。

   1. エクスプローラー ウィンドウで、該当する Azure サブスクリプション名から、 **[ストレージ アカウント]**  >  **[{*your-storage-account-name*}]**  >  **[BLOB コンテナー]**  >  **[azure-webjobs-eventhub]** の順に移動します。

      > [!NOTE]
      > **azure-webjobs-eventhub** フォルダーが存在しない場合、レプリケーション タスクがまだ実行されていません。 このフォルダーは、レプリケーション タスクが少なくとも 1 回実行された後にのみ表示されます。

      ![Azure Storage Explorer のスクリーンショット。ストレージ アカウントと BLOB コンテナーが展開され、"azure-webjobs-eventhub" フォルダーが選択状態で表示されています。](./media/create-replication-tasks-azure-resources/azure-webjobs-eventhub-storage-explorer.png)

   1. 表示された **[azure-webjobs-eventhub]** ペインで、Event Hubs 名前空間のフォルダーを選択します。フォルダー名は、`<source-Event-Hubs-namespace-name>.servicebus.windows.net` という形式になっています。

   1. 名前空間のフォルダーが開いたら、 **[azure-webjobs-eventhub]** ペインで <*former-source-entity-name*> フォルダーを選択します。 次の例のように、ツール バーまたはフォルダーのショートカット メニューから **[削除]** を選択します。

      ![以前のソース Event Hubs エンティティ フォルダーと [削除] ボタンが選択された画面のスクリーンショット。](./media/create-replication-tasks-azure-resources/delete-former-source-entity-folder-storage-explorer.png)

   1. フォルダーを削除することを確認します。

1. レプリケーション タスクの背後にあるロジック アプリのリソースまたはワークフローに戻ります。 ロジック アプリを再起動するか、ワークフローを再度有効にします。

プロデューサーとコンシューマーが新しいソース エンドポイントを使用するようにするには、新しいソースのエンティティに関する情報を、簡単にアクセスして更新できる場所で使用および検出できるようにする必要があります。 プロデューサーまたはコンシューマーは、頻繁に発生するまたは永続的なエラーを検出した場合、その場所を確認し、それらの構成を調整する必要があります。 この構成を共有するにはさまざまな方法がありますが、例として DNS やファイル共有などが挙げられます。

geo ディザスター リカバリーの詳細については、次のドキュメントを参照してください。

- [Azure Event Hubs - geo ディザスター リカバリー](../event-hubs/event-hubs-geo-dr.md)
- [Azure Service Bus - geo ディザスター リカバリー](../service-bus-messaging/service-bus-geo-dr.md)

<a name="edit-plan-scale-out-settings"></a>

## <a name="edit-hosting-plan-scale-out-settings"></a>ホスティング プランのスケールアウト設定を編集する

### <a name="portal"></a>[ポータル](#tab/portal)

1. [Azure portal](https://portal.azure.com) で、レプリケーション タスクの基になるロジック アプリ リソースを開きます。

1. ロジック アプリ リソース メニューの **[設定]** で、 **[Scale Out (App Service のプラン)]** を選択します。

   ![ホスティング プランの最大バースト数、最小インスタンス数、常時使用可能なインスタンス、スケールアウト制限の適用の各設定を示すスクリーンショット。](./media/create-replication-tasks-azure-resources/edit-app-service-plan-settings.png)

1. 実際のシナリオの要件に基づき、 **[プランのスケールアウト]** と **[アプリのスケールアウト]** で、それぞれ最大バーストと常時使用可能なインスタンスの値を変更します。

1. 操作が完了したら、 **[スケールアウト (App Service のプラン)]** ペインのツールバーで、 **[保存]** を選択します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

アプリの常時使用可能なインスタンスは、Azure CLI を使用して構成することもできます。

```azurecli-interactive
az resource update -g <resource_group> -n <logic-app-app-name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites
```

---

ワークフロー Standard プランと Azure Functions Premium プランにはいくつか共通点があります。詳細については、次のドキュメントを参照してください。

- [プランと SKU の設定 - Azure Functions Premium プラン](../azure-functions/functions-premium-plan.md#plan-and-sku-settings)
- [クラウド バーストについて](https://azure.microsoft.com/overview/what-is-cloud-bursting/)
- [常時使用可能なインスタンス - Azure Functions Premium プラン](../azure-functions/functions-premium-plan.md#always-ready-instances)

<a name="problems-failures"></a>

## <a name="replication-problems-and-failures"></a>レプリケーションの問題と失敗

このセクションでは、レプリケーションが失敗または動作しなくなる可能性のある状況について説明します。

- メッセージ サイズの制限

  レプリケーション タスクによって[レプリケーション プロパティ](#replication-properties)が追加されるため、メッセージは必ず 1 MB 未満で送信してください。 そうしないと、タスクによって[レプリケーション プロパティ](#replication-properties)が追加された後に、メッセージのサイズが Event Hubs エンティティに送信できるイベントのサイズより大きい場合、レプリケーション プロセスは失敗します。

  たとえば、メッセージのサイズが 1 MB であるとします。 タスクによってレプリケーション プロパティが追加された後、メッセージのサイズが 1 MB よりも大きくなります。 メッセージを送信しようとする送信呼び出しは失敗します。

- パーティション キー

  イベントにパーティション キーが存在する場合、Event Hubs インスタンス間のレプリケーションは、それらのインスタンスのパーティション数が同じであれば失敗します。

## <a name="next-steps"></a>次のステップ

- [シングルテナント Azure Logic Apps のワークフロー デザイナーについて](designer-overview.md)
- [シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)
- [シングルテナント Azure Logic Apps の環境をまたいだワークフローで使用するパラメーターを作成する](parameterize-workflow-app.md)
