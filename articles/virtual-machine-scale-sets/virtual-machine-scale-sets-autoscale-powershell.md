---
title: Azure PowerShell を使用した仮想マシン スケール セットの自動スケール | Microsoft Docs
description: Azure PowerShell を使用した仮想マシン スケール セットの自動スケール ルールを作成する方法
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 8928e56f353858234db314714d411a9c2990eb4e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2018
ms.locfileid: "30201333"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Azure PowerShell を使用して仮想マシン スケール セットを自動的にスケールする
スケール セットを作成するときに、実行する VM インスタンスの数を定義します。 アプリケーションの需要の変化に応じて、VM インスタンスの数を自動的に増減することができます。 自動スケール機能により、顧客のニーズに対応したり、アプリのライフ サイクルを通じて、アプリケーション パフォーマンスの変化に対応することができます。

この記事では、スケール セット内の VM インスタンスのパフォーマンスを監視する Azure PowerShell を使用して、自動スケール ルールを作成する方法を示します。 これらの自動スケール ルールは、これらのパフォーマンス メトリックに応じて VM インスタンスの数を増減します。 これらの手順は、[Azure CLI 2.0](virtual-machine-scale-sets-autoscale-cli.md) または [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md) を介して完了することもできます。


## <a name="prerequisites"></a>前提条件
自動スケール ルールを作成するには、既存の仮想マシン スケール セットが必要です。 スケール セットは、[Azure Portal](virtual-machine-scale-sets-create-portal.md)、[Azure PowerShell](virtual-machine-scale-sets-create-powershell.md)、または [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md) を使用して作成できます。

自動スケール ルールを作成しやすくするため、使用するスケール セット用にいくつかの変数を定義します。 次の例は、*myResourceGroup* という名前のリソース グループ内と *East US* 領域内の *myScaleSet* という名前のスケール セットの変数を定義します。 サブスクリプション ID は、[Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) を使用して取得します。 複数のサブスクリプションがアカウントに関連付けられている場合は、最初のサブスクリプションだけが返されます。 名前とサブスクリプション ID を次のように調整します。

```powershell
$mySubscriptionId = (Get-AzureRmSubscription).Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```


## <a name="create-a-rule-to-automatically-scale-out"></a>自動的にスケールアウトするルールの作成
アプリケーションの需要が増加すると、スケール セット内の VM インスタンスに対する負荷が増加します。 この増加した負荷が短期的な需要ではなく持続したものである場合は、スケール セット内の VM インスタンスの数を増やす自動スケール ルールを構成できます。 これらの VM インスタンスが作成され、アプリケーションがデプロイされると、スケール セットはロード バランサーを通じてそれらへのトラフィックの分散を開始します。 監視するメトリック (CPU やディスクなど)、指定されたしきい値をアプリケーションの負荷が満たす必要がある期間、およびスケール セットに追加する VM インスタンスの数を制御します。

CPU に対する負荷の平均が 5 分間に 70% を上回った場合に、スケール セット内の VM インスタンスの数を増やすルールを [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) を使用して作成してみましょう。 ルールがトリガーされると、VM インスタンスの数が 20% 増加します。 小数の VM インスタンスを持つスケール セットでは、`-ScaleActionScaleType` を除外して `-ScaleActionValue` だけを指定して、*1* つまたは *2* つのインスタンスずつ増やことができます。 多数の VM インスタンスがあるスケール セットでは、VM インスタンスを 10% または 20% 増やすとより適切になる場合があります。

このルールでは、次のパラメーターが使用されます。

| パラメーター               | 説明                                                                                                         | 値          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | スケール セット アクションを監視して適用するパフォーマンス メトリック。                                                   | Percentage CPU |
| *-TimeGrain*            | 分析のためにメトリックを収集する頻度。                                                                   | 1 分       |
| *-MetricStatistic*      | 分析のために収集したメトリックの集計方法を定義します。                                                | 平均        |
| *-TimeWindow*           | メトリックとしきい値を比較する前に監視する時間。                                   | 10 分      |
| *-Operator*             | しきい値に対してメトリック データを比較するために使用する演算子。                                                     | より大きい   |
| *-Threshold*            | 自動スケール ルールがアクションをトリガーする値。                                                      | 70%            |
| *-ScaleActionDirection* | ルールが適用されるときに、スケール セットをスケールアップするかスケールダウンするかを定義します。                                             | Increase (増加)       |
| *– ScaleActionScaleType* | VM インスタンスの数をパーセンテージで変更することを示します。                                 | Percent Change (変化率) |
| *-ScaleActionValue*     | ルールがトリガーされたときに VM インスタンスのパーセンテージを変更する必要があります。                                            | 20             |
| *-ScaleActionCooldown*  | 自動スケール アクションを有効にする時間を稼ぐため、ルールを再度適用する前に待機する時間。 | 5 分      |

