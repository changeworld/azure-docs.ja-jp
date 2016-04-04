<properties
	pageTitle="Azure リソース マネージャー VM の管理 | Microsoft Azure"
	description="Azure リソース マネージャー テンプレートと PowerShell を使用して仮想マシンを管理します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="davidmu"/>

# Azure リソース マネージャーと PowerShell を使用した仮想マシンの管理

> [AZURE.SELECTOR]
- [PowerShell](virtual-machines-windows-ps-manage.md)
- [CLI](virtual-machines-linux-cli-deploy-templates.md)

<br>


Azure PowerShell とリソース マネージャー テンプレートを使用すると、Microsoft Azure でリソースを管理する際に高い処理能力と柔軟性が得られます。この記事にあるタスクを使用して、仮想マシンのリソースを管理できます。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル。

次のタスクでは、PowerShell のみを使用します。

- [仮想マシンに関する情報の表示](#displayvm)
- [仮想マシンの起動](#start)
- [仮想マシンの停止](#stop)
- [仮想マシンの再起動](#restart)
- [仮想マシンの削除](#delete)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## <a id="displayvm"></a>仮想マシンに関する情報の表示

次のコマンドで、*resource group name* を、仮想マシンが含まれているリソース グループの名前で置き換え、*VM name* を仮想マシンの名前で置き換えた後、このコマンドを実行します。

	Get-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

次のような結果が返されます。


	ResourceGroupName        : rg1
	Id                       : /subscriptions/{subscription-id}/resourceGroups/
															rg1/providers/Microsoft.Compute/virtualMachines/vm1
	Name                     : vm1
	Type                     : Microsoft.Azure.Management.Compute.Models.VirtualMachineGetResponse
	Location                 : westus
	Tags                     : {}
	AvailabilitySetReference : null
	Extensions               : []
	HardwareProfile          :  {
																"VirtualMachineSize": "Standard_D1"
															}
	InstanceView             : null
	Location                 : westus
	Name                     : vm1
	NetworkProfile           :  {
																"NetworkInterfaces": [
																	{
																		"Primary": null,
																		"ReferenceUri": "/subscriptions/{subscription-id}/resourceGroups/
																		rg1/providers/Microsoft.Network/networkInterfaces/nc1"
																	}
																]
															}
	OSProfile                :  {
																"AdminPassword": null,
																"AdminUsername": "WinAdmin1",
																"ComputerName": "vm1",
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
	StorageProfile           : 	{
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
																		"Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
																	}
																}
															}
	DataDiskNames            :  {}
	NetworkInterfaceIDs      : { /subscriptions/{subscription-id}/resourceGroups/
																rg1/providers/Microsoft.Network/networkInterfaces/nc1}

このタスクが実行されているビデオを参照する場合は、次をご覧ください。

[AZURE.VIDEO displaying-information-about-a-virtual-machine-in-microsoft-azure-with-powershell]

## <a id="start"></a>仮想マシンの起動

次のコマンドで、*resource group name* を、仮想マシンが含まれているリソース グループの名前で置き換え、*VM name* を仮想マシンの名前で置き換えた後、このコマンドを実行します。

	Start-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

次のような結果が返されます。

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : 06935ddf-6e89-48d2-b46a-229493e3e9d1
	Output              :
	Error               :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	EndTime             : 4/28/2015 11:11:41 AM -07:00
	TrackingOperationId : c1aa0a70-4f4f-4d6c-a8ac-7ea35c004ce0

このタスクが実行されているビデオを参照する場合は、次をご覧ください。

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="stop"></a>仮想マシンの停止

次のコマンドで、*resource group name* を、仮想マシンが含まれているリソース グループの名前で置き換え、*VM name* を仮想マシンの名前で置き換えた後、このコマンドを実行します。

	Stop-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

次のように確認が求められます。

	Virtual machine stopping operation
	This cmdlet will stop the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

次のような結果が返されます。

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : aac41de1-b85d-4429-9a3d-040b922d2e6d
	Output              :
	Error               :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	EndTime             : 4/28/2015 11:11:41 AM -07:00
	TrackingOperationId : e1705973-d266-467e-8655-920016145347

このタスクが実行されているビデオを参照する場合は、次をご覧ください。

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="restart"></a>仮想マシンの再起動

次のコマンドで、*resource group name* を、仮想マシンが含まれているリソース グループの名前で置き換え、*VM name* を仮想マシンの名前で置き換えた後、このコマンドを実行します。

	Restart-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

次のような結果が返されます。

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : 4b05891c-fdff-4c9a-89ca-e4f1d7691aed
	Output              :
	Error               :
	StartTime           : 1/5/2016 12:06:53 PM -08:00
	EndTime             : 1/5/2016 12:06:54 PM -08:00
	TrackingOperationId : 5aeeab89-45ab-41b9-84ef-9e9a7e732207


このタスクが実行されているビデオを参照する場合は、次をご覧ください。

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="delete"></a>仮想マシンの削除

次のコマンドで、*resource group name* を、仮想マシンが含まれているリソース グループの名前で置き換え、*VM name* を仮想マシンの名前で置き換えた後、このコマンドを実行します。

	Remove-AzureRmVM -ResourceGroupName "resource group name" –Name "VM name"

> [AZURE.NOTE] **-Force** パラメーターを使用して確認プロンプトをスキップできます。

-Force パラメーターを使用していない場合は、次のように確認が求められます。

	Virtual machine removal operation
	This cmdlet will remove the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

次のような結果が返されます。

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : 2d723b40-ce1f-4b11-a603-dc659a13b6f0
	Output              :
	Error               :
	StartTime           : 1/5/2016 12:10:28 PM -08:00
	EndTime             : 1/5/2016 12:12:12 PM -08:00
	TrackingOperationId : d138ab29-83bf-4948-9d13-dab87db1a639

このタスクが実行されているビデオを参照する場合は、次をご覧ください。

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

<!---HONumber=AcomDC_0323_2016-->