---
title: SAP HANA on Azure (L インスタンス) に仮想ネットワークを接続する | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) への仮想ネットワークの接続方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/1/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017, devx-track-azurepowershell
ms.openlocfilehash: e451f969c1518a920f7828d92fdfed65cd80f69c
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2021
ms.locfileid: "111412387"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>HANA L インスタンスに仮想ネットワークを接続する

[Azure 仮想ネットワークを作成](hana-connect-azure-vm-large-instances.md)しました。 これで、そのネットワークを SAP HANA on Azure Large Instances (BareMetal インフラストラクチャ インスタンスとも呼ばれます) に接続できます。 この記事では、実行する必要がある手順について説明します。

## <a name="create-an-azure-expressroute-gateway-on-the-virtual-network"></a>仮想ネットワーク上に Azure ExpressRoute ゲートウェイを作成する

まず、仮想ネットワーク上に Azure ExpressRoute ゲートウェイを作成します。 このゲートウェイでは、HANA L インスタンス スタンプでテナントに接続する ExpressRoute 回線に仮想ネットワークをリンクさせることができます。

> [!NOTE] 
> この手順が完了するまで最大で 30 分かかることがあります。 指定した Azure サブスクリプション内で新しいゲートウェイを作成してから、指定した Azure Virtual Network に接続します。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

