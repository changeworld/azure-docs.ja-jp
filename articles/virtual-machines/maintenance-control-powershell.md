---
title: PowerShell を使用した Azure 仮想マシンのメンテナンス コントロール
description: メンテナンス コントロールと PowerShell を使用して Azure VM にメンテナンスを適用するタイミングを制御する方法について学びます。
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: 58bdbdfcb82806dfa0b2779967bddf1242a8b702
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77115707"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>プレビュー:メンテナンス コントロールと Azure PowerShell による更新をコントロールする

メンテナンス コントロールを使用して、再起動が不要なプラットフォームの更新を管理します。 Azure は、信頼性、パフォーマンス、セキュリティを改善し、新機能を導入する目的で、インフラストラクチャを頻繁に更新しています。 ほとんどの更新は、ユーザーからは透過的に行われます。 ゲーム、メディア ストリーミング、金融取引などの一部のセンシティブなワークロードでは、数秒間であっても、メンテナンスのために VM がフリーズしたり切断したりすることが許されません。 メンテナンス コントロールが提供するオプションを使用すると、プラットフォームの更新を待機し、35 日間のローリング期間内にそれらの更新を適用できます。 

メンテナンス コントロールを使用すると、分離された VM に更新プログラムを適用するタイミングをユーザーが決定できます。

メンテナンス コントロールを使用すると、次のことができます。
- 更新プログラムを 1 つの更新プログラム パッケージにまとめる。
- 最大 35 日間待機して更新プログラムを適用する。 
- Azure Functions を使用して、メンテナンス期間のプラットフォームの更新を自動化する。
- メンテナンス構成が、複数のサブスクリプションやリソース グループ全体で機能するようにする。 

> [!IMPORTANT]
> メンテナンス コントロールは、現在パブリック プレビューの段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
> 

## <a name="limitations"></a>制限事項

- VM は、[専用ホスト](./linux/dedicated-hosts.md)上にあるか、[分離された VM サイズ](./linux/isolation.md)を使用して作成される必要があります。
- 35 日後に、更新プログラムが自動的に適用されます。
- ユーザーは、**リソース共同作成者**のアクセス権を持っている必要があります。


## <a name="enable-the-powershell-module"></a>PowerShell モジュールを有効にする

`PowerShellGet` が最新の状態であることを確認します。

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

Az. Maintenance PowerShell コマンドレットは、プレビュー段階であるため、Cloud Shell またはローカルの PowerShell インストールで `AllowPrerelease` パラメーターを指定して、モジュールをインストールする必要があります。   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

ローカルにインストールする場合は、管理者として、PowerShell プロンプトを開いてください。

*信頼されていないリポジトリ*からインストールするかどうかを確認するメッセージが表示される場合もあります。 モジュールをインストールするには、`Y` を入力するか、 **[すべてはい]** を選択します。



## <a name="create-a-maintenance-configuration"></a>メンテナンス構成を作成する

構成のコンテナーとして、リソースグループを作成します。 この例では、*myMaintenanceRG*という名前のリソース グループが、*eastus* に作成されます。 使用するリソース グループが既にある場合は、この部分をスキップし、残りの例のリソース グループ名を自分の所有者に置き換えることができます。

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

[New-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) を使用して、メンテナンス構成を作成します。 この例では、ホストを対象とした *myConfig* という名前のメンテナンス構成を作成します。 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

`-MaintenanceScope host` を使用して、そのメンテナンス構成が、ホストに対する更新をコントロールするために確実に使用されているか確認します。

同じ名前の構成を別の場所に作成しようとすると、エラーが発生します。 構成名は、サブスクリプションに対して一意である必要があります。

[Get-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration) を使用して、使用可能なメンテナンス構成に対してクエリを実行できます。

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>構成を割り当てる

[New-AzConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) を使用して、分離された VM または Azure Dedicated Host に構成を割り当てます。

### <a name="isolated-vm"></a>分離された VM

構成の ID を使用して、構成を VM に適用します。 `-ResourceType VirtualMachines` を指定し、`-ResourceName` には VM の名前、`-ResourceGroupName` には VM のリソース グループを指定します。 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>専用ホスト

専用ホストに構成を適用するには、`-ResourceType hosts` と `-ResourceParentName` を含める必要もあります。これらには、ホスト グループの名前と `-ResourceParentType hostGroups` を使用します。 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>保留中の更新プログラムを確認する

[Get-AzMaintenanceUpdat](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) を使用して、保留中の更新プログラムがあるかどうかを確認します。 ログインしている VM と異なる場合は、`-subscription` を使用して、VM の Azure サブスクリプションを指定します。

表示する更新プログラムがない場合、このコマンドは何も返しません。 それ以外の場合は、PSApplyUpdate オブジェクトが返されます。

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>分離された VM

分離された VM の保留中の更新プログラムを確認します。 この例では、読みやすくするために出力を表形式で表示しています。

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>専用ホスト

専用ホストの保留中の更新プログラムを確認します。 この例では、読みやすくするために出力を表形式で表示しています。 リソースの値は実際の値に置き換えてください。

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>更新プログラムの適用

[New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) を使用して、保留中の更新プログラムを適用します。

### <a name="isolated-vm"></a>分離された VM

分離された VM に更新プログラムを適用する要求を作成します。

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

成功した場合、このコマンドでは `PSApplyUpdate` オブジェクトが返されます。 `Get-AzApplyUpdate` コマンドで Name 属性を指定して、更新プログラムの状態を確認できます。 「[更新プログラムの状態の確認](#check-update-status)」をご覧ください。

### <a name="dedicated-host"></a>専用ホスト

専用ホストに更新プログラムを適用します。

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>更新プログラムの状態の確認
更新プログラムの状態を確認するには、[Get-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azapplyupdate) を使用します。 次に示すコマンドは、`-ApplyUpdateName` パラメーターに `default` を使用して、最新の更新プログラムの状態を表示します。 更新プログラムの名前を置き換えて ([New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) コマンドによって返されます)、特定の更新プログラムの状態を取得できます。

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```

### <a name="isolated-vm"></a>分離された VM

特定の仮想マシンの更新プログラムを確認します。

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>専用ホスト

専用ホストの更新プログラムを確認します。

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>メンテナンスの構成を削除する

[Remove-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) を使用して、メンテナンス構成を削除します。

```azurecli-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>次のステップ
詳細については、[メンテナンスと更新プログラム](maintenance-and-updates.md)に関するページを参照してください。
