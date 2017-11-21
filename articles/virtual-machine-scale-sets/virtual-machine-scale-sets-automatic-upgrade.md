---
title: "Azure 仮想マシン スケール セットを使用した OS の自動アップグレード | Microsoft Docs"
description: "スケール セット内の VM インスタンス上の OS を自動的にアップグレードする方法について説明します"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: guybo
ms.openlocfilehash: 32358b23bb0a0a878e986150dd992513579d61c4
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2017
---
# <a name="azure-virtual-machine-scale-set-automatic-os-upgrades"></a>Azure 仮想マシン スケール セットによる OS の自動アップグレード

OS イメージの自動アップグレードは、すべての VM を最新の OS イメージに自動的にアップグレードする Azure 仮想マシン スケール セットのプレビュー機能です。

OS の自動アップグレードには、次の特徴があります。

- 構成すると、イメージ発行元によって公開された最新の OS イメージが、ユーザーの介入なしでスケール セットに自動的に適用されます。
- 新しいプラットフォーム イメージが発行元によって発行されるたびに、インスタンス群をローリング方法でアップグレードします。
- アプリケーション正常性プローブと統合します (省略できますが、安全のために実行することを強くお勧めします)。
- すべての VM サイズで機能します。
- Windows と Linux のプラットフォーム イメージで機能します。
- 自動アップグレードはいつでも停止できます (OS のアップグレードは、手動でも開始できます)。
- VM の OS ディスクが、最新バージョンのイメージで作成された新しい OS ディスクに置き換えられます。 永続化されているデータ ディスクは保持されたまま、構成済みの拡張機能とカスタム データ スクリプトが実行されます。


## <a name="preview-notes"></a>プレビューに関するメモ 
プレビュー段階では次の制限と制約が適用されます。

