---
title: "Windows VM からデータ ディスクを切断する - Azure | Microsoft Docs"
description: "Resource Manager デプロイ モデルを使用する Azure 仮想マシンから、データ ディスクを切断する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 7013e7ff3cb14dcad8e3e9a926bcee771180259d
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2017
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Windows 仮想マシンからディスクを切断する方法
仮想マシンに接続されたデータ ディスクが不要になった場合、そのディスクは簡単に切断できます。 そうすれば、ディスクは仮想マシンから削除されますが、ストレージからは削除されません。

> [!WARNING]
> ディスクを切断した場合、自動的には削除されません。 Premium Storage のサブスクリプションにお申込みいただいている場合は、ディスクのストレージ料金が引き続き発生します。 詳細については [Premium Storage 利用時の料金と課金](premium-storage.md#pricing-and-billing)に関する記事を参照してください。
>
>

再びディスク上の既存のデータを使用する場合は、同じ仮想マシンや別の仮想マシンに再接続できます。

## <a name="detach-a-data-disk-using-the-portal"></a>ポータルを使用してデータ ディスクを切断する方法

1. 左側のメニューで **[Virtual Machines]** を選択します。
2. 切断するデータ ディスクが接続されている仮想マシンを選択し、**[停止]** をクリックして VM の割り当てを解除します。
3. 仮想マシンのウィンドウで、**[ディスク]** を選択します。
4. **[ディスク]** ウィンドウの上部にある **[編集]** を選択します。
5. **[ディスク]** ウィンドウで、切断するデータ ディスクの右端にある ![切断ボタンのイメージ](./media/detach-disk/detach.png) 切断ボタンをクリックします。
5. ディスクが削除されたら、ウィンドウの上部にある **[保存]** をクリックします。
6. 仮想マシンのウィンドウで **[概要]** をクリックし、ウィンドウの上部にある **[開始]** ボタンをクリックして VM を再起動します。



ディスクはストレージに残りますが、仮想マシンからは切断されています。

## <a name="detach-a-data-disk-using-powershell"></a>PowerShell を使用してデータ ディスクを切断する方法
この例の最初のコマンドでは、[Get-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) コマンドレットを使用して、**RG11** リソース グループの **MyVM07** という名前の仮想マシンを取得し、**$VirtualMachine** 変数に格納します。

2 行目では、[Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk) コマンドレットを使用して、DataDisk3 という名前のデータ ディスクを仮想マシンから削除します。

3 行目では、[Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) コマンドレットを使用して仮想マシンの状態を更新し、データ ディスクを削除するプロセスを完了します。

```azurepowershell-interactive
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07"
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
Update-AzureRmVM -ResourceGroupName "RG11" -VM $VirtualMachine
```

詳細については、「[Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk)」を参照してください。

## <a name="next-steps"></a>次のステップ
データ ディスクを再利用する場合は、 [別の VM にそのデータ ディスクをアタッチ](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

