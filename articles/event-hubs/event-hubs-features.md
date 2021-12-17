---
title: 機能の概要 - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Event Hubs の機能と用語に関する詳細を示します。
ms.topic: overview
ms.date: 08/27/2021
ms.openlocfilehash: b06ce04a7b2fd4fbb55cbe1b3ac8c2510305e781
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123226332"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Azure Event Hubs の機能と用語

Azure Event Hubs は、大量のイベントやデータを取り込んで処理するスケーラブルなイベント処理サービスで、短い待機時間と高い信頼性を実現します。 概要については、「[Event Hubs とは](./event-hubs-about.md)」を参照してください。

[概要記事](./event-hubs-about.md)内の情報に基づいて作成されたこの記事では、Event Hubs のコンポーネントと機能に関する実装の技術的な詳細を説明します。

> [!TIP]
> [**Apache Kafka** クライアント (バージョン 1.0 以降) のプロトコル サポート](event-hubs-for-kafka-ecosystem-overview.md)により、任意のクライアントで Apache Kafka を使用するように構築されたアプリケーションで、Event Hubs を使用できるようにするネットワーク エンドポイントが提供されます。 既存の Kafka アプリケーションのほとんどは、Kafka クラスターのブートストラップ サーバーではなく、イベント ハブの名前空間を指すように簡単に再構成できます。 
>
>コスト、運用の作業量、信頼性の観点から見ると、Azure Event Hubs は、独自の Kafka および Zookeeper クラスターをデプロイして運用したり、Azure にネイティブではないサービスとしての Kafka を提供したりすることに代わる優れた手段です。 
>
> Apache Kafka ブローカーと同じコア機能を利用できるだけでなく、Azure Event Hub の機能にアクセスすることもできます。これには、[Event Hubs Capture](event-hubs-capture-overview.md) 経由の自動のバッチ処理とアーカイブ、自動のスケーリングと分散、ディザスター リカバリー、コストに依存しない可用性ゾーンのサポート、柔軟で安全なネットワーク統合、ファイアウォールに適した WebSocket 経由の AMQP プロトコルを含むマルチプロトコル サポートなどがあります。


## <a name="namespace"></a>名前空間
Event Hubs 名前空間は、イベント ハブ (Kafka 用語ではトピック) の管理コンテナーです。 これにより、DNS 統合ネットワーク エンドポイントと、一連のアクセス制御およびネットワーク統合管理機能 ([IP フィルタリング](event-hubs-ip-filtering.md)、[仮想ネットワーク サービス エンドポイント](event-hubs-service-endpoints.md)、[Private Link](private-link-service.md) など) が提供されます。

:::image type="content" source="./media/event-hubs-features/namespace.png" alt-text="Event Hubs 名前空間を示す図":::

## <a name="event-publishers"></a>イベント発行元

イベント ハブにデータを送信するエンティティはすべて、"*イベント発行元*" ("*イベント プロデューサー*" と同義) です。 イベント発行元は、HTTPS、AMQP 1.0、または Kafka プロトコルを使用してイベントを発行できます。 イベント発行元は、OAuth2 で発行された JWT トークンか、イベント ハブ固有の Shared Access Signature (SAS) トークンによる Azure Active Directory ベースの承認を使用して、発行アクセス権を取得します。

### <a name="publishing-an-event"></a>イベントの発行

