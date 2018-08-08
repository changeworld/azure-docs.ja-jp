---
title: Azure 仮想マシン スケール セットを使用した OS の自動アップグレード | Microsoft Docs
description: スケール セット内の VM インスタンス上の OS を自動的にアップグレードする方法について説明します
services: virtual-machine-scale-sets
documentationcenter: ''
author: yeki
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/03/2018
ms.author: yeki
ms.openlocfilehash: 6b20ef98e008d9c5d984ba29eed894b1c5ec8c09
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263250"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-upgrades"></a>Azure 仮想マシン スケール セットによる OS の自動アップグレード

OS イメージの自動アップグレードは、すべての VM を最新の OS イメージに自動的にアップグレードする Azure 仮想マシン スケール セットのプレビュー機能です。

OS の自動アップグレードには、次の特徴があります。

- 構成すると、イメージ発行元によって公開された最新の OS イメージが、ユーザーの介入なしでスケール セットに自動的に適用されます。
- 新しいプラットフォーム イメージが発行元によって発行されるたびに、ローリング方法でバッチごとにインスタンスをアップグレードします。
- アプリケーション正常性プローブと統合します (省略できますが、安全のために実行することを強くお勧めします)。
- すべての VM サイズで機能します。
- Windows と Linux のプラットフォーム イメージで機能します。
- 自動アップグレードはいつでも停止できます (OS のアップグレードは、手動でも開始できます)。
- VM の OS ディスクが、最新バージョンのイメージで作成された新しい OS ディスクに置き換えられます。 永続化されているデータ ディスクは保持されたまま、構成済みの拡張機能とカスタム データ スクリプトが実行されます。


## <a name="preview-notes"></a>プレビューに関するメモ 
プレビュー段階では次の制限と制約が適用されます。

