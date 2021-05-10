---
title: 出力値の複数のインスタンスを定義する
description: デプロイから値を返すときに、Azure Resource Manager テンプレート (ARM テンプレート) で copy 操作を使用して、複数回、反復処理を行います。
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 49050f4c0a494bbfb470b64704a09d8738a727f7
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385736"
---
# <a name="output-iteration-in-arm-templates"></a>ARM テンプレートでの出力の反復処理

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) で 1 つの出力に対して複数の値を作成する方法について説明します。 テンプレートの出力セクションにコピー ループを追加することにより、デプロイ時に複数の項目を動的に返すことができます。

[リソース](copy-resources.md)、[リソース内のプロパティ](copy-properties.md)、および[変数](copy-variables.md)でも、コピー ループを使用できます。

## <a name="syntax"></a>構文

# <a name="json"></a>[JSON](#tab/json)

テンプレートの出力セクションに `copy` 要素を追加し、複数の項目を返します。 この copy 要素には、次の一般的な形式があります。

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

`count` プロパティには、出力値で必要な反復回数を指定します。

`input` プロパティは、繰り返すプロパティを指定します。 `input` プロパティの値から構築された要素の配列を作成します。 それは、1 つのプロパティ (文字列など) にすることも、複数のプロパティを持つオブジェクトにすることもできます。

# <a name="bicep"></a>[Bicep](#tab/bicep)

ループを使用すると、デプロイ中に次の複数の項目を返すことができます。

- 配列の反復処理:

  ```bicep
  output <output-name> array = [for <item> in <collection>: {
    <properties>
  }]

  ```

- 配列の要素を反復処理します

  ```bicep
  output <output-name> array = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

- ループ インデックスの使用

  ```bicep
  output <output-name> array = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

---

## <a name="copy-limits"></a>コピー制限

count は 800 を超えることはできません。

count は負の数値にすることはできません。 Azure CLI、PowerShell、または REST API の最新バージョンを使用してテンプレートをデプロイする場合、ゼロを指定できます。 具体的には、次のものを使用する必要があります。

- Azure PowerShell **2.6** 以降
- Azure CLI **2.0.74** 以降
- REST API バージョン **2019-05-10** 以降
- [[Linked deployments]\(リンクされたデプロイ\)](linked-templates.md) には、デプロイ リソースの種類に API バージョン **2019-05-10** 以降を使用する必要があります

以前のバージョンの PowerShell、CLI、および REST API では、count の 0 をサポートしていません。

## <a name="outputs-iteration"></a>出力の反復

次の例では、可変数のストレージ アカウントが作成され、各ストレージ アカウントのエンドポイントが返されます。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "variables": {
    "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(), variables('baseName'))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ],
  "outputs": {
    "storageEndpoints": {
      "type": "array",
      "copy": {
        "count": "[parameters('storageCount')]",
        "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
      }
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

var baseName_var = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}${baseName_var}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]

output storageEndpoints array = [for i in range(0, storageCount): reference(${i}${baseName_var}).primaryEndpoints.blob]
```

---

前のテンプレートは、次の値を持つ配列を返します。

```json
[
  "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
  "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

次の例では、新しいストレージ アカウントから 3 つのプロパティが返されます。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "variables": {
    "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(), variables('baseName'))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ],
  "outputs": {
    "storageInfo": {
      "type": "array",
      "copy": {
        "count": "[parameters('storageCount')]",
        "input": {
          "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
          "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
          "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
        }
      }
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

var baseName_var = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}${baseName_var}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]

output storageInfo array = [for i in range(0, storageCount): {
  id: reference(concat(i, baseName_var), '2019-04-01', 'Full').resourceId
  blobEndpoint: reference(concat(i, baseName_var)).primaryEndpoints.blob
  status: reference(concat(i, baseName_var)).statusOfPrimary
}]
```

---

前の例は、次の値を持つ配列を返します。

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

## <a name="next-steps"></a>次のステップ

- チュートリアルについては、「[チュートリアル:ARM テンプレートを使用した複数のリソース インスタンスの作成](template-tutorial-create-multiple-instances.md)」を参照してください。
- コピー ループのその他の使用方法については、以下を参照してください。
  - [ARM テンプレートでのリソースの反復処理](copy-resources.md)
  - [ARM テンプレートでのプロパティの反復処理](copy-properties.md)
  - [ARM テンプレートでの変数の反復処理](copy-variables.md)
- テンプレートのセクションの詳細については、「[ARM テンプレートの構造と構文について](template-syntax.md)」を参照してください。
- テンプレートをデプロイする方法の詳細については、「[ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](deploy-powershell.md)」を参照してください。
