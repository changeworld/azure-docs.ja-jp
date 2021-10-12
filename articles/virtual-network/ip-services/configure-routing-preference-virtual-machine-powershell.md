---
title: 'チュートリアル: VM にルーティング優先順位を構成する - Azure PowerShell'
description: このチュートリアルでは、Azure PowerShell を使用して、ルーティング優先順位を選択したパブリック IP アドレスを持つ VM を作成する方法について説明します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 5442bbd177feab409daa7ed5eb5bad95aeed9e04
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369180"
---
# <a name="tutorial-configure-routing-preference-for-a-vm-using-azure-powershell"></a>チュートリアル: Azure PowerShell を使用して VM にルーティング優先順位を構成する

このチュートリアルでは、仮想マシンにルーティング優先順位を構成する方法について説明します。 ルーティング優先順位オプションとして **[インターネット]** を選択した場合、VM からインターネットへのトラフィックは ISP ネットワーク経由でルーティングされます。 既定のルーティングは、Microsoft グローバル ネットワーク経由です。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * **インターネット** ルーティング優先順位に対して構成されるパブリック IP アドレスを作成します。
> * 仮想マシンを作成します。
> * パブリック IP アドレスのルーティング優先順位が **インターネット** に設定されていることを確認します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- ローカルにインストールされた Azure PowerShell または Azure Cloud Shell

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して、**TutorVMRoutePref-rg** という名前のリソース グループを場所 **westus2** に作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name 'TutorVMRoutePref-rg' -Location 'westus2'

```

## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、**TutorVMRoutePref-rg** に **myPublicIP** という名前の標準ゾーン冗長パブリック IPv4 アドレスを作成します。 **インターネット** の **タグ** が、PowerShell コマンドのパラメーターとしてパブリック IP アドレスに適用され、**インターネット** ルーティング優先順位が有効になります。

```azurepowershell-interactive
## Create IP tag for Internet and Routing Preference. ##
$tag = @{
    IpTagType = 'RoutingPreference'
    Tag = 'Internet'   
}
$ipTag = New-AzPublicIpTag @tag

## Create IP. ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'TutorVMRoutePref-rg'
    Location = 'westus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    IpTag = $ipTag
    Zone = 1,2,3   
}
New-AzPublicIpAddress @ip
```

## <a name="create-virtual-machine"></a>仮想マシンの作成

[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して仮想マシンを作成します。 前のセクションで作成したパブリック IP アドレスが PowerShell コマンドの一部として追加され、作成時に VM にアタッチされます。

```azurepowershell-interactive
## Create virtual machine. ##
$vm = @{
    ResourceGroupName = 'TutorVMRoutePref-rg'
    Location = 'West US 2'
    Name = 'myVM'
    PublicIpAddressName = 'myPublicIP'
}
New-AzVM @vm
```

## <a name="verify-internet-routing-preference"></a>インターネット ルーティング優先順位の確認

[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) を使用して、パブリック IP アドレスに **インターネット** ルーティング優先順位が構成されていることを確認します。

```azurepowershell-interactive
$ip = @{
    ResourceGroupName = 'TutorVMRoutePref-rg'
    Name = 'myPublicIP'
}  
Get-AzPublicIPAddress @ip | select -ExpandProperty IpTags

```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

仮想マシンとパブリック IP プレフィックスを使い終えたら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用してリソース グループとそれに含まれるすべてのリソースを削除します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'TutorVMRoutePref-rg'

```

## <a name="next-steps"></a>次のステップ

次の記事に進み、ルーティング優先順位を混合設定して仮想マシンを作成する方法について学習してください。
> [!div class="nextstepaction"]
> [仮想マシンの両方のルーティング優先設定オプションを構成する](routing-preference-mixed-network-adapter-portal.md)

