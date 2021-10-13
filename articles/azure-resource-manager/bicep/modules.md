---
title: Bicep モジュール
description: モジュールを定義して使用する方法、およびモジュール スコープを使用する方法について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 10/05/2021
ms.openlocfilehash: bd5069db6a2ad9cb14f5f0b3bc28612afa519727
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619597"
---
# <a name="use-bicep-modules"></a>Bicep モジュールを使用する

Bicep を使用すると、複雑なソリューションをモジュールに分割できます。 Bicep モジュールとは、別の Bicep ファイルからデプロイされた単なる Bicep ファイルです。 リソース宣言の複雑な詳細をモジュールにカプセル化することができるので、そのモジュールを使用するファイルの読みやすさが向上します。 これらのモジュールは、再利用したり、他のユーザーと共有したりすることができます。 Bicep モジュールは、[入れ子になったテンプレート](../templates/linked-templates.md#nested-template)を持つ単一の Azure Resource Manager テンプレートに変換されて、デプロイされます。

この記事では、モジュールを定義して使用する方法について説明します。

チュートリアルについては、[Bicep テンプレートを使用した Azure リソースのデプロイ](/learn/modules/deploy-azure-resources-by-using-bicep-templates/)の記事を参照してください。

## <a name="define-modules"></a>モジュールを定義する

すべての Bicep ファイルをモジュールとして使用できます。 モジュールは、他の Bicep ファイルへのコントラクトとしてパラメーターと出力のみを公開します。 パラメーターと出力は省略可能です。

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

module stgModule 'storageAccount.bicep' = {
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
- **モジュール ファイル**: モジュール ファイルは、相対パスで参照する必要があります。 Bicep のすべてのパスは、クロスプラットフォームでの一貫したコンパイルを保証するために、スラッシュ (/) ディレクトリ区切り文字を使用して指定する必要があります。 Windows の円記号 (\\) 文字はサポートされていません。 パスにはスペースを含めることができます。
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
- **_params_** プロパティには、モジュール ファイルに渡すパラメーターが含まれます。 そのパラメーターは Bicep ファイルに定義されているパラメーターに一致します。

リソースと同様に、モジュールは他のモジュールやリソースのデプロイに依存しない限り、並列でデプロイされます。 依存関係の詳細については、「[リソースの依存関係を設定する](resource-declaration.md#set-resource-dependencies)」を参照してください。

モジュールから出力値を取得するには、`stgModule.outputs.storageEndpoint` ような構文を使用してプロパティ値を取得します。`stgModule` はモジュールの識別子です。

モジュールを条件付きでデプロイできます。 [リソースを条件付きでデプロイする](conditional-resource-deployment.md)ときと同じ **if** 構文を使用します。

```bicep
param deployZone bool

module dnsZone 'dnszones.bicep' = if (deployZone) {
  name: 'myZoneModule'
}
```

ループを使用すると、モジュールを複数回デプロイできます。 詳細については、「[Bicep でのモジュールの反復処理](loop-modules.md)」を参照してください。

## <a name="configure-module-scopes"></a>モジュールのスコープを構成する

モジュールを宣言するとき、そのモジュールのスコープとして、中に含まれている Bicep ファイルとは異なるスコープを設定できます。 モジュールのスコープを設定するには、`scope` プロパティを使用します。 scope プロパティが指定されていない場合、モジュールは親のターゲット スコープにデプロイされます。

次の Bicep ファイルは、リソース グループを作成し、そのリソース グループにモジュールをデプロイする方法を示しています。

```bicep
// set the target scope for this file
targetScope = 'subscription'

@minLength(3)
@maxLength(11)
param namePrefix string

param location string = deployment().location

var resourceGroupName = '${namePrefix}rg'
resource myResourceGroup 'Microsoft.Resources/resourceGroups@2021-04-01' = {
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

次の例では、既存のリソース グループにデプロイします。

```bicep
targetScope = 'subscription'

resource firstRG 'Microsoft.Resources/resourceGroups@2021-04-01' existing = {
  name: 'demogroup1'
}

resource secondRG 'Microsoft.Resources/resourceGroups@2021-04-01' existing = {
  name: 'demogroup2'
}

module storage1 'storageAccount.bicep' = {
  name: 'westusdeploy'
  scope: firstRG
  params: {
    storagePrefix: 'stg1'
    location: 'westus'
  }
}

module storage2 'storageAccount.bicep' = {
  name: 'eastusdeploy'
  scope: secondRG
  params: {
    storagePrefix: 'stg2'
    location: 'eastus'
  }
}
```

scope プロパティには、有効なスコープ オブジェクトを設定する必要があります。 Bicep ファイルを使用してリソース グループ、サブスクリプション、または管理グループをデプロイする場合、モジュールのスコープをそのリソースのシンボリック名に設定することができます。 また、スコープ関数を使用して有効なスコープを取得することもできます。

以下がその関数です。

- [resourceGroup](bicep-functions-scope.md#resourcegroup)
- [subscription](bicep-functions-scope.md#subscription)
- [managementGroup](bicep-functions-scope.md#managementgroup)
- [tenant](bicep-functions-scope.md#tenant)

次の例では、`managementGroup` 関数を使用してスコープを設定します。

```bicep
param managementGroupName string

module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupName)
}
```

## <a name="next-steps"></a>次のステップ

- 機密値をモジュールに渡すには、[getSecret](bicep-functions-resource.md#getsecret) 関数を使用します。
- ループを使用すると、モジュールを複数回デプロイできます。 詳細については、「[Bicep でのモジュールの反復処理](loop-modules.md)」を参照してください。
