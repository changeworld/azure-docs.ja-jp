---
title: VPN Gateway における NAT (ネットワーク アドレス変換) について
titleSuffix: Azure VPN Gateway
description: アドレス空間が重複するネットワークどうしを接続するための、Azure VPN における NAT (ネットワーク アドレス変換) について説明します。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/22/2021
ms.author: yushwang
ms.openlocfilehash: 2bcf33466b3cbf57a67307961bb1e21e3a810a72
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2021
ms.locfileid: "112759610"
---
# <a name="about-nat-on-azure-vpn-gateway-preview"></a>Azure VPN Gateway における NAT について (プレビュー)

この記事では、Azure VPN Gateway における NAT (ネットワーク アドレス変換) のサポートについて概要を説明します。 NAT は、IP パケット内の IP アドレスを別の IP アドレスに変換するメカニズムを定義するものです。 NAT にはさまざまなシナリオがあります。

* IP アドレスが重複する複数のネットワークを接続する
* プライベート IP アドレス (RFC1918) が使用されているネットワークからインターネットに接続する (インターネット ブレークアウト)
* IPv6 ネットワークを IPv4 ネットワークに接続する (NAT64)

> [!IMPORTANT]
> Azure VPN Gateway の NAT がサポートするのは 1 つ目のシナリオです。IP アドレスが重複するオンプレミスのネットワークやブランチ オフィスを Azure の仮想ネットワークに接続します。 インターネット ブレークアウトや NAT64 はサポート **されません**。

## <a name="overlapping-address-spaces"></a><a name="why"></a>重複するアドレス空間

一般に、組織のプライベート ネットワークにおける内部的な通信には、RFC1918 で規定されているプライベート IP アドレスが使用されます。 インターネット上の VPN やプライベート WAN を使用してこれらのネットワークを接続する場合、アドレス空間が重複 **しないようにする必要があります**。アドレス空間が重複すると通信に失敗します。 IP アドレスが重複する複数のネットワークを接続するためには、それらのネットワークを接続するゲートウェイ デバイス上に NAT をデプロイします。 

## <a name="nat-type-static--dynamic"></a><a name="type"></a>NAT の種類: 静的と動的

ゲートウェイ デバイス上の NAT は、アドレスの競合を防ぐ NAT ポリシー (規則) に基づいて送信元 IP アドレスまたは送信先 IP アドレスを変換します。 NAT 変換の規則には、さまざまな種類があります。

* **静的 NAT**: 静的規則では、固定されたアドレス マッピング関係が定義されます。 特定の IP アドレスは、ターゲット プールにある同じアドレスにマップされます。 静的規則のマッピングは固定されているためステートレスです。

* **動的 NAT**: 動的 NAT では、アドレスの割り当て状況に応じて、または IP アドレスと TCP/UDP ポートのさまざまな組み合わせを使用して、1 つの IP アドレスを異なるターゲット IP アドレスに変換できます。 後者は NAPT (Network Address and Port Translation) とも呼ばれます。 動的規則は、そのときどきのトラフィック フローに応じたステートフルな変換マッピングとなります。

もう 1 つ考慮すべき点は、変換に使用するアドレス プールのサイズです。 変換先アドレス プールのサイズが変換元アドレス プールと同じである場合は、静的 NAT 規則を使用して、シーケンシャルな順序での 1 対 1 のマッピングを定義します。 変換先アドレス プールのサイズが変換元アドレス プールよりも小さい場合は、動的 NAT 規則を使用してその差を吸収します。

> [!IMPORTANT]
> * Azure VPN ゲートウェイの NAT でサポートされるのは、1 対 1 の静的 NAT 規則のみです。 動的 NAT 規則はサポートされません。
> * NAT がサポートされる SKU は、VpnGw2 から VpnGw5 および VpnGw2AZ から VpnGw5AZ です。
> * NAT は、IPsec クロスプレミス接続でのみサポートされます。 VNet 間接続や P2S 接続はサポートされません。

## <a name="nat-mode-ingress--egress"></a><a name="mode"></a>NAT モード: イングレスとエグレス

各 NAT 規則では、アドレス マッピング、つまり対応するネットワーク アドレス空間の変換の関係を定義します。

* イングレス: **IngressSNAT** 規則は、オンプレミスのネットワーク アドレス空間を、変換後のアドレス空間にマップすることでアドレスの重複を回避します。

* エグレス: **EgressSNAT** 規則は、Azure VNet のアドレス空間を別の変換後のアドレス空間にマップします。 

NAT 規則ごとに、変換前後のアドレス空間を次の 2 つのフィールドで指定します。

* **内部マッピング**: 変換 **前** のアドレス空間です。 イングレス規則の場合、このフィールドは、オンプレミス ネットワークの元のアドレス空間に対応します。 エグレス規則の場合は、元の VNet アドレス空間となります。

* **外部マッピング**: オンプレミス ネットワーク (イングレス) または VNet (エグレス) の変換 **後** のアドレス空間です。 Azure VPN ゲートウェイに接続されたさまざまなネットワークについて、すべての **外部マッピング** のアドレス空間は、互いに重複していないこと、また NAT なしで接続されたネットワークとも重複していないことが必要です。

## <a name="nat-and-routing"></a><a name="routing"></a>NAT とルーティング

