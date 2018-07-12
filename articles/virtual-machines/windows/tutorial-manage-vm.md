---
title: チュートリアル - Azure PowerShell を使用した Windows VM の作成と管理 | Microsoft Docs
description: このチュートリアルでは、Azure PowerShell を使用して、Azure で Windows VM を作成し、管理する方法について説明します。
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
ms.date: 03/23/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d47981042fc13a96bdf5cb9690e4dc83a6aa0162
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932553"
---
# <a name="tutorial-create-and-manage-windows-vms-with-azure-powershell"></a>チュートリアル: Azure PowerShell を使用して Windows VM を作成および管理する

Azure 仮想マシンは、完全に構成可能で柔軟なコンピューティング環境を提供します。 このチュートリアルでは、VM サイズや VM イメージの選択、VM のデプロイなどの Azure 仮想マシンの展開に関する基本事項について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * VM を作成して VM に接続する
> * VM イメージを選択して使用する
> * 特定の VM サイズを確認して使用する
> * VM のサイズを変更する
> * VM の状態を表示して理解する

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 5.7.0 以降が必要になります。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-resource-group"></a>リソース グループの作成

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) コマンドでリソース グループを作成します。 

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 仮想マシンの前にリソース グループを作成する必要があります。 次の例では、*myResourceGroupVM* という名前のリソース グループが *EastUS* リージョンに作成されます。

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

このチュートリアル全体で示しているように、VM の作成時または変更時にリソース グループを指定します。

## <a name="create-virtual-machine"></a>仮想マシンの作成

仮想マシンを作成するときに、オペレーティング システム イメージ、ネットワーク構成、管理者資格情報など、いくつかのオプションを使用できます。 この例では、Windows Server 2016 Datacenter の既定の最新バージョンを実行している、*myVM* という名前の仮想マシンが作成されます。

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して、仮想マシンの管理者アカウントに必要なユーザー名とパスワードを設定します。

```azurepowershell-interactive
$cred = Get-Credential
```

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して、仮想マシンを作成します。

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="connect-to-vm"></a>VM への接続

デプロイが完了したら、仮想マシンとのリモート デスクトップ接続を作成します。

次のコマンドを実行して、仮想マシンのパブリック IP アドレスを取得します。 この IP アドレスを書き留めておきます。後の手順で Web 接続性をテストするときにブラウザーでこの IP アドレスに接続します。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

ローカル コンピューターで次のコマンドを使用して、仮想マシンとのリモート デスクトップ セッションを作成します。 IP アドレスを仮想マシンの *publicIPAddress* に置き換えます。 メッセージが表示されたら、仮想マシンの作成時に使用した資格情報を入力します。

```powershell
mstsc /v:<publicIpAddress>
```

**[Windows セキュリティ]** ウィンドウで、**[その他]**、**[別のアカウントを使用する]** の順に選択します。 仮想マシン用に作成したユーザー名とパスワードを入力し、**[OK]** をクリックします。

## <a name="understand-vm-images"></a>VM イメージについて

