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
    ms.date="10/06/2016"
    ms.author="davidmu"/>


# <a name="create-a-windows-virtual-machine-with-a-resource-manager-template"></a>リソース マネージャー テンプレートで Windows 仮想マシンを作成する

この記事では、Azure Resource Manager テンプレートを紹介し、PowerShell を使用してテンプレートをデプロイする方法を示します。 このテンプレートは、1 つのサブネットを持つ新しい仮想ネットワークに、Windows Server を実行する 1 つの仮想マシンをデプロイします。

この記事の手順を実行するには、約 20 分かかります。

> [AZURE.IMPORTANT] VM を可用性セットの一部にする場合は、VM を作成するときにセットに追加する必要があります。 作成後に VM を可用性セットに追加する方法は現在ありません。

## <a name="step-1:-create-the-template-file"></a>手順 1: テンプレート ファイルの作成

「 [Azure Resource Manager のテンプレートの作成](../resource-group-authoring-templates.md)」にある情報を使用して、独自のテンプレートを作成できます。 また、 [Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)から作成済みのテンプレートをデプロイすることもできます。

1. 任意のテキスト エディターを開き、必要なスキーマ要素と必要な contentVersion 要素を追加します。

        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
        }
2. [パラメーター](../resource-group-authoring-templates.md#parameters)は必須ではありませんが、パラメーターを指定すると、テンプレートをデプロイする際に値を入力することができます。 contentVersion 要素の後に、parameters 要素とその子要素を追加します。

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }

3. [変数](../resource-group-authoring-templates.md#variables) をテンプレートで使用して、頻繁に変化する可能性がある値、またはパラメーター値の組み合わせから作成する必要のある値を指定できます。 parameters セクションの後に、variables 要素を追加します。

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }
        
4. [リソース](../resource-group-authoring-templates.md#resources) を定義します。 variables セクションの後に、resources セクションを追加します。

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvnet1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
          
    >[AZURE.NOTE] この記事では、Windows Server オペレーティング システムのいずれかのバージョンを実行する仮想マシンを作成します。 他のイメージの選択の詳細については、 [Windows PowerShell と Azure CLI による Azure 仮想マシン イメージのナビゲーションと選択](virtual-machines-linux-cli-ps-findimage.md)に関する記事をご覧ください。  
            
2. テンプレート ファイルを *VirtualMachineTemplate.json* として保存します。

## <a name="step-2:-create-the-parameters-file"></a>手順 2: パラメーター ファイルの作成

テンプレートで定義されているリソース パラメーターの値を指定するには、テンプレートがデプロイされるときに使用される値を含むパラメーター ファイルを作成します。

1. テキスト エディターで、この JSON の内容を、*Parameters.json* という名前の新しいファイルにコピーします。

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

    >[AZURE.NOTE] [ユーザー名とパスワードの要件](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm)に関するページで詳細を確認してください。

2. パラメーター ファイルを保存します。

## <a name="step-3:-install-azure-powershell"></a>手順 3: Azure PowerShell のインストール

最新バージョンの Azure PowerShell をインストールし、サブスクリプションを選択して、ご利用のアカウントにサインインする方法については、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」を参照してください。

## <a name="step-4:-create-a-resource-group"></a>手順 4: リソース グループの作成

すべてのリソースを[リソース グループ](../resource-group-overview.md)にデプロイする必要があります。

1. リソースを作成できる場所の一覧を取得します。

        Get-AzureRmLocation | sort DisplayName | Select DisplayName

2. **$locName** の値を一覧の場所に置き換えます (例: **Central US** (米国中部))。 変数を作成します。

        $locName = "location name"
        
3. **$rgName** の値を、新しいリソース グループの名前に置き換えます。 変数とリソース グループを作成します。

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    次のような結果が表示されます。
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1

## <a name="step-5:-create-the-resources-with-the-template-and-parameters"></a>手順 5: テンプレートとパラメーターを使用したリソースの作成

**$templateFile** の値をテンプレート ファイルのパスと名前に置き換えます。 **$parameterFile** の値をパラメーター ファイルのパスと名前に置き換えます。 変数を作成し、テンプレートをデプロイします。 

        $templateFile = "template file"
        $parameterFile = "parameter file"
        New-AzureRmResourceGroupDeployment -ResourceGroupName $rgName -TemplateFile $templateFile -TemplateParameterFile $parameterFile

    You will see something like this:

        DeploymentName    : VirtualMachineTemplate
        ResourceGroupName : myrg1
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2016 8:11:37 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            adminUsername    String                     mytestacct1
                            adminPassword    SecureString

        Outputs           :

>[AZURE.NOTE] テンプレートとパラメーターを Azure ストレージ アカウントからデプロイすることもできます。 詳細については、「[Azure Storage での Azure PowerShell の使用](../storage/storage-powershell-guide-full.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

- デプロイに問題がある場合は、次の手順として、「[Azure Portal でのリソース グループのデプロイのトラブルシューティング](../resource-manager-troubleshoot-deployments-portal.md)」を参照してください。
- 「[Resource Manager と PowerShell を使用した Azure Virtual Machines の管理](virtual-machines-windows-ps-manage.md)」で、作成した仮想マシンを管理する方法を確認します。



<!--HONumber=Oct16_HO2-->


