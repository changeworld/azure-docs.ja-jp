<properties 
   pageTitle="リソース マネージャーでテンプレートを使用してルーティングを制御し、仮想アプライアンスを使用する | Microsoft Azure"
   description="Azure リソース マネージャーでテンプレートを使用してルーティングを制御し、仮想アプライアンスを使用する方法について説明します。"
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
   ms.date="02/23/2016"
   ms.author="telmos" />

#テンプレートを使用してリソース マネージャーでユーザー定義のルート (UDR) を作成する

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]この記事では、リソース マネージャーのデプロイ モデルについて説明します。

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

## テンプレート ファイルの UDR リソース

[サンプル テンプレート](https://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR)を表示してダウンロードすることができます。

以下のセクションは、上記のシナリオに基づいた、**azuredeploy-vnet-nsg-udr.json** ファイル内のフロントエンド UDR の定義を示します。

	"apiVersion": "2015-06-15",
	"type": "Microsoft.Network/routeTables",
	"name": "[parameters('frontEndRouteTableName')]",
	"location": "[resourceGroup().location]",
	"tags": {
	  "displayName": "UDR - FrontEnd"
	},
	"properties": {
	  "routes": [
	    {
	      "name": "RouteToBackEnd",
	      "properties": {
	        "addressPrefix": "[parameters('backEndSubnetPrefix')]",
	        "nextHopType": "VirtualAppliance",
	        "nextHopIpAddress": "[parameters('vmaIpAddress')]"
	      }
	    }
	  ]


フロントエンドのサブネットに UDR を関連付けるには、テンプレートのサブネットの定義を変更し、UDR の参照 ID を使用する必要があります。

	"subnets": [
		"name": "[parameters('frontEndSubnetName')]",
		"properties": {
		  "addressPrefix": "[parameters('frontEndSubnetPrefix')]",
		  "networkSecurityGroup": {
		    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('frontEndNSGName'))]"
		  },
		  "routeTable": {
		      "id": "[resourceId('Microsoft.Network/routeTables', parameters('frontEndRouteTableName'))]"
		  }
		},
		...]

同じことがバックエンド NSG およびテンプレートのバックエンドのサブネットに行われていることを確認します。

さらに、パケットの受信および転送に使用される NIC 上で、**FW1** VM の IP 転送プロパティが有効になっていることを確認する必要があります。以下のセクションは、上記のシナリオに基づいた、azuredeploy-nsg-udr.json ファイル内の FW1 用 NIC の定義を示します。

	"apiVersion": "2015-06-15",
	"type": "Microsoft.Network/networkInterfaces",
	"location": "[variables('location')]",
	"tags": {
	  "displayName": "NetworkInterfaces - DMZ"
	},
	"name": "[concat(variables('fwVMSettings').nicName, copyindex(1))]",
	"dependsOn": [
	  "[concat('Microsoft.Network/publicIPAddresses/', variables('fwVMSettings').pipName, copyindex(1))]",
	  "[concat('Microsoft.Resources/deployments/', 'vnetTemplate')]"
	],
	"properties": {
	  "ipConfigurations": [
	    {
	      "name": "ipconfig1",
	      "properties": {
	        "enableIPForwarding": true,
	        "privateIPAllocationMethod": "Static",
	        "privateIPAddress": "[concat('192.168.0.',copyindex(4))]",
	        "publicIPAddress": {
	          "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('fwVMSettings').pipName, copyindex(1)))]"
	        },
	        "subnet": {
	          "id": "[variables('dmzSubnetRef')]"
	        }
	      }
	    }
	  ]
	},
	"copy": {
	  "name": "fwniccount",
	  "count": "[parameters('fwCount')]"
	}

## [デプロイ] をクリックして ARM テンプレートをデプロイする