Azure Marketplace には、新しい仮想マシンの作成に使用できる多数の仮想マシンのイメージが用意されています。 前の手順では、Windows Server 2016 Datacenter イメージを使用して仮想マシンを作成しました。 この手順では、PowerShell モジュールを使用して、新しい VM のベースとしても使用できるその他の Windows イメージを Marketplace で検索します。 このプロセスは、イメージを[識別](cli-ps-findimage.md#terminology)するための発行元、プラン、SKU、およびオプションのバージョン番号の検索で構成されます。 

[Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) コマンドを使用して、イメージ発行元の一覧を取得します。

```powershell
Get-AzureRmVMImagePublisher -Location "EastUS"
```


  [Get-AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) を使用して、イメージ プランの一覧を取得します。 このコマンドを使用すると、返される一覧が、指定した発行元でフィルター処理されます。

```azurepowershell-interactive
Get-AzureRmVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"
```

```azurepowershell-interactive
Offer             PublisherName          Location
-----             -------------          -------- 
Windows-HUB       MicrosoftWindowsServer EastUS 
WindowsServer     MicrosoftWindowsServer EastUS   
WindowsServer-HUB MicrosoftWindowsServer EastUS   
```


  [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) コマンドは、発行元とプラン名でフィルター処理して、イメージ名の一覧を返します。

```azurepowershell-interactive
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

```azurepowershell-interactive
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

この情報は、特定のイメージを使用して VM をデプロイするために使用できます。 この例では、Windows Server 2016 with Containers イメージの最新バージョンを使用して仮想マシンをデプロイします。

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM2" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress2" `
    -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
    -Credential $cred `
    -AsJob
```

`-AsJob` パラメーターにより、バックグラウンド タスクとして VM が作成されるため、PowerShell プロンプトが表示されます。 `Get-Job` コマンドレットを使用して、バックグラウンド ジョブの詳細を表示できます。


## <a name="understand-vm-sizes"></a>VM のサイズについて

仮想マシンのサイズにより、CPU、GPU、メモリなど、仮想マシンで利用できるコンピューティング リソースの量が決定されます。 仮想マシンは、予想されるワークロードに適したサイズで作成する必要があります。 ワークロードが増えた場合は既存の仮想マシンのサイズを変更できます。

### <a name="vm-sizes"></a>VM サイズ

次の表は、ユース ケース別にサイズを分類したものです。  
| type                     | 一般的なサイズ           |    [説明]       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [汎用](sizes-general.md)         |Dsv3、Dv3、DSv2、Dv2、DS、D、Av2、A0 - 7| CPU とメモリのバランスがとれています。 開発/テスト環境や、小中規模のアプリケーションとデータ ソリューションに最適です。  |
| [コンピューティングの最適化](sizes-compute.md)   | Fs、F             | メモリに対する CPU の比が大きくなっています。 トラフィックが中程度のアプリケーション、ネットワーク アプライアンス、バッチ処理に適しています。        |
| [メモリの最適化](sizes-memory.md)    | Esv3、Ev3、M、GS、G、DSv2、DS、Dv2、D   | コアに対するメモリの比が大きくなっています。 リレーショナル データベース、中から大規模のキャッシュ、およびインメモリ分析に適しています。                 |
| [ストレージの最適化](sizes-storage.md)      | Ls                | 高いディスク スループットと IO。 ビッグ データ、SQL、および NoSQL のデータベースに最適です。                                                         |
| [GPU](sizes-gpu.md)          | NV、NC            | 負荷の高いグラフィック処理やビデオ編集に特化した VM です。       |
| [高性能](sizes-hpc.md) | H、A8 ～ 11          | オプションで高スループットのネットワーク インターフェイス (RDMA) を備えた、最も強力な CPU VM です。 


### <a name="find-available-vm-sizes"></a>使用可能な VM サイズを確認する

特定のリージョンで利用可能な VM サイズのリストを確認するには、[Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) コマンドを使用します。

```azurepowershell-interactive
Get-AzureRmVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>VM のサイズを変更する

デプロイ後に VM のサイズを変更して、リソースの割り当てを増減できます。

VM のサイズを変更する前に、現在の VM クラスターで目的のサイズを利用可能であるか確認します。 [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) コマンドは、サイズの一覧を返します。 

```azurepowershell-interactive
Get-AzureRmVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

目的のサイズが使用可能な場合は電源を入れた状態で VM のサイズを変更できます。ただし、この操作中に再起動が行われます。

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroupVM"
```

目的のサイズが現在のクラスターにない場合、サイズ変更の操作を行うには VM の割り当てを解除する必要があります。 VM の電源を再度入れると、一時ディスク上のデータはすべて削除され、静的 IP アドレスを使用している場合以外はパブリック IP アドレスが変更されるため、注意してください。 

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroupVM"
Start-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -Name $vm.name
```

## <a name="vm-power-states"></a>VM の電源の状態

Azure VM は、さまざまな電源状態のいずれかになります。 この状態は、ハイパーバイザーから見た VM の現在の状態を表しています。 

### <a name="power-states"></a>電源の状態

| 電源の状態 | 説明
|----|----|
| 開始中 | 仮想マシンが起動中であることを示します。 |
| 実行中 | 仮想マシンが実行中であることを示します。 |
| 停止中 | 仮想マシンが停止中であることを示します。 | 
| 停止済み | 仮想マシンが停止されていることを示します。 仮想マシンが停止済みの状態でも、コンピューティング料金は発生します。  |
| 割り当て解除中 | 仮想マシンの割り当てが解除中であることを示します。 |
| 割り当て解除済み | 仮想マシンがハイパーバイザーから完全に削除されているものの、コントロール プレーンでは使用可能であることを示します。 割り当て解除済み状態の仮想マシンでは、コンピューティング料金は発生しません。 |
| - | 仮想マシンの電源状態が不明であることを示します。 |

### <a name="find-power-state"></a>電源の状態を確認する

特定の VM の状態を取得するには、[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) コマンドを使用します。 必ず仮想マシンとリソース グループの有効な名前を指定してください。 

```azurepowershell-interactive
Get-AzureRmVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

出力:

```azurepowershell-interactive
Status
------
PowerState/running
```

## <a name="management-tasks"></a>管理タスク

仮想マシンのライフサイクルで、各種の管理タスクを実行する必要がある場合があります (仮想マシンの起動、停止、削除など)。 また、何度も行う作業や複雑な作業は、スクリプトを作成して自動化したい場合もあるでしょう。 日常的な管理タスクの多くは、Azure PowerShell を使ってコマンド ラインやスクリプトから実行できます。

### <a name="stop-virtual-machine"></a>仮想マシンの停止

[Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) を使用して仮想マシンを停止し、割り当てを解除します。

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM" -Force
```

仮想マシンをプロビジョニングされた状態で保持する場合は、-StayProvisioned パラメーターを使用します。

### <a name="start-virtual-machine"></a>仮想マシンの起動

```azurepowershell-interactive
Start-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM"
```

### <a name="delete-resource-group"></a>Delete resource group

リソース グループを削除すると、そこに含まれているリソースもすべて削除されます。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroupVM" -Force
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次のような基本的な VM の作成と管理を実行する方法について説明しました。

> [!div class="checklist"]
> * VM を作成して VM に接続する
> * VM イメージを選択して使用する
> * 特定の VM サイズを確認して使用する
> * VM のサイズを変更する
> * VM の状態を表示して理解する

次のチュートリアルに進み、VM ディスクについて確認してください。  

> [!div class="nextstepaction"]
> [VM ディスクの作成と管理](./tutorial-manage-data-disk.md)
