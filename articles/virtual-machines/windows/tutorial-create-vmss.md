---
title: チュートリアル - Azure 内に Windows 用の仮想マシン スケール セットを作成する | Microsoft Docs
description: このチュートリアルでは、Azure PowerShell で仮想マシン スケール セットを使用して、Windows VM 上に高可用性アプリケーションを作成してデプロイする方法について説明します
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: tutorial
ms.date: 03/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 9d6f898f519a5baabcc132fdefbb3fa8f8a120cb
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346210"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows-with-azure-powershell"></a>チュートリアル: Azure PowerShell で仮想マシン スケール セットを作成して Windows に高可用性アプリをデプロイする
仮想マシン スケール セットを使用すると、同一の自動スケールの仮想マシンのセットをデプロイおよび管理できます。 スケール セット内の VM の数を手動で拡張したり、CPU などのリソースの使用率、メモリの需要、またはネットワーク トラフィックに基づいて自動的にスケーリングするルールを定義したりできます。 このチュートリアルでは、仮想マシン スケール セットを Azure にデプロイします。 学習内容は次のとおりです。

> [!div class="checklist"]
> * カスタム スクリプト拡張機能を使用して、スケールする IIS サイトを定義する
> * スケール セットのロード バランサーを作成する
> * 仮想マシン スケール セットを作成する
> * スケール セット内のインスタンスの数を増減させる
> * 自動スケール ルールを作成する

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 5.7.0 以降が必要になります。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。


## <a name="scale-set-overview"></a>スケール セットの概要
仮想マシン スケール セットを使用すると、同一の自動スケールの仮想マシンのセットをデプロイおよび管理できます。 スケール セット内の VM は、1 つ以上の*配置グループ*内の論理障害ドメインおよび更新ドメインに配布されます。 これらは同じように構成された VM のグループであり、[可用性セット](tutorial-availability-sets.md)に似ています。

スケール セットには必要に応じて VM が作成されます。 スケール セットの VM を追加または削除する方法とタイミングを制御するには、自動スケール ルールを定義します。 これらのルールは、CPU の負荷、メモリの使用量、ネットワーク トラフィックなどのメトリックに基づいて発動できます。

Azure プラットフォーム イメージを使う場合、スケール セットは最大 1,000 個の VM をサポートします。 大量のインストールが必要なワークロードや、VM に大幅なカスタマイズが必要なワークロードについては、[カスタム VM イメージを作成する](tutorial-custom-images.md)という方法もあります。 カスタム イメージを使うと、最大 300 個の VM をスケール セットに作成できます。


## <a name="create-a-scale-set"></a>スケール セットを作成する
[New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) を使用して仮想マシン スケール セットを作成します。 次の例では、*Windows Server 2016 Datacenter* プラットフォーム イメージを使用する *myScaleSet* という名前のスケール セットを作成します。 仮想ネットワーク用の Azure ネットワーク リソース、パブリック IP アドレス、およびロード バランサーが自動的に作成されます。 メッセージが表示されたら、スケール セット内の VM インスタンス用の自分の管理者資格情報を指定します。

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic"
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。


## <a name="deploy-sample-application"></a>サンプル アプリケーションをデプロイする
スケール セットをテストするには、基本的な Web アプリケーションをインストールします。 VM インスタンスに IIS をインストールするスクリプトをダウンロードして実行するために、Azure カスタム スクリプト拡張機能が使用されます。 この拡張機能は、デプロイ後の構成、ソフトウェアのインストール、その他の構成や管理タスクに役立ちます。 詳細については、「[Windows のカスタム スクリプト拡張機能](extensions-customscript.md)」を参照してください。

カスタム スクリプト拡張機能を使用して、基本的な IIS Web サーバーをインストールします。 次のように、IIS をインストールするカスタム スクリプト拡張機能を適用します。

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroupScaleSet" `
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
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```


## <a name="test-your-scale-set"></a>スケール セットのテスト
スケール セットが動いていることを確認するには、[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使用してロード バランサーのパブリック IP アドレスを取得します。 以下の例では、スケール セットの一部として作成された *myPublicIP* の IP アドレスを取得します。

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myPublicIPAddress" | select IpAddress
```

