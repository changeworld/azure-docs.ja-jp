---
title: Azure 仮想マシン スケール セットを変更する | Microsoft Docs
description: Azure 仮想マシン スケール セットを変更する
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: negat
ms.openlocfilehash: fcca912a8120a51d2f0a454ef0a6341cd5882015
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="modify-a-virtual-machine-scale-set"></a>仮想マシン スケール セットを変更する
この記事では、既存の仮想マシン スケール セットを変更する方法について説明します。 タスクには、スケール セットの構成を変更する方法、スケール セットで実行されているアプリケーションの構成を変更する方法、可用性を管理する方法などが含まれます。

## <a name="fundamental-concepts"></a>基本的な概念

### <a name="scale-set-model"></a>スケール セット モデル

スケール セットには、スケール セット全体の "*望ましい*" 状態をキャプチャするモデルがあります。 スケール セットのモデルのクエリを実行するには、以下を使用できます。

* REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` 
   
  詳細については、[REST API のドキュメント](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/get)をご覧ください。

* PowerShell:

  `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}`
   
  詳細については、[PowerShell のドキュメント](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss)をご覧ください。

* Azure CLI: 

  `az vmss show -g {resourceGroupName} -n {vmSaleSetName}` 
   
  詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show)をご覧ください。

[Azure Resource Explorer (プレビュー)](https://resources.azure.com) または [Azure SDK](https://azure.microsoft.com/downloads/) を使用して、スケール セットのモデルを照会することもできます。

出力の正確な表示は、コマンドに指定したオプションによって異なります。 Azure CLI のサンプル出力を次に示します。

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
  .
  .
  .
}
```

ご覧のように、これらのプロパティはスケール セット全体に適用されます。



### <a name="scale-set-instance-view"></a>スケール セットのインスタンス ビュー

スケール セットには、スケール セット全体の現在の "*ランタイム*" 状態をキャプチャするインスタンス ビューもあります。 スケール セットのインスタンス ビューのクエリを実行するには、以下を使用できます。

* REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version={apiVersion}` 
   
  詳細については、[REST API のドキュメント](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview)をご覧ください。

* PowerShell: 

  `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceView` 
  
  詳細については、[PowerShell のドキュメント](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss)をご覧ください。

* Azure CLI: 

  `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName}` 
   
  詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view)をご覧ください。

[Azure Resource Explorer (プレビュー)](https://resources.azure.com) または [Azure SDK](https://azure.microsoft.com/downloads/) を使用して、スケール セットのインスタンス ビューを照会することもできます。

出力の正確な表示は、コマンドに指定したオプションによって異なります。 Azure CLI のサンプル出力を次に示します。

```
$ az vmss get-instance-view -g {resourceGroupName} -n {virtualMachineScaleSetName}
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
  .
  .
  .
}
```

ご覧のように、これらのプロパティは、スケール セットの VM の現在のランタイム状態の概要を示しています。 概要には、スケール セットに適用された拡張機能の状態が含まれています (簡潔にするために省略されています)。



### <a name="scale-set-vm-model-view"></a>スケール セットの VM のモデル ビュー

スケール セットにモデル ビューがあるのと同様に、スケール セット内の各 VM には独自のモデル ビューがあります。 スケール セットのモデル ビューのクエリを実行するには、以下を使用できます。

* REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}?api-version={apiVersion}` 
  
  詳細については、[REST API のドキュメント](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/get)をご覧ください。

* PowerShell: 

  `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` 
  
  詳細については、[PowerShell のドキュメント](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm)をご覧ください。

* Azure CLI: 

  `az vmss show -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` 
  
  詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show)をご覧ください。

