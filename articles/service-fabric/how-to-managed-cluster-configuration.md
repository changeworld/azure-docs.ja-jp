---
title: Service Fabric マネージド クラスターを構成する
description: OS の自動アップグレード、NSG ルールなどのために Service Fabric マネージド クラスターを構成する方法について説明します。
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: aad982def0a1dd2e4b6f847a72e270337abcd460
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286034"
---
# <a name="service-fabric-managed-cluster-configuration-options"></a>Service Fabric マネージド クラスターの構成オプション

クラスターの作成時に [Service Fabric マネージド クラスターの SKU](overview-managed-cluster.md#service-fabric-managed-cluster-skus) を選択するのに加えて、その他にも以下を含むさまざまな構成方法があります。

* ノードの種類に[仮想マシン スケール セット拡張機能](how-to-managed-cluster-vmss-extension.md)を追加する
* クラスターの[可用性ゾーンのスパン](how-to-managed-cluster-availability-zones.md)を構成する
* クラスターの[ネットワーク設定](how-to-managed-cluster-networking.md)を構成する
* [大規模な仮想マシン スケール セット](how-to-managed-cluster-large-virtual-machine-scale-sets.md)のノード タイプを構成する
* クラスター ノードの種類で[マネージド ID](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) を構成する
* クラスター ノードで [OS とデータ ディスクの暗号化](how-to-managed-cluster-enable-disk-encryption.md)を有効にする
* セカンダリノードタイプで[自動スケール](how-to-managed-cluster-autoscale.md)を構成する
* [ノードタイプを手動でスケーリングする](how-to-managed-cluster-modify-node-type.md#scale-a-node-type-manually-with-portal)
* クラスターノードの種類で[自動OSイメージアップグレード](how-to-managed-cluster-modify-node-type.md#enable-automatic-os-image-upgrades)を有効にする
* ノードの種類に使用する[OS イメージ](how-to-managed-cluster-modify-node-type.md#modify-the-os-image-for-a-node-type-with-portal) を変更する
* ノードの種類の[配置プロパティ](how-to-managed-cluster-modify-node-type.md#configure-placement-properties-for-a-node-type-with-portal)を構成する
* クラスターの[マネージド ディスクの種類](how-to-managed-cluster-managed-disk.md)の SKU を選択する
* ランタイム更新プログラムのクラスター [アップグレード オプション](how-to-managed-cluster-upgrades.md)を構成する


## <a name="next-steps"></a>次のステップ

[Service Fabric マネージド クラスターの概要](overview-managed-cluster.md)

[Service Fabric クラスター テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates)
