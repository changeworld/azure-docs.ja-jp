---
title: 既成の仮想ネットワークを Azure スケール セット テンプレートで参照する
description: 仮想ネットワークを既存の Azure 仮想マシン スケール セット テンプレートに追加する方法について説明します。
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 03/30/2021
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: f15fddc54f4b7c5a03843da1bcc11d1991b70d02
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076668"
---
# <a name="reference-an-existing-virtual-network-in-an-azure-scale-set-template"></a>既成の仮想ネットワークを Azure スケール セット テンプレートで参照する

この記事では、[基本のスケール セット テンプレート](virtual-machine-scale-sets-mvss-start.md)を変更して、新しい仮想ネットワークを作成するのではなく、既存の仮想ネットワークにデプロイする方法について説明します。

## <a name="prerequisites"></a>前提条件

前回の記事では、[基本的なスケール セット テンプレート](virtual-machine-scale-sets-mvss-start.md)を作成しました。 その以前のテンプレートが必要になり、それを変更することで、スケール セットを既存の仮想ネットワークにデプロイするテンプレートを作成できます。

## <a name="identify-subnet"></a>サブネットを識別する

まず、`subnetId` パラメーターを追加します。 この文字列はスケール セット構成に渡されます。これにより、スケール セットは仮想マシンをデプロイする作成済みのサブネットを特定できるようになります。 この文字列は、 の形式で指定する必要があります。

`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`


たとえば、名前が `myvnet`、サブネットが `mysubnet`、リソース グループが `myrg`、サブスクリプションが `00000000-0000-0000-0000-000000000000` の既存の仮想ネットワークにスケール セットをデプロイする場合、subnetId は以下になります。 

`/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
      },
      "adminPassword": {
        "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
      }
    },
```

## <a name="delete-extra-virtual-network-resource"></a>余分な仮想ネットワーク リソースを削除する

次に、`resources` 配列から仮想ネットワーク リソースを削除します。ここでは既存の仮想ネットワークを使用し、新しい仮想ネットワークをデプロイする必要はないためです。

```diff
    "variables": {},
    "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```
## <a name="remove-dependency-clause"></a>依存関係句を削除する

テンプレートをデプロイする前に仮想ネットワークが既に存在するので、スケール セットの `dependsOn` 句を仮想ネットワークに指定する必要はありません。 次の行を削除します。

```diff
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets",
        "name": "myScaleSet",
        "location": "[resourceGroup().location]",
        "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
        "sku": {
          "name": "Standard_A1",
          "capacity": 2
```

## <a name="pass-subnet-parameter"></a>サブネット パラメーターを渡す

最後に、ユーザーが設定した `subnetId` パラメーターを渡します (`resourceId` を使用して同じデプロイの vnet の ID は取得しません。これは、基本のスケール セット テンプレートで実行されます)。

```diff
                        "name": "myIpConfig",
                        "properties": {
                          "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                          }
                        }
                      }
```


## <a name="next-steps"></a>次のステップ

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
