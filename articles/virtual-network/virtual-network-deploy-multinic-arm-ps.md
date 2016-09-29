<properties
   pageTitle="リソース マネージャーでの PowerShell を使用した複数 NIC VM のデプロイ | Microsoft Azure"
   description="リソース マネージャーで PowerShell を使用して、複数の NIC を持つ VM をデプロイする方法を学習します"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
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
   ms.date="02/02/2016"
   ms.author="jdial" />

#PowerShell を使用した複数の NIC VM のデプロイ

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-deploy-multinic-classic-ps.md)。

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

現時点では、NIC が 1 つの VM と、NIC が複数ある VM を、同じ可用性セットに含めることはできません。したがって、バックエンド サーバーは、他のすべてのコンポーネントと異なるリソース グループで実装する必要があります。以下の手順では、メイン リソース グループとして *IaaSStory* という名前のリソース グループを使用し、バックエンド サーバーとして *IaaSStory-BackEnd* を使用します。

## 前提条件

バックエンド サーバーをデプロイするには、このシナリオで必要なすべてのリソースを含むメイン リソース グループをデプロイする必要があります。これらのリソースをデプロイするには、以下の手順に従います。

1. [テンプレート ページ](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)に移動します。
2. テンプレート ページの **[親リソース グループ]** の右側にある **[Azure へのデプロイ]** をクリックします。
3. 必要に応じて、パラメーター値を変更し、Azure プレビュー ポータル内の手順に従ってリソース グループをデプロイします。

> [AZURE.IMPORTANT] ストレージ アカウント名が一意であることを確認してください。Azure では重複するストレージ アカウント名を使用できません。

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## バックエンド VM のデプロイ

バックエンド VM は、以下にリストしたリソースの作成に依存します。

- **データ ディスクのストレージ アカウント**。パフォーマンスを高めるために、データベース サーバー上のデータ ディスクはソリッド ステート ドライブ (SSD) テクノロジーを使用します。これには、Premium Storage アカウントが必要です。デプロイする Azure の場所が Premium Storage をサポートすることを確認してください。
- **NIC**。各 VM には 2 つの NIC があり、1 つはデータベース アクセス用で、もう 1 つは管理用です。
- **可用性セット**。メンテナンス中に少なくとも 1 つの VM が稼働し、実行されているようにするためには、すべてのデータベース サーバーを単一の可用性セットに追加します。

### 手順 1 - スクリプトの開始

