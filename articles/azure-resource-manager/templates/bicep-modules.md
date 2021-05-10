---
title: Bicep モジュール
description: モジュールを定義して使用する方法、およびモジュール スコープを使用する方法について説明します。
ms.topic: conceptual
ms.date: 03/30/2021
ms.openlocfilehash: 6c325bbbe265e13241119761373985ca4552b158
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967882"
---
# <a name="use-bicep-modules-preview"></a>Bicep モジュールを使用する (プレビュー)

Bicep を使用すると、複雑なソリューションをモジュールに分割できます。 Bicep モジュールは、まとめてデプロイされる 1 つ以上のリソースのセットです。 モジュールは、未加工のリソース宣言の複雑な詳細を取り除くことで、読みやすさ向上させます。 これらのモジュールは、再利用したり、他のユーザーと共有したりすることができます。 [テンプレートの仕様](./template-specs.md)と組み合わせることで、モジュール性とコードの再利用の方法を生み出すことができます。 Bicep モジュールは、デプロイ用に[入れ子になったテンプレート](./linked-templates.md#nested-template)で単一の ARM テンプレートにトランスパイルされます。 Bicep では、[_dependsOn_](./template-syntax.md#resources) は自動的に処理されます。

チュートリアルについては、「[チュートリアル: Bicep モジュールを追加する](./bicep-tutorial-add-modules.md)」を参照してください。

## <a name="define-modules"></a>モジュールを定義する

すべての Bicep ファイルをモジュールとして使用できます。 モジュールは、他の Bicep ファイルへのコントラクトとしてパラメーターと出力のみを公開します。 パラメーターと出力はどちらも省略可能です。

次の Bicep ファイルを直接デプロイすると、ストレージ アカウントを作成したり、モジュールとして使用したりできます。  次のセクションでは、モジュールを使用する方法について説明します。

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_RAGRS'
  'Standard_ZRS'
  'Premium_LRS'
  'Premium_ZRS'
  'Standard_GZRS'
  'Standard_RAGZRS'
])
param storageSKU string = 'Standard_LRS'
param location string

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

output storageEndpoint object = stg.properties.primaryEndpoints
```

出力は、親の Bicep ファイルに値を渡すために使用されます。

## <a name="consume-modules"></a>モジュールを使用する

モジュールを使用するには、_module_ キーワードを使用します。 次の Bicep ファイルは、参照されているモジュール ファイルで定義されているリソースをデプロイします。

```bicep
@minLength(3)
@maxLength(11)
param namePrefix string
param location string = resourceGroup().location

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

- **モジュール**: キーワード。
- **シンボリック名** (stgModule): モジュールの識別子。
- **モジュール ファイル**: この例のモジュールのパスは、相対パス (./storageAccount.bicep) を使用して指定されています。 Bicep のすべてのパスは、クロスプラットフォームでの一貫したコンパイルを保証するために、スラッシュ (/) ディレクトリ区切り文字を使用して指定する必要があります。 Windows の円記号 (\\) 文字はサポートされていません。
- モジュールを使用する際には、**_name_** プロパティ (storageDeploy) が必要です。 Bicep がテンプレート IL を生成するとき、このフィールドは、モジュールに対して生成される入れ子になったデプロイ リソースの名前として使用されます。

    ```json
    ...
    ...
    "resources": [
      {
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2020-10-01",
        "name": "storageDeploy",
        "properties": {
          ...
        }
      }
    ]
    ...
    ```

モジュールから出力値を取得するには、`stgModule.outputs.storageEndpoint` ような構文を使用してプロパティ値を取得します。`stgModule` はモジュールの識別子です。

## <a name="configure-module-scopes"></a>モジュールのスコープを構成する

モジュールを宣言するときに、_scope_ プロパティを指定して、モジュールをデプロイするスコープを設定できます。

```bicep
module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: resourceGroup('someOtherRg') // pass in a scope to a different resourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}
```

_scope_ プロパティは、モジュールのターゲット スコープと親のターゲット スコープが同じ場合には省略できます。 scope プロパティが指定されていない場合、モジュールは親のターゲット スコープにデプロイされます。

次の Bicep ファイルは、リソース グループを作成し、そのリソース グループにモジュールをデプロイする方法を示しています。

```bicep
// set the target scope for this file
targetScope = 'subscription'

@minLength(3)
@maxLength(11)
param namePrefix string

param location string = deployment().location

var resourceGroupName = '${namePrefix}rg'
resource myResourceGroup 'Microsoft.Resources/resourceGroups@2020-01-01' = {
  name: resourceGroupName
  location: location
  scope: subscription()
}

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: myResourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

## <a name="next-steps"></a>次のステップ

- チュートリアルについては、「[チュートリアル: Bicep モジュールを追加する](./bicep-tutorial-add-modules.md)」を参照してください。
