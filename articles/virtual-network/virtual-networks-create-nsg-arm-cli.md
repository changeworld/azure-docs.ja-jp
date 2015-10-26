<properties 
   pageTitle="Azure CLI を使用して ARM モードで NSG を作成する方法 | Microsoft Azure"
   description="Azure CLI を使用して ARM で NSG を作成してデプロイする方法について"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/15/2015"
   ms.author="telmos" />

# Azure CLI で NSG を作成する方法

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]この記事では、リソース マネージャーのデプロイメント モデルについて説明します。[クラシック デプロイメント モデルで NSG を作成](virtual-networks-create-nsg-classic-cli.md)することもできます。

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

以下の Azure CLI のサンプル コマンドでは、上記シナリオに基づいて単純な環境が既に作成されていると想定します。このドキュメントに表示されているコマンドを実行するには、まず[このテンプレート](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd)をデプロイしてテスト環境を構築してから **[Azure にデプロイ]** をクリックし、必要に応じて既定のパラメーター値を置き換えてから、ポータルの指示に従います。

## フロントエンドのサブネットの NSG を作成する方法
上記のシナリオに基づいて *NSG-FrontEnd* という名前の NSG を作成するには、次の手順に従います。

1. Azure CLI を初めて使用する場合は、「[Azure CLI のインストール](xplat-cli-install.md)」を参照して、Azure のアカウントとサブスクリプションを選択する時点までの指示に従います。

2. 次に示すように、**azure config mode** コマンドを実行してリソース マネージャー モードに切り替えます。

		azure config mode arm

	予想される出力:

		info:    New mode is arm

3. **azure network nsg create** コマンドを実行して NSG を作成します。

		azure network nsg create -g TestRG -l westus -n NSG-FrontEnd

	予想される出力:

		info:    Executing command network nsg create
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Creating a network security group "NSG-FrontEnd"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
		data:    Name                            : NSG-FrontEnd
		data:    Type                            : Microsoft.Network/networkSecurityGroups
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Security group rules:
		data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
		data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
		data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000   
		data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001   
		data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500   
		data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000   
		data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001   
		data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500   
		info:    network nsg create command OK

	パラメーター:
	- **-g (または --resource-group)**。NSG の作成場所となるリソース グループの名前です。ここでは、*TestRG* です。
	- **-l (または --location)**。NSG が作成される Azure リージョンです。ここでは、*westus* です。
	- **-n (または --name)**。新しい NSG の名前です。ここでは、*NSG-FrontEnd* です。

4. **azure network nsg rule create** コマンドを実行して、インターネットからポート 3389 (RDP) へのアクセスを許可する規則を作成します。

		azure network nsg rule create -g TestRG -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389

	予想される出力:

		info:    Executing command network nsg rule create
		warn:    Using default direction: Inbound
		info:    Looking up the network security rule "rdp-rule"
		info:    Creating a network security rule "rdp-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp
		-rule
		data:    Name                            : rdp-rule
		data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
		data:    Provisioning state              : Succeeded
		data:    Source IP                       : Internet
		data:    Source Port                     : *
		data:    Destination IP                  : *
		data:    Destination Port                : 3389
		data:    Protocol                        : Tcp
		data:    Direction                       : Inbound
		data:    Access                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK

	パラメーター:

	- **-a (または --nsg-name)**。規則が作成される NSG の名前です。ここでは、*NSG-FrontEnd* です。
	- **-n (または --name)**。新しい規則の名前です。ここでは、*rdp-rule* です。
	- **-c (または --access)**。規則のアクセス レベルです (拒否または許可)。
	- **-p (または --protocol)**。規則のプロトコル (TCP、UDP、または *) です。
	- **-r (または --direction)**。接続の方向です (受信または送信)。
	- **-y (または --priority)**。規則の優先度です。
	- **-f (または --source-address-prefix)**。CIDR または既定のタグを使用する発信元アドレスのプレフィックス。
	- **-o (または --source-port-range)**。発信元ポート、またはポート範囲です。
	- **-e (または --destination-address-prefix)**。CIDR または既定のタグを使用する接続先アドレスのプレフィックス。
	- **-u (または --destination-port-range)**。接続先ポート、またはポート範囲です。	

