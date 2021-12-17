---
title: Virtual WAN の価格について
titleSuffix: Azure Virtual WAN
description: この記事では、Virtual WAN の価格に関する一般的な質問に回答していきます。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/02/2021
ms.author: cherylmc
ms.custom: references_pricing
ms.openlocfilehash: 853005cdc9e44357661cdb0b3b50e7f6ba5a0bbf
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124730105"
---
# <a name="about-virtual-wan-pricing"></a>Virtual WAN の価格について

Azure Virtual WAN は、ネットワーク、セキュリティ、ルーティングのさまざまな機能をまとめて、1 つの運用インターフェイスを提供するネットワーク サービスです。 これらの機能には、ブランチ接続 (SD-WAN や VPN CPE などの Virtual WAN パートナー デバイスからの接続自動化経由)、サイト間 VPN 接続、リモート ユーザー VPN (ポイント対サイト) 接続、プライベート (ExpressRoute) 接続、クラウド内接続 (仮想ネットワークの推移的な接続)、VPN ExpressRoute 相互接続、ルーティング、Azure Firewall、プライベート接続のための暗号化が含まれます。

この記事では、Azure Virtual WAN を使用して一般的に展開される 3 つのシナリオと、掲載されている価格に基づく、展開の一般的な料金の見積もりについて説明します。 さらに、Virtual WAN が役に立つ可能性のあるシナリオも多数あります。

