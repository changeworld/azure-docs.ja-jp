---
title: Azure クイック スタート - PowerShell で Windows VM を作成する | Microsoft Docs
description: PowerShell で Windows 仮想マシンを作成する方法を簡単に説明します
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/12/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 8adfc2e1718e69914baabaa450c5ff0f230e0368
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
---
# <a name="create-a-windows-virtual-machine-with-powershell"></a>PowerShell で Windows 仮想マシンを作成する

Azure PowerShell モジュールは、PowerShell コマンド ラインやスクリプトで Azure リソースを作成および管理するために使用します。 このクイックスタートでは、PowerShell を使用して、Windows Server 2016 を実行する Azure 仮想マシンを作成する方法について詳しく説明します。 デプロイが完了したら、サーバーに接続し、IIS をインストールします。  

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 5.3.0 以降が必要になります。 バージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。



## <a name="create-resource-group"></a>Create resource group

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```


## <a name="create-virtual-machine"></a>仮想マシンの作成

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して、仮想マシンを作成します。 必要なのはリソースの名前を指定することだけです。New-AzureRMVM コマンドレットは、それらが既に存在していない場合は自動的に作成します。

この手順の実行時に、資格情報の入力を求められます。 入力した値は、仮想マシンのユーザー名とパスワードとして構成されます。

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

デプロイが完了したら、仮想マシンとのリモート デスクトップ接続を作成します。

[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) コマンドを使用して、仮想マシンのパブリック IP アドレスを返します。 この IP アドレスを書き留めておきます。後の手順で Web 接続性をテストするときにブラウザーでこの IP アドレスに接続します。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

ローカル コンピューターで次のコマンドを使用して、仮想マシンとのリモート デスクトップ セッションを作成します。 IP アドレスを仮想マシンの *publicIPAddress* に置き換えます。 メッセージが表示されたら、仮想マシンの作成時に使用した資格情報を入力します。

```
mstsc /v:<publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>PowerShell を使用した IIS のインストール

Azure VM にログインしたら、1 行の PowerShell を使用して、IIS をインストールし、Web トラフィックを許可するローカル ファイアウォール規則を有効にできます。 VM で PowerShell プロンプトを開き、次のコマンドを実行します。

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>IIS のようこそページの表示

IIS をインストールし、VM のポート 80 をインターネットから開いたら、任意の Web ブラウザーを使用して IIS の既定のようこそページを表示することができます。 上の手順で指定した *publicIpAddress* を使用して既定のページにアクセスします。 

![IIS の既定のサイト](./media/quick-create-powershell/default-iis-website.png) 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、単純な仮想マシンとネットワーク セキュリティ グループの規則をデプロイし、Web サーバーをインストールしました。 Azure 仮想マシンの詳細については、Windows VM のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Azure Windows 仮想マシンのチュートリアル](./tutorial-manage-vm.md)
