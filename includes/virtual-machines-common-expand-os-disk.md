---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: sdwheeler
ms.service: virtual-machines
ms.topic: include
ms.date: 04/18/2018
ms.author: kirpas;iainfou;sewhee
ms.custom: include file
ms.openlocfilehash: ab4d5326fc06cc8676ea93ed529315cc47651e26
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2018
ms.locfileid: "36943455"
---
## <a name="overview"></a>概要
[Azure Marketplace](https://azure.microsoft.com/marketplace/) からイメージをデプロイすることによってリソース グループに新しい仮想マシン (VM) を作成するとき、多くの場合、既定の OS ドライブは 127 GB です (一部のイメージでは既定の OS ディスク サイズが小さくなります)。 VM にデータ ディスクを追加でき (数は選択されている SKU に依存)、アプリケーションおよび CPU 多用ワークロードはこれらの追加ディスクにインストールすることが推奨されますが、次のような特定のシナリオをサポートするために OS ドライブの拡張が必要になることがあります。

1. OS ドライブにコンポーネントをインストールするレガシ アプリケーションをサポートする場合。
2. オンプレミスから OS ドライブの大きい物理 PC または仮想マシンを移行する場合。

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して 2 種類のデプロイメント モデルがあります。リソース マネージャー デプロイメント モデルとクラシック デプロイメント モデルです。 この記事では、リソース マネージャー モデルの使用について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。
> 
> 
> [!WARNING]
> Azure Virtual Machine の OS ディスクのサイズを変更すると、Azure Virtual Machine が再起動します。
>

## <a name="resize-the-os-drive"></a>OS ドライブのサイズ変更
この記事では、 [Azure Powershell](/powershell/azureps-cmdlets-docs)のリソース マネージャー モジュールを使用して、OS ドライブのサイズを変更します。 ここでは、非管理対象ディスクと管理ディスクの両方について、OS ドライブのサイズを変更する方法を示します。これは、これらのディスク タイプでサイズの変更方法が異なるためです。

### <a name="for-resizing-unmanaged-disks"></a>非管理対象ディスクのサイズ変更:

管理者モードで Powershell ISE または Powershell ウィンドウを開き、次の手順に従います。

1. リソース管理モードで Microsoft Azure アカウントにサインインし、次のようにサブスクリプションを選択します。
   
   ```Powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. リソース グループ名と VM 名を次のように設定します。
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. 次のように、VM への参照を取得します。
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. 次のように、ディスクのサイズを変更する前に VM を停止します。
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. 次に、 非管理対象 OS ディスクのサイズを目的の値に設定し、次のように VM を更新します。
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > 新しいサイズには、既存のディスク サイズより大きい値を指定する必要があります。 OS ディスクで許可される最大値は、2,048 GB です  (VHD BLOB はこのサイズよりも大きくすることができます。ただし、OS の動作に使用できるのは、最初の 2,048 GB の領域のみです)。
   > 
   > 
6. VM の更新には数秒かかる可能性があります。 コマンドの実行が完了した後、VM を再起動します。
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

### <a name="for-resizing-managed-disks"></a>管理ディスクのサイズ変更:

管理者モードで Powershell ISE または Powershell ウィンドウを開き、次の手順に従います。

1. リソース管理モードで Microsoft Azure アカウントにサインインし、次のようにサブスクリプションを選択します。
   
   ```Powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. リソース グループ名と VM 名を次のように設定します。
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. 次のように、VM への参照を取得します。
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. 次のように、ディスクのサイズを変更する前に VM を停止します。
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. 管理 OS ディスクへの参照を取得します。 管理 OS ディスクのサイズを目的の値に設定し、次のようにディスクを更新します。
   
   ```Powershell
   $disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > 新しいサイズには、既存のディスク サイズより大きい値を指定する必要があります。 OS ディスクで許可される最大値は、2,048 GB です  (VHD BLOB はこのサイズよりも大きくすることができます。ただし、OS の動作に使用できるのは、最初の 2,048 GB の領域のみです)。
   > 
   > 
6. VM の更新には数秒かかる可能性があります。 コマンドの実行が完了した後、VM を再起動します。
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

これで終了です。 VM に RDP で接続し、[コンピューターの管理] \(または [ディスクの管理]) を開いて、新しく割り当てた領域を使用してドライブを拡張します。

## <a name="summary"></a>まとめ
この記事では、Powershell の Azure リソース マネージャー モジュールを使用して、IaaS 仮想マシンの OS ドライブを拡張しました。 参考のために、非管理対象ディスクと管理ディスクの両方について完全なスクリプトを以下に示します。

非管理対象ディスク:

```Powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
管理ディスク:

```Powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRMVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="for-resizing-data-disks"></a>データ ディスクのサイズを変更する場合
この記事では VM の非管理対象および管理 OS ディスクの拡張に主に重点を置きましたが、作成したスクリプトは、VM に接続されたデータ ディスクの拡張にも使用できます。 たとえば、VM に接続されている最初のデータ ディスクを拡張するには、```StorageProfile``` の ```OSDisk``` オブジェクトを ```DataDisks``` 配列に置き換え、数値インデックスを使用して接続されている最初のデータ ディスクへの参照を取得します。次に例を示します。

非管理対象ディスク:
```Powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```
管理ディスク:
```Powershell
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

同様に、上のようにインデックスを使用して、または以下に示すようにディスクの ```Name``` プロパティを使用して、VM に接続されている他のデータ ディスクを参照できます。

非管理対象ディスク:
```Powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```
管理ディスク:
```Powershell
(Get-AzureRmDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

Azure Resource Manager VM にディスクを接続する方法については、こちらの[記事](../articles/virtual-machines/windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)を参照してください。

