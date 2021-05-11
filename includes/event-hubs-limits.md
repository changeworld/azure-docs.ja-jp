---
title: include file description: include file services: event-hubs author: spelluru ms.service: event-hubs ms.topic: include ms.date: 03/31/2021 ms.author: spelluru ms.custom: "include file","fasttrack-edit","iot","event-hubs"

---

次の表に、[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) に固有のクォータと制限を示します。 Event Hubs の価格については、「[Event Hubs の価格](https://azure.microsoft.com/pricing/details/event-hubs/)」を参照してください。

### <a name="common-limits-for-all-tiers"></a>すべてのレベルに共通の制限
次の制限は、すべてのレベルで共通です。 

| 制限 |  Notes | 値 |
| --- |  --- | --- |
 イベント ハブ名のサイズ |- | 256 文字 |
| コンシューマー グループ名のサイズ | Kafka プロトコルでは、コンシューマー グループを作成する必要はありません。 | <p>Kafka: 256 文字</p><p>AMQP: 50 文字 |
| コンシューマー グループあたりの非エポック受信者の数 |- |5 |
| 名前空間ごとの承認規則の数 | 上限を超えると承認規則の作成要求が拒否されます。|12 |
| GetRuntimeInformation メソッドの呼び出し数 |  - | 1 秒あたり 50 | 
| 仮想ネットワーク (VNet) の数 | - | 128 | 
| IP 構成規則の数 | - | 128 | 
| スキーマ グループ名の最大長 | | 50 |  
| スキーマ名の最大長 | | 100 |    
| スキーマあたりのサイズ (バイト) | | 1 MB |   
| スキーマ グループあたりのプロパティの数 | | 1024 |
| スキーマ グループ プロパティ キーあたりのサイズ (バイト) | | 256 | 
| スキーマ グループ プロパティ値あたりのサイズ (バイト) | | 1024 | 

### <a name="basic-vs-standard-vs-dedicated-tiers"></a>Basic、Standard、Dedicated の各レベルの比較
次の表は、Basic、Standard、Dedicated の各レベルで異なる可能性がある制限を示しています。 この表で、CU は[容量ユニット](../articles/event-hubs/event-hubs-dedicated-overview.md)、TU は[スループット ユニット](../articles/event-hubs/event-hubs-faq.yml#what-are-event-hubs-throughput-units-)です。 

| 制限 | Basic | Standard | 専用 |
| ----- | ----- | -------- | -------- | 
| Event Hubs 発行の最大サイズ | 256 KB | 1 MB | 1 MB |
| イベント ハブあたりのコンシューマー グループの数 | 1 | 20 | CU あたりの制限なし、イベント ハブあたり 1,000 |
| 名前空間あたりの AMQP 接続の数 | 100 | 5,000 | 100,000 (付属、最大) |
| イベント データの最大リテンション期間 | 1 日 | 1 ～ 7 日間 | 90 日、CU あたり 10 TB を含む |
| 最大 TU または CU |20 TU | 20 TU | 20 CU |
| イベント ハブあたりのパーティションの数 | 32 | 32 | イベント ハブあたり 1,024
CU あたり 2,000 |
| サブスクリプションあたりの名前空間の数 | 100 | 100 | 100 (CU あたり 50) |
| 名前空間あたりのイベント ハブの数 | 10 | 10 | 1000 |
| イングレス イベント | | 100 万イベントごとの課金 | Included|
| キャプチャ | なし | 1 時間ごとの課金 | Included |
| スキーマ レジストリ (名前空間) のサイズ (MB) | なし | 25 |  1024 |
| スキーマ レジストリまたは名前空間内のスキーマ グループの数 | なし | 1 (既定のグループは除く) | 1000 |
| スキーマ グループ全体におけるスキーマ バージョンの数 | なし | 25 | 10000 |

> [!NOTE]
> イベントは、個別に発行することもバッチ処理することもできます。 発行の制限 (SKU に基づく) は、1 つのイベントであるかバッチであるかに関係なく適用されます。 最大しきい値を超えるイベントの発行は拒否されます。

