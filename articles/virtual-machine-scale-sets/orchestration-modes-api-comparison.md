---
title: オーケストレーション モードの API の比較
description: 均一オーケストレーション モードとフレキシブル オーケストレーション モードの API の違いについて説明します。
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 65f3ea7217930b680cfb197092533989a3206894
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054573"
---
# <a name="orchestration-modes-api-comparison"></a>オーケストレーション モードの API の比較 

この記事では、仮想マシン スケール セットの均一オーケストレーション モードと[フレキシブル オーケストレーション](..\virtual-machines\flexible-virtual-machine-scale-sets.md) モードの間の API の違いを比較します。 均一仮想マシン スケール セットとフレキシブル仮想マシン スケール セットの詳細については、[オーケストレーション モード](virtual-machine-scale-sets-orchestration-modes.md)に関するページをご覧ください。


## <a name="instance-view"></a>インスタンス ビュー

| 均一 API | フレキシブルな代替手段 |
|-|-|
| 仮想マシン スケール セット インスタンス ビュー | 個々の VM でインスタンス ビューを取得する。Resource Graph を使用して電源状態に関するクエリを実行する |


## <a name="scale-set-lifecycle-batch-operations"></a>スケール セット ライフサイクル バッチ操作  

| 均一 API | フレキシブルな代替手段 |
|-|-|
| [割り当て解除](/rest/api/compute/virtualmachinescalesetvms/deallocate)  | [単一 VM API の呼び出し - 割り当て解除](/rest/api/compute/virtualmachines/deallocate)   |
| [削除](/rest/api/compute/virtualmachinescalesetvms/delete)  | [単一 VM API の呼び出し - 削除](/rest/api/compute/virtualmachines/delete)  |
| [インスタンス ビュー取得](/rest/api/compute/virtualmachinescalesetvms/getinstanceview)  | [単一 VM API の呼び出し - インスタンス ビュー](/rest/api/compute/virtualmachines/instanceview)  |
| [メンテナンス実行](/rest/api/compute/virtualmachinescalesetvms/performmaintenance)  | [単一 VM API の呼び出し - メンテナンス実行](/rest/api/compute/virtualmachines/performmaintenance)  |
| [電源オフ](/rest/api/compute/virtualmachinescalesetvms/poweroff)  | [単一 VM API の呼び出し - 電源オフ](/rest/api/compute/virtualmachines/poweroff)  |
| [Redeploy](/rest/api/compute/virtualmachinescalesetvms/redeploy)  | [単一 VM API の呼び出し - 再デプロイ](/rest/api/compute/virtualmachines/redeploy)  |
| [再イメージ化](/rest/api/compute/virtualmachinescalesetvms/reimage)  | [単一 VM API の呼び出し - 再イメージ化](/rest/api/compute/virtualmachines/reimage)  |
| [すべて再イメージ化](/rest/api/compute/virtualmachinescalesetvms/reimageall)  | 該当なし |
| [再起動](/rest/api/compute/virtualmachinescalesetvms/restart)  | [単一 VM API の呼び出し - 再起動](/rest/api/compute/virtualmachines/restart)  |
| [削除をシミュレート](/rest/api/compute/virtualmachinescalesetvms/simulateeviction) | [単一 VM API の呼び出し - 削除をシミュレート](/rest/api/compute/virtualmachines/simulateeviction)  |
| [Start](/rest/api/compute/virtualmachinescalesetvms/start) | [単一 VM API の呼び出し - 起動](/rest/api/compute/virtualmachines/start) |


## <a name="get-or-update"></a>取得または更新 

**Uniform API:**

仮想マシン スケール セット VM インスタンスの取得または更新:
- [Get](/rest/api/compute/virtualmachinescalesetvms/get) 
- [アップデート](/rest/api/compute/virtualmachinescalesetvms/update)

**フレキシブルな代替手段** 

単一 VM API の呼び出し:
- インスタンス保護の種類の動作に対する [ARM リソースのロック](../azure-resource-manager/management/lock-resources.md?tabs=json) 
    

## <a name="get-or-update-scale-set-vm-instances"></a>スケールセット VM インスタンスの取得または更新

| 均一 API | フレキシブルな代替手段 |
|-|-|
| [スケールセット VM の詳細の取得](/rest/api/compute/virtualmachinescalesetvms/get) | [仮想マシンの取得](/rest/api/compute/virtualmachines/get) |
| [スケールセット VM インスタンスの更新](/rest/api/compute/virtualmachinescalesetvms/update) | [バーチャルマシンの更新](/rest/api/compute/virtualmachines/update) |


