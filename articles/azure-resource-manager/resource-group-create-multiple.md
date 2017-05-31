---
title: "Azure リソースの複数インスタンスのデプロイ | Microsoft Docs"
description: "Azure リソース マネージャー テンプレートで copy 操作と配列を使用して、リソースをデプロイする際に複数回反復処理する方法について説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 94d95810-a87b-460f-8e82-c69d462ac3ca
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/12/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: a8e35456af8c9f2cf1bf9e9c364e33641f29a477
ms.contentlocale: ja-jp
ms.lasthandoff: 05/15/2017


---
# <a name="deploy-multiple-instances-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートでリソースまたはプロパティの複数のインスタンスをデプロイする
このトピックでは、Azure リソース マネージャー テンプレートで反復処理して、リソースの複数のインスタンスを作成する方法について説明します。

## <a name="resource-iteration"></a>リソースの反復
あるリソース タイプのインスタンスを複数作成するには、そのリソース タイプに `copy` 要素を追加します。 copy 要素には、そのループの反復回数と名前を指定します。 数値は正の整数で、800 を超えることはできません。 リソース マネージャーは、並列でリソースを作成します。 そのため、作成される順序は保証されません。 反復処理されるリソースを順番に作成する場合は、「[シリアル コピー](#serial-copy)」を参照してください。 

複数回作成されるリソースは、次の形式を取ります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

各リソースの名前には、現在のループの反復を返す `copyIndex()` 関数が含まれます。 `copyIndex()` は 0 から始まります。 次の例を見てください。

```json
"name": "[concat('storage', copyIndex())]",
```

この場合、以下の名前が作成されます。

* storage0
* storage1
* storage2

インデックス値をオフセットするには、copyIndex() 関数に値を渡します。 実行する反復処理の数は copy 要素で指定されたままですが、copyIndex の値が指定された値でオフセットされます。 次の例を見てください。

```json
"name": "[concat('storage', copyIndex(1))]",
```

この場合、以下の名前が作成されます。

* storage1
* storage2
* storage3

コピー操作は、配列内の各要素に対して反復処理するため、配列で作業するときに便利です。 配列で反復回数を指定するには `length` 関数を使います。また、配列における現在のインデックスを取得するには `copyIndex` を使います。 次の例を見てください。

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "contoso", 
         "fabrikam", 
         "coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('storage', parameters('org')[copyIndex()])]", 
      "copy": { 
         "name": "storagecopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      ...
  } 
]
```

この場合、以下の名前が作成されます。

* storagecontoso
* storagefabrikam
* storagecoho

## <a name="serial-copy"></a>シリアル コピー

copy 要素を使用して、あるリソース タイプのインスタンスを複数作成する場合、Resource Manager によって既定でそれらのインスタンスが並列的にデプロイされます。 しかし、リソースが順番にデプロイされるように指定したい場合もあります。 たとえば、運用環境を更新するとき、一度に特定の数だけ更新されるように更新時間をずらす必要がある場合があります。

Resource Manager では、複数のインスタンスを順番にデプロイできるようにするプロパティを copy 要素に指定できます。 copy 要素で `mode` を **serial** に設定し、`batchSize` を一度にデプロイするインスタンスの数に設定します。 シリアル モードでは、Resource Manager はループ内で前のインスタンスへの依存関係を作成するので、前のバッチが完了するまで次のバッチは実行されません。

```json
"copy": {
    "name": "iterator",
    "count": "[parameters('numberToDeploy')]",
    "mode": "serial",
    "batchSize": 2
},
```

mode プロパティでも **parallel** が既定値として使用されます。

実際のリソースを作成せずにシリアル コピーをテストするには、空の入れ子になったテンプレートをデプロイする次のテンプレートを使用します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberToDeploy": {
      "type": "int",
      "minValue": 2,
      "defaultValue": 5
    }
  },
  "resources": [
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('loop-', copyIndex())]",
      "copy": {
        "name": "iterator",
        "count": "[parameters('numberToDeploy')]",
        "mode": "serial",
        "batchSize": 1
      },
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

デプロイ履歴で、入れ子になったデプロイが順番に処理されていることに注意してください。

![シリアル デプロイ](./media/resource-group-create-multiple/serial-copy.png)

より現実に即したシナリオでは、次の例のように、Linux VM の 2 つのインスタンスを、入れ子になったテンプレートから一度にデプロイします。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "User name for the Virtual Machine."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "dnsLabelPrefix": {
            "type": "string",
            "metadata": {
                "description": "Unique DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "ubuntuOSVersion": {
            "type": "string",
            "defaultValue": "16.04.0-LTS",
            "allowedValues": [
                "12.04.5-LTS",
                "14.04.5-LTS",
                "15.10",
                "16.04.0-LTS"
            ],
            "metadata": {
                "description": "The Ubuntu version for the VM. This will pick a fully patched image of this given Ubuntu version."
            }
        }
    },
    "variables": {
        "templatelink": "https://raw.githubusercontent.com/rjmax/Build2017/master/Act1.TemplateEnhancements/Chapter03.LinuxVM.json"
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "[concat('nestedDeployment',copyIndex())]",
            "type": "Microsoft.Resources/deployments",
            "copy": {
                "name": "myCopySet",
                "count": 4,
                "mode": "serial",
                "batchSize": 2
            },
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "adminUsername": {
                        "value": "[parameters('adminUsername')]"
                    },
                    "adminPassword": {
                        "value": "[parameters('adminPassword')]"
                    },
                    "dnsLabelPrefix": {
                        "value": "[parameters('dnsLabelPrefix')]"
                    },
                    "ubuntuOSVersion": {
                        "value": "[parameters('ubuntuOSVersion')]"
                    },
                    "index":{
                        "value": "[copyIndex()]"
                    }
                }
            }
        }
    ]
}
```

