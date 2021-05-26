---
title: Azure Cosmos DB 統合キャッシュ
description: Azure Cosmos DB 統合キャッシュはインメモリ キャッシュで、要求のボリュームが拡大するのに伴い、コストを管理しやすくし、低遅延を実現するのに役立ちます。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/25/2021
ms.author: tisande
ms.openlocfilehash: f0a0556ce2a46f922e387d96d20b6425ab362580
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386444"
---
# <a name="azure-cosmos-db-integrated-cache---overview-preview"></a>Azure Cosmos DB 統合キャッシュ - 概要 (プレビュー)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 統合キャッシュはインメモリ キャッシュで、要求のボリュームが拡大するのに伴い、コストを管理しやすくし、低遅延を実現するのに役立ちます。 統合キャッシュは簡単に設定できるため、キャッシュの無効化やバックエンド インフラストラクチャの管理のためのカスタム コードの作成に時間を費やす必要はありません。 統合キャッシュでは、Azure Cosmos DB アカウント内の[専用ゲートウェイ](dedicated-gateway.md)が使用されます。 統合キャッシュは、多くの Azure Cosmos DB 機能の中でも、パフォーマンスを向上させるために専用ゲートウェイを利用した最初の機能です。 自分のワークロードに必要なコアとメモリの数に基づいて、3 つの専用ゲートウェイ サイズから選択できます。

統合キャッシュは、専用ゲートウェイ内で自動的に構成されます。 統合キャッシュには、次の 2 つの部分があります。 

* ポイント読み取り用の項目キャッシュ 
* クエリ用のクエリ キャッシュ

統合キャッシュは、最も使われていない (LRU) 削除ポリシーを備えた、リードスルーとライトスルーのキャッシュです。 項目キャッシュとクエリ キャッシュでは、統合キャッシュ内で同じ容量が共有され、LRU 削除ポリシーは両方に適用されます。 言い換えると、データは、ポイント読み取りとクエリのどちらであるかに関係なく、最後に使用された日時に厳密に基づいてキャッシュから削除されます。

## <a name="workloads-that-benefit-from-the-integrated-cache"></a>統合キャッシュから利点を得られるワークロード

統合キャッシュの主な目的は、読み取り負荷の高いワークロードのコストを削減することです。 Azure Cosmos DB ならキャッシュなしでも既に高速であるため、低遅延であることは、便利ではありますが統合キャッシュの主な利点ではありません。

統合キャッシュにヒットするポイント読み取りとクエリでは、RU が一切使用されません。 つまり、キャッシュ ヒットの RU 料金は 0 になります。 バックエンド データベースからの読み取りに比べて、キャッシュ ヒットでは操作あたりのコストがはるかに低くなります。

次の特性に一致するワークロードでは、統合キャッシュがコストの削減に役立つかどうかを評価する必要があります。

-   読み取り負荷の高いワークロード
-   大きな項目に対して繰り返される多数のポイント読み取り
-   繰り返される多数の高 RU クエリ
-   読み取り用のホット パーティション キー

期待される削減効果の中でも最大の要素は、読み取りが繰り返される度合いです。 ワークロードで、同じポイント読み取りまたはクエリが短時間で一貫して実行される場合は、統合キャッシュの候補として最適です。 繰り返される読み取りに統合キャッシュを使用する場合は、最初の読み取りに RU を使用するだけです。 同じ専用ゲートウェイ ノードを介してルーティングされる後続の読み取り (`MaxIntegratedCacheStaleness` 期間内にあり、データが削除されていない場合) では、スループットは使用されません。

次のような一部のワークロードでは、統合キャッシュを考慮すべきではありません。

-   書き込み負荷の高いワークロード
-  めったに繰り返されないポイント読み取りまたはクエリ

## <a name="item-cache"></a>項目キャッシュ

ポイント読み取りには項目キャッシュを使用できます (つまり、項目 ID とパーティション キーに基づくキーと値の参照)。

### <a name="populating-the-item-cache"></a>項目キャッシュの設定