- OS の自動アップグレードでは、[3 つの OS SKU](#supported-os-images) のみをサポートします。 SLA または保証はありません。 プレビュー段階の自動アップグレードは、実稼働環境のクリティカルなワークロードでは使用しないことをお勧めします。
- Service Fabric クラスターのスケール セットに対するサポートは、近日対応予定です。
- 仮想マシン スケール セットの OS の自動アップグレードでは、Azure ディスクの暗号化 (現在プレビュー段階です) は、現時点では**サポートされていません**。
- ポータル エクスペリエンスは近日対応予定です。


## <a name="register-to-use-automatic-os-upgrade"></a>OS の自動アップグレードを使用するために登録する
OS の自動アップグレード機能を使用するには、次のように、[Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) を使用してプレビュー プロバイダーを登録します。

```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName AutoOSUpgradePreview
```

登録状態が "*[登録済み]*" と報告されるまで、約 10 分かかります。 現在の登録状態は、[Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature) を使用して確認できます。 登録したら、次のように、[Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) を使用して、*Microsoft.Compute* プロバイダーが登録されることを確認します。

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

アプリケーションで正常性プローブを使用することをお勧めします。 正常性プローブのプロバイダー機能を登録するには、次のように [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) を使用します。

```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVmssHealthProbe
```

ここでも、登録状態が *[登録済み]* と報告されるまで、約 10 分かかります。 現在の登録状態は、[Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature) を使用して確認できます。 登録されたら、次のように、[Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) を使用して、*Microsoft.Network* プロバイダーが登録されることを確認します。

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```


## <a name="supported-os-images"></a>サポート対象の OS イメージ
現時点では、特定の OS プラットフォーム イメージのみがサポートされています。 現時点では、自分で作成したカスタム イメージを使用することはできません。 プラットフォーム イメージの *version* プロパティには *latest* を設定する必要があります。

現時点では、次の SKU がサポートされています (さらに追加される予定です)。
    
| 発行元               | プラン         |  SKU               | バージョン  |
|-------------------------|---------------|--------------------|----------|
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter | latest   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    | latest   |
| Canonical               | UbuntuServer  | 16.04 LTS          | latest   |


## <a name="application-health"></a>アプリケーションの正常性
OS のアップグレード中は、スケール セット内の VM インスタンスが、一度に 1 つの群でアップグレードされます。 アップグレードは、アップグレード済みの VM インスタンス上でユーザーのアプリケーションが正常である場合のみ続行されます。 アプリケーションがスケール セットの OS アップグレード エンジンに正常性通知を提供することをお勧めします。 既定では、OS のアップグレード中、プラットフォームは、VM の電源状態と拡張機能のプロビジョニング状態を考慮して、アップグレード後に VM インスタンスが正常であるかどうかを判断します。 VM インスタンスの OS のアップグレード中、VM インスタンス上の OS ディスクは、最新バージョンのイメージに基づく新しいディスクに置き換えられます。 OS のアップグレードが完了した後、構成済みの拡張機能がこれらの VM 上で実行されます。 VM 上のすべての拡張機能が正常にプロビジョニングされた場合にのみ、アプリケーションは正常であるとみなされます。 

スケール セットにアプリケーション正常性プローブをオプションで構成して、アプリケーションの進行中の状態に関する正確な情報をプラットフォームに提供できます。 アプリケーション正常性プローブは、正常性シグナルとして使用されるカスタム ロード バランサー プローブです。 スケール セットの VM インスタンスで実行されているアプリケーションは、外部 HTTP または TCP 要求に応答して、正常かどうかを示すことができます。 カスタム ロード バランサー プローブの動作方法の詳細については、「[Load Balancer プローブを理解する](../load-balancer/load-balancer-custom-probe-overview.md)」を参照してください。 アプリケーション正常性プローブは、OS の自動アップグレードでは必要ありませんが、使用することを強くお勧めします。

スケール セットが複数の配置グループを使用するように構成されている場合は、[Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) を使用するプローブを使用する必要があります。


### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>スケール セットに関するアプリケーション正常性プローブとしてのカスタム ロード バランサー プローブの構成
ベスト プラクティスとして、スケール セットの正常性のためのロード バランサー プローブを明示的に作成します。 既存の HTTP プローブまたは TCP プローブと同じエンドポイントを使用できますが、この正常性プローブでは、従来のロード バランサー プローブとは異なる動作が必要になる可能性があります。 たとえば、従来のロード バランサー プローブは、インスタンスの負荷が高すぎる場合に異常を返すことがありますが、OS の自動 アップグレード中にインスタンスの正常性を決定するには、その動作は適切ではない可能性があります。 2 分未満のプローブ率が高いプローブを構成してします。

ロード バランサー プローブは、スケール セットの *networkProfile* 内で参照でき、次のように、内部または公開されているロード バランサーに関連付けることができます。

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```


## <a name="enforce-an-os-image-upgrade-policy-across-your-subscription"></a>サブスクリプションに OS イメージのアップグレード ポリシーを適用する
安全なアップグレードのために、アップグレード ポリシーを適用することをお勧めします。 このポリシーは、サブスクリプション全体でアプリケーション正常性プローブを要求することができます。 次の Azure Resource Manager ポリシーは、OS イメージの自動アップグレード設定が構成されていないデプロイを拒否します。

1. 次のように、[Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) を使用して、組み込みの Azure Resource Manager ポリシー定義を取得します。

    ```powershell
    $policyDefinition = Get-AzureRmPolicyDefinition -Id "/providers/Microsoft.Authorization/policyDefinitions/465f0161-0087-490a-9ad9-ad6217f4f43a"
    ```

2. 次のように、[New-AzureRmPolicyAssignment](/powershell/module/AzureRM.Resources/New-AzureRmPolicyAssignment) を使用して、ポリシーをサブスクリプションに割り当てます。

    ```powershell
    New-AzureRmPolicyAssignment `
        -Name "Enforce automatic OS upgrades with app health checks" `
        -Scope "/subscriptions/<SubscriptionId>" `
        -PolicyDefinition $policyDefinition
    ```


## <a name="configure-auto-updates"></a>自動更新を構成する
自動アップグレードを構成するには、スケール セットのモデル定義で *automaticOSUpgrade* プロパティが *true* に設定されていることを確認します。 このプロパティは、Azure PowerShell または Azure CLI 2.0 を使用して設定できます。

次の例では、Azure PowerShell (4.4.1 以降) を使用して、*myResourceGroup* という名前のリソース グループ内の *myVMSS* という名前のスケール セットのための自動アップグレードを構成します。

```powershell
$rgname = myResourceGroup
$vmssname = myVMSS
$vmss = Get-AzureRmVMss -ResourceGroupName $rgname -VmScaleSetName $vmssname
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true
Update-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname -VirtualMachineScaleSet $vmss
```


次の例では、Azure CLI (2.0.20 以降) を使用して、*myResourceGroup* という名前のリソース グループ内の *myVMSS* という名前のスケール セットのための自動アップグレードを構成します。

```azure-cli
rgname="myResourceGroup"
vmssname="myVMSS"
az vmss update --name $vmssname --resource-group $rgname --set upgradePolicy.AutomaticOSUpgrade=true
```


## <a name="check-the-status-of-an-automatic-os-upgrade"></a>OS の自動アップグレードの状態を確認する
Azure PowerShell、Azure CLI 2.0、または REST API を使用して、スケール セット上で実行された最新の OS のアップグレードの状態を確認できます。

### <a name="azure-powershell"></a>Azure PowerShell
次の例では、Azure PowerShell (4.4.1 以降) を使用して、*myResourceGroup* という名前のリソース グループ内の *myVMSS* という名前のスケール セットの状態をチェックします。

```powershell
Get-AzureRmVmssRollingUpgrade -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
次の例では、Azure CLI (2.0.20 以降) を使用して、*myResourceGroup* という名前のリソース グループ内の *myVMSS* という名前のスケール セットの状態をチェックします。

```azure-cli
az vmss rolling-upgrade get-latest --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>REST API
次の例では、REST API を使用して、*myResourceGroup* という名前のリソース グループ内の *myVMSS* という名前のスケール セットの状態をチェックします。

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/rollingUpgrades/latest?api-version=2017-03-30`
```

GET 呼び出しは、次の例の出力に似たプロパティを返します。

```json
{
  "properties": {
    "policy": {
      "maxBatchInstancePercent": 20,
      "maxUnhealthyInstancePercent": 5,
      "maxUnhealthyUpgradedInstancePercent": 5,
      "pauseTimeBetweenBatches": "PT0S"
    },
    "runningStatus": {
      "code": "Completed",
      "startTime": "2017-06-16T03:40:14.0924763+00:00",
      "lastAction": "Start",
      "lastActionTime": "2017-06-22T08:45:43.1838042+00:00"
    },
    "progress": {
      "successfulInstanceCount": 3,
      "failedInstanceCount": 0,
      "inprogressInstanceCount": 0,
      "pendingInstanceCount": 0
    }
  },
  "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
  "location": "southcentralus"
}
```


## <a name="automatic-os-upgrade-execution"></a>OS の自動アップグレードの実行
アプリケーション正常性プローブの使用を拡張するには、次の手順でスケール セットの OS のアップグレードを実行します。

1. 20% を超えるインスタンスが異常な場合は、アップグレードを停止します。それ以外の場合は先に進みます。
2. アップグレードする次の VM インスタンス群 (インスタンスの合計数の最大 20% を持っている群) を識別します。
3. 次の VM インスタンス群の OS をアップグレードします。
4. アップグレードされたインスタンスの 20% を超えるインスタンスが異常である場合は、アップグレードを停止します。それ以外の場合は先に進みます。
5. アプリケーション正常性プローブが構成されている場合、アップグレードはプローブが正常になるまで 5 分間待機した後、すぐに次の群に進みます。それ以外の場合は、30 分間待機した後、次の群に進みます。
6. アップグレードするインスタンスが残っている場合は、次の群で手順 1 を実行します。それ以外の場合、アップグレードは完了します。

スケール セットの OS アップグレード エンジンは、すべての群をアップグレードする前に、VM インスタンスの全体的な正常性を確認します。 群のアップグレード中に、Azure データ センターで他の計画済みまたは計画外のメンテナンスが同時に発生されることがあり、これによって VM の可用性に影響が出る場合があります。 そのため、一時的に 20% を超えるインスタンスがダウンする可能性があります。 このような場合は、現在の群の最後に、スケール セットのアップグレードは停止します。


## <a name="deploy-with-a-template"></a>テンプレートを使用したデプロイ

次のテンプレートを使用して、自動アップグレードを使用するスケール セットをデプロイできます。<a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>自動ローリング アップグレード - Ubuntu 16.04 LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"> <img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## <a name="next-steps"></a>次のステップ
スケール セットを使用して OS の自動アップグレードを使用する方法の例については、[プレビュー機能の GitHub リポジトリ](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade)を参照してください。