NAT 規則が定義された接続で有効なアドレス空間は、その規則によって変わります。 Azure VPN ゲートウェイ上で BGP が有効になっている場合に、NAT 規則を使用した接続で学習したルートやアドバタイズされたルートを自動的に変換するには、[Enable BGP Route Translation]\(BGP ルートの変換を有効にする\) を選択します。

* 学習したルート: IngressSNAT 規則がある接続上で学習されたルートの宛先プレフィックスは、その規則の内部マッピングのプレフィックス (NAT 前) から外部マッピングのプレフィックス (NAT 後) に変換されます。

* アドバタイズされたルート: Azure VPN ゲートウェイは、VNet アドレス空間の EgressSNAT 規則の外部マッピング (NAT 後) プレフィックスをアドバタイズすると共に、他の接続から学習した、NAT 後アドレス プレフィックスを使用したルートをアドバタイズします。

* NAT 変換されるオンプレミス ネットワークにおける BGP ピアの IP アドレスの考慮事項:
   * APIPA (169.254.0.1 から 169.254.255.254) アドレス: NAT 規則は不要です。ローカル ネットワーク ゲートウェイで直接 APIPA アドレスを指定してください。
   * APIPA 以外のアドレス: ローカル ネットワーク ゲートウェイ上で、**変換後** (**NAT 後**) の IP アドレスを指定します。 オンプレミスの VPN ルーターを構成するには、**変換後** (**NAT 後**) の Azure BGP IP アドレスを使用します。 意図した変換の NAT 規則が定義されていることを確認してください。

> [!NOTE]
> IngressSNAT 規則がない接続上で学習されたルートは変換されません。 EgressSNAT 規則のない接続に対してアドバタイズされた VNet ルートも変換されません。
>

## <a name="nat-example"></a><a name="example"></a>NAT の例

次の図は、Azure VPN NAT 構成の例です。

:::image type="content" source="./media/nat-overview/vpn-nat.png" alt-text="NAT の図。" lightbox="./media/nat-overview/vpn-nat.png" border="false":::

この図は、Azure VNet と 2 つのオンプレミス ネットワークを示しています。いずれもアドレス空間は 10.0.1.0/24 です。 この 2 つのネットワークを Azure VNet と VPN ゲートウェイに接続するには、次の規則を作成します。

* IngressSNAT 規則 1: この規則は、オンプレミス アドレス空間 10.0.1.0/24 を 100.0.2.0/24 に変換します。

* IngressSNAT 規則 2: この規則は、オンプレミス アドレス空間 10.0.1.0/24 を 100.0.3.0/24 に変換します。

* EgressSNAT 規則 1: この規則は、VNet アドレス空間 10.0.1.0/24 を 100.0.1.0/24 に変換します。

この図では、各接続リソースに次の規則が設定されています。

* 接続 1 (VNet-Branch1):
    * IngressSNAT 規則 1
    * EgressSNAT 規則 1

* 接続 2 (VNet-Branch2)
    * IngressSNAT 規則 2
    * EgressSNAT 規則 1

各接続に関連付けられている規則に基づき、各ネットワークに使用されるアドレス空間は次のようになります。

| ネットワーク  | 変更元    | 変換後   |
| ---      | ---         | ---          |
| VNet     | 10.0.1.0/24 | 100.0.1.0/24 |
| ブランチ 1 | 10.0.1.0/24 | 100.0.2.0/24 |
| 分岐 2 | 10.0.1.0/24 | 100.0.3.0/24 |

下の図は、Branch 1 から VNet への、NAT 変換前後の IP パケットを示しています。

:::image type="content" source="./media/nat-overview/nat-packet.png" alt-text="NAT 変換前後を示す図。" lightbox="./media/nat-overview/nat-packet.png" border="false":::

> [!IMPORTANT]
> 1 つの SNAT 規則で、特定のネットワークの **両方向** の変換が定義されます。
>
> * IngressSNAT 規則では、オンプレミス ネットワークから Azure VPN ゲートウェイに **入ってくる** 送信元 IP アドレスの変換が定義されます。 また、同じオンプレミス ネットワークに向かって VNet から出ていく送信先 IP アドレスの変換も処理されます。
> * EgressSNAT 規則では、オンプレミス ネットワークに向かって Azure VPN ゲートウェイから出ていく送信元 IP アドレスの変換が定義されます。 また、その接続を介して VNet に入ってくるパケットの送信先 IP アドレスの変換も EgressSNAT 規則によって処理されます。
> * どちらの場合も、**DNAT** 規則は必要ありません。

## <a name="nat-configuration"></a><a name="config"></a>NAT 構成

上に示したような NAT 構成を実装するにはまず、Azure VPN ゲートウェイに NAT 規則を作成したうえで、対応する NAT 規則を関連付けた接続を作成します。 ご利用のクロスプレミス接続に NAT を構成する手順については、[Azure VPN ゲートウェイに NAT を構成する](nat-howto.md)方法に関するページを参照してください。

## <a name="nat-faq"></a><a name="faq"></a>NAT の FAQ

[!INCLUDE [vpn-gateway-faq-nat-include](../../includes/vpn-gateway-faq-nat-include.md)]

## <a name="next-steps"></a>次のステップ

ご利用のクロスプレミス接続に NAT を構成する手順については、[Azure VPN ゲートウェイに NAT を構成する](nat-howto.md)方法に関するページを参照してください。