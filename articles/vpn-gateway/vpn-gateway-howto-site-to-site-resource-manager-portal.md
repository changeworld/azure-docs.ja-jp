---
title: "オンプレミスのネットワークを Azure 仮想ネットワークに接続する: サイト間 VPN: ポータル | Microsoft Docs"
description: "パブリック インターネットを経由したオンプレミスのネットワークから Azure 仮想ネットワークへの IPsec 接続を作成する手順。 これらの手順は、ポータルを使用してクロスプレミスのサイト間 VPN Gateway 接続を作成する際に役立ちます。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 827a4db7-7fa5-4eaf-b7e1-e1518c51c815
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: fea9796d8ca03351573f091dce41689743821cad
ms.contentlocale: ja-jp
ms.lasthandoff: 05/09/2017


---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Azure Portal でサイト間接続を作成する

この記事では、Azure Portal を使用して、オンプレミス ネットワークから VNet へのサイト間 VPN Gateway 接続を作成する方法について説明します。 この記事の手順は、Resource Manager デプロイメント モデルに適用されます。 また、この構成の作成には、次のリストから別のオプションを選択して、別のデプロイ ツールまたはデプロイ モデルを使用することもできます。

> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager - CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [クラシック - Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [クラシック - クラシック ポータル](vpn-gateway-site-to-site-create.md)
> 
>


サイト間 VPN Gateway 接続は、IPsec/IKE (IKEv1 または IKEv2) VPN トンネルを介してオンプレミス ネットワークを Azure 仮想ネットワークに接続するために使用します。 この種類の接続では、外部接続用パブリック IP アドレスが割り当てられていてるオンプレミスの VPN デバイスが必要です。 VPN Gateway の詳細については、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」を参照してください。

![クロスプレミスのサイト間 VPN Gateway 接続の図](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a>開始する前に

構成を開始する前に、以下の条件を満たしていることを確認します。

* Resource Manager デプロイメント モデルを使用することを確認します。 [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* 互換性のある VPN デバイスおよびデバイスを構成できる人員。 互換性のある VPN デバイスとデバイスの構成の詳細については、[VPN デバイスの概要](vpn-gateway-about-vpn-devices.md)に関する記事を参照してください。
* VPN デバイスの外部接続用パブリック IPv4 IP アドレス。 この IP アドレスを NAT の内側に割り当てることはできません。
* オンプレミス ネットワーク構成の IP アドレス範囲を把握していない場合は、詳細な情報を把握している担当者と協力して作業を行ってください。 この構成を作成する場合は、Azure がオンプレミスの場所にルーティングする IP アドレス範囲のプレフィックスを指定する必要があります。 オンプレミス ネットワークのサブネットと接続先の仮想ネットワーク サブネットが重複しないようにしなければなりません。 

### <a name="values"></a>値の例
以下の手順を練習として使用する場合は、次の例の値を使用できます。

* **VNet の名前:** TestVNet1
* **アドレス空間:** 
    * 10.11.0.0/16
    * 10.12.0.0/16 (この演習では省略可能)
* **サブネット:**
  * FrontEnd: 10.11.0.0/24
  * BackEnd: 10.12.0.0/24 (この演習では省略可能)
  * GatewaySubnet: 10.11.255.0/27
* **リソース グループ:** TestRG1
* **場所:** 米国東部
* **DNS サーバー:** ご利用の DNS サーバーの IP アドレス
* **仮想ネットワーク ゲートウェイ名:** VNet1GW
* **パブリック IP:** VNet1GWIP
* **VPN の種類:** ルート ベース
* **接続の種類:** サイト間 (IPsec)
* **ゲートウェイの種類:** VPN
* **ローカル ネットワーク ゲートウェイ名:** Site2
* **接続名:** VNet1toSite2

## <a name="CreatVNet"></a>1.仮想ネットワークの作成

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-s2s-rm-portal-include.md)]

## <a name="dns"></a>2.DNS サーバーの指定
サイト間接続に DNS は不要です。 ただし仮想ネットワークにデプロイされたリソースで名前解決を使用する場合は、DNS サーバーを指定する必要があります。 この設定では、この仮想ネットワークの名前解決に使用する DNS サーバーを指定することができます。 この設定で、DNS サーバーは作成されません。

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>3.ゲートウェイ サブネットを作成する

仮想ネットワーク ゲートウェイでは、VPN ゲートウェイ サービスが使用する IP アドレスが含まれているゲートウェイ サブネットを使用します。 ゲートウェイ サブネットを作成する際は、名前を "GatewaySubnet " にする必要があります。 別の名前にすると、接続の構成は失敗します。

指定したゲートウェイ サブネットのサイズは、作成する VPN ゲートウェイの構成によって異なります。 /29 と同程度の小規模なゲートウェイ サブネットを作成することはできますが、/28 または /27 を選択してさらに多くのアドレスが含まれる大規模なサブネットを作成することをお勧めします。 大規模なゲートウェイ サブネットを使用すると、将来の構成に対応するのに十分な IP アドレスを確保できます。

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-s2s-rm-portal-include.md)]


