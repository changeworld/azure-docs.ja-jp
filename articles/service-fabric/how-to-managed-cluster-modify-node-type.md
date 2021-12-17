---
title: Service Fabric のマネージド クラスターのノード タイプを構成または変更する
description: この記事では、マネージド クラスターのノード タイプを変更する方法について説明します。
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: 02d332774b98e7097bca0bbea6bc7216af0057fd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277260"
---
# <a name="service-fabric-managed-cluster-node-types"></a>Service Fabric のマネージド クラスターのノード タイプ

Service Fabric 管理対象クラスター内の各ノード タイプは、仮想マシン スケール セットによって提供されます。 マネージド クラスターでは、Service Fabric マネージド クラスター リソースプロバイダーを使用して必要な変更を行います。 クラスターの基礎となるすべてのリソースが、ユーザーの代わりにマネージド クラスター プロバイダーによって作成および抽象化されます。 リソース プロバイダーがリソースを管理することにより、クラスター ノード タイプの展開と管理が簡素化され、シード ノードの削除などの操作エラーが回避され、VM SKU の検証などのベスト プラクティスの適用が安全に使用できるようになります。

このドキュメントの残りの部分では、さまざまな設定を調整する方法について説明します。これには、ノード タイプの作成、ノード タイプのインスタンス数の調整、OS イメージの自動アップグレードの有効化、OS イメージの変更、配置プロパティの構成などが含まれます。 このドキュメントでは、Azure portal または Azure Resource Manager テンプレートを使用して変更を行う方法についても説明します。

> [!NOTE]
> 変更の進行中はノード タイプを変更できません。 要求された変更を完了してから別の変更を行うことをお勧めします。


## <a name="add-or-remove-a-node-type-with-portal"></a>ポータルを使用してノード タイプを追加または削除する

このチュートリアルでは、ポータルを使用してノード タイプを追加または削除する方法について説明します。

**ノード タイプを追加するには:**
1) [Azure portal](https://portal.azure.com/) にログインします

2) クラスター リソースの [概要] ページに移動します。 
![サンプルの [概要] ページ][overview]

3) `Settings` セクションの `Node types` を選択します![[ノード タイプ] ビュー][addremove]

4) 上部の `Add` をクリックして必要な情報を入力し、下部にある [追加] をクリックします。


**ノード タイプを削除するには:**
1) [Azure portal](https://portal.azure.com/) にログインします

2) クラスター リソースの [概要] ページに移動します。 
![サンプルの [概要] ページ][overview]

3) `Settings` セクションの `Node types` を選択します![[ノード タイプ] ビュー][addremove]

4) 削除する `Node Type` を選択し、上部の `Delete` をクリックします。

> [!NOTE]
> あるプライマリ ノード タイプが、クラスター内で唯一のプライマリ ノード タイプである場合、それを削除することはできません。


## <a name="add-a-node-type-with-a-template"></a>テンプレートを使用してノード タイプを追加する

**ARM テンプレートを使用してノード タイプを追加するには**

必要な値を持使用して別のリソースの種類 `Microsoft.ServiceFabric/managedclusters/nodetypes` を追加し、設定を有効にするためにクラスターの展開を実行します。

* Service Fabric マネージド クラスター リソースの apiVersion は、**2021-05-01** 以降である必要があります。

```json
          {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeType2Name'))]",
            "location": "[resourcegroup().location]",
            "dependsOn": [
              "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
            ],
            "properties": {
                "isPrimary": false,
                "vmImagePublisher": "[parameters('vmImagePublisher')]",
                "vmImageOffer": "[parameters('vmImageOffer')]",
                "vmImageSku": "[parameters('vmImageSku')]",
                "vmImageVersion": "[parameters('vmImageVersion')]",
                "vmSize": "[parameters('nodeType2VmSize')]",
                "vmInstanceCount": "[parameters('nodeType2VmInstanceCount')]",
                "dataDiskSizeGB": "[parameters('nodeType2DataDiskSizeGB')]",
                "dataDiskType": "[parameters('nodeType2managedDataDiskType')]"
           }
```
2 つのノード タイプの構成例については、[2 つのノード タイプの ARM テンプレートのサンプル](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/SF-Managed-Standard-SKU-2-NT)を参照してください


## <a name="scale-a-node-type-manually-with-portal"></a>ポータルを使用してノード タイプを手動でスケーリングする

このチュートリアルでは、ポータルを使用してノード タイプのノード数を変更する方法について説明します。

1) [Azure portal](https://portal.azure.com/) にログインします

2) クラスター リソースの [概要] ページに移動します。 
![サンプルの [概要] ページ][overview]

