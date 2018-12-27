---
title: Azure Virtual Machines 用の一般的な PowerShell コマンド | Microsoft Docs
description: Azure で Windows VM の作成および管理を開始する際に使用される一般的な PowerShell コマンド。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: cb9f03ab87079ba33135840e3e7599d2e8cc95e9
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2018
ms.locfileid: "42144064"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Azure Virtual Machines の作成および管理に使用される一般的な PowerShell コマンド

この記事では、Azure サブスクリプション内の仮想マシンの作成および管理に使用できる Azure PowerShell コマンドの一部について説明します。  具体的なコマンド ライン スイッチやオプションについて詳しくは、**Get-Help** "*コマンド*" を使って確認できます。

最新バージョンの Azure PowerShell をインストールし、サブスクリプションを選択して、ご利用のアカウントにサインインする方法については、「[Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)」を参照してください。

この記事内のコマンドを複数実行する場合は、変数が役に立つことがあります。

- $location: 仮想マシンの場所。 [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) を使用して、適切な[地理的リージョン](https://azure.microsoft.com/regions/)を検索できます。
- $myResourceGroup: 仮想マシンを含むリソース グループの名前。
- $myVM: 仮想マシンの名前。

## <a name="create-a-vm---simplified"></a>VM を作成する - 簡易版

| タスク | コマンド |
| ---- | ------- |
| 簡単な VM を作成する | [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) -Name $myVM <BR></BR><BR></BR> New-AzureRMVM には "*簡易版*" パラメーターのセットがあり、必要なのは 1 つの名前だけです。 -Name の値は、新しい VM の作成に必要なすべてのリソースの名前として使われます。 詳細を指定できますが、必要なものはこれだけです。|
| カスタム イメージから VM を作成する | New-AzureRmVm -ResourceGroupName $myResourceGroup -Name $myVM ImageName "myImage" -Location $location  <BR></BR><BR></BR>独自の[マネージド イメージ](capture-image-resource.md)を既に作成してある必要があります。 イメージを使って、複数の同じ VM を作成できます。 |



## <a name="create-a-vm-configuration"></a>VM 構成の作成

| タスク | コマンド |
| ---- | ------- |
| VM 構成の作成 |$vm = [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>VM 構成は、仮想マシンの設定の定義または更新に使用します。 この構成は、VM の名前とその [サイズ](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) で初期化します。 |
| 構成設定の追加 |$vm = [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>[資格情報](https://technet.microsoft.com/library/hh849815.aspx) を含むオペレーティング システムの設定が、New-AzureRmVMConfig を使用してあらかじめ作成した構成オブジェクトに追加されます。 |
| ネットワーク インターフェイスの追加 |$vm = [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/Add-AzureRmVMNetworkInterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>仮想ネットワークで通信するには、VM に [ネットワーク インターフェイス](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) がある必要があります。 [Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) を使用して既存のネットワーク インターフェイス オブジェクトを取得することもできます。 |
| プラットフォーム イメージの指定 |$vm = [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage) -VM $vm -PublisherName "発行元名" -Offer "発行元のプラン" -Skus "製品SKU" -Version "最新"<BR></BR><BR></BR>[イメージ情報](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) が、New-AzureRmVMConfig を使用してあらかじめ作成した構成オブジェクトに追加されます。 このコマンドから返されるオブジェクトは、プラットフォーム イメージを使用するために OS ディスクを設定する場合にのみ使用されます。 |
| VM の作成 |[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>すべてのリソースを[リソース グループ](../../azure-resource-manager/powershell-azure-resource-manager.md)に作成する必要があります。 このコマンドを実行する前に、New-AzureRmVMConfig、Set-AzureRmVMOperatingSystem、Set-AzureRmVMSourceImage、Add-AzureRmVMNetworkInterface、および Set-AzureRmVMOSDisk を実行します。 |
| VM の更新 |[Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Get-AzureRmVM を使用して現在の VM の構成を取得し、VM オブジェクトの構成設定を変更してから、このコマンドを実行します。 |

## <a name="get-information-about-vms"></a>VM に関する情報の取得

| タスク | コマンド |
| ---- | ------- |
| サブスクリプション内の VM の一覧表示 |[Get-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvm) |
| リソース グループ内の VM の一覧表示 |Get-AzureRmVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>サブスクリプションのリソース グループのリストを取得するには、[Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresourcegroup) を使用します。 |
| VM に関する情報の取得 |Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>VM の管理
| タスク | コマンド |
| --- | --- |
| VM の起動 |[Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| VM の停止 |[Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| 実行中の VM の再起動 |[Restart-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/restart-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| VM の削除 |[Remove-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>次の手順
* 仮想マシンを作成するための基本的な手順について「[Resource Manager と PowerShell を使用して Windows VM を作成する](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。

