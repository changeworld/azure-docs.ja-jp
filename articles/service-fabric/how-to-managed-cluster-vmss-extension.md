---
title: Service Fabric 管理対象クラスター ノード タイプに仮想マシン スケール セット拡張機能を追加する
description: Service Fabric 管理対象クラスター ノード タイプに仮想マシン スケール セット拡張機能を追加する方法
ms.topic: article
ms.date: 8/02/2021
ms.openlocfilehash: 3b0f15f2f83ddad0b8251642fc63db79581a5a97
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547059"
---
# <a name="virtual-machine-scale-set-extension-support-on-service-fabric-managed-cluster-node-types"></a>Service Fabric 管理対象クラスター ノード タイプでの仮想マシン スケール セット拡張機能のサポート

Service Fabric 管理対象クラスター内の各ノード タイプは、仮想マシン スケール セットによって提供されます。 これにより、Service Fabric 管理対象クラスター ノード タイプに[仮想マシン スケール セット拡張機能](../virtual-machines/extensions/overview.md)を追加できます。 拡張機能は、Azure VM でのデプロイ後の構成と自動化を提供する小さなアプリケーションです。 Azure プラットフォームでは、VM の構成、監視、セキュリティ、およびユーティリティのアプリケーションを対象とする多くの拡張機能をホストします。 公開元は、アプリケーションを取得し、それを拡張機能にまとめて、インストールを簡略化します。 ユーザーは必須パラメーターを指定するだけで済みます。 

## <a name="add-a-virtual-machine-scale-set-extension"></a>仮想マシン スケール セット拡張機能の追加
[Add-AzServiceFabricManagedNodeTypeVMExtension](/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension) PowerShell コマンドを使用して、仮想マシン スケール セット拡張機能を Service Fabric 管理対象クラスター ノード タイプに追加できます。

または、Azure Resource Manager テンプレート内の Service Fabric 管理対象クラスター ノード タイプに仮想マシン スケール セット拡張機能を追加することもできます。次に例を示します。

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
    "vmExtensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "type": "KeyVaultForWindows",
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "settings": {
          }
        }
      }
    ]
  }
}
```

Service Fabric 管理対象クラスター ノード タイプの構成の詳細については、[管理対象クラスター ノードタイプ](/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

Service Fabric マネージド クラスターについて詳しくは、以下の記事をご覧ください。

> [!div class="nextstepaction"]
> [Service Fabric マネージド クラスターに関するよく寄せられる質問](./faq-managed-cluster.yml)
