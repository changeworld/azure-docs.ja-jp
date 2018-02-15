---
title: "Azure での仮想ネットワークの作成 - PowerShell | Microsoft Docs"
description: "PowerShell を使用した仮想ネットワークの作成方法を簡単に説明します。 仮想ネットワークでは、プライベートで相互通信するために、たくさんの種類の Azure リソースを使用できます。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: 
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 091e7e6cabf325cdd9d4289e7d22e71c583d91db
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-virtual-network-using-powershell"></a>PowerShell を使用して仮想ネットワークを作成する

この記事では、仮想ネットワークの作成方法について説明します。 仮想ネットワークを作成した後は、仮想ネットワークに 2 つの仮想マシンをデプロイし、それらのマシン間でプライベート通信を行います。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 5.1.1 以降が必要になります。 インストールされているバージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-resource-group"></a>リソース グループの作成

[New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。 Azure リソースはすべて、Azure の場所 (リージョン) 内に作成されます。

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) を使用して仮想ネットワークを作成します。 次の例では、*EastUS* の場所にある *myVirtualNetwork* という名前の既定の仮想ネットワークを作成します。

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/24
```

すべての仮想ネットワークには、1 つまたは複数のアドレス プレフィックスが割り当てられています。 アドレス範囲は、CIDR 表記で指定されます。 アドレス空間 10.0.0.0/24 は、10.0.0.0 ～ 10.0.0.254 を含みます。 仮想ネットワークは、それらのアドレス空間内に 0 個または 1 つ以上のサブネットを保持します。 リソースは、仮想ネットワークのサブネットにデプロイされます。 

[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) を使用してサブネット構成を作成します。 すべてのサブネットには、仮想ネットワークのアドレス プレフィックス内に存在するアドレス プレフィックスがあります。 この例では、仮想ネットワークのアドレス プレフィックスと同じアドレス プレフィックスで、サブネット構成が作成されています。

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

サブネット アドレス プレフィックスは 10.0.0.0 ～ 10.0.0.254 を含みますが、Azure では最初の 4 つのアドレス (0 ～ 3) と各サブネットの最後のアドレスを予約しているため、アドレス 10.0.0.4 ～ 10.0.0.254 のみが使用可能です。 サブネット アドレス プレフィックスは仮想ネットワーク アドレスのプレフィックスと同じなので、この仮想ネットワークには 1 つのサブネットだけが存在することになります。

[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) を使って、仮想ネットワークにサブネット構成を書き込みます。これにより、サブネットが作成されます。

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>仮想マシンを作成する

仮想ネットワークでは、プライベートで相互通信するために、複数の種類の Azure リソースを使用できます。 仮想ネットワークにデプロイできるリソースの種類の 1 つは、仮想マシンです。 以降の手順では、仮想ネットワークにある仮想マシン間の通信の仕組みについて検証して理解できるように、仮想ネットワークに 2 つの仮想マシンを作成します。

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使って、仮想マシンを作成します。 この手順の実行時に、資格情報の入力を求められます。 入力した値は、仮想マシンのユーザー名とパスワードとして構成されます。 仮想マシンを作成する場所は、仮想ネットワークが存在する場所と同じ場所にする必要があります。 この記事では同じになっていますが、仮想マシンは、該当の仮想マシンと同じリソース グループ内に配置する必要はありません。 `-AsJob` パラメーターを使用すると、バック グラウンドでコマンドが実行されるため、次のタスクで続行することができます。

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

次の出力例と同様の出力が返され、Azure はバックグラウンドで仮想マシンの作成を開始します。

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

*既定*のサブネットで使用できる最初のアドレスは 10.0.0.4 なので、Azure DHCP はこれを仮想マシンに自動的に割り当てます。

2 番目の仮想マシンを作成します。 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```
仮想マシンの作成には、数分かかります。 作成されると、Azure は、作成された仮想マシンに関する出力を返します。 返された出力ではわかりませんが、サブネットで使用できる次のアドレスは *10.0.0.5* なので、Azure はこれを *myVm2* 仮想マシンに割り当てました。

## <a name="connect-to-a-virtual-machine"></a>仮想マシンへの接続

仮想マシンのパブリック IP アドレスを返す [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) コマンドを使用します。 Azure では既定で、インターネット ルーティング可能なパブリック IP アドレスを各仮想マシンに割り当てます。 パブリック IP アドレスは、[各 Azure リージョンに割り当てられているアドレスのプール](https://www.microsoft.com/download/details.aspx?id=41653)から、仮想マシンに割り当てられます。 Azure ではどのパブリック IP アドレスが仮想マシンに割り当てられているかを把握していますが、仮想マシンで実行されているオペレーティング システムが、割り当てられたパブリック IP アドレスを認識することはありません。 次の例では、*myVm1* 仮想マシンのパブリック IP アドレスを返しています。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -Name myVm1 -ResourceGroupName myResourceGroup | Select IpAddress
```

次のコマンドを使用して、お使いのローカル コンピューターから、*myVm1* 仮想マシンでリモート デスクトップ セッションを作成します。 `<publicIpAddress>` を前のコマンドで返された IP アドレスに置き換えます。

```
mstsc /v:<publicIpAddress>
```

リモート デスクトップ プロトコル (.rdp) ファイルが作成され、お使いのコンピューターにダウンロードされて開かれます。 仮想マシンを作成するときに指定したユーザー名とパスワードを入力して、**[OK]**  をクリックします。 サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** または **[続行]** をクリックして接続処理を続行します。

## <a name="validate-communication"></a>通信を検証する

Windows ファイアウォールでは既定で ping が許可されていないため、Windows 仮想マシンへの ping を試行すると、エラーになります。 *myVm1* への ping を許可するには、コマンド プロンプトから以下のコマンドを入力します。

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

*myVm2* との通信を検証するには、*myVm1* 仮想マシン上でコマンド プロンプトから以下のコマンドを入力します。 仮想マシンを作成したときに使用した資格情報を提供して、接続を完了します。

```
mstsc /v:myVm2
```

両方の仮想マシンが*既定の*サブネットから割り当てられたプライベート IP アドレスを保持し、かつ、リモート デスクトップが既定で Windows ファイアウォール経由で開かれたため、リモート デスクトップ接続に成功します。 Azure は仮想ネットワーク内のすべてのホストに DNS 名前解決を自動的に提供するので、*myVm2* にはホスト名で接続できます。 コマンド プロンプトで、*myVm2* から *myVm1* を ping します。

```
ping myvm1
```

前の手順で *myVm1* 仮想マシン上の Windows ファイアウォール経由での ping を許可したため、ping に成功します。 インターネットへの送信方向の通信を確認するには、以下のコマンドを入力します。

```
ping bing.com
```

bing.com から 4 つの応答を受信します。既定では、仮想ネットワークにあるどの仮想マシンも、インターネットへの送信方向の通信が可能です。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要なくなったら、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドを使用して、リソース グループとその中のすべてのリソースを削除できます。 リモート デスクトップ セッションを終了し、リソース グループを削除するコンピューターから次のコマンドを実行します。

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次の手順

この記事では、1 つのサブネットと 2 つの仮想マシンを含む既定の仮想ネットワークをデプロイしました。 複数のサブネットでカスタム仮想ネットワークを作成し、基本の仮想ネットワーク管理タスクを実行する方法を習得するには、カスタム仮想ネットワークの作成と管理に関するチュートリアルに進んでください。


> [!div class="nextstepaction"]
> [カスタム仮想ネットワークの作成および管理](virtual-networks-create-vnet-arm-pportal.md#powershell)
