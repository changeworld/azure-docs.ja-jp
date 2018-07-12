---
title: チュートリアル - Azure PowerShell を使用して Azure ディスクを管理する | Microsoft Docs
description: このチュートリアルでは、Azure PowerShell を使用して、仮想マシン用の Azure ディスクの作成と管理を行う方法について説明します
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 67e95ea4b816e1c21b5a43233360c7fa89f43a7a
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929306"
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>チュートリアル - Azure PowerShell を使用して Azure ディスクを管理する

Azure Virtual Machines では、VM のオペレーティング システム、アプリケーション、およびデータを格納するためにディスクを使用します。 VM を作成するときは、予測されるワークロードに適したディスクのサイズと構成を選択する必要があります。 このチュートリアルでは、VM ディスクのデプロイと管理について説明します。 内容は次のとおりです。

> [!div class="checklist"]
> * OS ディスクと一時ディスク
> * データ ディスク
> * Standard ディスクと Premium ディスク
> * ディスクのパフォーマンス
> * データ ディスクの接続と準備

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 5.7.0 以降が必要になります。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

## <a name="default-azure-disks"></a>既定の Azure ディスク

Azure 仮想マシンを作成すると、2 つのディスクが仮想マシンに自動的に接続されます。 

**オペレーティング システム ディスク** - オペレーティング システム ディスクは、最大 4 TB までサイズを変更でき、VM のオペレーティング システムをホストします。  OS ディスクには、既定でドライブ文字 *c:* が割り当てられます。 OS ディスクのディスク キャッシュ構成は、OS パフォーマンスの向上のために最適化されています。 OS ディスクでアプリケーションやデータをホスト**しないでください**。 アプリケーションとデータには、この記事の後半で説明するデータ ディスクを使用してください。

**一時ディスク** - 一時ディスクは、VM と同じ Azure ホストに配置されているソリッド ステート ドライブを使用します。 一時ディスクは、パフォーマンスが高く、一時的なデータ処理などの操作に使用される場合があります。 ただし、VM を新しいホストに移動すると、一時ディスクに格納されているデータは削除されます。 一時ディスクのサイズは VM のサイズによって決まります。 一時ディスクには、既定でドライブ文字 *d:* が割り当てられます。

### <a name="temporary-disk-sizes"></a>一時ディスクのサイズ

| type | 一般的なサイズ | 一時ディスクの最大サイズ (GiB) |
|----|----|----|
| [汎用](sizes-general.md) | A、B、D シリーズ | 1600 |
| [コンピューティングの最適化](sizes-compute.md) | F シリーズ | 576 |
| [メモリの最適化](sizes-memory.md) | D、E、G、M シリーズ | 6144 |
| [ストレージの最適化](sizes-storage.md) | L シリーズ | 5630 |
| [GPU](sizes-gpu.md) | N シリーズ | 1440 |
| [高性能](sizes-hpc.md) | A および H シリーズ | 2000 |

## <a name="azure-data-disks"></a>Azure データ ディスク

アプリケーションのインストールやデータの保存のために、データ ディスクをさらに追加できます。 耐久性と応答性の高いデータ ストレージが望ましい状況では、必ず、データ ディスクを使用する必要があります。 各データ ディスクの最大容量は 4 TB です。 仮想マシンのサイズによって、VM に接続できるデータ ディスクの数が決まります。 各 VM vCPU に、2 つのデータ ディスクを接続できます。 

### <a name="max-data-disks-per-vm"></a>VM あたりの最大データ ディスク数

| type | 一般的なサイズ | VM あたりの最大データ ディスク数 |
|----|----|----|
| [汎用](sizes-general.md) | A、B、D シリーズ | 64 |
| [コンピューティングの最適化](sizes-compute.md) | F シリーズ | 64 |
| [メモリの最適化](sizes-memory.md) | D、E、G、M シリーズ | 64 |
| [ストレージの最適化](sizes-storage.md) | L シリーズ | 64 |
| [GPU](sizes-gpu.md) | N シリーズ | 64 |
| [高性能](sizes-hpc.md) | A および H シリーズ | 64 |

## <a name="vm-disk-types"></a>VM ディスクの種類

Azure では、2 種類のディスクを提供しています。

### <a name="standard-disk"></a>Standard ディスク

Standard Storage では、HDD が使用されており、高パフォーマンスでありながらコスト効率にも優れたストレージを提供します。 Standard ディスクは、コスト効率が重視される、開発およびテストのワークロードに最適です。

### <a name="premium-disk"></a>Premium ディスク