## <a name="depend-on-resources-in-a-loop"></a>ループ内のリソースへの依存
`dependsOn` 要素を使用することで、リソースを別のリソースの後にデプロイするよう指定することが可能です。 ループ内のリソースの集合に依存するリソースをデプロイするには、dependsOn 要素にコピー ループの名前を指定します。 次の例では、仮想マシンをデプロイする前に 3 つのストレージ アカウントをデプロイする方法を示します。 完全な仮想マシン定義は示されていません。 コピー要素の name が `storagecopy` に設定され、Virtual Machines の dependsOn 要素が `storagecopy` に設定されるよう注意してください。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        },
        {
            "apiVersion": "2015-06-15", 
            "type": "Microsoft.Compute/virtualMachines", 
            "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
            "dependsOn": ["storagecopy"],
            ...
        }
    ],
    "outputs": {}
}
```

## <a name="create-multiple-instances-of-a-child-resource"></a>子リソースの複数のインスタンスの作成
子リソースにコピー ループを使用することはできません。 通常他のリソース内の入れ子として定義されるリソースの複数のインスタンスを作成するには、代わりにそのリソースを最上位のリソースとして作成する必要があります。 type および name の各プロパティを使用して、親リソースとの関係を定義します。

たとえば、通常はデータ ファクトリ内の子リソースとしてデータセットを定義するとします。

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
    "resources": [
    {
        "type": "datasets",
        "name": "exampleDataSet",
        "dependsOn": [
            "exampleDataFactory"
        ],
        ...
    }
}]
```

データセットの複数のインスタンスを作成するには、データ ファクトリの外部に移動します。 データセットは、データ ファクトリと同じレベルである必要がありますが、今までどおりデータ ファクトリの子リソースです。 type および name の各プロパティを使用して、データセットとデータ ファクトリの関係を保存します。 テンプレート内の位置から type を推論できなくなったため、`{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}` の形式で完全修飾型を指定する必要があります。

データ ファクトリのインスタンスを使用して親子関係を確立するには、親リソースの名前を含むデータ セットの名前を指定します。 `{parent-resource-name}/{child-resource-name}` の形式で入力します。  

次の例は、実装を示します。

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
},
{
    "type": "Microsoft.DataFactory/datafactories/datasets",
    "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
    "dependsOn": [
        "exampleDataFactory"
    ],
    "copy": { 
        "name": "datasetcopy", 
        "count": "3" 
    } 
    ...
}]
```

## <a name="next-steps"></a>次のステップ
* テンプレートのセクションについては、「[Azure Resource Manager のテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。
* テンプレートをデプロイする方法については、「 [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)」を参照してください。


