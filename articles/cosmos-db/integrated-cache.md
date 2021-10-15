---
title: Azure Cosmos DB 統合キャッシュ
description: Azure Cosmos DB 統合キャッシュはインメモリ キャッシュで、要求のボリュームが拡大するのに伴い、コストを管理しやすくし、低遅延を実現するのに役立ちます。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/28/2021
ms.author: tisande
ms.openlocfilehash: ebf9eb5e06b98bdd573d91f0a57daeb9d81b1f50
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129230572"
---
# <a name="azure-cosmos-db-integrated-cache---overview-preview"></a>Azure Cosmos DB 統合キャッシュ - 概要 (プレビュー)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 統合キャッシュはインメモリ キャッシュで、要求のボリュームが拡大するのに伴い、コストを管理しやすくし、低遅延を実現するのに役立ちます。 統合キャッシュは簡単に設定できるため、キャッシュの無効化やバックエンド インフラストラクチャの管理のためのカスタム コードの作成に時間を費やす必要はありません。 統合キャッシュでは、Azure Cosmos DB アカウント内の[専用ゲートウェイ](dedicated-gateway.md)が使用されます。 統合キャッシュは、多くの Azure Cosmos DB 機能の中でも、パフォーマンスを向上させるために専用ゲートウェイを利用した最初の機能です。 自分のワークロードに必要なコアとメモリの数に基づいて、3 つの専用ゲートウェイ サイズから選択できます。

統合キャッシュは、専用ゲートウェイ内で自動的に構成されます。 統合キャッシュには、次の 2 つの部分があります。 

* ポイント読み取り用の項目キャッシュ 
* クエリ用のクエリ キャッシュ

統合キャッシュは、最も使われていない (LRU) 削除ポリシーを備えた、リードスルーとライトスルーのキャッシュです。 項目キャッシュとクエリ キャッシュでは、統合キャッシュ内で同じ容量が共有され、LRU 削除ポリシーは両方に適用されます。 言い換えると、データは、ポイント読み取りとクエリのどちらであるかに関係なく、最後に使用された日時に厳密に基づいてキャッシュから削除されます。

> [!NOTE]
> 統合キャッシュについてフィードバックはありますか? ご意見をお待ちしています。 ぜひ、Azure Cosmos DB エンジニアリング チーム (cosmoscachefeedback@microsoft.com) まで直接お寄せください。

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

クエリ キャッシュでは、クエリの後続トークンが自動的にキャッシュされます (該当する場合)。 結果が複数のページにわたるクエリがある場合、統合キャッシュに格納されているすべてのページで、RU 料金は 0 になります。 クエリ結果の後続のページでバックエンドでの実行が必要な場合、前のページからの後続トークンがあるため、前の作業の重複を避けることができます。

> [!NOTE]
> 異なる専用ゲートウェイ ノード内の統合キャッシュ インスタンスには、互いに独立したキャッシュが備わっています。 データは、1 つのノード内にキャッシュされている場合、他のノードにもキャッシュされているとは限りません。

## <a name="integrated-cache-consistency"></a>統合キャッシュの一貫性

統合キャッシュでは、セッションと最終的な[整合性](consistency-levels.md)の両方のみがサポートされます。 一貫したプレフィックス、有界整合性制約、厳密な一貫性が設定されている読み取りの場合、統合キャッシュは常にバイパスされます。

