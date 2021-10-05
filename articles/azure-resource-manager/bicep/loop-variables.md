---
title: Bicep で変数の複数のインスタンスを定義する
description: 変数を作成するときに、反復するための Bicep 変数ループを使用します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 14721057379217ae9d14b97c94483435dde08203
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831981"
---
# <a name="variable-iteration-in-bicep"></a>Bicep での変数の反復処理

この記事では、Bicep ファイルで 1 つの変数に対して複数の値を作成する方法について説明します。 `variables` 宣言にループを追加し、変数の項目数を動的に設定できます。 これにより、Bicep ファイルで構文を繰り返さないようにします。

[モジュール](loop-modules.md)、[リソース](loop-resources.md)、[リソース内のプロパティ](loop-properties.md)、[出力](loop-outputs.md)でもコピーを使用できます。

### <a name="microsoft-learn"></a>Microsoft Learn

ループの詳細とハンズオン ガイダンスについては、**Microsoft Learn** の「[条件とループを使用して柔軟な Bicep テンプレートを作成する](/learn/modules/build-flexible-bicep-templates-conditions-loops/)」を参照してください。

## <a name="syntax"></a>構文

ループを使用すると、次の方法で複数の変数を宣言できます。

- ループ インデックスの使用。

  ```bicep
  var <variable-name> = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

  詳細については、「[ループ インデックス](#loop-index)」を参照してください。

- 配列の反復処理。

  ```bicep
  var <variable-name> = [for <item> in <collection>: {
    <properties>
  }]

  ```

  詳細については、[ループ配列](#loop-array)に関するページを参照してください。

- 配列とインデックスの反復処理。

  ```bicep
  var <variable-name> = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>ループの制限

Bicep ファイルのループの反復処理に、負の数を指定したり、800 回を超える数を指定したりすることはできません。 

## <a name="loop-index"></a>ループ インデックス

次の例では、文字列値の配列を作成する方法を示します。

```bicep
param itemCount int = 5

var stringArray = [for i in range(0, itemCount): 'item${(i + 1)}']

output arrayResult array = stringArray
```

出力には、次の値を含む配列が返されます。

```json
[
  "item1",
  "item2",
  "item3",
  "item4",
  "item5"
]
```

次の例では、`name`、`diskSizeGB`、および `diskIndex` という 3 つのプロパティを持つオブジェクトの配列を作成する方法を示します。

```bicep
param itemCount int = 5

var objectArray = [for i in range(0, itemCount): {
  name: 'myDataDisk${(i + 1)}'
  diskSizeGB: '1'
  diskIndex: i
}]

output arrayResult array = objectArray
```

出力には、次の値を含む配列が返されます。

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
  },
  {
    "name": "myDataDisk4",
    "diskSizeGB": "1",
    "diskIndex": 3
  },
  {
    "name": "myDataDisk5",
    "diskSizeGB": "1",
    "diskIndex": 4
  }
]
```

## <a name="loop-array"></a>ループ配列

次の例では、パラメーターとして渡された配列に対してループ処理を行います。 パラメーターから、必要な形式のオブジェクトが変数によって構築されます。

```bicep
@description('An array that contains objects with properties for the security rules.')
param securityRules array = [
  {
    name: 'RDPAllow'
    description: 'allow RDP connections'
    direction: 'Inbound'
    priority: 100
    sourceAddressPrefix: '*'
    destinationAddressPrefix: '10.0.0.0/24'
    sourcePortRange: '*'
    destinationPortRange: '3389'
    access: 'Allow'
    protocol: 'Tcp'
  }
  {
    name: 'HTTPAllow'
    description: 'allow HTTP connections'
    direction: 'Inbound'
    priority: 200
    sourceAddressPrefix: '*'
    destinationAddressPrefix: '10.0.1.0/24'
    sourcePortRange: '*'
    destinationPortRange: '80'
    access: 'Allow'
    protocol: 'Tcp'
  }
]


var securityRulesVar = [for rule in securityRules: {
  name: rule.name
  properties: {
    description: rule.description
    priority: rule.priority
    protocol: rule.protocol
    sourcePortRange: rule.sourcePortRange
    destinationPortRange: rule.destinationPortRange
    sourceAddressPrefix: rule.sourceAddressPrefix
    destinationAddressPrefix: rule.destinationAddressPrefix
    access: rule.access
    direction: rule.direction
  }
}]

resource netSG 'Microsoft.Network/networkSecurityGroups@2020-11-01' = {
  name: 'NSG1'
  location: resourceGroup().location
  properties: {
    securityRules: securityRulesVar
  }
}
```

## <a name="next-steps"></a>次のステップ

- ループのその他の使用方法については、以下を参照してください。
  - [Bicep でのリソースの繰り返し](loop-resources.md)
  - [Bicep でのモジュールの反復処理](loop-modules.md)
  - [Bicep でのプロパティの繰り返し](loop-properties.md)
  - [Bicep での出力の繰り返し](loop-outputs.md)
- ループで作成されたリソースへの依存関係を設定する方法については、「[リソースの依存関係を設定する](./resource-declaration.md#set-resource-dependencies)」を参照してください。
