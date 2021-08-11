---
title: Bicep ファイルの構造と構文
description: 宣言型の構文を使用した Bicep ファイルの構造とプロパティについて説明します。
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: a9dcb83b54a665a71c8f1095a682c9dabcafd039
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026688"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>Bicep ファイルの構造と構文について

この記事では、Bicep ファイルの構造について説明します。 ファイルの各種セクションとそれらのセクションで使用できるプロパティを紹介します。

この記事は、Bicep ファイルにある程度慣れているユーザーを対象としています。 Bicep ファイルの構造に関する詳細情報を提供します。 Bicep ファイルを作成する手順を説明したチュートリアルについては、[「クイックスタート: Visual Studio Code を使用した Bicep ファイルの作成」](./quickstart-create-bicep-use-visual-studio-code.md)を参照してください。

## <a name="bicep-format"></a>Bicep 形式

Bicep ファイルには次の要素があります。 Bicep は宣言型言語であり、要素を任意の順序で表示できます。  命令型言語とは異なり、要素の順序は配置の処理方法に影響しません。

```bicep
targetScope = '<scope>'

@<decorator>(<argument>)
param <parameter-name> <parameter-data-type> = <default-value>

var <variable-name> = <variable-value>

resource <resource-symbolic-name> '<resource-type>@<api-version>' = {
  <resource-properties>
}

// conditional deployment
resource <resource-symbolic-name> '<resource-type>@<api-version>' = if (<condition-to-deploy>) {
  <resource-properties>
}

// iterative deployment
@<decorator>(<argument>)
resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
  <resource-properties>
}]

module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// conditional deployment
module <module-symbolic-name> '<path-to-file>' = if (<condition-to-deploy>) {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// iterative deployment
module <module-symbolic-name> '<path-to-file>' = [for <item> in <collection>: {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}]

// deploy to different scope
module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  scope: <scope-object>
  params: {
    <parameter-names-and-values>
  }
}

output <output-name> <output-data-type> = <output-value>

// iterative output
output <output-name> array = [for <item> in <collection>: {
  <output-properties>
}]
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

モジュールでは、Bicep ファイルの残りの部分とは異なるスコープを指定できます。 詳細については、[「モジュール スコープの構成」](modules.md#configure-module-scopes)を参照してください

## <a name="parameters"></a>パラメーター

パラメーターは、さまざまなデプロイに応じて変化する必要がある値に使用します。 パラメーターには既定値を定義することができ、これは、デプロイ中に値が指定されない場合に使用されます。

たとえば、リソースのさまざまなサイズを指定するための SKU パラメーターを追加することができます。 Bicep 関数を使用して、リソース グループの場所の取得といった、既定値を作成できます。

```bicep
param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location
```

使用できるデータ型については、[Bicep のデータ型](data-types.md)に関する記事を参照してください。

パラメーターの名前を変数、モジュール、またはリソースと同じにすることはできません。

詳細については、[Bicep のパラメーター](./parameters.md)に関する記事を参照してください。

## <a name="parameter-decorators"></a>パラメーターのデコレーター

パラメーターごとに 1 つ以上のデコレーターを追加できます。 これらのデコレーターによって、パラメーターに使用できる値を定義します。 次の例では、デプロイできる SKU を Bicep ファイルを使用して指定します。

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

次の表では、使用できるデコレーターとそれらの使用方法について説明します。

| デコレーター | [適用対象] | 引数 | 説明 |
| --------- | ---- | ----------- | ------- |
| [許可] | all | array | パラメーターに使用できる値。 このデコレーターを使用して、ユーザーが正しい値を指定するようにします。 |
| description | all | string | パラメーターの使用方法を説明するテキスト。 説明は、ポータルを通じてユーザーに表示されます。 |
| maxLength | array、string | INT | 文字列および配列のパラメーターの最大長。 この値は包含値です。 |
| maxValue | INT | INT | 整数パラメーターの最大値。 この値は包含値です。 |
| metadata | all | object | パラメーターに適用するカスタム プロパティ。 description デコレーターに相当する description プロパティを含めることができます。 |
| minLength | array、string | INT | 文字列および配列のパラメーターの最小長。 この値は包含値です。 |
| minValue | INT | INT | 整数パラメーターの最小値。 この値は包含値です。 |
| secure | string、object | なし | パラメーターを、セキュリティで保護されているとしてマークします。 セキュリティで保護されたパラメーターの値はデプロイ履歴に保存されず、ログに記録されません。 詳細については、「[セキュリティで保護された文字列とオブジェクト](data-types.md#secure-strings-and-objects)」を参照してください。 |

## <a name="variables"></a>変数

Bicep ファイル内で繰り返される複雑な式には変数を使用します。 たとえば、複数の値を連結して構成されるリソース名の変数を追加できます。

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

変数には[データ型](data-types.md)を指定しません。 代わりに、データ型は値から推定されます。

変数の名前を変数、パラメーター、モジュール、またはリソースと同じにすることはできません。

詳細については、[Bicep の変数](./variables.md)に関する記事を参照してください。

## <a name="resource"></a>リソース

デプロイするリソースを定義するには `resource` キーワードを使用します。 リソース宣言には、リソースのシンボリック名が含まれます。 このシンボリック名を、リソースから値を取得する必要がある場合に Bicep ファイルの他の部分で使用します。

リソース宣言には、リソースの種類と API バージョンも含まれます。

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

リソース宣言には、リソースの種類のプロパティを含めます。 これらのプロパティは、リソースの種類ごとに固有です。

詳細については、[Bicep のリソース宣言](resource-declaration.md)に関する記事を参照してください。

[リソースを条件付きでデプロイ](conditional-resource-deployment.md)するには、`if` 式を追加します。

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
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

リソースの種類から[複数のインスタンスをデプロイ](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md)するには、`for` 式を追加します。 この式では、配列のメンバーに対して反復処理を行うことができます。

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  name: storageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}]
```

