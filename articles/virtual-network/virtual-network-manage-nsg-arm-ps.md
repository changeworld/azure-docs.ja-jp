<properties 
   pageTitle="リソース マネージャーで PowerShell を使用して NSG を管理する | Microsoft Azure"
   description="リソース マネージャーで PowerShell を使用して既存の NSG を管理する方法について説明します。"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="telmos" />

# PowerShell を使用して NSG を管理する

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル。

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## 情報を取得する

既存の NSG を表示し、既存の NSG の規則を取得し、NSG が関連付けられているリソースを検索することができます。

### 既存の NSG を表示する
サブスクリプションの既存の NSG をすべて表示するには、次のように `Get-AzureRmNetworkSecurityGroup` コマンドレットを実行します。

	Get-AzureRmNetworkSecurityGroup

予想される結果:

	Name                 : NSG-BackEnd
	ResourceGroupName    : RG-NSG
	Location             : westus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-BackEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 	                       
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	
	Name                 : NSG-FrontEnd
	ResourceGroupName    : RG-NSG
	Location             : eastus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	                     	
	Name                 : WEB1
	ResourceGroupName    : RG101
	Location             : eastus2
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG101/providers/M
	                       icrosoft.Network/networkSecurityGroups/WEB1
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]


特定のリソース グループの NSG 一覧を表示するには、次のように `Get-AzureRmNetworkSecurityGroup` コマンドレットを実行します。

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG

予想される出力:

	Name                 : NSG-BackEnd
	ResourceGroupName    : RG-NSG
	Location             : westus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-BackEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 	                       
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	
	Name                 : NSG-FrontEnd
	ResourceGroupName    : RG-NSG
	Location             : eastus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
		 
### NSG のすべての規則を一覧表示する

**NSG-FrontEnd** という NSG の規則を表示するには、次のように `Get-AzureRmNetworkSecurityGroup` コマンドレットを実行します。

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules

予想される出力:
	
	Name                     : rdp-rule
	Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/						   Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
	Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ProvisioningState        : Succeeded
	Description              : Allow RDP
	Protocol                 : Tcp
	SourcePortRange          : *
	DestinationPortRange     : 3389
	SourceAddressPrefix      : Internet
	DestinationAddressPrefix : *
	Access                   : Allow
	Priority                 : 100
	Direction                : Inbound
	
	Name                     : web-rule
	Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/						   Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
	Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ProvisioningState        : Succeeded
	Description              : Allow HTTP
	Protocol                 : Tcp
	SourcePortRange          : *
	DestinationPortRange     : 80
	SourceAddressPrefix      : Internet
	DestinationAddressPrefix : *
	Access                   : Allow
	Priority                 : 101
	Direction                : Inbound

>[AZURE.NOTE] `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules` を使用して、**NSG-FrontEnd** NSG の既定の規則を一覧表示することもできます。

### NSG の関連付けを表示する

**NSG-FrontEnd** NSG が関連付けられているリソースを表示するには、次のように `Get-AzureRmNetworkSecurityGroup` コマンドレットを実行します。

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd

次の **NetworkInterfaces** と **Subnets** の各プロパティを確認してください。

	NetworkInterfaces    : []
	Subnets              : [
	                         {
	                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
	                           "IpConfigurations": []
	                         }
	                       ]

上の例では、NSG はネットワーク インターフェイス (NIC) に関連付けられておらず、**FrontEnd** というサブネットに関連付けられています。

## 規則を管理する

既存の NSG に規則を追加し、既存の規則を編集し、規則を削除することができます。

### 規則を追加する

任意のコンピューターから **NSG-FrontEnd** NSG へのポート **443** に対する**受信**トラフィックを許可する規則を追加するには、次の手順を実行します。

