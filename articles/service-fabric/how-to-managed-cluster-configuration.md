---
title: Service Fabric マネージド クラスターを構成する
description: OS の自動アップグレード、NSG ルールなどのために Service Fabric マネージド クラスターを構成する方法について説明します。
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: 5be6422af6a1611bb8978ff4f0b76122cb146eb7
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864355"
---
# <a name="service-fabric-managed-cluster-configuration-options"></a>Service Fabric マネージド クラスターの構成オプション

クラスターの作成時に [Service Fabric マネージド クラスターの SKU](overview-managed-cluster.md#service-fabric-managed-cluster-skus) を選択するのに加えて、その他にも以下を含むさまざまな構成方法があります。

* ノードの種類に[仮想マシン スケール セット拡張機能](how-to-managed-cluster-vmss-extension.md)を追加する
* クラスターの[可用性ゾーンのスパン](how-to-managed-cluster-availability-zones.md)を構成する
* クラスターの[ネットワーク設定](how-to-managed-cluster-networking.md)を構成する
* [大規模な仮想マシン スケール セット](how-to-managed-cluster-large-virtual-machine-scale-sets.md)のノード タイプを構成する
* クラスター ノードの種類で[マネージド ID](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) を構成する
* クラスター ノードに対して[自動 OS アップグレード](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades)を有効にする
* クラスター ノードで [OS とデータ ディスクの暗号化](how-to-enable-managed-cluster-disk-encryption.md)を有効にする
* クラスターの[マネージド ディスクの種類](how-to-managed-cluster-managed-disk.md)の SKU を選択する
* ランタイム更新プログラムのクラスター [アップグレード オプション](how-to-managed-cluster-upgrades.md)を構成する

## <a name="enable-automatic-os-image-upgrades"></a>OS の自動アップグレードを有効にする

マネージド クラスター ノードが実行されている仮想マシンに対して、OS イメージの自動アップグレードを有効にすることができます。 仮想マシン スケール セットのリソースは、ユーザーに代わって Service Fabric マネージド クラスターによって管理されますが、クラスター ノードに対して OS イメージの自動アップグレードを有効にするかどうかはユーザーが選択できます。 [従来の Service Fabric](service-fabric-best-practices-infrastructure-as-code.md#virtual-machine-os-automatic-upgrade-configuration) クラスターと同様に、クラスターに対する意図しない中断を防ぐために、マネージド クラスター ノードは既定ではアップグレードされません。

OS の自動アップグレードを有効にするには、次のようにします。

* `2021-05-01` (またはそれ以降の) バージョンの *Microsoft ServiceFabric/managedclusters* リソースと *Microsoft servicefabric/managedclusters/nodetypes* リソースを使用する
* クラスターのプロパティ `enableAutoOSUpgrade` を *true* に設定する
* クラスターの nodeTypes のリソースのプロパティ `vmImageVersion` を *latest* に設定する

次に例を示します。

```json
    {
      "apiVersion": "2021-05-01",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-05-01",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

有効にすると、Service Fabric によってマネージド クラスター内で OS イメージのバージョンの照会と追跡が開始されます。 新しい OS バージョンが利用可能な場合、クラスター ノードの種類 (仮想マシン スケール セット) が一度に 1 つずつアップグレードされます。 Service Fabric ランタイムのアップグレードは、クラスター ノードの OS イメージのアップグレードが進行中でないことを確認した後にのみ実行されます。

アップグレードが失敗した場合、Service Fabric によって 24 時間後に再試行され、最大で 3 回再試行されます。 従来の (アンマネージド) Service Fabric のアップグレードと同様に、異常なアプリやノードによって OS イメージのアップグレードがブロックされることがあります。

イメージのアップグレードの詳細については、「[Azure 仮想マシン スケール セットを使用した OS イメージの自動アップグレード](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

[Service Fabric マネージド クラスターの概要](overview-managed-cluster.md)

[Service Fabric クラスター テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates)
