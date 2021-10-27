---
title: Azure Virtual WAN パートナーと場所 | Microsoft Docs
description: この記事には、Azure Virtual WAN パートナーおよびハブの場所の一覧が含まれています。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: cherylmc
ms.custom: references_regions
ms.openlocfilehash: a7c94118b1b1a08f4bbd7b58faec6008f9ebc0f8
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130002740"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Virtual WAN パートナーと仮想ハブの場所

この記事では、仮想ハブへの接続に関して Virtual WAN でサポートされているリージョンおよびパートナーの情報を提供します。

Azure Virtual WAN は、Azure を介して支店間接続を最適化し、自動化するネットワーク サービスです。 Virtual WAN を使用すると、ブランチ デバイスを接続および構成して Azure と通信できます。 接続と構成は、手動で、または Virtual WAN パートナーを通じてプロバイダー デバイスを使用して行うことができます。 パートナー デバイスを使用すると、使いやすさ、接続の簡素化、および構成管理を実現できます。

オンプレミス デバイスからの接続は、自動化された方法で仮想ハブに向けて確立されます。 仮想ハブは、Microsoft のマネージド仮想ネットワークです。 ハブには、オンプレミス ネットワーク (vpnsite) からの接続を可能にするさまざまなサービス エンドポイントが含まれています。 

## <a name="branch-ipsec-connectivity-automation-from-partners"></a><a name="automation"></a>パートナーからのブランチ IPsec 接続の自動化

Azure Virtual WAN に接続するデバイスには、接続の自動化が組み込まれています。 これは通常、VPN ブランチ デバイスと Azure Virtual Hub VPN エンドポイント (VPN ゲートウェイ) 間の接続および構成管理を設定するデバイス管理 UI (または同等の機能) で設定されます。

デバイス コンソール/管理センターでは、次の高度な自動化が設定されます。

* デバイスが Azure Virtual WAN リソース グループにアクセスするための適切なアクセス許可
* Azure Virtual WAN へのブランチ デバイスのアップロード
* Azure 接続情報の自動ダウンロード
* オンプレミス ブランチ デバイスの構成 

接続パートナーによっては、自動化を拡張して Azure Virtual Hub VNet および VPN Gateway の作成を含める場合があります。 自動化の詳細については、[仮想 WAN パートナー向けの自動化ガイドライン](virtual-wan-configure-automation-providers.md)を参照してください。

## <a name="branch-ipsec-connectivity-partners"></a><a name="partners"></a>ブランチ IPsec 接続のパートナー

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

128 Technologies、Arista、F5 Networks、Oracle SD-WAN (Talari)、SharpLink の各パートナーは、パートナーのデバイスと Azure Virtual WAN VPN Gateway 間の IPsec 接続の自動化作業の範囲を示す、両社間で締結された条件概要書に基づき、当社のロードマップに記載されています。

## <a name="partners-with-integrated-virtual-hub-offerings"></a>仮想ハブ サービスを統合しているパートナー

ブランチ オフィス IPsec 接続を自動化していることに加え、一部のパートナーは、Azure Virtual WAN ハブに直接統合できる **ネットワーク仮想アプライアンス (NVA)** を提供しています。  これにより、お客様は仮想ハブ内の互換性のあるサードパーティ アプライアンスへのブランチ接続を終了することができます。  

仮想 WAN ハブで NVA を提供するパートナーは、次のことを行う必要があります。

* ブランチ デバイスから IPsec 接続自動化を実装し、NVA サービスを Azure Virtual WAN ハブにオンボードしておきます。
* 既存のネットワーク仮想アプライアンス サービスを Azure Marketplace で入手できるようにします。

パートナーが仮想ハブ サービスのマネージド NVA について質問がある場合、vwannvaonboarding@microsoft.com にメールを送り、お問い合わせください。

## <a name="integrated-virtual-hub-nva-partners"></a>仮想ハブ NVA を統合したパートナー

これらのパートナーの **マネージド アプリケーション** サービスは仮想 WAN ハブにデプロイできるようになりました。

|パートナー|構成/方法/デプロイ ガイド|
|---|---|
|[Barracuda Networks](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overviewus/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overview)| [Barracuda CloudGen WAN デプロイ ガイド](https://campus.barracuda.com/product/cloudgenwan/doc/91980640/deployment/)|
|[Cisco Cloud Service Router (CSR) VWAN](https://aka.ms/ciscoMarketPlaceOffer)| Cisco SD-WAN ソリューションと Azure 仮想 WAN の統合により、Cloud OnRamp のマルチクラウド デプロイが強化され、Azure Virtual WAN ハブでネットワーク仮想アプライアンス (NVA) として Cisco Catalyst 8000V Edge ソフトウェア (Cisco Catalyst 8000V) を構成できます。 [Cisco SD-WAN Cloud OnRamp、Cisco IOS XE リリース 17.x 構成ガイドをご覧ください](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) 
|[Virtual WAN ハブ内の VMware SD-WAN](https://sdwan.vmware.com/partners/microsoft) | 仮想 WAN ハブで VMware SD-WAN アプライアンスを表示する手順については、[Virtual WAN ハブ内の VMware SD-WAN デプロイ ガイド](https://kb.vmware.com/s/article/82746)を参照してください。 デプロイするには、[ここで](https://azuremarketplace.microsoft.com/marketplace/apps/velocloud.vmware_sdwan_in_vwan)マネージド アプリケーションにアクセスしてください|

Aviatrix、Citrix、Versa Networks、Silver Peak の各パートナーは、近い将来、仮想ハブ サービスでネットワーク仮想アプライアンスを提供する予定です。

## <a name="locations"></a><a name="locations"></a>場所

[!INCLUDE [Virtual WAN regions file](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>次のステップ

* Virtual WAN の詳細については、[Virtual WAN FAQ](virtual-wan-faq.md) を参照してください。

* Azure Virtual WAN への接続を自動化する方法については、[仮想 WAN パートナー向けの自動化ガイドライン](virtual-wan-configure-automation-providers.md)を参照してください。
