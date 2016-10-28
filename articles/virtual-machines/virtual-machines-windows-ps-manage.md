<properties
	pageTitle="Resource Manager と PowerShell を使用した VM の管理 |Microsoft Azure"
	description="Azure Resource Manager と PowerShell を使用して仮想マシンを管理します。"
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
	ms.date="06/07/2016"
	ms.author="davidmu"/>

# Resource Manager と PowerShell を使用した Azure Virtual Machines の管理

## Azure PowerShell をインストールするには
 
最新バージョンの Azure PowerShell をインストールし、使用するサブスクリプションを選択し、Azure アカウントにサインインする方法については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」を参照してください。

## 変数の設定

この記事のすべてのコマンドでは、仮想マシンがあるリソース グループの名前と、管理する仮想マシンの名前が必要です。**$rgName** の値を、仮想マシンが含まれているリソース グループの名前に置き換えます。**$vmName** の値を、VM の名前に置き換えます。変数を作成します。

    $rgName = "resource-group-name"
    $vmName = "VM-name"

## 仮想マシンに関する情報の表示

仮想マシンの情報を取得します。
  
    Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName

次のような結果が返されます。

    ResourceGroupName        : rg1
    Id                       : /subscriptions/{subscription-id}/resourceGroups/
                               rg1/providers/Microsoft.Compute/virtualMachines/vm1
    Name                     : vm1
    Type                     : Microsoft.Compute/virtualMachines
    Location                 : centralus
    Tags                     : {}
    AvailabilitySetReference : {
                                  "id": "/subscriptions/{subscription-id}/resourceGroups/
                                  rg1/providers/Microsoft.Compute/availabilitySets/av1"
                               }
    Extensions               : []
    HardwareProfile          : {
                                  "vmSize": "Standard_A0"
                               }
    InstanceView             : null
    NetworkProfile           : {
                                  "networkInterfaces": [
                                    {
                                      "properties.primary": null,
                                      "id": "/subscriptions/{subscription-id}/resourceGroups/
                                      rg1/providers/Microsoft.Network/networkInterfaces/nc1"
                                    }
                                  ]
                               }
    OSProfile                : {
                                  "computerName": "vm1",
                                  "adminUsername": "myaccount1",
                                  "adminPassword": null,
                                  "customData": null,
                                  "windowsConfiguration": {
                                    "provisionVMAgent": true,
                                    "enableAutomaticUpdates": true,
                                    "timeZone": null,
                                    "additionalUnattendContents": [],
                                    "winRM": null
                                  },
                                  "linuxConfiguration": null,
                                  "secrets": []
                               }
    Plan                     : null
    ProvisioningState        : Succeeded
    StorageProfile           : {
                                  "imageReference": {
                                    "publisher": "MicrosoftWindowsServer",
                                    "offer": "WindowsServer",
                                    "sku": "2012-R2-Datacenter",
                                    "version": "latest"
                                  },
                                  "osDisk": {
                                    "osType": "Windows",
                                    "encryptionSettings": null,
                                    "name": "osdisk",
                                    "vhd": {
                                      "Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
                                    }
                                    "image": null,
                                    "caching": "ReadWrite",
                                    "createOption": "FromImage"
                                  }
                                  "dataDisks": [],
                               }
    DataDiskNames            : {}
    NetworkInterfaceIDs      : {/subscriptions/{subscription-id}/resourceGroups/
                                rg1/providers/Microsoft.Network/networkInterfaces/nc1}

## 仮想マシンの起動

仮想マシンを開始します。

    Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName

数分後、次のような結果が返されます。

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## 仮想マシンの停止

仮想マシンを停止します。

    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName

次のように確認が求められます。

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
        
仮想マシンを停止するには、「**Y**」と入力します。

数分後、次のような結果が返されます。

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## 仮想マシンの再起動

仮想マシンを再起動します。

    Restart-AzureRmVM -ResourceGroupName $rgName -Name $vmName

次のような結果が返されます。

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## 仮想マシンの削除

仮想マシンを削除します。

    Remove-AzureRmVM -ResourceGroupName $rgName –Name $vmName

> [AZURE.NOTE] **-Force** パラメーターを使用して確認プロンプトをスキップできます。

-Force パラメーターを使用していない場合は、次のように確認が求められます。

    Virtual machine removal operation
    This cmdlet will remove the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

次のような結果が返されます。

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## 仮想マシンのサイズ変更

この例では、仮想マシンのサイズを更新する方法を示します。
        
    $vmSize = "Standard_A1"
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    $vm.HardwareProfile.vmSize = $vmSize
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
    
次のような結果が返されます。

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK
                              
仮想マシンに使用できるサイズの一覧は、「[Azure の仮想マシンのサイズ](virtual-machines-windows-sizes.md)」をご覧ください。

## データ ディスクを仮想マシンに追加する

この例では、既存の仮想マシンにデータ ディスクを追加する方法を示します。

    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    Add-AzureRmVMDataDisk -VM $vm -Name "disk-name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/datadisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm

追加するディスクは初期化されていません。ディスクを初期化するには、ログインしてディスクの管理を使用します。証明書を作成したときに WinRM と証明書をインストールした場合、ディスクを初期化するためにリモート PowerShell を使用できます。また、カスタム スクリプト拡張機能を使用することができます。

    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"

スクリプト ファイルには、ディスクを初期化するために次のようなコードを含めることができます。

    $disks = Get-Disk |   Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { ([char]$_) }
    $count = 0
    $labels = @("data1","data2")

    foreach($d in $disks) {
        $driveLetter = $letters[$count].ToString()
        $d | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] `
            -Confirm:$false -Force 
        $count++
    }

## 次のステップ

デプロイに問題がある場合は、「[Azure ポータルでのリソース グループのデプロイのトラブルシューティング](../resource-manager-troubleshoot-deployments-portal.md)」をご覧ください。

<!---HONumber=AcomDC_0907_2016-->