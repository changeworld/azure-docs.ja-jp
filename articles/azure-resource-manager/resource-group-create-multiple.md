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
ms.date: 04/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 8ecf7c058b90fd18e41fd4e1cbc29e22dfeb0883
ms.lasthandoff: 04/19/2017


---
# <a name="deploy-multiple-instances-of-resources-in-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートでの複数のリソース インスタンスのデプロイ
このトピックでは、Azure リソース マネージャー テンプレートで反復処理して、リソースの複数のインスタンスを作成する方法について説明します。

## <a name="copy-and-copyindex"></a>copy および copyIndex
複数回作成されるリソースは、次の形式を取ります。

```json
"resources": [ 
  { 
      "name": "[concat('examplecopy-', copyIndex())", 
      "type": "Microsoft.Web/sites", 
      "location": "East US", 
      "apiVersion": "2015-08-01",
      "copy": { 
         "name": "websitescopy", 
         "count": "[parameters('count')]" 
      }, 
      "properties": {
          "serverFarmId": "hostingPlanName"
      } 
  } 
]
```

反復回数が copy オブジェクトで指定されていることを確認します。

```json
"copy": { 
    "name": "websitescopy", 
    "count": "[parameters('count')]" 
} 
```

数値は正の整数で、800 を超えることはできません。

各リソースの名前には、現在のループの反復を返す `copyIndex()` 関数が含まれます。

```json
"name": "[concat('examplecopy-', copyIndex())]",
```

次の 3 つの web サイトをデプロイする場合に命名されます。

* examplecopy-0
* examplecopy-1
* examplecopy-2.

インデックス値をオフセットするには、`copyIndex(1)` のような copyIndex() 関数に値を渡します。 実行する反復処理の数は copy 要素で指定されたままですが、copyIndex の値が指定された値でオフセットされます。 そのため前の例と同じテンプレートを使用しても、 copyIndex(1) を指定すると、次の名前の 3 つの Web サイトがデプロイされます。

* examplecopy-1
* examplecopy-2
* examplecopy-3

リソース マネージャーは、並列でリソースを作成します。 そのため、作成される順序は保証されません。 連続して繰り返されるリソースを作成する場合は、[Azure Resource Manager テンプレートでの連続ループ](resource-manager-sequential-loop.md)に関するページを参照してください。 

copy オブジェクトは、最上位のリソースにのみ適用できます。 リソースの種類のプロパティや子リソースには適用できません。 次の擬似コードの例で、copy を適用できる場所を示します。

```json
"resources": [
  {
    "type": "{provider-namespace-and-type}",
    "name": "parentResource",
    "copy": {  
      /* Yes, copy can be applied here */
    },
    "properties": {
      "exampleProperty": {
        /* No, copy cannot be applied here */
      }
    },
    "resources": [
      {
        "type": "{provider-type}",
        "name": "childResource",
        /* No, copy cannot be applied here. The resource must be promoted to top-level. */ 
      }
    ]
  }
] 
```

子リソースを反復処理するには、[子リソースの複数インスタンス作成](#create-multiple-instances-of-a-child-resource)を参照してください。

copy をプロパティに適用することはできませんが、プロパティは、今までどおりそのプロパティが含まれるリソースの反復の一部です。 したがって、プロパティ内で copyIndex() を使用して値を指定することができます。 プロパティに複数の値を作成するには、[リソースタイプに複数プロパティのインスタンスを作成する](resource-manager-property-copy.md)を参照してください。

## <a name="use-copy-with-array"></a>配列でのコピーの使用
コピー操作は、配列内の各要素に対して反復処理するため、配列で作業するときに便利です。 次の 3 つの Web サイトをデプロイするには:

* examplecopy-Contoso
* examplecopy-Fabrikam
* examplecopy-Coho

次のテンプレートを使用します。

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "Contoso", 
         "Fabrikam", 
         "Coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
      "type": "Microsoft.Web/sites", 
      "location": "East US", 
      "apiVersion": "2015-08-01",
      "copy": { 
         "name": "websitescopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      "properties": {
          "serverFarmId": "hostingPlanName"
      } 
  } 
]
```

数の指定には、必ず `length` 関数を使用してください。 length 関数のパラメーターとして、配列を指定します。

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
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
            "apiVersion": "2015-06-15",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
            "location": "[resourceGroup().location]",
            "properties": {
                "accountType": "Standard_LRS"
            },
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
* 連続して繰り返されるリソースを作成する場合は、[Azure Resource Manager テンプレートでの連続ループ](resource-manager-sequential-loop.md)に関するページを参照してください。
* テンプレートをデプロイする方法については、「 [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)」を参照してください。