## <a name="instance-protection"></a>インスタンス保護 

| 均一 API | フレキシブルな代替手段 |
|-|-|
| [インスタンスの保護](virtual-machine-scale-sets-instance-protection.md) | インスタンス保護の種類の動作に対する [ARM リソースのロック](../azure-resource-manager/management/lock-resources.md?tabs=json) | 


## <a name="list-instances"></a>インスタンスの一覧表示 

**Uniform API:**

`VMSS List Instances`: 
- 各インスタンスに関連付けられているスケール セット ID を返します

**フレキシブルな代替手段**

Azure Resource Graph: 

```armasm
resources 
| where type == "microsoft.compute/virtualmachines" 
| where properties.virtualMachineScaleSet.id contains "portalbb01" 
```

## <a name="scale-set-instance-operations"></a>スケールセットインスタンスの操作 

**Uniform API:**

仮想マシン スケール セットの操作:
- [インスタンスの更新](/rest/api/compute/virtual-machine-scale-sets/update-instances)
- [割り当て解除](/rest/api/compute/virtual-machine-scale-sets/deallocate)
- [メンテナンス実行](/rest/api/compute/virtual-machine-scale-sets/perform-maintenance)
- [電源オフ](/rest/api/compute/virtual-machine-scale-sets/power-off)
- [Redeploy](/rest/api/compute/virtual-machine-scale-sets/redeploy)
- [再イメージ化](/rest/api/compute/virtual-machine-scale-sets/reimage)
- [すべて再イメージ化](/rest/api/compute/virtual-machine-scale-sets/reimage-all)
- [再起動](/rest/api/compute/virtual-machine-scale-sets/restart)
- [オーケストレーション サービスの状態の設定](/rest/api/compute/virtual-machine-scale-sets/set-orchestration-service-state)
- [スタート](/rest/api/compute/virtual-machine-scale-sets/start)

**フレキシブルな代替手段**

個々の VM に対する操作を呼び出します。

仮想マシンの操作:
- [再イメージ化](/rest/api/compute/virtual-machines/reimage): 単一 VM API の呼び出し - エフェメラル OS の VM でのみ再イメージ化

## <a name="vm-extension"></a>VM 拡張機能

**Uniform API:**

仮想マシン スケール セット VM 拡張機能:
- [Create または Update](/rest/api/compute/virtual-machine-scale-set-vm-extensions/create-or-update)
- [削除](/rest/api/compute/virtual-machine-scale-set-vm-extensions/delete)
- [Get](/rest/api/compute/virtual-machine-scale-set-vm-extensions/get)
- [リスト](/rest/api/compute/virtual-machine-scale-set-vm-extensions/list)
- [アップデート](/rest/api/compute/virtual-machine-scale-set-vm-extensions/update) 

**フレキシブルな代替手段**

個々の VM に対する操作を呼び出します。


## <a name="networking"></a>ネットワーク 

| 均一 API | フレキシブルな代替手段 |
|-|-|
| ロードバランサーの NAT プール | 特定のインスタンスに NAT 規則を指定する | 

> [!IMPORTANT]
> ネットワークの動作は、スケールセット内で仮想マシンを作成する方法によって異なります。 **手動で追加された VM インスタンスには、** 既定の送信接続アクセス権があります。 **暗黙的に作成された VM インスタンスには** 、既定のアクセス権が設定されません。
>
> 柔軟なスケールセットのネットワークの詳細については、「 [スケーラブルなネットワーク接続](../virtual-machines/flexible-virtual-machine-scale-sets-migration-resources.md#create-scalable-network-connectivity)」を参照してください。


## <a name="scale-set-apis"></a>スケール セット API

**Uniform API:**

均一仮想マシン スケール セット API:
- [1 つの配置グループに変換](/rest/api/compute/virtual-machine-scale-sets/convert-to-single-placement-group)
- [強制的に回復 Service Fabric プラットフォーム更新ドメイン ウォーク](/rest/api/compute/virtual-machine-scale-sets/force-recovery-service-fabric-platform-update-domain-walk)

**フレキシブルな代替手段**

フレキシブル仮想マシン スケール セットではサポートされていません。


## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [さまざまなオーケストレーション モードについて学習する](virtual-machine-scale-sets-orchestration-modes.md)