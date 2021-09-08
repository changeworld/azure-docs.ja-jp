---
title: Bicep でモジュールの複数のインスタンスをデプロイする
description: Bicep ファイルでループと配列を使用して、モジュールの複数のインスタンスをデプロイします。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/27/2021
ms.openlocfilehash: 44e5a4356cac0f252b344a7acf8b68a2f51d1ef1
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123215230"
---
# <a name="module-iteration-in-bicep"></a>Bicep でのモジュールの反復処理

この記事では、Bicep ファイルで[モジュール](modules.md)の複数のインスタンスをデプロイする方法について説明します。 `module` 宣言にループを追加して、そのモジュールをデプロイする回数を動的に設定することができます。 これにより、Bicep ファイルで構文を繰り返さないようにします。

[resources](loop-resources.md)、[properties](loop-properties.md)、[variables](loop-variables.md) および [outputs](loop-outputs.md) でもループを使用できます。

## <a name="syntax"></a>構文

ループを使用すると、次の方法で複数のモジュールを宣言できます。

- ループ インデックスの使用。

  ```bicep
  module <module-symbolic-name> '<module-file>' = [for <index> in range(<start>, <stop>): {
    <module-properties>
  }]
  ```

  詳細については、[ループ インデックス](#loop-index)に関するページを参照してください。

- 配列の反復処理。

  ```bicep
  module <module-symbolic-name> '<module-file>' = [for <item> in <collection>: {
    <module-properties>
  }]
  ```

  詳細については、[ループ配列](#loop-array)に関するページを参照してください。

- 配列とインデックスの反復処理:

  ```bicep
  module <module-symbolic-name> 'module-file' = [for (<item>, <index>) in <collection>: {
    <module-properties>
  }]
  ```

## <a name="loop-limits"></a>ループの制限

Bicep ファイルのループの反復処理に、負の数を指定したり、800 回を超える数を指定したりすることはできません。

## <a name="loop-index"></a>ループ インデックス

次の例では、`storageCount` パラメーターで指定した回数、モジュールをデプロイします。 モジュールの各インスタンスは、ストレージ アカウントを作成します。

```bicep
param location string = resourceGroup().location
param storageCount int = 2

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): {
  name: '${i}storage${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

ストレージ アカウント リソース名の作成にインデックス `i` が使用されていることに注意してください。 ストレージ アカウントは、パラメーター値としてモジュールに渡されます。

## <a name="loop-array"></a>ループ配列

次の例では、`storageNames` パラメーターで指定された名前ごとに 1 つのストレージ アカウントを作成します。 このモジュールでは、ストレージ アカウントを作成します

```bicep
param rgLocation string = resourceGroup().location
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

module stgModule './storageAccount.bicep' = [for name in storageNames: {
  name: '${name}${uniqueString(resourceGroup().id)}'
  params: {
    storageName: name
    location: location
  }
}]

```

モジュール コレクションの参照は、出力ループではサポートされていません。 コレクション内のモジュールから結果を出力するには、式に配列インデクサーを適用します。 詳細については、[出力の反復処理](loop-outputs.md)に関するページを参照してください。

## <a name="module-iteration-with-condition"></a>条件を使用したモジュールの反復処理

次の例は、フィルター処理されたモジュール ループを含む Bicep ファイルを示しています。 フィルターは、ブール値として評価される式である必要があります。

```bicep
param location string = resourceGroup().location
param storageCount int = 2
param createNewStorage bool = true

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): if(createNewStorage) {
  name: '${i}storage${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

前の例では、ブール値が `true` の場合にのみ、モジュールが呼び出されます。

フィルターは、[リソース ループ](loop-resources.md)でもサポートされます。

## <a name="deploy-in-batches"></a>バッチ処理でのデプロイ

既定では、Resource Manager は並列でリソースを作成します。 ループを使用して、あるリソースの種類のインスタンスを複数作成すると、それらのインスタンスはすべて同時にデプロイされます。 それらが作成される順序は保証されません。 Bicep ファイルの合計リソース数の上限 (800 個) 以外、並列にデプロイされるリソースの数に制限はありません。

あるリソースの種類のすべてのインスタンスを同時に更新したくない場合があるかもしれません。 たとえば、運用環境を更新するとき、一度に特定の数だけ更新されるように更新時間をずらす必要がある場合があります。 その場合、インスタンスのサブセットをまとめてバッチ処理し、同時にデプロイされるように指定できます。 他のインスタンスは、そのバッチが完了するまで待機します。

モジュールのインスタンスを順次デプロイするには、[batchSize デコレータ](./file.md#resource-and-module-decorators)を追加します。 値には、同時にデプロイするインスタンスの数を設定します。 ループ内の前のインスタンスへの依存関係が作成されるので、前のバッチが完了するまで次のバッチは実行されません。

```bicep
param location string = resourceGroup().location

@batchSize(2)
module stgModule './storageAccount.bicep' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

純粋に順次デプロイを行う場合は、バッチ サイズを 1 に設定します。

## <a name="next-steps"></a>次のステップ

- ループのその他の使用方法については、以下を参照してください。
  - [Bicep でのリソースの繰り返し](loop-resources.md)
  - [Bicep でのプロパティの繰り返し](loop-properties.md)
  - [Bicep での変数の繰り返し](loop-variables.md)
  - [Bicep での出力の繰り返し](loop-outputs.md)
- モジュールの詳細については、「[Bicep モジュールを使用する](modules.md)」をご覧ください。
- ループで作成されたリソースへの依存関係を設定する方法については、「[リソースの依存関係を設定する](./resource-declaration.md#set-resource-dependencies)」を参照してください。
