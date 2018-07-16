---
title: クイック スタート - Azure PowerShell を使用して Windows VM を作成する | Microsoft Docs
description: このクイック スタートでは、Azure PowerShell を使用して Windows 仮想マシンを作成する方法について説明します
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b199d031d4aaef4c1a707199f5692c86543e6360
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38767206"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-with-powershell"></a>クイック スタート: PowerShell を使用して Azure に Windows 仮想マシンを作成する

Azure PowerShell モジュールは、PowerShell コマンド ラインやスクリプトで Azure リソースを作成および管理するために使用します。 このクイック スタートでは、Azure PowerShell モジュールを使って、Windows Server 2016 を実行する仮想マシン (VM) を Azure に展開する方法を示します。 次に、VM の動作を確認するために、VM に RDP 接続し、IIS Web サーバーをインストールします。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 5.7.0 以降が必要になります。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-resource-group"></a>リソース グループの作成

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-virtual-machine"></a>仮想マシンの作成

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して VM を作成します。 各リソースの名前を指定すると、それらがまだ存在していない場合は、[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) コマンドレットによって作成されます。

メッセージが表示されたら、VM に対するログオン資格情報として使うユーザー名とパスワードを入力します。

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389
```

## <a name="connect-to-virtual-machine"></a>仮想マシンへの接続

展開が完了した後、RDP で VM に接続します。 VM の動作を確認するために、IIS Web サーバーがインストールされます。

VM のパブリック IP アドレスを確認するには、[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使います。

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

次のコマンドを使って、お使いのローカル コンピューターからリモート デスクトップ セッションを作成します。 IP アドレスは、実際の VM のパブリック IP アドレスに置き換えてください。 メッセージが表示されたら、VM の作成時に使った資格情報を入力します。

```powershell
mstsc /v:publicIpAddress
```

## <a name="install-web-server"></a>Web サーバーのインストール

VM の動作を確認するために、IIS Web サーバーをインストールします。 VM で PowerShell プロンプトを開き、次のコマンドを実行します。

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

終了したら、VM への RDP 接続を閉じます。

## <a name="view-the-web-server-in-action"></a>動作中の Web サーバーを表示する

IIS をインストールし、VM のポート 80 をインターネットから開いたら、任意の Web ブラウザーを使用して IIS の既定のウェルカム ページを表示することができます。 前の手順で取得した VM のパブリック IP アドレスを使用します。 次の例は、既定の IIS Web サイトを示しています。

![IIS の既定のサイト](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドレットを使って、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、単純な仮想マシンをデプロイし、Web トラフィック用のネットワーク ポートを開き、基本的な Web サーバーをインストールしました。 Azure 仮想マシンの詳細については、Windows VM のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Azure Windows 仮想マシンのチュートリアル](./tutorial-manage-vm.md)
