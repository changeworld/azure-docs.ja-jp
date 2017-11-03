---
title: "Azure で VHD のスナップショットを作成する | Microsoft Docs"
description: "バックアップまたは問題のトラブルシューティングに使うために、Azure VM のコピーを作成する方法について説明します。"
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: dba70db512d88dfc57107bade0df50d1834eb883
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2017
---
# <a name="create-a-snapshot"></a>スナップショットの作成

バックアップ、または VM の問題を解決するために、OS またはデータ ディスク VHD のスナップショットを作成します。 スナップショットは、VHD の完全な読み取り専用コピーです。 

## <a name="use-azure-portal-to-take-a-snapshot"></a>Azure Portal を使ってスナップショットを作成する 

1. [Azure Portal](https://portal.azure.com)にサインインします。
2. 左上の **[新規]** をクリックし、**[スナップショット]** を探します。
3. [スナップショット] ブレードで **[作成]** をクリックします。
4. スナップショットの **[名前]** を入力します。
5. 既存の[リソース グループ](../../azure-resource-manager/resource-group-overview.md#resource-groups)を選択するか、新しいリソース グループの名前を入力します。 
6. Azure データセンターの場所を選択します。  
7. **[ソース ディスク]** で、スナップショットを作成する Managed Disk を選びます。
8. スナップショットの保存に使う **[アカウントの種類]** を選びます。 高パフォーマンスのディスクに保存する必要がある場合を除き、**[Standard_LRS]** をお勧めします。
9. **Create** をクリックしてください。

## <a name="use-powershell-to-take-a-snapshot"></a>PowerShell を使用し、スナップショットを作成する
次の手順では、コピーする VHD ディスクの取得方法、スナップショット構成の作成方法、[New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) コマンドレットを使用したディスクのスナップショットの取得方法を示します。 

AzureRM.Compute PowerShell モジュールの最新バージョンがインストールされていることを確認してください。 インストールするには次のコマンドを実行します。

```
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
詳細については、[Azure PowerShell のバージョン管理に関するページ](/powershell/azure/overview)をご覧ください。


1. パラメーターを設定する。 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$dataDiskName = 'myDisk' 
$snapshotName = 'mySnapshot'  
```

2. コピーされる VHD ディスクを取得する。

 ```azurepowershell-interactive
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. スナップショットの構成を作成する。 

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. スナップショットを取得する。

 ```azurepowershell-interactive
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
スナップショットを使って Managed Disk を作成し、高パフォーマンスが必要な VM に接続する計画がある場合は、New-AzureRmSnapshot コマンドで `-AccountType Premium_LRS` パラメーターを使います。 スナップショットが作成され、Premium Managed Disk として保存されます。 Premium Managed Disks は、Standard Managed Disks よりも高価格です。 なので、このパラメーターを使用する前に Premium Managed Disks が本当に必要なことを確認してください。

## <a name="next-steps"></a>次のステップ

スナップショットから管理ディスクを作成し、その新しい管理ディスクを OS ディスクとして接続することで、スナップショットから仮想マシンを作成します。 詳細については、「[PowerShell でスナップショットから仮想マシンを作成する](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)」のサンプルを参照してください。
