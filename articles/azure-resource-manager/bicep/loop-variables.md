---
title: Bicep で変数の複数のインスタンスを定義する
description: 変数を作成するときに、反復するための Bicep 変数ループを使用します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: be97b88156600121deb1b870940a1a40af84fbfc
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026744"
---
# <a name="variable-iteration-in-bicep"></a>Bicep での変数の反復処理

この記事では、Bicep ファイルで 1 つの変数に対して複数の値を作成する方法について説明します。 `variables` セクションにループを追加し、デプロイ時に変数の項目数を動的に設定できます。 また、Bicep ファイルで構文を繰り返さないようにします。

[リソース](loop-resources.md)、[リソース内のプロパティ](loop-properties.md)、および[出力](loop-outputs.md)でもコピーを使用できます。

## <a name="syntax"></a>構文

ループを使用すると、次の方法で複数の変数を宣言できます。

- 配列の反復処理。

  ```bicep
  var <variable-name> = [for <item> in <collection>: {
    <properties>
  }]

  ```

- 配列の要素の反復処理。

  ```bicep
  var <variable-name> = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

- ループ インデックスの使用。

  ```bicep
  var <variable-name> = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>ループの制限

Bicep ファイルのループの反復処理に、負の数を指定したり、800 回を超える数を指定したりすることはできません。 Bicep ファイルをデプロイするには、最新バージョンの [Bicep ツール](install.md)をインストールします。

## <a name="variable-iteration"></a>変数の反復処理

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

## <a name="example-templates"></a>サンプル テンプレート

次の例では、1 つの変数に対して複数の値を作成するための一般的なシナリオを示します。

|Template  |説明  |
|---------|---------|
|[ループ変数](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/loopvariables.bicep) | 変数を反復する方法を示します。 |
|[Multiple security rules](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.bicep) |ネットワーク セキュリティ グループに複数のセキュリティ規則をデプロイします。 セキュリティ規則はパラメーターから構築されます。 パラメーターについては、[複数の NSG パラメーター ファイル](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)に関するページを参照してください。 |

## <a name="next-steps"></a>次のステップ

- ループのその他の使用方法については、以下を参照してください。
  - [Bicep ファイルでのリソースの反復処理](loop-resources.md)
  - [Bicep ファイルでのプロパティの反復処理](loop-properties.md)
  - [Bicep ファイルでの出力の反復処理](loop-outputs.md)
- Bicep ファイルのセクションの詳細については、「[Bicep ファイルの構造と構文について](file.md)」を参照してください。
- 複数のリソースをデプロイする方法については、「[Bicep モジュールの使用](modules.md)」を参照してください。
- ループで作成されたリソースへの依存関係を設定する方法については、「[リソースの依存関係を設定する](./resource-declaration.md#set-resource-dependencies)」を参照してください。
- PowerShell を使用してデプロイする方法については、「[Bicep と Azure PowerShell を使用してリソースをデプロイする](deploy-powershell.md)」を参照してください。
- Azure CLI を使用してデプロイする方法については、「[Bicep と Azure CLI を使用してリソースをデプロイする](deploy-cli.md)」を参照してください。
