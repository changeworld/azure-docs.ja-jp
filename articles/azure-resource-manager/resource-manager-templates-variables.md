---
title: "Azure Resource Manager テンプレートの変数 | Microsoft Docs"
description: "宣言型 JSON 構文を使用した Azure Resource Manager テンプレートで変数を定義する方法について説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: tomfitz
ms.openlocfilehash: 8d9f227ad1f450cf6cdfca1dafb1b51bc6f6c9f9
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2017
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
                "input": {
                    <properties-to-repeat>
                }
            }
        ]
    },
    "copy": [
        {
            "name": "<variable-array-name>",
            "count": <number-of-iterations>,
            "input": {
                <properties-to-repeat>
            }
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

**copy** 構文を使用して、複数の要素の配列で変数を作成できます。 要素の数を表すカウントを指定します。 各要素には、**input** オブジェクト内にプロパティが含まれます。 コピーは変数内で使用することも、変数の作成に使用することもできます。 変数を定義し、その変数内で **copy** を使用するには、配列化されたプロパティを持つオブジェクトを作成します。 トップ レベルで **copy** を使用し、その中で 1 つ以上の変数を定義する場合は、1 つ以上の配列を作成します。 次の例では、両方の方法を示します。

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
        }
    ]
},
```

変数 **disk-array-on-object** には、**disks** という名前の配列を持つ、次のようなオブジェクトが格納されています。

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

コピーを使用して変数を作成するときに、複数のオブジェクトを指定することもできます。 次の例では、変数として 2 つの配列を定義しています。 1 つは **disks-top-level-array** という名前で 5 個の要素があります。 もう 1 つは **a-different-array** という名前で 3 つの要素があります。

```json
"variables": {
    "copy": [
        {
            "name": "disks-top-level-array",
            "count": 5,
            "input": {
                "name": "[concat('oneDataDisk', copyIndex('disks-top-level-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('disks-top-level-array')]"
            }
        },
        {
            "name": "a-different-array",
            "count": 3,
            "input": {
                "name": "[concat('twoDataDisk', copyIndex('a-different-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('a-different-array')]"
            }
        }
    ]
},
```

パラメーターの値が必要で、その値がテンプレートの値として正しい形式であることを確認する場合に、この方法はうまく機能します。 次の例では、セキュリティの規則の定義で使用するために、パラメーターの値を書式設定します。

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

## <a name="recommendations"></a>Recommendations
変数を使用する場合は、次の情報を活用してください。

* テンプレート内で複数回使用する必要のある値には、変数を使用してください。 1 回しか使用しない値は、ハードコーディングすることでテンプレートが読みやすくなります。
* テンプレートの **variables** セクションでは [reference](resource-group-template-functions-resource.md#reference) 関数は使用できません。 **reference** 関数は、リソースのランタイム状態からその値を取得します。 ただし、変数が解決されるのは、テンプレートの初期解析時です。 **reference** 関数を必要とする値は、テンプレートの **resources** セクションまたは **outputs** セクションに直接作成してください。
* リソース名を表す変数は、一意である必要があります。

## <a name="example-templates"></a>サンプル テンプレート

次のサンプル テンプレートでは、変数を使用するいくつかのシナリオを例示します。 さまざまなシナリオで変数がどのように処理されるかを、このサンプルを展開して試してください。 

|テンプレート  |説明  |
|---------|---------|
| [変数の定義](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | さまざまな種類の変数を例示します。 このテンプレートではリソースを展開しません。 変数の値を作成して、その値を返します。 |
| [構成変数](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | 構成の値を定義する変数の用法を例示します。 このテンプレートではリソースを展開しません。 変数の値を作成して、その値を返します。 |
| [ネットワーク セキュリティの規則](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json)と[パラメーター ファイル](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | ネットワーク セキュリティ グループにセキュリティの規則を割り当てるために、配列を正しい形式で作成します。 |


## <a name="next-steps"></a>次のステップ
* さまざまな種類のソリューションのテンプレートについては、「 [Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)」をご覧ください。
* テンプレート内から使用できる関数の詳細については、「 [Azure Resource Manager テンプレートの関数](resource-group-template-functions.md)」を参照してください。
* デプロイ中に複数のテンプレートを結合するには、「 [Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)」をご覧ください。
* 別のリソース グループ内に存在するリソースの使用が必要になる場合があります。 このシナリオは、複数のリソース グループ間で共有されているストレージ アカウントまたは仮想ネットワークを使用している場合は一般的です。 詳細については、 [resourceId 関数](resource-group-template-functions-resource.md#resourceid)に関するセクションをご覧ください。