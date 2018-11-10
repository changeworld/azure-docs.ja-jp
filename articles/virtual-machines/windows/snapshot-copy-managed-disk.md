---
title: Azure で VHD のスナップショットを作成する | Microsoft Docs
description: バックアップまたは問題のトラブルシューティングに使うために、Azure VM のコピーを作成する方法について説明します。
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 0f223660f8dc99b590f8aa80a03d60fcbf3aa5d3
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232581"
---
# <a name="create-a-snapshot"></a>スナップショットの作成

スナップショットは、仮想ハード ドライブ (VHD) の完全な読み取り専用コピーです。 バックアップ、または仮想マシン (VM) の問題を解決するために、OS またはデータ ディスク VHD のスナップショットを取得できます。

スナップショットを使用して新しい VM を作成しようとしている場合は、進行中のすべてのプロセスを消去するため、スナップショットを作成する前に VM を正常にシャット ダウンすることをお勧めします。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用 

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左のメニューから、**[リソースの作成]** を選択し、**スナップショット**を探して選択します。
3. **スナップショット** ウィンドウで、**[作成]** を選択します。 **[スナップショットの作成]** ウィンドウが表示されます。
4. スナップショットの **[名前]** を入力します。
5. 既存の[リソース グループ](../../azure-resource-manager/resource-group-overview.md#resource-groups)を選択するか、新しいリソース グループの名前を入力します。 
6. Azure データセンターの**場所**を選択します。  
7. **[ソース ディスク]** で、スナップショットを作成するマネージド ディスクを選びます。
8. スナップショットの保存に使う **[アカウントの種類]** を選びます。 スナップショットを高パフォーマンスのディスクに保存する必要がある場合を除き、**[Standard_HDD]** を選択します。
9. **作成**を選択します。

## <a name="use-powershell"></a>PowerShell の使用

次の手順では、VHD ディスクのコピー方法、スナップショット構成の作成方法、[New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) コマンドレットを使用したディスクのスナップショットの取得方法を示します。 

始める前に、AzureRM.Compute PowerShell モジュールの最新バージョン (バージョン 5.7.0 以降) があることを確認してください。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、[Connect-AzureRmAccount](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) を実行して Azure との接続を作成することも必要です。

1. パラメーターを設定します。 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  
```

2. VM を取得します。

 ```azurepowershell-interactive
$vm = get-azurermvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
```

3. スナップショットの構成を作成します。 この例では、スナップショットは OS ディスクのものです。

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
```
   
   > [!NOTE]
   > スナップショットをゾーン回復性のあるストレージに格納する場合は、[可用性ゾーン](../../availability-zones/az-overview.md)をサポートするリージョンにストレージを作成し、`-SkuName Standard_ZRS` パラメーターを含めます。   
   
4. スナップショットを取得します。

 ```azurepowershell-interactive
New-AzureRmSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
```


## <a name="next-steps"></a>次の手順

スナップショットからマネージド ディスクを作成し、その新しいマネージド ディスクを OS ディスクとして接続することで、スナップショットから仮想マシンを作成します。 詳細については、「[PowerShell でスナップショットから仮想マシンを作成する](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)」のサンプルを参照してください。