1. `Get-AzureRmNetworkSecurityGroup` コマンドレットを実行して既存の NSG を取得し、次のように変数に格納します。

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. 次のように `Add-AzureRmNetworkSecurityRuleConfig` コマンドレットを実行します。

		Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule `
		    -Description "Allow HTTPS" `
		    -Access Allow `
		    -Protocol Tcp `
		    -Direction Inbound `
		    -Priority 102 `
		    -SourceAddressPrefix * `
		    -SourcePortRange * `
		    -DestinationAddressPrefix * `
		    -DestinationPortRange 443

3. NSG に対する変更を保存するには、次のように `Set-AzureRmNetworkSecurityGroup` コマンドレットを実行します。

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	予想される出力 (セキュリティ規則のみを表示):

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         },
		                         {
		                           "Name": "https-rule",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
		                           "Description": "Allow HTTPS",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "443",
		                           "SourceAddressPrefix": "*",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 102,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]

### 規則を変更する

上の手順で作成した規則を、**インターネット**からの受信トラフィックのみを許可する規則に変更するには、次の手順を実行します。

1. `Get-AzureRmNetworkSecurityGroup` コマンドレットを実行して既存の NSG を取得し、次のように変数に格納します。

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. 次のように `Set-AzureRmNetworkSecurityRuleConfig` コマンドレットを実行します。

		Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule `
		    -Description "Allow HTTPS" `
		    -Access Allow `
		    -Protocol Tcp `
		    -Direction Inbound `
		    -Priority 102 `
		    -SourceAddressPrefix * `
		    -SourcePortRange Internet `
		    -DestinationAddressPrefix * `
		    -DestinationPortRange 443

3. NSG に対する変更を保存するには、次のように `Set-AzureRmNetworkSecurityGroup` コマンドレットを実行します。

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	予想される出力 (セキュリティ規則のみを表示):

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         },
		                         {
		                           "Name": "https-rule",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
		                           "Description": "Allow HTTPS",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "443",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 102,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]

### 規則を削除する

1. `Get-AzureRmNetworkSecurityGroup` コマンドレットを実行して既存の NSG を取得し、次のように変数に格納します。

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. 次のように `Remove-AzureRmNetworkSecurityRuleConfig` コマンドレットを実行します。

		Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule

3. NSG に対する変更を保存するには、次のように `Set-AzureRmNetworkSecurityGroup` コマンドレットを実行します。

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	予想される出力 (セキュリティ規則のみを表示。**https-rule** がない点に注目してください):

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         }
		                       ]

## 関連付けを管理する

NSG をサブネットと NIC に関連付けることができます。また、NSG とリソースの関連付けを解除することもできます。

### NSG を NIC に関連付ける

**NSG-FrontEnd** NSG を **TestNICWeb1** NIC に関連付けるには、次の手順を実行します。

1. `Get-AzureRmNetworkSecurityGroup` コマンドレットを実行して既存の NSG を取得し、次のように変数に格納します。

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. `Get-AzureRmNetworkInterface` コマンドレットを実行して既存の NIC を取得し、次のように変数に格納します。

		$nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
		    -Name TestNICWeb1

3. 次のように、**NIC** 変数の **NetworkSecurityGroup** プロパティを **NSG** 変数の値に設定します。

		$nic.NetworkSecurityGroup = $nsg

