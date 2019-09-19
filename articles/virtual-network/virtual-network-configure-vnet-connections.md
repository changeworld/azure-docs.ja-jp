---
title: VNet または VPN 接続の構成および検証
description: さまざまな Azure VPN および VNet デプロイを構成して検証するためのステップ バイ ステップ ガイダンス
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: fc4b649ce8d082d8d854c4c19b617c088ff3141c
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901435"
---
# <a name="configure-and-validate-vnet-or-vpn-connections"></a>VNet または VPN 接続の構成および検証

このガイド付きチュートリアルでは、トランジット ルーティング、VNet 間、BGP、マルチサイト、ポイント対サイトなどのシナリオで、Azure VPN および VNet のさまざまなデプロイを構成して検証するステップ バイ ステップ ガイダンスを提供します。

Azure VPN ゲートウェイを使用すると、ほぼすべての種類の接続された Virtual Network (VNet) トポロジを Azure に柔軟に配置できます。VNet は、リージョン間、VNet の種類間 (Azure Resource Manager とクラシック)、Azure 内またはオンプレミスのハイブリッド環境で、異なるサブスクリプションなどで、接続できます。 

## <a name="scenario-1-vnet-to-vnet-vpn-connection"></a>シナリオ 1:VNet 間 VPN 接続

VPN を経由した仮想ネットワークから別の仮想ネットワーク (VNet 間) への接続は、VNet からオンプレミス サイトの場所への接続に似ています。 どちらの接続の種類でも、VPN ゲートウェイを使用して、**IPsec/IKE** を使った安全なトンネルが提供されます。 仮想ネットワークが属しているリージョンやサブスクリプションは異なっていてもかまいません。

