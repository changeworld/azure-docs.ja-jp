---
title: Azure Virtual WAN パートナーの場所 | Microsoft Docs
description: この記事には、Azure Virtual WAN パートナーおよびハブの場所の一覧が含まれています
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: 76542392522ff14642ed7ccc6de8ed543d66513f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984995"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Virtual WAN パートナーと仮想ハブの場所

この記事では、仮想ハブへの接続に関して Virtual WAN でサポートされているリージョンおよび優先パートナーの情報を提供します。

Azure Virtual WAN は、Azure を介して支店間接続を最適化し、自動化するネットワーク サービスです。 Virtual WAN を使用すると、ブランチ デバイスを接続および構成して Azure と通信できます。 これは、手動で行うことも、Virtual WAN 推奨パートナーを介して推奨プロバイダー デバイスを使用して行うこともできます。 推奨パートナー デバイスを使用すると、使いやすさ、接続の簡素化、および構成管理を実現できます。 オンプレミス デバイスからの接続は、自動化された方法で仮想ハブに向けて確立されます。 仮想ハブは、Microsoft が管理する仮想ネットワークです。 ハブには、オンプレミス ネットワーク (vpnsite) からの接続を可能にするさまざまなサービス エンドポイントが含まれています。 ハブはリージョンごとに 1 つしか存在できません。

## <a name="regions"></a>リージョン

サポートされていて利用可能なリージョンの一覧は次のとおりです。

|地理的リージョン | Azure Azure リージョン|
|---|---|
|北米 | 米国東部、米国西部、米国東部 2、米国西部 2、米国中部、米国中南部、米国中北部、米国中西部、カナダ中部、カナダ東部 |
|南アメリカ |ブラジル南部 |
| ヨーロッパ | フランス中部、フランス南部、北ヨーロッパ、西ヨーロッパ、英国西部、英国南部 |
| アジア | 東アジア、東南アジア |
| 日本  | 西日本、東日本 |
| オーストラリア | オーストラリア東部、オーストラリア南東部 | 
| オーストラリア政府 | オーストラリア中部、オーストラリア中部 2 |
| インド | インド西部、インド中部、インド南部 |
| 韓国 | 韓国中部、韓国南部 | 

## <a name="automation-from-connectivity-partners"></a>接続パートナーからの自動化

このセクションでは、接続プロバイダーからの自動化の高度な詳細について説明します。

Azure Virtual WAN に接続するデバイスには、接続の自動化が組み込まれています。 これは通常、VPN ブランチ デバイスと Azure Virtual Hub VPN エンドポイント (VPN ゲートウェイ) 間の接続および構成管理を設定するデバイス管理 UI (または同等の機能) で設定されます。

デバイス コンソール/管理センターでは、次の高度な自動化が設定されます。

* デバイスが Azure Virtual WAN リソース グループにアクセスするための適切なアクセス許可
* Azure Virtual WAN へのブランチ デバイスのアップロード
* Azure 接続情報の自動ダウンロード 
* オンプレミス ブランチ デバイスの構成 

接続パートナーによっては、自動化を拡張して Azure Virtual Hub VNet および VPN Gateway の作成を含める場合があります。 自動化の詳細については、[Configure Automation – WAN Partners](virtual-wan-configure-automation-providers.md)\(自動化の構成 – WAN パートナー\) を参照してください。

## <a name="connectivity-through-preferred-partners"></a>優先パートナーを通じた接続

お客様のブランチ デバイス パートナーが次のセクションの一覧に含まれていない場合は、お客様のブランチ デバイス プロバイダーにお問い合わせの上、プロバイダーから azurevirtualwan@microsoft.com まで電子メールでご連絡ください。

次のリンクから、推奨パートナーが提供するサービスの詳細情報を収集できます。 

|推奨パートナー|
|---|
|[Barracuda Networks](https://www.barracuda.com/AzurevWAN)|
| [Check Point](https://www.checkpoint.com/solutions/microsoft-azure-virtual-wan/) |
| [Citrix](https://www.citrix.com/global-partners/microsoft/sd-wan-for-azure-virtual-wan.html)|
|[Netfoundry](https://netfoundry.io/solutions/netfoundry-for-microsoft-azure-virtual-wan/)|
|[Palo Alto Networks](https://researchcenter.paloaltonetworks.com/2018/09/azure-vwan-integration/) |
|[Riverbed Technology](https://www.riverbed.com/go/steelconnect-azurewan.html)|
|[128 Technology](https://www.128technology.com/partners/azure) |

## <a name="next-steps"></a>次の手順

Virtual WAN の詳細については、[Virtual WAN FAQ](virtual-wan-faq.md) を参照してください。

Azure Virtual WAN への接続を自動化する方法については、[Virtual WAN Partners - How to automate](virtual-wan-configure-automation-providers.md)\(Virtual WAN パートナー - 自動化の方法\) を参照してください。
