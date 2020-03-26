---
title: Azure Virtual WAN の概要 | Microsoft Docs
description: Virtual WAN の自動化されたスケーラブルなブランチ間接続、利用可能なリージョン、パートナーについて説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 02/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 9ac70252ce7c818ccbdecfd996b9970f011aa967
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "79290190"
---
# <a name="about-azure-virtual-wan"></a>Azure Virtual WAN の概要

Azure Virtual WAN は、Azure への、または Azure 経由での、最適化および自動化されたブランチ接続を提供するネットワーク サービスです。 Azure リージョンは、ブランチの接続先として選択できるハブとして機能します。 Azure バックボーンを利用してブランチを接続し、ブランチから VNet への接続を利用することもできます。 Azure Virtual WAN VPN による接続の自動化をサポートするパートナーの一覧があります。 詳細については、[Virtual WAN のパートナーと場所](virtual-wan-locations-partners.md)に関する記事を参照してください。

Azure Virtual WAN は、サイト間 VPN、ユーザー VPN (ポイント対サイト)、ExpressRoute など、多数の Azure クラウド接続サービスを、1 つの操作インターフェイスにまとめたものです。 Azure VNet への接続は、仮想ネットワーク接続を使用して確立されます。 これにより、[グローバル トランジット ネットワーク アーキテクチャ](virtual-wan-global-transit-network-architecture.md)が可能になります。これは、クラウドでホストされたネットワーク "ハブ" によって、さまざまな種類の "スポーク" に分散されている可能性があるエンドポイント間の推移的な接続が可能になる、従来のハブ アンド スポーク接続モデルが基になっています。

![Virtual WAN のダイアグラム](./media/virtual-wan-about/virtualwan1.png)

この記事では、Azure Virtual WAN のネットワーク接続について簡単に説明します。 Virtual WAN には次の利点があります。

* **ハブ アンド スポーク型の統合された接続ソリューション:** オンプレミス サイトと Azure ハブの間のサイト間の構成と接続を自動化します。
* **自動化されたスポークの設定と構成:** 仮想ネットワークとワークロードを Azure ハブにシームレスに接続します。
* **直感的なトラブルシューティング:** Azure 内でエンド ツー エンドのフローを確認し、この情報を使用して必要なアクションを実行できます。

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>Basic および Standard の仮想 WAN

仮想 WAN には、次の 2 つの種類があります:Basic と Standard です。 各種類に対して使用できる構成を、次の表に示します。

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

仮想 WAN をアップグレードする手順については、「[Virtual WAN を Basic から Standard にアップグレードする](upgrade-virtual-wan.md)」をご覧ください。

## <a name="architecture"></a><a name="architecture"></a>アーキテクチャ

仮想 WAN のアーキテクチャと Virtual WAN への移行方法については、次の記事を参照してください。

