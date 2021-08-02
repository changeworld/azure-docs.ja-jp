---
title: Bicep で出力値の複数のインスタンスを定義する
description: 反復するための Bicep 出力ループを使用して、デプロイ値を返します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: cc931b7e0d65804892176a2965f87022a2becb7b
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026753"
---
# <a name="output-iteration-in-bicep"></a>Bicep での出力の反復処理

この記事では、Bicep ファイルで 1 つの出力に対して複数の値を作成する方法について説明します。 ファイルの `output` セクションにループを追加し、デプロイ中に複数の項目を動的に返すことができます。

[リソース](loop-resources.md)、[リソース内のプロパティ](loop-properties.md)、および[変数](loop-variables.md)でも、ループを使用できます。

## <a name="syntax"></a>構文

ループを使用すると、次の方法でデプロイ中に複数の項目を返すことができます。

- 配列の反復処理。

  ```bicep
  output <output-name> array = [for <item> in <collection>: {
    <properties>
  }]

  ```

- 配列の要素の反復処理。

  ```bicep
  output <output-name> array = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

- ループ インデックスの使用。

  ```bicep
  output <output-name> array = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>ループの制限

Bicep ファイルのループの反復処理に、負の数を指定したり、800 回を超える数を指定したりすることはできません。 Bicep ファイルをデプロイするには、最新バージョンの [Bicep ツール](install.md)をインストールします。

## <a name="output-iteration"></a>出力の反復処理

次の例では、可変数のストレージ アカウントが作成され、各ストレージ アカウントのエンドポイントが返されます。

```bicep
param rgLocation string = resourceGroup().location
param storageCount int = 2

var baseNameVar = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, storageCount): {
  name: '${i}${baseNameVar}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]

output storageEndpoints array = [for i in range(0, storageCount): reference('${i}${baseNameVar}').primaryEndpoints.blob]
```

出力には、次の値を含む配列が返されます。

```json
[
  "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
  "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

次の例では、新しいストレージ アカウントから 3 つのプロパティが返されます。

```bicep
param rgLocation string = resourceGroup().location
param storageCount int = 2

var baseNameVar = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, storageCount): {
  name: '${i}${baseNameVar}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]

output storageInfo array = [for i in range(0, storageCount): {
  id: reference('${i}${baseNameVar}', '2021-02-01', 'Full').resourceId
  blobEndpoint: reference('${i}${baseNameVar}').primaryEndpoints.blob
  status: reference('${i}${baseNameVar}').statusOfPrimary
}]
```

出力には、次の値を含む配列が返されます。

```json
[
  {
    "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
    "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  },
  {
    "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
    "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  }
]
```

この例では、リソース モジュールまたはモジュール コレクションへの直接参照が出力ループでサポートされていないため、配列インデックスを使用します。

```bicep
param rgLocation string = resourceGroup().location

var stgNames = [
  'demostg1'
  'demostg2'
  'demostg3'
]

resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = [for name in stgNames: {
  name: name
  location: rgLocation
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
}]

output stgOutput array = [for (name, i) in stgNames: {
  name: stg[i].name
  resourceId: stg[i].id
}]
```

出力には、次の値を含む配列が返されます。

```json
[
  {
    "name": "demostg1",
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/demostg1"
  },
  {
    "name": "demostg2",
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/demostg2"
  },
  {
    "name": "demostg3",
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/demostg3"
  }
]
```

## <a name="next-steps"></a>次のステップ

- ループのその他の使用方法については、以下を参照してください。
  - [Bicep ファイルでのリソースの反復処理](loop-resources.md)
  - [Bicep ファイルでのプロパティの反復処理](loop-properties.md)
  - [Bicep ファイルでの変数の反復処理](loop-variables.md)
- Bicep ファイルのセクションの詳細については、「[Bicep ファイルの構造と構文について](file.md)」を参照してください。
- 複数のリソースをデプロイする方法については、「[Bicep モジュールの使用](modules.md)」を参照してください。
- ループで作成されたリソースへの依存関係を設定する方法については、「[リソースの依存関係を設定する](./resource-declaration.md#set-resource-dependencies)」を参照してください。
- PowerShell を使用してデプロイする方法については、「[Bicep と Azure PowerShell を使用してリソースをデプロイする](deploy-powershell.md)」を参照してください。
- Azure CLI を使用してデプロイする方法については、「[Bicep と Azure CLI を使用してリソースをデプロイする](deploy-cli.md)」を参照してください。