## <a name="VNetGateway"></a>4.VPN ゲートウェイを作成する

[!INCLUDE [vpn-gateway-add-gw-s2s-rm-portal](../../includes/vpn-gateway-add-gw-s2s-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>5.ローカル ネットワーク ゲートウェイを作成する

ローカル ネットワーク ゲートウェイは通常、オンプレミスの場所を指します。 サイトに Azure が参照できる名前を付け、接続を作成するオンプレミス VPN デバイスの IP アドレスを指定します。 また、VPN ゲートウェイを介して VPN デバイスにルーティングされる IP アドレスのプレフィックスも指定します。 指定するアドレスのプレフィックスは、オンプレミス ネットワークのプレフィックスです。 オンプレミス ネットワークが変わった場合は、プレフィックスを簡単に更新できます。

[!INCLUDE [vpn-gateway-add-lng-s2s-rm-portal](../../includes/vpn-gateway-add-lng-s2s-rm-portal-include.md)]

## <a name="VPNDevice"></a>6.VPN デバイスの構成

オンプレミス ネットワークとのサイト間接続には VPN デバイスが必要です。 この手順では、VPN デバイスを構成します。 VPN デバイスを構成する際に、次の情報が必要になります。

- 共有キー。 サイト間 VPN 接続を作成するときにも、これと同じ共有キーを指定します。 ここで紹介している例では、基本的な共有キーを使用しています。 実際には、もっと複雑なキーを生成して使用することをお勧めします。
- 仮想ネットワーク ゲートウェイのパブリック IP アドレス。 パブリック IP アドレスは、Azure Portal、PowerShell、または CLI を使用して確認できます。 Azure Portal を使用して VPN ゲートウェイのパブリック IP アドレスを調べるには、**[仮想ネットワーク ゲートウェイ]** に移動し、該当するゲートウェイの名前をクリックします。


[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>7.VPN 接続を作成する

仮想ネットワーク ゲートウェイとオンプレミス VPN デバイスとの間にサイト間 VPN 接続を作成します。

[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include.md)]

## <a name="VerifyConnection"></a>8.VPN 接続の確認

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]


## <a name="next-steps"></a>次のステップ

*  BGP の詳細については、[BGP の概要](vpn-gateway-bgp-overview.md)に関する記事と [BGP の構成方法](vpn-gateway-bgp-resource-manager-ps.md)に関する記事を参照してください。
*  強制トンネリングについては、[強制トンネリング](vpn-gateway-forced-tunneling-rm.md)に関する記事を参照してください。
*  高可用性のアクティブ/アクティブ接続については、「[高可用性のクロスプレミス接続および VNet 間接続](vpn-gateway-highlyavailable.md)」を参照してください。
