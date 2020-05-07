---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 03/24/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5d2d33dc2ef135fde0955336a40f851d6ed4e0e7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82204453"
---
### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>ユーザーは、Azure Virtual WAN を使用するために、SD-WAN/VPN デバイスを利用したハブとスポークを用意する必要がありますか。

Virtual WAN では、サイト/サイト間 VPN 接続、ユーザー/P2S 接続、ExpressRoute 接続、Virtual Network 接続、VPN ExpressRoute 相互接続、VNET 間の推移的な接続、一元化されたルーティング、Azure ファイアウォールとファイアウォール マネージャーのセキュリティ、監視、ExpressRoute 暗号化など、多数の機能が 1 つの画面に組み込まれています。 Virtual WAN の利用を開始するために、これらのすべてのユース ケースを用意する必要はありません。 1 つのユース ケースのみで利用を開始できます。 Virtual WAN アーキテクチャは、スケールとパフォーマンスが組み込まれたハブ アンド スポーク アーキテクチャで、ブランチ (VPN/SD-WAN デバイス)、ユーザー (Azure VPN クライアント、openVPN、または IKEv2 クライアント)、ExpressRoute 回線、仮想ネットワークが仮想ハブのスポークとして機能します。 Standard Virtual WAN ではすべてのハブがフル メッシュで接続されるため、ユーザーは Any-to-Any (任意のスポーク) 接続に Microsoft バックボーンを簡単に使用できます。 SD-WAN/VPN デバイスを利用したハブとスポークの場合、ユーザーは Azure Virtual WAN ポータル内で手動で設定するか、Virtual WAN パートナーのCPE (SD-WAN/VPN) を使用して Azure への接続を設定することができます。 Virtual WAN パートナーは、デバイス情報を Azure にエクスポートし、Azure 構成をダウンロードして、Azure Virtual WAN ハブへの接続を確立できる、接続の自動化を提供しています。 ポイント対サイト/ユーザー VPN 接続の場合、[Azure VPN クライアント](https://go.microsoft.com/fwlink/?linkid=2117554)、OpenVPN、または IKEv2 クライアントがサポートされています。 

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Azure Virtual WAN ユーザー VPN (ポイント対サイト) ではどのクライアントがサポートされていますか。

Virtual WAN では [Azure VPN クライアント](https://go.microsoft.com/fwlink/?linkid=2117554)、OpenVPN クライアント、または任意の IKEv2 クライアントがサポートされています。 Azure VPN クライアントでは、Azure AD 認証がサポートされています。少なくとも Windows 10 クライアント OS バージョン 17763.0 以降が必要です。  OpenVPN クライアントでは、証明書ベースの認証をサポートできます。 ゲートウェイで証明書ベースの認証を選択すると、ご使用のデバイスにダウンロードできる .ovpn ファイルが表示されます。 IKEv2 では、証明書と RADIUS 認証の両方がサポートされています。 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>ユーザー VPN (ポイント対サイト) の場合、P2S クライアント プールが 2 つのルートに分割されるのはなぜですか。

各ゲートウェイには 2 つのインスタンスがあり、接続されたクライアントに対して各ゲートウェイ インスタンスが個別にクライアント IP を割り当てることができ、仮想ネットワークからのトラフィックを適切なゲート ウェイ インスタンスに戻るようルーティングしてゲートウェイ インスタンス間のホップを避けるために分割が行われます。

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>P2S クライアント用の DNS サーバーを追加するにはどうすればよいですか。

P2S クライアント用の DNS サーバーを追加するには、2つのオプションがあります。

1. Microsoft にサポート チケットを申請し、ハブに DNS サーバーを追加してもらいます
2. または、Windows 10 向けに Azure VPN クライアントを使用している場合は、ダウンロードしたプロファイル XML ファイルを変更して、 **\<dnsservers>\<dnsserver> \</dnsserver>\</dnsservers>** タグを追加してからインポートできます。

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>ユーザー VPN (ポイント対サイト) の場合、サポートされているクライアントの数はいくつですか。

各ユーザー VPN P2S ゲートウェイには 2 つのインスタンスがあり、各インスタンスではスケール ユニットの変更に応じて特定のユーザー数までサポートします。 スケール ユニット 1 から 3 では、500 接続がサポートされます。スケール ユニット 4 から 6 では、1,000 接続がサポートされます。スケール ユニット 7 から 12 では、5,000 接続がサポートされます。スケール ユニット 13 から 20 では最大 10,000 接続がサポートされます。 たとえば、ユーザーが 1 スケール ユニットを選択したとします。 各スケール ユニットはデプロイされた 1 つのアクティブ/アクティブ ゲートウェイを意味し、(この例では 2 つある) インスタンスのそれぞれで最大 500 接続がサポートされます。 このスケール ユニットでは、ゲートウェイあたり 500 接続 x 2 となるからといって、500 ではなく 1,000 を見込んで計画を立てるわけにはいきません。インスタンスがサービスを提供する必要がある場合に、推奨接続数を超えると、余剰分の 500 の接続が中断される可能性があるためです。

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Azure 仮想ネットワーク ゲートウェイ (VPN ゲートウェイ) と Azure Virtual WAN VPN ゲートウェイの違いは何ですか。

Virtual WAN は、大規模なサイト間接続を提供し、スループット、スケーラビリティ、使いやすさを考慮して構築されています。 Virtual WAN の VPN ゲートウェイにサイトを接続するとき、そのゲートウェイは、"VPN" タイプのゲートウェイを使用する通常の仮想ネットワーク ゲートウェイとは異なります。 同様に、ExpressRoute 回線を Virtual WAN ハブに接続するとき、ExpressRoute ゲートウェイには、"ExpressRoute" タイプのゲートウェイを使用する通常の仮想ネットワーク ゲートウェイとは異なるリソースが使用されます。 Virtual WAN は、VPN と ExpressRoute のどちらについても、最大 20 Gbps の総スループットをサポートします。 また、CPE 支店デバイス パートナーのエコシステムとの接続に関して、Virtual WAN は自動化に対応しています。 CPE 支店デバイスには自動化機能が組み込まれており、Azure Virtual WAN に対して自動的にプロビジョニングされ、接続されます。 これらのデバイスは、拡大を続ける SD-WAN および VPN パートナーのエコシステムから利用できます。 [推奨されるパートナーの一覧](../articles/virtual-wan/virtual-wan-locations-partners.md)を参照してください。

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Virtual WAN と Azure 仮想ネットワーク ゲートウェイは、どんな点が違いますか。

仮想ネットワーク ゲートウェイの VPN は、トンネル数が 30 に制限されています。 大規模な VPN の場合、接続するには、Virtual WAN を使用する必要があります。 各リージョン (仮想ハブ) につき最大 1,000 個の支店接続を、ハブあたり合計 20 Gbps で接続できます。 接続は、オンプレミス VPN デバイスから仮想ハブへのアクティブ/アクティブ型トンネルです。 リージョンごとに 1 つのハブを持つことができます。これは、ハブ全体で 1,000 を超える支店を接続できることを意味します。

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Virtual WAN ゲートウェイ スケール ユニットとは何か
スケール ユニットは、仮想ハブにおけるゲートウェイの総スループットを選択するために定義された単位です。 VPN の 1 スケール ユニットは 500 Mbps です。 ExpressRoute の 1 スケール ユニットは 2 Gbps です。 例:VPN の 10 スケール ユニットは 5 Gbps (= 500 Mbps * 10) を意味します。

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>どのデバイス プロバイダー (Virtual WAN パートナー) がサポートされていますか。

現時点で、多くのパートナーが、完全に自動化された Virtual WAN エクスペリエンスをサポートしています。 詳細については、[Virtual WAN パートナー](../articles/virtual-wan/virtual-wan-locations-partners.md)に関するページを参照してください。 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Virtual WAN パートナーの自動化手順はどのようになっていますか。

パートナーの自動化手順については、[Virtual WAN パートナーの自動化](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)に関するページを参照してください。

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>推奨パートナー デバイスを使用する必要がありますか。

いいえ。 Azure の IKEv2/IKEv1 IPsec サポートのための要件に準拠する、任意の VPN 対応デバイスを使用できます。

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Virtual WAN パートナーはどのように Azure Virtual WAN との接続を自動化しますか。

通常、ソフトウェア定義の接続ソリューションでは、コントローラーまたはデバイス プロビジョニング センターを使用してブランチ デバイスを管理します。 コントローラーは、Azure API を使用して、Azure Virtual WAN への接続を自動化できます。 自動化には、支店情報のアップロードや、Azure の構成のダウンロード、Azure 仮想ハブに対する IPsec トンネルの設定、支店デバイスから Azure Virtual WAN への接続の自動設定が含まれます。 何百もの支店が存在する場合は、Virtual WAN CPE パートナーを使用して接続するのが簡単です。オンボーディング エクスペリエンスにより、大規模な IPsec 接続の設定、構成、管理が不要となるためです。 詳細については、「[Virtual WAN partner automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)」 (Virtual WAN パートナーの自動化) を参照してください。


### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Virtual WAN では SD-WAN デバイスがどのようにサポートされますか。

Virtual WAN パートナーによって、Azure VPN エンドポイントへの IPsec 接続が自動化されます。 Virtual WAN パートナーが SD-WAN プロバイダーである場合、SD-WAN コントローラーで Azure VPN エンドポイントに対する自動化および IPsec 接続が管理されることを意味します。 SD-WAN デバイスに、専用の SD-WAN 機能の Azure VPN ではなく、独自のエンドポイントが必要である場合、SD-WAN エンドポイントを Azure VNet にデプロイし、Azure Virtual WAN と共存させることができます。

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Virtual WAN によって既存の接続機能は変更されますか。

既存の Azure 接続機能に変更はありません。

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Virtual WAN 用に使用できる新しい Resource Manager リソースはありますか。
  
はい。Virtual WAN では、新しい Resource Manager リソースが導入されます。 詳細については、[概要](../articles/virtual-wan/virtual-wan-about.md)に関するページを参照してください。

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>1 つのハブにはいくつの VPN デバイスを接続できますか。

1 つの仮想ハブにつき、最大で 1,000 件の接続がサポートされます。 各接続は 4 つのリンクから成り、各リンク接続は、アクティブ/アクティブ構成の 2 つのトンネルをサポートします。 トンネルは、Azure 仮想ハブの vpngateway で終了します。

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>オンプレミスの VPN デバイスは、複数のハブに接続できますか。

はい。 開始時のトラフィック フローは、オンプレミス デバイスから、最も近い Microsoft ネットワーク エッジへ、次に仮想ハブへとなります。

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Azure Virtual WAN で (NVA VNet 内に) 好みのネットワーク仮想アプライアンスをデプロイして使用できますか。

はい、お好みのネットワーク仮想アプライアンス (NVA) の VNet を Azure Virtual WAN に接続できます。 最初に、ハブ仮想ネットワーク接続を使用して、ネットワーク仮想アプライアンスの VNet をハブに接続します。 次に、仮想アプライアンスを指す次ホップを指定して仮想ハブ ルートを作成します。 仮想ハブのルート テーブルには、複数のルートを適用できます。 NVA の VNet に接続されているすべてのスポークの VNet ルートが確実にオンプレミス システムに伝達されるように、それらのスポークを追加で仮想ハブに接続する必要があります。

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>仮想ハブ内にネットワーク仮想アプライアンスを作成することはできますか。

ネットワーク仮想アプライアンス (NVA) を仮想ハブ内にデプロイすることはできません。 ただし、仮想ハブに接続されたスポーク VNet にそれを作成し、そのハブ内で、宛先 VNet に向かうトラフィックを NVA の (NIC の) IP アドレスを経由するよう誘導するルートを有効にすることはできます。

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>スポーク VNet に仮想ネットワーク ゲートウェイを配置することはできますか。

いいえ。 仮想ハブに接続されている場合、スポーク VNet に仮想ネットワーク ゲートウェイを配置することはできません。

### <a name="is-there-support-for-bgp"></a>BGP のサポートはありますか。

はい。BGP はサポートされています。 VPN サイトを作成するときに、BGP パラメーターをそこに指定することができます。 これは、そのサイトの Azure で作成されたすべての接続が BGP に対して有効になることを意味します。 さらに、NVA を含む VNet がある場合と、この NVA VNet が仮想 WAN ハブにアタッチされている場合、確実に NVA VNet からのルートが適切にアドバタイズされるように、NVA VNet にアタッチされているスポークで BGP を無効にする必要があります。 また、これらのスポーク VNet を仮想ハブ VNet に接続し、スポーク Vnet ルートがオンプレミス システムに確実に伝達されるようにします。

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>仮想ハブで UDR を使用してトラフィックを転送できますか。

はい、仮想ハブのルート テーブルを使用して、VNet にトラフィックを誘導できます。 特定の (通常は NVA の NIC の) IP アドレスを経由して Azure 内の宛先 VNet に向かうルートは、この方法で設定できます。

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Virtual WAN のライセンス情報や価格情報はありますか。

はい。 [価格](https://azure.microsoft.com/pricing/details/virtual-wan/)に関するページを参照してください。

### <a name="how-do-i-calculate-price-of-a-hub"></a>ハブの価格はどのように計算しますか。

* ハブのサービスは課金対象となります。 たとえば、支店またはオンプレミス デバイスが 10 個あり、それを Azure Virtual WAN に接続する必要がある場合、ハブの VPN エンドポイントに接続することになります。 たとえば、1 スケール ユニットが 500 Mbps の VPN である場合、これは $0.361/時で課金されます。 各接続は 1 時間あたり $0.05 が課金されます。 接続が 10 件の場合、1 時間あたりのサービス料金の合計は $0.361 + $0.5 となります。 Azure を離れるトラフィックに対するデータの料金が適用されます。

* 別途ハブの料金が発生します。 [価格](https://azure.microsoft.com/pricing/details/virtual-wan/)に関するページを参照してください。

* 仮想ハブに接続する ExpressRoute 回線のための ExpressRoute ゲートウェイがあった場合、支払う金額は、スケール ユニットの価格となります。 ER 内の各スケール ユニットは 2 Gbps で、接続ユニットはそれぞれ VPN 接続ユニットと同じレートで課金されます。

* スポーク VNET がハブに接続されている場合、スポーク VNET でのピアリング料金は引き続き適用されます。 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>ローンチ パートナーの一覧に記載されていない新しいパートナーが一覧に記載されるには、どうすればよいですか。

すべての仮想 WAN API はオープン API です。 ドキュメントを参照して、技術的実現可能性を評価することができます。 ご質問がある場合は、azurevirtualwan@microsoft.com 宛てにメールでお寄せください。 理想的なパートナーは、IKEv1 または IKEv2 IPsec 接続用にプロビジョニングできるデバイスを持つパートナーです。

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>使用しているデバイスが Virtual WAN パートナー リストにない場合はどうなりますか。 Azure Virtual WAN VPN に引き続き接続することはできますか。

はい。デバイスで IPsec IKEv1 または IKEv2 がサポートされている限り接続できます。 Virtual WAN パートナーによって、デバイスから Azure VPN エンドポイントへの接続が自動化されます。 これは '支店情報のアップロード'、'IPsec と構成'、'接続' などの手順が自動化されることを意味します。ご利用のデバイスが Virtual WAN パートナーのエコシステムからのものでない場合、手動で Azure の構成を取得し、デバイスを更新して IPsec 接続を設定するといった面倒な作業が必要になります。

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Resource Manager テンプレートを使用して Azure Virtual WAN を構築することはできますか。

1 つのハブを持つ 1 つの Virtual WAN と、1 つの vpnsite から成るシンプルな構成は、[クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network)を使用して作成できます。 Virtual WAN は、主として REST またはポータルによって駆動されるサービスです。

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Azure Virtual WAN ではグローバル VNet ピアリングがサポートされていますか。 

自分の仮想 WAN とは別のリージョンにある VNet を接続できます。

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>仮想ハブに接続されているスポーク VNet は相互に通信 (V2V 転送) できますか。

はい。 Standard Virtual WAN では、Vnet が接続されている Virtual WAN ハブを介して、Vnet 間の推移的な接続がサポートされています。 Virtual WAN の用語では、これらのパスを、VNet が単一のリージョン内の Virtual Wan Hub に接続されている場合は "ローカル Virtual WAN VNet 転送" と呼び、VNet が 2 つ以上のリージョンにまたがる複数の Virtual WAN Hub を通じて接続されている場合は "グローバル Virtual WAN VNet 転送" と呼びます。 VNet 転送では、パブリック プレビュー期間中、最大 3 Gbps のスループットがサポートされます。 グローバル転送が GA になると、スループットが拡張されます。

注:現在、V2V 転送 (プレビュー) でルーティング要素の起動をトリガーするには、仮想ハブに VPN GW がデプロイされている必要があります。 この VPN GW は、V2V 転送パスには使用されません。 これは既知の制限であり、V2V GA の時点で解除される予定です。 ハブの VPN Gateway は、V2V 転送機能には不要であるため、完全に起動した後は削除することができます。 

一部のシナリオでは、ローカルまたはグローバルの Virtual WAN VNet 転送の他に、[仮想ネットワーク ピアリング](../articles/virtual-network/virtual-network-peering-overview.md)を使用してスポーク Vnet を相互に直接ピアリングすることもできます。 この場合、Vnet ピアリングは、Virtual WAN ハブを介した推移的な接続よりも優先されます。 

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Azure Virtual WAN への支店接続とは

支店デバイスから Azure Virtual WAN への接続では、最大で 4 つのリンクがサポートされます。 リンクは、支店の場所における物理接続リンクです (ATT、Verizon など)。 それぞれのリンク接続は、2 つのアクティブ/アクティブ IPsec トンネルで構成されます。

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Virtual WAN では、支店間接続を行うことができますか?

はい。VPN 用または VPN から ExpressRoute 用の Virtual WAN では、支店間接続が利用可能です。

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>支店間トラフィックは、Azure Virtual WAN を横断しますか。

はい。

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Virtual WAN では、各サイトからの ExpressRoute が必要ですか。

いいえ。この Virtual WAN では、各サイトからの ExpressRoute は不要です。 デバイスから Azure Virtual WAN ハブへのインターネット リンク経由で標準 IPsec サイト間接続が使用されます。 サイトは、ExpressRoute 回線を使用してプロバイダーのネットワークに接続される場合があります。 仮想ハブ内で ExpressRoute を使用して接続されているサイトの場合、サイトでは VPN と ExpressRoute の間に、支店間のトラフィック フローを持つことができます。

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Azure Virtual WAN を使用する場合、ネットワーク スループットに制限はありますか。

支店の数は、ハブ/リージョンあたり 1,000 接続に制限されています。ハブ内では合計 20 Gbp までです。 リージョンごとに 1 つのハブを配置できます。

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Virtual WAN ハブでサポートされる VPN 接続の数を教えてください。

Azure Virtual WAN ハブでは、最大で 1,000 個のサイト間接続、10,000 個の P2S 接続、4 個の ExpressRoute 接続を同時にサポートできます。

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>VPN トンネルおよび接続の VPN スループットの合計を教えてください。

ハブの VPN スループットの合計は、選択されたスケール ユニットに基づき、最大で 20 Gbps です。 スループットはすべての既存の接続によって共有されます。 接続内の各トンネルは最大 1 Gbps をサポートできます。

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>仮想ハブの 20 Gbps の設定がポータルに表示されません。 どのように構成すればよいですか。

ポータルでハブ内の VPN ゲートウェイに移動し、スケール ユニットをクリックして適切な設定に変更します。

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Virtual WAN では、オンプレミス デバイスで複数の ISP を並行して利用できますか、それとも常に単一の VPN トンネルとなりますか。

仮想 WAN VPN への接続は常に、支店で利用可能なリンクを使用する、アクティブ/アクティブ トンネルとなります (同じハブまたはリージョン内の回復性の場合)。 このリンクは、オンプレミス支店の ISP リンクである可能性があります。 サイトには、Virtual WAN の "VPNSite" を通じてリンク情報を追加することができます。 複数の ISP が支店にあって、それぞれの ISP からリンクが提供されている場合は、その情報を Azure の VPN サイト情報に設定できます。 ただし、支店で複数の ISP にまたがるフェールオーバーを管理することは、完全にその支店主体のルーティング操作となります。

### <a name="what-is-global-transit-architecture"></a>グローバル トランジット アーキテクチャとは何ですか。

グローバル トランジット アーキテクチャについては、「[グローバル トランジット ネットワーク アーキテクチャと Virtual WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md)」を参照してください。

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Azure バックボーン上では、トラフィックはどのようにルーティングされますか。

トラフィックは、支店のデバイス -> ISP -> Microsoft ネットワーク エッジ -> Microsoft DC (ハブ VNet) -> Microsoft ネットワーク エッジ -> ISP -> 支店のデバイスというパターンに従います

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>このモデルでは、各サイトでどのようなものが必要ですか。 インターネット接続は必要ですか。

はい。 IPsec をサポートするインターネット接続および物理デバイスが必要です。統合 [Virtual WAN パートナー](../articles/virtual-wan/virtual-wan-locations-partners.md)が提供するものをお勧めします。 必要に応じて、好みのデバイスから、構成と Azure への接続を手動で管理できます。

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>接続 (VPN、ExpressRoute、Virtual Network) の既定のルート (0.0.0.0/0) を有効にするにはどうすればよいですか。

仮想ハブは、仮想ネットワーク、サイト間 VPN、または ExpressRoute 接続に対し、学習した既定のルートを伝達することができます (対応するフラグが、その接続で "有効" になっている場合)。 このフラグは、ユーザーが仮想ネットワーク接続、VPN 接続、または ExpressRoute 接続を編集するときに表示されます。 サイトまたは ExpressRoute 回線がハブに接続されると、このフラグは既定で無効になります。 VNet を仮想ハブに接続するための仮想ネットワーク接続が追加されたときは、既定で有効になります。 既定のルートの起点は Virtual WAN ハブではありません。Virtual WAN ハブにファイアウォールをデプロイした結果としてそのハブが既定のルートを既に学習している場合、または接続されている別のサイトで強制トンネリングが有効な場合に、既定のルートが伝達されます。

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Virtual WAN 内の仮想ハブは、どのようにして複数のハブからのルートに最適なパスを選択するのですか

仮想ハブが複数のリモート ハブから同じルートを学習する場合は、次の順序で決定されます。
1) ルートの発信元  a) ネットワーク ルート – 仮想ハブ ゲートウェイによって直接習得された VNET プレフィックス  b) ハブの RouteTable (静的に構成されたルート)  c) BGP  d) InterHub ルート
2)  ルート メトリック:Virtual WAN では、VPN よりも ExpressRoute が優先されます。 ExpressRoute ピアは VPN ピアと比べて重み付けが高くなります
3)  AS パスの長さ

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Virtual WAN に IPv6 のサポートはありますか。

IPv6 は、Virtual WAN ハブとそのゲートウェイではサポートされていません。IPv6 をサポートする VNET があり、その VNET を Virtual WAN に接続する必要がある場合、このシナリオもサポートされません。 

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Basic タイプの Virtual WAN と Standard タイプの Virtual WAN の違いは何ですか。

"Basic" タイプの WAN では、基本的なハブを作成できます (SKU = Basic)。 "Standard" タイプの WAN では、標準ハブを作成できます (SKU = Standard)。 Basic のハブは、サイト間 VPN 機能に限定されます。 Standard のハブでは、ハブを介した VNet 対 VNet トランジット、ExpressRoute、ユーザー VPN (P2S)、フル メッシュ ハブを利用できます。 Standard のハブでは、基本料金である 1 時間あたり $0.25 に加え、VNet 対 VNet 接続時のハブ経由トランジットに関するデータ処理の料金、およびハブ間のトラフィックに関するデータ処理の料金が課金されます。 詳細については、「[Basic と Standard の仮想 WAN](../articles/virtual-wan/virtual-wan-about.md#basicstandard)」を参照してください。 価格については、[価格](https://azure.microsoft.com/pricing/details/virtual-wan/)ページを参照してください。