- ゲートウェイが既に存在する場合は、それが ExpressRoute ゲートウェイかどうかを確認します。 それが ExpressRoute ゲートウェイでない場合は、ゲートウェイを削除し、ExpressRoute ゲートウェイとして作成し直します。 ExpressRoute ゲートウェイが既に確立されている場合は、この記事の後述の「[仮想ネットワークをリンクする](#link-virtual-networks)」というセクションに進んでください。 

- [Azure Portal](https://portal.azure.com/) または PowerShell を使用して、お使いの仮想ネットワークに接続された ExpressRoute VPN ゲートウェイを作成します。
    - Azure Portal を使用する場合、新しい **Virtual Network ゲートウェイ** を追加し、ゲートウェイの種類として **[ExpressRoute]** を選択します。
    - PowerShell を使用する場合は、まず、最新の [Azure PowerShell SDK](https://azure.microsoft.com/downloads/) をダウンロードして、それを使用します。 
 
    次のコマンドは ExpressRoute ゲートウェイを作成します。 _$_ に続くテキストは、ユーザー固有の情報で更新する必要があるユーザー定義変数です。

    ```powershell
    # These Values should already exist, update to match your environment
    $myAzureRegion = "eastus"
    $myGroupName = "SAP-East-Coast"
    $myVNetName = "VNet01"
    
    # These values are used to create the gateway, update for how you wish the GW components to be named
    $myGWName = "VNet01GW"
    $myGWConfig = "VNet01GWConfig"
    $myGWPIPName = "VNet01GWPIP"
    $myGWSku = "UltraPerformance" # Supported values for HANA large instances are: UltraPerformance
    
    # These Commands create the Public IP and ExpressRoute Gateway
    $vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
    $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
    -Location $myAzureRegion -AllocationMethod Dynamic
    $gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
    $gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
    -PublicIpAddressId $gwpip.Id
    
    New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
    -IpConfigurations $gwipconfig -GatewayType ExpressRoute `
    -GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
    ```

SAP HANA on Azure (L インスタンス) でサポートされている唯一のゲートウェイ SKU は、**UltraPerformance** です。

## <a name="link-virtual-networks"></a>仮想ネットワークをリンクする

これで、Azure Virtual Network に ExpressRoute ゲートウェイが作成されました。 Microsoft から提供された承認情報を使用して、ExpressRoute ゲートウェイを SAP HANA L インスタンスの ExpressRoute 回線に接続します。 Azure Portal または PowerShell を使用して接続できます。 PowerShell での手順は次のとおりです。 

接続ごとに異なる AuthGUID を使用して、ExpressRoute ゲートウェイごとに次のコマンドを実行します。 次のスクリプトに示す最初の 2 つのエントリは、Microsoft から提供される情報です。 また、AuthGUID はすべての仮想ネットワークとそのゲートウェイに固有のものです。 別の Azure 仮想ネットワークを追加する場合は、HANA L インスタンスを Microsoft の Azure に接続する ExpressRoute 回線のための別の AuthID を取得する必要があります。 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> コマンド New-AzVirtualNetworkGatewayConnection の最後のパラメーター **ExpressRouteGatewayBypass** は、ExpressRoute FastPath を有効にする新しいパラメーターです。 これは、2019 年 5 月に追加された機能であり、HANA L インスタンス ユニットと Azure VM の間のネットワーク待ち時間を短縮します。 詳細については、「[SAP HANA (L インスタンス) のネットワーク アーキテクチャ](./hana-network-architecture.md)」を参照してください。 このコマンドを実行する前に、最新バージョンの PowerShell コマンドレットを実行していることを確認してください。

場合によっては、サブスクリプションに関連付けられている複数の ExpressRoute 回線にゲートウェイを接続する必要があります。 その場合は、この手順を複数回実行する必要があります。 たとえば、通常は同じ仮想ネットワーク ゲートウェイを、仮想ネットワークとオンプレミス ネットワークとを接続する ExpressRoute 回線に接続します。

## <a name="applying-expressroute-fastpath-to-existing-hana-large-instance-expressroute-circuits"></a>既存の HANA L インスタンスの ExpressRoute 回線への ExpressRoute FastPath の適用
HANA L インスタンス デプロイで作成された新しい ExpressRoute 回線を、いずれかの Azure 仮想ネットワークの Azure ExpressRoute ゲートウェイに接続する方法について説明してきました。 ただし、既に ExpressRoute 回線を設定していて、仮想ネットワークを HANA L インスタンスに既に接続している場合はどうでしょうか。 

新しい ExpressRoute FastPath を使用すると、ネットワーク待機時間が短縮されます。 この待機時間の短縮を利用するには、変更を適用することをお勧めします。 新しい ExpressRoute 回線を接続するためのコマンドは、既存の ExpressRoute 回線を変更するためのコマンドと同じです。 そのため、既存の回線を変更するには、この PowerShell コマンドのシーケンスを実行する必要があります。 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

ExpressRoute FastPath 機能を有効にするために、上に表示されている最後のパラメーターを追加することが重要です。


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach

次のいずれかのシナリオで、Global Reach を有効にします。

 - 追加されたプロキシまたはファイアウォールを使用しない HANA システム レプリケーション。
 - システム コピーまたはシステム更新を行うための、2 つの異なるリージョン内の HANA L インスタンス ユニット間でのバックアップのコピー。

Global Reach を有効にするには、次の操作を行います。

- /29 のアドレス空間のアドレス空間範囲を指定します。 そのアドレス範囲は、HANA L インスタンスを Azure に接続するためにそれまで使用されていた他のアドレス空間範囲のいずれとも重複することはできません。 また、アドレス範囲は、Azure またはオンプレミスの他の場所で使用したどの IP アドレス範囲とも重複しないようにする必要があります。
- オンプレミスのルートを HANA L インスタンスにアドバタイズするために使用できる自律システム番号 (ASN) には制限があります。 オンプレミスでは、65000 – 65020 または 65515 の範囲のプライベート ASN を持つルートをアドバタイズできません。 
- オンプレミスの直接アクセスを HANA L インスタンスに接続する場合は、Azure に接続する回線の料金を計算する必要があります。 詳細については、[Global Reach アドオン](https://azure.microsoft.com/pricing/details/expressroute/)の価格を確認してください。

シナリオのどちらかまたは両方をデプロイに適用するには、「[HANA large Instances のサポート リクエストを開く](./hana-li-portal.md#open-a-support-request-for-hana-large-instances)」で説明されているように Azure でサポート メッセージを開きます

Microsoft が要求をルーティングして実行するために使用する必要があるデータとキーワードは次のとおりです。

- サービス: SAP HANA L インスタンス
- 問題の種類: 構成とセットアップ
- 問題のサブタイプ: 問題が上記の一覧にない。
- 件名 "ネットワークを変更する - Global Reach を追加する"
- 詳細: "HANA L インスタンスから HANA L インスタンス テナントに対して Global Reach を追加する"。 または "オンプレミスから HANA L インスタンス テナントに対して Global Reach を追加する"。
- HANA L インスタンスから HANA L インスタンス テナントのケースの追加詳細: 接続する 2 つのテナントが配置されている **2 つの Azure リージョン** を定義する必要があり、**さらに**、 **/29 IP アドレス範囲** を送信する必要があります。
- オンプレミスから HANA L インスタンス テナントのケースの追加の詳細: 
    - 直接接続する HANA L インスタンス テナントがデプロイされる **Azure リージョン** を定義します。 
    - オンプレミスと Azure の間に ExpressRoute 回線を確立したときに受信した **Auth GUID** と **回線ピア ID** を指定します。 
    - **ASN** に名前を付けます。 
    - ExpressRoute Global Reach の **/29 IP アドレス範囲** を指定します。

> [!NOTE]
> 両方のケースが処理されるようにしたい場合は、これまでに使用された他のどの IP アドレス範囲とも重複しない 2 つの異なる /29 の IP アドレス範囲を指定する必要があります。 

## <a name="next-steps"></a>次のステップ

Azure に SAP HANA L インスタンスをデプロイするために必要になる可能性のある他のネットワーク要件について確認します。

> [!div class="nextstepaction"]
> [L インスタンス向け追加のネットワーク要件](hana-additional-network-requirements.md)