[Azure Resource Explorer (プレビュー)](https://resources.azure.com) または [Azure SDK](https://azure.microsoft.com/downloads/) を使用して、スケール セットの VM のモデルを照会することもできます。

出力の正確な表示は、コマンドに指定したオプションによって異なります。 Azure CLI のサンプル出力を次に示します。

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
  .
  .
  .
}
```

ご覧のように、これらのプロパティは、スケール セット全体の構成ではなく VM 自体の構成を示します。 たとえば、スケール セット モデルにはプロパティとして `overprovision` がありますが、スケール セットの VM のモデルにはありません。 この違いがあるのは、オーバープロビジョニングが、スケール セットの個々の VM ではなくスケール セット全体のプロパティであるためです  (オーバープロビジョニングの詳細については、「[スケール セットの設計上の考慮事項](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning)」をご覧ください)。



### <a name="scale-set-vm-instance-view"></a>スケール セットの VM のインスタンス ビュー

スケール セットにインスタンス ビューがあるのと同様に、スケール セット内の各 VM には独自のインスタンス ビューがあります。 スケール セットのインスタンス ビューのクエリを実行するには、以下を使用できます。

* REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/instanceView?api-version={apiVersion}` 
 
  詳細については、[REST API のドキュメント](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/getinstanceview)をご覧ください。

* PowerShell: 

  `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -InstanceView` 
  
  詳細については、[PowerShell のドキュメント](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm)をご覧ください。

* Azure CLI: 

  `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` 
  
  詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view)をご覧ください。

[Azure Resource Explorer (プレビュー)](https://resources.azure.com) または [Azure SDK](https://azure.microsoft.com/downloads/) を使用して、スケール セットの VM のインスタンス ビューを照会することもできます。

出力の正確な表示は、コマンドに指定したオプションによって異なります。 Azure CLI のサンプル出力を次に示します。

```
$ az vmss get-instance-view -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
  .
  .
  .
}
```

ご覧のように、これらのプロパティは、VM 自体の現在のランタイム状態を示しています。 状態には、スケール セットに適用された拡張機能が含まれます (簡潔にするために省略されています)。




## <a name="techniques-for-updating-global-scale-set-properties"></a>グローバル スケール セットのプロパティを更新する方法

グローバル スケール セットのプロパティを更新するには、スケール セット モデルのプロパティを更新する必要があります。 この更新は以下を使用して実行できます。

* REST API: 

  `PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` 
  
  詳細については、[REST API のドキュメント](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)をご覧ください。

  REST API からプロパティを使用して Azure Resource Manager テンプレートをデプロイすることで、グローバル スケール セットのプロパティを更新することもできます。

* PowerShell: 

  `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -VirtualMachineScaleSet {scaleSetConfigPowershellObject}` 
  
  詳細については、[PowerShell のドキュメント](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss)をご覧ください。

* Azure CLI:

  * プロパティを変更する: `az vmss update --set {propertyPath}={value}` 
  
  * スケール セットのリスト プロパティにオブジェクトを追加する: `az vmss update --add {propertyPath} {JSONObjectToAdd}` 
  
  * スケール セットのリスト プロパティからオブジェクトを削除する: `az vmss update --remove {propertyPath} {indexToRemove}` 
  
  詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update)をご覧ください。 
  
  `az vmss create` コマンドを使用してスケール セットを既にデプロイしている場合は、`az vmss create` コマンドをもう一度実行してスケール セットを更新できます。 これを行うには、変更するプロパティを除き、`az vmss create` コマンドのすべてのプロパティが以前と同じであることを確認します。