次の例では、このスケール アップ ルールを保持する *myRuleScaleOut* という名前のオブジェクトを作成します。 *-MetricResourceId* は、サブスクリプション ID、リソース グループ名、およびスケール セット名に以前に定義した変数を使用します。

```powershell
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic Average `
  -TimeWindow 00:10:00 `
  -Operator GreaterThan `
  -Threshold 70 `
  -ScaleActionDirection Increase `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-automatically-scale-in"></a>自動的にスケールインするルールの作成
夜間や週末は、アプリケーションの需要が低下する可能性があります。 この低下した負荷が一定期間持続する場合、スケール セット内の VM インスタンスの数を減らす自動スケール ルールを構成できます。 このスケールイン アクションによって、現在の需要を満たすのに必要な数のインスタンスのみが実行されるようになるため、スケール セットの実行コストが削減されます。

CPU に対する負荷の平均が 10 分間に 30% を下回った場合にスケール セット内の VM インスタンスの数を減らすルールを [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) を使用して作成します。 ルールがトリガーされると、VM インスタンスの数は 20% 低下します。次の例では、このスケール アップ ルールを保持する *myRuleScaleDown* という名前のオブジェクトを作成します。 *-MetricResourceId* は、サブスクリプション ID、リソース グループ名、およびスケール セット名に以前に定義した変数を使用します。

```powershell
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:10:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20
```


## <a name="define-an-autoscale-profile"></a>自動スケール プロファイルの定義
自動スケール ルールをスケール セットに関連付ける場合に、プロファイルを作成します。 自動スケール プロファイルは、スケール セット容量の既定、最小、および最大を定義し、自動スケール ルールを関連付けます。 [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile) を使用して自動スケール プロファイルを作成します。 次の例では、既定および最小の容量として *2* つの VM インスタンスを設定し、最大の容量として *10* を定義しています。 上記の手順で作成されたスケールアウトおよびスケールイン ルールは次のように接続されます。

```powershell
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>自動スケール ルールをスケール セットに適用
最後の手順では、自動スケールのプロファイルをスケール セットに適用します。 これでアプリケーションの需要に基づいて、スケールが自動的にスケールインまたはスケールアウトすることができます。 次のように [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting) を使用して自動スケール プロファイルを適用します。

```powershell
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>スケール セット内のインスタンスの数の監視
VM インスタンスの数と状態を確認するには、[Get-AzureRmVmssVM](/powershell/module/AzureRM.Compute/Get-AzureRmVmssVM) を使用してスケール セット内のインスタンスの一覧を表示します。 状態は、VM インスタンスが自動的にスケールアウトするスケール セットとしてプロビジョニングされているかどうか、または自動的にスケールインするスケールとしてプロビジョニング解除されているかどうかを示します。 次の例は、*myResourceGroup* という名前のリソース グループ内の *myScaleSet* という名前のスケール セットの VM インスタンスの状態を表示します。

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="autoscale-based-on-a-schedule"></a>スケジュールに基づいた自動スケール
前の例では、CPU 使用率などの基本的なホスト メトリックを使用して、スケール セットを自動的にスケールインまたはスケールアウトしました。 スケジュールに基づいて自動スケール ルールを作成することもできます。 これらのスケジュール ベースのルールを使用すると、アプリケーションの需要の増加が予想されるコアタイムなどの前に、VM インスタンスの数を自動的にスケールアウトしたり、週末などの需要の低下が見込まれる時間にインスタンスの数を自動的にスケールインすることができます。

ホスト メトリックではなくスケジュールに基づいて自動スケール ルールを作成するには、Azure ポータルを使用します。 スケジュールに基づくルールは、Azure PowerShell では現在作成できません。


## <a name="next-steps"></a>次の手順
この記事では、自動スケール ルールを使用して、水平方向にスケーリングして、スケール セット内の VM インスタンスの*数*を増減する方法について説明しました。 垂直方向にスケーリングして、VM インスタンスの "*サイズ*" を増減することもできます。 詳細については、[仮想マシン スケール セットでの垂直方向の自動スケール](virtual-machine-scale-sets-vertical-scale-reprovision.md)に関するページを参照してください。

VM インスタンスの管理方法については、[Azure PowerShell を使用した仮想マシン スケール セットの管理](virtual-machine-scale-sets-windows-manage.md)に関するページを参照してください。

自動スケール ルールをトリガーするときにアラートを生成する方法について詳しくは、「[Azure Monitor で自動スケール操作を使用して電子メールと webhook アラート通知を送信する](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)」をご覧ください。 [Azure Monitor で監査ログを使用して電子メールと webhook アラート通知を送信する](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)こともできます。