AMQP 1.0、Kafka プロトコル、または HTTPS 経由でイベントを発行できます。 Event Hubs サービスは、イベント ハブにイベントを発行するための [REST API](/rest/api/eventhub/) と [.NET](event-hubs-dotnet-standard-getstarted-send.md)、[Java](event-hubs-java-get-started-send.md)、[Python](event-hubs-python-get-started-send.md)、[JavaScript](event-hubs-node-get-started-send.md)、[Go](event-hubs-go-get-started-send.md) の各クライアント ライブラリを備えています。 その他のランタイムとプラットフォームには、 [Apache Qpid](https://qpid.apache.org/)などの任意の AMQP 1.0 クライアントを使用できます。 

AMQP または HTTPS のどちらを使用するかは、使用シナリオによって決まります。 AMQP では、トランスポート レベルのセキュリティ (TLS) または SSL/TLS に加えて、永続的な双方向ソケットを確立する必要があります。 AMQP ではセッション初期化時のネットワーク コストが高くなりますが、HTTPS では要求ごとに追加の TLS オーバーヘッドが必要になります。 AMQP は、頻度の高い発行元に対して非常に高いパフォーマンスを備えており、非同期発行コードと共に使用すると、はるかに短い待機時間を実現できます。

イベントは、個別に発行することもバッチ処理することもできます。 1 つのイベントかバッチ処理かに関係なく、1 回の発行には 1 MB の制限があります。 このしきい値を超えるイベントの発行は拒否されます。 

Event Hubs のスループットは、パーティションとスループット ユニットの割り当てを使用してスケーリングされます (以下を参照)。 発行元は、イベント ハブ用に選択された特定のパーティション分割モデルは使用せずに、関連するイベントを同じパーティションに一貫して割り当てるために使用される "*パーティション キー*" の指定だけを行うことをお勧めします。

![パーティション キー](./media/event-hubs-features/partition_keys.png)

Event Hubs によって、1 つのパーティション キー値を共有するすべてのイベントが一緒に格納され、到着順に配信されます。 パーティション キーと発行元ポリシーを併用する場合は、発行元の ID とパーティション キーの値が一致する必要があります。 そうでない場合、エラーが発生します。

### <a name="event-retention"></a>イベントの保持

発行されたイベントは、構成可能な時間ベースの保持ポリシーに基づいてイベント ハブから削除されます。 重要な点がいくつかあります。

- **既定** 値および指定可能な **最小** 保持期間は **1 日 (24 時間)** です。
- Event Hubs **Standard** の場合、最大保持期間は **7 日** です。 
- Event Hubs の **Premium** および **Dedicated** の場合、最大保持期間は **90 日間** です。
- 保持期間を変更すると、既にイベント ハブ内にあるメッセージを含むすべてのメッセージに適用されます。 

Event Hubs は構成された期間にわたりイベントを保持します。これは、すべてのパーティションに適用されます。 保持期間が経過するとイベントは自動的に削除されます。 保持期間を 1 日に指定すると、イベントは、その受領からちょうど 24 時間で利用できなくなります。 イベントを明示的に削除することはできません。 

許可された保持期間を超えるイベントをアーカイブする必要がある場合は、[Event Hubs Capture 機能](event-hubs-capture-overview.md)を有効にすることにより、Azure Storage または Azure Data Lake に自動的に格納できます。 そのようなディープ アーカイブを検索または分析する必要がある場合は、それらを [Azure Synapse](store-captured-data-data-warehouse.md) または他の同様のストアや分析プラットフォームに簡単にインポートできます。 

Event Hubs でデータの保持期間に上限を設けている理由は、タイムスタンプによってのみインデックスされ、順次アクセスしかできない大きなストアに、過去に発生した大量のカスタマー データが無造作に取り込まれるのを避けるためです。 このようなアーキテクチャが採用されている理由は、履歴データには、Event Hubs や Kafka で提供されているリアルタイム イベントのインターフェイスよりも高度なインデックス作成とより直接的なアクセスが必要であると考えられるからです。 イベント ストリーム エンジンは、イベント ソーシングのデータ レイクや長期アーカイブの用途には適していません。 
 

> [!NOTE]
> Event Hubs はリアルタイムのイベント ストリーム エンジンであるため、データベースや、無期限に保持されるイベント ストリームの永続的なストアの代わりとして使用されるように設計されていません。 
> 
> イベント ストリームの履歴が多いほど、特定のストリームの特定の履歴スライスを見つけるために多くの補助インデックスが必要になります。 イベント ペイロードとインデックス作成の検査は、Event Hubs (または Apache Kafka) の機能の範囲内にはありません。 したがって、データベースや、[Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)、[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md)、[Azure Synapse](../synapse-analytics/overview-what-is.md) などの専用の分析ストアおよびエンジンの方が、履歴イベントの保存にはずっと適しています。
>
> [Event Hubs Capture](event-hubs-capture-overview.md) は Azure Blob Storage および Azure Data Lake Storage に直接統合されており、その統合を通じて[イベントを Azure Synapse に直接フローさせる](store-captured-data-data-warehouse.md)ことができます。
>
> アプリケーションの[イベント ソーシング](/azure/architecture/patterns/event-sourcing) パターンを使用する場合は、スナップショット戦略を Event Hubs の保持期間の制限に合わせる必要があります。 期間の開始時点からのすべての未加工イベントで、具体化されたビューを再構築しようとしないでください。 そのような戦略は、アプリケーションをしばらく本稼働させて十分に使用した後、プロジェクション ビルダーが、最新および継続中の変更に追い付こうとしながら数年分の変更イベントを使用しなければならなくなった場合に必ず後悔するようになります。 


### <a name="publisher-policy"></a>発行元ポリシー

Event Hubs では、 *発行元ポリシー* を介してイベント プロデューサーをきめ細かく制御できます。 発行元ポリシーは、多数の独立したイベント発行元を支援するために設計されたランタイム機能です。 発行元ポリシーでは、次のメカニズムを使用してイベント ハブにイベントを発行する際に、各発行元は独自の一意の識別子を使用します。

```http
//<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>
```

前もって発行元名を作成しておく必要はありませんが、独立した発行元 ID を保証するために、発行元名はイベントを発行するときに使用される SAS トークンと一致する必要があります。 発行元ポリシーを使用する場合は、 **PartitionKey** 値を発行元名に設定します。 適切に機能するために、これらの値が一致する必要があります。

## <a name="capture"></a>キャプチャ

[Event Hubs Capture](event-hubs-capture-overview.md) を使用すると、Event Hubs のストリーミング データを自動的にキャプチャし、BLOB ストレージ アカウントまたは Azure Data Lake Storage アカウントのいずれかを選択して保存できます。 Azure portal からキャプチャを有効にし、キャプチャを実行する最小サイズと時間枠を指定できます。 Event Hubs Capture を使用して、独自の Azure Blob Storage アカウントとコンテナー、または Azure Data Lake Storage アカウントを指定し、そのいずれかに、キャプチャされたデータを格納します。 キャプチャされたデータは、Apache Avro 形式で書き込まれます。

:::image type="content" source="./media/event-hubs-features/capture.png" alt-text="Event Hub のデータの Azure Storage または Azure Data Lake Storage へのキャプチャを示す図":::

Event Hubs Capture によって生成されたファイルには、次の Avro スキーマがあります。

:::image type="content" source="./media/event-hubs-capture-overview/event-hubs-capture3.png" alt-text="キャプチャされたデータの構造を示す図":::

<<<<<<< HEAD
## <a name="partitions"></a>パーティション
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]
=======
## <a name="partitions"></a>メジャー グループ
[!INCLUDE [event-hubs-partitions](./includes/event-hubs-partitions.md)]
>>>>>>> repo_sync_working_branch


