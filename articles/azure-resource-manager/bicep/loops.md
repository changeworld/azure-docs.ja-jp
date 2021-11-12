---
title: Bicep の反復ループ
description: ループを使用して Bicep 内のコレクションを反復処理する
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: 9c38824fd3fdf10ce807b66aeccda2c0631cbc58
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269739"
---
# <a name="iterative-loops-in-bicep"></a>Bicep の反復ループ

この記事では、`for` 構文を使用してコレクション内の項目を反復処理する方法について説明します。 ループを使用すると、リソース、モジュール、変数、プロパティ、または出力の複数のコピーを定義できます。 ループを使用して、Bicep ファイル内で繰り返し構文を回避し、デプロイ時に作成するコピーの数を動的に設定します。

### <a name="microsoft-learn"></a>Microsoft Learn

ループの詳細とハンズオン ガイダンスについては、**Microsoft Learn** の「[条件とループを使用して柔軟な Bicep テンプレートを作成する](/learn/modules/build-flexible-bicep-templates-conditions-loops/)」を参照してください。

## <a name="loop-syntax"></a>Loop 構文

ループは次の方法で宣言できます。

- **整数インデックス** を使用します。 このオプションは、"この多数のインスタンスを作成します。" というシナリオがある場合に機能します。 [range 関数](bicep-functions-array.md#range)は、開始インデックスから始まり、指定された要素の数を含む整数の配列を作成します。 ループ内では、整数インデックスを使用して値を変更できます。 詳細については、「[整数インデックス](#integer-index)」を参照してください。

  ```bicep
  [for <index> in range(<startIndex>, <numberOfElements>): {
    ...
  }]
  ```

- **配列内の項目** を使用します。 このオプションは、"配列の各要素に対してインスタンスを作成します。" というシナリオがある場合に機能します。 ループ内では、現在の配列要素の値を使用して値を変更できます。 詳細については、「[配列要素](#array-elements)」を参照してください。

  ```bicep
  [for <item> in <collection>: {
    ...
  }]
  ```

- **辞書オブジェクト内の項目** を使用します。 このオプションは、"オブジェクトの各項目に対してインスタンスを作成します。" というシナリオがある場合に機能します。 [items 関数](bicep-functions-array.md#items)は、オブジェクトを配列に変換します。 ループ内では、オブジェクトのプロパティを使用して値を作成できます。 詳細については、「[辞書オブジェクト](#dictionary-object)」を参照してください。

  ```bicep
  [for <item> in items(<object>): {
    ...
  }]
  ```

- **配列内の整数インデックスと項目** を使用します。 このオプションは、"配列内の各要素に対してインスタンスを作成する必要がありますが、別の値を作成するには現在のインデックスも必要です。" というシナリオでは、次のようになります。 詳細については、「[ループ配列とインデックス](#array-and-index)」を参照してください。

  ```bicep
  [for (<item>, <index>) in <collection>: {
    ...
  }]
  ```

- **条件付きデプロイ** を追加します。 このオプションは、"複数のインスタンスを作成する必要がありますが、条件が true の場合にのみデプロイするインスタンスごとに" になります。 詳細については、「[条件判定を伴うループ](#loop-with-condition)」を参照してください。

  ```bicep
  [for <item> in <collection>: if(<condition>) {
    ...
  }]
  ```

## <a name="loop-limits"></a>ループの制限

Bicep でのループの使用には、次の制限があります。

- ループの反復処理に、負の数を指定したり、800 回を超える数を指定したりすることはできません。
- 入れ子になった子リソースを持つリソースをループすることはできません。 子リソースをトップレベル リソースに変更します。  「[子リソースの反復処理](#iteration-for-a-child-resource)」を参照してください。
- 複数レベルのプロパティに対してループを行うことはできません。

## <a name="integer-index"></a>整数インデックス

インデックスを使用する簡単な例については、文字列の配列を含む **変数** を作成します。

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

次の例では、`storageCount` パラメーターで指定されているストレージ アカウントの数を作成します。 各ストレージ アカウントの 3 つのプロパティが返されます。

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

output storageInfo array = [for i in range(0, storageCount): {
  id: storageAcct[i].id
  blobEndpoint: storageAcct[i].properties.primaryEndpoints.blob
  status: storageAcct[i].properties.statusOfPrimary
}]
```

ストレージ アカウント リソース名の作成にインデックス `i` が使用されていることに注意してください。

次の例では、モジュールを複数回配置します。

```bicep
param location string
param storageCount int = 2

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): {
  name: '${i}deploy${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

## <a name="array-elements"></a>配列要素

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

次の例では、配列に対して反復処理を行い、プロパティを定義します。 仮想ネットワーク内に 2 つのサブネットを作成します。

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/loops/loopproperty.bicep" highlight="23-28" :::

## <a name="array-and-index"></a>配列とインデックス

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

次の例では、配列とインデックスの両方の要素を使用して、新しいリソースに関する情報を出力します。

```bicep
param nsgLocation string = resourceGroup().location
param orgNames array = [
  'Contoso'
  'Fabrikam'
  'Coho'
]

resource nsg 'Microsoft.Network/networkSecurityGroups@2020-06-01' = [for name in orgNames: {
  name: 'nsg-${name}'
  location: nsgLocation
}]

output deployedNSGs array = [for (name, i) in orgNames: {
  orgName: name
  nsgName: nsg[i].name
  resourceId: nsg[i].id
}]
```

## <a name="dictionary-object"></a>辞書オブジェクト

辞書オブジェクト内の要素を反復処理するには、[items 関数](bicep-functions-array.md#items)を使用します。これにより、オブジェクトが配列に変換されます。 `value` プロパティを使用して、オブジェクトのプロパティを取得します。

```bicep
param nsgValues object = {
  nsg1: {
    name: 'nsg-westus1'
    location: 'westus'
  }
  nsg2: {
    name: 'nsg-east1'
    location: 'eastus'
  }
}

resource nsg 'Microsoft.Network/networkSecurityGroups@2020-06-01' = [for nsg in items(nsgValues): {
  name: nsg.value.name
  location: nsg.value.location
}]
```

## <a name="loop-with-condition"></a>条件判定を伴うループ

**リソースとモジュール** の場合は、ループ構文を含む `if` 式を追加して、条件に応じてコレクションを展開できます。

次の例では、ループを条件ステートメントと組み合わせて示します。 この例では、1 つの条件がモジュールのすべてのインスタンスに適用されます。

```bicep
param location string
param storageCount int = 2
param createNewStorage bool = true

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): if(createNewStorage) {
  name: '${i}deploy${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

次の例は、配列内の現在の要素に固有の条件を適用する方法を示しています。

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

## <a name="deploy-in-batches"></a>バッチ処理でのデプロイ

既定では、Azure リソースは並列でデプロイされます。 ループを使用して、あるリソースの種類のインスタンスを複数作成すると、それらのインスタンスはすべて同時にデプロイされます。 それらが作成される順序は保証されません。 Bicep ファイルの合計リソース数の上限 (800 個) 以外、並列にデプロイされるリソースの数に制限はありません。

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

順次デプロイを行う場合は、バッチ サイズを 1 に設定します。

`batchSize` デコレーターは、[sys 名前空間](bicep-functions.md#namespaces-for-functions)にあります。 このデコレーターを同じ名前の別の項目と区別する必要がある場合は、デコレータの前に「**sys**: `@sys.batchSize(2)`」を付けます。

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

- ループで作成されたリソースへの依存関係を設定する方法については、「[リソースの依存関係](./resource-declaration.md#dependencies)」を参照してください。
