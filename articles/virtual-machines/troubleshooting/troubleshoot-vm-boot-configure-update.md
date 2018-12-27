---
title: VM の起動が停止し、"Windows の準備をしています。 コンピューターの電源を切らないでください。" が表示される (Azure 内) | Microsoft Docs
description: VM の起動が停止し、"Windows の準備をしています。 コンピューターの電源を切らないでください" が表示される問題をトラブルシューティングするステップを紹介します。
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: 2bcdb2b458327a5e87dc36e4a5f50f0ac46bf96a
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621037"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>VM の起動が停止し、"Windows の準備をしています。 コンピューターの電源を切らないでください。" が表示される (Azure 内)

この記事は、仮想マシン (VM) が起動中の "Windows の準備をしています。 コンピューターの電源を切らないでください。" が表示される 段階で停止する場合の問題を解決する際に役立ちます。

## <a name="symptoms"></a>現象

**ブート診断**を使用して VM のスクリーンショットを取得するときに、オペレーティング システムの起動が完了しません。 さらに、VM で **"Windows の準備をしています。コンピューターの電源を切らないでください。"** という メッセージが表示されます。

![メッセージの例](./media/troubleshoot-vm-configure-update-boot/message1.png)

![メッセージの例](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>原因

この問題は通常、構成の変更後にサーバーが最終リブートを行っている場合に発生します。 構成の変更は、Windows の更新プログラムまたはサーバーのロール/機能の変更によって初期化されることがあります。 Windows Update の場合、更新のサイズが大きいと、オペレーティング システムが変更を再構成するまでに時間がかかります。

## <a name="back-up-the-os-disk"></a>OS ディスクのバックアップ

この問題の修正を試みる前に、OS ディスクのバックアップを取ってください。

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>暗号化されたディスクがある VM の場合は、最初にディスクのロックを解除する必要があります

VM が暗号化された VM であるかどうかを確認します。 そのためには、次の手順に従います。

1. Azure portal で VM を開き、ディスクを参照します。

2. [暗号化] という列があり、この列によって、暗号化が有効にされているかどうかがわかります。

OS ディスクが暗号化されている場合は、暗号化されたディスクのロックを解除します。 そのためには、次の手順に従います。

1. 影響を受ける VM と同じリソース グループ、ストレージ アカウント、場所に復旧 VM を作成します。

2. Azure portal で、影響を受ける VM を削除し、ディスクは保持します。

3. PowerShell を管理者として実行します。

4. 次のコマンドレットを実行し、シークレット名を取得します。

    ```Powershell
    Login-AzureRmAccount
 
    $vmName = “VirtualMachineName”
    $vault = “AzureKeyVaultName”
 
    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq “C:\”) -and ($_.ContentType -eq ‘BEK‘)}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq ‘BEK’)}
    ```

5. シークレット名を取得した後、PowerShell で次のコマンドを実行します。

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. Base64 でエンコードされた値をバイトに変換し、出力をファイルに書き込みます。 

    > [!Note]
    > USB ロック解除オプションを使用する場合、BEK ファイル名は元の BEK GUID と一致する必要があります。 また、次の手順を実行する前に、C ドライブに "BEK" という名前のフォルダーを作成する必要もあります。
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. PC に BEK ファイルが作成された後、ロックされた OS ディスクが接続されている復旧 VM にファイルをコピーします。 BEK ファイルの場所を使用して、次のコマンドを実行します。

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **オプション**: シナリオによっては、次のコマンドを使用してディスクの暗号化解除が必要になる場合があります。
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > ここでは、暗号化するディスクが F：であることを前提としています。

8. ログを収集する必要がある場合、パス **DRIVE LETTER:\Windows\System32\winevt\Logs** に移動します。

9. 復旧マシンからドライブをデタッチします。

### <a name="create-a-snapshot"></a>スナップショットの作成

スナップショットを作成するには、「[ディスクのスナップショットの作成](..\windows\snapshot-copy-managed-disk.md)」の手順に従います。

## <a name="collect-an-os-memory-dump"></a>OS のメモリ ダンプの収集

VM の構成時に停止したときに OS ダンプを収集するには、「[OS ダンプ ファイルを収集する](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file)」セクションの手順を使用します。

## <a name="contact-microsoft-support"></a>Microsoft サポートに問い合わせる

ダンプ ファイルを収集した後、根本原因の分析を [Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)にご依頼ください。


## <a name="rebuild-the-vm-using-powershell"></a>PowerShell の使用による VM の再構築

メモリ ダンプ ファイルを収集した後、次の手順に従って VM を再構築します。

**非マネージド ディスクの場合**

```PowerShell
# To login to Azure Resource Manager
Login-AzureRmAccount

# To view all subscriptions for your account
Get-AzureRmSubscription

# To select a default subscription for your current session
Get-AzureRmSubscription –SubscriptionID “SubscriptionID” | Select-AzureRmSubscription

$rgname = "RGname"
$loc = "Location"
$vmsize = "VmSize"
$vmname = "VmName"
$vm = New-AzureRmVMConfig -VMName $vmname -VMSize $vmsize;

$nic = Get-AzureRmNetworkInterface -Name ("NicName") -ResourceGroupName $rgname;
$nicId = $nic.Id;

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId;

$osDiskName = "OSdiskName"
$osDiskVhdUri = "OSdiskURI"

$vm = Set-AzureRmVMOSDisk -VM $vm -VhdUri $osDiskVhdUri -name $osDiskName -CreateOption attach -Windows

New-AzureRmVM -ResourceGroupName $rgname -Location $loc -VM $vm -Verbose
```

**マネージド ディスクの場合**

```PowerShell
# To login to Azure Resource Manager
Login-AzureRmAccount

# To view all subscriptions for your account
Get-AzureRmSubscription

# To select a default subscription for your current session
Get-AzureRmSubscription –SubscriptionID "SubscriptionID" | Select-AzureRmSubscription

#Fill in all variables
$subid = "SubscriptionID"
$rgName = "ResourceGroupName";
$loc = "Location";
$vmSize = "VmSize";
$vmName = "VmName";
$nic1Name = "FirstNetworkInterfaceName";
#$nic2Name = "SecondNetworkInterfaceName";
$avName = "AvailabilitySetName";
$osDiskName = "OsDiskName";
$DataDiskName = "DataDiskName"

#This can be found by selecting the Managed Disks you wish you use in the Azure Portal if the format below doesn't match
$osDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$osDiskName";
$dataDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$DataDiskName";

$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availability Set
#$avSet = Get-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName;
#$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additional Data Disk
#Add-AzureRmVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzureRmVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```
