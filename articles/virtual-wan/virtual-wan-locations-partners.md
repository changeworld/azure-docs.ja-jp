---
title: Azure Virtual WAN パートナーの場所 | Microsoft Docs
description: この記事には、Azure Virtual WAN パートナーおよびハブの場所の一覧が含まれています。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: b7a3d443b64943c097a51eeae4be6253a0466e18
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933713"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Virtual WAN パートナーと仮想ハブの場所

この記事では、仮想ハブへの接続に関して Virtual WAN でサポートされているリージョンおよびパートナーの情報を提供します。

Azure Virtual WAN は、Azure を介して支店間接続を最適化し、自動化するネットワーク サービスです。 Virtual WAN を使用すると、ブランチ デバイスを接続および構成して Azure と通信できます。 これは、手動で行うことも、Virtual WAN パートナーを介してプロバイダー デバイスを使用して行うこともできます。 パートナー デバイスを使用すると、使いやすさ、接続の簡素化、および構成管理を実現できます。

オンプレミス デバイスからの接続は、自動化された方法で仮想ハブに向けて確立されます。 仮想ハブは、Microsoft のマネージド仮想ネットワークです。 ハブには、オンプレミス ネットワーク (vpnsite) からの接続を可能にするさまざまなサービス エンドポイントが含まれています。 ハブはリージョンごとに 1 つしか存在できません。

## <a name="automation"></a>接続パートナーからの自動化

Azure Virtual WAN に接続するデバイスには、接続の自動化が組み込まれています。 これは通常、VPN ブランチ デバイスと Azure Virtual Hub VPN エンドポイント (VPN ゲートウェイ) 間の接続および構成管理を設定するデバイス管理 UI (または同等の機能) で設定されます。

デバイス コンソール/管理センターでは、次の高度な自動化が設定されます。

* デバイスが Azure Virtual WAN リソース グループにアクセスするための適切なアクセス許可
* Azure Virtual WAN へのブランチ デバイスのアップロード
* Azure 接続情報の自動ダウンロード
* オンプレミス ブランチ デバイスの構成 

接続パートナーによっては、自動化を拡張して Azure Virtual Hub VNet および VPN Gateway の作成を含める場合があります。 自動化の詳細については、[Configure Automation – WAN Partners](virtual-wan-configure-automation-providers.md)\(自動化の構成 – WAN パートナー\) を参照してください。

## <a name="partners"></a>パートナー経由の接続

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

次のパートナーが近い将来のロードマップに追加されることが予定されています。Arista、Aruba HPE、Cisco Systems、F5 Networks、Open Systems、Oracle Talari、SharpLink、および VMWare Velocloud。

## <a name="locations"></a>場所

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>次のステップ

* Virtual WAN の詳細については、[Virtual WAN FAQ](virtual-wan-faq.md) を参照してください。

* Azure Virtual WAN への接続を自動化する方法については、[Virtual WAN Partners - How to automate](virtual-wan-configure-automation-providers.md)\(Virtual WAN パートナー - 自動化の方法\) を参照してください。
