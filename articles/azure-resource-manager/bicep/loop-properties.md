---
title: Bicep でプロパティの複数のインスタンスを定義する
description: Bicep プロパティ ループを使用して、リソース プロパティの作成時に反復処理します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 59ca13e4f6ab29a71c543fcbe779ef616beba43b
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026752"
---
# <a name="property-iteration-in-bicep"></a>Bicep でのプロパティの繰り返し

この記事では、Bicep ファイルでプロパティの複数のインスタンスを作成する方法について説明します。 リソースの `properties` セクションにループを追加し、デプロイ時にプロパティの項目数を動的に設定できます。 また、Bicep ファイルで構文を繰り返さないようにします。

ループをプロパティに適用する場合でも、最上位リソースでのみループを使用できます。 子リソースを最上位リソースに変更する方法の詳細については、「[子リソースの反復処理](loop-resources.md#iteration-for-a-child-resource)」を参照してください。

[resources](loop-resources.md)、[variables](loop-variables.md)、および [outputs](loop-outputs.md) でもループを使用できます。

## <a name="syntax"></a>構文

ループを使用すると、次の方法で複数のプロパティを宣言できます。

- 配列の反復処理。

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }]
  ```

- 配列の要素の反復処理。

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }]
  ```

- ループ インデックスの使用。

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>ループの制限

Bicep ファイルのループの反復処理に、負の数を指定したり、800 回を超える数を指定したりすることはできません。 Bicep ファイルをデプロイするには、最新バージョンの [Bicep ツール](install.md)をインストールします。

## <a name="property-iteration"></a>プロパティの反復処理

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
  - [Bicep ファイルでのリソースの反復処理](loop-resources.md)
  - [Bicep ファイルでの変数の反復処理](loop-variables.md)
  - [Bicep ファイルでの出力の反復処理](loop-outputs.md)
- Bicep ファイルのセクションの詳細については、「[Bicep ファイルの構造と構文について](file.md)」を参照してください。
- 複数のリソースをデプロイする方法については、「[Bicep モジュールの使用](modules.md)」を参照してください。
- ループで作成されたリソースへの依存関係を設定する方法については、「[リソースの依存関係を設定する](./resource-declaration.md#set-resource-dependencies)」を参照してください。
- PowerShell を使用してデプロイする方法については、「[Bicep と Azure PowerShell を使用してリソースをデプロイする](deploy-powershell.md)」を参照してください。
- Azure CLI を使用してデプロイする方法については、「[Bicep と Azure CLI を使用してリソースをデプロイする](deploy-cli.md)」を参照してください。
