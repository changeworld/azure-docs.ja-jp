<properties 
   pageTitle="クラシック デプロイメント モデルで PowerShell を使用してルーティングを制御し、仮想アプライアンスを使用する | Microsoft Azure"
   description="クラシック デプロイメント モデルで PowerShell を使用して VNet でのルーティングを制御する方法を使用する"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#PowerShell を使用してルーティングを制御し仮想アプライアンス (クラシック) を使用する

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

この記事では、クラシック デプロイメント モデルについて説明します。

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

以下の Azure PowerShell のサンプル コマンドでは、上記シナリオに基づいて単純な環境が既に作成されていると想定します。このドキュメントに示されているようにコマンドを実行するのであれば、「[PowerShell を使用して VNet (クラシック) を作成する](virtual-networks-create-vnet-classic-netcfg-ps.md)」に示されている環境を構築します。

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## フロントエンドのサブネットの UDR を作成する
上記のシナリオに基づいて、フロントエンドのサブネットに必要なルート テーブルとルートを作成するには、次の手順に従います。

3. **`New-AzureRouteTable`** コマンドレットを実行して、フロントエンドのサブネットのルート テーブルを作成します。

		New-AzureRouteTable -Name UDR-FrontEnd `
			-Location uswest `
			-Label "Route table for front end subnet"

	出力:

		Name         Location   Label                          
		----         --------   -----                          
		UDR-FrontEnd West US    Route table for front end subnet

4. バックエンドのサブネット (192.168.2.0/24) 宛てのすべてのトラフィックを **FW1** VM (192.168.0.4) に送信するために、**`Set-AzureRoute`** コマンドレットを実行して、上記で作成済みのルート テーブル内にルートを作成します。
	
		Get-AzureRouteTable UDR-FrontEnd `
			|Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
			-NextHopType VirtualAppliance `
			-NextHopIpAddress 192.168.0.4

	出力:

		Name     : UDR-FrontEnd
		Location : West US
		Label    : Route table for frontend subnet
		Routes   : 
		           Name                 Address Prefix    Next hop type        Next hop IP address
		           ----                 --------------    -------------        -------------------
		           RouteToBackEnd       192.168.2.0/24    VirtualAppliance     192.168.0.4  

5. **`Set-AzureSubnetRouteTable`** コマンドレットを実行して、上記で作成したルート テーブルを **FrontEnd** サブネットに関連付けます。

		Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
			-SubnetName FrontEnd `
			-RouteTableName UDR-FrontEnd
 
## バックエンドのサブネットの UDR を作成する
上記のシナリオに基づいて、バックエンドのサブネットに必要なルート テーブルとルートを作成するには、次の手順に従います。

3. **`New-AzureRouteTable`** コマンドレットを実行して、バックエンドのサブネットのルート テーブルを作成します。

		New-AzureRouteTable -Name UDR-BackEnd `
			-Location uswest `
			-Label "Route table for back end subnet"

4. フロントエンドのサブネット (192.168.1.0/24) 宛てのすべてのトラフィックを **FW1** VM (192.168.0.4) に送信するために、**`Set-AzureRoute`** コマンドレットを実行して、上記で作成済みのルート テーブル内にルートを作成します。

		Get-AzureRouteTable UDR-BackEnd `
			|Set-AzureRoute -RouteName RouteToFrontEnd -AddressPrefix 192.168.1.0/24 `
			-NextHopType VirtualAppliance `
			-NextHopIpAddress 192.168.0.4

5. **`Set-AzureSubnetRouteTable`** コマンドレットを実行して、上記で作成したルート テーブルを **BackEnd** サブネットに関連付けます。

		Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
			-SubnetName BackEnd `
			-RouteTableName UDR-BackEnd

## FW1 VM で IP 転送を有効にする
FW1 VM で IP 転送を有効にするには、次の手順に従います。

1. **`Get-AzureIPForwarding`** コマンドレットを実行して IP 転送の状態をチェックします。

		Get-AzureVM -Name FW1 -ServiceName TestRGFW `
			| Get-AzureIPForwarding

	出力:

		Disabled

2. **`Set-AzureIPForwarding`** コマンドを実行して、*FW1* VM で IP 転送を有効にします。

		Get-AzureVM -Name FW1 -ServiceName TestRGFW `
			| Set-AzureIPForwarding -Enable

<!---HONumber=AcomDC_0810_2016-->