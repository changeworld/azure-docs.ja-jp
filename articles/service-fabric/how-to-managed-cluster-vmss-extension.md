---
title: Service Fabric 管理対象クラスター ノード タイプに仮想マシン スケール セット拡張機能を追加する (プレビュー)
description: Service Fabric 管理対象クラスター ノード タイプに仮想マシン スケール セット拡張機能を追加する方法
ms.topic: article
ms.date: 09/28/2020
ms.openlocfilehash: 10487bad4fce41c68b4e2cb90c311b986d709eee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91410272"
---
# <a name="add-a-virtual-machine-scale-set-extension-to-a-service-fabric-managed-cluster-node-type-preview"></a>Service Fabric 管理対象クラスター ノード タイプに仮想マシン スケール セット拡張機能を追加する (プレビュー)

Service Fabric 管理対象クラスター内の各ノード タイプは、仮想マシン スケール セットによって提供されます。 これにより、Service Fabric 管理対象クラスター ノード タイプに[仮想マシン スケール セット拡張機能](../virtual-machines/extensions/overview.md)を追加できます。

[Add-AzServiceFabricManagedNodeTypeVMExtension](https://docs.microsoft.com/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension?view=azps-4.7.0&preserve-view=true) PowerShell コマンドを使用して、仮想マシン スケール セット拡張機能をノード タイプに追加できます。

または、Azure Resource Manager テンプレート内の Service Fabric 管理対象クラスター ノード タイプで仮想マシン スケール セット拡張機能を使用することもできます。次に例を示します。

```json
{
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "[variables('sfApiVersion')]",
    "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
    "dependsOn": [
        "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
    ],
    "location": "[resourceGroup().location]",
    "properties": {
        "isPrimary": true,
        "vmInstanceCount": 3,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2",
        "vmImagePublisher": "MicrosoftWindowsServer",
        "vmImageOffer": "WindowsServer",
        "vmImageSku": "2019-Datacenter",
        "vmImageVersion": "latest",
        "vmExtensions": [{
            "name": "ExtensionA",
            "properties": {
                "publisher": "ExtensionA.Publisher",
                "type": "KeyVaultForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                }
            }
        }]
    }
}
```

Service Fabric 管理対象クラスター ノード タイプの構成の詳細については、[管理対象クラスター ノードタイプ](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

Service Fabric マネージド クラスターについて詳しくは、以下の記事をご覧ください。

> [!div class="nextstepaction"]
> [Service Fabric マネージド クラスターに関するよく寄せられる質問](./faq-managed-cluster.md)
