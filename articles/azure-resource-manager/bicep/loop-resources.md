---
title: Bicep でリソースの複数のインスタンスをデプロイする
description: Bicep ファイルでループと配列を使用して、リソースの複数のインスタンスをデプロイします。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 1b044b4ae3f5d73ad535d44153ea3d47023aeaaa
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225310"
---
# <a name="resource-iteration-in-bicep"></a>Bicep でのリソースの繰り返し

この記事では、Bicep ファイルでリソースの複数のインスタンスを作成する方法について説明します。 `resource` 宣言にループを追加して、デプロイするリソース数を動的に設定することができます。 これにより、Bicep ファイルで構文を繰り返さないようにします。

[モジュール](loop-modules.md)、[プロパティ](loop-properties.md)、[変数](loop-variables.md)、[出力](loop-outputs.md)でもループを使用できます。

リソースをデプロイするかどうかを指定する必要がある場合は、[condition 要素](conditional-resource-deployment.md)に関する記述を参照してください。

## <a name="syntax"></a>構文

ループを使用すると、次の方法で複数のリソースを宣言できます。

- ループ インデックスの使用。

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <index> in range(<start>, <stop>): {
    <resource-properties>
  }]
  ```

  詳細については、「[ループ インデックス](#loop-index)」を参照してください。

- 配列の反復処理。

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
    <resource-properties>
  }]
  ```

  詳細については、[ループ配列](#loop-array)に関するページを参照してください。

- 配列とインデックスの反復処理。

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for (<item>, <index>) in <collection>: {
    <resource-properties>
  }]
  ```

  詳細については、「[ループ配列とインデックス](#loop-array-and-index)」を参照してください。

## <a name="loop-limits"></a>ループの制限

Bicep ファイルのループの反復処理に、負の数を指定したり、800 回を超える数を指定したりすることはできません。

## <a name="loop-index"></a>ループ インデックス

次の例では、`storageCount` パラメーターで指定されているストレージ アカウントの数を作成します。

```bicep
param rgLocation string = resourceGroup().location
param storageCount int = 2

resource storageAcct 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, storageCount): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

ストレージ アカウント リソース名の作成にインデックス `i` が使用されていることに注意してください。

## <a name="loop-array"></a>ループ配列

次の例では、`storageNames` パラメーターで指定された名前ごとに 1 つのストレージ アカウントを作成します。

```bicep
param rgLocation string = resourceGroup().location
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

resource storageAcct 'Microsoft.Storage/storageAccounts@2021-02-01' = [for name in storageNames: {
  name: '${name}${uniqueString(resourceGroup().id)}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

デプロイされたリソースから値を返す場合は、[output セクション](loop-outputs.md)内でループを使用できます。

## <a name="loop-array-and-index"></a>ループ配列とインデックス

次の例では、ストレージ アカウントを定義するときに、配列要素とインデックス値の両方を使用します。

```bicep
param storageAccountNamePrefix string

var storageConfigurations = [
  {
    suffix: 'local'
    sku: 'Standard_LRS'
  }
  {
    suffix: 'geo'
    sku: 'Standard_GRS'
  }
]

resource storageAccountResources 'Microsoft.Storage/storageAccounts@2021-02-01' = [for (config, i) in storageConfigurations: {
  name: '${storageAccountNamePrefix}${config.suffix}${i}'
  location: resourceGroup().location
  properties: {
    supportsHttpsTrafficOnly: true
    accessTier: 'Hot'
    encryption: {
      keySource: 'Microsoft.Storage'
      services: {
        blob: {
          enabled: true
        }
        file: {
          enabled: true
        }
      }
    }
  }
  kind: 'StorageV2'
  sku: {
    name: config.sku
  }
}]
```

## <a name="resource-iteration-with-condition"></a>条件を使用したリソースの反復処理

次の例は、フィルター処理されたリソース ループと組み合わされた、入れ子になったループを示しています。 フィルターは、ブール値として評価される式である必要があります。

```bicep
resource parentResources 'Microsoft.Example/examples@2020-06-06' = [for parent in parents: if(parent.enabled) {
  name: parent.name
  properties: {
    children: [for child in parent.children: {
      name: child.name
      setting: child.settingValue
    }]
  }
}]
```

フィルターは、[モジュール ループ](loop-modules.md)でもサポートされます。

## <a name="deploy-in-batches"></a>バッチ処理でのデプロイ

既定では、Resource Manager は並列でリソースを作成します。 ループを使用して、あるリソースの種類のインスタンスを複数作成すると、それらのインスタンスはすべて同時にデプロイされます。 それらが作成される順序は保証されません。 Bicep ファイルの合計リソース数の上限 (800 個) 以外、並列にデプロイされるリソースの数に制限はありません。

あるリソースの種類のすべてのインスタンスを同時に更新したくない場合があるかもしれません。 たとえば、運用環境を更新するとき、一度に特定の数だけ更新されるように更新時間をずらす必要がある場合があります。 その場合、インスタンスのサブセットをまとめてバッチ処理し、同時にデプロイされるように指定できます。 他のインスタンスは、そのバッチが完了するまで待機します。

リソースのインスタンスを順次デプロイするには、[batchSize デコレータ](./file.md#resource-and-module-decorators)を追加します。 値には、同時にデプロイするインスタンスの数を設定します。 ループ内の前のインスタンスへの依存関係が作成されるので、前のバッチが完了するまで次のバッチは実行されません。

```bicep
param rgLocation string = resourceGroup().location

@batchSize(2)
resource storageAcct 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

純粋に順次デプロイを行う場合は、バッチ サイズを 1 に設定します。

## <a name="iteration-for-a-child-resource"></a>子リソースの反復処理

入れ子にされた子リソースにループを使用することはできません。 子リソースのインスタンスを複数作成するには、子リソースを最上位レベルのリソースに変更します。

たとえば、通常はファイル サービスとファイル共有を、ストレージ アカウントの入れ子になったリソースとして定義しているとします。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
  resource service 'fileServices' = {
    name: 'default'
    resource share 'shares' = {
      name: 'exampleshare'
    }
  }
}
```

複数のファイル共有を作成するには、ファイル共有をストレージ アカウントの外に移動します。 親リソースとの関係は、`parent` プロパティを通じて定義します。

次の例では、ストレージ アカウント、ファイル サービス、および複数のファイル共有を作成する方法を示しています。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
}

resource service 'Microsoft.Storage/storageAccounts/fileServices@2021-02-01' = {
  name: 'default'
  parent: stg
}

resource share 'Microsoft.Storage/storageAccounts/fileServices/shares@2021-02-01' = [for i in range(0, 3): {
  name: 'exampleshare${i}'
  parent: service
}]
```

## <a name="next-steps"></a>次のステップ

- ループのその他の使用方法については、以下を参照してください。
  - [Bicep でのプロパティの繰り返し](loop-properties.md)
  - [Bicep での変数の繰り返し](loop-variables.md)
  - [Bicep での出力の繰り返し](loop-outputs.md)
- ループで作成されたリソースへの依存関係を設定する方法については、「[リソースの依存関係を設定する](./resource-declaration.md#set-resource-dependencies)」を参照してください。
