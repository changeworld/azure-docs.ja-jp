---
title: プロパティの複数のインスタンスを定義する
description: Azure Resource Manager テンプレートで copy 操作を使用して、リソースのプロパティを作成する際に複数回反復処理する方法について説明します。
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: e86d38b0e5d2e39d54b3c419b6eebdcda74022db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258109"
---
# <a name="property-iteration-in-arm-templates"></a>ARM テンプレートでのプロパティの反復処理

この記事では、Azure Resource Manager (ARM) テンプレートでプロパティの複数のインスタンスを作成する方法について説明します。 テンプレート内のリソースの properties セクションに **copy** 要素を追加することにより、デプロイ時にプロパティの項目数を動的に設定できます。 テンプレートの構文を繰り返す必要もありません。

[リソース](copy-resources.md)、[変数](copy-variables.md)、および [出力](copy-outputs.md)でもコピーを使用できます。

## <a name="property-iteration"></a>プロパティの反復処理

この copy 要素には、次の一般的な形式があります。

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

**name**には、作成するリソース プロパティの名前を指定します。 **count** プロパティは、プロパティに対して必要な反復の数を指定します。

**input** プロパティは、繰り返すプロパティを指定します。 **input** プロパティの値から構築された要素の配列を作成します。

次の例は、仮想マシンで dataDisks プロパティに `copy` を適用する方法を示しています。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 16,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2017-03-30",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "diskSizeGB": 1023,
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty"
              }
            }
          ]
        }
      }
    }
  ]
}
```

プロパティの反復処理内で `copyIndex` を使用する場合、反復処理の名前を指定する必要があります。

> [!NOTE]
> プロパティの反復処理では、オフセット引数もサポートされています。 オフセットは、copyIndex('dataDisks', 1) など、反復処理の名前の後に指定する必要があります。
>

Resource Manager はデプロイ中に `copy` 配列を展開します。 配列の名前がプロパティの名前になります。 入力値がオブジェクトのプロパティになります。 デプロイされたテンプレートは次のようになります。

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

copy 要素は配列であるため、リソースの複数のプロパティを指定できます。

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "exampleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

リソースの反復処理とプロパティの反復処理は同時に使用できます。 プロパティの反復処理を名前で参照します。

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="copy-limits"></a>コピー制限

count は 800 を超えることはできません。

count は負の数値にすることはできません。 Azure PowerShell 2.6 以降、Azure CLI 2.0.74 以降、または REST API バージョン **2019-05-10** 以降を使用してテンプレートをデプロイする場合は、count を 0 に設定できます。 以前のバージョンの PowerShell、CLI、および REST API では、count の 0 をサポートしていません。

## <a name="example-templates"></a>サンプル テンプレート

次の例は、プロパティに対して複数の値を作成する一般的なシナリオを示しています。

|Template  |説明  |
|---------|---------|
|[VM deployment with a variable number of data disks](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |仮想マシンと共に複数のデータ ディスクをデプロイします。 |

## <a name="next-steps"></a>次のステップ

* チュートリアルを実行するには、「[チュートリアル: ARM テンプレートを使用した複数のリソース インスタンスの作成](template-tutorial-create-multiple-instances.md)」を参照してください。
* copy 要素のその他の使用方法については、以下を参照してください。
  * [ARM テンプレートでのリソースの反復処理](copy-resources.md)
  * [ARM テンプレートでの変数の反復処理](copy-variables.md)
  * [ARM テンプレートでの出力の反復処理](copy-outputs.md)
* テンプレートのセクションについては、[ARM テンプレートの作成](template-syntax.md)に関する記事を参照してください。
* テンプレートをデプロイする方法については、[ARM テンプレートを使用したアプリケーションのデプロイ](deploy-powershell.md)に関する記事を参照してください。

