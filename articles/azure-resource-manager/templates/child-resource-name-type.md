---
title: テンプレート内の子リソース
description: Azure Resource Manager テンプレート (ARM テンプレート ) で子リソースの名前と種類を設定する方法について説明します。
ms.topic: conceptual
ms.date: 04/23/2021
ms.openlocfilehash: 1928a94fbfefc694091a3f09ea577e8250540bf4
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/25/2021
ms.locfileid: "107949526"
---
# <a name="set-name-and-type-for-child-resources"></a>子リソースの名前と種類の設定

子リソースとは、別のリソースのコンテキスト内でのみ存在するリソースのことです。 たとえば[仮想マシン拡張機能](/azure/templates/microsoft.compute/virtualmachines/extensions)は、[仮想マシン](/azure/templates/microsoft.compute/virtualmachines)なしでは存在できません。 この拡張機能リソースが仮想マシンの子です。

各親リソースは、子リソースとして特定のリソースの種類のみを受け取ります。 子リソースのリソースの種類には、親リソースのリソースの種類が含まれます。 たとえば、`Microsoft.Web/sites/config` と `Microsoft.Web/sites/extensions` は、どちらも `Microsoft.Web/sites` の子リソースです。 許容されるリソースの種類は、親リソースの[テンプレート スキーマ](https://github.com/Azure/azure-resource-manager-schemas)で指定されます。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

Azure Resource Manager テンプレート (ARM テンプレート) では、親リソースの内側または外側に子リソースを指定できます。 リソースの名前とリソースの種類に指定する値は、子リソースが親リソースの内側で定義されているか、外側で定義されているかによって変わります。

## <a name="within-parent-resource"></a>親リソースの内側

次の例は、親リソースの resources プロパティ内に追加された子リソースを示しています。

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

子リソースの定義の深さは 5 レベルまでです。

親リソースの type 内に type と name の値を定義するときは、スラッシュを使わず 1 つのセグメントとして書式設定します。

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

次に示したのは、サブネットを含んだ仮想ネットワークの例です。 仮想ネットワークの resources 配列内にサブネットが含まれていることがわかります。 name が **Subnet1** に設定され、type が **subnets** に設定されています。 子リソースは、親リソースに依存するリソースとしてマークされています。子リソースをデプロイするためには、先に親リソースが存在していなければならないためです。

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

完全なリソースの種類は、あくまで `Microsoft.Network/virtualNetworks/subnets` になります。 親リソースの種類から想定されるため、`Microsoft.Network/virtualNetworks/` は不要です。

子リソースの名前は **Subnet1** に設定されていますが、完全名には親の名前が含まれます。 **VNet1** は、親リソースから引き継がれるので指定する必要はありません。

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource <parent-resource-symbolic-name> '<resource-type>@<api-version>' = {
  <parent-resource-properties>

  resource <child-resource-symbolic-name> '<child-resource-type>' = {
    <child-resource-properties>
  }
}
```

入れ子になったリソース宣言は、親リソースの構文の最上位レベルに記述する必要があります。 各レベルが親リソースの子の種類である限りは、宣言を任意の深さで入れ子にすることができます。

親リソースの type 内に type と name の値を定義するときは、スラッシュを使わず 1 つのセグメントとして書式設定します。 次に示したのは、サブネットを含んだ仮想ネットワークの例です。 仮想ネットワークの resources 配列内にサブネットが含まれていることがわかります。 name が **Subnet1** に設定され、type が **subnets** に設定されています。

```bicep
param location string

resource VNet1 'Microsoft.Network/virtualNetworks@2018-10-01' = {
  name: 'VNet1'
  location: location
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.0.0.0/16'
      ]
    }
  }

  resource VNet1_Subnet1 'subnets' = {
    name: 'Subnet1'
    properties: {
      addressPrefix: '10.0.0.0/24'
    }
  }
}
```

完全なリソースの種類は、あくまで `Microsoft.Network/virtualNetworks/subnets` になります。 `Microsoft.Network/virtualNetworks/` は親リソースの種類とバージョンから想定されるため不要です。 必要に応じて、入れ子になったリソースを使用すれば、構文 `<segment>@<version>` を使用して、API バージョンを宣言できます。 入れ子になったリソースで API バージョンが省略される場合は、親リソースの API バージョンが使用されます。 入れ子になったリソースに API バージョンが指定される場合は、指定されたその API バージョンが使用されます。

子リソースの名前は **Subnet1** に設定されていますが、完全名には親の名前が含まれます。 VNet1 は、親リソースから引き継がれるので指定する必要はありません。

子リソースのシンボル名にアクセスするには、`::` 演算子を使用する必要があります。 たとえば、子リソースからプロパティを出力します。

```bicep
output childAddressPrefix string = VNet1::VNet1_Subnet1.properties.addressPrefix
```

入れ子になったリソースは、その親リソースのプロパティにアクセスできます。 同じ親リソースの本体内で宣言されたその他のリソースは、互いに参照することができます。また、循環依存関係に関する一般的な規則が適用されます。 親リソースは、それに含まれるリソースのプロパティにアクセスできない場合があり、これにより、循環依存関係が発生します。

---

## <a name="outside-parent-resource"></a>親リソースの外側

次の例は、親リソースの外側の子リソースを示しています。 親リソースが同じテンプレート内にデプロイされていない場合、または複数の子リソースを作成するために [copy](copy-resources.md) を使う場合は、このアプローチを使用することがあります。

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

親リソースの外側に type と name を定義するときは、スラッシュを使って親の種類と名前を含めるように書式設定します。

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

次の例に示した仮想ネットワークとサブネットは、どちらもルート レベルで定義されています。 仮想ネットワークの resources 配列内にはサブネットが含まれていないことがわかります。 name が **VNet1/Subnet1** に設定され、type が `Microsoft.Network/virtualNetworks/subnets` に設定されています。 子リソースは、親リソースに依存するリソースとしてマークされています。子リソースをデプロイするためには、先に親リソースが存在していなければならないためです。

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource <parent-resource-symbolic-name> '<resource-type>@<api-version>' = {
  <parent-resource-properties>
}

resource <child-resource-symbolic-name> '<child-resource-type>@<api-version>' = {
  <child-resource-properties>
}
```

親リソースの外側に type と name を定義するときは、スラッシュを使って親の種類と名前を含めるように書式設定します。

次の例に示した仮想ネットワークとサブネットは、どちらもルート レベルで定義されています。 仮想ネットワークの resources 配列内にはサブネットが含まれていないことがわかります。 name が **VNet1/Subnet1** に設定され、type が `Microsoft.Network/virtualNetworks/subnets` に設定されています。 子リソースは、親リソースに依存するリソースとしてマークされています。子リソースをデプロイするためには、先に親リソースが存在していなければならないためです。

```bicep
param location string

resource VNet1 'Microsoft.Network/virtualNetworks@2018-10-01' = {
  name: 'VNet1'
  location: location
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.0.0.0/16'
      ]
    }
  }
}

resource VNet1_Subnet1 'Microsoft.Network/virtualNetworks/subnets@2018-10-01' = {
  name: '${VNet1.name}/Subnet1'
  properties: {
    addressPrefix: '10.0.0.0/24'
  }
}
```

---

## <a name="next-steps"></a>次のステップ

* ARM テンプレート作成の詳細については、「[ARM テンプレートの構造と構文の詳細](template-syntax.md)」を参照してください。
* リソースを参照する際のリソース名の形式については、[reference 関数](template-functions-resource.md#reference)の説明を参照してください。