4. NIC に対する変更を保存するには、次のように `Set-AzureRmNetworkInterface` コマンドレットを実行します。

		Set-AzureRmNetworkInterface -NetworkInterface $nic

	予想される出力 (**NetworkSecurityGroup** プロパティのみを表示):

		NetworkSecurityGroup : {
		                         "SecurityRules": [],
		                         "DefaultSecurityRules": [],
		                         "NetworkInterfaces": [],
		                         "Subnets": [],
		                         "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                       }

### NSG と NIC の関連付けを解除する

**NSG-FrontEnd** NSG と **TestNICWeb1** NIC の関連付けを解除するには、次の手順を実行します。

1. `Get-AzureRmNetworkSecurityGroup` コマンドレットを実行して既存の NSG を取得し、次のように変数に格納します。

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. `Get-AzureRmNetworkInterface` コマンドレットを実行して既存の NIC を取得し、次のように変数に格納します。

		$nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
		    -Name TestNICWeb1

3. 次のように、**NIC** 変数の **NetworkSecurityGroup** プロパティを **$null** に設定します。

		$nic.NetworkSecurityGroup = $null

4. NIC に対する変更を保存するには、次のように `Set-AzureRmNetworkInterface` コマンドレットを実行します。

		Set-AzureRmNetworkInterface -NetworkInterface $nic

	予想される出力 (**NetworkSecurityGroup** プロパティのみを表示):

		NetworkSecurityGroup : null

### NSG とサブネットの関連付けを解除する

**NSG-FrontEnd** NSG と **FrontEnd** サブネットの関連付けを解除するには、次の手順を実行します。

1. `Get-AzureRmVirtualNetwork` コマンドレットを実行して既存の VNet を取得し、次のように変数に格納します。

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
		    -Name TestVNet

2. `Get-AzureRmVirtualNetworkSubnetConfig` コマンドレットを実行して **FrontEnd** サブネットを取得し、次のように変数に格納します。

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
		    -Name FrontEnd 

3. 次のように、**subnet** 変数の **NetworkSecurityGroup** プロパティを **$null** に設定します。

		$subnet.NetworkSecurityGroup = $null

4. サブネットに対する変更を保存するには、次のように `Set-AzureRmVirtualNetwork` コマンドレットを実行します。

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	予想される出力 (**FrontEnd** サブネットのプロパティのみを表示。**NetworkSecurityGroup** のプロパティがない点に注目してください):

			...
			Subnets           : [
			                      {
			                        "Name": "FrontEnd",
			                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
			                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
			                        "AddressPrefix": "192.168.1.0/24",
			                        "IpConfigurations": [
			                          {
			                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
			                          },
			                          {
			                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
			                          }
			                        ],
			                        "ProvisioningState": "Succeeded"
			                      },
									...
			                    ]

### NSG サブネットへの関連付け

**NSG-FrontEnd** NSG を **FronEnd** サブネットにもう一度関連付けるには、次の手順を実行します。

1. `Get-AzureRmVirtualNetwork` コマンドレットを実行して既存の VNet を取得し、次のように変数に格納します。

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
		    -Name TestVNet

2. `Get-AzureRmVirtualNetworkSubnetConfig` コマンドレットを実行して **FrontEnd** サブネットを取得し、次のように変数に格納します。

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
		    -Name FrontEnd 

1. `Get-AzureRmNetworkSecurityGroup` コマンドレットを実行して既存の NSG を取得し、次のように変数に格納します。

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

3. 次のように、**subnet** 変数の **NetworkSecurityGroup** プロパティを **$null** に設定します。

		$subnet.NetworkSecurityGroup = $nsg

4. サブネットに対する変更を保存するには、次のように `Set-AzureRmVirtualNetwork` コマンドレットを実行します。

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	予想される出力 (**FrontEnd** サブネットの **NetworkSecurityGroup** プロパティのみを表示):

		...
		"NetworkSecurityGroup": {
		                          "SecurityRules": [],
		                          "DefaultSecurityRules": [],
		                          "NetworkInterfaces": [],
		                          "Subnets": [],
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                        }
		...

## NSG の削除

NSG に関連付けられているリソースがない場合にのみ、NSG を削除できます。NSG を削除するには、次の手順を実行します。

1. NSG に関連付けられているリソースを確認するには、「[NSG の関連付けを表示する](#View-NSGs-associations)」を参照し、`azure network nsg show` を実行します。
2. NSG に関連付けられている NIC がある場合は、「[NSG と NIC の関連付けを解除する](#Dissociate-an-NSG-from-a-NIC)」を参照し、各 NIC について `azure network nic set` を実行します。 
3. NSG に関連付けられているサブネットがある場合は、「[NSG とサブネットの関連付けを解除する](#Dissociate-an-NSG-from-a-subnet)」を参照し、各サブネットについて `azure network vnet subnet set` を実行します。
4. NSG を削除するには、次のように `Remove-AzureRmNetworkSecurityGroup` コマンドレットを実行します。

		Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force

	>[AZURE.NOTE] **-Force** パラメーターを指定すると、削除の確認が求められなくなります。

## 次のステップ

- NSG の[ログを有効にします](virtual-network-nsg-manage-log.md)。

<!---HONumber=AcomDC_0323_2016-->