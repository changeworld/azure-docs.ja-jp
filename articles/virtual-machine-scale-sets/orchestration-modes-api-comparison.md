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
ms.openlocfilehash: 20278ebae9e590a99293c37df506b7d07ff79ddc
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122868408"
---
# <a name="preview-orchestration-modes-api-comparison"></a>プレビュー: オーケストレーション モードの API の比較 

この記事では、仮想マシン スケール セットの均一オーケストレーション モードと[フレキシブル オーケストレーション](..\virtual-machines\flexible-virtual-machine-scale-sets.md) モードの間の API の違いを比較します。 均一仮想マシン スケール セットとフレキシブル仮想マシン スケール セットの詳細については、[オーケストレーション モード](virtual-machine-scale-sets-orchestration-modes.md)に関するページをご覧ください。

> [!IMPORTANT]
> フレキシブル オーケストレーション モードの仮想マシン スケール セットは現在、パブリック プレビュー段階です。 後述するパブリック プレビュー機能を使用するためには、オプトイン手順が必要です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


## <a name="instance-view"></a>インスタンス ビュー

| 均一 API | フレキシブルな代替手段 |
|-|-|
| 仮想マシン スケール セット インスタンス ビュー | 個々の VM でインスタンス ビューを取得する。Resource Graph を使用して電源状態に関するクエリを実行する |


## <a name="scale-set-lifecycle-batch-operations"></a>スケール セット ライフサイクル バッチ操作  

| 均一 API | フレキシブルな代替手段 |
|-|-|
| 仮想マシン スケール セット VM ライフサイクル バッチ操作:  | 特定のインスタンスで単一 VM API を呼び出す: |
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

### <a name="uniform-api"></a>均一 API
仮想マシン スケール セット VM インスタンスの取得または更新:
- [Get](/rest/api/compute/virtualmachinescalesetvms/get) 
- [アップデート](/rest/api/compute/virtualmachinescalesetvms/update)

### <a name="flexible-alternative"></a>フレキシブルな代替手段 
単一 VM API の呼び出し:
- インスタンス保護の種類の動作に対する [ARM リソースのロック](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources?tabs=json) 


## <a name="list-instances"></a>インスタンスの一覧表示 

### <a name="uniform-api"></a>均一 API
`VMSS List Instances`: 
- 本プレビューの間、各インスタンスに関連付けられているスケール セット ID を返します 

### <a name="flexible-alternative"></a>フレキシブルな代替手段
Azure Resource Graph: 

```armasm
resources 
| where type == "microsoft.compute/virtualmachines" 
| where properties.virtualMachineScaleSet.id contains "portalbb01" 
```

## <a name="scale-set-operations"></a>スケール セットの操作 

### <a name="uniform-api"></a>均一 API
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

### <a name="flexible-alternative"></a>フレキシブルな代替手段
個々の VM に対する操作を呼び出します。


## <a name="vm-extension"></a>VM 拡張機能

### <a name="uniform-api"></a>均一 API
仮想マシン スケール セット VM 拡張機能:
- [Create または Update](/rest/api/compute/virtual-machine-scale-set-vm-extensions/create-or-update)
- [削除](/rest/api/compute/virtual-machine-scale-set-vm-extensions/delete)
- [Get](/rest/api/compute/virtual-machine-scale-set-vm-extensions/get)
- [リスト](/rest/api/compute/virtual-machine-scale-set-vm-extensions/list)
- [アップデート](/rest/api/compute/virtual-machine-scale-set-vm-extensions/update) 

### <a name="flexible-alternative"></a>フレキシブルな代替手段
個々の VM に対する操作を呼び出します。


## <a name="networking"></a>ネットワーク 

### <a name="uniform-api"></a>均一 API
- NAT プール / ポート フォワーディング 
- フレキシブル スケール セットでは NAT プールはサポートされません  

### <a name="flexible-alternative"></a>フレキシブルな代替手段
- 各 VM で個々の NAT 規則を設定する


## <a name="scale-set-apis"></a>スケール セット API

### <a name="uniform-api"></a>均一 API
均一仮想マシン スケール セット API:
- [1 つの配置グループに変換](/rest/api/compute/virtual-machine-scale-sets/convert-to-single-placement-group)
- [強制的に回復 Service Fabric プラットフォーム更新ドメイン ウォーク](/rest/api/compute/virtual-machine-scale-sets/force-recovery-service-fabric-platform-update-domain-walk)

### <a name="flexible-alternative"></a>フレキシブルな代替手段
フレキシブル仮想マシン スケール セットではサポートされていません。


## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [さまざまなオーケストレーション モードについて学習する](virtual-machine-scale-sets-orchestration-modes.md)