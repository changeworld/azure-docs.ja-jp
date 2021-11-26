---
title: Bicep ファイルの構造と構文
description: 宣言型の構文を使用した Bicep ファイルの構造とプロパティについて説明します。
ms.topic: conceptual
ms.date: 11/17/2021
ms.openlocfilehash: 7483335facd2123153be3e35516011e119252114
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132707599"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>Bicep ファイルの構造と構文について

この記事では、Bicep ファイルの構造と構文について説明します。 ファイルの各種セクションとそれらのセクションで使用できるプロパティを紹介します。

Bicep ファイルを作成する手順を説明したチュートリアルについては、[「クイックスタート: Visual Studio Code を使用した Bicep ファイルの作成」](./quickstart-create-bicep-use-visual-studio-code.md)を参照してください。

## <a name="bicep-format"></a>Bicep 形式

Bicep は宣言型言語であり、要素を任意の順序で表示できます。 命令型言語とは異なり、要素の順序は配置の処理方法に影響しません。

Bicep ファイルには次の要素があります。

```bicep
targetScope = '<scope>'

@<decorator>(<argument>)
param <parameter-name> <parameter-data-type> = <default-value>

var <variable-name> = <variable-value>

resource <resource-symbolic-name> '<resource-type>@<api-version>' = {
  <resource-properties>
}

module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

output <output-name> <output-data-type> = <output-value>
```

これらの要素を実装する例を次に示します。

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location

var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}

module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

## <a name="target-scope"></a>ターゲット スコープ

既定では、ターゲット スコープは `resourceGroup` に設定されます。 リソース グループ レベルでデプロイしている場合は、Bicep ファイルでターゲット スコープを設定する必要はありません。

使用できる値は、次のとおりです。

* **resourceGroup** - 既定値であり、[リソース グループのデプロイ](deploy-to-resource-group.md)に使用されます。
* **subscription** - [サブスクリプションjのデプロイ](deploy-to-subscription.md)に使用されます。
* **managementGroup** - [管理グループのデプロイ](deploy-to-management-group.md)に使用されます。
* **tenant** - [テナントのデプロイ](deploy-to-tenant.md)に使用されます。