3) `Settings` セクションで `Node Types` を選択します 

4) 変更したい `Node type name` を選択します

5) `Node count` を目的の新しい値に調整し、下部にある `Apply` を選択します。 このスクリーンショットでは、`3` であった値が `5` に調整されています。
![ノード数の増加を示すサンプル][adjust-node-count]

6) `Provisioning state` には、完了するまでの `Updating` の状態が表示されるようになります。 完了すると、`Succeeded` がもう一度表示されます。
![ノード タイプの更新を示すサンプル][node-type-updating]


## <a name="scale-a-node-type-manually-with-a-template"></a>テンプレートを使用してノード タイプを手動でスケーリングする

ARM テンプレートを使用してノード タイプのノード数を調整するには、`vmInstanceCount` プロパティを新しい値で調整し、設定を有効にするためにクラスターの展開を実行します。

* Service Fabric マネージド クラスター リソースの apiVersion は、**2021-05-01** 以降である必要があります。

> [!NOTE]
> スケーリング要求が必須の最小値に違反する場合、マネージド クラスター プロバイダーではスケール調整がブロックされます。

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                ...
                "vmInstanceCount": "[parameters('nodeTypeVmInstanceCount')]",
                ...
            }
        }
}
```

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

## <a name="modify-the-os-image-for-a-node-type-with-portal"></a>ポータルを使用してノード タイプの OS イメージを変更する

このチュートリアルでは、ポータルを使用してノード タイプの OS イメージを変更する方法について説明します。

1) [Azure portal](https://portal.azure.com/) にログインします

2) クラスター リソースの [概要] ページに移動します。 
![サンプルの [概要] ページ][overview]

3) `Settings` セクションで `Node Types` を選択します 

4) 変更したい `Node type name` を選択します

5) `OS Image` を目的の新しい値に調整し、下部にある `Apply` を選択します。 ![OS イメージの変更を示すサンプル][change-os-image]

6) `Provisioning state` には `Updating` の状態が表示され、一度に 1 つのアップグレード ドメインの処理を行います。 完了すると、`Succeeded` がもう一度表示されます。
![ノード タイプの更新を示すサンプル][node-type-updating]


## <a name="modify-the-os-image-for-a-node-type-with-a-template"></a>テンプレートを使用してノード タイプの OS イメージを変更する

ARM テンプレートを使用してノード タイプに使用される OS イメージを変更するには、`vmImageSku` プロパティを新しい値で調整し、設定を有効にするためにクラスターの展開を実行します。 マネージド クラスター プロバイダーでは、アップグレード ドメインごとに各インスタンスが再イメージ化されます。

* Service Fabric マネージド クラスター リソースの apiVersion は、**2021-05-01** 以降である必要があります。

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                ...
                "vmImagePublisher": "[parameters('vmImagePublisher')]",
                "vmImageOffer": "[parameters('vmImageOffer')]",
                "vmImageSku": "[parameters('vmImageSku')]",
                "vmImageVersion": "[parameters('vmImageVersion')]",
                ...
            }
        }
}
```

## <a name="configure-placement-properties-for-a-node-type-with-portal"></a>ポータルを使用してノード タイプの配置プロパティを構成する

このチュートリアルでは、ポータルを使用してノード タイプの配置プロパティを変更する方法について説明します。

1) [Azure portal](https://portal.azure.com/) にログインします

2) クラスター リソースの [概要] ページに移動します。 
![サンプルの [概要] ページ][overview]

3) `Settings` セクションで `Node Types` を選択します 

4) 変更したい `Node type name` を選択します

5) `Placement properties` セクションで、必要な名前と値を追加し、下部にある `Apply` を選択します。 このスクリーンショットでは、`Name` `SSD_Premium` が使用され、`Value` は `true` になっています。
![配置プロパティの追加を示すサンプル][nodetype-placement-property]

6) `Provisioning state` には、完了するまでの `Updating` の状態が表示されるようになります。 完了すると、`Succeeded` がもう一度表示されます。
![ノード タイプの更新を示すサンプル][node-type-updating]

これで、[配置プロパティを使用して、特定のワークロードがクラスター内の特定の種類のノードだけで実行されるようにすることができます](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)。 

## <a name="configure-placement-properties-for-a-node-type-with-a-template"></a>テンプレートを使用してノード タイプの配置プロパティを構成する

ARM テンプレートを使用してノード タイプの配置プロパティを調整するには、`placementProperties` プロパティを新しい値で調整し、設定を有効にするためにクラスターの展開を実行します。 次のサンプルは、ノード タイプに対して 3 つの値が設定されていることを示しています。

