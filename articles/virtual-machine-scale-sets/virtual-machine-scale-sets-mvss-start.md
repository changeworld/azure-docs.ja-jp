---
title: "Azure 仮想マシン スケール セット: 実行可能な最小のスケール セット | Microsoft Docs"
description: "実行可能な最小のスケール セット テンプレートを作成する方法を説明します"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/14/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: eee63d76941f327304348262f00afafe8413cb6b
ms.lasthandoff: 03/09/2017


---

# <a name="about-this-tutorial"></a>このチュートリアルについて

[Azure Resource Manager テンプレート](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment)は、関連するリソースのグループをデプロイするための優れた方法です。 このチュートリアル シリーズでは、実行可能な最小のスケール セット テンプレートを作成する方法と、そのテンプレートをさまざまなシナリオに適合するように変更する方法を示します。 すべての例は、こちらの [github リポジトリ](https://github.com/gatneil/mvss)を基にしています。 このチュートリアルで示されている diff は、このリポジトリの分岐に対して `git diff` を実行した結果です。 これらのテンプレートと diff の目的は単純化するためであり、本格的な例を示すためではありません。 スケール セット テンプレートの完全な例については、[Azure クイックスタート テンプレート github リポジトリ](https://github.com/Azure/azure-quickstart-templates)で、`vmss` という文字列を含むフォルダーを検索してください。

## <a name="a-minimum-viable-scale-set"></a>実行可能な最小のスケール セット

実行可能な最小のスケール セット テンプレートは、[こちら](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json)で確認できます。 テンプレートを熟知している場合は、「次の手順」セクションに進んで、このテンプレートを他のシナリオに適合するように変更する方法を参照できます。 ただし、テンプレートに慣れていない場合は、以下の項目ごとの説明が役に立ちます。 最初に、このテンプレートを作成する diff (`git diff master minimum-viable-scale-set`) を項目ごとに調べてみましょう。

まず、テンプレートの `$schema` と `contentVersion`を定義します。 `$schema` は、テンプレートの言語のバージョンを定義し、Visual Studio 構文の強調表示や同様の検証機能で使用されます。 `contentVersion` は、実際には Azure ではまったく使用されません。 これは、テンプレートのバージョンを追跡する際に役立ちます。

```diff
+{
+  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
+  "contentVersion": "1.0.0.0",
```

次に、2 つのパラメーター (`adminUsername` と `adminPassword`) を定義します。 これらのパラメーターは、デプロイ時にユーザーによって指定される値です。 `adminUsername` は単なる `string` ですが、`adminPassword` はシークレットであるため、`securestring` 型にします。 これらのパラメーターは、後ほどスケール セット構成に渡されます。

```diff
+  "parameters": {
+    "adminUsername": {
+      "type": "string"
+    },
+    "adminPassword": {
+      "type": "securestring"
+    }
+  },
```

Resource Manager テンプレートでは、テンプレート内で後ほど使用される変数を定義することもできます。 この例では変数を使用しないため、JSON オブジェクトは空のままにします。

```diff
+  "variables": {},
```

次に、テンプレートのリソースを指定します。ここでは、何を実際にデプロイするかを定義します。 `parameters` と `variables` とは異なり (これらは JSON オブジェクトです)、`resources` は JSON オブジェクトの JSON リストです。

```diff
+  "resources": [
```

すべてのリソースの `type`、`name`、`apiVersion`、および`location` を指定する必要があります。 最初のリソースは、名前が `myVnet`、apiVersion が `2016-03-30`、種類が `Microsft.Network/virtualNetwork` のリソースです。 リソースの種類に対する最新の API バージョンを確認するには、[Azure REST API ドキュメント](https://docs.microsoft.com/rest/api/)を参照してください。

```diff
+    {
+      "type": "Microsoft.Network/virtualNetworks",
+      "name": "myVnet",
+      "apiVersion": "2016-12-01",
```

仮想ネットワークの場所を指定するには、[Resource Manager テンプレート関数](../azure-resource-manager/resource-group-template-functions.md)を使用します。この関数は、`"[<template-function>]"` のように、引用符と角かっこで囲む必要があります。 この例では、resourceGroup 関数を使用します。この関数には引数がなく、JSON オブジェクトとこのデプロイのデプロイ先であるリソース グループに関するメタデータを返します。 リソース グループは、デプロイ時にユーザーによって設定されます。 その時点で、JSON オブジェクトから場所を取得するために、JSON オブジェクトを `.location` でインデックス化します。

```diff
+      "location": "[resourceGroup().location]",
```


各 Resource Manager リソースには、リソース固有の構成を指定するための独自の `properties` セクションがあります。 この例では、仮想ネットワークにはプライベート IP アドレス範囲 `10.0.0.0/16` を使用するサブネットが&1; つだけ存在するように指定しています。 スケール セットは、常に&1; つのサブネットに収まります。 複数のサブネットにまたがることできません。

```diff
+      "properties": {
+        "addressSpace": {
+          "addressPrefixes": [
+            "10.0.0.0/16"
+          ]
+        },
+        "subnets": [
+          {
+            "name": "mySubnet",
+            "properties": {
+              "addressPrefix": "10.0.0.0/16"
+            }
+          }
+        ]
+      }
+    },
```

必須のプロパティである `type`、`name`、`apiVersion`、および`location`に加え、必要に応じて `dependsOn` 文字列リストを指定できます。このリストには、このリソースをデプロイする前にデプロイを完了する必要があるその他のリソースを指定します。 この例では、リストには、前述の仮想ネットワークという要素が&1; つだけ指定されています。 この依存関係を指定するのは、スケール セットで VM を作成するには、ネットワークが存在している必要があるためです。 これにより、スケール セットは、作成する VM に対して、ネットワーク プロパティに指定されている IP アドレス範囲からプライベート IP アドレスを割り当てることができます。 dependsOn リストの各文字列の形式は `<type>/<name>` です (`type` と`name` は、仮想ネットワークのリソース定義で先ほど使用したものと同じです)。

```diff
+    {
+      "type": "Microsoft.Compute/virtualMachineScaleSets",
+      "name": "myScaleSet",
+      "apiVersion": "2016-04-30-preview",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Network/virtualNetworks/myVnet"
+      ],
```

スケール セットは、作成する VM のサイズ ("SKU 名") と、作成する VM の数 ("SKU 容量") を把握する必要があります。 利用可能な VM サイズを確認するには、[VM サイズに関するドキュメント](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes)を参照してください。

```diff
+      "sku": {
+        "name": "Standard_A1",
+        "capacity": 2
+      },
```

さらに、スケール セットは、スケール セットに関する更新を処理する方法を知る必要があります。 現時点では、`Manual` と `Automatic` の&2; つのオプションがあります。 この&2; つの違いの詳細については、「[仮想マシン スケール セットのアップグレード](./virtual-machine-scale-sets-upgrade-scale-set.md)」を参照してください。

```diff
+      "properties": {
+        "upgradePolicy": {
+          "mode": "Manual"
+        },
```

スケール セットは、VM に配置するオペレーティング システムも知る必要があります。 ここでは、完全にパッチが適用された Ubuntu 16.04-LTS イメージを使用して VM を作成します。

```diff
+        "virtualMachineProfile": {
+          "storageProfile": {
+            "imageReference": {
+              "publisher": "Canonical",
+              "offer": "UbuntuServer",
+              "sku": "16.04-LTS",
+              "version": "latest"
+            }
+          },
```

スケール セットは複数の VM をデプロイするため、個別の VM 名を指定する代わりに、`computerNamePrefix` を指定します。 スケール セットは、各 VM のプレフィックスにインデックスを追加するため、VM 名の形式は `<computerNamePrefix>_<auto-generated-index>` になります。 このスニペットでは、スケール セット内のすべての VM に対して、すでに説明した管理者のユーザー名とパスワードを設定するパラメーターも使用されています。 これは、参照するパラメーターを指定する文字列を引数として受け取り、そのパラメーターの値を出力する `parameters` テンプレート関数を使用して実行されます。

```diff
+          "osProfile": {
+            "computerNamePrefix": "vm",
+            "adminUsername": "[parameters('adminUsername')]",
+            "adminPassword": "[parameters('adminPassword')]"
+          },
```

最後に、スケール セット内の VM のネットワーク構成を指定する必要があります。 この例では、先ほど作成したサブネットの ID を指定するだけで、スケール セットは、そのサブネットにネットワーク インターフェイスを配置することを認識します。 サブネットを含む仮想ネットワークの ID は、`resourceId` テンプレート関数を使用して取得できます。 この関数は、リソースの種類と名前を引数として受け取り、そのリソースの完全修飾識別子を返します (この ID の形式は `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>` になります)。 ただし、仮想ネットワークの ID では十分ではありません。 スケール セットの VM が配置される特定のサブネットを指定する必要があるため、仮想ネットワークの ID に `/subnets/mySubnet`を連結します。 結果は、サブネットの完全修飾 ID です。 この連結は、一連の文字列を引数として受け取り、それらを連結したものを返す `concat` 関数で実行します。

```diff
+          "networkProfile": {
+            "networkInterfaceConfigurations": [
+              {
+                "name": "myNic",
+                "properties": {
+                  "primary": "true",
+                  "ipConfigurations": [
+                    {
+                      "name": "myIpConfig",
+                      "properties": {
+                        "subnet": {
+                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                        }
+                      }
+                    }
+                  ]
+                }
+              }
+            ]
+          }
+        }
+      }
+    }
+  ]
+}

```

## <a name="next-steps"></a>次のステップ

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