使用するすべての PowerShell スクリプトは、[ここ](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/arm/virtual-network-deploy-multinic-arm-ps.ps1)からダウンロードできます。以下の手順に従って、ご使用の環境で機能するようにスクリプトを変更します。

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. 上記の[前提条件](#Prerequisites)でデプロイした既存のリソース グループに基づいて、以下の変数の値を変更します。

		$existingRGName        = "IaaSStory"
		$location              = "West US"
		$vnetName              = "WTestVNet"
		$backendSubnetName     = "BackEnd"
		$remoteAccessNSGName   = "NSG-RemoteAccess"
		$stdStorageAccountName = "wtestvnetstoragestd"

2. バックエンド デプロイメントに使用する値に基づいて、以下の変数の値を変更します。

		$backendRGName         = "IaaSStory-Backend"
		$prmStorageAccountName = "wtestvnetstorageprm"
		$avSetName             = "ASDB"
		$vmSize                = "Standard_DS3"
		$publisher             = "MicrosoftSQLServer"
		$offer                 = "SQL2014SP1-WS2012R2"
		$sku                   = "Standard"
		$version               = "latest"
		$vmNamePrefix          = "DB"
		$osDiskPrefix          = "osdiskdb"
		$dataDiskPrefix        = "datadisk"
		$diskSize	           = "120"	
		$nicNamePrefix         = "NICDB"
		$ipAddressPrefix       = "192.168.2."
		$numberOfVMs           = 2

3. デプロイメントに必要な既存のリソースを取得します。

		$vnet                  = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $existingRGName
		$backendSubnet         = $vnet.Subnets|?{$_.Name -eq $backendSubnetName}
		$remoteAccessNSG       = Get-AzureRmNetworkSecurityGroup -Name $remoteAccessNSGName -ResourceGroupName $existingRGName
		$stdStorageAccount     = Get-AzureRmStorageAccount -Name $stdStorageAccountName -ResourceGroupName $existingRGName

### 手順 2 - VM 用に必要なリソースの作成

新しいリソース グループ、データ ディスク用のストレージ アカウント、およびすべての VM 用の可用性セットを作成する必要があります。VM ごとに、ローカル管理者アカウントの資格情報も必要です。これらのリソースを作成するには、次の手順を実行します。

1. 新しいリソース グループを作成します。

		New-AzureRmResourceGroup -Name $backendRGName -Location $location

2. 上記で作成したリソース グループ内に、新しい Premium Storage アカウントを作成します。

		$prmStorageAccount = New-AzureRmStorageAccount -Name $prmStorageAccountName `
			-ResourceGroupName $backendRGName -Type Premium_LRS -Location $location

3. 新しい可用性セットを作成します。

		$avSet = New-AzureRmAvailabilitySet -Name $avSetName -ResourceGroupName $backendRGName -Location $location

4. VM ごとに、使用するローカル管理者アカウントの資格情報を取得します。

		$cred = Get-Credential -Message "Type the name and password for the local administrator account."

### ステップ 3 - NIC とバックエンド VM の作成

ループを使用して必要な数の VM を作成し、必要な NIC と VM をループ内に作成します。NIC と VM を作成するには、次の手順を実行します。

1. `$numberOfVMs` 変数の値に基づいて、`for` ループを開始して、1 つの VM と 2 つの NIC を作成するコマンドを必要な回数だけ繰り返します。

		for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. データベース アクセスに使用する NIC を作成します。

		    $nic1Name = $nicNamePrefix + $suffixNumber + "-DA"
		    $ipAddress1 = $ipAddressPrefix + ($suffixNumber + 3)
		    $nic1 = New-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $backendRGName `
				-Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress1

3. リモート アクセスに使用する NIC を作成します。この NIC がどのようにして NSG を自身に関連付けるかに注意してください。

		    $nic2Name = $nicNamePrefix + $suffixNumber + "-RA"
		    $ipAddress2 = $ipAddressPrefix + (53 + $suffixNumber)
		    $nic2 = New-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $backendRGName `
				-Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress2 `
				-NetworkSecurityGroupId $remoteAccessNSG.Id

4. `vmConfig` オブジェクトを作成します。

		    $vmName = $vmNamePrefix + $suffixNumber
		    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id

5. VM あたり 2 つのデータ ディスクを作成します。データ ディスクが、前に作成した、Premium Storage アカウント内にあることに注意してください。

		    $dataDisk1Name = $vmName + "-" + $osDiskPrefix + "-1"    
		    $data1VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk1Name + ".vhd"
		    Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk1Name -DiskSizeInGB $diskSize `
				-VhdUri $data1VhdUri -CreateOption empty -Lun 0

		    $dataDisk2Name = $vmName + "-" + $dataDiskPrefix + "-2"    
		    $data2VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk2Name + ".vhd"
		    Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk2Name -DiskSizeInGB $diskSize `
				-VhdUri $data2VhdUri -CreateOption empty -Lun 1

6. VM で使用するオペレーティング システムとイメージを構成します。

		    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher -Offer $offer -Skus $sku -Version $version

7. 上記で作成した 2 つの NIC を `vmConfig` オブジェクトに追加します。

		    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic1.Id -Primary
		    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic2.Id

8. OS ディスクを作成し、VM を作成します。`}` で `for` ループが終了することに注意してください。

		    $osDiskName = $vmName + "-" + $osDiskSuffix
		    $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
		    $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
		    New-AzureRmVM -VM $vmConfig -ResourceGroupName $backendRGName -Location $location
		}

### 手順 4 - スクリプトの実行

ニーズに合わせてスクリプトをダウンロードおよび変更し、スクリプトを実行して複数の NIC を持つバックエンド データベース VM を作成しました。

