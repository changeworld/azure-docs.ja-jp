---
title: 可用性と継続性
titleSuffix: Azure Cognitive Search
description: 長期間の中断や致命的な障害が発生しても、検索サービスで高い可用性と回復性を発揮する方法について説明します。
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.custom: references_regions
ms.openlocfilehash: 493f6759f63f023572f38647076e04425acf9d6a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581533"
---
# <a name="availability-and-business-continuity-in-azure-cognitive-search"></a>Azure Cognitive Search の可用性とビジネス継続性

Cognitive Search では、複数のレプリカによって可用性が実現されます。一方、ビジネス継続性 (およびディザスター リカバリー) は複数の検索サービスによって実現されます。 この記事では、可用性と継続運用というビジネス要件を両方とも満たす戦略を開発するための出発点となるガイダンスを示します。

<a name="scale-for-availability"></a>

## <a name="high-availability"></a>高可用性

Cognitive Search では、レプリカとはインデックスのコピーです。 Azure Cognitive Search で複数のレプリカを用意すれば、あるレプリカでコンピューターの再起動やメンテナンスを行い、同時に他のレプリカでクエリの実行を続けることが可能になります。 レプリカの追加については、「[レプリカとパーティションの追加または削減](search-capacity-planning.md#adjust-capacity)」を参照してください。

Microsoft は、個々の検索サービスについて、次の条件を満たす構成に対して 99.9% 以上の可用性を保証します。 

+ 読み取り専用ワークロード (クエリ) の高可用性を実現するには 2 つのレプリカ

+ 読み取り/書き込みワークロード (クエリとインデックス作成) の高可用性を実現するには 3 つ以上のレプリカ 

Free レベルに対して、SLA は提供されません。 詳細については、「[Azure Cognitive Search の SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)」を参照してください。

<a name="availability-zones"></a>

## <a name="availability-zones"></a>可用性ゾーン

[Availability Zones](../availability-zones/az-overview.md) は、リージョンのデータ センターを個別の物理的な場所のグループに分割することで、同一リージョン内での高可用性を実現する Azure プラットフォーム機能です。 Cognitive Search で Availability Zones を使用する場合、個々のレプリカがゾーン割り当ての単位です。 検索サービスは 1 つのリージョン内で実行され、そのレプリカは異なるゾーンで実行されます。

検索サービスに 2 つ以上のレプリカを追加することで、Azure Cognitive Search で Availability Zones を利用することができます。 各レプリカは、リージョン内の異なる可用性ゾーンに配置されます。 Availability Zones よりも多くのレプリカがある場合、レプリカは Availability Zones 全体に可能な限り均等に配布されます。 Availability Zones を提供するリージョンに[検索サービスを作成](search-create-service-portal.md)し、[複数のレプリカを使用](search-capacity-planning.md#adjust-capacity)するようにサービスを構成する以外に、特定の操作は必要ありません。

現在、Azure Cognitive Search では、次のいずれかのリージョンで作成された Standard レベル以上の検索サービスの Availability Zones がサポートされます。

+ オーストラリア東部 (2021 年 1 月 30 日以降に作成)
+ カナダ中部 (2021 年 1 月 30 日以降に作成)
+ 米国中部 (2020 年 12 月 4 日以降に作成)
+ 米国東部 (2021 年 1 月 27 日以降に作成)
+ 米国東部 2 (2021 年 1 月 30 日以降に作成)
+ フランス中部 (2020 年 10 月 23 日以降に作成)
+ 東日本 (2021 年 1 月 30 日以降に作成)
+ 北ヨーロッパ (2021 年 1 月 28 日以降に作成)
+ 東南アジア (2021 年 1 月 31 日以降に作成)
+ 英国南部 (2021 年 1 月 30 日以降に作成)
+ 西ヨーロッパ (2021 年 1 月 29 日以降に作成)
+ 米国西部 2 (2021 年 1 月 30 日以降に作成)

Availability Zones は、[Azure Cognitive Search サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/search/v1_0/)には影響しません。 クエリの高可用性を実現するには、3 つ以上のレプリカが必要です。

## <a name="multiple-services-in-separate-geographic-regions"></a>個別の地理的リージョン内の複数のサービス

ほとんどのお客様はサービスを 1 つしか使いませんが、運用要件に次のことが含まれる場合、サービスの冗長性が必要になる場合があります。

+ [事業継続とディザスター リカバリー (BCDR)](../best-practices-availability-paired-regions.md) (Cognitive Search では、停止時の即時フェールオーバーは提供されません)。
+ グローバルにデプロイされたアプリケーション。 クエリとインデックス作成の要求が世界中から行われた場合、ホスト データセンターに近いユーザーほど、より高速なパフォーマンスを得られます。 これらのユーザーに近接しているリージョンに追加のサービスを作成すると、全ユーザーのパフォーマンスを均質化できます。
+ [マルチテナント アーキテクチャ](search-modeling-multitenant-saas-applications.md)では、2 つ以上のサービスが呼び出されることがあります。

さらに 2 つの検索サービスが必要になった場合は、それらを異なるリージョンに作成することで、継続性と復旧というアプリケーション要件を満たし、グローバル ユーザー ベースの応答時間も短縮できます。

Azure Cognitive Search では、リージョン間で検索インデックスを geo レプリケートする自動化された方法を現在は提供していませんが、実装と管理のためにこのプロセスを簡素化できる手法がいくつかあります。 以下のセクションでは、これらの手法の概要を説明します。

地理的に分散した一連の Search サービスの目的は、2 つかそれ以上のリージョンで 2 つかそれ以上のインデックスを利用できるようにすることです。そうすることで、待機時間が最も短い Azure Cognitive Search サービスまでの経路がユーザーに与えられます。

   ![リージョン別のサービスのクロス集計][1]

このアーキテクチャを実装するには、複数のサービスを作成し、データ同期の戦略を設計します。 必要に応じて、ルート指定要求のための Azure Traffic Manager などのリソースを含めることができます。 詳細については、[検索サービスの作成](search-create-service-portal.md)に関する記事をご覧ください。

<a name="data-sync"></a>

### <a name="keep-data-synchronized-across-multiple-services"></a>複数のサービス間でデータの同期を維持する

2 つ以上の分散した Search サービスの同期を維持するには 2 つのオプションがあり、[Azure Cognitive Search インデクサー](search-indexer-overview.md)か、Push API ([Azure Cognitive Search REST API](/rest/api/searchservice/) とも呼ばれます) を使用します。 

#### <a name="option-1-use-indexers-for-updating-content-on-multiple-services"></a>オプション 1: 複数のサービス上のコンテンツ更新のためのインデクサーの使用

あるサービスで既にインデクサーを使用している場合は、2 つ目のサービスに 2 つ目のインデクサーを構成し、同じデータ ソース オブジェクトを使用して同じ場所からデータをプルすることができます。 各リージョン内の各サービスにはその独自のインデクサーとターゲット インデックスがありますが (検索インデックスが共有されていないため、データが重複しています)、各インデクサーは同じデータ ソースを参照します。

このアーキテクチャの概観を次に示します。

   ![分散したインデクサーとサービスの組み合わせを使用する単一のデータ ソース][2]

#### <a name="option-2-use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>オプション 2: 複数のサービスでコンテンツの更新をプッシュするための REST API を使用する

[検索インデックスの内容をプッシュ](tutorial-optimize-indexing-push-api.md)するために、Azure Cognitive Search REST API を使用している場合は、更新が必要になるたびにすべての Search サービスに変更をプッシュすることで、さまざまな Search サービスの同期を維持できます。 コードでは、ある Search サービスの更新が失敗した場合でも、他のサービスでの成功は必ず処理してください。

### <a name="use-azure-traffic-manager-to-coordinate-requests"></a>Azure Traffic Manager を使用して要求を調整する

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) を使用すると、複数の Search サービスが支援する地理的に配置された複数の Web サイトに要求をルーティングできます。 Traffic Manager の利点の 1 つは、Azure Cognitive Search をプローブしてそれが利用可能であることを確実にし、ダウンタイムが発生した場合にユーザーを別の Search サービスにルーティングできることです。 また、Azure Web サイトを使用して検索要求をルーティングしている場合、Azure Traffic Manager を使用すると、Web サイトは稼働していても Azure Cognitive Search が稼働していない場合に負荷を分散できます。 Traffic Manager を活用したアーキテクチャの例を次に示します。

   ![リージョン別のサービスのクロス集計 (中央の Traffic Manager を使用)][3]

## <a name="disaster-recovery-and-service-outages"></a>障害復旧とサービスの停止

Microsoft はユーザーのデータを回収できますが、Azure Cognitive Search では、クラスターまたはデータ センターのレベルで障害が発生した場合のサービスの即時フェールオーバーは提供されません。 データ センターでクラスターの障害が発生した場合、運用チームは障害を検出してサービスの復元を行います。 サービスの復元中にダウンタイムが発生しますが、[サービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/) に従い、サービス使用不可に対する補償としてサービス クレジットを要求できます。 

Microsoft の管理が及ばない外部の壊滅的災害の際にサービスを継続する必要がある場合は、異なるリージョンに[追加のサービスをプロビジョニング](search-create-service-portal.md)し、geo レプリケーション戦略を導入して、インデックスがすべてのサービスで完全に冗長になるようにする必要があります。

インデックスの作成と更新に[インデクサー](search-indexer-overview.md)を使っているお客様は、同じデータ ソースを利用する geo 固有のインデクサーによってディザスター リカバリーを処理できます。 異なるリージョンにある 2 つのサービスそれぞれでインデクサーを実行し、同じデータ ソースのインデックスを作成すると、geo 冗長性を実現できます。 geo 冗長性も備えたデータ ソースからインデックスを作成する場合、Azure Cognitive Search インデクサーは、プライマリ レプリカからの (新規、変更、または削除されたドキュメントの更新をマージする) 増分インデックスの作成のみを実行できることに注意してください。 フェールオーバー イベントでは、新しいプライマリ レプリカをポイントするように再度インデクサーを設定してください。 

インデクサーを使用しない場合は、アプリケーションのコードを使ってオブジェクトとデータを異なる検索サービスに並列にプッシュします。 詳細については、「[複数のサービス間でデータの同期を維持する](#data-sync)」を参照してください。

## <a name="back-up-and-restore-alternatives"></a>バックアップと復元の代替手段

Azure Cognitive Search は主要なデータ ストレージ ソリューションではないため、Microsoft ではセルフサービスのバックアップと復元のための正式なメカニズムは提供していません。 ただし、こちらの [Azure Cognitive Search .NET サンプル リポジトリ](https://github.com/Azure-Samples/azure-search-dotnet-samples)の **index-backup-restore** サンプル コードを使用して、インデックス定義とインデックス スナップショットを一連の JSON ファイルにバックアップし、後で必要に応じて、これらのファイルを使用してインデックスを復元できます。 このツールを使用して、サービス レベル間でインデックスを移動することもできます。

そうしない場合は、インデックスの作成と設定に使われるアプリケーション コードが、誤ってインデックスを削除した場合の事実上の復元オプションです。 インデックスを再構築するには、インデックスを削除し (存在する場合)、サービスでインデックスを再作成して、プライマリ データ ストアからデータを取得することによって再読み込みします。

## <a name="next-steps"></a>次のステップ

価格レベルと各レベルのサービスの制限の詳細については、[サービスの制限](search-limits-quotas-capacity.md)に関するページをご覧ください。 パーティションとレプリカの組み合わせの詳細については、[キャパシティ プランニング](search-capacity-planning.md)に関する記事をご覧ください。

この記事で説明された手法のパフォーマンスとデモについて、次の動画をご覧ください。

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png