Premium ディスクは、SSD ベースの高性能で待機時間の短いディスクによってサポートされています。 実稼働ワークロードを実行する VM に最適です。 Premium Storage は、DS シリーズ、DSv2 シリーズ、GS シリーズ、FS シリーズの VM をサポートしています。 Premium ディスクは、5 種類 (P10、P20、P30、P40、P50) に分類され、ディスク サイズによってディスクの種類が決まります。 ディスク サイズを選択するときは、値を切り上げて 1 つ上の種類にします。 たとえば、サイズが 128 GB 未満の場合、ディスクの種類は P10 で、サイズが 129 GB から 512 GB までの場合、ディスクの種類は P20 です。

### <a name="premium-disk-performance"></a>Premium ディスクのパフォーマンス

|Premium Storage ディスクの種類 | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ディスク サイズ (切り上げ) | 32 GB | 64 GB | 128 GB | 512 GB | 1,024 GB (1 TB) | 2,048 GB (2 TB) | 4,095 GB (4 TB) |
| ディスクあたりの最大 IOPS | 120 | 240 | 500 | 2,300 | 5,000 | 7,500 | 7,500 |
ディスクあたりのスループット | 25 MB/秒 | 50 MB/秒 | 100 MB/秒 | 150 MB/秒 | 200 MB/s | 250 MB/秒 | 250 MB/秒 |

上記の表は、ディスクあたりの最大 IOPS を割り出していますが、複数のデータ ディスクをストライピングすることによって、より高いレベルのパフォーマンスを実現できます。 たとえば、64 のデータ ディスクを Standard_GS5 VM に接続することができます。 これらの各ディスクのサイズが P30 である場合、最大 80,000 IOPS を実現できます。 VM あたりの最大 IOPS について詳しくは、「[VM の種類とサイズ](./sizes.md)」をご覧ください。

## <a name="create-and-attach-disks"></a>ディスクを作成して接続する

このチュートリアルの例を完了するには、既存の仮想マシンが必要です。 必要に応じて、次のコマンドを使用して仮想マシンを作成します。

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して、仮想マシンの管理者アカウントに必要なユーザー名とパスワードを設定します。

```azurepowershell-interactive
$cred = Get-Credential
```

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して、仮想マシンを作成します。

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -AsJob
```

`-AsJob` パラメーターにより、バックグラウンド タスクとして VM が作成されるため、PowerShell プロンプトが表示されます。 `Job` コマンドレットを使用して、バックグラウンド ジョブの詳細を表示できます。

[New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig) を使用して、初期構成を作成します。 次の例では、サイズが 128 ギガバイトのディスクを構成します。

```azurepowershell-interactive
$diskConfig = New-AzureRmDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

[New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk) コマンドを使用して、データ ディスクを作成します。

```azurepowershell-interactive
$dataDisk = New-AzureRmDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) コマンドを使用して、データ ディスクを追加する仮想マシンを取得します。

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

[Add-AzureRmVMDataDisk](/powershell/module/azurerm.compute/add-azurermvmdatadisk) コマンドを使用して、データ ディスクを仮想マシンの構成に追加します。

```azurepowershell-interactive
$vm = Add-AzureRmVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

[Update-AzureRmVM](/powershell/module/azurerm.compute/add-azurermvmdatadisk) コマンドを使用して、仮想マシンを更新します。

```azurepowershell-interactive
Update-AzureRmVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>データ ディスクを準備する

ディスクが仮想マシンに接続されたら、そのディスクを使用するようにオペレーティング システムを構成する必要があります。 次の例では、最初に VM に追加されたディスクを手動で構成する方法を示します。 この処理は、[カスタム スクリプト拡張機能](./tutorial-automate-vm-deployment.md)を使用して自動化することもできます。

### <a name="manual-configuration"></a>手動構成

仮想マシンとの RDP 接続を作成します。 PowerShell を開き、次のスクリプトを実行します。

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' | `
Initialize-Disk -PartitionStyle MBR -PassThru | `
New-Partition -AssignDriveLetter -UseMaximumSize | `
Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、VM ディスクについて、次のようなトピックを学習しました。

> [!div class="checklist"]
> * OS ディスクと一時ディスク
> * データ ディスク
> * Standard ディスクと Premium ディスク
> * ディスクのパフォーマンス
> * データ ディスクの接続と準備

次のチュートリアルに進み、VM 構成を自動化する方法について学習してください。

> [!div class="nextstepaction"]
> [VM 構成の自動化](./tutorial-automate-vm-deployment.md)
