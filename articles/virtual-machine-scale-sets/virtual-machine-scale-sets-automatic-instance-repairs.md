---
title: Azure Virtual Machine Scale Sets による自動インスタンス修復
description: スケール セット内の VM インスタンスの自動修復ポリシーを構成する方法について説明します
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: avverma
ms.openlocfilehash: f335b0fb3396103c321d740bcf6d125e60e95086
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274579"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>プレビュー:Azure Virtual Machine Scale Sets の自動インスタンス修復

Azure Virtual Machine Scale Sets の自動インスタンス修復を有効にすると、正常なインスタンスのセットを維持することで、アプリケーションの高可用性を実現できます。 スケール セット内のインスタンスが[アプリケーション正常性拡張機能](./virtual-machine-scale-sets-health-extension.md)または [Load Balancer の正常性プローブ](../load-balancer/load-balancer-custom-probe-overview.md)によって報告された通り異常な状態であることが判明した場合、この機能により問題のあるインスタンスが削除され、新たに作成されたインスタンスにより置き換えられます。

> [!NOTE]
> このプレビュー機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。

## <a name="requirements-for-using-automatic-instance-repairs"></a>自動インスタンス修復を使用するための要件

**自動インスタンス修復プレビューのオプトイン**

REST API または Azure PowerShell を使用して、自動インスタンス修復のプレビューをオプトインします。 これらの手順により、サブスクリプションがプレビュー機能に登録されます。 この機能を使用するには、1 回限りのセットアップが必要です。 自動インスタンス修復プレビューにサブスクリプションが既に登録されている場合は、再度登録する必要はありません。 

REST API の使用 

1. [Features - Register](/rest/api/resources/features/register) を使用して機能に登録する 

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview/register?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registering"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

2. *State* が *Registered* に変更されるまで数分待ちます。 これを確認するには、次の API を使用します。