1. スクリプトを保存し、それを **PowerShell** コマンド プロンプトまたは **PowerShell ISE** から実行します。次のように、最初の出力が表示されます。

		ResourceGroupName : IaaSStory-Backend
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		                    Actions  NotActions
		                    =======  ==========
		                    *                  

		ResourceId        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/IaaSStory-Backend

2. 数分後、資格情報プロンプトに入力して、**[OK]** をクリックします。次に示す出力は、1 つの VM を表しています。プロセス全体を完了するには、8 分間かかりました。

		ResourceGroupName            :
		Id                           :
		Name                         : DB2
		Type                         :
		Location                     :
		Tags                         :
		TagsText                     : null
		AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
		AvailabilitySetReferenceText : {
		                                 "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
		                               Microsoft.Compute/availabilitySets/ASDB"
		                               }
		Extensions                   :
		ExtensionsText               : null
		HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
		HardwareProfileText          : {
		                                 "VirtualMachineSize": "Standard_DS3"
		                               }
		InstanceView                 :
		InstanceViewText             : null
		NetworkProfile               :
		NetworkProfileText           : null
		OSProfile                    :
		OSProfileText                : null
		Plan                         :
		PlanText                     : null
		ProvisioningState            :
		StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
		StorageProfileText           : {
		                                 "DataDisks": [
		                                   {
		                                     "Lun": 0,
		                                     "Caching": null,
		                                     "CreateOption": "empty",
		                                     "DiskSizeGB": 127,
		                                     "Name": "DB2-datadisk-1",
		                                     "SourceImage": null,
		                                     "VirtualHardDisk": {
		                                       "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
		                                     }
		                                   }
		                                 ],
		                                 "ImageReference": null,
		                                 "OSDisk": null
		                               }
		DataDiskNames                : {DB2-datadisk-1}
		NetworkInterfaceIDs          :
		RequestId                    :
		StatusCode                   : 0


		ResourceGroupName            :
		Id                           :
		Name                         : DB2
		Type                         :
		Location                     :
		Tags                         :
		TagsText                     : null
		AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
		AvailabilitySetReferenceText : {
		                                 "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
		                               Microsoft.Compute/availabilitySets/ASDB"
		                               }
		Extensions                   :
		ExtensionsText               : null
		HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
		HardwareProfileText          : {
		                                 "VirtualMachineSize": "Standard_DS3"
		                               }
		InstanceView                 :
		InstanceViewText             : null
		NetworkProfile               :
		NetworkProfileText           : null
		OSProfile                    :
		OSProfileText                : null
		Plan                         :
		PlanText                     : null
		ProvisioningState            :
		StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
		StorageProfileText           : {
		                                 "DataDisks": [
		                                   {
		                                     "Lun": 0,
		                                     "Caching": null,
		                                     "CreateOption": "empty",
		                                     "DiskSizeGB": 127,
		                                     "Name": "DB2-datadisk-1",
		                                     "SourceImage": null,
		                                     "VirtualHardDisk": {
		                                       "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
		                                     }
		                                   },
		                                   {
		                                     "Lun": 1,
		                                     "Caching": null,
		                                     "CreateOption": "empty",
		                                     "DiskSizeGB": 127,
		                                     "Name": "DB2-datadisk-2",
		                                     "SourceImage": null,
		                                     "VirtualHardDisk": {
		                                       "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-2.vhd"
		                                     }
		                                   }
		                                 ],
		                                 "ImageReference": null,
		                                 "OSDisk": null
		                               }
		DataDiskNames                : {DB2-datadisk-1, DB2-datadisk-2}
		NetworkInterfaceIDs          :
		RequestId                    :
		StatusCode                   : 0


		EndTime             : 10/30/2015 9:30:03 AM -08:00
		Error               :
		Output              :
		StartTime           : 10/30/2015 9:22:54 AM -08:00
		Status              : Succeeded
		TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		StatusCode          : OK

<!---HONumber=AcomDC_0914_2016-->