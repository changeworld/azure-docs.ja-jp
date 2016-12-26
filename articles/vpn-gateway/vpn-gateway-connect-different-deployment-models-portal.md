---
title: "ポータルでクラシック仮想ネットワークを Resource Manager の仮想ネットワークに接続する方法 | Microsoft Docs"
description: "VPN Gateway とポータルを使用して、クラシック VNet と Resource Manager の VNet の間に VPN 接続を作成する方法について説明します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 5a90498c-4520-4bd3-a833-ad85924ecaf9
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/03/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 7834aefeb9eb007ffa9daf708250c9f06ec05e67
ms.openlocfilehash: 96c709d8df311e649a2f8fe9dc177d43615c65ad


---
# <a name="connect-virtual-networks-from-different-deployment-models-in-the-portal"></a>異なるデプロイ モデルの仮想ネットワークをポータルで接続する
> [!div class="op_single_selector"]
> * [ポータル](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Azure には現在、クラシックと Resource Manager (RM) の 2 つの管理モデルがあります。 これまで Azure を使用してきているユーザーであれば、おそらく Azure VM およびクラシック VNet で実行されているインスタンス ロールを利用されていることでしょう。 新しい VM とロール インスタンスが、Resource Manager で作成された VNet 上で実行されていることも考えられます。 この記事では、クラシック VNet を Resource Manager VNet に接続し、異なるデプロイ モデルにあるリソースをゲートウェイ接続経由で相互に通信できるようにする方法について説明します。 

別のサブスクリプション、別のリージョンに存在する VNet 間で接続を作成することができます。 オンプレミスのネットワークに既に接続されている VNet を接続することもできます。ただし、Vnet が構成されているゲートウェイが動的またはルート ベースである場合に限ります。 VNet 間接続の詳細については、この記事の最後にある「[VNet 間接続に関してよく寄せられる質問](#faq)」を参照してください。

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>VNet 間接続で使用できるデプロイメント モデルとデプロイ方法
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

以下の表は、この構成について新しい記事、追加のツールが利用できるようになったら更新されるものです。 記事が利用できるようになったら、表から直接リンクできるようにします。<br><br>

**VNet 間**

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**VNet ピアリング**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="before-beginning"></a>作業を開始する前に
次の手順では、各 VNet 用に動的またはルート ベースのゲートウェイを構成してゲートウェイ間の VPN 接続を作成する際に必要な設定について説明します。 この構成は、静的またはポリシー ベースのゲートウェイをサポートしていません。 

この記事では、クラシック ポータル、Azure ポータル、PowerShell を使用します。 現在、Azure ポータルのみを使用してこの構成を作成することはできません。

### <a name="prerequisites"></a>前提条件
* 両方の VNet が既に作成されている。
* これらの VNet のアドレス範囲が互いに重複していない。また、ゲートウェイの接続先になる可能性のある他の接続の範囲と重複していない。
* 最新の PowerShell コマンドレット (1.0.2 以降) がインストール済みである。 詳細については、「 [Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md) 」ご覧ください。 必ずサービス管理 (SM) と Resource Manager (RM) のコマンドレットの両方をインストールしてください。 

### <a name="a-namevaluesaexample-settings"></a><a name="values"></a>設定例
設定の例は、リファレンスとして使用できます。

**クラシック VNet の設定**

VNet Name = ClassicVNet  <br>
Location = West US <br>
Virtual Network Address Spaces = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Local Network Name = RMVNetLocal <br>

**Resource Manager の VNet の設定**

VNet Name = RMVNet  <br>
Resource Group = RG1 <br>
Virtual Network IP Address Spaces = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Location = East US <br>
Virtual network gateway name = RMGateway <br>
Gateway public IP name = gwpip <br>
Gateway type = VPN <br>
VPN type = Route-based <br>
Local network gateway = ClassicVNetLocal <br>

## <a name="a-namecreatesmgwasection-1-configure-classic-vnet-settings"></a><a name="createsmgw"></a>セクション 1: クラシック VNet の設定を構成する
このセクションでは、ローカル ネットワークと、クラシック VNet のゲートウェイを作成します。 このセクションの手順ではクラシック ポータルを使用します。 現在、Azure ポータルでは、クラシック VNet 関連の設定の一部を提供していません。

### <a name="part-1---create-a-new-local-network"></a>パート 1 - 新しいローカル ネットワークを作成する
[クラシック ポータル](https://manage.windowsazure.com) を開き、Azure アカウントでサインインします。

1. 画面の左下隅で、**[新規]**、 > **[ネットワーク サービス]** > 、**[仮想ネットワーク]** > 、**[ローカル ネットワークの追加]** の順にクリックします。
2. **[ローカル ネットワークの詳細を指定する]** ウィンドウで、接続先の RM VNet の名前を入力します。 **[VPN デバイス IP アドレス (オプション)]** ボックスで、任意の有効なパブリック IP アドレスを入力します。 これは、あくまで一時的なプレースホルダーです。 この IP アドレスは後で変更します。 ウィンドウの右下隅にある矢印ボタンをクリックします。
3. **[アドレス空間を指定します]** ページにある **[開始 IP]** ボックスに、接続先の Resource Manager の VNet 用のネットワーク プレフィックスと CIDR ブロックを入力します。 この設定を使用して、RM VNet にルーティングするアドレス空間を指定します。

### <a name="part-2---associate-the-local-network-to-your-vnet"></a>パート 2 - ローカル ネットワークを VNet に関連付ける
1. ページの上部にある **[仮想ネットワーク]** をクリックして [仮想ネットワーク] 画面に切り替え、クラシック VNet を選択します。 VNet のページで、 **[構成]** をクリックして構成ページに移動します。
2. **[サイト間接続]** 接続セクションで、**[ローカル ネットワークに接続する]** チェックボックスをオンにします。 作成したローカル ネットワークを選択します。 複数のローカル ネットワークを作成した場合は、必ず、Resource Manager の VNet に対応するように作成したローカル ネットワークをドロップダウン リストから選択してください。
3. ページの下部にある **[保存]** をクリックします。

### <a name="part-3---create-the-gateway"></a>パート 3 - ゲートウェイを作成する
1. 設定を保存した後で、ページの上部にある **[ダッシュボード]** をクリックして、[ダッシュボード] ページに移動します。 [ダッシュボード] ページの下部にある **[ゲートウェイの作成]** をクリックし、**[動的ルーティング]** をクリックします。 **[はい]** をクリックして、ゲートウェイの作成を開始します。 この構成には、動的ルーティング ゲートウェイが必要です。
2. ゲートウェイが作成されるのを待ちます。 完了するまでに 45 分以上かかることがあります。

### <a name="a-nameipapart-4---view-the-gateway-public-ip-address"></a><a name="ip"></a>パート 4 - ゲートウェイのパブリック IP アドレスを表示する
ゲートウェイが作成されたら、 **[ダッシュボード]** ページでゲートウェイ IP アドレスを確認することができます。 これが、ゲートウェイのパブリック IP アドレスです。 パブリック IP アドレスは、書き留めるかコピーします。 後の手順で Resource Manager の VNet の構成用のローカル ネットワークを作成するときに、これを使用します。

## <a name="a-namecreatermgwasection-2-configure-resource-manager-vnet-settings"></a><a name="creatermgw"></a>セクション 2: Resource Manager の VNet の設定を構成する
このセクションでは、仮想ネットワーク ゲートウェイと、Resource Manager の VNet 用のローカル ネットワークを作成します。 クラシック VNet のゲートウェイのパブリック IP アドレスを取得するまで、次の手順を開始しないでください。

スクリーンショットは例として示されています。 サンプルの値は必ず実際の値に変更してください。 練習としてこの構成を作成する場合は、上記の [値](#values)を参照してください。

### <a name="part-1---create-a-gateway-subnet"></a>パート 1 - ゲートウェイ サブネットを作成する
仮想ネットワークをゲートウェイに接続する前に、まず、接続先の仮想ネットワークのゲートウェイ サブネットを作成する必要があります。 CIDR カウント /28 以上 (/27、/26 など) で、ゲートウェイ サブネットを作成します。

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

ブラウザーから [Azure ポータル](http://portal.azure.com) に移動し、Azure アカウントでサインインします。

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>パート 2 - 仮想ネットワーク ゲートウェイを作成する
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

### <a name="part-3---create-a-local-network-gateway"></a>パート 3 - ローカル ネットワーク ゲートウェイを作成する
"ローカル ネットワーク ゲートウェイ" は通常、オンプレミスの場所を意味します。 これによって、その場所にルーティングする IP アドレス範囲と、その場所用のデバイスのパブリック IP アドレスが Azure に通知されます。 ただし、ここでは、クラシック VNet および仮想ネットワーク ゲートウェイに関連付けられているアドレス範囲とパブリック IP アドレスを意味します。

Azure から参照できるように、ローカル ネットワーク ゲートウェイに名前を付けます。 仮想ネットワーク ゲートウェイの作成中に、ローカル ネットワーク ゲートウェイを作成できます。 この構成では、 [前のセクション](#ip)でクラシック VNet ゲートウェイに割り当てたパブリック IP アドレスを使用します。

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

### <a name="part-4---copy-the-public-ip-address"></a>パート 4 - パブリック IP アドレスをコピーする
仮想ネットワーク ゲートウェイの作成が完了したら、ゲートウェイに関連付けられているパブリック IP アドレスをコピーします。 このアドレスは、クラシック VNet のローカル ネットワーク設定を構成するときに使用します。 

## <a name="section-3-modify-the-local-network-for-the-classic-vnet"></a>セクション 3: クラシック VNet 用のローカル ネットワークを変更する
[クラシック ポータル](https://manage.windowsazure.com)を開きます。

1. クラシック ポータルの画面左側のバーを下にスクロールし、 **[ネットワーク]**をクリックします。 **[ネットワーク]** ページの上部にある **[ローカル ネットワーク]** をクリックします。 
2. パート 1 で構成したローカル ネットワークをクリックして選択します。 ページの下部にある **[編集]**をクリックします。
3. **[ローカル ネットワークの詳細を指定する]** ページで、プレース ホルダー IP アドレスを、前のセクションで作成した Resource Manager のゲートウェイのパブリック IP アドレスに置き換えます。 矢印をクリックして次のセクションに進みます。 **[アドレス空間]** が正しいことを確認し、チェックマークをクリックして変更を確定します。

## <a name="a-nameconnectasection-4-create-the-connection"></a><a name="connect"></a>セクション 4: 接続を作成する
このセクションでは、VNet 間の接続を作成します。 この手順には PowerShell が必要です。 どのポータルでもこの接続は作成できません。 クラシック (SM) と Resource Manager (RM) の両方の PowerShell コマンドレットをダウンロードしてインストールしていることを確認してください。

1. PowerShell コンソールで Azure アカウントにログインします。 次のコマンドレットは、Azure アカウントのログイン資格情報をユーザーに求めます。 ログイン後にアカウント設定がダウンロードされ、Azure PowerShell で使用できるようになります。
   
        Login-AzureRmAccount 
   
     複数のサブスクリプションがある場合は、Azure サブスクリプションの一覧が表示されます。
   
        Get-AzureRmSubscription
   
    使用するサブスクリプションを指定します。 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"
2. 従来の PowerShell コマンドレットを使用して、Azure アカウントを追加します。 これを行うには、次のコマンドを使用できます。
   
        Add-AzureAccount
3. 次の例を実行して、共有キーを設定します。 このサンプルでは、`-VNetName` がクラシック VNet の名前で、`-LocalNetworkSiteName` はクラシック ポータルでローカル ネットワークを構成したときに指定した名前です。 `-SharedKey` は、生成して指定できる値です。 ここで指定する値は、次の手順で接続を作成するときに指定するものと同じ値である必要があります。
   
        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
4. 次のコマンドを実行して、VPN 接続を作成します。
   
    **変数を設定する**
   
        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   
    **接続を作成する**<br> `-ConnectionType` は "IPsec です"。"Vnet2Vnet" ではありません。 このサンプルでは、`-Name` は、接続に付ける名前です。 次のサンプルでは、"*rm-to-classic-connection*" という名前の接続が作成されます。
   
        New-AzureRmVirtualNetworkGatewayConnection -Name rm-to-classic-connection -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="verify-your-connection"></a>接続を確認する
クラシック ポータル、Azure ポータル、または PowerShell を使用して接続を確認できます。 次の手順を使用して、接続を確認できます。 サンプルの値は実際の値に変更してください。

[!INCLUDE [vpn-gateway-verify connection](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="a-namefaqavnet-to-vnet-faq"></a><a name="faq"></a>VNet 間接続に関してよく寄せられる質問
VNet 間接続に関するその他の情報についてよく寄せられる質問の詳細を示します。

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]




<!--HONumber=Nov16_HO4-->


