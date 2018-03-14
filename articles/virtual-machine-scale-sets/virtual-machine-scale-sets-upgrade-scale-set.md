---
title: "Azure 仮想マシン スケール セットを変更する | Microsoft Docs"
description: "Azure 仮想マシン スケール セットを変更する"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: negat
ms.openlocfilehash: cdd1015f63e80b7ec51565c18f3440ce1828fb03
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2018
---
# <a name="modify-a-virtual-machine-scale-set"></a>仮想マシン スケール セットを変更する
この記事では、既存のスケール セットを変更する方法について説明します。 これには、スケール セットの構成を変更する方法、スケール セットで実行されているアプリケーションの構成を変更する方法、可用性を管理する方法などが含まれます。

## <a name="fundamental-concepts"></a>基本的な概念

### <a name="the-scale-set-model"></a>スケール セット モデル

スケール セットには、スケール セット全体の "*望ましい*" 状態をキャプチャする "スケール セット モデル" があります。 スケール セットのモデルのクエリを実行するには、以下を使用できます。

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` (詳しくは、[REST API のドキュメント](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/get)をご覧ください)

Powershell: `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (詳しくは、[Powershell のドキュメント](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss)をご覧ください)

CLI: `az vmss show -g {resourceGroupName} -n {vmSaleSetName}` (詳しくは、[CLI のドキュメント](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show)をご覧ください)

[resources.azure.com](https://resources.azure.com) または [Azure SDK](https://azure.microsoft.com/downloads/) を使用して、スケール セットのモデルのクエリを実行することもできます。

出力の正確な表示はコマンドに指定したオプションによって異なりますが、CLI からのサンプル出力を次に示します。

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



### <a name="the-scale-set-instance-view"></a>スケール セットのインスタンス ビュー

スケール セットには、スケール セット全体の現在の "*ランタイム*" 状態をキャプチャする "スケール セットのインスタンス ビュー" もあります。 スケール セットのインスタンス ビューのクエリを実行するには、以下を使用できます。

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version={apiVersion}` (詳しくは、[REST API のドキュメント](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview)をご覧ください)

Powershell: `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceView` (詳しくは、[Powershell のドキュメント](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss)をご覧ください)

CLI: `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName}` (詳しくは、[CLI のドキュメント](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view)をご覧ください)

[resources.azure.com](https://resources.azure.com) または [Azure SDK](https://azure.microsoft.com/downloads/) を使用して、スケール セットのインスタンス ビューのクエリを実行することもできます。

出力の正確な表示はコマンドに指定したオプションによって異なりますが、CLI からのサンプル出力を次に示します。

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

ご覧のように、これらのプロパティは、スケールセット内の VM の現在のランタイム状態の概要を示します。これには、スケール セットに適用される拡張機能の状態が含まれます (簡略化のために省略されています)。



### <a name="the-scale-set-vm-model-view"></a>スケール セットの VM モデル ビュー

スケール セットにモデル ビューがあるのと同様に、スケール セット内の各 VM には独自のモデル ビューがあります。 スケール セットのモデル ビューのクエリを実行するには、以下を使用できます。

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}?api-version={apiVersion}` (詳しくは、[REST API のドキュメント](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/get)をご覧ください)

Powershell: `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` (詳しくは、[Powershell のドキュメント](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm)をご覧ください)

CLI: `az vmss show -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` (詳しくは、[CLI のドキュメント](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show)をご覧ください)

