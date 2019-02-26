---
title: Azure Resource Manager テンプレートの変数 | Microsoft Docs
description: 宣言型 JSON 構文を使用した Azure Resource Manager テンプレートで変数を定義する方法について説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2019
ms.author: tomfitz
ms.openlocfilehash: 50feca90d375d6afd3b04afe019ad9f9025f19dc
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2019
ms.locfileid: "56308574"
---
# <a name="variables-section-of-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートの変数セクション
テンプレート内で使用できる値は、variables セクションで構築します。 必ずしも変数を定義する必要はありませんが、変数を定義することによって複雑な式が減り、テンプレートが単純化されることはよくあります。

## <a name="define-and-use-a-variable"></a>変数の定義と使用

変数を定義する例を以下に示します。 これにより、ストレージ アカウント名の文字列値が作成されます。 テンプレート関数をいくつか使用してパラメーター値を取得し、連結して一意の文字列にします。

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

リソースを定義するときに、この変数を使用します。

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

## <a name="available-definitions"></a>使用可能な定義

前述の例では、変数を定義する方法の 1 つを示しました。 次のような定義を使用することもできます。

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    },
    "<variable-object-name>": {
        "copy": [
            {
                "name": "<name-of-array-property>",
                "count": <number-of-iterations>,
                "input": <object-or-value-to-repeat>
            }
        ]
    },
    "copy": [
        {
            "name": "<variable-array-name>",
            "count": <number-of-iterations>,
            "input": <object-or-value-to-repeat>
        }
    ]
}
```

## <a name="configuration-variables"></a>構成変数

複雑な JSON 型を使用して、環境に関連する値を定義できます。 

```json
"variables": {
    "environmentSettings": {
        "test": {
            "instanceSize": "Small",
            "instanceCount": 1
        },
        "prod": {
            "instanceSize": "Large",
            "instanceCount": 4
        }
    }
},
```

パラメーターで、使用する構成の値を示す値を作成します。

```json
"parameters": {
    "environmentName": {
        "type": "string",
        "allowedValues": [
          "test",
          "prod"
        ]
    }
},
```

現在の設定を取得するには次のようにします。

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="use-copy-element-in-variable-definition"></a>変数定義での copy 要素の使用

変数のインスタンスを複数作成するには、variables セクション内で `copy` プロパティを使用します。 `input` プロパティの値から構築された要素の配列を作成します。 `copy` プロパティは、変数内で使用することも、variables セクションの最上位レベルで使用することもできます。 変数の反復処理内で `copyIndex` を使用するときは、反復処理の名前を指定する必要があります。

次の例は、copy の使用方法を示しています。

```json
"variables": {
  "disk-array-on-object": {
    "copy": [
      {
        "name": "disks",
        "count": 3,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('disks')]"
        }
      }
    ]
  },
  "copy": [
    {
      "name": "disks-top-level-array",
      "count": 3,
      "input": {
        "name": "[concat('myDataDisk', copyIndex('disks-top-level-array', 1))]",
        "diskSizeGB": "1",
        "diskIndex": "[copyIndex('disks-top-level-array')]"
      }
    },
    {
      "name": "top-level-string-array",
      "count": 5,
      "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
    }
  ]
},
```

copy 式が評価されると、変数 **disk-array-on-object** には、**disks** という名前の配列を持つ、次のようなオブジェクトが格納されます。

```json
{
  "disks": [
    {
      "name": "myDataDisk1",
      "diskSizeGB": "1",
      "diskIndex": 0
    },
    {
      "name": "myDataDisk2",
      "diskSizeGB": "1",
      "diskIndex": 1
    },
    {
      "name": "myDataDisk3",
      "diskSizeGB": "1",
      "diskIndex": 2
    }
  ]
}
```

変数 **disks-top-level-array** には、次のような配列が格納されています。

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  }
]
```

変数 **top-level-string-array** には、次のような配列が格納されています。

```json
[
  "myDataDisk1",
  "myDataDisk2",
  "myDataDisk3",
  "myDataDisk4",
  "myDataDisk5"
]
```

copy の使用は、パラメーター値を取得して、リソースの値にそれらをマップする必要がある場合に最適です。 次の例では、セキュリティの規則の定義で使用するために、パラメーターの値を書式設定します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "securityRules": {
      "type": "array"
    }
  },
  "variables": {
    "copy": [
      {
        "name": "securityRules",
        "count": "[length(parameters('securityRules'))]",
        "input": {
          "name": "[parameters('securityRules')[copyIndex('securityRules')].name]",
          "properties": {
            "description": "[parameters('securityRules')[copyIndex('securityRules')].description]",
            "priority": "[parameters('securityRules')[copyIndex('securityRules')].priority]",
            "protocol": "[parameters('securityRules')[copyIndex('securityRules')].protocol]",
            "sourcePortRange": "[parameters('securityRules')[copyIndex('securityRules')].sourcePortRange]",
            "destinationPortRange": "[parameters('securityRules')[copyIndex('securityRules')].destinationPortRange]",
            "sourceAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].sourceAddressPrefix]",
            "destinationAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].destinationAddressPrefix]",
            "access": "[parameters('securityRules')[copyIndex('securityRules')].access]",
            "direction": "[parameters('securityRules')[copyIndex('securityRules')].direction]"
          }
        }
      }
    ]
  },
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "NSG1",
      "location": "[resourceGroup().location]",
      "properties": {
        "securityRules": "[variables('securityRules')]"
      }
    }
  ],
  "outputs": {}
}
```

## <a name="example-templates"></a>サンプル テンプレート

次のサンプル テンプレートでは、変数を使用するいくつかのシナリオを例示します。 さまざまなシナリオで変数がどのように処理されるかを、このサンプルを展開して試してください。 

|Template  |説明  |
|---------|---------|
| [変数の定義](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | さまざまな種類の変数を例示します。 このテンプレートではリソースをデプロイしません。 変数の値を作成して、その値を返します。 |
| [構成変数](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | 構成の値を定義する変数の用法を例示します。 このテンプレートではリソースをデプロイしません。 変数の値を作成して、その値を返します。 |
| [ネットワーク セキュリティの規則](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json)と[パラメーター ファイル](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | ネットワーク セキュリティ グループにセキュリティの規則を割り当てるために、配列を正しい形式で作成します。 |


## <a name="next-steps"></a>次の手順
* さまざまな種類のソリューションのテンプレートについては、「 [Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)」をご覧ください。
* テンプレート内から使用できる関数の詳細については、「 [Azure Resource Manager テンプレートの関数](resource-group-template-functions.md)」を参照してください。
* テンプレート作成の推奨事項については、「[Azure Resource Manager テンプレートのベスト プラクティス](template-best-practices.md)」を参照してください。
* 別のリソース グループ内に存在するリソースの使用が必要になる場合があります。 このシナリオは、複数のリソース グループ間で共有されているストレージ アカウントまたは仮想ネットワークを使用している場合は一般的です。 詳細については、 [resourceId 関数](resource-group-template-functions-resource.md#resourceid)に関するセクションをご覧ください。