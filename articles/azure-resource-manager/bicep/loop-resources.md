---
title: Bicep でリソースの複数のインスタンスをデプロイする
description: Bicep ファイルでループと配列を使用して、リソースの複数のインスタンスをデプロイします。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: b510d2601c7f1d2724e104017707345e05fb47c7
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453343"
---
# <a name="resource-iteration-in-bicep"></a>Bicep でのリソースの繰り返し

この記事では、Bicep ファイルでリソースの複数のインスタンスを作成する方法について説明します。 ファイルの `resource` セクションにループを追加し、デプロイするリソースの数を動的に設定することができます。 また、Bicep ファイルで構文を繰り返さないようにします。

[プロパティ](loop-properties.md)、[変数](loop-variables.md)、および[出力](loop-outputs.md)でもループを使用できます。

リソースをデプロイするかどうかを指定する必要がある場合は、[condition 要素](conditional-resource-deployment.md)に関する記述を参照してください。

## <a name="syntax"></a>構文

ループを使用すると、次の方法で複数のリソースを宣言できます。

- 配列の反復処理。

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
    <resource-properties>
  }]
  ```

- 配列の要素の反復処理。

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for (<item>, <index>) in <collection>: {
    <resource-properties>
  }]
  ```

- ループ インデックスの使用。

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <index> in range(<start>, <stop>): {
    <resource-properties>
  }]
  ```

## <a name="loop-limits"></a>ループの制限

Bicep ファイルのループの反復処理に、負の数を指定したり、800 回を超える数を指定したりすることはできません。 Bicep ファイルをデプロイするには、最新バージョンの [Bicep ツール](install.md)をインストールします。

## <a name="resource-iteration"></a>リソースの反復

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

フィルターは、モジュール ループでもサポートされます。

## <a name="deploy-in-batches"></a>バッチ処理でのデプロイ

既定では、Resource Manager は並列でリソースを作成します。 ループを使用して、あるリソースの種類のインスタンスを複数作成すると、それらのインスタンスはすべて同時にデプロイされます。 それらが作成される順序は保証されません。 Bicep ファイルの合計リソース数の上限 (800 個) 以外、並列にデプロイされるリソースの数に制限はありません。

あるリソースの種類のすべてのインスタンスを同時に更新したくない場合があるかもしれません。 たとえば、運用環境を更新するとき、一度に特定の数だけ更新されるように更新時間をずらす必要がある場合があります。 その場合、インスタンスのサブセットをまとめてバッチ処理し、同時にデプロイされるように指定できます。 他のインスタンスは、そのバッチが完了するまで待機します。

リソースのインスタンスを順次デプロイするには、[batchSize デコレータ](./file.md#resource-and-module-decorators)を追加します。 値には、一度にデプロイするインスタンスの数を設定します。 ループ内の前のインスタンスへの依存関係が作成されるので、前のバッチが完了するまで次のバッチは実行されません。

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

## <a name="example-templates"></a>サンプル テンプレート

次の例は、リソースまたはプロパティの複数のインスタンスを作成するための一般的なシナリオを示しています。

|Template  |説明  |
|---------|---------|
|[ループ ストレージ](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/loopstorage.bicep) |名前にインデックス番号を含む複数のストレージ アカウントをデプロイします。 |
|[シリアル ループ ストレージ](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/loopserialstorage.bicep) |複数のストレージ アカウントを一度に 1 つずつデプロイします。 名前にはインデックス番号が含まれます。 |
|[配列を使用したループ ストレージ](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/loopstoragewitharray.bicep) |複数のストレージ アカウントをデプロイします。 名前には、配列からの値が含まれます。 |

## <a name="next-steps"></a>次のステップ

- ループのその他の使用方法については、以下を参照してください。
  - [Bicep ファイルでのプロパティの反復処理](loop-properties.md)
  - [Bicep ファイルでの変数の反復処理](loop-variables.md)
  - [Bicep ファイルでの出力の反復処理](loop-outputs.md)
- Bicep ファイルのセクションの詳細については、「[Bicep ファイルの構造と構文について](file.md)」を参照してください。
- 複数のリソースをデプロイする方法については、「[Bicep モジュールの使用](modules.md)」を参照してください。
- ループで作成されたリソースへの依存関係を設定する方法については、「[リソースの依存関係を設定する](./resource-declaration.md#set-resource-dependencies)」を参照してください。
- PowerShell を使用してデプロイする方法については、「[Bicep と Azure PowerShell を使用してリソースをデプロイする](deploy-powershell.md)」を参照してください。
- Azure CLI を使用してデプロイする方法については、「[Bicep と Azure CLI を使用してリソースをデプロイする](deploy-cli.md)」を参照してください。
