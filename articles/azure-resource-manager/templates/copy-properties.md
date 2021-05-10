---
title: プロパティの複数のインスタンスを定義する
description: Azure Resource Manager テンプレート (ARM テンプレート) で copy 操作を使用して、リソースのプロパティを作成する際に複数回反復処理する方法について説明します。
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 16c293f1c3aff64aeb8b6cae4b7f1aa14dcd0a77
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480003"
---
# <a name="property-iteration-in-arm-templates"></a>ARM テンプレートでのプロパティの反復処理

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) でプロパティの複数のインスタンスを作成する方法について説明します。 テンプレート内のリソースの properties セクションにコピー ループを追加することにより、デプロイ時にプロパティの項目数を動的に設定できます。 テンプレートの構文を繰り返す必要もありません。

コピー ループをプロパティに適用する場合でも、最上位リソースでのみコピー ループを使用できます。 子リソースを最上位リソースに変更する方法の詳細については、「[子リソースの反復処理](copy-resources.md#iteration-for-a-child-resource)」を参照してください。

[resources](copy-resources.md)、[variables](copy-variables.md)、および [outputs](copy-outputs.md) でもコピー ループを使用できます。

## <a name="syntax"></a>構文

# <a name="json"></a>[JSON](#tab/json)

テンプレートの resources セクションに `copy` 要素を追加して、プロパティに対する項目の数を設定します。 この copy 要素には、次の一般的な形式があります。

```json
"copy": [
  {
    "name": "<name-of-property>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

`name`には、作成するリソース プロパティの名前を指定します。

`count` プロパティは、プロパティに対して必要な反復の数を指定します。

`input` プロパティは、繰り返すプロパティを指定します。 `input` プロパティの値から構築された要素の配列を作成します。

# <a name="bicep"></a>[Bicep](#tab/bicep)

ループを使用すると、次の方法で複数のプロパティを宣言できます。

- 配列の反復処理:

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }]
  ```

- 配列の要素を反復処理します

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }]
  ```

- ループ インデックスの使用

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

---

## <a name="copy-limits"></a>コピー制限

count は 800 を超えることはできません。

count は負の数値にすることはできません。 Azure CLI、PowerShell、または REST API の最新バージョンを使用してテンプレートをデプロイする場合、ゼロを指定できます。 具体的には、次のものを使用する必要があります。

- Azure PowerShell **2.6** 以降
- Azure CLI **2.0.74** 以降
- REST API バージョン **2019-05-10** 以降
- [[Linked deployments]\(リンクされたデプロイ\)](linked-templates.md) には、デプロイ リソースの種類に API バージョン **2019-05-10** 以降を使用する必要があります

以前のバージョンの PowerShell、CLI、および REST API では、count の 0 をサポートしていません。

## <a name="property-iteration"></a>プロパティの反復処理

次の例は、仮想マシンで `dataDisks` プロパティにコピー ループを適用する方法を示しています。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 3,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty",
                "diskSizeGB": 1023
              }
            }
          ]
        }
        ...
      }
    }
  ]
}
```

プロパティの反復処理内で `copyIndex` を使用する場合、反復処理の名前を指定する必要があります。 プロパティの反復処理では、オフセット引数もサポートされています。 オフセットは、`copyIndex('dataDisks', 1)` など、反復処理の名前の後に指定する必要があります。

デプロイされたテンプレートは次のようになります。

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
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

コピー操作は、配列内の各要素に対して反復処理するため、配列で作業するときに便利です。 配列で反復回数を指定するには `length` 関数を使います。また、配列における現在のインデックスを取得するには `copyIndex` を使います。

次のテンプレートの例では、配列として渡されるデータベースに対してフェールオーバー グループが作成されます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "primaryServerName": {
      "type": "string"
    },
    "secondaryServerName": {
      "type": "string"
    },
    "databaseNames": {
      "type": "array",
      "defaultValue": [
        "mydb1",
        "mydb2",
        "mydb3"
      ]
    }
  },
  "variables": {
    "failoverName": "[concat(parameters('primaryServerName'),'/', parameters('primaryServerName'),'failovergroups')]"
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers/failoverGroups",
      "apiVersion": "2015-05-01-preview",
      "name": "[variables('failoverName')]",
      "properties": {
        "readWriteEndpoint": {
          "failoverPolicy": "Automatic",
          "failoverWithDataLossGracePeriodMinutes": 60
        },
        "readOnlyEndpoint": {
          "failoverPolicy": "Disabled"
        },
        "partnerServers": [
          {
            "id": "[resourceId('Microsoft.Sql/servers', parameters('secondaryServerName'))]"
          }
        ],
        "copy": [
          {
            "name": "databases",
            "count": "[length(parameters('databaseNames'))]",
            "input": "[resourceId('Microsoft.Sql/servers/databases', parameters('primaryServerName'), parameters('databaseNames')[copyIndex('databases')])]"
          }
        ]
      }
    }
  ],
  "outputs": {
  }
}
```

`copy` 要素は配列であるため、リソースの複数のプロパティを指定できます。

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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minValue(0)
@maxValue(16)
@description('The number of dataDisks to be returned in the output array.')
param numberOfDataDisks int = 16

resource vmName 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
  properties: {
    storageProfile: {
      ...
      dataDisks: [for i in range(0, numberOfDataDisks): {
        lun: i
        createOption: 'Empty'
        diskSizeGB: 1023
      }]
    }
    ...
  }
}
```

デプロイされたテンプレートは次のようになります。

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
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

---

リソースの反復処理とプロパティの反復処理は同時に使用できます。 プロパティの反復処理を名前で参照します。

# <a name="json"></a>[JSON](#tab/json)

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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource vnetname_resource 'Microsoft.Network/virtualNetworks@2018-04-01' = [for i in range(0, 2): {
  name: concat(vnetname, i)
  location: resourceGroup().location
  properties: {
    addressSpace: {
      addressPrefixes: [
        addressPrefix
      ]
    }
    subnets: [for j in range(0, 2): {
      name: 'subnet-${j}'
      properties: {
        addressPrefix: subnetAddressPrefix[j]
      }
    }]
  }
}]
```

---

## <a name="example-templates"></a>サンプル テンプレート

次の例は、プロパティに対して複数の値を作成する一般的なシナリオを示しています。

|Template  |説明  |
|---------|---------|
|[VM deployment with a variable number of data disks](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |仮想マシンと共に複数のデータ ディスクをデプロイします。 |

## <a name="next-steps"></a>次のステップ

- チュートリアルについては、「[チュートリアル:ARM テンプレートを使用した複数のリソース インスタンスの作成](template-tutorial-create-multiple-instances.md)」を参照してください。
- コピー ループのその他の使用方法については、以下を参照してください。
  - [ARM テンプレートでのリソースの反復処理](copy-resources.md)
  - [ARM テンプレートでの変数の反復処理](copy-variables.md)
  - [ARM テンプレートでの出力の反復処理](copy-outputs.md)
- テンプレートのセクションの詳細については、「[ARM テンプレートの構造と構文について](template-syntax.md)」を参照してください。
- テンプレートをデプロイする方法の詳細については、「[ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](deploy-powershell.md)」を参照してください。
