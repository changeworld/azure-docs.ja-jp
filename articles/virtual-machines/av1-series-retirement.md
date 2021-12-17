---
title: Av1 シリーズの提供終了
description: Av1 シリーズの VM サイズについての提供終了情報。
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: mimckitt
ms.openlocfilehash: cfc30046d33ad9e6f91b866dc5653fbbcbdbd995
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577734"
---
# <a name="av1-series-retirement"></a>Av1 シリーズの提供終了

2024 年 8 月 31 日に、Basic および Standard の A シリーズの VM が廃止されます。 その日までに、ワークロードを Av2 シリーズの VM に移行する必要があります。この VM では、vCPU あたりメモリが増加し、ソリッドステート ドライブ (SSD) でのより高速なストレージが提供されます。

> [!NOTE]
> 場合によっては、サイズ変更の前に VM の割り当てを解除する必要があります。 これは、現在 VM をホストしているハードウェア クラスターで新しいサイズを使用できない場合に発生する可能性があります。


## <a name="migrate-workloads-from-basic-and-standard-a-series-vms-to-av2-series-vms"></a>Basic および Standard の A シリーズの VM から Av2 シリーズの VM にワークロードを移行する 

[Azure portal、PowerShell、または CLI](resize-vm.md) を使用して、仮想マシンのサイズを Av2 シリーズに変更できます。 Azure portal と PowerShell を使用して VM のサイズを変更する例を以下に示します。 

> [!IMPORTANT]
> 仮想マシンのサイズを変更すると、再起動が発生します。 再起動につながる操作は、ピーク時以外の営業時間中に行うことをお勧めします。 

### <a name="azure-portal"></a>Azure portal 
1. [Azure portal](https://portal.azure.com) を開きます。
1. 検索に「**virtual machines**」と入力します。
1. **[サービス]** で、 **[仮想マシン]** を選択します。
1. **[仮想マシン]** ページで、サイズ変更する仮想マシンをクリックします。
1. 左側のメニューで **[サイズ]** を選択します。
1. 利用可能なサイズの一覧から新しい Av2 サイズを選択し、 **[サイズ変更]** をクリックします。

### <a name="azure-powershell"></a>Azure PowerShell
1. リソース グループと VM 名の変数を設定します。 値は、サイズ変更する VM の情報に置き換えます。 

    ```powershell
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    ```
2. VM がホストされているハードウェア クラスターで使用できる VM のサイズを一覧表示します。

    ```powershell
    Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName
    ```

3. VM のサイズを新しいサイズに変更します。

    ```powershell
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
    $vm.HardwareProfile.VmSize = "<newAv2VMsize>"
    Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
    ```

## <a name="help-and-support"></a>ヘルプとサポート

質問がある場合は、[Microsoft Q&A](/answers/topics/azure-virtual-machines.html) でコミュニティの専門家に質問してください。 サポート プランに加入していて技術的な支援が必要な場合は、[サポート リクエスト](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を作成してください。

1. [問題の種類] で、[技術] を選択します。
1. [サブスクリプション] で、ご使用のサブスクリプションを選択します。
1. [サービス] で、[使用中のサービス] をクリックします。
1. [サービスの種類] で、[Windows/Linux を実行している仮想マシン] を選択します。
1. [概要] で、要求の概要を入力します。
1. [問題の種類] で、[Assistance with resizing my VM]\(VM のサイズ変更に関するサポート\) を選択します。
1. [問題のサブタイプ] で、該当するオプションを選択します。

## <a name="next-steps"></a>次のステップ
[Av2 シリーズ VM](av2-series.md) の詳細情報
