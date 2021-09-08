---
title: Bicep でプロパティの複数のインスタンスを定義する
description: Bicep プロパティ ループを使用して、リソース プロパティの作成時に反復処理します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 6e9d41136401b28cf330bc828947d35a67c69a43
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225960"
---
# <a name="property-iteration-in-bicep"></a>Bicep でのプロパティの繰り返し

この記事では、Bicep ファイルでプロパティの複数のインスタンスを作成する方法について説明します。 リソースの `properties` セクションにループを追加し、プロパティの項目数を動的に設定できます。 これにより、Bicep ファイルで構文を繰り返さないようにします。

ループをプロパティに適用する場合でも、最上位リソースでのみループを使用できます。 子リソースを最上位リソースに変更する方法の詳細については、「[子リソースの反復処理](loop-resources.md#iteration-for-a-child-resource)」を参照してください。

[モジュール](loop-modules.md)、[リソース](loop-resources.md)、[変数](loop-variables.md)、[出力](loop-outputs.md)でもループを使用できます。

## <a name="syntax"></a>構文

ループを使用すると、次の方法で複数のプロパティを宣言できます。

- ループ インデックスの使用。

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

- 配列の反復処理。

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }]
  ```

  詳細については、「[配列をループする](#loop-array)」を参照してください。

- 配列とインデックスの反復処理。

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>ループの制限

Bicep ファイルのループの反復処理に、負の数を指定したり、800 回を超える数を指定したりすることはできません。 

## <a name="loop-array"></a>配列をループする

この例では、`subnets` プロパティの配列を反復処理して、仮想ネットワーク内に 2 つのサブネットを作成します。

```bicep
param rgLocation string = resourceGroup().location

var subnets = [
  {
    name: 'api'
    subnetPrefix: '10.144.0.0/24'
  }
  {
    name: 'worker'
    subnetPrefix: '10.144.1.0/24'
  }
]

resource vnet 'Microsoft.Network/virtualNetworks@2020-07-01' = {
  name: 'vnet'
  location: rgLocation
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.144.0.0/20'
      ]
    }
    subnets: [for subnet in subnets: {
      name: subnet.name
      properties: {
        addressPrefix: subnet.subnetPrefix
      }
    }]
  }
}
```

## <a name="next-steps"></a>次のステップ

- ループのその他の使用方法については、以下を参照してください。
  - [Bicep でのリソースの繰り返し](loop-resources.md)
  - [Bicep でのモジュールの反復処理](loop-modules.md)
  - [Bicep での変数の繰り返し](loop-variables.md)
  - [Bicep での出力の繰り返し](loop-outputs.md)
- ループで作成されたリソースへの依存関係を設定する方法については、「[リソースの依存関係を設定する](./resource-declaration.md#set-resource-dependencies)」を参照してください。
