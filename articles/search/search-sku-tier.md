---
title: Choose a pricing tier
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search をプロビジョニングできるレベルは、Free、Basic、および Standard です。Standard は、さまざまなリソース構成および容量レベルで使用できます。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 062bd41b0803cbb08f74fbcbcebb89bbddeb0d45
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/16/2020
ms.locfileid: "97559805"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Azure Cognitive Search の価格レベルの選択

[検索サービスを作成](search-create-service-portal.md)するときに、サービスの有効期間にわたって固定される価格レベルを選択します。 選択したレベルによって以下が決まります。

+ 作成できるインデックスおよびその他のオブジェクトの数量 (上限)
+ パーティション (物理ストレージ) のサイズと速度
+ 課金対象のレート、月単位の固定コスト (ただし、パーティションまたはレプリカを追加した場合は増分のコスト)

さらに、一部の[プレミアム機能](#premium-features)には、サービス レベルの要件が伴います。

## <a name="tier-descriptions"></a>サービス レベルの説明

レベルには、**Free**、**Basic**、**Standard**、**Storage Optimized** があります。 Standard と Storage Optimized は、いくつかの構成および容量で利用できます。

次の Azure portal のスクリーンショットは、使用可能なレベルを示しています。価格は除外されていますが、ポータルと[価格に関するページ](https://azure.microsoft.com/pricing/details/search/)で確認できます。 

![Azure Cognitive Search の価格レベル](media/search-sku-tier/tiers.png "Azure Cognitive Search の価格レベル")

**Free** は、チュートリアルやサンプルの実行など、小規模なプロジェクト用の限定された検索サービスを作成します。 内部的には、複数のサブスクライバー間でレプリカとパーティションが共有されます。 無料のサービスをスケーリングしたり、重要なワークロードを実行したりすることはできません。

**Basic** と **Standard** は最も一般的に使用される課金対象のレベルで、**Standard** が既定値になっています。 自分の制御下の専用リソースを使用して、より大規模なプロジェクトのデプロイ、パフォーマンスの最適化、および容量の増加を行うことができます。

一部のレベルは、特定の種類の作業向けに最適化されています。 たとえば、**Standard 3 High Density (S3 HD)** は、S3 用の "*ホスティング モード*" で、基になるハードウェアが多数の小さいインデックス用に最適化されており、マルチテナント シナリオ向けです。 S3 HD のユニットあたりの料金は S3 と同じですが、ハードウェアは数多くの小さいインデックスでの高速ファイル読み取り用に最適化されています。

**Storage Optimized** レベルでは、Standard レベルより安い TB あたりの価格で、大容量のストレージが提供されます。 主なトレードオフとしてクエリの待ち時間が長くなるので、特定のアプリケーションの要件に対して妥当かどうかを確認する必要があります。 このレベルのパフォーマンスに関する考慮事項の詳細については、[パフォーマンスと最適化の考慮事項](search-performance-optimization.md)に関するページを参照してください。

さまざまなレベルの詳細については、[Azure Cognitive Search サービスの制限](search-limits-quotas-capacity.md)に関する記事の[価格に関するページ](https://azure.microsoft.com/pricing/details/search/)、およびサービスのプロビジョニング時のポータル ページを参照してください。

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>階層による機能の使用の可否

ほとんどの機能は Free レベルを含むすべてのレベルで使用できます。 場合によっては、選択したレベルによって機能を実装する能力に影響が生じることがあります。 次の表では、サービス レベルに関連する機能の制約について説明します。

| 機能 | 制限事項 |
|---------|-------------|
| [インデクサー](search-indexer-overview.md) | インデクサーは S3 HD では使用できません。  |
| [AI エンリッチメント](search-security-manage-encryption-keys.md) | Free レベルで実行されますが、推奨されていません。 |
| [顧客が管理する暗号化キー](search-security-manage-encryption-keys.md) | Free レベルでは使用できません。 |
| [IP ファイアウォール アクセス](service-configure-firewall.md) | Free レベルでは使用できません。 |
| [プライベート エンドポイント (Azure Private Link との統合)](service-create-private-endpoint.md) | 検索サービスへの受信接続の場合、Free レベルでは利用できません。 インデクサーによる他の Azure リソースへの送信接続の場合は、Free または S3 HD では使用できません。 スキルセットを使用するインデクサーの場合、Free、Basic、S1、S3 HD では使用できません。|

リソース集中型の機能は、十分な容量を与えない限り、うまくいかない場合があります。 たとえば、[AI エンリッチメント](cognitive-search-concept-intro.md)には、データセットのサイズが小さい場合を除いて Free サービスではタイムアウトになってしまう、実行時間の長いスキルがあります。

## <a name="billable-events"></a>課金対象のイベント

Azure Cognitive Search 上に構築されたソリューションでは、次のようなコストが発生する場合があります。

+ 最小の構成 (1 つのパーティションとレプリカ) で 24 時間 365 日実行される[サービス自体のコスト](#service-costs) (基本料金)。 これは、サービスを実行する固定費と考えることができます。

+ 容量 (レプリカまたはパーティション) の追加 (コストは課金対象のレートに応じて増加します)

+ 帯域幅料金 (送信データ転送)

+ 特定の機能に必要なアドオンサービス:

  + AI エンリッチメント ([Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) が必要)
  + ナレッジ ストア ([Azure Storage](https://azure.microsoft.com/pricing/details/storage/) が必要)
  + インクリメンタル エンリッチメント ([Azure Storage](https://azure.microsoft.com/pricing/details/storage/) が必要。AI エンリッチメントに適用される)
  + カスタマー マネージド キーと二重暗号化 ([Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) が必要)
  + インターネットにアクセスできないモデル用のプライベート エンドポイント ([Azure Private Link が必要](https://azure.microsoft.com/pricing/details/private-link/))

### <a name="service-costs"></a>サービスのコスト

課金を回避するために "一時停止" できる仮想マシンやその他のリソースとは異なり、Azure Cognitive Search サービスは、お客様が独占的に使用できる専用のハードウェア上で常に使用可能です。 そのため、サービスの作成は課金対象のイベントであり、そのサービスを作成したときに開始され、そのサービスを削除したときに終了します。 

最低料金は、課金対象のレートでの最初の検索単位 (1 つのレプリカ x 1 つのパーティション) です。 これより小さい構成ではサービスは実行できないため、この最低料金はサービスの有効期間を通して一定です。 最小構成を超える場合は、レプリカとパーティションを互いに独立して追加できます。 レプリカおよびパーティションによって容量が徐々に増加すると、[(レプリカ数 x パーティション数 x レート)](#search-units) という式に基づいて料金が増加します。この場合、課金されるレートは、選択した価格レベルによって異なります。

検索ソリューションのコストを見積もる際には、価格と容量は直線的に比例するものではないことに注意してください (容量を 2 倍にすると、コストは 2 倍より多くなります)。数式による計算の例については、「[レプリカとパーティションを割り当てる方法](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)」を参照してください。

### <a name="bandwidth-charges"></a>帯域幅の料金

Azure データ ソースが Azure Cognitive Search とは別のリージョンにある場合、[インデクサー](search-indexer-overview.md)の使用は課金に影響します。 このシナリオでは、これは Azure データ ソースから Azure Cognitive Search へ送信データを移動させるためにかかるコストです。 

Azure Cognitive Search サービスをデータと同じリージョンに作成すれば、データ エグレス料金が発生する事態を回避できます。 以下に、[帯域幅の価格に関するページ](https://azure.microsoft.com/pricing/details/bandwidth/)の情報の一部を示します。

+ Azure 上のどのサービスも、受信データには料金がかかりません。
+ Azure Cognitive Search からの送信データの料金は発生しません。 たとえば、検索サービスが米国西部にあり、Azure Web アプリが米国東部にある場合、米国西部から発生したクエリ応答ペイロードには課金されません。
+ マルチサービス ソリューションでは、すべてのサービスが同じリージョンにあれば、伝送データに料金はかかりません。

それらのサービスが別々のリージョンにある場合は、アウトバウンド データに料金が適用されます。 これらの料金は、実際には Azure Cognitive Search の課金の一部ではありません。 データまたは AI によって強化されたインデクサーを使用して異なるリージョンからデータをプルする場合、それらのコストが全体の請求書に反映されるため、ここで言及しています。

### <a name="ai-enrichment-with-cognitive-services"></a>Cognitive Services を使用する AI エンリッチメント

[AI エンリッチメント](cognitive-search-concept-intro.md)の場合は、従量課金制の処理について、Azure Cognitive Search と同じリージョンの S0 価格レベルで、[有料の Azure Cognitive Services リソースをアタッチする](cognitive-search-attach-cognitive-services.md)ように計画することをお勧めします。 Cognitive Services のアタッチには、関連する固定コストはありません。 課金の対象となるのは、必要な処理の分だけです。

| 操作 | 課金への影響 |
|-----------|----------------|
| ドキュメント解析、テキスト抽出 | Free |
| ドキュメント解析、画像抽出 | ドキュメントから抽出された画像の数に基づいて課金されます。 **インデクサー構成** で、[imageAction](/rest/api/searchservice/create-indexer#indexer-parameters) は、画像抽出をトリガーするパラメーターです。 **imageAction** が "none" (既定値) に設定されている場合、画像の抽出に対して課金されません。 画像抽出のレートは、Azure Cognitive Search の[価格の詳細](https://azure.microsoft.com/pricing/details/search/)に関するページに記載されています。|
| [組み込みのコグニティブ スキル](cognitive-search-predefined-skills.md) | Cognitive Services を直接使用してそのタスクを実行した場合と同じレートで課金されます。 |
| カスタム スキル | カスタム スキルは、自分が提供する機能です。 カスタム スキルを使用した場合のコストは、カスタム コードで他の従量制サービスを呼び出しているかどうかによって決まります。 |

[インクリメンタル エンリッチメント (プレビュー)](cognitive-search-incremental-indexing-conceptual.md) 機能を使用するとキャッシュを提供でき、インデクサーがコグニティブ スキル (将来、スキルセットを変更する場合に必要) のみを効率的に実行できるようになり、時間とコストが節約されます。

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>請求式 (R x P = SU)

Azure Cognitive Search の操作について理解するための最も重要な課金の概念は、"*検索ユニット*" (SU) です。 Azure Cognitive Search は、インデックス作成とクエリについてレプリカとパーティションの両方に依存しているため、この片方のみを基準に課金しても意味がありません。 このため、この両方を合わせた単位に基づいて課金されます。

SU は、サービスによって使用される "*レプリカ*" と "*パーティション*" の積です **(R x P = SU)** 。

すべてのサービスは、最小値として、1 SU (1 つのレプリカと 1 つのパーティションの積) から開始します。 どのサービスも、最大値は 36 SU です。 この最大値に達するパターンは、いくつかあります。たとえば、6 パーティション x 6 レプリカ、3 パーティション x 12 レプリカなどです。 一般には、総容量よりも少ない量を使用します (たとえば、3 レプリカと 3 パーティションのサービスで、9 SU の課金)。 有効な組み合わせについては、「[パーティションとレプリカの組み合わせ](search-capacity-planning.md#chart)」の表を参照してください。

課金レートは、SU ごとの 1 時間単位です。 レベルごとに、レートが高くなっていきます。 レベルが高いほど、パーティションは大きく高速になり、そのレベルの時間あたりの料金が全体的に高くなります。 各レベルのレートは、[価格の詳細](https://azure.microsoft.com/pricing/details/search/)に関するページでご覧いただけます。

ほとんどのお客様は、総容量の一部だけをオンラインにし、残りを取っておきます。 課金については、オンラインにするパーティションとレプリカの数 (SU 式を使用して計算) によって、時間単位で支払う金額が決まります。

## <a name="next-steps"></a>次の手順

コスト管理は、キャパシティ プランニングの不可欠な要素です。 次の手順として、容量を推定し、コストを管理する方法に関するガイダンスについて、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [Azure Cognitive Search でコストを管理し、容量を見積もる方法](search-sku-manage-costs.md)