- 新しい書き込み、更新、および削除は、項目キャッシュに自動的に設定されます
- 以前はキャッシュになかった特定の項目 (キャッシュ ミス) をアプリが読み取ろうとすると、その項目は項目キャッシュに格納されるようになりました

### <a name="item-cache-invalidation-and-eviction"></a>項目キャッシュの無効化と削除

- 項目の更新または削除
- 最も使われていない (LRU)
- キャッシュの保有時間 (つまり `MaxIntegratedCacheStaleness` )

## <a name="query-cache"></a>クエリ キャッシュ

クエリ キャッシュは、クエリをキャッシュするために使用できます。 クエリ キャッシュにより、クエリはキーと値の参照に変換されます。この場合、キーはクエリ テキスト、値はクエリ結果になります。 統合キャッシュにはクエリ エンジンがなく、各クエリのキーと値の参照のみが格納されます。

### <a name="populating-the-query-cache"></a>クエリ キャッシュの設定

- キャッシュにそのクエリの結果がない場合 (キャッシュミス)、そのクエリはバックエンドに送信されます。 クエリが実行された後、そのクエリの結果がキャッシュに格納されます

### <a name="query-cache-eviction"></a>クエリ キャッシュの削除

- 最も使われていない (LRU)
- キャッシュの保有時間 (つまり `MaxIntegratedCacheStaleness` )

### <a name="working-with-the-query-cache"></a>クエリ キャッシュの操作

クエリの結果が複数のページにわたる場合でも、クエリ キャッシュを操作する際に特別なコードは不要です。 クエリが統合キャッシュにヒットするか、バックエンドのクエリ エンジンで実行されているかにかかわらず、クエリの改ページのベスト プラクティスとコードは同じです。

クエリ キャッシュでは、クエリの後続トークンが自動的にキャッシュされます (該当する場合)。 結果が複数のページにわたるクエリがある場合、統合キャッシュに格納されているすべてのページで、RU 料金は 0 になります。 クエリ結果の後続のページでバックエンドでの実行が必要な場合でも、前のページからの後続トークンがあるため、前の作業の重複を避けることができます。

> [!NOTE]
> 異なる専用ゲートウェイ ノード内の統合キャッシュ インスタンスには、互いに独立したキャッシュが備わっています。 データは、1 つのノード内にキャッシュされている場合、他のノードにもキャッシュされているとは限りません。

## <a name="integrated-cache-consistency"></a>統合キャッシュの一貫性

統合キャッシュでは、最終的な[整合性](consistency-levels.md)のみがサポートされます。 一貫したプレフィックス、セッション、有界整合性制約、厳密な一貫性が設定されている読み取りの場合、統合キャッシュは常にバイパスされます。