![イメージ](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
図 1 - IPsec を使用した VNet 間

VNet が同じリージョンにある場合は、VPN ゲートウェイを使用しない VNet ピアリングを使用してそれらを接続し、スループットを向上させ、待機時間を短縮することを検討できます。VNet ピアリング接続を構成するには、 **[Configure and validate VNet Peering]\(VNet ピアリングの構成と検証\)** を選択します。

どちらの VNet も Azure Resource Manager デプロイ モデルを使用して作成されている場合は、 **[Configure and validate a Resource Manager VNet to a Resource Manager VNet connection]\(Resource Manager VNet 間の接続を構成して検証する\)** を選択して、VPN 接続を構成します。

どちらかの VNet が Azure クラシック デプロイ モデルを使用して作成され、もう一方が Resource Manager で作成されている場合は、 **[Configure and validate a classic VNet to a Resource Manager VNet connection]\(クラシック VNet と Resource Manager VNet 間の接続を構成して検証する\)** を選択して VPN 接続を構成します。

### <a name="configuration-1-configure-vnet-peering-for-two-vnets-in-the-same-region"></a>構成 1:同じリージョン内で 2 つの VNet に VNet ピアリングを構成する

Azure VNet ピアリングの実装を開始する前に、VNet ピアリングを構成するための次の前提条件を満たしていることを確認します。

* ピアリングする仮想ネットワークは同じ Azure リージョンに存在する必要があります。
* ピアリングする仮想ネットワークの IP アドレス空間は、重複していてはなりません。
* 仮想ネットワーク ピアリングは、2 つの仮想ネットワーク間の関係です。 ピアリング間で派生する推移的な関係は存在しません。 たとえば、VNetA が VNetB とピアリングされ、VNetB が VNetC とピアリングされているからといって、VNetA が VNetC とピアリングされていることには "*なりません*"。

要件を満たしている場合は、[仮想ネットワーク ピアリングを作成するチュートリアル](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering)に従って、VNet ピアリングを作成して構成することができます。

VNet ピアリングの構成を確認するには、次の方法を使用します。

1. 必要な[ロールとアクセス許可](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions)があるアカウントで、[Azure portal](https://portal.azure.com/) にサインインします。
2. Azure Portal の上部にある "*リソースの検索*" というテキストが表示されたボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それをクリックします。
3. 表示される **[仮想ネットワーク]** ブレードで、ピアリングを作成する仮想ネットワークをクリックします。
4. 選択した仮想ネットワークに対して表示されたウィンドウで、 **[設定]** セクションの **[ピアリング]** をクリックします。
5. 構成を確認するピアリングをクリックします。

![イメージ](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Azure Powershell を使用して、コマンド [Get-AzureRmVirtualNetworkPeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) を実行して仮想ネットワーク ピアリングを取得します。次に例を示します。

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connection-type-1-connect-a-resource-manager-vnet-to-anther-resource-manager-vnet-azure-resource-manager-to-azure-resource-manager"></a>接続の種類 1:Resource Manager VNet を別の Resource Manager VNet に接続する (Azure Resource Manager 間)

ある Resource Manager VNet から別の Resource Manager VNet への直接接続を構成することも、IPsec を使用して接続を構成することもできます。

### <a name="configuration-2-configure-vpn-connection-between-resource-manager-vnets"></a>構成 2:Resource Manager Vnet 間の VPN 接続の構成

IPsec なしで Resource Manager VNet 間の接続を構成するには、「[Azure portal を使用して VNet 間 VPN ゲートウェイ接続を構成する](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)」を参照してください。

IPsec を使用して 2 つの Resource Manager VNet 間の接続を構成するには、各 VNet で 「[Azure portal でサイト間接続を作成する](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)」の手順 1 - 5 に従います。

> [!Note]
> これらの手順は、同じサブスクリプションに存在する VNet でのみ使用できます。 接続する VNet がそれぞれ異なるサブスクリプションに存在する場合は、PowerShell を使用する必要があります。 [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps) に関する記事を参照してください。

### <a name="validate-vpn-connection-between-resource-manager-vnets"></a>Resource Manager Vnet 間の VPN 接続の検証

![イメージ](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

図 4 - Azure Resource Manager VNet へのクラシック VNet 接続

VPN 接続が正しく構成されていることを確認するには、次の手順に従います。

> [!Note]
> 次の手順で、仮想ネットワーク コンポーネントの後の数字 ("Vnet 1" など) は、図 4 の番号に対応しています。

1. 接続された VNet でアドレス空間が重複していないかどうかを確認します。

   > [!Note]
   > Vnet 1 と Vnet 6 でアドレス空間が重複することはできません。 

2. **接続オブジェクト** 4 で Azure Resource Manager Vnet 1 のアドレス範囲が正確に定義されていることを確認します。
3. **接続オブジェクト** 3 で Azure Resource Manager Vnet 6 のアドレス範囲が正確に定義されていることを確認します。
4. 接続オブジェクト 3 と 4 の両方で、事前共有キーが一致していることを確認します。
5. **接続オブジェクト** 4 で Azure Resource Manager Vnet Gateway 2 VIP が正確に定義されていることを確認します。
6. **接続オブジェクト** 3 で Azure Resource Manager Vnet Gateway 5 VIP が正確に定義されていることを確認します。

### <a name="connection-type-2-connect-a-classic-vnet-to-a-resource-manager-vnet"></a>接続の種類 2:クラシック VNet と Resource Manager VNet を接続する

別のサブスクリプション、別のリージョンに存在する VNet 間で接続を作成することができます。 ゲートウェイの種類をルート ベースとして構成している場合に限り、オンプレミスのネットワークに既に接続されている VNet を接続することもできます。

クラシック VNet と Resource Manager VNet 間の接続を構成する詳細については、「[Azure portal を使って異なるデプロイ モデルの仮想ネットワークを接続する](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal)」を参照してください。

![イメージ](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

図 5 - Azure Resource Manager VNet へのクラシック VNet 接続

クラシック VNet を Azure Resource Manager VNet に接続するときの構成を確認するには、次の手順に従います。

> [!Note]
> 次の手順で、仮想ネットワーク コンポーネントの後の数字 ("Vnet 1" など) は、図 5 の番号に対応しています。

1. 接続された VNet でアドレス空間が重複していないかどうかを確認します。

   > [!Note]
   > Vnet 1 と Vnet 6 でアドレス空間が重複することはできません。

2. Azure Resource Manager VNet 6 のアドレス範囲がクラシック ローカル ネットワーク定義 3 で正確に定義されていることを確認します。
3. クラシック VNet 1 のアドレス範囲が Azure Resource Manager **接続オブジェクト** 4 で正確に定義されていることを確認します。
4. クラシック VNet Gateway 2 VIP が Azure Resource Manager **接続オブジェクト** 4 で正確に定義されていることを確認します。
5. Azure Resource Manager VNet Gateway 5 VIP がクラシック **ローカル ネットワーク定義** 3 で正確に定義されていることを確認します。
6. 接続されている VNet の両方で、事前共有キーが一致していることを確認します。
   1. クラシック Vnet - ローカル ネットワーク定義 3
   2. Azure Resource Manager Vnet - 接続オブジェクト 4

## <a name="scenario-2-point-to-site-vpn-connection"></a>シナリオ 2:ポイント対サイト VPN 接続

ポイント対サイト (P2S) 構成では、個々のクライアント コンピューターから仮想ネットワークへのセキュリティで保護された接続を作成することができます。 ポイント対サイト接続は、自宅や会議室など、リモートの場所から VNet に接続する場合や、仮想ネットワークに接続する必要があるクライアントの数が少ない場合に便利です。 P2S VPN 接続は、クライアント コンピューターからネイティブ Windows VPN クライアントを使用して開始されます。 クライアントの接続には、認証のために証明書が使用されます。

![イメージ](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

図 2 - ポイント対サイト接続

ポイント対サイト接続では、VPN デバイスは必要ありません。 P2S により、SSTP (Secure Socket トンネリング プロトコル) 経由での VPN 接続が作成されます。 さまざまなデプロイ ツールとデプロイ モデルを使用して、ポイント対サイト接続を VNet に接続できます。

* [Azure Portal を使用した VNet へのポイント対サイト接続の構成](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Azure portal を使用した VNet へのポイント対サイト接続の構成 (クラシック)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [PowerShell を使用した VNet へのポイント対サイト接続の構成](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connections"></a>ポイント対サイト接続の検証

記事「[トラブルシューティング:Azure ポイント対サイト接続の問題](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems)」では、ポイント対サイト接続に関する一般的な問題について説明しています。

## <a name="scenario-3-multi-site-vpn-connection"></a>シナリオ 3:マルチサイト VPN 接続

サイト間 (S2S) 接続、ポイント対サイト接続、または VNet 間接続が既にある VNet に S2S 接続を追加できます。この種類の接続は、**マルチサイト**構成と呼ばれることがよくあります。 

![イメージ](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

図 3 - マルチサイト接続

Azure は現在、2 つのデプロイメント モデルで使用できます。Resource Manager とクラシックです。 これらの 2 つのモデルには、完全に互換性があるわけではありません。 異なるモデルを使用して**マルチサイト**接続を構成するには、次の記事を確認してください。

* [既存の VPN ゲートウェイ接続を使用してサイト間接続を VNet に追加する](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [既存の VPN ゲートウェイ接続を使用してサイト間接続を VNet に追加する (クラシック)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> 次の手順は、ExpressRoute とサイト間の共存接続の構成には適用されません。 共存接続の詳細については、[ExpressRoute およびサイト間の共存接続](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)に関する記事を参照してください。

## <a name="scenario-4-configure-transit-routing"></a>シナリオ 4:トランジット ルーティングの構成

トランジット ルーティングは、"デイジー チェーン" トポロジで複数のネットワークを接続する特定のルーティング シナリオです。 このルーティングにより、"チェーン" のいずれかの端にある VNet のリソースが、中間の VNet を介して相互に通信できるようになります。 トランジット ルーティングを使用しない場合、ハブを介してピアリングしたネットワークまたはデバイスは互いに接続できません。

### <a name="configuration-1-configure-transit-routing-in-a-point-to-site-connection"></a>構成 1:ポイント対サイト接続でのトランジット ルーティングの構成

このシナリオでは、VNetA と VNetB の間にサイト間 VPN 接続を構成します。また、クライアントが VNetA のゲートウェイに接続するようにポイント対サイト VPN を構成します。 次に、ポイント対サイト クライアントが VNetB に接続し、VNetA を通過するトランジット ルーティングを有効にします。 このシナリオは、VNetA と VNetB の間で、BGP がサイト間 VPN で有効になっている場合にサポートされます。 詳細については、「[ポイント対サイト VPN ルーティングについて](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)」を参照してください。

### <a name="configuration-2-configure-transit-routing-in-an-expressroute-connection"></a>構成 2:ExpressRoute 接続でのトランジット ルーティングの構成

Microsoft Azure ExpressRoute を利用すれば、接続プロバイダーが提供する専用プライベート接続で、オンプレミスのネットワークを Microsoft クラウドに拡張できます。 ExpressRoute では、Microsoft Azure、Office 365、Dynamics 365 などの Microsoft クラウド サービスへの接続を確立できます。  詳細については、「[ExpressRoute の概要](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)」を参照してください。

![イメージ](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

図 6 - Azure VNet への ExpressRoute の "プライベート ピアリング" 接続

> [!Note]
> VNetA と VNetB が同じ地理的リージョンにある場合は、トランジット ルーティングを構成するのではなく、[両方の VNet を ExpressRoute 回線にリンク](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm)することをお勧めします。 VNet が異なる地理的リージョンにある場合で、[ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium) をお持ちの場合は、それらを直接回線にリンクすることもできます。 

ExpressRoute とサイト間が共存している場合、トランジット ルーティングはサポートされません。 詳細については、[ExpressRoute およびサイト間の共存接続の構成](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)に関する記事を参照してください。

ローカル ネットワークを Azure 仮想ネットワークに接続するために ExpressRoute を有効にした場合は、トランジット ルーティングを行う VNet 間で VNet ピアリングを有効にすることができます。 ローカル ネットワークがリモート VNet に接続できるようにするには、[仮想ネットワーク ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity)を構成する必要があります。 

> [!Note]
> VNet ピアリングは、同じリージョンの VNet に対してのみ使用できます。

VNet ピアリングにトランジット ルーティングを構成したかどうかを確認するには、次の手順に従います。

1. 必要な[ロールとアクセス許可](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions)があるアカウントで、[Azure portal](https://portal.azure.com/) にサインインします。
2. 前の図 (図 6) に示されているように、[VNetA と VNetB の間にピアリングを作成](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering)します。 
3. 選択した仮想ネットワークに対して表示されたウィンドウで、 **[設定]** セクションの **[ピアリング]** をクリックします。
4. 表示するピアリングをクリックし、 **[構成]** をクリックして ExpressRoute 回線に接続されている VNetA で **[ゲートウェイ転送を許可する]** が有効になっており、ExpressRoute 回線に接続されていないリモート VNetB で **[リモート ゲートウェイを使用する]** が有効になっていることを確認します。

### <a name="configuration-3-configure-transit-routing-in-a-vnet-peering-connection"></a>構成 3:VNet ピアリング接続でのトランジット ルーティングの構成

仮想ネットワークがピアリングされているとき、そのピアリングされた仮想ネットワークのゲートウェイを、オンプレミスのネットワークへのトランジット ポイントとして構成することもできます。 VNet ピアリングでトランジット ルートを構成するには、[仮想ネットワーク間接続](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json)に関する記事を確認してください。

> [!Note]
> ゲートウェイ トランジットは、異なるデプロイメント モデルを使って作成された仮想ネットワーク間のピアリング関係ではサポートされません。 ゲートウェイ トランジットが機能するためには、ピアリング関係にある両方の仮想ネットワークが Resource Manager を介して作成されている必要があります。

VNet ピアリングにトランジット ルーティングを構成したかどうかを確認するには、次の手順に従います。

1. 必要な[ロールとアクセス許可](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions)があるアカウントで、[Azure portal](https://portal.azure.com/) にサインインします。
2. Azure portal の上部にある "リソースの検索" というテキストが表示されたボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それをクリックします。
3. 表示される **[仮想ネットワーク]** ブレードで、ピアリング設定を確認する仮想ネットワークをクリックします。
4. 選択した仮想ネットワークに対して表示されたウィンドウで、 **[設定]** セクションの **[ピアリング]** をクリックします。
5. 表示するピアリングをクリックし、 **[構成]** の下で **[ゲートウェイ転送を許可する]** と **[リモート ゲートウェイを使用する]** が有効になっていることを確認します。

![イメージ](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configuration-4-configure-transit-routing-in-a-vnet-to-vnet-connection"></a>構成 4:VNet 間接続でのトランジット ルーティングの構成

VNet 間のトランジット ルーティングを構成するには、Resource Manager デプロイ モデルと PowerShell を使用して、すべての中間 VNet 間接続で BGP を有効にする必要があります。 手順については、「[PowerShell を使用して Azure VPN Gateway で BGP を構成する方法](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)」を参照してください。

クラシック デプロイ モデルを使用して Azure VPN ゲートウェイ経由でトラフィックを通過させることは可能です。この場合、ネットワーク構成ファイル内の静的に定義されたアドレス空間が使用されます。 BGP はまだ、クラシック デプロイ モデルを使用した Azure Virtual Networks と VPN Gateway ではサポートされていません。 BGP が使用できない場合、手動で通過アドレス空間を定義するとエラーが発生しやすいため、これは推奨していません。

> [!Note]
> クラシック VNet 間接続は、Azure portal (クラシック) を使用するか、クラシック ポータルのネットワーク構成ファイルを使用して構成します。 Azure Resource Manager デプロイ モデルまたは Azure portal を使用してクラシック仮想ネットワークを作成または変更することはできません。 クラシック VNet のトランジット ルーティングの詳細については、「[VPN (V1) を使用した Azure ARM でのハブ & スポーク、デイジー チェーン、およびフルメッシュ VNet トポロジ](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)」を参照してください。

### <a name="configuration-5-configure-transit-routing-in-a-site-to-site-connection"></a>構成 5:サイト間接続でのトランジット ルーティングの構成

オンプレミス ネットワークとサイト間接続を使用した VNet の間にトランジット ルーティングを構成するには、Resource Manager デプロイ モデルと PowerShell を使用して、すべての中間サイト間接続で BGP を有効にする必要があります。手順については、「[PowerShell を使用して Azure VPN Gateway で BGP を構成する方法](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)」を参照してください。

クラシック デプロイ モデルを使用して Azure VPN ゲートウェイ経由でトラフィックを通過させることは可能です。この場合、ネットワーク構成ファイル内の静的に定義されたアドレス空間が使用されます。 BGP はまだ、クラシック デプロイ モデルを使用した Azure Virtual Networks と VPN Gateway ではサポートされていません。 BGP が使用できない場合、手動で通過アドレス空間を定義するとエラーが発生しやすいため、これは推奨していません。

> [!Note]
> クラシック サイト間接続は、Azure portal (クラシック) を使用するか、クラシック ポータルのネットワーク構成ファイルを使用して構成します。 Azure Resource Manager デプロイ モデルまたは Azure portal を使用してクラシック仮想ネットワークを作成または変更することはできません。 クラシック VNet のトランジット ルーティングの詳細については、「[VPN (V1) を使用した Azure ARM でのハブ & スポーク、デイジー チェーン、およびフルメッシュ VNet トポロジ](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)」を参照してください。

## <a name="scenario-5-configure-bgp-for-a-vpn-gateway"></a>シナリオ 5:VPN ゲートウェイ用の BGP の構成

BGP は、2 つ以上のネットワーク間でルーティングと到達可能性の情報を交換するためにインターネット上で使用されている標準のルーティング プロトコルです。 Azure Virtual Networks のコンテキストで BGP を使用すると、BGP によって Azure VPN Gateway とオンプレミスの VPN デバイス (BGP ピアまたはネイバーと呼ばれます) が有効になります。 これらは、関連するゲートウェイまたはルーターの可用性と、プレフィックスが到達できる可能性に関する情報を両方のゲートウェイに伝達する "ルート" をやり取りします。 また、BGP では、BGP ゲートウェイが特定の BGP ピアから学習したルートを他のすべての BGP ピアに伝達することで、複数のネットワークでトランジット ルーティングを行うこともできます。 詳細については、「[BGP と Azure VPN Gateway について](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)」を参照してください。

### <a name="configure-bgp-for-a-vpn-connection"></a>VPN 接続用の BGP の構成

BGP を使用する VPN 接続を構成するには、「[PowerShell を使用して Azure VPN Gateway で BGP を構成する方法](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)」を参照してください。

> [!Note]
> 1. AS 番号を作成することによって、Virtual Network Gateway で BGP を有効にします。 基本ゲートウェイでは、BGP はサポートされていません。 ゲートウェイの SKU を確認するには、Azure portal で [VPN Gateway] ブレードの [概要] セクションに移動します。 SKU が **Basic** の場合は、SKU ([ゲートウェイのサイズ変更](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)) を **VpnGw1** SKU に変更する必要があります。 SKU を確認すると、20 - 30 分のダウンタイムが発生します。 ゲートウェイが適切な SKU を持つようになったら、[Set-AzureRmVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) PowerShell コマンドレットを使用して AS を追加できます。 AS 番号を構成すると、ゲートウェイの BGP ピア IP が自動的に提供されます。
> 2. LocalNetworkGateway は、AS 番号と BGP ピア アドレスを使用して**手動で**指定する必要があります。 [New-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) または [Set-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) PowerShell コマンドレットを使用して、**ASN** と **-BgpPeeringAddress** の値を設定できます。 一部の AS 番号は Azure 用に予約されており、「[BGP と Azure VPN Gateway について](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview#bgp-faq)」で説明されているように、それらを使用することはできません。
> 3. 最後に、Connection オブジェクトで BGP が有効になっている必要があります。 **-EnableBGP** 値を `$True` に設定するには、[New-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) または [Set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) を使用します。

### <a name="validate-the-bgp-configuration"></a>BGP 構成の検証

BGP が正しく構成されているかどうかを確認するには、コマンドレット `get-AzureRmVirtualNetworkGateway` と `get-AzureRmLocalNetworkGateway` を実行してから、BgpSettingsText 部分で BGP 関連の出力を確認します。 例: BgpSettingsText:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="scenario-6-highly-available-active-active-vpn-connection"></a>シナリオ 6:高可用性のアクティブ/アクティブ VPN 接続

アクティブ/アクティブ ゲートウェイとアクティブ/スタンバイ ゲートウェイの主な違い:

* 2 つのパブリック IP アドレスを使用して 2 つのゲートウェイ IP 構成を作成する必要がある
* *EnableActiveActiveFeature* フラグを設定する必要がある
* ゲートウェイ SKU は VpnGw1、VpnGw2、VpnGw3 である必要がある

クロスプレミスと VNet 間接続で高可用性を実現するには、複数の VPN ゲートウェイをデプロイし、ネットワークと Azure 間に複数の並列接続を確立する必要があります。 接続オプションとトロポジの概要については、「[高可用性のクロスプレミス接続および VNet 間接続](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)」をご覧ください。

アクティブ/アクティブのクロスプレミス接続と VNet 間接続を作成するには、「[Azure VPN Gateway で、アクティブ/アクティブ S2S VPN 接続を構成する](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell)」の手順に従って、アクティブ/アクティブ モードで Azure VPN Gateway を構成します。

> [!Note]  
> 1. BGP が有効になっているローカル ネットワーク ゲートウェイにアドレスを追加すると、アクティブ/アクティブ モードでは、*BGP ピアの /32 アドレスのみが追加*されます。 さらにアドレスを追加すると、それらは静的ルートと見なされ、BGP ルートよりも優先されます。
> 2. Azure に接続するオンプレミス ネットワークには、異なる BGP ASN を使用する必要があります。 (これらが同じ場合、オンプレミス VPN デバイスが既に ASN を使用して他の BGP 近隣ノードとピアリングしているのであれば、VNet ASN を変更する必要があります。)

## <a name="scenario-7-change-an-azure-vpn-gateway-type-after-deployment"></a>シナリオ 7:デプロイ後に Azure VPN ゲートウェイの種類を変更する

Azure VNet ゲートウェイの種類をポリシーベースからルートベース (またはその逆) に変更することはできません。 ゲートウェイを削除する必要があります。その後は、IP アドレスと事前共有キー (PSK) は保持されません。 その後、目的の種類の新しいゲートウェイを作成できます。 ゲートウェイを削除して作成するには、次の手順に従います。

1. 元のゲートウェイに関連付けられているすべての接続を削除します。
2. Azure portal、PowerShell、またはクラシック PowerShell を使用してゲートウェイを削除します。 
   * [Azure portal を使用して仮想ネットワーク ゲートウェイを削除する](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [PowerShell を使用して仮想ネットワーク ゲートウェイを削除する](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [PowerShell を使用して仮想ネットワーク ゲートウェイを削除する (クラシック)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. 「[VPN ゲートウェイを作成する](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#a-namevnetgatewaya4-create-the-vpn-gateway)」の手順に従って、目的の種類の新しいゲートウェイを作成し、VPN の設定を完了します。

> [!Note]
> このプロセスには約 60 分かかります。

## <a name="next-steps"></a>次の手順

* [Azure VM 間の接続に関する問題のトラブルシューティング](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