## <a name="sas-tokens"></a>SAS トークン

Event Hubs は、名前空間とイベント ハブのレベルで利用可能な *Shared Access Signature* を使用します。 SAS トークンは、SAS キーから生成されるものであり、特定の形式でエンコードされた URL の SHA ハッシュです。 Event Hubs は、キー (ポリシー) の名前とトークンを使用することで、ハッシュを再生成し、送信者を認証できます。 通常、イベント発行元の SAS トークンは特定のイベント ハブへの **送信** 特権のみを付加して作成されます。 この SAS トークン URL のメカニズムは、発行元ポリシーに導入された発行元識別のための基盤です。 SAS を使用する方法の詳細については、「[Service Bus による Shared Access Signature 認証](../service-bus-messaging/service-bus-sas.md)」を参照してください。

## <a name="event-consumers"></a>イベント コンシューマー

イベント ハブからイベント データを読み取るエンティティは、いずれも "*イベント コンシューマー*" です。 Event Hubs のすべてのコンシューマーは AMQP 1.0 セッションを介して接続します。イベントは使用可能になると、このセッションを使用して配信されます。 クライアントがデータの可用性をポーリングする必要はありません。

### <a name="consumer-groups"></a>コンシューマー グループ

Event Hubs の発行/サブスクライブのメカニズムは、"*コンシューマー グループ*" によって有効になります。 コンシューマー グループは、イベント ハブ全体のビュー (状態、位置、またはオフセット) を表します。 コンシューマー グループを使用することにより、複数のコンシューマー アプリケーションは、イベント ストリームの個別のビューをそれぞれ保有し、独自のペースで独自のオフセットによってストリームを別々に読み取ることができます。