リソースの名前を、パラメーター、変数またはモジュールと同じにすることはできません。

## <a name="modules"></a>モジュール

モジュールを使用して、再利用するコードが含まれる他の Bicep ファイルにリンクします。 モジュールには、デプロイするリソースが 1 つ以上含まれています。 これらのリソースは、Bicep ファイル内の他のリソースと共にデプロイされます。

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

モジュールの名前を、パラメーター、変数、またはリソースと同じにすることはできません。

リソースと同様に、モジュールも条件付きで、または反復的にデプロイできます。 モジュールの構文は、リソースと同じです。

詳細については、「[Bicep モジュールを使用する](./modules.md)」を参照してください。

## <a name="resource-and-module-decorators"></a>リソースとモジュールのデコレーター

デコレーターを、リソースまたはモジュールの定義に追加できます。 サポートされているデコレーターは `batchSize(int)` のみです。 これは、`for` 式を使用するリソースまたはモジュールの定義だけに適用できます。

既定では、リソースは並列でデプロイされます。 完了する順序はわかりません。 `batchSize` デコレーターを追加する場合、インスタンスを順次にデプロイします。 並列でデプロイするインスタンスの数を指定するには、整数引数を使用します。

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

詳細については、[「バッチでのデプロイ」](loop-resources.md#deploy-in-batches)を参照してください。

## <a name="outputs"></a>出力

出力を使用して、デプロイから値が返されるようにします。 通常は、別の操作に値を再利用する必要がある場合に、デプロイされたリソースからその値が返されるようにします。

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

出力値には[データ型](data-types.md)を指定します。

出力の名前を、パラメーター、変数、モジュール、またはリソースと同じにすることはできません。

詳細については、[Bicep の出力](./outputs.md)に関する記事を参照してください。

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

## <a name="next-steps"></a>次のステップ

Bicep の概要については、「[Bicep とは](./overview.md)」を参照してください。 Bicep データ型については、[「データ型」](./data-types.md)を参照してください。