パブリック リポジトリで使用できるサンプル テンプレートは、上記のシナリオの生成に使用した既定値を含むパラメーター ファイルを使用します。"クリックしてデプロイ" を使用してこのテンプレートをデプロイするには、[このリンク](https://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR)に従って、**[Azure へのデプロイ]** をクリックし、必要に応じて既定のパラメーター値を置き換えて、ポータルの指示に従います。

## PowerShell を使用して ARM テンプレートをデプロイする

PowerShell を使用してダウンロードした ARM テンプレートをデプロイするには、次の手順に従います。

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Azure PowerShell を初めて使用する場合は、[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)を参照し、このページにある手順をすべて最後まで実行し、Azure にサインインしてサブスクリプションを選択します。

2. リソース グループを作成するには、`New-AzureRmResourceGroup` コマンドレットを実行します。

		New-AzureRmResourceGroup -Name TestRG -Location westus

3. テンプレートをデプロイするには、`New-AzureRmResourceGroupDeployment` コマンドレットを実行します。

		New-AzureRmResourceGroupDeployment -Name DeployUDR -ResourceGroupName TestRG `
		    -TemplateUri https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.json `
		    -TemplateParameterUri https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.parameters.json	    	

	予想される出力:

		ResourceGroupName : TestRG
		Location          : westus
		ProvisioningState : Succeeded
		Tags              : 
		Permissions       : 
		                    Actions  NotActions
		                    =======  ==========
		                    *                  
		                    
		Resources         : 
		                    Name                Type                                     Location
		                    ==================  =======================================  ========
		                    ASFW                Microsoft.Compute/availabilitySets       westus  
		                    ASSQL               Microsoft.Compute/availabilitySets       westus  
		                    ASWEB               Microsoft.Compute/availabilitySets       westus  
		                    FW1                 Microsoft.Compute/virtualMachines        westus  
		                    SQL1                Microsoft.Compute/virtualMachines        westus  
		                    SQL2                Microsoft.Compute/virtualMachines        westus  
		                    WEB1                Microsoft.Compute/virtualMachines        westus  
		                    WEB2                Microsoft.Compute/virtualMachines        westus  
		                    NICFW1              Microsoft.Network/networkInterfaces      westus  
		                    NICSQL1             Microsoft.Network/networkInterfaces      westus  
		                    NICSQL2             Microsoft.Network/networkInterfaces      westus  
		                    NICWEB1             Microsoft.Network/networkInterfaces      westus  
		                    NICWEB2             Microsoft.Network/networkInterfaces      westus  
		                    NSG-BackEnd         Microsoft.Network/networkSecurityGroups  westus  
		                    NSG-FrontEnd        Microsoft.Network/networkSecurityGroups  westus  
		                    PIPFW1              Microsoft.Network/publicIPAddresses      westus  
		                    PIPSQL1             Microsoft.Network/publicIPAddresses      westus  
		                    PIPSQL2             Microsoft.Network/publicIPAddresses      westus  
		                    PIPWEB1             Microsoft.Network/publicIPAddresses      westus  
		                    PIPWEB2             Microsoft.Network/publicIPAddresses      westus  
		                    UDR-BackEnd         Microsoft.Network/routeTables            westus  
		                    UDR-FrontEnd        Microsoft.Network/routeTables            westus  
		                    TestVNet            Microsoft.Network/virtualNetworks        westus  
		                    testvnetstorageprm  Microsoft.Storage/storageAccounts        westus  
		                    testvnetstoragestd  Microsoft.Storage/storageAccounts        westus  
		                    
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

## Azure CLI を使用して ARM テンプレートをデプロイする

Azure CLI を使用して ARM テンプレートをデプロイするには、次の手順に従います。

1. Azure CLI を初めて使用する場合は、「[Azure CLI のインストール](../xplat-cli-install.md)」を参照して、Azure のアカウントとサブスクリプションを選択する時点までの指示に従います。
2. 次に示すように、`azure config mode` コマンドを実行して、リソース マネージャー モードに切り替えます。

		azure config mode arm

	上記のコマンドで想定される出力を次に示します。

		info:    New mode is arm

3. ブラウザーから ****https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.parameters.json** に移動し、json ファイルの内容をコピーして、コンピューター内の新しいファイルに貼り付けます。このシナリオでは、次の値を **c:\\udr\\azuredeploy.parameters.json** という名前のファイルにコピーします。

		{
		  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "fwCount": {
		      "value": 1
		    },
		    "webCount": {
		      "value": 2
		    },
		    "sqlCount": {
		      "value": 2
		    }
		  }
		}