* Service Fabric マネージド クラスター リソースの apiVersion は、**2021-05-01** 以降である必要があります。

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                "placementProperties": {
                    "PremiumSSD": "true",
                    "NodeColor": "green",
                    "SomeProperty": "5"
            }
        }
}
```
これで、[配置プロパティを使用して、特定のワークロードがクラスター内の特定の種類のノードだけで実行されるようにすることができます](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)。 

## <a name="modify-the-vm-sku-for-a-node-type"></a>ノード タイプの VM SKU を変更する

Service Fabric マネージド クラスターでは VM SKU のインプレース変更がサポートされていませんが、従来よりは簡素化されています。 そのためには、次の操作を実行する必要があります。
* 必要な VM SKU を指定した[新しいノード タイプを作成します](how-to-managed-cluster-modify-node-type.md#add-or-remove-a-node-type-with-portal)。
* ワークロードを移行します。 1 つの方法は、[配置プロパティを使用して、特定のワークロードがクラスター内の特定の種類のノードだけで実行されるようにすることです](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)。 
* [古いノード タイプを削除します](how-to-managed-cluster-modify-node-type.md#add-or-remove-a-node-type-with-portal)



## <a name="configure-multiple-managed-disks-preview"></a>複数のマネージド ディスクを構成する (プレビュー)
Service Fabric のマネージド クラスターでは、既定では、1 つのマネージド ディスクが構成されます。 次のオプションのプロパティと値を構成することにより、クラスター内のノードの種類にマネージド ディスクを追加できます。 ドライブ文字、ディスクの種類、およびディスクごとのサイズを指定できます。

次に示すように、Resource Manager テンプレートで `additionalDataDisks` プロパティと必要なパラメーターを宣言してマネージド ディスクを構成します。

**機能の要件**
* LUN はディスクごとに一意である必要があり、予約済みの LUN 0 を使用することはできません
* ディスク文字では、予約済みの文字 C および D は使用できず、作成後に変更することはできません。 指定されない場合、S が既定値として使用されます。
* [サポートされているディスクの種類](how-to-managed-cluster-managed-disk.md)を指定する必要があります
* Service Fabric マネージド クラスター リソースの apiVersion は **2021-11-01-preview** 以降である必要があります。

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                "additionalDataDisks": {
                    "lun": "1",
                    "diskSizeGB": "50",
                    "diskType": "Standard_LRS",
                    "diskLetter": "S" 
            }
        }
     }
```

[使用可能なパラメーターの完全なリスト](/azure/templates/microsoft.servicefabric/2021-11-01/managedclusters)を参照してください。

## <a name="configure-the-service-fabric-data-disk-drive-letter-preview"></a>Service Fabric データ ディスクのドライブ文字を構成する (プレビュー)
Service Fabric のマネージド クラスターでは、既定で Service Fabric データ ディスクが構成され、ノードの種類のすべてのノードでドライブ文字が自動的に構成されます。 この省略可能なプロパティと値を構成することにより、ドライブ文字のマッピングに特定の要件がある場合に、Service Fabric データ ディスクの文字を指定して取得できます。

**機能の要件**
* ディスク文字では、予約済みの文字 C および D は使用できず、作成後に変更することはできません。 指定されない場合、S が既定値として使用されます。
* Service Fabric マネージド クラスター リソースの apiVersion は **2021-11-01-preview** 以降である必要があります。

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                "dataDiskLetter": "S"      
            }
        }
     }
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Service Fabric マネージド クラスター ノード タイプを自動スケーリングする](how-to-managed-cluster-autoscale.md)
> [!div class="nextstepaction"]
> [Service Fabric マネージド クラスターの構成オプション](how-to-managed-cluster-configuration.md)
> [!div class="nextstepaction"]
> [Service Fabric マネージド クラスターにアプリをデプロイする](./tutorial-managed-cluster-deploy-app.md)


[overview]: ./media/how-to-managed-cluster-modify-node-type/sfmc-overview.png
[node-type-updating]: ./media/how-to-managed-cluster-modify-node-type/sfmc-adjust-node-type-updating.png
[adjust-node-count]: ./media/how-to-managed-cluster-modify-node-type/sfmc-adjust-node-counts.png
[change-nodetype-os-image]: ./media/how-to-managed-cluster-modify-node-type/sfmc-change-os-image.png
[nodetype-placement-property]: ./media/how-to-managed-cluster-modify-node-type/sfmc-nodetype-placement-property.png
[addremove]: ./media/how-to-managed-cluster-modify-node-type/sfmc-addremove-node-type.png

