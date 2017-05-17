---
title: "Azure Resource Manager テンプレートの関数 - 数値 | Microsoft Docs"
description: "Azure Resource Manager テンプレートで、数値を操作するために使用する関数について説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: 5844540801a6f0ff593b3f404f6815473c65a52e
ms.contentlocale: ja-jp
ms.lasthandoff: 04/28/2017


---
# <a name="numeric-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートの数値関数

リソース マネージャーには、整数を操作する次の関数が用意されています。

* [add](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [min](#min)
* [max](#max)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

<a id="add" />

## <a name="add"></a>追加
`add(operand1, operand2)`

指定された 2 つ整数の合計を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- | 
|operand1 |はい |int |加算する最初の整数。 |
|operand2 |はい |int |加算する 2 つ目の整数。 |

### <a name="examples"></a>例

次の例では、2 つのパラメーターを加算します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Second integer to add"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "addResult": {
            "type": "int",
            "value": "[add(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>戻り値

パラメーターの合計を格納する整数。

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(offset)`

反復処理のループのインデックスを返します。 

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| offset |いいえ |int |0 から始まる反復値に追加する整数。 |

### <a name="remarks"></a>解説

この関数は常に **copy** オブジェクトと共に使用されます。 **offset** の値が指定されていない場合、現在の反復値が返されます。 反復値は 0 から始まります。 **copyIndex**の使用方法の詳細については、「 [Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」を参照してください。

### <a name="examples"></a>例

次の例では、コピー ループと、名前に含まれるインデックス値を示します。 

```json
"resources": [ 
  { 
    "name": "[concat('examplecopy-', copyIndex())]", 
    "type": "Microsoft.Web/sites", 
    "copy": { 
      "name": "websitescopy", 
      "count": "[parameters('count')]" 
    }, 
    ...
  }
]
```

### <a name="return-value"></a>戻り値

反復値の現在のインデックスを表す整数。

<a id="div" />

## <a name="div"></a>div
`div(operand1, operand2)`

指定された 2 つの整数の整数除算を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| operand1 |はい |int |除算される整数。 |
| operand2 |はい |int |除算に使用される整数。 0 にすることはできません。 |

### <a name="examples"></a>例

次の例では、一方のパラメーターをもう一方のパラメーターで除算します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[div(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>戻り値

除算を表す整数。

<a id="float" />

## <a name="float"></a>float
`float(arg1)`

値を浮動小数点数に変換します。 この関数は、ロジック アプリなどのアプリケーションにカスタム パラメーターを渡す場合にのみ使用します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |あり |文字列または整数 |浮動小数点数に変換する値。 |

### <a name="examples"></a>例

次の例では、ロジック アプリにパラメーターを渡すために浮動小数点数を使用する方法を示します。

```json
{
    "type": "Microsoft.Logic/workflows",
    "properties": {
        ...
        "parameters": {
        "custom1": {
            "value": "[float('3.0')]"
        },
        "custom2": {
            "value": "[float(3)]"
        },
```

### <a name="return-value"></a>戻り値
浮動小数点数。

<a id="int" />

## <a name="int"></a>int
`int(valueToConvert)`

指定された値を整数に変換します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| valueToConvert |はい |文字列または整数 |整数に変換する値。 |

### <a name="examples"></a>例

次の例では、ユーザー指定のパラメーター値を整数に変換します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[variables('intValue')]"
        }
    }
}
```

### <a name="return-value"></a>戻り値

整数。

<a id="min" />

## <a name="min"></a>Min
`min (arg1)`

整数の配列または整数のコンマ区切りリストから最小値を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |あり |整数の配列、または整数のコンマ区切りリスト |最小値を取得するコレクション。 |

### <a name="examples"></a>例

次の例では、min を配列および整数のリストと共に使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[min(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[min(0,3,2,5,4)]"
        }
    }
}
```

### <a name="return-value"></a>戻り値

コレクションの最小値を表す整数。

<a id="max" />

## <a name="max"></a>max
`max (arg1)`

整数の配列または整数のコンマ区切りリストから最大値を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |あり |整数の配列、または整数のコンマ区切りリスト |最大値を取得するコレクション。 |

### <a name="examples"></a>例

次の例では、max を配列および整数のリストと共に使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[max(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[max(0,3,2,5,4)]"
        }
    }
}
```

### <a name="return-value"></a>戻り値

コレクションの最大値を表す整数。

<a id="mod" />

## <a name="mod"></a>mod
`mod(operand1, operand2)`

指定された 2 つの整数を使用した整数除算の剰余を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| operand1 |あり |int |除算される整数。 |
| operand2 |あり |int |除算に使用される整数。0 にすることはできません。 |

### <a name="examples"></a>例

次の例では、一方のパラメーターをもう一方のパラメーターで除算した剰余を返します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "modResult": {
            "type": "int",
            "value": "[mod(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>戻り値
剰余を表す整数。

<a id="mul" />

## <a name="mul"></a>mul
`mul(operand1, operand2)`

指定された 2 つの整数の乗算を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| operand1 |あり |int |乗算する最初の整数。 |
| operand2 |はい |int |乗算する 2 つ目の整数。 |

### <a name="examples"></a>例

次の例では、一方のパラメーターをもう一方のパラメーターで乗算します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Second integer to multiply"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "mulResult": {
            "type": "int",
            "value": "[mul(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>戻り値

乗算を表す整数。

<a id="sub" />

## <a name="sub"></a>sub
`sub(operand1, operand2)`

指定された 2 つの整数の減算を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| operand1 |はい |int |減算される整数。 |
| operand2 |はい |int |減算する整数。 |

### <a name="examples"></a>例

次の例では、一方のパラメーターからもう一方のパラメーターを減算します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Integer to subtract"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "subResult": {
            "type": "int",
            "value": "[sub(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>戻り値
減算を表す整数。

## <a name="next-steps"></a>次のステップ
* Azure Resource Manager テンプレートのセクションの説明については、[Azure Resource Manager テンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。
* 複数のテンプレートをマージするには、[Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)に関するページを参照してください。
* 1 種類のリソースを指定した回数分繰り返し作成するには、「 [Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」を参照してください。
* 作成したテンプレートをデプロイする方法を確認するには、[Azure Resource Manager テンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)に関するページを参照してください。