ストリーム処理アーキテクチャにおいて、各ダウンストリーム アプリケーションはコンシューマー グループに相当します。 (パーティションからさらに) 長期的なストレージにイベント データを書き込む場合、そのストレージ ライター アプリケーションはコンシューマー グループとなります。 複雑なイベント処理は、別の異なるコンシューマー グループで実行できます。 パーティションにはコンシューマー グループを介してのみアクセスできます。 イベント ハブには必ず、既定のコンシューマー グループが存在します。対応する価格レベルに応じた[コンシューマー グループの最大数](event-hubs-quotas.md)まで作成できます。 

コンシューマー グループごとに 1 つのパーティションに同時に接続できるリーダーは最大で 5 つですが、**コンシューマー グループごとのパーティションのアクティブ レシーバーは 1 つだけにすることをお勧めします**。 1 つのパーティション内で、各リーダーがすべてのメッセージを受信します。 同じパーティションに複数のリーダーがある場合、重複したメッセージを処理します。 お使いのコード内でこれを処理する必要があり、相応の作業量が生じる場合があります。 ただし、これは一部のシナリオで有効な手法です。

Azure SDK によって提供される一部のクライアントはインテリジェントなコンシューマー エージェントです。これは、各パーティションに 1 つのリーダーがあり、イベント ハブのすべてのパーティションが読み取られていることを確認するための詳細を自動的に管理します。 これにより、コードではイベント ハブから読み取られるイベントの処理に注力できるため、パーティションの詳細の多くを無視できます。 詳細については、「[パーティションに接続する](#connect-to-a-partition)」を参照してください。

コンシューマー グループ URI 表記の例を次に示します。

```http
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>
```

次の図は、Event Hubs ストリーム処理のアーキテクチャを示しています。

![Event Hubs のアーキテクチャ](./media/event-hubs-about/event_hubs_architecture.svg)

### <a name="stream-offsets"></a>ストリームのオフセット

"*オフセット*" は、パーティション内のイベントの位置です。 オフセットは、クライアント側のカーソルと考えることができます。 オフセットはイベントのバイト位置です。 このオフセットにより、イベント コンシューマー (リーダー) は、イベント ストリーム内でのイベント読み取りの開始点を指定することができます。 オフセットは、タイムスタンプとして、またはオフセット値として指定することができます。 Event Hubs サービスの外部で独自のオフセット値を格納する場合は、コンシューマーの責任で行います。 パーティション内では、各イベントにオフセットが含まれます。

![パーティションのオフセット](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>チェックポイント機能

"*チェックポイント処理*" とは、リーダーがパーティションにおけるイベント シーケンス内の位置をマークまたはコミットするために使用する処理です。 チェックポイント処理はコンシューマーの責任で行います。この処理はコンシューマー グループ内でパーティションごとに発生します。 つまり、コンシューマー グループごとに、各パーティション リーダーは、イベント ストリーム内でのその現在の位置を追跡する必要があり、データ ストリームが完了したと見なしたときにサービスに通知することができます。

リーダーがパーティションから切断し、その後再び接続すると、該当するコンシューマー グループ内の該当するパーティションの最後のリーダーによって最後に送信されたチェックポイントから読み取りが開始されます。 リーダーは接続の際に、このオフセットをイベント ハブに渡して、読み取りを開始する場所を指定します。 このように、チェックポイント処理を使用することで、ダウンストリーム アプリケーションごとにイベントに "完了" のマークを付けると共に、異なるコンピューター上で実行中のリーダー間でフェールオーバーが発生した場合に回復性をもたらすことができます。 このチェックポイント処理で、より小さなオフセットを指定すると、古いデータに戻ることができます。 このメカニズムにより、チェックポイント処理ではフェールオーバーの回復性とイベント ストリームの再生の両方を実現できます。

> [!IMPORTANT]
> オフセットは、Event Hubs サービスによって提供されます。 イベントが処理されるときにチェックポイント処理を行うのはコンシューマーの責任です。

> [!NOTE]
> Azure で一般公開されているものとは異なるバージョンの Storage Blob SDK をサポートする環境で、チェックポイント ストアとして Azure Blob Storage を使用している場合は、コードを使用して、Storage Service API バージョンをその環境でサポートされている特定のバージョンに変更する必要があります。 たとえば、[Azure Stack Hub バージョン 2002 上で Event Hubs](/azure-stack/user/event-hubs-overview) を実行している場合、Storage Service で利用可能な最も高いバージョンは 2017-11-09 です。 この場合は、コードを使用して、対象にする Storage Service API のバージョンを 2017-11-09 にする必要があります。 特定の Storage API バージョンを対象にする方法の例については、GitHub の次のサンプルを参照してください。 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/) 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventhub/eventhubs-checkpointstore-blob/samples/v1/javascript) または [TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventhub/eventhubs-checkpointstore-blob/samples/v1/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

### <a name="common-consumer-tasks"></a>一般的なコンシューマー タスク

すべての Event Hubs コンシューマーは、AMQP 1.0 セッション (状態に対応する双方向の通信チャネル) を介して接続します。 各パーティションには、パーティションによって分離されたイベントの転送を容易にする AMQP 1.0 セッションがあります。

#### <a name="connect-to-a-partition"></a>パーティションに接続する

パーティションに接続する場合は、特定のパーティションへのリーダーの接続を調整するためにリース メカニズムを使用するのが一般的です。 このため、コンシューマー グループ内のどのパーティションもアクティブなリーダーが 1 つだけである可能性があります。 チェックポイント処理、リース、およびリーダーの管理は、インテリジェントなコンシューマー エージェントとして機能する Event Hubs SDK 内のクライアントを使用して簡略化されます。 次のとおりです。

- .NET 用 [EventProcessorClient](/dotnet/api/azure.messaging.eventhubs.eventprocessorclient)
- Java 用 [EventProcessorClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/main/java/com/azure/messaging/eventhubs/EventProcessorClient.java)
- Python 用 [EventHubConsumerClient](/python/api/azure-eventhub/azure.eventhub.aio.eventhubconsumerclient)
- JavaScript/TypeScript 用 [EventHubConsumerClient](/javascript/api/@azure/event-hubs/eventhubconsumerclient)

#### <a name="read-events"></a>イベントを読み取る

特定のパーティションに対して AMQP 1.0 のセッションおよびリンクが開かれると、Event Hubs サービスによってイベントが AMQP 1.0 クライアントに配信されます。 この配信メカニズムでは、HTTP GET などのプル ベースのメカニズムよりも高いスループットおよび短い遅延時間を実現します。 イベントがクライアントに送信されるとき、イベント データの各インスタンスには、イベント シーケンスでのチェックポイント処理を容易にするために使用されるオフセットやシーケンス番号などの重要なメタデータが含まれます。

イベント データ:
* Offset
* Sequence number
* Body
* ユーザー プロパティ
* システム プロパティ

オフセットを管理するのはユーザーの責任になります。

## <a name="next-steps"></a>次のステップ

Event Hubs の詳細については、次のリンクを参照してください。

- Event Hubs の使用
    - [.NET](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
* [Event Hubs のプログラミング ガイド](event-hubs-programming-guide.md)
* [Event Hubs における可用性と一貫性](event-hubs-availability-and-consistency.md)
* [Event Hubs の FAQ](event-hubs-faq.yml)
* [Event Hubs サンプル](event-hubs-samples.md)
