<properties
	pageTitle="Windows VM の移動 | Microsoft Azure"
	description="Resource Manager デプロイメント モデルで Windows VM を他の Azure サブスクリプションまたはリソース グループに移動します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="cynthn"/>

	


# 他の Azure サブスクリプションまたはリソース グループへの Windows VM の移動 

この記事では、リソース グループまたはサブスクリプション間で Windows VM を移動する方法について説明します。サブスクリプション間での移動は、個人のサブスクリプションで作成した VM を、会社のサブスクリプションに移動して作業を続ける場合に便利です。

> [AZURE.NOTE] 移動の一環として新しいリソース ID が作成されます。VM を移動したら、この新しいリソース ID を使用するために、ツールやスクリプトを更新する必要があります。


[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]


## PowerShell を使用した VM の移動

仮想マシンを他のリソース グループに移動するには、依存リソースも必ずすべて移動する必要があります。Move-AzureRMResource コマンドレットを使用するには、リソース名とリソースの種類が必要です。これは両方とも、Find-AzureRMResource コマンドレットで取得できます。

	Find-AzureRMResource -ResourceGroupNameContains "<sourceResourceGroupName>"
	

VM を移動するには、複数のリソースを移動する必要があります。リソースごとに個別の変数を作成するだけで、その変数を一覧表示できます。この例には、VM の基本的なリソースのほとんどが含まれていますが、必要に応じて追加することもできます。

	$sourceRG = "<sourceResourceGroupName>"
	$destinationRG = "<destinationResourceGroupName>"
    
	$vm = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Compute/virtualMachines" -ResourceName "<vmName>"
    $storageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<storageAccountName>"
	$diagStorageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<diagnosticStorageAccountName>"
	$vNet = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/virtualNetworks" -ResourceName "<vNetName>"
	$nic = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkInterfaces" -ResourceName "<nicName>"
	$ip = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/publicIPAddresses" -ResourceName "<ipName>"
	$nsg = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkSecurityGroups" -ResourceName "<nsgName>"
	
	Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId

リソースを他のサブスクリプションに移動するには、**-DestinationSubscriptionId** パラメーターを含めます。

	Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId



指定したリソースを移動することの確認を求められます。「**Y**」を入力して、リソースの移動を確定します。

  
## 次のステップ

リソース グループとサブスクリプションの間でさまざまな種類のリソースを移動できます。詳細については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../resource-group-move-resources.md)」を参照してください。

<!---HONumber=AcomDC_0810_2016-->