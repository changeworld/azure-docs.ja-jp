---
title: Service Fabric のマネージド クラスター上の大規模な仮想マシン スケール セットのセカンダリ ノード タイプを構成する
description: この記事では、大規模な仮想マシン スケール セットとしてセカンダリ ノード タイプを構成する方法について説明します
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: 05177b519caa504ac9d931b07b9f6126a3851c0b
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545098"
---
# <a name="service-fabric-managed-cluster-node-type-scaling"></a>Service Fabric のマネージド クラスターのノード タイプのスケーリング

Service Fabric 管理対象クラスター内の各ノード タイプは、仮想マシン スケール セットによって提供されます。 マネージド クラスターのノード タイプで、[大規模な仮想マシン スケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)を作成するために、ノード タイプの定義にプロパティ `multiplePlacementGroups` が追加されました。 既定では、マネージド クラスターのノード タイプでは、配置グループ内で障害およびアップグレード ドメインの一貫性を維持するために、このプロパティを false に設定しますが、この設定では、ノード タイプが 100 台の VM を超えてスケーリングすることを制限します。 アプリケーションで大規模なスケール セットを効率的に使用できるかどうかを判断するには、[この一覧の要件](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md#checklist-for-using-large-scale-sets)を確認してください。

Azure Service Fabric のマネージド クラスター リソース プロバイダーでは、スケーリングを調整し、データにマネージド ディスクを使用します。このため、ステートフルとステートレスの両方のセカンダリ ノード タイプに対して大規模なスケール セットをサポートできます。

> [!NOTE]
> このプロパティは、ノード タイプのデプロイ後に変更することはできません。

## <a name="enable-large-virtual-machine-scale-sets-in-a-service-fabric-managed-cluster"></a>Service Fabric のマネージド クラスターで大規模な仮想マシン スケール セット有効にする
セカンダリ ノード タイプを大規模なスケール セットとして構成するには、**multiplePlacementGroups** プロパティを **true** に設定します。
> [!NOTE]
> このプロパティは、プライマリ ノード タイプでは設定できません。

* Service Fabric マネージド クラスター リソースの apiVersion は、**2021-05-01** 以降である必要があります。

```json
{
  "apiVersion": "[variables('sfApiVersion')]",
  "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
  "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
  "location": "[resourcegroup().location]",
  "dependsOn": [
    "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
  ],
  "properties": {
    "multiplePlacementGroups": true,
    "isPrimary": false,
    "vmImagePublisher": "[parameters('vmImagePublisher')]",
    "vmImageOffer": "[parameters('vmImageOffer')]",
    "vmImageSku": "[parameters('vmImageSku')]",
    "vmImageVersion": "[parameters('vmImageVersion')]",
    "vmSize": "[parameters('nodeTypeSize')]",
    "vmInstanceCount": "[parameters('nodeTypeVmInstanceCount')]",
    "dataDiskSizeGB": "[parameters('nodeTypeDataDiskSizeGB')]"
  }
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Service Fabric マネージド クラスターにアプリをデプロイする](./tutorial-managed-cluster-deploy-app.md)