[Azure Resource Explorer (プレビュー)](https://resources.azure.com) または [Azure SDK](https://azure.microsoft.com/downloads/) を使用して、スケール セット モデルを更新することもできます。

スケール セット モデルを更新すると、スケール セットに作成された新しい VM に新しい構成が適用されます。 ただし、スケール セットの既存の VM のモデルも、最新の全体的なスケール セット モデルで最新の状態になっている必要があります。 各 VM のモデルでは、`latestModelApplied` というブール型のプロパティによって、最新の全体的なスケール セット モデルで VM が最新の状態かどうかが示されます  (値が `true` の場合、最新のモデルで VM が最新の状態であることを意味します)。




## <a name="techniques-for-bringing-vms-up-to-date-with-the-latest-scale-set-model"></a>最新のスケール セット モデルで VM を最新の状態にする方法

スケール セットには、最新のスケール セット モデルで VM を最新の状態にする方法を決定する "*アップグレード ポリシー*" があります。 アップグレード ポリシーの 3 つのモードは次のとおりです。

- **自動**: このモードでは、スケール セットで VM の停止順序は保証されません。 スケール セットは、すべての VM を同時に停止させる可能性があります。 
- **ローリング**: このモードでは、スケール セットは更新をバッチでロールアウトします。必要に応じて、バッチ間の一時停止時間を設定します。
- **手動**: このモードでは、スケール セット モデルの更新時に、既存の VM は更新されません。 既存の VM を更新するには、各 VM を 手動でアップグレードする必要があります。 この手動アップグレードは以下を使用して行うことができます。

  - REST API: 
  
    `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/manualupgrade?api-version={apiVersion}` 
    
    詳細については、[REST API のドキュメント](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/updateinstances)をご覧ください。

  - PowerShell: 
  
    `Update-AzureRmVmssInstance -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` 
    
    詳細については、[PowerShell のドキュメント](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance)をご覧ください。

  - Azure CLI: 
  
    `az vmss update-instances -g {resourceGroupName} -n {vmScaleSetName} --instance-ids {instanceIds}` 
    
    詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update_instances)をご覧ください。

  [Azure SDK](https://azure.microsoft.com/downloads/) を使用して、スケール セットの VM を手動でアップグレードすることもできます。

>[!NOTE]
> Azure Service Fabric クラスターでは自動モードのみを使用できますが、更新は異なる方法で処理されます。 Service Fabric の更新の詳細については、[Service Fabric のドキュメント](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade)をご覧ください。

グローバル スケール セットのプロパティの変更の中で、スケール セットの OS プロファイルの変更は、アップグレード ポリシーには従いません  (管理者ユーザー名とパスワードなど)。これらのプロパティは、API バージョン 2017-12-01 以降でのみ変更できます。 これらの変更は、スケール セット モデルの変更後に作成された VM にのみ適用されます。 既存の VM を最新の状態にするには、各既存の VM を再イメージ化する必要があります。 VM の再イメージ化は、以下を使用して行います。

* REST API: 

  `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` 
  
  詳細については、[REST API のドキュメント](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage)をご覧ください。

* PowerShell: 

  `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` 
  
  詳細については、[PowerShell のドキュメント](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm)をご覧ください。

* Azure CLI: 

  `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` 
  
  詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage)をご覧ください。

