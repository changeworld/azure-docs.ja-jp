<properties
	pageTitle="Azure リソース マネージャー VM の管理 | Microsoft Azure"
	description="Azure リソース マネージャー テンプレートと PowerShell を使用して仮想マシンを管理します。"
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
	ms.date="10/08/2015"
	ms.author="davidmu"/>

# Azure リソース マネージャーと PowerShell を使用した仮想マシンの管理

> [AZURE.SELECTOR]
- [Azure preview portal](virtual-machines-windows-tutorial.md)
- [PowerShell - Windows](virtual-machines-deploy-rmtemplates-powershell.md)
- [Azure CLI](virtual-machines-deploy-rmtemplates-azure-cli.md)

Azure PowerShell とリソース マネージャー テンプレートを使用すると、Microsoft Azure でリソースを管理する際に高い処理能力と柔軟性が得られます。この記事にあるタスクを使用すると、仮想マシンのリソースを作成して管理できます。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-tutorial-classic-portal.md)。

次のタスクでは、リソース マネージャー テンプレートと PowerShell を使用します。

- [仮想マシンの作成](#windowsvm)
- [専用ディスクを使用した仮想マシンの作成](#customvm)
- [外部ロード バランサーを使用した仮想ネットワーク内での複数の仮想マシンの作成](#multivm)

次のタスクでは、PowerShell のみを使用します。

- [リソース グループの削除](#removerg)
- [仮想マシンに関する情報の表示](#displayvm)
- [仮想マシンの起動](#start)
- [仮想マシンの停止](#stop)
- [仮想マシンの再起動](#restart)
- [仮想マシンの削除](#delete)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## Azure リソース マネージャー テンプレートとリソース グループ

この記事にあるいくつかのタスクでは、Azure リソース マネージャー テンプレートと Powershell を使用して、Azure 仮想マシンのデプロイと管理を自動的に行う方法について説明します。

Microsoft Azure で実行されているほとんどのアプリケーションは、異なる種類のクラウド リソースの組み合わせ (1 つ以上の仮想マシンとストレージ アカウント、SQL データベース、仮想ネットワークなど) から構築されます。Azure リソース マネージャー テンプレートにより、リソースと関連する構成およびデプロイ パラメーターの JSON 記述を使用して、これらのさまざまなリソースをまとめて管理できるようになります。

JSON ベースのリソース テンプレートを定義すると、PowerShell コマンドでそのテンプレートを使用して、定義したリソースを Azure にデプロイできます。これらのコマンドは、PowerShell コマンド シェルで個別に実行することも、その他のオートメーション ロジックを含むスクリプトに統合することもできます。

Azure リソース マネージャー テンプレートを使用して作成するリソースは、新規または既存の *Azure リソース グループ*にデプロイされます。リソース グループでは、デプロイした複数のリソースを論理グループとしてまとめて管理できます。つまり、グループやアプリケーションのライフサイクル全体を管理できます。

テンプレートの作成に興味がある場合は、「[Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。

### リソース グループの作成

リソースを作成するタスクでは、既存のリソース グループがない場合は必要となります。

次のコマンドで、*リソース グループ名* を新しいリソース グループの名前で置き換え、*Azure location* を、リソースを配置する Azure データセンターの場所で置き換えた後、このコマンドを実行します。

	New-AzureRmResourceGroup -Name "resource group name" -Location "Azure location"

## <a id="windowsvm"></a>タスク: 仮想マシンの作成

このタスクでは、テンプレート ギャラリーのテンプレートを使用します。テンプレートの詳細については、「[Deploy a simple Windows VM in West US (米国西部での簡単な Windows VM のデプロイ)](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/)」をご覧ください。

![](./media/virtual-machines-deploy-rmtemplates-powershell/windowsvm.png)

次のコマンドで、*deployment name* をそのデプロイメントに使用する名前で置き換え、*リソース グループ名* を既存のリソース グループの名前で置き換えた後、このコマンドを実行します。

	New-AzureRmResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"

次に例を示します。

	New-AzureRmResourceGroupDeployment -Name "TestDeployment" -ResourceGroupName "TestRG" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"

JSON ファイルの **parameters** セクションのパラメーター値を指定するように求められます。

	cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso

次のような結果が返されます。

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
	                    newStorageAccountName  String                     saacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso9875
	                    windowsOSVersion  String                     2012-R2-Datacenter

	Outputs           :

このタスクが実行されているビデオを参照する場合は、次をご覧ください。

[AZURE.VIDEO deploy-a-windows-virtual-machine-with-azure-resource-manager-templates-and-powershell]

## <a id="customvm"></a>タスク: 専用ディスクを使用した仮想マシンの作成

このタスクでは、テンプレート ギャラリーのテンプレートを使用します。テンプレートの詳細については、「[Create a VM from a specialized VHD disk (専用 VHD ディスクからの VM の作成)](https://azure.microsoft.com/documentation/templates/201-vm-from-specialized-vhd/)」をご覧ください。

次のコマンドで、*deployment name* をそのデプロイメントに使用する名前で置き換え、*リソース グループ名* を既存のリソース グループの名前で置き換えた後、このコマンドを実行します。

	New-AzureRmResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"

次に例を示します。

	New-AzureRmResourceGroupDeployment -Name "TestDeployment" -ResourceGroupName "TestRG" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"

JSON ファイルの **parameters** セクションのパラメーター値を指定するように求められます。

	cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	osDiskVhdUri: http://saacct.blob.core.windows.net/vhds/osdiskforwindows.vhd
	osType: windows
	location: West US
	vmSize: Standard_A3
	...

> [AZURE.NOTE]上記の例では、saacct ストレージ アカウントに存在する vhd ファイルが使用されています。ディスクの名前は、テンプレートのパラメーターとして指定されていました。

このタスクが実行されているビデオを参照する場合は、次をご覧ください。

[AZURE.VIDEO create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell]

## <a id="multivm"></a>タスク: 外部ロード バランサーを使用した仮想ネットワーク内での複数の仮想マシンの作成

このタスクでは、テンプレート ギャラリーのテンプレートを使用します。テンプレートの詳細については、「[Create a VM from a specialized VHD disk (専用 VHD ディスクからの VM の作成)](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/)」をご覧ください。

![](./media/virtual-machines-deploy-rmtemplates-powershell/multivmextlb.png)

次のコマンドで、*deployment name* をそのデプロイに使用する名前に置き換え、*リソース グループ名* を既存のリソース グループの名前に置き換えた後、このコマンドを実行します。

	New-AzureRmResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json"

JSON ファイルの **parameters** セクションのパラメーター値を指定するように求められます。

	cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	adminUserName: WebAdmin1
	adminPassword: *******
	dnsNameforLBIP: web07
	backendPort: 80
	vmNamePrefix: WEBFARM
	...

このタスクが実行されているビデオを参照する場合は、次をご覧ください。

[AZURE.VIDEO deploy-multi-vm-app-with-a-virtual-network-and-load-balancer-in-azure-resource-manager]

## <a id="removerg"></a>タスク: リソース グループの削除

次のコマンドの *リソース グループ名* を、削除するリソース グループの名前で置き換えた後、このコマンドを実行します。

	Remove-AzureRmResourceGroup  -Name "resource group name"

> [AZURE.NOTE]**-Force** パラメーターを使用して確認プロンプトをスキップできます。

-Force パラメーターを使用していない場合は、次のように確認が求められます。

	Confirm
	Are you sure you want to remove resource group 'BuildRG'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

このタスクが実行されているビデオを参照する場合は、次をご覧ください。

[AZURE.VIDEO removing-a-resource-group-in-azure]

## <a id="displayvm"></a>タスク: 仮想マシンに関する情報の表示

次のコマンドで、*リソース グループ名* を、仮想マシンが含まれているリソース グループの名前で置き換え、*VM name* を仮想マシンの名前で置き換えた後、このコマンドを実行します。

	Get-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

次のような結果が返されます。

	AvailabilitySetReference : null
	Extensions               : []
	HardwareProfile          : {
	                             "VirtualMachineSize": "Standard_D1"
	                           }
	Id                       : /subscriptions/{subscription-id}/resourceGroups/BuildRG/providers/Microso
	                           ft.Compute/virtualMachines/MyWindowsVM
	InstanceView             : null
	Location                 : westus
	Name                     : MyWindowsVM
	NetworkProfile           : {
	                             "NetworkInterfaces": [
	                               {
	                                 "Primary": null,
	                                 "ReferenceUri": "/subscriptions/{subscription-id}/resourceGroups/Bu
	                           ildRG/providers/Microsoft.Network/networkInterfaces/myVMNic"
	                               }
	                             ]
	                           }
	OSProfile                : {
	                             "AdminPassword": null,
	                             "AdminUsername": "WinAdmin1",
	                             "ComputerName": "MyWindowsVM",
	                             "CustomData": null,
	                             "LinuxConfiguration": null,
	                             "Secrets": [],
	                             "WindowsConfiguration": {
	                               "AdditionalUnattendContents": [],
	                               "EnableAutomaticUpdates": true,
	                               "ProvisionVMAgent": true,
	                               "TimeZone": null,
	                               "WinRMConfiguration": null
	                             }
	                           }
	Plan                     : null
	ProvisioningState        : Succeeded
	StorageProfile           : {
	                             "DataDisks": [],
	                             "ImageReference": {
	                               "Offer": "WindowsServer",
	                               "Publisher": "MicrosoftWindowsServer",
	                               "Sku": "2012-R2-Datacenter",
	                               "Version": "latest"
	                             },
	                             "OSDisk": {
	                               "OperatingSystemType": "Windows",
	                               "Caching": "ReadWrite",
	                               "CreateOption": "FromImage",
	                               "Name": "osdisk",
	                               "SourceImage": null,
	                               "VirtualHardDisk": {
	                                 "Uri": "http://saacct.blob.core.windows.net/vhds/osdiskforwindowssimple.vhd"
	                               }
	                             },
	                             "SourceImage": null
	                           }
	Tags                     : {}
	Type                     : Microsoft.Compute/virtualMachines

このタスクが実行されているビデオを参照する場合は、次をご覧ください。

[AZURE.VIDEO displaying-information-about-a-virtual-machine-in-microsoft-azure-with-powershell]

## <a id="start"></a>タスク: 仮想マシンの起動

次のコマンドで、*リソース グループ名* を、仮想マシンが含まれているリソース グループの名前で置き換え、*VM name* を仮想マシンの名前で置き換えた後、このコマンドを実行します。

	Start-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

次のような結果が返されます。

	EndTime             : 4/28/2015 11:11:41 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	Status              : Succeeded
	TrackingOperationId : e1705973-d266-467e-8655-920016145347
	RequestId           : aac41de1-b85d-4429-9a3d-040b922d2e6d
	StatusCode          : OK

このタスクが実行されているビデオを参照する場合は、次をご覧ください。

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="stop"></a>タスク: 仮想マシンの停止

次のコマンドで、*リソース グループ名* を、仮想マシンが含まれているリソース グループの名前で置き換え、*VM name* を仮想マシンの名前で置き換えた後、このコマンドを実行します。

	Stop-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

次のように確認が求められます。

	Virtual machine stopping operation
	This cmdlet will stop the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

次のような結果が返されます。

	EndTime             : 4/28/2015 11:09:08 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:06:55 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 0c94dc74-c553-412c-a187-108bdb29657e
	RequestId           : 5cc9ddba-0643-4b5e-82b6-287b321394ee
	StatusCode          : OK

このタスクが実行されているビデオを参照する場合は、次をご覧ください。

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="restart"></a>タスク: 仮想マシンの再起動

次のコマンドで、*リソース グループ名* を、仮想マシンが含まれているリソース グループの名前で置き換え、*VM name* を仮想マシンの名前で置き換えた後、このコマンドを実行します。

	Restart-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

次のような結果が返されます。

	EndTime             : 4/28/2015 11:16:26 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:16:25 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 390571e0-c804-43ce-88c5-f98e0feb588e
	RequestId           : 7dac33e3-0164-4a08-be33-96205284cb0b
	StatusCode          : OK

このタスクが実行されているビデオを参照する場合は、次をご覧ください。

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="delete"></a>タスク: 仮想マシンの削除

次のコマンドで、*リソース グループ名* を、仮想マシンが含まれているリソース グループの名前で置き換え、*VM name* を仮想マシンの名前で置き換えた後、このコマンドを実行します。

	Remove-AzureRmVM -ResourceGroupName "resource group name" –Name "VM name"

> [AZURE.NOTE]**-Force** パラメーターを使用して確認プロンプトをスキップできます。

-Force パラメーターを使用していない場合は、次のように確認が求められます。

	Virtual machine removal operation
	This cmdlet will remove the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

次のような結果が返されます。

	EndTime             : 4/28/2015 11:21:55 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:20:13 AM -07:00
	Status              : Succeeded
	TrackingOperationId : f74fad9e-f6bc-46ae-82b1-bfad3952aa44
	RequestId           : 6a30d2e0-63ca-43cf-975b-058631e048e7
	StatusCode          : OK

このタスクが実行されているビデオを参照する場合は、次をご覧ください。

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## その他のリソース
[Azure のクイック スタート テンプレート](http://azure.microsoft.com/documentation/templates/)と[アプリケーション フレームワーク](virtual-machines-app-frameworks.md)

[Azure リソース マネージャーにおける Azure Compute、ネットワーク、ストレージ プロバイダー](virtual-machines-azurerm-versus-azuresm.md)

[Azure リソース マネージャーの概要](resource-group-overview.md)

[Virtual Machines のドキュメント](http://azure.microsoft.com/documentation/services/virtual-machines/)

<!---HONumber=AcomDC_1125_2015-->