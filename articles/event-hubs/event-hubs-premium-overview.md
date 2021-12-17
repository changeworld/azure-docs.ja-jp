---
title: Event Hubs Premium の概要
description: この記事では、ハイエンドのストリーミングのニーズに対応するために Event Hubs のマルチテナント デプロイを提供する Azure Event Hubs Premium の概要を説明します。
ms.topic: article
ms.date: 10/20/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8822b44f5f2a6d5cbc367da9a8ac5d397f87e9f7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131073018"
---
# <a name="overview-of-event-hubs-premium"></a>Event Hubs Premium の概要

Event Hubs Premium レベルは、予測可能な待機時間を伴うエラスティックで優れたパフォーマンスを必要とする、ハイエンド ストリーミング シナリオ向けに設計されています。 パフォーマンスは、予約済みのコンピューティング、メモリ、ストレージ リソースを提供することで実現され、これによりマネージド マルチテナント PaaS 環境でのテナント間の干渉が最小限に抑えられます。 

Event Hubs Premium では、耐久性を損なうことなく、以前の世代よりもはるかに予測可能で、送信とパススルーの待機時間を大幅に短縮する、新しい 2 層のネイティブ コード ログ エンジンが導入されています。 Event Hubs Premium では、すべてのイベントが 3 つのレプリカにレプリケートされ、使用可能な場合は Azure 可用性ゾーンに分散されます。すべてのレプリカは、送信操作が完了したと報告される前に、基になる高速ストレージに同期的にフラッシュされます。 すぐに読み取られないイベント、または後で再読み取りが必要なイベントは、最大 90 日間保持され、可用性ゾーンの冗長ストレージ層に透過的に保持できます。 高速ストレージ層とデータ保持ストレージ層の両方のイベントは暗号化されます。Event Hubs Premium では、暗号化キーを指定することができます。 

これらのストレージ関連の機能と、Event Hubs Standard オファリングのすべての機能およびプロトコルのサポートに加えて、Event Hubs Premium の分離モデルを使用すると、動的パーティションのスケールアップやまだ追加されていない将来的な機能などの新機能が可能になります。 また、クォータの割り当て量がはるかに多くなります。 Event Hubs Capture は追加コストなしで含まれます。

Premium オファリングは、基になるインフラストラクチャ内の分離されたリソース (CPU、メモリ、ストレージ) の共有に対応する[処理ユニット (PU)](event-hubs-scalability.md#processing-units) によって請求されます。 

専用オファリングと比較して、Event Hubs Premium は、リソースをすばやくシフトできる非常に大規模なマルチテナント環境内で分離を提供するため、はるかにエラスティックかつ迅速にスケーリングでき、PU を動的に調整できます。 そのため、Event Hubs Premium は、Event Hubs Dedicated と比較した場合に、特に 1 日または 1 週間を通して負荷が変化する状況では、ミッドレンジ (<120 MB/sec) のスループット要件に対してよりコスト効率の高いオプションとなることがよくあります。 
> [!NOTE]
> Event Hubs Premium では TLS 1.2 以上のみがサポートされることに注意してください。 

可用性ゾーンのサポートによって得られる堅牢性を高めるために、Event Hubs Dedicated の最小デプロイ スケールは 8 容量ユニット (CU) ですが、Event Hubs Premium では、すべての AZ リージョンで最初の PU から可用性ゾーンがサポートされます。 

名前空間ごとに 1、2、4、8、または 16 の処理ユニットを購入できます。 Event Hubs Premium は容量ベースのオファリングであるため、達成可能なスループットは、Event Hubs Standard のようにスロットルによって設定されるのではなく、Event Hubs Dedicated と同様に、Event Hubs に要求する作業によって異なります。 PU ごとの有効な取り込みとストリームのスループットは、次のようなさまざまな要因によって異なります。

* プロデューサーとコンシューマーの数
* ペイロードのサイズ 
* [パーティション数]
* エグレス要求率 
* Event Hubs Capture、スキーマ レジストリ、その他の高度な機能の使用

詳細については、[Event Hubs の SKU 間の比較](event-hubs-quotas.md)に関する記事を参照してください。


> [!NOTE]
> 既定では、すべての Event Hubs 名前空間が Apache Kafka RPC プロトコルに対して有効になっており、既存の Kafka ベースのアプリケーションで使用することができます。 クラスターで Kafka を有効にしても、Kafka 以外のユース ケースには影響しません。クラスターで Kafka を無効にするオプションはなく、無効にする必要もありません。

## <a name="why-premium"></a>Premium を選択する理由

Premium Event Hubs には、低待機時間と高スループットのデータ インジェストのニーズがあるマルチテナント環境で分離性を向上させる必要があるお客様に、3 つの魅力的な利点があります。

#### <a name="superior-performance-with-the-new-two-tier-storage-engine"></a>新しい 2 層のストレージ エンジンによる優れたパフォーマンス

Event Hubs Premium では、新しい 2 層のログ ストレージ エンジンを使用して、持続性の保証を損なうことなく、全体的な待機時間と待機時間のジッターを大幅に短縮して、データ イングレスのパフォーマンスを大幅に向上させます。 

#### <a name="better-isolation-and-predictability"></a>分離と予測可能性の向上

Event Hubs Premium は、分離されたコンピューティングとメモリ容量を提供することで、マルチテナント デプロイでより予測可能な待機時間を実現し、"*うるさい隣人*" の影響リスクを大幅に軽減します。

Event Hubs Premium では、マルチテナント クラスターに "*クラスター イン クラスター*" モデルが実装され、マネージド マルチテナント PaaS 環境のすべての利点を維持しながら、予測可能性とパフォーマンスが提供されます。 


#### <a name="cost-savings-and-scalability"></a>コスト削減とスケーラビリティ
Event Hubs Premium はマルチテナント オファリングであるため、より柔軟かつ迅速に動的にスケーリングできます。 容量は、クラスター内の CPU とメモリの分離されたポッドを割り当てる処理ユニットに割り当てられます。 これらのポッドの数は、名前空間ごとにスケールアップまたはスケールダウンできます。 したがって、Event Hubs Premium は、全体のスループット範囲が 120 MB/s 未満であるが、Standard SKU で達成できるものよりも高いメッセージング シナリオ向けの低コスト オプションです。  

## <a name="quotas-and-limits"></a>クォータと制限
Premium レベルでは、Standard プランのすべての機能が提供される一方で、より優れたパフォーマンスや分離、十分なクォータが提供されます。 クォータと制限に関する詳細については、[Event Hubs のクォータと制限](event-hubs-quotas.md)に関する記事を参照してください


## <a name="faqs"></a>FAQ

[!INCLUDE [event-hubs-dedicated-clusters-faq](./includes/event-hubs-premium-faq.md)]

## <a name="next-steps"></a>次のステップ

Event Hubs Premium は、[Azure portal](https://portal.azure.com/#create/Microsoft.EventHub) から使用を開始できます。 価格設定の詳細については [Event Hubs Premium の価格設定](https://azure.microsoft.com/pricing/details/event-hubs/)に関する記事を、Event Hubs に関してよく寄せられる質問に対する回答を見つけるには [Event Hubs に関する FAQ](event-hubs-faq.yml) をご覧ください。 
