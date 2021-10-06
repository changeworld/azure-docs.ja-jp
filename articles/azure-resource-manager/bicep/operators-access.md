---
title: Bicep アクセサー演算子
description: Bicep リソースのアクセス演算子とプロパティ アクセス演算子について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: f3ed1ca46c2dfbcab8bfbdc720ff7ab448cbc742
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744452"
---
# <a name="bicep-accessor-operators"></a>Bicep アクセサー演算子

アクセサー演算子は、子リソース、オブジェクトのプロパティ、配列の要素にアクセスするために使われます。 また、プロパティ アクセサーを使用していくつかの関数を使用することもできます。

| 演算子 | 名前 |
| ---- | ---- |
| `[]` | [インデックス アクセサー](#index-accessor) |
| `.`  | [関数アクセサー](#function-accessor) |
| `::` | [入れ子になったリソース アクセサー](#nested-resource-accessor) |
| `.`  | [プロパティ アクセサー](#property-accessor) |

## <a name="index-accessor"></a>インデックス アクセサー

`array[integerIndex]`

`object['stringIndex']`

インデックス アクセサーを使用して、配列から要素を取得するか、オブジェクトからプロパティを取得します。

**配列** の場合は、インデックスを **整数** として指定します。 整数は、取得する要素の 0 から始まる位置と一致します。

**オブジェクト** の場合は、インデックスを **文字列** として指定します。 文字列は、取得するオブジェクトの名前と一致します。

次の例では、配列内の要素を取得します。

```bicep
var arrayVar = [
  'Coho'
  'Contoso'
  'Fabrikan'
]

output accessorResult string = arrayVar[1]
``` 

例の出力を次に示します。

| 名前 | 種類 | 値 |
| ---- | ---- | ---- |
| accessorResult | string | 'Contoso' |

次の例では、オブジェクトのプロパティを取得します。

```bicep
var environmentSettings = {
  dev: {
    name: 'Development'
  }
  prod: {
    name: 'Production'
  }
}

output accessorResult string = environmentSettings['dev'].name
```

例の出力を次に示します。

| 名前 | 種類 | 値 |
| ---- | ---- | ---- |
| accessorResult | string | 'Development' |

## <a name="function-accessor"></a>関数アクセサー

`resourceName.functionName()`

[getSecret](bicep-functions-resource.md#getsecret) と [list*](bicep-functions-resource.md#list)の 2 つの関数は、アクセサー演算子による関数の呼び出しに対応しています。 これら 2 つの関数は、アクセサー演算子をサポートする唯一の関数です。

### <a name="example"></a>例

次の例では、既存のキー コンテナーを参照し、`getSecret` を使用してモジュールにシークレットを渡します。

```bicep
resource kv 'Microsoft.KeyVault/vaults@2019-09-01' existing = {
  name: kvName
  scope: resourceGroup(subscriptionId, kvResourceGroup )
}

module sql './sql.bicep' = {
  name: 'deploySQL'
  params: {
    sqlServerName: sqlServerName
    adminLogin: adminLogin
    adminPassword: kv.getSecret('vmAdminPassword')
  }
}
```

## <a name="nested-resource-accessor"></a>入れ子になったリソース アクセサー

`parentResource::nestedResource`

入れ子になったリソースとは、別のリソース内で宣言されているリソースです。 入れ子になったリソース アクセサー `::` を使用して、親リソースの外部から入れ子になったリソースにアクセスします。

親リソース内では、シンボリック名だけを使用して、入れ子になったリソースを参照します。 入れ子になったリソース アクセサーは、親リソースの外部から、入れ子になったリソースを参照する場合にのみ使用する必要があります。

### <a name="example"></a>例

次の例は、親リソース内から、および親リソースの外部から、入れ子になったリソースを参照する方法を示しています。

```bicep
resource demoParent 'demo.Rp/parentType@2020-01-01' = {
  name: 'demoParent'
  location: 'West US'

  // Declare a nested resource within 'demoParent'
  resource demoNested 'childType' = {
    name: 'demoNested'
    properties: {
      displayName: 'The nested instance.'
    }
  }

  // Declare another nested resource
  resource demoSibling 'childType' = {
    name: 'demoSibling'
    properties: {
      // Use symbolic name to reference because this line is within demoParent
      displayName: 'Sibling of ${demoNested.properties.displayName}'
    }
  }
}

// Use nested accessor to reference because this line is outside of demoParent
output displayName string = demoParent::demoNested.properties.displayName
```

## <a name="property-accessor"></a>プロパティ アクセサー

`objectName.propertyName`

プロパティ アクセサーは、オブジェクトのプロパティにアクセスするために使用します。 プロパティ アクセサーは、オブジェクトであるパラメーターや変数など、任意のオブジェクトで使用できます。 オブジェクトではない式に対してプロパティ アクセスを使用すると、エラーが発生します。

### <a name="example"></a>例

次の例は、オブジェクト変数と、プロパティにアクセスする方法を示しています。

```bicep
var x = {
  y: {
    z: 'Hello'
    a: true
  }
  q: 42
}

output outputZ string = x.y.z
output outputQ int = x.q
```

例の出力を次に示します。

| 名前 | 種類 | 値 |
| ---- | ---- | ---- |
| `outputZ` | string | 'Hello' |
| `outputQ` | 整数 (integer) | 42 |

通常、Bicep ファイルにデプロイされたリソースでは、プロパティ アクセサーを使用します。 次の例では、パブリック IP アドレスを作成し、プロパティ アクセサーを使用して、デプロイされたリソースから値を返します。

```bicep
resource publicIp 'Microsoft.Network/publicIPAddresses@2020-06-01' = {
  name: publicIpResourceName
  location: location
  properties: {
    publicIPAllocationMethod: dynamicAllocation ? 'Dynamic' : 'Static'
    dnsSettings: {
      domainNameLabel: publicIpDnsLabel
    }
  }
}

// Use property accessor to get value
output ipFqdn string = publicIp.properties.dnsSettings.fqdn
```

## <a name="next-steps"></a>次のステップ

- この例を実行するには、Azure CLI または Azure PowerShell を使用して、[Bicep ファイルをデプロイ](./quickstart-create-bicep-use-visual-studio-code.md#deploy-the-bicep-file)します。
- Bicep ファイルの作成方法については、「[クイックスタート: Visual Studio Code を使用して Bicep ファイルを作成する](./quickstart-create-bicep-use-visual-studio-code.md)」を参照してください。
- Bicep の型のエラーを解決する方法については、「[Bicep の any 関数](./bicep-functions-any.md)」を参照してください。