* [仮想 WAN のアーキテクチャ](migrate-from-hub-spoke-topology.md)
* [グローバル転送ネットワーク アーキテクチャ](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Virtual WAN リソース

エンドツーエンドの仮想 WAN を構成するには、次のリソースを作成します。

* **virtualWAN:** virtualWAN リソースは、Azure ネットワークの仮想オーバーレイを表し、複数のリソースのコレクションです。 これには、仮想 WAN 内に配置するすべての仮想ハブへのリンクが含まれます。 Virtual WAN リソースは相互に分離されており、共通のハブを含むことはできません。 Virtual WAN 上の仮想ハブは互いに通信しません。

* **ハブ:** 仮想ハブは、Microsoft のマネージド仮想ネットワークです。 ハブには、接続を可能にするためのさまざまなサービス エンドポイントが含まれています。 オンプレミス ネットワーク (vpnsite) から、仮想ハブ内の VPN Gateway に接続したり、ExpressRoute 回線を仮想ハブに接続したり、またはモバイル ユーザーを仮想ハブ内のポイント対サイト ゲートウェイに接続したりすることもできます。 ハブは、リージョン内のネットワークのコアです。 1 つの Azure リージョンに配置できるハブは 1 つのみです。

  ハブ ゲートウェイは、ExpressRoute および VPN Gateway に使用する仮想ネットワーク ゲートウェイと同じではありません。 たとえば、Virtual WAN を使用する場合は、オンプレミス サイトから直接 VNet にサイト間接続を作成しません。 代わりに、ハブへのサイト間接続を作成します。 トラフィックは、常にハブ ゲートウェイを通過します。 これは、VNet には独自の仮想ネットワーク ゲートウェイが必要ないことを意味します。 Virtual WAN により、VNet は、仮想ハブと仮想ハブ ゲートウェイを介して簡単にスケーリングできます。

* **ハブ仮想ネットワーク接続:** ハブ仮想ネットワーク接続リソースは、ハブを仮想ネットワークにシームレスに接続するために使用します。

* **(プレビュー) ハブ間接続** - ハブはすべて、仮想 WAN 内で相互に接続されています。 これは、ローカル ハブに接続されているブランチ、ユーザー、または VNet が、接続されたハブのフル メッシュ アーキテクチャを使用して、別のブランチまたは VNet と通信できることを意味します。 また、ハブ間の VNet と同様に、ハブ間接続されたフレームワークを使用して、仮想ハブを通じて転送するハブ内の VNet を接続することもできます。

* **ハブのルート テーブル:** 仮想ハブのルートを作成して、そのルートを仮想ハブのルート テーブルに適用することができます。 仮想ハブのルート テーブルには、複数のルートを適用できます。

**追加の Virtual WAN リソース**

  * **サイト:** このリソースは、サイト間接続にのみ使用されます。 サイト リソースは **vpnsite** です。 これは、オンプレミスの VPN デバイスとその設定を表します。 Virtual WAN パートナーと連携することで、この情報を Azure に自動的にエクスポートする組み込みのソリューションが得られます。

## <a name="types-of-connectivity"></a><a name="connectivity"></a>接続の種類

Virtual WAN では、次の種類の接続を使用できます。サイト間 VPN、ユーザー VPN (ポイント対サイト)、および ExpressRoute です。

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>サイト間 VPN 接続

![Virtual WAN のダイアグラム](./media/virtual-wan-about/virtualwan.png)

仮想 WAN サイト間接続を作成するときに、利用可能なパートナーと連携できます。 パートナーを利用しない場合は、手動で接続を構成できます。 詳細については、[Virtual WAN を使用したサイト間接続の作成](virtual-wan-site-to-site-portal.md)に関するページを参照してください。

#### <a name="virtual-wan-partner-workflow"></a><a name="s2spartner"></a>Virtual WAN パートナーのワークフロー

Virtual WAN パートナーと連携する場合のワークフローは、次のとおりです。

1. ブランチ デバイス (VPN/SDWAN) コントローラーが、[Azure サービス プリンシパル](../active-directory/develop/howto-create-service-principal-portal.md)を使用してサイト中心の情報を Azure にエクスポートするために認証を受けます。
2. ブランチ デバイス (VPN/SDWAN) コントローラーが、Azure 接続構成を取得し、ローカル デバイスを更新します。 これにより、オンプレミス VPN デバイスの構成のダウンロード、編集、および更新が自動化されます。
3. デバイスに適切な Azure 構成が設定されると、Azure WAN に対してサイト間接続 (2 つのアクティブなトンネル) が確立されます。 Azure では、IKEv1 と IKEv2 の両方がサポートされています。 BGP はオプションです。

#### <a name="partners-for-site-to-site-virtual-wan-connections"></a><a name="partners"></a>サイト間仮想 WAN 接続のパートナー

利用可能なパートナーと場所の一覧については、[Virtual WAN のパートナーと場所](virtual-wan-locations-partners.md)に関する記事を参照してください。

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>ユーザー VPN (ポイント対サイト) 接続

IPsec/IKE (IKEv2) 接続または OpenVPN 接続を使用して、Azure 内のリソースに接続できます。 この種類の接続を使うには、クライアント コンピューター上で VPN クライアントを構成する必要があります。 詳細については、[ポイント対サイト接続の作成](virtual-wan-point-to-site-portal.md)に関する記事をご覧ください。

### <a name="expressroute-connections"></a><a name="er"></a>ExpressRoute 接続
ExpressRoute を使用すると、プライベート接続を介してオンプレミス ネットワークを Azure に接続できます。 接続を作成するには、[Virtual WAN を使用した ExpressRoute 接続の作成](virtual-wan-expressroute-portal.md)に関するページを参照してください。

## <a name="locations"></a><a name="locations"></a>場所

場所の情報については、[Virtual WAN のパートナーと場所](virtual-wan-locations-partners.md)に関する記事を参照してください。

## <a name="faq"></a><a name="faq"></a>FAQ

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>次のステップ

[Virtual WAN を使用してサイト間接続を作成する](virtual-wan-site-to-site-portal.md)
