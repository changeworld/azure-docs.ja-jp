---
title: クイック スタート - Azure PowerShell を使用して仮想マシン スケール セットを作成する | Microsoft Docs
description: Azure PowerShell を使用して仮想マシン スケール セットをすばやく作成する方法を説明します
services: virtual-machine-scale-sets
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/18
ms.author: zarhoads
ms.openlocfilehash: 7c24375cd86700b3b4125447e1aa6dbc7507d8ba
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2018
ms.locfileid: "51515813"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-with-azure-powershell"></a>クイック スタート: Azure PowerShell を使用して仮想マシン スケール セットを作成する
仮想マシン スケール セットを使用すると、同一の自動スケールの仮想マシンのセットをデプロイおよび管理できます。 スケール セット内の VM の数を手動で拡張したり、CPU などのリソースの使用率、メモリの需要、またはネットワーク トラフィックに基づいて自動的にスケールする規則を定義したりすることができます。 その後、Azure ロード バランサーがトラフィックをスケール セット内の VM インスタンスに分散します。 このクイック スタートでは、Azure PowerShell を使用して仮想マシン スケール セットを作成し、サンプル アプリケーションをデプロイします。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 6.0.0 以降が必要になります。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。


## <a name="create-a-scale-set"></a>スケール セットを作成する
[New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) を使用して仮想マシン スケール セットを作成します。 次の例では、*Windows Server 2016 Datacenter* プラットフォーム イメージを使用する *myScaleSet* という名前のスケール セットを作成します。 仮想ネットワーク用の Azure ネットワーク リソース、パブリック IP アドレス、およびロード バランサーが自動的に作成されます。 メッセージが表示されたら、スケール セット内の VM インスタンス用の自分の管理者資格情報を設定できます。

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。


## <a name="deploy-sample-application"></a>サンプル アプリケーションをデプロイする
スケール セットをテストするには、基本的な Web アプリケーションをインストールします。 VM インスタンスに IIS をインストールするスクリプトをダウンロードして実行するために、Azure カスタム スクリプト拡張機能が使用されます。 この拡張機能は、デプロイ後の構成、ソフトウェアのインストール、その他の構成や管理タスクに役立ちます。 詳細については、「[Windows のカスタム スクリプト拡張機能](../virtual-machines/windows/extensions-customscript.md)」を参照してください。

カスタム スクリプト拡張機能を使用して、基本的な IIS Web サーバーをインストールします。 次のように、IIS をインストールするカスタム スクリプト拡張機能を適用します。

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroup" `
            -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

## <a name="allow-traffic-to-application"></a>アプリケーションへのトラフィックを許可する

 基本的な Web アプリケーションへのアクセスを許可するには、[New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.compute/new-azurermnetworksecurityruleconfig) と [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.compute/new-azurermnetworksecuritygroup) を使用してネットワーク セキュリティ グループを作成します。 詳細については、「[Azure 仮想マシン スケール セットのネットワーク](virtual-machine-scale-sets-networking.md)」を参照してください。

 ```azurepowershell-interactive
 # Get information about the scale set
 $vmss = Get-AzureRmVmss `
             -ResourceGroupName "myResourceGroup" `
             -VMScaleSetName "myScaleSet"

 #Create a rule to allow traffic over port 80
 $nsgFrontendRule = New-AzureRmNetworkSecurityRuleConfig `
   -Name myFrontendNSGRule `
   -Protocol Tcp `
   -Direction Inbound `
   -Priority 200 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 80 `
   -Access Allow

 #Create a network security group and associate it with the rule
 $nsgFrontend = New-AzureRmNetworkSecurityGroup `
   -ResourceGroupName  "myResourceGroup" `
   -Location EastUS `
   -Name myFrontendNSG `
   -SecurityRules $nsgFrontendRule

 $vnet = Get-AzureRmVirtualNetwork `
   -ResourceGroupName  "myResourceGroup" `
   -Name myVnet

 $frontendSubnet = $vnet.Subnets[0]

 $frontendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
   -VirtualNetwork $vnet `
   -Name mySubnet `
   -AddressPrefix $frontendSubnet.AddressPrefix `
   -NetworkSecurityGroup $nsgFrontend

 Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

 # Update the scale set and apply the Custom Script Extension to the VM instances
 Update-AzureRmVmss `
     -ResourceGroupName "myResourceGroup" `
     -Name "myScaleSet" `
     -VirtualMachineScaleSet $vmss
 ```

## <a name="test-your-scale-set"></a>スケール セットのテスト
動作中のスケール セットを表示するには、Web ブラウザーでサンプル Web アプリケーションにアクセスします。 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使用して、ロード バランサーのパブリック IP アドレスを取得します。 次の例は、*myResourceGroup* リソース グループに作成された IP アドレスを表示します。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

ロード バランサーのパブリック IP アドレスを Web ブラウザーに入力します。 ロード バランサーは、次の例に示すように、VM インスタンスのいずれかにトラフィックを配分します。

![実行中の IIS サイト](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ
必要がなくなったら、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) を使用して、リソース グループ、スケール セット、およびすべての関連リソースを削除できます。 `-Force` パラメーターは、追加のプロンプトを表示せずにリソースの削除を確定します。 `-AsJob` パラメーターは、操作の完了を待たずにプロンプトに制御を戻します。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>次の手順
このクイック スタートでは、基本的なスケール セットを作成し、カスタム スクリプト拡張機能を使用して基本的な IIS Web サーバーを VM インスタンスにインストールしました。 さらに学習するには、Azure 仮想マシン スケール セットを作成および管理する方法についてのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure 仮想マシン スケール セットの作成と管理](tutorial-create-and-manage-powershell.md)