[Azure SDK](https://azure.microsoft.com/downloads/) を使用して、スケール セット内の VM の再イメージ化を行うこともできます。




## <a name="properties-with-restrictions-on-modification"></a>変更に制限のあるプロパティ

### <a name="create-time-properties"></a>作成時のプロパティ

一部のプロパティは、スケール セットの初期作成時にのみ設定できます。 これには次のようなプロパティがあります。

- ゾーン
- イメージ参照発行元
- イメージ参照プラン

### <a name="properties-that-can-be-changed-based-on-the-current-value-only"></a>現在の値のみに基づいて変更できるプロパティ

一部のプロパティは、現在の値に基づいて変更できます (ただし、例外があります)。 これには次のようなプロパティがあります。

- `singlePlacementGroup`: `singlePlacementGroup` が true の場合は false に変更できます。 ただし、`singlePlacementGroup` が false の場合には、true に変更することは*できません*。
- `subnet`: 元のサブネットと新しいサブネットが同じ仮想ネットワーク内にあれば、スケール セットのサブネットを変更できます。

### <a name="properties-that-require-deallocation-to-change"></a>変更するために割り当て解除が必要なプロパティ

一部のプロパティは、スケール セットの VM の割り当てが解除されている場合にのみ、特定の値に変更できます。 これには次のようなプロパティがあります。

- `sku name`: スケール セットが現在存在するハードウェアで新しい VM SKU がサポートされていない場合は、`sku name` を変更する前に、スケール セットの VM の割り当てを解除する必要があります。 VM のサイズ変更について詳しくは、[この Azure ブログの投稿](https://azure.microsoft.com/blog/resize-virtual-machines/)をご覧ください。


## <a name="vm-specific-updates"></a>VM 固有の更新

特定の変更は、グローバル スケール セットのプロパティではなく、特定の VM に適用できます。 現在サポートされている唯一の VM 固有の更新は、スケール セット内の VM との間でのデータ ディスクのアタッチ/デタッチです。 この機能はプレビュー段階にあります。 詳しくは、[プレビュー ドキュメント](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk)をご覧ください。

## <a name="scenarios"></a>シナリオ

### <a name="application-updates"></a>アプリケーションの更新

拡張機能を使用してアプリケーションがスケール セットにデプロイされている場合、拡張機能の構成を更新すると、アップグレード ポリシーに従ってアプリケーションが更新されます。 たとえば、カスタム スクリプト拡張機能で実行するスクリプトの新しいバージョンがある場合は、その新しいスクリプトを参照するように `fileUris` プロパティを更新できます。 

拡張機能の構成が変更されていなくても、強制的に更新することが必要な場合もあります  (スクリプトの URI を変更せずにスクリプトを更新した場合など)。このような場合は、`forceUpdateTag` を変更して更新を適用できます。 Azure プラットフォームはこのプロパティを解釈しないため、その値の変更は、拡張機能の動作に影響しません。 変更すると、拡張機能が強制的に再実行されるだけです。 

`forceUpdateTag` の詳細については、[拡張機能の REST API ドキュメント](https://docs.microsoft.com/rest/api/compute/virtualmachineextensions/createorupdate)をご覧ください。

カスタム イメージを使用してアプリケーションをデプロイするのも一般的です。 このシナリオについては、次のセクションで説明します。

### <a name="os-updates"></a>OS の更新

プラットフォーム イメージを使用している場合は、`imageReference` を変更することでイメージを更新できます。 詳細については、[REST API のドキュメント](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)をご覧ください。

>[!NOTE]
> プラットフォーム イメージでは、イメージ参照バージョンに "latest" を指定するのが一般的です。 これは、スケール セットを作成、スケール アウト、再イメージ化するときに、VM が使用可能な最新バージョンで作成されることを意味します。 ただし、新しいイメージ バージョンがリリースされたときに OS イメージが自動的に更新されることは意味*しません*。 これは別の機能であり、現在プレビュー段階にあります。 詳細については、[OS の自動アップグレード](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)に関する記事をご覧ください。

カスタム イメージを使用している場合は、`imageReference` ID を更新することでイメージを更新できます。 詳細については、[REST API のドキュメント](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)をご覧ください。

## <a name="examples"></a>例

### <a name="update-the-os-image-for-your-scale-set"></a>スケール セットの OS イメージの更新

たとえば、古いバージョンの Ubuntu LTS 16.04 を実行しているスケール セットがあるとします。 新しいバージョンの Ubuntu LTS 16.04 (例： バージョン 16.04.201801090) に更新する必要があります。 イメージ参照バージョン プロパティはリストに含まれないため、次のコマンドを使用してこれらのプロパティを直接変更できます。

* PowerShell: 

  `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -ImageReferenceVersion 16.04.201801090`

* Azure CLI: 

  `az vmss update -g {resourceGroupName} -n {vmScaleSetName} --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090`


### <a name="update-the-load-balancer-for-your-scale-set"></a>スケール セットのロード バランサーの更新

Azure Load Balancer を使用するスケール セットがあり、このロード バランサーを Azure Application Gateway に置き換えるとします。 スケール セットのロード バランサーとアプリケーション ゲートウェイのプロパティはリストに含まれています。 そのため、プロパティを直接変更するのではなく、リストの要素を削除および追加するコマンドを使用できます。

PowerShell:
```
# Get the current model of the scale set and store it in a local PowerShell object named $vmss
> $vmss=Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName}

# Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
> $ipconf = New-AzureRmVmssIPConfig myNic -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name

# Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure load balancer) with the new IP configuration
> $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf

# Update the model of the scale set with the new configuration in the local PowerShell object
> Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName} -virtualMachineScaleSet $vmss

```

Azure CLI:
```
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0 # Remove the load balancer back-end pool from the scale set model
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0 # Remove the load balancer back-end pool from the scale set model; only necessary if you have NAT pools configured on the scale set
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}' # Add the application gateway back-end pool to the scale set model
```

>[!NOTE]
> これらのコマンドでは、スケール セットの IP 構成とロード バランサーが 1 つだけであると想定されます。 複数ある場合は、0 以外のリスト インデックスを使用する必要があります。