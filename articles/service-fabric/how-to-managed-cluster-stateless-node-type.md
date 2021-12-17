---
title: ステートレス ノード タイプを使用した Service Fabric マネージド クラスターのデプロイ
description: Service Fabric マネージド クラスターでステートレス ノード タイプを作成してデプロイする方法について説明します。
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: a18c09e92cd8d78e314969fe15a802e4b7e2cc67
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546370"
---
# <a name="deploy-a-service-fabric-managed-cluster-with-stateless-node-types"></a>ステートレス ノード タイプを使用した Service Fabric マネージド クラスターのデプロイ

Service Fabric ノード タイプには、ある時点でステートフル サービスがノードに配置されるという固有の前提があります。 ステートレス ノード タイプは、ノード タイプのこの前提を緩和します。 この前提を緩和することで、ノードのステートレスノードタイプで、修復およびメンテナンス操作の制限の一部を取り除くことにより、高速なスケールアウト操作を利用できるようになります。

* プライマリ ノード タイプをステートレスに構成することはできません
* ステートレス ノード タイプには **2021-05-01** 以降の API バージョンが必要です
* これにより **multipleplacementgroup** プロパティが自動的に **true** に設定されます。詳細については [こちら](how-to-managed-cluster-large-virtual-machine-scale-sets.md)を参照してください。
* これにより、指定のノード タイプに対して最大 1,000 ノードのサポートが可能になります

サンプル テンプレートを使用できます。[Service Fabric のステートレス ノード タイプのテンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="enable-stateless-node-types-in-a-service-fabric-managed-cluster"></a>Service Fabric マネージド クラスターでステートレス ノード タイプを有効にする
ノード タイプ リソースで 1 つまたは複数のノード タイプをステートレスとして設定するには、**isStateless** プロパティを **true** に設定します。 ステートレス ノード タイプを使用して Service Fabric クラスターをデプロイする場合、クラスター内でステートレスではないプライマリ ノード タイプが少なくとも 1 つ必要になります。

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
    "isStateless": true,
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

## <a name="configure-stateless-node-types-with-multiple-availability-zones"></a>複数の Availability Zones を持つステートレス ノード タイプの構成
複数の Availability Zones にまたがるステートレス ノード タイプを構成するには、 [Availability Zones をまたがる Azure Service Fabric クラスター](.\service-fabric-cross-availability-zones.md)に従います。 

>[!NOTE]
> ゾーン回復性プロパティはクラスターレベルで設定する必要があり、このプロパティはインプレースでは変更できません。

## <a name="migrate-to-using-stateless-node-types-in-a-cluster"></a>クラスターでステートレス ノード タイプの使用に移行する
すべての移行シナリオで、新しいステートレス ノード タイプを追加する必要があります。 既存のノード タイプをステートレスに移行することはできません。 新しいステートレス ノード タイプを既存の Service Fabric マネージド クラスターに追加し、元のノード タイプをクラスターから削除できます。 

## <a name="next-steps"></a>次のステップ 

Service Fabric マネージド クラスターについて詳しくは、以下の記事をご覧ください。

> [!div class="nextstepaction"]
> [Service Fabric マネージド クラスターに関するよく寄せられる質問](./faq-managed-cluster.yml)