[resources.azure.com](https://resources.azure.com) または [Azure SDK](https://azure.microsoft.com/downloads/) を使用して、スケール セット内の VM のモデルのクエリを実行することもできます。

出力の正確な表示はコマンドに指定したオプションによって異なりますが、CLI からのサンプル出力を次に示します。

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

ご覧のように、これらのプロパティは、スケール セット全体の構成ではなく VM 自体の構成を示します。 たとえば、スケール セット モデルにはプロパティとして `overprovision` がありますが、スケール セット内の VM のモデルにはありません。 この違いは、オーバープロビジョニングがスケール セット内の個々の VM ではなくスケール セット全体のプロパティであることが原因です (オーバープロビジョニングについて詳しくは、[このドキュメント](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning)をご覧ください)。



### <a name="the-scale-set-vm-instance-view"></a>スケール セットの VM インスタンス ビュー

スケール セットにインスタンス ビューがあるのと同様に、スケール セット内の各 VM には独自のインスタンス ビューがあります。 スケール セットのインスタンス ビューのクエリを実行するには、以下を使用できます。

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/instanceView?api-version={apiVersion}` (詳しくは、[REST API のドキュメント](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/getinstanceview)をご覧ください)

Powershell: `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -InstanceView` (詳しくは、[Powershell のドキュメント](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm)をご覧ください)

CLI: `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` (詳しくは、[CLI のドキュメント](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view)をご覧ください)

[resources.azure.com](https://resources.azure.com) または [Azure SDK](https://azure.microsoft.com/downloads/) を使用して、スケール セット内の VM のインスタンス ビューのクエリを実行することもできます。

出力の正確な表示はコマンドに指定したオプションによって異なりますが、CLI からのサンプル出力を次に示します。

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

ご覧のように、これらのプロパティは、VM 自体の現在のランタイム状態を示します。これには、スケール セットに適用される拡張機能が含まれます (簡略化のために省略されています)。




## <a name="how-to-update-global-scale-set-properties"></a>グローバル スケール セットのプロパティの更新方法

グローバル スケール セットのプロパティを更新するには、スケール セット モデルのプロパティを更新する必要があります。 この更新は以下を使用して実行できます。

REST API: `PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` (詳しくは、[REST API のドキュメント](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)をご覧ください)

Resource Manager テンプレート: REST API からプロパティを使用して Resource Manager テンプレートをデプロイして、グローバル スケール セットのプロパティを更新することもできます。

Powershell: `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -VirtualMachineScaleSet {scaleSetConfigPowershellObject}` (詳しくは、[Powershell のドキュメント](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss)をご覧ください)

CLI。 プロパティを変更するには: `az vmss update --set {propertyPath}={value}`。 スケール セットのリスト プロパティにオブジェクトを追加するには: `az vmss update --add {propertyPath} {JSONObjectToAdd}`。 スケール セットのリスト プロパティからオブジェクトを削除するには: `az vmss update --remove {propertyPath} {indexToRemove}`。 (詳しくは、[CLI のドキュメント](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update)をご覧ください)。 また、`az vmss create` コマンドを使用してスケール セットを既にデプロイした場合は、`az vmss create` コマンドをもう一度実行してスケール セットを更新できます。 これを行うには、変更するプロパティを除き、`az vmss create` コマンドのすべてのプロパティが以前と同じであることを確認する必要があります。



[resources.azure.com](https://resources.azure.com) または [Azure SDK](https://azure.microsoft.com/downloads/) を使用して、スケール セット モデルを更新することもできます。

スケール セット モデルを更新すると、スケール セットに作成された新しい VM に新しい構成が適用されます。 ただし、スケール セット内の既存の VM のモデルは、最新の全体的なスケール セット モデルで最新の状態にされている必要があります。 各 VM のモデルでは、`latestModelApplied` というブール型のプロパティがあります。このプロパティは、VM が最新の全体的なスケール セット モデルで最新の状態にされているかどうかを示します (`true` は VM が最新のモデルで最新の状態になっていることを意味します)。




## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>最新のスケール セット モデルで VM を最新の状態にする方法

スケール セットには、VM を最新のスケール セット モデルで最新の状態にする方法を決定する "アップグレード ポリシー" があります。 アップグレード ポリシーの 3 つのモードは次のとおりです。

- 自動: このモードでは、スケール セットは VM の停止順序を保証しません。 スケール セットは、すべての VM を同時に停止できます。 
- ローリング: このモードでは、スケール セットは更新をバッチでロールアウトします。バッチ間にはオプションの一時停止時間があります。
- 手動: このモードでは、スケール セット モデルの更新時に、既存の VM には何も行われません。 既存の VM を更新するには、各既存の VM の "手動アップグレード" を行う必要があります。 この手動アップグレードは以下を使用して行うことができます。

REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/manualupgrade?api-version={apiVersion}` (詳しくは、[REST API のドキュメント](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/updateinstances)をご覧ください)

Powershell: `Update-AzureRmVmssInstance -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` (詳しくは、[Powershell のドキュメント](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance)をご覧ください)

CLI: `az vmss update-instances -g {resourceGroupName} -n {vmScaleSetName} --instance-ids {instanceIds}` (詳しくは、[CLI のドキュメント](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update_instances)をご覧ください)。

[Azure SDK](https://azure.microsoft.com/downloads/) を使用して、スケール セット内の VM で手動アップグレードを行うこともできます。

>[!NOTE]
> Service Fabric クラスターは自動モードのみを使用できますが、更新は別途処理されます。 Service Fabric の更新について詳しくは、[Service Fabric のドキュメント](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade)をご覧ください。

>[!NOTE]
> グローバル スケール セットのプロパティに対する変更には、アップグレード ポリシーに従わない種類が 1 つあります。 これらは、スケール セット OS プロファイルに対する変更 (たとえば、管理者ユーザー名とパスワードなど) です。 これらのプロパティは、バージョン 2017-12-01 以降の API でのみ変更できます。 これらの変更は、スケール セット モデルの変更後に作成された VM にのみ適用されます。 既存の VM を最新の状態にするには、各既存の VM の "再イメージ化" を行う必要があります。 この再イメージ化は、以下を使用して行うことができます。

REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (詳しくは、[REST API のドキュメント](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage)をご覧ください)

Powershell: `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (詳しくは、[Powershell のドキュメント](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm)をご覧ください)

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (詳しくは、[CLI のドキュメント](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage)をご覧ください)。

[Azure SDK](https://azure.microsoft.com/downloads/) を使用して、スケール セット内の VM の再イメージ化を行うこともできます。




## <a name="properties-with-restrictions-on-modification"></a>変更に制限のあるプロパティ

### <a name="create-time-properties"></a>作成時のプロパティ

一部のプロパティは、スケール セットの初期作成時にのみ設定できます。 これには次のようなプロパティがあります。

- ゾーン
- イメージ参照発行元
- イメージ参照プラン

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>現在の値に基づいてのみ変更できるプロパティ

一部のプロパティは、現在の値に基づく例外付きで変更できます。 これには次のようなプロパティがあります。

- singlePlacementGroup: singlePlacementGroup が true の場合は、false に変更できます。 ただし、singlePlacementGroup が false の場合は、true に変更**できません**。
- サブネット: 元のサブネットと新しいサブネットが同じ仮想ネットワークにある限り、スケール セットのサブネットは変更できます。

### <a name="properties-that-require-deallocation-to-change"></a>変更するために割り当て解除が必要なプロパティ

一部のプロパティは、スケール セット内の VM の割り当てが解除されている場合に、特定の値にのみ変更できます。 これには次のようなプロパティがあります。

- SKU 名: スケール セットが現在存在するハードウェアで新しい VM SKU がサポートされていない場合は、SKU 名を変更する前にスケール セット内の VM の割り当てを解除する必要があります。 VM のサイズ変更について詳しくは、[この Azure ブログの投稿](https://azure.microsoft.com/blog/resize-virtual-machines/)をご覧ください。


## <a name="vm-specific-updates"></a>VM 固有の更新

特定の変更は、グローバル スケール セットのプロパティではなく特定の VM に適用できます。 現在サポートされている唯一の VM 固有の更新は、スケール セット内の VM との間でのデータ ディスクのアタッチ/デタッチです。 この機能はプレビュー段階にあります。 詳しくは、[プレビュー ドキュメント](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk)をご覧ください。

## <a name="scenarios-application-updates-os-updates-etc"></a>シナリオ: アプリケーションの更新、OS の更新など。

### <a name="application-updates"></a>アプリケーションの更新

拡張機能を通じてアプリケーションがスケール セットにデプロイされている場合、拡張機能の構成を更新すると、アップグレード ポリシーに従ってアプリケーションが更新されます。 たとえば、カスタム スクリプト拡張機能で実行するスクリプトの新しいバージョンがある場合は、新しいスクリプトを指すように fileUris プロパティを更新できます。 ただし、場合によっては、拡張機能の構成が変更されていない場合でも、強制的に更新したいことがあります (たとえば、スクリプトの URI を変更せずにスクリプトを更新した場合など)。 このような場合は、強制的に更新するように forceUpdateTag を変更することができます。 Azure プラットフォームはこのプロパティを解釈しないため、その値の変更は、拡張機能の動作に影響しません。 変更すると、拡張機能が強制的に再実行されるだけです。 forceUpdateTag について詳しくは、[拡張機能の REST API ドキュメント](https://docs.microsoft.com/rest/api/compute/virtualmachineextensions/createorupdate)をご覧ください。

カスタム イメージを通じてデプロイするアプリケーションでも一般的です。 このシナリオについては、次の「OS の更新」セクションで説明します

### <a name="os-updates"></a>OS の更新

プラットフォーム イメージを使用している場合は、imageReference を変更してイメージを更新することができます (詳しくは、[REST API のドキュメント](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)をご覧ください)。

>[!NOTE]
> プラットフォーム イメージでは、イメージ参照バージョンに "最新" を指定することが一般的です。 これは、スケール セットの作成、スケール アウト、再イメージ化中に、VM が使用可能な最新バージョンで作成されることを意味します。 ただし、新しいイメージ バージョンがリリースされたときに OS イメージが自動的に更新されることは意味**しません**。 これは別の機能であり、現在プレビュー段階にあります。 詳しくは、[OS アップグレードのドキュメント](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)をご覧ください。

カスタム イメージを使用している場合は、imageReference ID を更新してイメージを更新することができます (詳しくは、[REST API のドキュメント](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)をご覧ください)。

## <a name="examples"></a>例

### <a name="updating-the-os-image-for-your-scale-set"></a>スケール セットの OS イメージの更新

旧バージョンの Ubuntu LTS 16.04 を実行しているスケール セットがあり、新しいバージョンの Ubuntu LTS 16.04 (たとえば、バージョン 16.04.201801090) に更新するとします。 イメージ参照バージョン プロパティは一覧に含まれないため、以下のコマンドでこれらのプロパティを直接変更できます。

Powershell: `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -ImageReferenceVersion 16.04.201801090`

CLI: `az vmss update -g {resourceGroupName} -n {vmScaleSetName} --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090`


### <a name="updating-the-load-balancer-for-your-scale-set"></a>スケール セットのロード バランサーの更新

Azure Load Balancer を使用するスケール セットがあり、Azure Load Balancer を Azure Application Gateway で置換するとします。 スケール セットのロード バランサーとアプリケーション ゲートウェイのプロパティは一覧に含まれるため、プロパティを直接変更する代わりに一覧の要素を削除および追加するコマンドを使用できます。

Powershell:
```
# get the current model of the scale set and store it in a local powershell object named $vmss
> $vmss=Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName}

# create a local powershell object for the new desired IP configuration, which includes the referencerence to the application gateway
> $ipconf = New-AzureRmVmssIPConfig myNic -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name

# replace the existing IP configuration in the local powershell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
> $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf

# Update the model of the scale set with the new configuration in the local powershell object
> Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName} -virtualMachineScaleSet $vmss

```

CLI:
```
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0 # remove the load balancer backend pool from the scale set model
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0 # remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}' # add the application gateway backend pool to the scale set model
```

>[!NOTE]
> これらのコマンドでは、スケール セットの IP 構成とロード バランサーが 1 つだけであると想定されます。 複数ある場合は、0 以外のリスト インデックスを使用する必要があります。