- OS の自動アップグレードでは、[4 つの OS SKU](#supported-os-images) のみをサポートします。 SLA または保証はありません。 プレビュー段階の自動アップグレードは、実稼働環境のクリティカルなワークロードでは使用しないことをお勧めします。
- 仮想マシン スケール セットの OS の自動アップグレードでは、Azure Disk Encryption は、現時点では**サポートされていません**。


## <a name="register-to-use-automatic-os-upgrade"></a>OS の自動アップグレードを使用するために登録する
OS の自動アップグレード機能を使用するには、Azure PowerShell または Azure CLI 2.0 を使用してプレビュー プロバイダーを登録します。

### <a name="powershell"></a>PowerShell

1. [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) を使用して登録します。

     ```powershell
     Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName AutoOSUpgradePreview
     ```

2. 登録状態が "*[登録済み]*" と報告されるまで、約 10 分かかります。 現在の登録状態は、[Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature) を使用して確認できます。 

3. 登録したら、*Microsoft.Compute* プロバイダーが登録されていることを確認します。 次に示したのは、Azure PowerShell と [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) を使用した例です。

     ```powershell
     Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
     ```


### <a name="cli-20"></a>CLI 2.0

1. [az feature register](/cli/azure/feature#az-feature-register) を使用して登録します。

     ```azurecli
     az feature register --name AutoOSUpgradePreview --namespace Microsoft.Compute
     ```

2. 登録状態が "*[登録済み]*" と報告されるまで、約 10 分かかります。 現在の登録状態は、[az feature show](/cli/azure/feature#az-feature-show) を使用して確認できます。 
 
3. 登録したら、*Microsoft.Compute* プロバイダーが登録されていることを確認します。 次に示したのは、Azure CLI (2.0.20 以降) と [az provider register](/cli/azure/provider#az-provider-register) を使用した例です。

     ```azurecli
     az provider register --namespace Microsoft.Compute
     ```

> [!NOTE]
> Service Fabric クラスターには、アプリケーションの正常性に関する独自の概念があります。しかし、Service Fabric を使用しないスケール セットでは、ロード バランサーの正常性プローブを使用して、アプリケーションの正常性を監視します。 
>
> ### <a name="azure-powershell"></a>Azure PowerShell
>
> 1. 正常性プローブのプロバイダー機能を登録するには、次のように [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) を使用します。
>
>      ```powershell
>      Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVmssHealthProbe
>      ```
>
> 2. ここでも、登録状態が *[登録済み]* と報告されるまで、約 10 分かかります。 現在の登録状態は、[Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature) を使用して確認できます。
>
> 3. 登録されたら、次のように、[Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) を使用して、*Microsoft.Network* プロバイダーが登録されていることを確認します。
>
>      ```powershell
>      Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
>      ```
>
>
> ### <a name="cli-20"></a>CLI 2.0
>
> 1. 正常性プローブのプロバイダー機能を登録するには、次のように [az feature register](/cli/azure/feature#az-feature-register) を使用します。
>
>      ```azurecli
>      az feature register --name AllowVmssHealthProbe --namespace Microsoft.Network
>      ```
>
> 2. ここでも、登録状態が *[登録済み]* と報告されるまで、約 10 分かかります。 現在の登録状態は、[az feature show](/cli/azure/feature#az-feature-show) を使用して確認できます。 
>
> 3. 登録されたら、次のように、[az provider register](/cli/azure/provider#az-provider-register) を使用して、*Microsoft.Network* プロバイダーが登録されていることを確認します。
>
>      ```azurecli
>      az provider register --namespace Microsoft.Network
>      ```

## <a name="portal-experience"></a>ポータルでの操作
上記の登録手順に従うと、[Azure Portal](https://aka.ms/managed-compute) に移動して、スケール セットの OS 自動アップグレードを有効にし、アップグレードの進行状況を確認できます。

![](./media/virtual-machine-scale-sets-automatic-upgrade/automatic-upgrade-portal.png)


## <a name="supported-os-images"></a>サポート対象の OS イメージ
現時点では、特定の OS プラットフォーム イメージのみがサポートされています。 現時点では、自分で作成したカスタム イメージを使用することはできません。 プラットフォーム イメージの *version* プロパティには *latest* を設定する必要があります。

現時点では、次の SKU がサポートされています (さらに追加される予定です)。
    
| 発行元               | プラン         |  SKU               | Version  |
|-------------------------|---------------|--------------------|----------|
| Canonical               | UbuntuServer  | 16.04 LTS          | latest   |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter | latest   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    | latest   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Smalldisk | latest   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-with-Containers | latest   |



## <a name="application-health-without-service-fabric"></a>Service Fabric を使用しないアプリケーションの正常性
> [!NOTE]
> このセクションは、Service Fabric を使用しないスケール セットにのみ適用されます。 Service Fabric には、アプリケーションの正常性に関する独自の概念があります。 Service Fabric と OS 自動アップグレードを使用している場合、Service Fabric で実行されているサービスの高可用性を維持するために、新しい OS イメージは更新ドメインごとにロールアウトされます。 Service Fabric クラスターの持続性の特徴の詳細については、[こちらのドキュメント](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)を参照してください。

OS のアップグレード中は、スケール セット内の VM インスタンスが、一度に 1 つのバッチでアップグレードされます。 アップグレードは、アップグレード済みの VM インスタンス上でユーザーのアプリケーションが正常である場合のみ続行されます。 このため、アプリケーションがスケール セットの OS アップグレード エンジンに正常性シグナルを提供することが必要になります。 OS のアップグレード中、プラットフォームは、VM の電源状態と拡張機能のプロビジョニング状態を考慮して、アップグレード後に VM インスタンスが正常であるかどうかを判断します。 VM インスタンスの OS のアップグレード中、VM インスタンス上の OS ディスクは、最新バージョンのイメージに基づく新しいディスクに置き換えられます。 OS のアップグレードが完了した後、構成済みの拡張機能がこれらの VM 上で実行されます。 VM 上のすべての拡張機能が正常にプロビジョニングされた場合にのみ、アプリケーションは正常であるとみなされます。 

さらに、スケール セットにアプリケーション正常性プローブを構成して、アプリケーションの進行中の状態に関する正確な情報をプラットフォームに提供する "*必要があります*"。 アプリケーション正常性プローブは、正常性シグナルとして使用されるカスタム ロード バランサー プローブです。 スケール セットの VM インスタンスで実行されているアプリケーションは、外部 HTTP または TCP 要求に応答して、正常かどうかを示すことができます。 カスタム ロード バランサー プローブの動作方法の詳細については、「[Load Balancer プローブを理解する](../load-balancer/load-balancer-custom-probe-overview.md)」を参照してください。

スケール セットが複数の配置グループを使用するように構成されている場合は、[Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) を使用するプローブを使用する必要があります。

### <a name="important-keep-credentials-up-to-date"></a>重要: 資格情報を最新に保つ
スケール セットが外部のリソースにアクセスするときに資格情報を使用する場合、資格情報が最新に保たれているか確認する必要があります。 たとえば VM 拡張機能は、ストレージ アカウントの SAS トークンを使用するように構成されている場合があります。 証明書やトークンなどの資格情報の期限が切れている場合は、アップグレードは失敗し、VM の最初のバッチは障害が発生した状態になります。

リソースの認証エラーが発生した場合に、VM を復旧し、OS の自動アップグレードを再度有効にするための推奨手順は次のとおりです。

* 拡張機能に渡されたトークン (またはその他の資格情報) を再生成します。
* 外部エンティティと対話する VM 内から使用されるすべての資格情報が最新であることを確認します。
* 新しいトークンで、スケール セット モデルの拡張機能を更新します。
* 障害が発生したものを含むすべての VM インスタンスを更新する、更新されたスケール セットをデプロイします。 

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>スケール セットに関するアプリケーション正常性プローブとしてのカスタム ロード バランサー プローブの構成
スケール セットの正常性のためのロード バランサー プローブを明示的に作成する*必要があります*。 既存の HTTP プローブまたは TCP プローブと同じエンドポイントを使用できますが、この正常性プローブでは、従来のロード バランサー プローブとは異なる動作が必要になる可能性があります。 たとえば、従来のロード バランサー プローブは、インスタンスの負荷が高すぎる場合に異常を返すことがあります。 逆に言えば、OS の自動アップグレード中にインスタンスの正常性を決定するには、その動作は適切ではない可能性があります。 2 分未満のプローブ率が高いプローブを構成します。

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

### <a name="powershell"></a>Powershell
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

### <a name="cli-20"></a>CLI 2.0
次のように、ビルトインの Azure Resource Manager ポリシーを使用して、サブスクリプションにポリシーを割り当てます。

```azurecli
az policy assignment create --display-name "Enforce automatic OS upgrades with app health checks" --name "Enforce automatic OS upgrades" --policy 465f0161-0087-490a-9ad9-ad6217f4f43a --scope "/subscriptions/<SubscriptionId>"
```

## <a name="configure-auto-updates"></a>自動更新を構成する
自動アップグレードを構成するには、スケール セットのモデル定義で *automaticOSUpgrade* プロパティが *true* に設定されていることを確認します。 このプロパティは、Azure PowerShell または Azure CLI 2.0 を使用して設定できます。

### <a name="powershell"></a>PowerShell
次の例では、Azure PowerShell (4.4.1 以降) を使用して、*myResourceGroup* という名前のリソース グループ内の *myVMSS* という名前のスケール セットのための自動アップグレードを構成します。

```powershell
$rgname = myResourceGroup
$vmssname = myVMSS
$vmss = Get-AzureRmVMss -ResourceGroupName $rgname -VmScaleSetName $vmssname
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true
Update-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname -VirtualMachineScaleSet $vmss
```

### <a name="cli-20"></a>CLI 2.0
次の例では、Azure CLI (2.0.20 以降) を使用して、*myResourceGroup* という名前のリソース グループ内の *myVMSS* という名前のスケール セットのための自動アップグレードを構成します。

```azurecli
rgname="myResourceGroup"
vmssname="myVMSS"
az vmss update --name $vmssname --resource-group $rgname --set upgradePolicy.AutomaticOSUpgrade=true
```


## <a name="check-the-status-of-an-automatic-os-upgrade"></a>OS の自動アップグレードの状態を確認する
Azure PowerShell、Azure CLI 2.0、または REST API を使用して、スケール セット上で実行された最新の OS のアップグレードの状態を確認できます。

### <a name="powershell"></a>PowerShell
次の例では、Azure PowerShell (4.4.1 以降) を使用して、*myResourceGroup* という名前のリソース グループ内の *myVMSS* という名前のスケール セットの状態をチェックします。

```powershell
Get-AzureRmVmssRollingUpgrade -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS
```

### <a name="cli-20"></a>CLI 2.0
次の例では、Azure CLI (2.0.20 以降) を使用して、*myResourceGroup* という名前のリソース グループ内の *myVMSS* という名前のスケール セットの状態をチェックします。

```azurecli
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

1. 20% を超えるインスタンスが異常な場合は、アップグレードを停止します。それ以外の場合は続行します。
2. アップグレードする VM インスタンスの次のバッチ (インスタンスの合計数の最大 20% を持っているバッチ) を識別します。
3. VM インスタンスの次のバッチの OS をアップグレードします。
4. アップグレードされたインスタンスの 20% を超えるインスタンスが異常である場合は、アップグレードを停止します。それ以外の場合は続行します。
5. Service Fabric クラスターに属していないスケール セットの場合、プローブが正常になるまでアップグレードは最大 5 分間待機して、その後すぐに次のバッチが続行されます。 Service Fabric クラスターに属しているスケール セットの場合、スケール セットは 30 分間待機してから、次のバッチに移動します。
6. アップグレードするインスタンスが残っている場合は、次のバッチで手順 1 を実行します。それ以外の場合、アップグレードは完了します。

スケール セットの OS アップグレード エンジンは、すべてのバッチをアップグレードする前に、VM インスタンスの全体的な正常性を確認します。 バッチのアップグレード中に、Azure データ センターで他の計画済みまたは計画外のメンテナンスが同時に発生されることがあり、これによって VM の可用性に影響が出る場合があります。 そのため、一時的に 20% を超えるインスタンスがダウンする可能性があります。 このような場合は、現在のバッチの最後に、スケール セットのアップグレードは停止します。


## <a name="deploy-with-a-template"></a>テンプレートを使用したデプロイ

次のテンプレートを使用して、自動アップグレードを使用するスケール セットをデプロイできます。<a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>自動ローリング アップグレード - Ubuntu 16.04 LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## <a name="next-steps"></a>次の手順
スケール セットを使用して OS の自動アップグレードを使用する方法の例については、[プレビュー機能の GitHub リポジトリ](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade)を参照してください。