すべての読み取りの最終的な整合性を構成する最も簡単な方法は、[アカウントレベルでこれを設定する](consistency-levels.md#configure-the-default-consistency-level)ことです。 ただし、一部の読み取りにのみ最終的な整合性を設定する場合は、[要求レベル](how-to-manage-consistency.md#override-the-default-consistency-level)で整合性を構成することもできます。

## <a name="integrated-cache-retention-time"></a>統合キャッシュの保有時間

キャッシュの保有時間とは、キャッシュ データの最大保有期間です。 プレビュー期間中、`MaxIntegratedCacheStaleness` は常に 5 分に設定され、カスタマイズはできません。

## <a name="metrics"></a>メトリック

統合キャッシュを使用する際は、いくつかの主要なメトリックを監視すると便利です。 統合キャッシュのメトリックには次のものが含まれます。

- `DedicatedGatewayCpuUsage` - 各専用ゲートウェイ ノードによる CPU 使用率
- `DedicatedGatewayMemoryUsage` - 要求のルーティングとキャッシュの両方に対する各専用ゲートウェイ ノードによるメモリ使用量
- `DedicatedGatewayRequests` - 各専用ゲートウェイ ノードを介してルーティングされる要求の数
- `IntegratedCacheEvictedEntriesSize` - 統合キャッシュから削除されたデータの量
- `IntegratedCacheTTLExpirationCount` - 特に `MaxIntegratedCacheStaleness` の時間を超えたキャッシュ データが原因で統合キャッシュから削除されたエントリの数。
- `IntegratedCacheHitRate` - 統合キャッシュを使用したポイント読み取りとクエリの比率 (統合キャッシュの使用を試行したすべての専用ゲートウェイ要求から算出)。

既定では、([メトリック (クラシック)] ではなく) **[メトリック]** ブレードから、既存のすべてのメトリックを使用できます。

   :::image type="content" source="./media/integrated-cache/integrated-cache-metrics.png" alt-text="統合キャッシュのメトリックの場所を示す画像" border="false":::

メトリックはすべて、専用ゲートウェイ ノードすべての平均として公開されます。 たとえば、5 つのノードが含まれる専用ゲートウェイ クラスターをプロビジョニングする場合、メトリックには 5 つのノードすべてにわたる平均値が反映されます。

## <a name="troubleshooting-common-issues"></a>一般的な問題のトラブルシューティング

次の例は、いくつかの一般的なシナリオでのデバッグ方法を示しています。

### <a name="i-cant-tell-if-my-application-is-using-the-dedicated-gateway"></a>アプリケーションが専用ゲートウェイを使用しているかどうかを判断できない

`DedicatedGatewayRequests` を確認します。 このメトリックには、統合キャッシュにヒットするかどうかに関係なく、専用ゲートウェイを使用するすべての要求が含まれています。 アプリケーションで標準ゲートウェイまたは直接モードが元の接続文字列と共に使用されている場合、エラー メッセージは表示されませんが、`DedicatedGatewayRequests` は 0 になります。

### <a name="i-cant-tell-if-my-requests-are-hitting-the-integrated-cache"></a>要求が統合キャッシュにヒットしているどうかを判断できない

`IntegratedCacheHitRate` を確認します。 この値が 0 の場合、要求は統合キャッシュにヒットしていません。 専用ゲートウェイの接続文字列を使用していること、ゲートウェイ モードで接続していること、最終的な整合性が設定されていることを確認してください。

### <a name="i-want-to-understand-if-my-dedicated-gateway-is-too-small"></a>専用ゲートウェイが小さすぎるかどうかを確認したい

`IntegratedCacheHitRate` を確認します。 この値が大きければ (0.5 から 0.6 より大きい場合など)、専用ゲートウェイは十分な大きさであると判断できます。

`IntegratedCacheHitRate` が低い場合は、`IntegratedCacheEvictedEntriesSize` を確認してください。 `IntegratedCacheEvictedEntriesSize` が高い場合は、専用ゲートウェイのサイズを大きくした方がよい可能性があります。

### <a name="i-want-to-understand-if-my-dedicated-gateway-is-too-large"></a>専用ゲートウェイが大きすぎるかどうかを確認したい

これを測定することは、より困難です。 一般に、専用ゲートウェイのサイズは、小さい状態から始めて、`IntegratedCacheHitRate` が高くならなくなるまで徐々に大きくしてください。

LRU ではなく `MaxIntegratedCacheStaleness` を超過したことが原因でほとんどのデータがキャッシュから削除される場合は、キャッシュが必要以上に大きい可能性があります。 `IntegratedCacheTTLExpirationCount` の大きさが `IntegratedCacheEvictedEntriesSize` とほぼ同じであるかどうかを確認します。 そうであった場合、より小さい専用ゲートウェイ サイズで試して、パフォーマンスを比較することができます。

`DedicatedGatewayMemoryUsage` を確認し、専用ゲートウェイのサイズと比較します。 `DedicatedGatewayMemoryUsage` が専用ゲートウェイのサイズよりも小さい場合は、専用ゲートウェイのサイズを小さくしてみることをお勧めします。

## <a name="next-steps"></a>次の手順

- [統合キャッシュに関する FAQ](integrated-cache-faq.md)
- [統合キャッシュの構成](how-to-configure-integrated-cache.md)
- [専用ゲートウェイ](dedicated-gateway.md)