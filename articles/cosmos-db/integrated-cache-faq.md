---
title: Azure Cosmos DB 統合キャッシュについてよく寄せられる質問
description: Azure Cosmos DB 統合キャッシュについてよく寄せられる質問。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/20/2021
ms.author: tisande
ms.openlocfilehash: a8d1dc69ef64726f55dffd20221a19c3f3114964
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595144"
---
# <a name="azure-cosmos-db-integrated-cache-frequently-asked-questions"></a>Azure Cosmos DB 統合キャッシュについてよく寄せられる質問
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 統合キャッシュは、Azure Cosmos DB に組み込まれているインメモリ キャッシュです。 この記事では、Azure Cosmos DB 統合キャッシュについてよく寄せられる質問に回答します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="why-does-the-integrated-cache-require-a-dedicated-gateway"></a>統合キャッシュに専用ゲートウェイが必要なのはなぜですか?

ゲートウェイ モードを使用して Azure Cosmos DB に接続している場合は、標準ゲートウェイを使用しています。 Azure Cosmos DB バックエンド (プロビジョニング済みのスループットとストレージ) ではコンテナーごとに専用容量が備わっていますが、標準ゲートウェイは多くのお客様の間で共有されます。 個々のお客様によって消費されるコンピューティング リソースは最小限であるため、多くのお客様が標準ゲートウェイを共有するのは実用的です。 統合キャッシュは Azure Cosmos DB アカウントに固有であり、大量の CPU とメモリを必要とするため、専用ゲートウェイ ノードが必要です。

### <a name="what-is-a-dedicated-gateway"></a>専用ゲートウェイとは何ですか?

[専用ゲートウェイ](dedicated-gateway.md)は、Azure Cosmos DB アカウント内にあるデータのフロントエンドであるサーバー側のコンピューティングです。 専用ゲートウェイ エンドポイントに接続すると、その専用ゲートウェイに対してアプリケーションから要求が送信され、その要求が別のバックエンド パーティションにルーティングされます。

### <a name="does-using-the-dedicated-gateway-offer-any-other-performance-benefits-over-using-the-standard-gateway"></a>専用ゲートウェイを使用すると、標準ゲートウェイを使用した場合よりもパフォーマンス上の利点が得られますか?

一般に、専用ゲートウェイによってルーティングされた要求の遅延は、標準ゲートウェイによってルーティングされた要求よりも若干低く、一貫性があります。 統合キャッシュを使用しない要求でも、標準ゲートウェイよりわずかに低遅延となります。

### <a name="what-kind-of-latency-should-i-expect-from-the-integrated-cache"></a>統合キャッシュでは、どのような待ち時間の種類が予想されますか?

キャッシュ データがバックエンドではなく専用ゲートウェイにインメモリで格納されるため、統合キャッシュによって提供される要求の方が高速になります。 キャッシュされたポイント読み取りでは、予想される待ち時間は 2 から 4 ミリ秒です。

キャッシュされたクエリの場合、待ち時間はそのクエリに依存します。 クエリ キャッシュは、特定のクエリに対するクエリ エンジンの応答をキャッシュすることで機能します。 その後、この応答は、処理のためにクライアント側で SDK に返されます。 単純なクエリの場合、SDK で必要な処理が最小限であるため、待ち時間は 2 から 4 ミリ秒が一般的です。 ただし、`GROUP BY` または `DISTINCT` を使用するより複雑なクエリでは、SDK でさらに多くの処理が必要になるため、クエリ キャッシュを使用した場合でも待ち時間が長くなる可能性があります。

以前に直接モードで Azure Cosmos DB に接続していて、専用ゲートウェイでの接続に切り替える場合は、一部の要求の待ち時間がわずかに長くなる可能性があります。 ゲートウェイ モードを使用するには、要求をゲートウェイ (この場合は専用ゲートウェイ) に送信し、バックエンドに適切にルーティングする必要があります。 直接モードでは、その名前のとおり、クライアントがバックエンドと直接通信でき、余分なホップは削除されます。 

自分のアプリで以前に直接モードを使用していた場合、統合キャッシュの待ち時間の利点は、次のシナリオでのみ顕著に表れます。

- 大きな項目 (16 KB より大きい) のポイント読み取りの待ち時間
- 高 RU または複雑なクエリ

自分のアプリで以前に標準ゲートウェイによるゲートウェイ モードを使用していた場合、統合キャッシュを使用すると、ほぼすべてのシナリオで待ち時間が短縮されます。 

### <a name="does-the-azure-cosmos-db-availability-sla-extend-to-the-dedicated-gateway-and-integrated-cache"></a>Azure Cosmos DB の可用性 SLA は、専用ゲートウェイと統合キャッシュまで拡張されますか?

この機能が一般提供された後、専用ゲートウェイ (および統合キャッシュ) に可用性 SLA および SLO が用意されます。 高可用性が要求されるシナリオでは、必要な専用ゲートウェイ インスタンス数の 3 倍をプロビジョニングする必要があります。 たとえば、運用環境で専用ゲートウェイ ノードが 1 つ必要な場合は、ダウンタイムや停止の可能性を考慮して、追加で 2 つの専用ゲートウェイ ノードをプロビジョニングする必要があります。

### <a name="the-integrated-cache-is-only-available-for-sql-core-api-right-now-are-you-planning-on-releasing-it-for-other-apis-as-well"></a>統合キャッシュは、現在、SQL (Core) API でのみ使用できます。 他の API 向けにもリリースする予定はありますか?

SQL API 以外への統合キャッシュの拡張は、長期的なロードマップにおいては計画されていますが、統合キャッシュの初期パブリック プレビューでは行われません。

### <a name="what-consistency-does-the-integrated-cache-support"></a>統合キャッシュではどのような整合性がサポートされますか?

統合キャッシュでは、セッションと最終的な整合性の両方がサポートされます。 また、オプションの [MaxIntegratedCacheStaleness](integrated-cache.md#maxintegratedcachestaleness) を構成することもできます。これにより、キャッシュされたデータに上限が設定されます。

## <a name="next-steps"></a>次の手順

- [統合キャッシュ](integrated-cache.md)
- [統合キャッシュの構成](how-to-configure-integrated-cache.md)
- [専用ゲートウェイ](dedicated-gateway.md)
- Azure Cosmos DB への移行のための容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
    - 知っていることが既存のデータベース クラスター内の仮想コアとサーバーの数のみである場合は、[仮想コアまたは仮想 CPU の数を使用した要求ユニットの見積もり](convert-vcore-to-request-unit.md)に関するページを参照してください 
    - 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB 容量計画ツールを使用した要求ユニットに見積もり](estimate-ru-with-capacity-planner.md)に関するページを参照してください