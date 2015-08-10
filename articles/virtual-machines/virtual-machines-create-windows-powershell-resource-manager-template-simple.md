<properties
	pageTitle="リソース マネージャー テンプレートと PowerShell で Windows 仮想マシンを作成する"
	description="リソース マネージャー テンプレートと Azure PowerShell を使用して新しい Windows 仮想マシンを作成します。"
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="davidmu"/>

# リソース マネージャー テンプレートと PowerShell で Windows 仮想マシンを作成する

Windows ベースの Azure Virtual Machines (VM) は、Azure PowerShell からリソース マネージャー テンプレートを使って簡単に新規作成できます。このテンプレートは、新しいリソース グループにサブネットを 1 つ持った新しい仮想ネットワークに、Windows を実行する単一の仮想マシンを作成するものです。

![](./media/virtual-machines-create-windows-powershell-resource-manager-template-simple/windowsvm.png)

実際の作業を開始する前に、Azure PowerShell が構成され、準備が整っていることを確認してください。

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## Windows VM の作成

Github テンプレート リポジトリ内のリソース マネージャー テンプレートと Azure PowerShell を使用して Windows VM を作成するには、以下の手順に従います。

Azure のデプロイ名、リソース グループ名、Azure データ センターの場所を入力し、次のコマンドを実行します。

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

**New-AzureResourceGroupDeployment** コマンドを実行すると、JSON ファイルの "parameters" セクションのパラメーター値を指定するよう求められます。パラメーターの値がすべて指定されていれば、リソース グループと仮想マシンが作成されます。

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

次のような結果が表示されます。

	cmdlet New-AzureResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: newsaacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso
	VERBOSE: 10:56:59 AM - Template is valid.
	VERBOSE: 10:56:59 AM - Create template deployment 'TestDeployment'.
	VERBOSE: 10:57:08 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 10:57:11 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is running
	VERBOSE: 10:57:11 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is running
	VERBOSE: 10:57:38 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is succeeded
	VERBOSE: 10:57:40 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 10:57:45 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 10:57:45 AM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 11:01:59 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded


	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 6:02:13 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    	Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     newsaacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso
	                    windowsOSVersion  String                     2012-R2-Datacenter

	Outputs           :

これで、新しいリソース グループに MyWindowsVM という名前の新しい Windows 仮想マシンが作成されました。

## その他のリソース

[Azure リソース マネージャーにおける Azure Compute、ネットワーク、ストレージ プロバイダー](virtual-machines-azurerm-versus-azuresm.md)

[Azure リソース マネージャーの概要](resource-group-overview.md)

[Azure リソース マネージャーと PowerShell で Windows 仮想マシンを作成する](virtual-machines-create-windows-powershell-resource-manager.md)

[PowerShell と Azure サービス マネージャーで Windows 仮想マシンを作成する](virtual-machines-create-windows-powershell-service-manager.md)

[Virtual Machines のドキュメント](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Azure PowerShell のインストールおよび構成方法](install-configure-powershell.md)

<!---HONumber=July15_HO5-->