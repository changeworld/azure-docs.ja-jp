---
title: Azure Virtual WAN パートナーと場所 | Microsoft Docs
description: この記事には、Azure Virtual WAN パートナーおよびハブの場所の一覧が含まれています。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: d1655b42b57edaeeaaaada802a248f56e0ba1470
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2020
ms.locfileid: "91801462"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Virtual WAN パートナーと仮想ハブの場所

この記事では、仮想ハブへの接続に関して Virtual WAN でサポートされているリージョンおよびパートナーの情報を提供します。

Azure Virtual WAN は、Azure を介して支店間接続を最適化し、自動化するネットワーク サービスです。 Virtual WAN を使用すると、ブランチ デバイスを接続および構成して Azure と通信できます。 これは、手動で行うことも、Virtual WAN パートナーを介してプロバイダー デバイスを使用して行うこともできます。 パートナー デバイスを使用すると、使いやすさ、接続の簡素化、および構成管理を実現できます。

オンプレミス デバイスからの接続は、自動化された方法で仮想ハブに向けて確立されます。 仮想ハブは、Microsoft のマネージド仮想ネットワークです。 ハブには、オンプレミス ネットワーク (vpnsite) からの接続を可能にするさまざまなサービス エンドポイントが含まれています。 ハブはリージョンごとに 1 つしか存在できません。

## <a name="branch-ipsec-connectivity-automation-from-partners"></a><a name="automation"></a>パートナーからのブランチ IPSec 接続自動化

Azure Virtual WAN に接続するデバイスには、接続の自動化が組み込まれています。 これは通常、VPN ブランチ デバイスと Azure Virtual Hub VPN エンドポイント (VPN ゲートウェイ) 間の接続および構成管理を設定するデバイス管理 UI (または同等の機能) で設定されます。

デバイス コンソール/管理センターでは、次の高度な自動化が設定されます。

* デバイスが Azure Virtual WAN リソース グループにアクセスするための適切なアクセス許可
* Azure Virtual WAN へのブランチ デバイスのアップロード
* Azure 接続情報の自動ダウンロード
* オンプレミス ブランチ デバイスの構成 

接続パートナーによっては、自動化を拡張して Azure Virtual Hub VNet および VPN Gateway の作成を含める場合があります。 自動化の詳細については、[仮想 WAN パートナー向けの自動化ガイドライン](virtual-wan-configure-automation-providers.md)を参照してください。

## <a name="branch-ipsec-connectivity-partners"></a><a name="partners"></a>ブランチ IPSec 接続パートナー

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

次のパートナーが近い将来のロードマップに追加されることが予定されています。128 Technologies、Arista、Cisco Systems (Viptela)、F5 Networks、Oracle SD-WAN、SharpLink。

## <a name="partners-with-integrated-virtual-hub-offerings"></a>仮想ハブ サービスを統合しているパートナー
ブランチ オフィス IPSec 接続を自動化していることに加え、一部のパートナーは、Azure 仮想 WAN ハブに直接統合できる**ネットワーク仮想アプライアンス (NVA)** を提供しています。  これにより、お客様は仮想ハブ内の互換性のあるサードパーティ アプライアンスへのブランチ接続を終了することができます。  

仮想 WAN ハブで NVA を提供するパートナーは、次のことを行う必要があります。

* ブランチ デバイスから IPSec 接続自動化を実装し、NVA サービスを Azure 仮想 WAN ハブにオンボードしておきます。
* 既存のネットワーク仮想アプライアンス サービスを Azure Marketplace で入手できるようにします。

パートナーが仮想ハブ サービスのマネージド NVA について質問がある場合、vwannvaonboarding@microsoft.com にメールを送り、お問い合わせください。

## <a name="integrated-virtual-hub-nva-partners"></a>仮想ハブ NVA を統合したパートナー
これらのパートナーの**マネージド アプリケーション** サービスは仮想 WAN ハブにデプロイできるようになりました。

|パートナー|構成/方法/デプロイ ガイド|
|---|---|
|[Barracuda Networks](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overviewus/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overview)| [Barracuda CloudGen WAN デプロイ ガイド](https://campus.barracuda.com/product/cloudgenwan/doc/91980640/deployment/)|
|[Cisco Cloud Service Router (CSR) VWAN](https://aka.ms/ciscoMarketPlaceOffer)| Cisco Cloud Services (CSR) WAN を VWAN ハブに導入するパブリック プレビューの間、Cisco により、vwan_public_preview@external.cisco.com にメールを送り、vManage Deployment Guide をリクエストして Cisco EFT (Early Field Trial) 顧客として登録することがエンド カスタマーに求められます。 |

次のパートナーは、近い将来、仮想ハブで NVA を提供する予定です。Citrix、Versa Networks、VeloCloud。

## <a name="locations"></a><a name="locations"></a>場所

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>次のステップ

* Virtual WAN の詳細については、[Virtual WAN FAQ](virtual-wan-faq.md) を参照してください。

* Azure Virtual WAN への接続を自動化する方法については、[仮想 WAN パートナー向けの自動化ガイドライン](virtual-wan-configure-automation-providers.md)を参照してください。
