<properties
	pageTitle="Resource Manager テンプレートでの VM の作成 | Microsoft Azure"
	description="Resource Manager テンプレートと PowerShell を使用して、新しい Windows 仮想マシンを簡単に作成します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2016"
	ms.author="davidmu"/>

# リソース マネージャー テンプレートで Windows 仮想マシンを作成する

この記事では、Azure Resource Manager テンプレートを紹介し、PowerShell を使用してテンプレートをデプロイする方法を示します。このテンプレートは、1 つのサブネットを持つ新しい仮想ネットワークに、Windows Server を実行する 1 つの仮想マシンをデプロイします。

この記事の手順を実行するには、約 20 分かかります。

> [AZURE.IMPORTANT] VM を可用性セットの一部にする場合は、VM を作成するときにセットに追加する必要があります。作成後に VM を可用性セットに追加する方法は現在ありません。

## 手順 1: テンプレート ファイルの作成

「[Azure Resource Manager のテンプレートの作成](../resource-group-authoring-templates.md)」にある情報を使用して、独自のテンプレートを作成できます。また、[Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)から作成済みのテンプレートをデプロイすることもできます。

1. 任意のテキスト エディターを開き、この JSON 情報を、*VirtualMachineTemplate.json* という名前の新しいファイルにコピーします。

        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": {
              "type": "string",
              "metadata": {
                "Description": "The name of the storage account where the VM disk is stored."
              }
            },
            "adminUsername": {
              "type": "string",
              "metadata": {
                "Description": "The name of the administrator account on the VM."
              }
            },
            "adminPassword": {
              "type": "securestring",
              "metadata": {
                "Description": "The administrator account password on the VM."
              }
            },
            "dnsNameForPublicIP": {
              "type": "string",
              "metadata": {
                "Description": "The name of the public IP address used to access the VM."
              }
            }
          },
          "variables": {
            "location": "Central US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "windowsOSVersion": "2012-R2-Datacenter",
            "OSDiskName": "osdisk1",
            "nicName": "nc1",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "sn1",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "ip1",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "vm1",
            "vmSize": "Standard_A0",
            "virtualNetworkName": "vn1",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('newStorageAccountName')]",
              "apiVersion": "2015-06-15",
              "location": "[variables('location')]",
              "properties": {
                "accountType": "[variables('storageAccountType')]"
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "[variables('publicIPAddressName')]",
              "location": "[variables('location')]",
              "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                  "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "[variables('virtualNetworkName')]",
              "location": "[variables('location')]",
              "properties": {
                "addressSpace": {
                  "addressPrefixes": [
                    "[variables('addressPrefix')]"
                  ]
                },
                "subnets": [
                  {
                    "name": "[variables('subnetName')]",
                    "properties": {
                      "addressPrefix": "[variables('subnetPrefix')]"
                    }
                  }
                ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "[variables('nicName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
              ],
              "properties": {
                "ipConfigurations": [
                  {
                    "name": "ipconfig1",
                    "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                      },
                      "subnet": {
                        "id": "[variables('subnetRef')]"
                      }
                    }
                  }
                ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "[variables('vmName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
              ],
              "properties": {
                "hardwareProfile": {
                  "vmSize": "[variables('vmSize')]"
                },
                "osProfile": {
                  "computername": "[variables('vmName')]",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "[variables('imagePublisher')]",
                    "offer": "[variables('imageOffer')]",
                    "sku" : "[variables('windowsOSVersion')]",
                    "version":"latest"
                  },
                  "osDisk" : {
                    "name": "osdisk",
                    "vhd": {
                      "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces": [
                    {
                      "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                    }
                  ]
                }
              }
            }
          ]
        }
        
    >[AZURE.NOTE] この記事では、Windows Server オペレーティング システムのいずれかのバージョンを実行する仮想マシンを作成します。他のイメージの選択の詳細については、[Windows PowerShell と Azure CLI による Azure 仮想マシン イメージのナビゲーションと選択](virtual-machines-linux-cli-ps-findimage.md)に関する記事をご覧ください。
    
2. テンプレート ファイルを保存します。

## 手順 2: パラメーター ファイルの作成

テンプレートで定義されているリソース パラメーターの値を指定するには、値を含むパラメーター ファイルを作成し、テンプレートと共にリソース マネージャーに送信します。

1. テキスト エディターで、この JSON 情報を、*Parameters.json* という名前の新しいファイルにコピーします。

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "value": "mytestsa1" },
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" },
            "dnsNameForPublicIP": { "value": "mytestdns1" }
          }
        }

    >[AZURE.NOTE] [ユーザー名とパスワードの要件](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm)に関するページで詳細を確認してください。

2. パラメーター ファイルを保存します。

## 手順 3: Azure PowerShell のインストール

最新バージョンの Azure PowerShell をインストールし、使用するサブスクリプションを選択し、Azure アカウントにサインインする方法については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」を参照してください。

## 手順 4: リソース グループの作成

すべてのリソースをリソース グループにデプロイする必要があります。詳細については、「[Azure リソース マネージャーの概要](../resource-group-overview.md)」をご覧ください。

1. リソースを作成できる場所の一覧を取得します。

	    Get-AzureRmLocation | sort DisplayName | Select DisplayName

2. **$locName** の値を一覧の場所に置き換えます (例: **Central US** (米国中部))。変数を作成します。

        $locName = "location name"
        
3. **$rgName** の値を、新しいリソース グループの名前に置き換えます。変数とリソース グループを作成します。

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    次のような結果が表示されます。
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1

### 手順 5: テンプレートとパラメーターを使用したリソースの作成

1. **$deployName** の値をデプロイの名前に置き換えます。**$templatePath** の値をテンプレート ファイルのパスと名前に置き換えます。**$parameterFile** の値をパラメーター ファイルのパスと名前に置き換えます。変数を作成します。

        $deployName="deployment name"
        $templatePath = "template path"
        $parameterFile = "parameter file"

2. テンプレートをデプロイします。

        New-AzureRmResourceGroupDeployment -ResourceGroupName "davidmurg6" -TemplateFile $templatePath -TemplateParameterFile $parameterFile

    次のような結果が表示されます。

        DeploymentName    : VirtualMachineTemplate
        ResourceGroupName : myrg1
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2016 8:11:37 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            newStorageAccountName  String                     mytestsa1
                            adminUsername    String                     mytestacct1
                            adminPassword    SecureString
                            dnsNameForPublicIP  String                     mytestdns1

        Outputs           :

    >[AZURE.NOTE] テンプレートとパラメーターを Azure ストレージ アカウントからデプロイすることもできます。詳細については、「[Azure Storage での Azure PowerShell の使用](../storage-powershell-guide-full.md)」をご覧ください。

## 次のステップ

- デプロイに問題がある場合は、[Azure Portal でのリソース グループのデプロイのトラブルシューティング](../resource-manager-troubleshoot-deployments-portal.md)に関する記事をご覧ください。
- [Azure Resource Manager と PowerShell を使用した仮想マシンの管理](virtual-machines-windows-ps-manage.md)に関する記事で、作成した仮想マシンを管理する方法を確認します。

<!---HONumber=AcomDC_0921_2016-->