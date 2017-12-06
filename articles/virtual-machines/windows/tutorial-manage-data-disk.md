---
title: "Azure PowerShell を使用した Azure ディスクの管理 | Microsoft Docs"
description: "チュートリアル - Azure PowerShell を使用した Azure ディスクの管理"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: b994cfd09156ae8e1662f4947241aa1a4672df98
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2017
---
# <a name="manage-azure-disks-with-powershell"></a>PowerShell を使用した Azure ディスクの管理

Azure の仮想マシンでは、VM のオペレーティング システム、アプリケーション、データの格納にディスクを使用します。 VM を作成するときは、予測されるワークロードに適したディスクのサイズと構成を選択する必要があります。 このチュートリアルでは、VM ディスクのデプロイと管理について説明します。 内容は次のとおりです。

> [!div class="checklist"]
> * OS ディスクと一時ディスク
> * データ ディスク
> * Standard ディスクと Premium ディスク
> * ディスクのパフォーマンス
> * データ ディスクの接続と準備

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 3.6 以降が必要になります。 バージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzureRmAccount` を実行して Azure との接続を作成することも必要です。 

## <a name="default-azure-disks"></a>既定の Azure ディスク

Azure 仮想マシンを作成すると、2 つのディスクが仮想マシンに自動的に接続されます。 

**オペレーティング システム ディスク** - オペレーティング システム ディスクは、最大 1 TB までサイズを変更でき、VM のオペレーティング システムをホストします。  OS ディスクには、既定でドライブ文字 *c:* が割り当てられます。 OS ディスクのディスク キャッシュ構成は、OS パフォーマンスの向上のために最適化されています。 OS ディスクでアプリケーションやデータをホスト**しないでください**。 アプリケーションとデータには、この記事の後半で説明するデータ ディスクを使用してください。

**一時ディスク** - 一時ディスクは、VM と同じ Azure ホストに配置されているソリッド ステート ドライブを使用します。 一時ディスクは、パフォーマンスが高く、一時的なデータ処理などの操作に使用される場合があります。 ただし、VM を新しいホストに移動すると、一時ディスクに格納されているデータは削除されます。 一時ディスクのサイズは VM のサイズによって決まります。 一時ディスクには、既定でドライブ文字 *d:* が割り当てられます。

### <a name="temporary-disk-sizes"></a>一時ディスクのサイズ

| 型 | VM サイズ | 一時ディスクの最大サイズ (GB) |
|----|----|----|
| [汎用](sizes-general.md) | A および D シリーズ | 800 |
| [コンピューティングの最適化](sizes-compute.md) | F シリーズ | 800 |
| [メモリの最適化](../virtual-machines-windows-sizes-memory.md) | D および G シリーズ | 6144 |
| [ストレージの最適化](../virtual-machines-windows-sizes-storage.md) | L シリーズ | 5630 |
| [GPU](sizes-gpu.md) | N シリーズ | 1440 |
| [高性能](sizes-hpc.md) | A および H シリーズ | 2000 |

## <a name="azure-data-disks"></a>Azure データ ディスク

アプリケーションのインストールやデータの保存のために、データ ディスクをさらに追加できます。 耐久性と応答性の高いデータ ストレージが望ましい状況では、必ず、データ ディスクを使用する必要があります。 各データ ディスクの最大容量は 1 TB です。 仮想マシンのサイズによって、VM に接続できるデータ ディスクの数が決まります。 各 VM vCPU に、2 つのデータ ディスクを接続できます。 

### <a name="max-data-disks-per-vm"></a>VM あたりの最大データ ディスク数

| 型 | VM サイズ | VM あたりの最大データ ディスク数 |
|----|----|----|
| [汎用](sizes-general.md) | A および D シリーズ | 32 |
| [コンピューティングの最適化](sizes-compute.md) | F シリーズ | 32 |
| [メモリの最適化](../virtual-machines-windows-sizes-memory.md) | D および G シリーズ | 64 |
| [ストレージの最適化](../virtual-machines-windows-sizes-storage.md) | L シリーズ | 64 |
| [GPU](sizes-gpu.md) | N シリーズ | 48 |
| [高性能](sizes-hpc.md) | A および H シリーズ | 32 |

## <a name="vm-disk-types"></a>VM ディスクの種類

Azure では、2 種類のディスクを提供しています。

### <a name="standard-disk"></a>Standard ディスク

Standard Storage では、HDD が使用されており、高パフォーマンスでありながらコスト効率にも優れたストレージを提供します。 Standard ディスクは、コスト効率が重視される、開発およびテストのワークロードに最適です。

### <a name="premium-disk"></a>Premium ディスク

Premium ディスクは、SSD ベースの高性能で待機時間の短いディスクによってサポートされています。 実稼働ワークロードを実行する VM に最適です。 Premium Storage は、DS シリーズ、DSv2 シリーズ、GS シリーズ、FS シリーズの VM をサポートしています。 Premium ディスクは、3 種類 (P10、P20、P30) に分類され、ディスク サイズによってディスクの種類が決まります。 ディスク サイズを選択するときは、値を切り上げて 1 つ上の種類にします。 たとえば、サイズが 128 GB 未満の場合はディスクの種類が P10 になり、129 ～ 512 GB の場合は P20、512 GB を超える場合は P30 になります。 

### <a name="premium-disk-performance"></a>Premium ディスクのパフォーマンス

|Premium Storage ディスクの種類 | P10 | P20 | P30 |
| --- | --- | --- | --- |
| ディスク サイズ (切り上げ) | 128 GB | 512 GB | 1,024 GB (1 TB) |
| ディスクあたりの IOPS | 500 | 2,300 | 5,000 |
ディスクあたりのスループット | 100 MB/秒 | 150 MB/秒 | 200 MB/s |

上記の表は、ディスクあたりの最大 IOPS を割り出していますが、複数のデータ ディスクをストライピングすることによって、より高いレベルのパフォーマンスを実現できます。 たとえば、64 のデータ ディスクを Standard_GS5 VM に接続することができます。 これらの各ディスクのサイズが P30 である場合、最大 80,000 IOPS を実現できます。 VM あたりの最大 IOPS について詳しくは、「[VM の種類とサイズ](./sizes.md)」をご覧ください。

## <a name="create-and-attach-disks"></a>ディスクを作成して接続する

このチュートリアルの例を完了するには、既存の仮想マシンが必要です。 必要に応じて、この[サンプル スクリプト](../scripts/virtual-machines-windows-powershell-sample-create-vm.md)で仮想マシンを作成できます。 このチュートリアルを実行するときは、リソース グループと VM の名前を適宜置き換えてください。

[New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig) を使用して、初期構成を作成します。 次の例では、サイズが 128 ギガバイトのディスクを構成します。

```azurepowershell-interactive
$diskConfig = New-AzureRmDiskConfig -Location EastUS -CreateOption Empty -DiskSizeGB 128
```

[New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk) コマンドを使用して、データ ディスクを作成します。

```azurepowershell-interactive
$dataDisk = New-AzureRmDisk -ResourceGroupName myResourceGroup -DiskName myDataDisk -Disk $diskConfig
```

[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) コマンドを使用して、データ ディスクを追加する仮想マシンを取得します。

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

[Add-AzureRmVMDataDisk](/powershell/module/azurerm.compute/add-azurermvmdatadisk) コマンドを使用して、データ ディスクを仮想マシンの構成に追加します。

```azurepowershell-interactive
$vm = Add-AzureRmVMDataDisk -VM $vm -Name myDataDisk -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 1
```

[Update-AzureRmVM](/powershell/module/azurerm.compute/add-azurermvmdatadisk) コマンドを使用して、仮想マシンを更新します。

```azurepowershell-interactive
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm
```

## <a name="prepare-data-disks"></a>データ ディスクを準備する

ディスクが仮想マシンに接続されたら、そのディスクを使用するようにオペレーティング システムを構成する必要があります。 次の例では、最初に VM に追加されたディスクを手動で構成する方法を示します。 この処理は、[カスタム スクリプト拡張機能](./tutorial-automate-vm-deployment.md)を使用して自動化することもできます。

### <a name="manual-configuration"></a>手動構成

仮想マシンとの RDP 接続を作成します。 PowerShell を開き、次のスクリプトを実行します。

```azurepowershell-interactive
Get-Disk | Where partitionstyle -eq 'raw' | `
Initialize-Disk -PartitionStyle MBR -PassThru | `
New-Partition -AssignDriveLetter -UseMaximumSize | `
Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="next-steps"></a>次のステップ

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
