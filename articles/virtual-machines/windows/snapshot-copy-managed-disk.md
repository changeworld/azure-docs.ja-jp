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
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 7d45fd749fea4036d944d740541d8b8607553835
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658157"
---
# <a name="create-a-snapshot"></a>スナップショットの作成

バックアップ、または VM の問題を解決するために、OS またはデータ ディスク VHD のスナップショットを作成します。 スナップショットは、VHD の完全な読み取り専用コピーです。 

## <a name="use-azure-portal-to-take-a-snapshot"></a>Azure Portal を使ってスナップショットを作成する 

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左上の **[リソースの作成]** をクリックし、**[スナップショット]** を探します。
3. [スナップショット] ブレードで **[作成]** をクリックします。
4. スナップショットの **[名前]** を入力します。
5. 既存の[リソース グループ](../../azure-resource-manager/resource-group-overview.md#resource-groups)を選択するか、新しいリソース グループの名前を入力します。 
6. Azure データセンターの場所を選択します。  
7. **[ソース ディスク]** で、スナップショットを作成する Managed Disk を選びます。
8. スナップショットの保存に使う **[アカウントの種類]** を選びます。 高パフォーマンスのディスクに保存する必要がある場合を除き、**[Standard_LRS]** をお勧めします。
9. **Create** をクリックしてください。

## <a name="use-powershell-to-take-a-snapshot"></a>PowerShell を使用し、スナップショットを作成する

次の手順では、コピーする VHD ディスクの取得方法、スナップショット構成の作成方法、[New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) コマンドレットを使用したディスクのスナップショットの取得方法を示します。 

始める前に、AzureRM.Compute PowerShell モジュールの最新バージョンがあることを確認してください。 この記事では、AzureRM モジュール バージョン 5.7.0 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

パラメーターを設定する。 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  
```

VM を取得します。

 ```azurepowershell-interactive
$vm = get-azurermvm `
   -ResourceGroupName $resourceGroupName `
   -Name $vmName
```

スナップショットの構成を作成します。 この例では、OS ディスクのスナップショットを作成します。

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig `
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
   -Location $location `
   -CreateOption copy
```
   
> [!NOTE]
> スナップショットをゾーン回復性のあるストレージに格納する場合は、[可用性ゾーン](../../availability-zones/az-overview.md)をサポートするリージョンにストレージを作成し、`-SkuName Standard_ZRS` パラメーターを含める必要があります。   

   
スナップショットを取得する。

```azurepowershell-interactive
New-AzureRmSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```




## <a name="next-steps"></a>次の手順

スナップショットから管理ディスクを作成し、その新しい管理ディスクを OS ディスクとして接続することで、スナップショットから仮想マシンを作成します。 詳細については、「[PowerShell でスナップショットから仮想マシンを作成する](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)」のサンプルを参照してください。