このパブリック IP アドレスを Web ブラウザーに入力します。 Web アプリが表示され、ロード バランサーによって負荷分散されたトラフィックの宛先となった VM のホスト名が表示されます。

![実行中の IIS サイト](./media/tutorial-create-vmss/running-iis-site.png)

動作しているスケール セットを確認するには、Web ブラウザーを強制的に最新の情報に更新して、アプリが動作しているすべての VM に対してロード バランサーからトラフィックが負荷分散されているのを見ることができます。


## <a name="management-tasks"></a>管理タスク
スケール セットのライフサイクルを通じて、1 つ以上の管理タスクを実行する必要がある場合があります。 さらに、各種ライフサイクルのタスクを自動化するスクリプトを作成するほうが便利な場合もあります。 Azure PowerShell には、これらのタスクを簡単に実行するための方法が用意されています。 一般的なタスクには、次のようなものがあります。

### <a name="view-vms-in-a-scale-set"></a>スケール セットの VM を表示する
スケール セット内の VM インスタンスの一覧を表示するには、次のように [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) を使用します。

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet"
```

次の出力例には、スケール セット内の 2 つの VM インスタンスが示されています。

```powershell
ResourceGroupName                 Name Location             Sku InstanceID ProvisioningState
-----------------                 ---- --------             --- ---------- -----------------
MYRESOURCEGROUPSCALESET   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUPSCALESET   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

特定の VM インスタンスに関する追加情報を表示するには、[Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) に `-InstanceId` パラメーターを追加します。 次の例では、VM インスタンス *1* に関する情報を表示しています。

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


### <a name="increase-or-decrease-vm-instances"></a>VM インスタンスを増減させる
現時点でスケール セットに存在するインスタンスの数を確認するには、[Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) と *sku.capacity* に対するクエリを使います。

```azurepowershell-interactive
Get-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -VMScaleSetName "myScaleSet" | `
    Select -ExpandProperty Sku
```

インスタンスの数が表示されたら、[Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) を使って、スケール セット内の仮想マシンの数を手動で増減させることができます。 次の例では、スケール セット内の VM の数を *3* に設定します。

```azurepowershell-interactive
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $scaleset
```

スケール セット内のインスタンスの数を指定どおりに更新する処理が終わるまでには、数分かかります。


### <a name="configure-autoscale-rules"></a>自動スケール ルールを構成する
スケール セット内のインスタンスの数を手動でスケールする代わりに、自動スケール ルールを定義しておくこともできます。 自動スケール ルールを使うと、スケール セット内のインスタンスが監視され、定義しておいたメトリックとしきい値に基づいて適切な対応が自動で実行されます。 以下の例は、CPU に対する負荷の平均が 5 分間に 60% を上回った場合にインスタンスの数を 1 つ増やしてスケールアウトするためのものです。 CPU に対する負荷の平均が 5 分間に 30% を下回った場合に、インスタンスが 1 つ減ってスケールインされます。

```azurepowershell-interactive
# Define your scale set information
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
$myScaleSetId = (Get-AzureRmVmss -ResourceGroupName $myResourceGroup -VMScaleSetName $myScaleSet).Id 

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5-minute period
$myRuleScaleDown = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId $myScaleSetId `
  -AutoscaleProfile $myScaleProfile
```

自動スケールの使用に関する詳しい設計情報については、[自動スケールのベスト プラクティス](/azure/architecture/best-practices/auto-scaling)に関する記事をご覧ください。


## <a name="next-steps"></a>次の手順
このチュートリアルでは、仮想マシン スケール セットを作成しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * カスタム スクリプト拡張機能を使用して、スケールする IIS サイトを定義する
> * スケール セットのロード バランサーを作成する
> * 仮想マシン スケール セットを作成する
> * スケール セット内のインスタンスの数を増減させる
> * 自動スケール ルールを作成する

次のチュートリアルでは、仮想マシンでの負荷分散の概念について詳しく説明します。

> [!div class="nextstepaction"]
> [仮想マシンを負荷分散する](tutorial-load-balancer.md)
