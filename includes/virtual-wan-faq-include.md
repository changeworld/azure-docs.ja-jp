---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/18/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d059cab5668eef8d4dafc1442ca9749a7dcf8c9d
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162516"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Azure 仮想ネットワーク ゲートウェイ (VPN Gateway) と Azure Virtual WAN vpngateway の違いは何ですか。

Virtual WAN は、大規模なサイト間接続を提供し、スループット、スケーラビリティ、使いやすさを考慮して構築されています。 CPE ブランチ デバイスは、自動的にプロビジョニングされ、Azure Virtual WAN に接続されます。 これらのデバイスは、拡大を続ける SD-WAN および VPN パートナーのエコシステムから利用できます。

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>ローンチ段階でどのデバイス プロバイダー (Virtual WAN パートナー) がサポートされていますか。 

現時点で、Citrix と Riverbed が、完全に自動化された Virtual WAN エクスペリエンスをサポートしています。 詳細については、[Virtual WAN パートナー](https://aka.ms/virtualwan)に関するページを参照してください。

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>推奨パートナー デバイスを使用する必要がありますか。

いいえ。 プレビューの IKEv2 IPsec サポートのための要件に準拠する、任意の VPN 対応デバイスを使用できます。

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Virtual WAN パートナーはどのように Azure Virtual WAN との接続を自動化しますか。

通常、ソフトウェア定義の接続ソリューションでは、コントローラーまたはデバイス プロビジョニング センターを使用してブランチ デバイスを管理します。 コントローラーは、Azure API を使用して、Azure Virtual WAN への接続を自動化できます。 詳細については、「[Virtual WAN partner automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)」 (Virtual WAN パートナーの自動化) を参照してください。

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Virtual WAN によって既存の接続機能は変更されますか。   

既存の Azure 接続機能に変更はありません。

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Virtual WAN 用に使用できる新しい Resource Manager リソースはありますか。
  
はい。Virtual WAN では、新しい Resource Manager リソースが導入されます。 詳細については、[概要](https://go.microsoft.com/fwlink/p/?LinkId=2004389)に関するページを参照してください。

### <a name="are-there-any-special-requirements-to-join-the-preview"></a>プレビューに参加するうえで特別な要件はありますか。 

Azure Virtual WAN を構成する前に、まずプレビューにサブスクリプションを登録する必要があります。 登録するには、サブスクリプション ID を明記して、<azurevirtualwan@microsoft.com> にメールを送信します。 サブスクリプションが登録されると、メールが届きます。 サブスクリプションが登録されたことを示す確認を受け取るまでは、ポータルで Virtual WAN を使用できません。

考慮事項:

* プレビューは、Azure パブリック リージョンのみに制限されます。
* 1 つの仮想ハブにつき、最大で 100 件の接続がサポートされます。 各接続は、アクティブ/アクティブ構成の 2 つのトンネルで構成されます。 トンネルは、Azure Virtual Hub vpngateway で終了します。
* 次の条件に該当する場合は、このプレビューの使用を検討してください。
  * 仮想ハブあたり 1 Gbps 未満の集約帯域幅をデプロイしたい。
  * ルートベースの構成と IKEv2 IPsec 接続をサポートする VPN デバイスがある。
  * SD-WAN の使用とブランチ デバイスの運用に関してローンチ パートナー (Riverbed と Citrix) を利用したい。<br>or<br>オンプレミス デバイスの構成管理を含む、支店間の接続および支店と Azure の間の接続を設定したい。 (自己構成)

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Azure Virtual WAN ではグローバル VNet ピアリングがサポートされていますか。 

 いいえ。

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>仮想ハブに接続されているスポーク VNet は相互に通信できますか。

はい。 仮想ハブに接続されているスポーク間で VNet ピアリングを直接行うことができます。 詳細については、「[仮想ネットワーク ピアリング](../articles/virtual-network/virtual-network-peering-overview.md)」を参照してください。

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Azure Virtual WAN で (NVA VNet 内に) 好みのネットワーク仮想アプライアンスをデプロイして使用できますか。

はい。お好みのネットワーク仮想アプライアンス (NVA) VNet を Azure Virtual WAN に接続できます。ただし、それには、ロードマップで予定されているハブのルーティング機能が必要です。 さらに、NVA VNet に接続されているすべてのスポークを仮想ハブに接続する必要があります。 

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>NVA VNet に仮想ネットワーク ゲートウェイを配置できますか。

いいえ。 仮想ハブに接続されている場合、NVA VNet に仮想ネットワーク ゲートウェイを配置することはできません。 

### <a name="is-there-support-for-bgp"></a>BGP のサポートはありますか。

はい。BGP はサポートされています。 NVA VNet からのルートが適切にアドバタイズされるようにするために、仮想ハブに接続されている NVA VNet にスポークが接続されている場合は BGP を無効にする必要があります。 さらに、スポーク VNet を仮想ハブに接続します。

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>仮想ハブで UDR を使用してトラフィックを転送できますか。

これは、ロードマップで予定されています。 しばらくお待ちください。

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Virtual WAN のライセンス情報や価格情報はありますか。
 
プレビュー中は追加料金はかかりません。 プレビュー期間中は、現在の [Azure VPN とエグレスの料金](https://azure.microsoft.com/pricing/details/vpn-gateway/)がそのまま有効になります。

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>ローンチ パートナーの一覧に記載されていない新しいパートナーが一覧に記載されるには、どうすればよいですか。

azurevirtualwan@microsoft.com に電子メールを送信する。 理想的なパートナーは、IKEv2 IPSec 接続用にプロビジョニングできるデバイスを持つパートナーです。

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Resource Manager テンプレートを使用して Azure Virtual WAN を構築することはできますか。

今後可能になる予定です。 現時点では、サービスは、REST およびポータルで駆動します。

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Virtual WAN では、支店間接続を行うことができますか?

はい。Virtual WAN では、支店間接続が利用可能です。

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>支店間トラフィックは、Azure Virtual WAN を横断しますか。

はい。

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Virtual WAN と既存の Azure Virtual Network Gateway は、どんな点が違いますか。

Virtual Network Gateway VPN は、トンネル数が 30 に制限されています。 大規模な VPN の場合、接続するには、Virtual WAN を使用する必要があります。 パブリック プレビューでは、ハブ内で支店の接続数は 100、速度は 1 Gbps に制限されています。

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>この Virtual WAN では、各サイトからの ExpressRoute が必要ですか。

いいえ。この Virtual WAN では、各サイトからの ExpressRoute は不要です。 デバイスから Azure Virtual WAN ハブへのインターネット リンク経由で標準 IPsec サイト間接続が使用されます。

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Azure Virtual WAN を使用する場合、ネットワーク スループットに制限はありますか。

パブリック プレビューでは、支店の数は、ハブ/リージョンあたり 100 接続に制限されています。ハブ内では、合計 1 G までです。

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Virtual WAN では、オンプレミス デバイスで複数の ISP を並行して利用できますか、それとも常に単一の VPN トンネルですか。

はい。支店のデバイスにもよりますが、単一の支店からアクティブ/アクティブ トンネル (2 トンネル = 1 Azure Virtual WAN 接続) を利用できます。

### <a name="how-is-traffic-is-routed-on-the-azure-backbone"></a>Azure バックボーン上では、トラフィックはどのようにルーティングされますか。

トラフィックは、支店のデバイス -> ISP -> Microsoft Edge -> Microsoft DC -> Microsoft Edge -> ISP -> 支店のデバイスというパターンに従います。

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>このモデルでは、各サイトでどのようなものが必要ですか。 インターネット接続は必要ですか。

はい。 インターネット接続と物理デバイスが必要です。物理デバイスは、統合パートナーが提供するものをお勧めします。 これは、Azure への接続および構成をご使用のデバイスから手動で管理しない場合に必要となります。