> [!IMPORTANT]
> この記事に記載されている価格は、例としてのみ使用することを目的としています。 
>  * 価格はいつでも変更される可能性があります。 現在の価格に関する情報については、「[Virtual WAN の価格](https://azure.microsoft.com/pricing/details/virtual-wan/)」ページをご覧ください。 
>  * ハブ間 (ハブからハブ) の料金にはリージョン間 (大陸内または大陸間) の料金が適用されるため、この価格は Virtual WAN の価格ページには表示されていません。 詳細については、[Azure のデータ転送の料金](https://azure.microsoft.com/pricing/details/bandwidth/)に関するページを参照してください。
>

## <a name="pricing-components"></a><a name="pricing"></a>価格の構成要素

次の図は、Virtual WAN を含むネットワークにおける典型的なデータ ルートと、1 時間当たりおよび GB 当たりの価格の異なる構成要素を示しています。

:::image type="content" source="./media/pricing-concepts/data-routes.png" alt-text="データ ルートの図。" lightbox="./media/pricing-concepts/data-routes.png":::

| 値 | シナリオ| 1 時間あたりのコスト | 1 GB あたりのコスト |
| --- | --- | --- | --- |
| 1 | 米国東部の標準 vWAN ハブ経由での、スポーク VNet から VPN サイト間ブランチへのデータ転送|展開時間 ($0.25/時間) + VPN S2S スケール ユニット ($0.261/時間) + VPN S2S 接続ユニット ($0.05/時間) = $0.561/時間|VNet ピアリング (送信) ($0.01/GB) + 標準送信ゾーン 1 ($0.087/GB) = $0.097/GB|
| 1'|米国東部の標準 vWAN ハブ経由での、VPN サイト間ブランチからスポーク VNet へのデータ転送|展開時間 ($0.25/時間) + VPN S2S スケール ユニット ($0.261/時間) + VPN S2S 接続ユニット ($0.05/時間) = $0.561/時間|VNet ピアリング (受信) ($0.01/GB) = $0.01/GB|
|2|標準 vWAN ハブ経由での、VPN サイト間ブランチから米国東部の別の VPN サイト間ブランチへのデータ転送|展開時間 ($0.25/時間) + VPN S2S スケール ユニット ($0.261/時間) + VPN S2S 接続ユニット (2x$0.05/時間) = $0.611/時間|標準送信ゾーン 1 ($0.087/GB) = $0.087/GB|
|2'|標準 vWAN ハブ経由での、VPN サイト間ブランチから米国東部の ExpressRoute で接続されているデータ センターまたは本社へのデータ転送|展開時間 ($0.25/時間) + VPN S2S スケール ユニット ($0.261/時間) + VPN S2S 接続ユニット ($0.05/時間) + ExpressRoute スケール ユニット ($0.42/時間) + ExpressRoute 接続ユニット ($0.05/時間) = $1.03/時間|ExpressRoute の従量制課金送信ゾーン 1 ($0.025/GB) = $0.025/GB|
|3|標準 vWAN ハブ経由での、VPN サイト間ブランチから米国東部の ExpressRoute で接続されているデータ センターまたは本社へのデータ転送|展開時間 ($0.25/時間) + VPN S2S スケール ユニット ($0.261/時間) + VPN S2S 接続ユニット ($0.05/時間) + ExpressRoute スケール ユニット ($0.42/時間) + ExpressRoute 接続ユニット ($0.05/時間) = $1.03/時間|ExpressRoute の従量制課金送信ゾーン 1 ($0.025/GB) = $0.025/GB|
|4|米国東部の標準 vWAN ハブ経由での、スポーク VNet から ExpressRoute で接続されているデータ センターまたは本社へのデータ転送|展開時間 ($0.25/時間) + ExpressRoute スケール ユニット ($0.42/時間) + ExpressRoute 接続ユニット ($0.05/時間) = $0.72/時間|VNet ピアリング (送信) ($0.01/GB) + ExpressRoute の従量制課金送信ゾーン 1 ($0.025/GB) = $0.035/GB|
|4'|米国東部の標準 vWAN ハブ経由での、ExpressRoute で接続されているデータ センターまたは本社からスポーク VNet へのデータ転送|展開時間 ($0.25/時間) + ExpressRoute スケール ユニット ($0.42/時間) + ExpressRoute 接続ユニット ($0.05/時間) = $0.72/時間|VNet ピアリング (受信) ($0.01/GB) = $0.01/GB|
|4"|ヨーロッパの標準 vWAN ハブ経由での、ExpressRoute で接続されているデータ センターまたは本社からリモートのスポーク VNet へのデータ転送|展開時間 (2x$0.25/時間) + ExpressRoute スケール ユニット ($0.42/時間) + ExpressRoute 接続ユニット ($0.05/時間) = $0.97/時間|VNet ピアリング (受信) ($0.01/GB) + ハブ データ処理 (ヨーロッパ) ($0.02/GB) + リージョン間データ転送 (米国東部からヨーロッパ) ($0.05/GB) = $0.08/GB|
|5|米国東部の標準 vWAN ハブ経由での、スポーク VNet から別のスポーク VNet へのデータ転送|展開時間 ($0.25/時間) = $0.25/時間|VNet ピアリング (送信 + 受信) (2x$0.01/GB) + ハブ データ処理 ($0.02/GB) = $0。 04/GB|
|6|米国東部のハブに接続されているスポーク VNet から、ヨーロッパのハブに接続されているヨーロッパの別のスポーク (異なるリージョン) の VNet へのデータ転送|展開時間 (2x$0.25/時間) = $0.50/時間|VNet ピアリング (送信 + 受信) (2x$0.01/GB) + ハブ データ処理 (2x$0.02/GB) + リージョン間データ転送 (米国東部からヨーロッパ) ($0.05/GB) = $0。 11/GB|
|7|ヨーロッパの標準 vWAN ハブ経由での、スポーク VNet からユーザー VPN (ポイント対サイト) へのデータ転送|展開時間 ($0.25/時間) + VPN P2S スケール ユニット ($0.261/時間) + VPN P2S 接続ユニット ($0.0125/時間) = $0.524/時間|VNet ピアリング (送信) ($0.01/GB) + 標準送信ゾーン 1 ($0.087/GB) = $0.097/GB|
|7'|ヨーロッパの標準 vWAN ハブ経由での、ユーザー VPN (ポイント対サイト) からスポーク VNet へのデータ転送|展開時間 ($0.25/時間) + VPN P2S スケール ユニット ($0.261/時間) + VPN P2S 接続ユニット ($0.0125/時間) = $0.524/時間|VNet ピアリング (受信) ($0.01/GB) = $0.01/GB|
|8|シンガポールのハブのネットワーク仮想アプライアンス経由での、SD WAN ブランチから同じリージョンの別の SD WAN ブランチへのデータ転送|標準 vWAN ハブ展開時間 ($0.25/時間) + NVA インフラストラクチャ ユニット ($0.25/時間) = $0.50/時間<br> *追加のサードパーティ ライセンス料金が適用される場合があります|標準送信ゾーン 2 ($0.12/GB) = $0.12/GB|

## <a name="common-topology-scenarios"></a><a name="topologies"></a>トポロジの一般的なシナリオ

### <a name="microsoft-global-backbone-wan"></a><a name="global"></a>Microsoft グローバル バックボーン WAN

このシナリオでは、完全にメッシュ化された自動リージョン ハブをグローバルに作成します。これは、グローバルにトラフィック接続を行うための Microsoft のバックボーンとして機能します。 一般的な接続には、ブランチ (サイト間 VPN や SD WAN)、リモート ユーザー (ポイント対サイト VPN)、プライベート回線 (ExpressRoute) などのエンドポイントが含まれます。 これは、Microsoft のバックボーンを利用してトラフィックをグローバルに伝達します。

**シナリオ 1 の図: Microsoft グローバル バックボーン WAN**

:::image type="content" source="./media/pricing-concepts/global-backbone.png" alt-text="Microsoft グローバル バックボーン WAN の図。" lightbox="./media/pricing-concepts/global-backbone.png":::

#### <a name="alternatives"></a>代替

* また、セキュリティで保護された vWAN ハブ (ファイアウォールを含む) を使用して、各グローバル リージョンのルーティングとセキュリティのニーズの中心とすることもできます。

### <a name="software-defined-wide-area-network-sd-wan"></a><a name="sdwan"></a>ソフトウェアによって定義されたワイド エリア ネットワーク (SD-WAN)

このシナリオでは、SD-WAN テクノロジに移行する店舗から Azure 上のリソースへのアクセス、およびオンプレミス (データ センター) への接続が店舗で自動的に終了するよう、vWAN ハブが使用されています。 各店舗は VPN トンネルで接続されており、ハブを介してインターネットでオンプレミスのデータ センターにトラフィックを安全に送信したり、Azure 上の共有アプリにアクセスしたりできるようになっています。

**シナリオ 2 の図: ソフトウェアによって定義されたワイド エリア ネットワーク (SD-WAN)**

:::image type="content" source="./media/pricing-concepts/sd-wan.png" alt-text="SD-WAN の図。" lightbox="./media/pricing-concepts/sd-wan.png":::

#### <a name="alternatives"></a>代替

* ハブでサードパーティのネットワーク仮想アプライアンスを使用し、それを小売店やセンターに接続することも可能です。

* また、セキュリティで保護された vWAN ハブ (ファイアウォールを含む) を使用して、ルーティングとセキュリティのニーズの中心とすることもできます。

### <a name="remote-user-connectivity"></a><a name="remote"></a>リモート ユーザーの接続

このシナリオでは、リモート ユーザー セッションは vWAN ハブで終了します。 これには、リモート ユーザーや、仮想ネットワークからの Azure Virtual Desktop セッションが該当します。 現在、各ハブでサポートされているユーザー数は 10 万人です。

次の図は、スポーク VNet と vWAN ハブ間のトンネルで暗号化されたトラフィックを提供する、仮想ネットワーク接続経由のポイント対サイト VPN を示しています。 また、異なるスポーク VNet 間で仮想ネットワーク ピアリング接続を行い、直接接続することも可能です。 たとえば、共有と VDI のスポーク Vnet 間などです。

**シナリオ 3 の図: リモート ユーザー接続**

:::image type="content" source="./media/pricing-concepts/remote.png" alt-text="リモート ユーザー接続を示す図。" lightbox="./media/pricing-concepts/remote.png":::

## <a name="data-flow-calculations"></a>データ フローの計算

> [!IMPORTANT]
> この記事に記載されている価格は、あくまでも例であり、変更される可能性があります。 最新の価格情報については、「[Virtual WAN の価格](https://azure.microsoft.com/pricing/details/virtual-wan/)」を参照してください。
>

### <a name="microsoft-global-backbone-wan-calculation"></a>Microsoft グローバル バックボーン WAN の計算

このシナリオでは、次の図に示すように、合計 8 TB のデータが vWAN ハブを介してグローバル ネットワークを流れると仮定しています。 データ転送コストの合計は $600 になります (ExpressRoute が従量課金されると仮定した際の、下図のデータ フロー コストの合計)。また、ハブの合計コストは $1,534 (3 スケール ユニット + 3 接続ユニット (ER) + 3 ハブ デプロイ) となります。

**シナリオ 1 の図: Microsoft グローバル バックボーン WAN の計算**

:::image type="content" source="./media/pricing-concepts/global-backbone-pricing.png" alt-text="Microsoft のバックボーンの料金の図。" lightbox="./media/pricing-concepts/global-backbone-pricing.png":::

| 値 | 計算 |
| --- | --- |
|S2S VPN ハブ シンガポール |(1 S2S VPN スケール ユニット ($0.361/時間) + 1 接続ユニット ($0.05/時間)) x 730 時間 = $300/月|
|ExpressRoute ハブ 米国東部 |(1 ER スケール ユニット ($0.42/時間) + 1 接続ユニット ($0.05/時間)) x 730 時間 = $343/月|
|ExpressRoute ハブ ヨーロッパ|(1 ER スケール ユニット ($0.42/時間) + 1 接続ユニット ($0.05/時間)) x 730 時間 = $343/月|
|標準ハブのデプロイ コスト |3 ハブ x 730 時間 x $0.25/時間 = $548/月|
|**合計**|1 か月あたり **$1,534** |

### <a name="software-defined-wide-area-network-sd-wan-calculation"></a>ソフトウェアによって定義されたワイド エリア ネットワーク (SD-WAN) の計算

このシナリオでは、次の図に示すように、合計 12 TB のデータが vWAN ハブを介して米国東部リージョンを流れると仮定しています。 データ転送コストの合計は $434 になります (ハブの処理料金、ピアリング、帯域幅、従量課金された ER データ転送の料金を含む、すべてのデータ フロー コストの合計)。また、ハブの合計コストは $863 (2 スケール ユニット + 3 接続ユニット (S2S 2 つ、ER 1 つ) + 1 ハブ デプロイ) となります。

**シナリオ 2 の図: ソフトウェアによって定義されたワイド エリア ネットワーク (SD-WAN) の計算**

:::image type="content" source="./media/pricing-concepts/sd-wan-pricing.png" alt-text="SD-WAN の料金の図。" lightbox="./media/pricing-concepts/sd-wan-pricing.png":::

| 値 | 計算 |
| --- | --- |
|VPN S2S (ブランチ) |(1 S2S VPN スケール ユニット ($0.361/時間) + 2 接続ユニット (2x$0.05/時間) x 730 時間 = $337/月|
|ExpressRoute ハブ (本社) |(1 ER スケール ユニット ($0.42/時間) + 1 接続ユニット ($0.05/時間) x 730 時間 = $343/月|
|標準ハブのデプロイ コスト|1 ハブ x 730 時間 x $0.25/時間 = $183/月|
|**合計**|1 か月あたり **$863**|

### <a name="remote-user-connectivity-calculation"></a>リモート ユーザー接続の計算

このシナリオでは、合計 15 TB のデータが米国東部リージョンのネットワークを経由して流れていると仮定しています。 データ転送コストの合計は $405 になります (ハブの処理料金、ピアリング、帯域幅、従量課金された ER データ転送の料金が含まれます)。また、ハブの合計コストは $708 (2 スケール ユニット + 2 接続ユニット + 2 ハブ デプロイ) になります。

**シナリオ 3 の図: リモート ユーザー接続の計算**
    
:::image type="content" source="./media/pricing-concepts/remote-pricing.png" alt-text="リモート ユーザーの料金の図。" lightbox="./media/pricing-concepts/remote-pricing.png":::

| 値 | 計算 |
| --- | --- |
|ExpressRoute ハブ (本社) |(1 ER スケール ユニット ($0.42/時間) + 1 接続ユニット ($0.05/時間)) x 730 時間 = $343/月 |
|標準ハブのデプロイ コスト| 2 ハブ x 730 時間 x $0.25/時間 = $365/月 |
|**合計** |1 か月あたり **$708** |

## <a name="pricing-faq"></a>料金に関する FAQ

> [!IMPORTANT]
> この記事に記載されている価格は、あくまでも例であり、変更される可能性があります。 最新の価格情報については、「[Virtual WAN の価格](https://azure.microsoft.com/pricing/details/virtual-wan/)」を参照してください。
>

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>スケール ユニットとは何ですか?

**スケール ユニット** によって、仮想ハブ内のサイト間 (S2S)、ポイント対サイト (P2S)、および ExpressRoute (ER) の合計容量に対するユニットが指定されます。 次に例を示します。

* **1 S2S VPN スケール ユニット** は、仮想ハブにおける合計容量が 500 Mbps の VPN ゲートウェイ (回復性のために 2 つのインスタンスがデプロイされます) を意味し、1 時間あたり $0.361 の料金がかかります。

* **1 ER スケール ユニット** は、仮想ハブにおける合計 2 Gbps の ER ゲートウェイを意味し、1 時間あたり $0.42 の料金がかかります。

* **5 ER スケール ユニット** は、仮想ハブ VNet 内の合計 10 Gbps の ER ゲートウェイを意味し、1 時間あたり $0.42*5 の料金がかかります。 ER のスケール ユニット 6 から 10 では、1 時間あたり $0.25 の増分が発生します。

### <a name="what-is-a-connection-unit"></a><a name="connection-unit"></a>接続ユニットとは何ですか?

Azure ゲートウェイに対するすべてのオンプレミスまたは Microsoft 以外のエンドポイント接続には、**接続ユニット** が適用されます。 サイト間 VPN の場合、この値はブランチを意味します。 ユーザー VPN (ポイント対サイト) の場合、この値はリモート ユーザーを意味します。 ExpressRoute の場合、この値は ExpressRoute 回線接続を意味します。<br>次に例を示します。

* 仮想ハブの Azure VPN に接続する 1 つのブランチ接続には、1 時間あたり $0.05 の料金がかかります。 したがって、100 個のブランチ接続が Azure 仮想ハブに接続する場合は、1 時間あたり $0.05*100 の料金がかかります。

* 2 つの ExpressRoute 回線接続が仮想ハブに接続する場合、1 時間あたり $0.05*2 の料金がかかります。

* 3 つのリモート ユーザー接続が Azure 仮想ハブの P2S ゲートウェイに接続する場合、1 時間あたり $0.03*3 の料金がかかります。

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>データ転送の料金はどのように計算されますか?

* Azure に入ってくるトラフィックは課金されません。 (VPN、ExpressRoute、またはポイント対サイト ユーザー VPN 接続を介して) Azure から出ていくトラフィックには、標準の [Azure データ転送料金](https://azure.microsoft.com/pricing/details/bandwidth/)が適用され、ExpressRoute の場合は、[ExpressRoute の料金](https://azure.microsoft.com/pricing/details/expressroute/)がかかります。

* ピアリング料金は、vWAN ハブに接続されている VNet がデータを送受信する場合に適用されます。 詳細については、「[Virtual Network の価格](https://azure.microsoft.com/pricing/details/virtual-network/)」を参照してください。

* 1 つの Virtual WAN ハブと、ソース ハブとは異なるリージョンにあるリモート Virtual WAN ハブまたは VNet 間のデータ転送料金については、ハブから出ていくトラフィックに対してデータ転送料金が適用されます。 例:米国東部ハブから出ていくトラフィックには $0.02/GB の料金がかかり、米国西部ハブに送信されます。 米国西部ハブに入ってくるトラフィックには料金がかかりません。 すべてのハブ間トラフィックには、リージョン間 (大陸内/大陸間) 料金 [Azure データ転送料金](https://azure.microsoft.com/pricing/details/bandwidth/)が適用されます。 

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>Standard ハブの料金と Standard ハブの処理料金の違いは何ですか?

Virtual WAN には、次の 2 種類があります。

* **Basic Virtual WAN** では、ユーザーが複数のハブをデプロイし、VPN サイト間接続を利用できます。 Basic Virtual WAN には、フル メッシュ ハブ、ExpressRoute 接続、ユーザー VPN またはポイント対サイト VPN 接続、VNet 間のトランジット接続、VPN と ExpressRoute のトランジット接続、または Azure Firewall のような高度な機能はありません。 Basic Virtual WAN では、ハブに対して基本料金やデータ処理料金は発生しません。

* **Standard Virtual WAN** には、フル メッシュ ハブ、ExpressRoute 接続、ユーザー VPN またはポイント対サイト VPN 接続、VNet 間のトランジット接続、VPN と ExpressRoute のトランジット接続、および Azure Firewall などの高度な機能が備わっています。仮想ハブのルーティングはすべて、仮想ハブ内で複数のサービスを実現するルーターによって提供されます。 ハブには 1 時間あたり $0.25 の基本料金がかかります。 また、VNet 間のトランジット接続に対する仮想ハブ ルーター内でのデータ処理にも料金がかかります。 仮想ハブ ルーターのデータ処理にかかる料金は、「[価格の構成要素](#pricing)」に示すブランチ間転送 (シナリオ 2、2'、3)、または同じ vWAN ハブを経由した VNet からブランチへの転送 (シナリオ 1、1') には適用されません。 

## <a name="next-steps"></a>次のステップ

* 現在の価格については、「[Virtual WAN の価格](https://azure.microsoft.com/pricing/details/virtual-wan/)」をご覧ください。

* Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。