5. **azure network nsg rule create** コマンドを実行して、インターネットからポート 80 (HTTP) へのアクセスを許可する規則を作成します。

		azure network nsg rule create -g TestRG -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80

	予想される出力:

		info:    Executing command network nsg rule create
		info:    Looking up the network security rule "web-rule"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
		data:    Name                            : web-rule
		data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
		data:    Provisioning state              : Succeeded
		data:    Source IP                       : Internet
		data:    Source Port                     : *
		data:    Destination IP                  : *
		data:    Destination Port                : 80
		data:    Protocol                        : Tcp
		data:    Direction                       : Inbound
		data:    Access                          : Allow
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. **azure network vnet subnet set** コマンドを実行して、NSG をフロントエンドのサブネットにリンクさせます。

		azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -o NSG-FrontEnd

	予想される出力:

		info:    Executing command network vnet subnet set
		info:    Looking up the subnet "FrontEnd"
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Setting subnet "FrontEnd"
		info:    Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:    Network security group          : [object Object]
		data:    IP configurations:
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ip
		Configurations/ipconfig1
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ip
		Configurations/ipconfig1
		data:    
		info:    network vnet subnet set command OK

## バックエンドのサブネットの NSG を作成する方法
上記のシナリオに基づいて *NSG-BackEnd* という名前の NSG を作成するには、次の手順に従います。

3. **azure network nsg create** コマンドを実行して NSG を作成します。

		azure network nsg create -g TestRG -l westus -n NSG-BackEnd

	予想される出力:

		info:    Executing command network nsg create
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Creating a network security group "NSG-BackEnd"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkSecurityGroups/NSG-BackEnd
		data:    Name                            : NSG-BackEnd
		data:    Type                            : Microsoft.Network/networkSecurityGroups
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Security group rules:
		data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
		data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
		data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000   
		data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001   
		data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500   
		data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000   
		data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001   
		data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500   
		info:    network nsg create command OK

4. **azure network nsg rule create** コマンドを実行して、フロントエンドのサブネットからポート 1433 (SQL) へのアクセスを許可する規則を作成します。

		azure network nsg rule create -g TestRG -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433

	予想される出力:

		info:    Executing command network nsg rule create
		info:    Looking up the network security rule "sql-rule"
		info:    Creating a network security rule "sql-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkSecurityGroups/NSG-BackEnd/securityRules/sql-rule
		data:    Name                            : sql-rule
		data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
		data:    Provisioning state              : Succeeded
		data:    Source IP                       : 192.168.1.0/24
		data:    Source Port                     : *
		data:    Destination IP                  : *
		data:    Destination Port                : 1433
		data:    Protocol                        : Tcp
		data:    Direction                       : Inbound
		data:    Access                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK

5. **azure network nsg rule create** コマンドを実行して、インターネットへのアクセスを拒否する規則を作成します。

		azure network nsg rule create -g TestRG -a NSG-BackEnd -n web-rule -c Deny -p * -r Outbound -y 200 -f * -o * -e Internet -u *

	予想される出力:

		info:    Executing command network nsg rule create
		info:    Looking up the network security rule "web-rule"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkSecurityGroups/NSG-BackEnd/securityRules/web-rule
		data:    Name                            : web-rule
		data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
		data:    Provisioning state              : Succeeded
		data:    Source IP                       : *
		data:    Source Port                     : *
		data:    Destination IP                  : Internet
		data:    Destination Port                : *
		data:    Protocol                        : *
		data:    Direction                       : Outbound
		data:    Access                          : Deny
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. **azure network vnet subnet set** コマンドを実行して、NSG をバックエンドのサブネットにリンクさせます。

		azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -o NSG-BackEnd

	予想される出力:

		info:    Executing command network vnet subnet set
		info:    Looking up the subnet "BackEnd"
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Setting subnet "BackEnd"
		info:    Looking up the subnet "BackEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/BackEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : BackEnd
		data:    Address prefix                  : 192.168.2.0/24
		data:    Network security group          : [object Object]
		data:    IP configurations:
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICSQL1/ip
		Configurations/ipconfig1
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICSQL2/ip
		Configurations/ipconfig1
		data:    
		info:    network vnet subnet set command OK

<!---HONumber=Oct15_HO3-->