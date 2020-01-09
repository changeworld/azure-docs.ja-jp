---
title: テンプレートでのユーザー定義関数
description: Azure Resource Manager テンプレートでユーザー定義関数を定義して使用する方法について説明します。
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: e2e68c6f6b9fc9eb40d282d87572ce1e805a11a8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75476161"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Azure Resource Manager テンプレートのユーザー定義関数

テンプレート内で、独自の関数を作成できます。 これらの関数は、テンプレートで使用可能です。 ユーザー定義関数は、テンプレート内で自動的に使用可能になる[標準テンプレート関数](template-functions.md)とは別のものです。 テンプレートで繰り返し使用される複雑な式がある場合は、独自の関数を作成します。

この記事では、Azure Resource Manager テンプレートにユーザー定義関数を追加する方法について説明します。

## <a name="define-the-function"></a>関数を定義する

テンプレート関数との名前の競合を回避するために、お使いの関数には名前空間の値が必要です。 次の例は、ストレージ アカウント名を返す関数を示しています。

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>関数を使用する

次の例は、自分で定義した関数を呼び出す方法を示しています。

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="limitations"></a>制限事項

ユーザー関数を定義するときに、適用される制限がいくつかあります。

* 関数は変数にアクセスできません。
* 関数は、関数内で定義されているパラメーターのみを使用できます。 ユーザー定義関数内で [parameters](template-functions-deployment.md#parameters) 関数を使用する場合は、その関数のパラメーターに制限されます。
* 関数は、その他のユーザー定義関数を呼び出すことはできません。
* 関数では、[reference](template-functions-resource.md#reference) 関数、またはいずれの [list](template-functions-resource.md#list) 関数も使用できません。
* 関数のパラメーターでは既定値を指定できません。


## <a name="next-steps"></a>次のステップ

* ユーザー定義関数で使用できるプロパティの詳細については、「[Azure Resource Manager テンプレートの構造と構文の詳細](template-syntax.md)」をご覧ください。
* 使用可能なテンプレート関数の一覧については、「[Azure Resource Manager テンプレートの関数](template-functions.md)」をご覧ください。