```
GET on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registered"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

3. *State* が *Registered* に変更されたら、次を実行します。

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

Azure PowerShell の使用

1. コマンドレット [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) の後に [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) を使用して、この機能に登録します。

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. *RegistrationState* が *Registered* に変更されるまで数分待ちます。 これを確認するには、次のコマンドレットを使用します。

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 応答は次のようになります。

| FeatureName                           | ProviderName            | registrationState       |
|---------------------------------------|-------------------------|-------------------------|
| RepairVMScaleSetInstancesPreview      | Microsoft.Compute       | 登録済み              |

3. *RegistrationState* が *Registered* に変更されたら、次のコマンドレットを実行します。

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**スケール セットでアプリケーションの正常性の監視を有効にする**

スケール セットでは、インスタンスのアプリケーションの正常性の監視が有効になっている必要があります。 これを行うには、[アプリケーション正常性拡張機能](./virtual-machine-scale-sets-health-extension.md)または [Load Balancer の正常性プローブ](../load-balancer/load-balancer-custom-probe-overview.md)を使用します。 この内、有効にできるのは一度に 1 つのみです。 アプリケーション正常性拡張機能または Load Balancer プローブは、仮想マシン インスタンス上に構成されたアプリケーション エンドポイントに ping を行い、アプリケーションの正常性状態を判断します。 この正常性状態は、インスタンスの正常性を監視し、必要に応じて修復を実行するために、スケール セットのオーケストレーターによって使用されます。

**正常性状態を提供するようにエンドポイントを構成する**

自動インスタンス修復ポリシーを有効にする前に、スケール セット インスタンスにアプリケーションの正常性状態を出力するように構成されたアプリケーション エンドポイントがあることを確認します。 インスタンスがこのアプリケーション エンドポイントで状態 200 (OK) を返すと、インスタンスは "健全" とマークされます。 それ以外の場合、次のシナリオを含め、インスタンスは "異常" とマークされます。

- 仮想マシン インスタンス内に、アプリケーションの正常性状態を提供するアプリケーション エンドポイントが構成されていない場合
- アプリケーション エンドポイントが正しく構成されていない場合
- アプリケーション エンドポイントに到達できない場合

"異常" とマークされているインスタンスの場合、自動修復はスケール セットによってトリガーされます。 エンドポイントの構成中に予期せずインスタンスが修復されることを回避するため、アプリケーション エンドポイントが正しく構成されていることを確認してから、自動修復ポリシーを有効にします。

**1 つの配置グループを有効にする**

現在、このプレビューは 1 つの配置グループとしてデプロイされたスケール セットに対してのみ使用できます。 スケール セットで自動インスタンス修復機能を使用するには、プロパティ *singlePlacementGroup* が *true* に設定されている必要があります。 [配置グループ](./virtual-machine-scale-sets-placement-groups.md#placement-groups)の詳細を確認してください。

**API バージョン**

自動修復ポリシーは、コンピューティング API バージョン 2018-10-01 以降でサポートされています。

**リソースまたはサブスクリプションの移動に関する制限事項**

このプレビューの一部として、自動修復ポリシーが有効になっている場合、スケール セットでは、リソースまたはサブスクリプションの移動は現在サポートされていません。

**Service Fabric スケール セットの制限事項**

現在、このプレビュー機能は Service Fabric スケール セットではサポートされていません。

## <a name="how-do-automatic-instance-repairs-work"></a>自動インスタンス修復のしくみ

自動インスタンス修復機能は、スケール セット内の個々のインスタンスの正常性の監視に依存します。 スケール セット内の VM インスタンスは、[アプリケーション正常性拡張機能](./virtual-machine-scale-sets-health-extension.md)または [Load Balancer の正常性プローブ](../load-balancer/load-balancer-custom-probe-overview.md)を使用してアプリケーションの正常性状態を出力するように構成できます。 インスタンスが異常であることが判明した場合、スケール セットでは正常でないインスタンスが削除されて新たに作成されたインスタンスにより置き換えられ、修復操作が実行されます。 この機能は、*automaticRepairsPolicy* オブジェクトを使用して、仮想マシン スケール セット モデルで有効にすることができます。

### <a name="batching"></a>バッチ処理

自動インスタンス修復操作は、バッチで実行されます。 どの時点においても、スケール セット内のインスタンスの 5% 以上が自動修復ポリシーを使用して修復されることはありません。 これにより、同時に異常が検出された場合に、多数のインスタンスが同時に削除され、再作成されるのを防ぐことができます。

### <a name="grace-period"></a>猶予期間

スケール セットで実行される PUT、PATCH、または POST アクション (再イメージ化、再デプロイ、更新など) が原因でインスタンスの状態変更操作が開始されると、そのインスタンスの修復操作は、猶予期間が経過した後にのみ実行されます。 猶予期間は、インスタンスが正常な状態に戻るまでにかけられる期間です。 猶予期間は、状態の変更が完了した後に開始されます。 これは、修復操作が不完全または偶発的に実行されることを回避するのに役立ちます。 猶予期間は、スケール セット内に新しく作成されたインスタンス (修復操作の結果として作成されたインスタンスを含む) に適用されます。 猶予期間は、ISO 8601 形式では分単位で指定し、プロパティ *automaticRepairsPolicy.gracePeriod* を使用して設定できます。 猶予期間は 30 分から 90 分の範囲で指定でき、既定値は 30 分です。

自動インスタンス修復プロセスは、次のように機能します。

1. [アプリケーション正常性拡張機能](./virtual-machine-scale-sets-health-extension.md)または [Load Balancer の正常性プローブ](../load-balancer/load-balancer-custom-probe-overview.md)は、スケール セット内の各仮想マシンのアプリケーション エンドポイントに ping を行って、各インスタンスのアプリケーションの正常性状態を取得します。
2. エンドポイントからステータス 200 (OK) が返された場合、インスタンスは "健全" とマークされます。 それ以外のすべての場合 (エンドポイントに到達できない場合を含む)、インスタンスは "異常" とマークされます。
3. インスタンスが異常であることが判明すると、スケール セットでは正常でないインスタンスが削除されて新たに作成されたインスタンスにより置き換えられ、修復操作がトリガされます。
4. インスタンスの修復はバッチで実行されます。 どの時点においても、スケール セット内のインスタンスの合計の 5% 以上が修復されることはありません。 スケール セットのインスタンス数が 20 未満の場合、異常なインスタンスの修復は一度に 1 つずつ実行されます。
5. 上記のプロセスは、スケール セット内のすべての異常なインスタンスが修復されるまで続行されます。

## <a name="instance-protection-and-automatic-repairs"></a>インスタンスの保護と自動修復

スケール セット内のインスタンスが *[スケール セット アクションから保護する保護ポリシー](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* を適用することで保護されている場合、そのインスタンスに対して自動修復は実行されません。

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>新しいスケール セットを作成するときに自動修復ポリシーを有効にする

新しいスケール セットの作成時に自動修復ポリシーを有効にするには、この機能をオプトインするためのすべての[要件](#requirements-for-using-automatic-instance-repairs)が満たされていることを確認します。 エンドポイントの構成中に意図せず修復がトリガーされないように、スケール セット インスタンスのアプリケーション エンドポイントを正しく構成する必要があります。 新しく作成されたスケール セットの場合、すべてのインスタンスの修復は、猶予期間が終了するまで待機した後に実行されます。 スケール セットで自動インスタンス修復を有効にするには、仮想マシン スケール セット モデルで *automaticRepairsPolicy* オブジェクトを使用します。

### <a name="rest-api"></a>REST API

次の例では、スケール セット モデルで自動インスタンス修復を有効にする方法を示します。 API バージョン 2018-10-01 以降をお使いください。

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

新しいスケール セットの作成時に自動インスタンス修復機能を有効にするには、[New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) コマンドレットを使用します。 このサンプル スクリプトでは、構成ファイルを使用したスケール セットと関連リソースの作成について説明します。[完全な仮想マシン スケール セットを作成します](./scripts/powershell-sample-create-complete-scale-set.md)。 自動インスタンス修復ポリシーを構成するには、パラメーター *EnableAutomaticRepair* と *AutomaticRepairGracePeriod* をスケール セットを作成するための構成オブジェクトに追加します。 次の例では、30 分の猶予期間でこの機能を有効にします。

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>既存のスケール セットを更新するときに自動修復ポリシーを有効にする

既存のスケール セットで自動修復ポリシーを有効にする前に、この機能をオプトインするためのすべての[要件](#requirements-for-using-automatic-instance-repairs)が満たされていることを確認してください。 エンドポイントの構成中に意図せず修復がトリガーされないように、スケール セット インスタンスのアプリケーション エンドポイントを正しく構成する必要があります。 スケール セットで自動インスタンス修復を有効にするには、仮想マシン スケール セット モデルで *automaticRepairsPolicy* オブジェクトを使用します。

### <a name="rest-api"></a>REST API

次の例では、猶予期間が 40 分のポリシーを有効にします。 API バージョン 2018-10-01 以降をお使いください。

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

[Update-AzVmss](/powershell/module/az.compute/update-azvmss) コマンドレットを使用して、既存のスケール セットの自動インスタンス修復機能の構成を変更します。 次の例では、猶予期間を 40 分に更新します。

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

## <a name="troubleshoot"></a>トラブルシューティング

**自動修復ポリシーを有効にできない**

"'プロパティ' 型のオブジェクトにメンバー ‘automaticRepairsPolicy’ が見つかりませんでした" というメッセージが表示された 'BadRequest' エラーが発生した場合は、仮想マシン スケール セットに使用されている API バージョンを確認してください。 この機能では、API バージョン 2018-10-01 以降が必要です。

**ポリシーが有効にもかかわらずインスタンスが修復されない**

インスタンスの猶予期間内である可能性があります。 これは、インスタンスの状態が変更されてから、修復が実行されるまでの待機時間です。 これは、修復が不完全または偶発的に実行されることを回避するためです。 修復操作は、インスタンスの猶予期間が完了した後に実行されます。

**スケール セット インスタンスのアプリケーションの正常性状態の表示**

仮想マシンスケール セットのインスタンスに対して [Get Instance View API](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) を使用すると、アプリケーションの正常性状態を表示できます。 Azure PowerShell では、コマンドレット [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) を *InstanceView* フラグと共に使用できます。 アプリケーションの正常性状態は、プロパティ *vmHealth* に表示されます。

## <a name="next-steps"></a>次のステップ

スケール セットの [アプリケーション正常性拡張機能](./virtual-machine-scale-sets-health-extension.md) または [Load Balancer の正常性プローブ](../load-balancer/load-balancer-custom-probe-overview.md) を構成する方法について説明します。