モジュールでは、Bicep ファイルの残りの部分とは異なるスコープを指定できます。 詳細については、[「モジュール スコープの構成」](modules.md#set-module-scope)を参照してください

## <a name="parameters"></a>パラメーター

パラメーターは、さまざまなデプロイに応じて変化する必要がある値に使用します。 パラメーターには既定値を定義することができ、これは、デプロイ中に値が指定されない場合に使用されます。

たとえば、リソースのさまざまなサイズを指定するための SKU パラメーターを追加できます。 テスト環境と運用環境のいずれかに配置するかに応じて、異なる値を渡すことができます。

```bicep
param storageSKU string = 'Standard_LRS'
```

このパラメーターは Bicep ファイルで使用できます。

```bicep
sku: {
  name: storageSKU
}
```

詳細については、[Bicep のパラメーター](./parameters.md)に関する記事を参照してください。

## <a name="parameter-decorators"></a>パラメーターのデコレーター

パラメーターごとに 1 つ以上のデコレーターを追加できます。 これらのデコレーターは、パラメーターを記述し、渡された値の制約を定義するものです。 次の例にはデコレーターが 1 つありますが、他にもたくさん利用できます。

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

使用可能なすべてのデコレーターの説明などの詳細については、「[デコレーター](parameters.md#decorators)」を参照してください。

## <a name="variables"></a>変数

複雑な式を変数にカプセル化することで、Bicep ファイルを読みやすくすることができます。 たとえば、複数の値を連結して構成されるリソース名の変数を追加できます。

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

複合式が必要な場合は常にこの変数を適用します。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
```

詳細については、[Bicep の変数](./variables.md)に関する記事を参照してください。

## <a name="resources"></a>リソース

デプロイするリソースを定義するには `resource` キーワードを使用します。 リソース宣言には、リソースのシンボリック名が含まれます。 このシンボリック名を Bicep ファイルの他の部分で使用し、リソースから値を取得します。

リソース宣言には、リソースの種類と API バージョンが含まれます。 リソース宣言の本文内に、リソースの種類に固有のプロパティを含めます。

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}
```

詳細については、[Bicep のリソース宣言](resource-declaration.md)に関する記事を参照してください。

一部のリソースには親子関係があります。 子リソースは、親リソースの中にも外側にも定義できます。

次の例では、親リソース内に子リソースを定義する方法を示します。 これには、ストレージ アカウント内に定義された子リソース (ファイル サービス) を持つストレージ アカウントが含まれます。 このファイル サービスには、その中に定義された子リソース (共有) もあります。

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/insidedeclaration.bicep" highlight="9,12":::

次の例は、親リソースの外側の子リソースを定義する方法を示しています。 parent プロパティを使用して、親子関係を識別します。 同じ 3 つのリソースが定義されています。

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/outsidedeclaration.bicep" highlight="10,12,15,17":::

詳細については、「[Bicep での子リソースの名前と種類の設定](child-resource-name-type.md)」を参照してください。

## <a name="modules"></a>モジュール

モジュールを使用すると、他の Bicep ファイル内の Bicep ファイルからコードを再利用できます。 モジュール宣言では、再利用するファイルにリンクします。 Bicep ファイルをデプロイすると、モジュール内のリソースもデプロイされます。

```bicep
module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}
```

シンボリック名を使用すると、ファイル内の別の場所からモジュールを参照できます。 たとえば、シンボリック名および出力値の名前を使用して、モジュールから出力値を取得できます。

詳細については、「[Bicep モジュールを使用する](./modules.md)」を参照してください。

## <a name="resource-and-module-decorators"></a>リソースとモジュールのデコレーター

デコレーターを、リソースまたはモジュールの定義に追加できます。 サポートされているデコレーターは `batchSize(int)` のみです。 これは、`for` 式を使用するリソースまたはモジュールの定義だけに適用できます。

既定では、リソースは並列でデプロイされます。 `batchSize` デコレーターを追加する場合、インスタンスを順次にデプロイします。

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

詳細については、[「バッチでのデプロイ」](loops.md#deploy-in-batches)を参照してください。

## <a name="outputs"></a>出力

出力を使用し、デプロイから値を返します。 通常は、別の操作に値を再利用する必要がある場合に、デプロイされたリソースからその値が返されるようにします。

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

詳細については、[Bicep の出力](./outputs.md)に関する記事を参照してください。

## <a name="loops"></a>ループ

Bicep ファイルに反復的なループを追加し、次の複数のコピーを定義できます。

* resource
* name
* 変数
* property
* output

`for` 式を使用してループを定義します。

```bicep
param moduleCount int = 2

module stgModule './example.bicep' = [for i in range(0, moduleCount): {
  name: '${i}deployModule'
  params: {
  }
}]
```

配列、オブジェクト、または整数インデックスを反復処理できます。

詳細については [、「Bicep の反復ループ」を参照してください](loops.md)。

## <a name="conditional-deployment"></a>条件付きデプロイ

条件付きでデプロイされるリソースまたはモジュールを Bicep ファイルに追加できます。 デプロイ中に条件が評価され、その結果によりデプロイ対象としてリソースまたはモジュールが決定されます。 `if` 式を使用し、条件付きデプロイを追加します。

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

詳細については [、「Bicep での条件付きデプロイ」を参照してください](conditional-resource-deployment.md)。

## <a name="whitespace"></a>空白

Bicep ファイルを作成する際、スペースとタブは無視されます。

Bicep では改行が識別されます。 次に例を示します。

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  ...
}
```

これを次のように記述することはできません。

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' =
    if (newOrExisting == 'new') {
      ...
    }
```

複数の行に[オブジェクト](./data-types.md#objects)と[配列](./data-types.md#arrays)を定義します。

## <a name="comments"></a>説明

単一行のコメントには `//` を、複数行のコメントには `/* ... */` を使用します

次の例に、単一行のコメントを示します。

```bicep
// This is your primary NIC.
resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
   ...
}
```

次の例に、複数行のコメントを示します。

```bicep
/*
  This Bicep file assumes the key vault already exists and
  is in same subscription and resource group as the deployment.
*/
param existingKeyVaultName string
```

## <a name="multi-line-strings"></a>複数行の文字列

文字列を複数の行に分割することができます。 複数行の文字列を開始および終了するには、3 つの単一引用符文字 `'''` を使用します。

複数行の文字列内の文字はそのまま処理されます。 エスケープ文字は不要です。 複数行の文字列の中に `'''` を含めることはできません。 文字列補間は現在サポートされていません。

開始の `'''` の直後に文字列を始めることも、改行を含めることもできます。 どちらの場合も、結果として得られる文字列に改行は含まれません。 Bicep ファイルの行の終わりに応じて、改行は `\r\n` または `\n` として解釈されます。

次の例に、複数行の文字列を示します。

```bicep
var stringVar = '''
this is multi-line
  string with formatting
  preserved.
'''
```

上記の例は次の JSON と同等です。

```json
"variables": {
  "stringVar": "this is multi-line\r\n  string with formatting\r\n  preserved.\r\n"
}
```

## <a name="known-limitations"></a>既知の制限事項

- 単一の apiProfile をリソースの種類ごとの apiVersion のセットにマップするために使用される apiProfile の概念はサポートされていません。
- ユーザー定義関数はサポートされていません。

## <a name="next-steps"></a>次のステップ

Bicep の概要については、「[Bicep とは](./overview.md)」を参照してください。 Bicep データ型については、[「データ型」](./data-types.md)を参照してください。
