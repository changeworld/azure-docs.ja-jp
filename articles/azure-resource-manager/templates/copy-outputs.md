---
title: 出力値の複数のインスタンスを定義する
description: デプロイから値を返すときに、Azure Resource Manager テンプレートで copy 操作を使用して、複数回、反復処理を行います。
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: db5c548c7bd4c60357d3656b1273b0192c497459
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624514"
---
# <a name="output-iteration-in-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートでの出力の反復

この記事では、Azure Resource Manager テンプレートで 1 つの出力に対して複数の値を作成する方法について説明します。 テンプレートの outputs セクションに **copy** 要素を追加することにより、デプロイ時に多数の項目を動的に返すことができます。

[resources](copy-resources.md)、[resource 内の properties](copy-properties.md)、および[variables](copy-variables.md) でも、copy を使用できます。

## <a name="outputs-iteration"></a>出力の反復

この copy 要素には、次の一般的な形式があります。

```json
"copy": [
  {
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

**count** プロパティには、出力値で必要な反復回数を指定します。

**input** プロパティは、繰り返すプロパティを指定します。 **input** プロパティの値から構築される要素の配列を作成します。 それは、1 つのプロパティ (文字列など) にすることも、複数のプロパティを持つオブジェクトにすることもできます。

次の例では、可変数のストレージ アカウントが作成され、各ストレージ アカウントのエンドポイントが返されます。

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

前のテンプレートは、次の値を持つ配列を返します。

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

次の例では、新しいストレージ アカウントから 3 つのプロパティが返されます。

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

* チュートリアルについては、「[チュートリアル: Resource Manager テンプレートを使用した複数のリソース インスタンスの作成](template-tutorial-create-multiple-instances.md)」を参照してください。
* copy 要素のその他の使用方法については、以下を参照してください。
  * [Azure Resource Manager テンプレートでのリソースの反復](copy-resources.md)
  * [Azure Resource Manager テンプレートでのプロパティの反復](copy-properties.md)
  * [Azure Resource Manager テンプレートでの変数の反復](copy-variables.md)
* テンプレートのセクションについては、「[Azure Resource Manager のテンプレートの作成](template-syntax.md)」を参照してください。
* テンプレートをデプロイする方法については、「 [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](deploy-powershell.md)」を参照してください。