すべての読み取りのセッションまたは最終的な整合性のいずれかを構成する最も簡単な方法は、[アカウントレベルでこれを設定する](consistency-levels.md#configure-the-default-consistency-level)ことです。 ただし、一部の読み取りにのみ特定の整合性を設定する場合は、[要求レベル](how-to-manage-consistency.md#override-the-default-consistency-level)で整合性を構成することもできます。

### <a name="session-consistency"></a>"セッション" 整合性

[セッション整合性](consistency-levels.md#session-consistency)は、1 つのリージョンのアプリケーションと世界中に分散された Azure Cosmos DB アカウントの両方で最も広く使用されている整合性レベルです。 セッションの整合性を使用する場合、単一のクライアント セッションで独自の書き込みを読み取ることができます。 統合されたキャッシュを使用すると、書き込みを実行しているセッションの外部のクライアントは、最終的な整合性を確認できます。

## <a name="maxintegratedcachestaleness"></a>MaxIntegratedCacheStaleness

`MaxIntegratedCacheStaleness` は、選択した整合性に関係なく、キャッシュされたポイントの読み取りとクエリに対して許容される最大の staleness です。 `MaxIntegratedCacheStaleness` は、要求レベルで構成できます。 たとえば、`MaxIntegratedCacheStaleness` を 2 時間に設定した場合、データの経過時間が 2 時間未満であれば、要求からは、キャッシュされているデータのみが返されます。 繰り返し読み取りで統合キャッシュを使用する可能性を高めるには、`MaxIntegratedCacheStaleness` を、ビジネス要件で許容される限り最大の長さに設定する必要があります。

`MaxIntegratedCacheStaleness` は、最終的にキャッシュに保存する要求で構成された場合、その要求がキャッシュされる期間に影響を与えないことを理解することが重要です。 `MaxIntegratedCacheStaleness` により、キャッシュされたデータを使用しようとするときに整合性が適用されます。 グローバル TTL またはキャッシュ リテンション期間設定がないため、統合キャッシュがいっぱいであるか、現在キャッシュされているエントリの経過時間よりも短い `MaxIntegratedCacheStaleness` で新しい読み取りが実行された場合にのみ、データがキャッシュから削除されます。

これは、ほとんどのキャッシュの動作方法と比べて改善された点であり、次の追加のカスタマイズが可能になります。

- ポイント読み取りまたはクエリごとに異なる失効期限要件を設定できます。
- さまざまなクライアントが同じポイント読み取りまたはクエリを実行する場合でも、クライアントでは、異なる `MaxIntegratedCacheStaleness` 値を構成できます。
- キャッシュされたデータを使用するときに読み取りの整合性を変更した場合、`MaxIntegratedCacheStaleness` を変更すると読み取りの整合性に即座に影響します。

> [!NOTE]
> 明示的に構成されない場合、MaxIntegratedCacheStaleness の既定値は 5 分です。

`MaxIntegratedCacheStaleness` パラメーターをより深く理解するために、次の例について考えてみましょう。

| 時刻       | Request                                         | Response                                                     |
| ---------- | ----------------------------------------------- | ------------------------------------------------------------ |
| t = 0 sec  | MaxIntegratedCacheStaleness = 30 秒でクエリ A を実行する | バックエンド データベースから結果 (通常の RU 料金) が返され、キャッシュに設定される     |
| t = 0 sec  | MaxIntegratedCacheStaleness = 60 秒でクエリ B を実行する | バックエンド データベースから結果 (通常の RU 料金) が返され、キャッシュに設定される     |
| t = 20 sec | MaxIntegratedCacheStaleness = 30 秒でクエリ A を実行する | 統合キャッシュから結果 (0 RU 料金) が返される           |
| t = 20 sec | MaxIntegratedCacheStaleness = 60 秒でクエリ B を実行する | 統合キャッシュから結果 (0 RU 料金) が返される           |
| t = 40 sec | MaxIntegratedCacheStaleness = 30 秒でクエリ A を実行する | バックエンド データベースから結果 (通常の RU 料金) が返され、キャッシュが更新される |
| t = 40 sec | MaxIntegratedCacheStaleness = 60 秒でクエリ B を実行する | 統合キャッシュから結果 (0 RU 料金) が返される           |
| t = 50 sec | MaxIntegratedCacheStaleness = 20 秒でクエリ B を実行する | バックエンド データベースから結果 (通常の RU 料金) が返され、キャッシュが更新される |

> [!NOTE]
> `MaxIntegratedCacheStaleness` のカスタマイズは、最新の .NET および Java プレビュー SDK でのみサポートされます。

[`MaxIntegratedCacheStaleness` の構成の詳細情報。](how-to-configure-integrated-cache.md#adjust-maxintegratedcachestaleness)

## <a name="metrics"></a>メトリック

統合キャッシュを使用する場合、いくつかの主要なメトリックを監視すると便利です。 統合キャッシュのメトリックには次のものが含まれます。

- `DedicatedGatewayAverageCpuUsage` - 専用ゲートウェイ ノード全体の平均 CPU 使用率。
- `DedicatedGatewayMaxCpuUsage` - 専用ゲートウェイ ノード全体の最大 CPU 使用率。
- `DedicatedGatewayAverageMemoryUsage` - 要求のルーティングとデータのキャッシュの両方に使用されている、専用ゲートウェイ ノード全体の平均メモリ使用量。
- `DedicatedGatewayRequests` - すべての専用ゲートウェイ インスタンス全体の専用ゲートウェイ要求の総数。
- `IntegratedCacheEvictedEntriesSize` - 専用ゲートウェイ ノード全体で、LRU が原因で統合キャッシュから削除された平均データ量。 これには、`MaxIntegratedCacheStaleness` 時間を超過したために期限切れになったデータは含まれません。
- `IntegratedCacheItemExpirationCount` - キャッシュされているポイント読み取りが `MaxIntegratedCacheStaleness` 時間を超過したために統合キャッシュから削除された項目の数。 この値は、すべての専用ゲートウェイ ノード全体の統合キャッシュ インスタンスの平均です。
- `IntegratedCacheQueryExpirationCount` - キャッシュされているクエリが `MaxIntegratedCacheStaleness` 時間を超過したために統合キャッシュから削除されたクエリの数。 この値は、すべての専用ゲートウェイ ノード全体の統合キャッシュ インスタンスの平均です。
- `IntegratedCacheItemHitRate` - (セッションの整合性または最終的な整合性を保って専用ゲートウェイを経由したすべてのポイント読み取り数のうち) 統合キャッシュを使用したポイント読み取りの割合。 この値は、すべての専用ゲートウェイ ノード全体の統合キャッシュ インスタンスの平均です。
- `IntegratedCacheQueryHitRate` - (セッションの整合性最終的な整合性を保って専用ゲートウェイを経由したすべてのクエリ数のうち) 統合キャッシュを使用したクエリの割合。 この値は、すべての専用ゲートウェイ ノード全体の統合キャッシュ インスタンスの平均です。

既定では、([メトリック (クラシック)] ではなく) **[メトリック]** ブレードから、既存のすべてのメトリックを使用できます。

   :::image type="content" source="./media/integrated-cache/integrated-cache-metrics.png" alt-text="統合キャッシュのメトリックの場所を示す画像" border="false":::

メトリックは、すべての専用ゲートウェイ ノード全体の平均、最大、または合計のいずれかです。 たとえば、5 つのノードが含まれる専用ゲートウェイ クラスターをプロビジョニングする場合、メトリックには 5 つのすべてのノード全体の集計値が反映されます。 個々のノードのメトリック値を決定することはできません。

## <a name="troubleshooting-common-issues"></a>一般的な問題のトラブルシューティング

次の例は、いくつかの一般的なシナリオでのデバッグ方法を示しています。

### <a name="i-cant-tell-if-my-application-is-using-the-dedicated-gateway"></a>アプリケーションが専用ゲートウェイを使用しているかどうかを判断できない

`DedicatedGatewayRequests` を確認します。 このメトリックには、統合キャッシュにヒットするかどうかに関係なく、専用ゲートウェイを使用するすべての要求が含まれています。 アプリケーションで標準ゲートウェイまたは直接モードが元の接続文字列と共に使用されている場合、エラー メッセージは表示されませんが、`DedicatedGatewayRequests` は 0 になります。

### <a name="i-cant-tell-if-my-requests-are-hitting-the-integrated-cache"></a>要求が統合キャッシュにヒットしているどうかを判断できない

`IntegratedCacheItemHitRate` と `IntegratedCacheQueryHitRate` を確認します。 これらの値が両方とも 0 の場合、要求は統合キャッシュにヒットしていません。 専用ゲートウェイの接続文字列を使用していること、[ゲートウェイ モードで接続している](sql-sdk-connection-modes.md)こと、[セッションまたは最終的な整合性が設定されている](consistency-levels.md#configure-the-default-consistency-level)ことをご確認ください。

### <a name="i-want-to-understand-if-my-dedicated-gateway-is-too-small"></a>専用ゲートウェイが小さすぎるかどうかを確認したい

`IntegratedCacheItemHitRate` と `IntegratedCacheQueryHitRate` を確認します。 この値が大きい場合 (たとえば、0.7 から 0.8 より大きい場合)、専用ゲートウェイは十分な大きさであると判断できます。

`IntegratedCacheItemHitRate` または `IntegratedCacheQueryHitRate` が小さい場合は、`IntegratedCacheEvictedEntriesSize` を確認します。 `IntegratedCacheEvictedEntriesSize` が高い場合は、専用ゲートウェイのサイズを大きくした方がよい可能性があります。 実験するには、専用ゲートウェイのサイズを増やし、新しい `IntegratedCacheItemHitRate` および `IntegratedCacheQueryHitRate` を比較します。 専用ゲートウェイのサイズを大きくしても、`IntegratedCacheItemHitRate` または `IntegratedCacheQueryHitRate` が改善されない場合、統合キャッシュが効果を発揮するのに十分な回数、読み取りが繰り返されないことがあります。

### <a name="i-want-to-understand-if-my-dedicated-gateway-is-too-large"></a>専用ゲートウェイが大きすぎるかどうかを確認したい

専用ゲートウェイが大きすぎるかどうかを測定するのは、専用ゲートウェイが小さすぎるかどうかを測定するよりも困難です。 一般に、専用ゲートウェイのサイズは、小さい状態から始めて、`IntegratedCacheItemHitRate` および `IntegratedCacheQueryHitRate` が改善されなくなるまで徐々に大きくしていきます。 場合によっては、2 つのキャッシュ ヒット メトリックの両方ではなく、一方のみが重要になることがあります。 たとえば、ワークロードがポイント読み取りではなく主にクエリである場合、`IntegratedCacheQueryHitRate` の方が `IntegratedCacheItemHitRate` よりもはるかに重要です。

LRU ではなく `MaxIntegratedCacheStaleness` を超過したことが原因でほとんどのデータがキャッシュから削除される場合は、キャッシュが必要以上に大きい可能性があります。 `IntegratedCacheItemExpirationCount` および `IntegratedCacheQueryExpirationCount` の合計が `IntegratedCacheEvictedEntriesSize` とほぼ同じ大きさの場合は、専用ゲートウェイのサイズを小さくして実験を行い、パフォーマンスを比較できます。

### <a name="i-want-to-understand-if-i-need-to-add-more-dedicated-gateway-nodes"></a>専用ゲートウェイ ノードを追加する必要があるかどうかを知りたい

場合によっては、待機時間が予想外に長い場合、専用ゲートウェイ ノードのサイズではなく、数を増加することが必要になります。 `DedicatedGatewayMaxCpuUsage` と `DedicatedGatewayAverageMemoryUsage` を調べて、専用ゲートウェイ ノードを追加すると、待機時間が短縮されるかどうかを確認します。 統合キャッシュのすべてのインスタンスは互いに独立しているため、専用ゲートウェイ ノードを追加しても `IntegratedCacheEvictedEntriesSize` は減少しないことに留意してください。 ただし、ノードを追加すると、専用ゲートウェイ クラスターが処理できる要求量が向上します。

## <a name="next-steps"></a>次の手順

- [統合キャッシュに関する FAQ](integrated-cache-faq.md)
- [統合キャッシュの構成](how-to-configure-integrated-cache.md)
- [専用ゲートウェイ](dedicated-gateway.md)
- Azure Cosmos DB への移行のための容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
    - 知っていることが既存のデータベース クラスター内の仮想コアとサーバーの数のみである場合は、[仮想コアまたは仮想 CPU の数を使用した要求ユニットの見積もり](convert-vcore-to-request-unit.md)に関するページを参照してください 
    - 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](estimate-ru-with-capacity-planner.md)に関するページを参照してください