---
title: Service Fabric 管理対象クラスター ノード タイプに仮想マシン スケール セット拡張機能を追加する
description: Service Fabric 管理対象クラスター ノード タイプに仮想マシン スケール セット拡張機能を追加する方法
ms.topic: article
ms.date: 5/10/2021
ms.openlocfilehash: 0c0aac8d7804caeb6c08657b1ed36c45958a0ea5
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2021
ms.locfileid: "113033697"
---
# <a name="add-a-virtual-machine-scale-set-extension-to-a-service-fabric-managed-cluster-node-type"></a>Service Fabric 管理対象クラスター ノード タイプに仮想マシン スケール セット拡張機能を追加する

Service Fabric 管理対象クラスター内の各ノード タイプは、仮想マシン スケール セットによって提供されます。 これにより、Service Fabric 管理対象クラスター ノード タイプに[仮想マシン スケール セット拡張機能](../virtual-machines/extensions/overview.md)を追加できます。

[Add-AzServiceFabricManagedNodeTypeVMExtension](/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension) PowerShell コマンドを使用して、仮想マシン スケール セット拡張機能をノード タイプに追加できます。

または、Azure Resource Manager テンプレート内の Service Fabric 管理対象クラスター ノード タイプで仮想マシン スケール セット拡張機能を使用することもできます。次に例を示します。

```json
{
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "[variables('sfApiVersion')]",
    "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
    "dependsOn": [
        "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
    ],
    "location": "[resourceGroup().location]",
    "properties": {
        "isPrimary": true,
        "vmInstanceCount": 3,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2",
        "vmImagePublisher": "MicrosoftWindowsServer",
        "vmImageOffer": "WindowsServer",
        "vmImageSku": "2019-Datacenter",
        "vmImageVersion": "latest",
        "vmExtensions": [{
            "name": "ExtensionA",
            "properties": {
                "publisher": "ExtensionA.Publisher",
                "type": "KeyVaultForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                }
            }
        }]
    }
}
```

Service Fabric 管理対象クラスター ノード タイプの構成の詳細については、[管理対象クラスター ノードタイプ](/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

Service Fabric マネージド クラスターについて詳しくは、以下の記事をご覧ください。

> [!div class="nextstepaction"]
> [Service Fabric マネージド クラスターに関するよく寄せられる質問](./faq-managed-cluster.yml)