4. 上記でダウンロードして変更したテンプレート ファイルとパラメーター ファイルを使用して、**azure group create** コマンドレットを実行して新しい VNet をデプロイします。出力の後に表示される一覧では、使用されたパラメーターについて説明されています。

		azure group create -n TestRG -l westus --template-uri 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.json' -e 'c:\udr\azuredeploy.parameters.json'

	予想される出力:

		info:    Executing command group create
		info:    Getting resource group TestRG
		info:    Updating resource group TestRG
		info:    Updated resource group TestRG
		info:    Initializing template configurations and parameters
		info:    Creating a deployment
		info:    Created template deployment "azuredeploy"
		data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
		data:    Name:                TestRG
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:    
		info:    group create command OK

5. **azure group show** コマンドレットを実行し、新しいリソース グループに作成されたリソースを表示します。

		azure group show TestRG

	予想される結果
		
		info:    Executing command group show
		info:    Listing resource groups
		info:    Listing resources for the group
		data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
		data:    Name:                TestRG
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:    Resources:
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/ASFW
		data:      Name    : ASFW
		data:      Type    : availabilitySets
		data:      Location: westus
		data:      Tags    : displayName=AvailabilitySet - DMZ
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/ASSQL
		data:      Name    : ASSQL
		data:      Type    : availabilitySets
		data:      Location: westus
		data:      Tags    : displayName=AvailabilitySet - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/ASWEB
		data:      Name    : ASWEB
		data:      Type    : availabilitySets
		data:      Location: westus
		data:      Tags    : displayName=AvailabilitySet - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/FW1
		data:      Name    : FW1
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - DMZ
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/SQL1
		data:      Name    : SQL1
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/SQL2
		data:      Name    : SQL2
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WEB1
		data:      Name    : WEB1
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WEB2
		data:      Name    : WEB2
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICFW1
		data:      Name    : NICFW1
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - DMZ
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL1
		data:      Name    : NICSQL1
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL2
		data:      Name    : NICSQL2
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1
		data:      Name    : NICWEB1
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2
		data:      Name    : NICWEB2
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd
		data:      Name    : NSG-BackEnd
		data:      Type    : networkSecurityGroups
		data:      Location: westus
		data:      Tags    : displayName=NSG - Front End
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
		data:      Name    : NSG-FrontEnd
		data:      Type    : networkSecurityGroups
		data:      Location: westus
		data:      Tags    : displayName=NSG - Remote Access
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPFW1
		data:      Name    : PIPFW1
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - DMZ
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPSQL1
		data:      Name    : PIPSQL1
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPSQL2
		data:      Name    : PIPSQL2
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPWEB1
		data:      Name    : PIPWEB1
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPWEB2
		data:      Name    : PIPWEB2
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd
		data:      Name    : UDR-BackEnd
		data:      Type    : routeTables
		data:      Location: westus
		data:      Tags    : displayName=Route Table - Back End
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd
		data:      Name    : UDR-FrontEnd
		data:      Type    : routeTables
		data:      Location: westus
		data:      Tags    : displayName=UDR - FrontEnd
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		data:      Name    : TestVNet
		data:      Type    : virtualNetworks
		data:      Location: westus
		data:      Tags    : displayName=VNet
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/testvnetstorageprm
		data:      Name    : testvnetstorageprm
		data:      Type    : storageAccounts
		data:      Location: westus
		data:      Tags    : displayName=Storage Account - Premium
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/testvnetstoragestd
		data:      Name    : testvnetstoragestd
		data:      Type    : storageAccounts
		data:      Location: westus
		data:      Tags    : displayName=Storage Account - Simple
		data:    
		data:    Permissions:
		data:      Actions: *
		data:      NotActions: 
		data:    
		info:    group show command OK

>[AZURE.TIP] すべてのリソースが表示されない場合は、`azure group deployment show` コマンドを実行して、デプロイのプロビジョニング状態が *Succeded* になっていることを確認します。

<!---HONumber=AcomDC_0330_2016-->