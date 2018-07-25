---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/10/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8c3f727c6154a0364f151d22000d2684c361676a
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39037212"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Azure 仮想ネットワーク ゲートウェイ (VPN Gateway) と Azure Virtual WAN vpngateway の違いは何ですか。

Virtual WAN は、大規模なサイト間接続を提供し、スループット、スケーラビリティ、使いやすさを考慮して構築されています。 CPE ブランチ デバイスは、自動的にプロビジョニングされ、Azure Virtual WAN に接続されます。 これらのデバイスは、拡大を続ける SD-WAN および VPN パートナーのエコシステムから利用できます。

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>ローンチ段階でどのデバイス プロバイダー (Virtual WAN パートナー) がサポートされていますか。 

現時点で、Citrix と Riverbed が、完全に自動化された Virtual WAN エクスペリエンスをサポートしています。 今後数か月で、Nokia Nuage、Palo Alto、Checkpoint など、さらに多くのパートナーが増える予定です。 詳細については、[Virtual WAN パートナー](https://aka.ms/virtualwan)に関するページを参照してください。

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>推奨パートナー デバイスを使用する必要がありますか。

いいえ。 プレビューの IKEv2 IPsec サポートのための要件に準拠する、任意の VPN 対応デバイスを使用できます。

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Virtual WAN パートナーはどのように Azure Virtual WAN との接続を自動化しますか。

通常、ソフトウェア定義の接続ソリューションでは、コントローラーまたはデバイス プロビジョニング センターを使用してブランチ デバイスを管理します。 コントローラーは、Azure API を使用して、Azure Virtual WAN への接続を自動化できます。 この仕組みの詳細については、[Virtual WAN パートナーによる自動化](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)に関するページを参照してください。

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

はい。お好みのネットワーク仮想アプライアンス (NVA) VNet を Azure Virtual WAN に接続できます。ただし、それには GA で予定されているハブのルーティング機能が必要です。 さらに、NVA VNet に接続されているすべてのスポークを仮想ハブに接続する必要があります。 

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>NVA VNet に仮想ネットワーク ゲートウェイを配置できますか。

いいえ。 仮想ハブに接続されている場合、NVA VNet に仮想ネットワーク ゲートウェイを配置することはできません。 

### <a name="is-there-support-for-bgp"></a>BGP のサポートはありますか。

はい。BGP はサポートされています。 NVA VNet からのルートが適切にアドバタイズされるようにするために、仮想ハブに接続されている NVA VNet にスポークが接続されている場合は BGP を無効にする必要があります。 さらに、スポーク VNet を仮想ハブに接続します。

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>仮想ハブで UDR を使用してトラフィックを転送できますか。

UDR とルーティング機能は GA までに利用可能になります。

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Virtual WAN のライセンス情報や価格情報はありますか。
 
プレビュー中は追加料金はかかりません。 プレビュー期間中は、現在の [Azure VPN とエグレスの料金](https://azure.microsoft.com/pricing/details/vpn-gateway/)がそのまま有効になります。