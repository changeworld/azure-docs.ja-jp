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
ms.date: 01/23/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: eea00841708212789e14fa8717d83dd81d472bac
ms.openlocfilehash: c99788aded849933289555dd133b8146d6b2bdf2
ms.lasthandoff: 01/27/2017


---
# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-portal"></a>Azure Portal を使用してサイト間接続を持つ VNet を作成する
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [クラシック - クラシック ポータル](vpn-gateway-site-to-site-create.md)
> 
> 

この記事では、Azure Resource Manager デプロイメント モデルと Azure Portal を使用して、仮想ネットワークと、オンプレミス ネットワークに対するサイト間 VPN ゲートウェイ接続を作成する手順について説明します。 サイト間接続は、クロスプレミスおよびハイブリッド構成に使用できます。

![クロスプレミスのサイト間 VPN Gateway 接続の図](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>サイト間接続のデプロイメント モデルとデプロイ方法
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

以下の表は、サイト間の構成に関して現在利用できるデプロイメント モデルとデプロイ方法を示しています。 構成手順を説明した記事が利用できるようになったら、表から直接リンクできるようにします。

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>追加の構成
VNet どうしは接続しても、オンプレミスへの接続は作成しない場合は、 [VNet 間の接続の構成](vpn-gateway-vnet-vnet-rm-ps.md)に関するページを参照してください。 既存の接続が存在する VNet にサイト間接続を追加する場合は、[VPN Gateway 接続が既に存在する VNet へのサイト間接続の追加](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に
構成を開始する前に、以下が揃っていることを確認します。

* 互換性のある VPN デバイスおよびデバイスを構成できる人員。 「 [VPN デバイスについて](vpn-gateway-about-vpn-devices.md)」を参照してください。 VPN デバイスの構成に詳しくない場合や、オンプレミス ネットワーク構成の IP アドレス範囲を把握していない場合は、詳細な情報を把握している担当者と協力して作業を行ってください。
* VPN デバイスの外部接続用パブリック IP アドレス。 この IP アドレスを NAT の内側に割り当てることはできません。
* Azure サブスクリプション。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、[無料アカウント](http://azure.microsoft.com/pricing/free-trial)にサインアップしてください。

### <a name="a-namevaluesasample-configuration-values-for-this-exercise"></a><a name="values"></a>この演習のサンプル構成値
以下の手順を練習として使用する場合は、次のサンプル構成値を使用してください。

* **VNet の名前:** TestVNet1
* **アドレス空間:** 10.11.0.0/16 と 10.12.0.0/16
* **サブネット:**
  * FrontEnd: 10.11.0.0/24
  * BackEnd: 10.12.0.0/24
  * GatewaySubnet: 10.12.255.0/27
* **リソース グループ:** TestRG1
* **場所:** 米国東部
* **DNS サーバー:** 8.8.8.8
* **ゲートウェイ名:** VNet1GW
* **パブリック IP:** VNet1GWIP
* **VPN の種類:** ルート ベース
* **接続の種類:** サイト間 (IPsec)
* **ゲートウェイの種類:** VPN
* **ローカル ネットワーク ゲートウェイ名:** Site2
* **接続名:** VNet1toSite2

## <a name="a-namecreatvneta1-create-a-virtual-network"></a><a name="CreatVNet"></a>1.仮想ネットワークの作成
既に VNet がある場合は、設定が VPN ゲートウェイの設計に適合していることを確認します。 特に、他のネットワークと重複している可能性のあるサブネットに注意してください。 サブネットの重複があると、接続が適切に動作しません。 VNet が正しい設定で構成されていたら、「 [DNS サーバーの指定](#dns) 」セクションの手順に進んでください。

### <a name="to-create-a-virtual-network"></a>仮想ネットワークを作成するには
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="a-namesubnetsa2-add-additional-address-space-and-subnets"></a><a name="subnets"></a>2.アドレス空間とサブネットの追加
VNet が作成されたら、アドレス空間とサブネットをさらに追加できます。

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="a-namednsa3-specify-a-dns-server"></a><a name="dns"></a>3.DNS サーバーの指定
### <a name="to-specify-a-dns-server"></a>DNS サーバーを指定するには
[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="a-namegatewaysubneta4-create-a-gateway-subnet"></a><a name="gatewaysubnet"></a>4.ゲートウェイ サブネットの作成
仮想ネットワークをゲートウェイに接続する前に、まず、接続先の仮想ネットワークのゲートウェイ サブネットを作成する必要があります。 将来的な構成要件も見越して十分な IP アドレスを確保するために、可能であれば、/28 または /27 の CIDR ブロックを使用してゲートウェイ サブネットを作成することをお勧めします。

練習としてこの構成を作成する場合は、ゲートウェイ サブネットの作成時に、上記の [値](#values) を参照してください。

### <a name="to-create-a-gateway-subnet"></a>ゲートウェイ サブネットを作成するには
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="a-namevnetgatewaya5-create-a-virtual-network-gateway"></a><a name="VNetGateway"></a>5.仮想ネットワーク ゲートウェイの作成
練習としてこの構成を作成する場合は、[サンプル構成値](#values)を参照してください。

### <a name="to-create-a-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイを作成するには
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="a-namelocalnetworkgatewaya6-create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>6.ローカル ネットワーク ゲートウェイの作成
"ローカル ネットワーク ゲートウェイ" は、オンプレミスの場所を指します。 Azure から参照できるように、ローカル ネットワーク ゲートウェイに名前を付けます。 

練習としてこの構成を作成する場合は、[サンプル構成値](#values)を参照してください。

### <a name="to-create-a-local-network-gateway"></a>ローカル ネットワーク ゲートウェイを作成するには
[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="a-namevpndevicea7-configure-your-vpn-device"></a><a name="VPNDevice"></a>7.VPN デバイスの構成
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="a-namecreateconnectiona8-create-a-site-to-site-vpn-connection"></a><a name="CreateConnection"></a>8.サイト間 VPN 接続の作成
仮想ネットワーク ゲートウェイと VPN デバイスの間にサイト間 VPN 接続を作成します。 サンプルの値は必ず実際の値に変更してください。 共有キーは、VPN デバイスの構成に使用したものと同じ値にする必要があります。 

このセクションを開始する前に、仮想ネットワーク ゲートウェイとローカル ネットワーク ゲートウェイの作成が完了していることを確認してください。 練習としてこの構成を作成する場合は、接続の作成時に、上記の [値](#values) を参照してください。

### <a name="to-create-the-vpn-connection"></a>VPN 接続を作成するには
[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## <a name="a-nameverifyconnectiona9-verify-the-vpn-connection"></a><a name="VerifyConnection"></a>9.VPN 接続の確認
VPN 接続の確認はポータルで行えるほか、PowerShell を使って確認することもできます。

[!INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="next-steps"></a>次のステップ
*  接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、[Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) に関するページを参照してください。
*  BGP の詳細については、[BGP の概要](vpn-gateway-bgp-overview.md)に関する記事と [BGP の構成方法](vpn-gateway-bgp-resource-manager-ps.md)に関する記事を参照してください。


