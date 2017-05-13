---
title: "Azure Resource Manager テンプレートの関数 - 配列とオブジェクト | Microsoft Docs"
description: "Azure Resource Manager テンプレートで、配列とオブジェクトを操作するために使用する関数について説明します。"
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
ms.openlocfilehash: 34fc513b6d4408e341fc5a723ca743daee39b85d
ms.contentlocale: ja-jp
ms.lasthandoff: 04/28/2017


---
# <a name="array-and-object-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートの配列とオブジェクトの関数 

Resource Manager には、配列とオブジェクトを操作する関数がいくつか用意されています。

* [array](#array)
* [coalesce](#coalesce)
* [concat](#concat)
* [contains](#contains)
* [createArray](#createarray)
* [empty](#empty)
* [first](#first)
* [intersection](#intersection)
* [last](#last)
* [length](#length)
* [min](#min)
* [max](#max)
* [range](#range)
* [skip](#skip)
* [take](#take)
* [union](#union)

文字列値の配列をある値で区切られた状態にするには、「 [split](resource-group-template-functions-string.md#split)」を参照してください。

<a id="array" />

## <a name="array"></a>array
`array(convertToArray)`

値を配列に変換します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| convertToArray |はい |整数、文字列、配列、オブジェクト |配列に変換する値。 |

### <a name="examples"></a>例

次の例では、array 関数をさまざまな型で使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "intToConvert": {
            "type": "int",
            "defaultValue": 1
        },
        "stringToConvert": {
            "type": "string",
            "defaultValue": "a"
        },
        "objectToConvert": {
            "type": "object",
            "defaultValue": {"a": "b", "c": "d"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "intOutput": {
            "type": "array",
            "value": "[array(parameters('intToConvert'))]"
        },
        "stringOutput": {
            "type": "array",
            "value": "[array(parameters('stringToConvert'))]"
        },
        "objectOutput": {
            "type": "array",
            "value": "[array(parameters('objectToConvert'))]"
        }
    }
}
```

### <a name="return-value"></a>戻り値

配列。

<a id="coalesce" />

## <a name="coalesce"></a>coalesce
`coalesce(arg1, arg2, arg3, ...)`

パラメーターから最初の null 以外の値を返します。 空の文字列、空の配列、空のオブジェクトは null ではありません。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数、文字列、配列、オブジェクト |null かどうかがテストされる最初の値。 |
| 残りの引数 |いいえ |整数、文字列、配列、オブジェクト |null かどうかがテストされる残りの値。 |

### <a name="examples"></a>例

次の例では、coalesce をさまざまな方法で使用したときの出力を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {"first": null, "second": null}
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[coalesce(parameters('objectToTest').first, parameters('objectToTest').second, 'fallback')]"
        },
        "intOutput": {
            "type": "int",
            "value": "[coalesce(parameters('objectToTest').first, parameters('objectToTest').second, 1)]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[coalesce(parameters('objectToTest').first, parameters('objectToTest').second, parameters('objectToTest'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[coalesce(parameters('objectToTest').first, parameters('objectToTest').second, array(1))]"
        }
    }
}
```

### <a name="return-value"></a>戻り値

最初の null 以外のパラメーターの値。文字列、整数、配列、またはオブジェクトが返されます。 すべてのパラメーターが null の場合は null になります。 

<a id="concat" />

## <a name="concat"></a>concat
`concat(arg1, arg2, arg3, ...)`

複数の配列を結合し、連結された配列を返します。または複数の文字列値を結合し、連結文字列を返します。 

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列または文字列 |連結する最初の配列または文字列。 |
| 残りの引数 |いいえ |配列または文字列 |順次連結する残りの配列または文字列。 |

この関数は、任意の数の引数を取ることができ、パラメーターに文字列または配列を使用できます。

### <a name="examples"></a>例

次の例では、2 つの配列を結合する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "firstArray": { 
            "type": "array", 
            "defaultValue": [ 
                "1-1", 
                "1-2", 
                "1-3" 
            ] 
        },
        "secondArray": {
            "type": "array", 
            "defaultValue": [ 
                "2-1", 
                "2-2",
                "2-3" 
            ] 
        }
    },
    "resources": [
    ],
    "outputs": {
        "return": {
            "type": "array",
            "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

次の例は、2 つの文字列値を結合して 1 つの連結文字列を返す方法を示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "prefix": {
            "type": "string",
            "defaultValue": "prefix"
        }
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('prefix'), uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

### <a name="return-value"></a>戻り値
連結された値の文字列または配列。

<a id="contains" />

## <a name="contains"></a>contains
`contains(container, itemToFind)`

配列に値が含まれるかどうか、オブジェクトにキーが含まれるかどうか、または文字列に部分文字列が含まれるかどうかを確認します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| container |はい |配列、オブジェクト、文字列 |検索対象の値を含む値。 |
| itemToFind |はい |文字列または整数 |検索対象の値。 |

### <a name="examples"></a>例

次の例では、contains をさまざまな型で使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "OneTwoThree"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringTrue": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'e')]"
        },
        "stringFalse": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'z')]"
        },
        "objectTrue": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'one')]"
        },
        "objectFalse": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'a')]"
        },
        "arrayTrue": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'three')]"
        },
        "arrayFalse": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'four')]"
        }
    }
}
```

### <a name="return-value"></a>戻り値

項目が見つかった場合は **True**、それ以外の場合は **False** です。

<a id="createarray" />

## <a name="createarray"></a>createarray
`createArray (arg1, arg2, arg3, ...)`

パラメーターから配列を作成します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |はい |文字列、整数、配列、オブジェクト |配列の最初の値。 |
| 残りの引数 |なし |文字列、整数、配列、オブジェクト |配列の残りの値。 |

### <a name="examples"></a>例

次の例では、createArray をさまざまな型で使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringArray": {
            "type": "array",
            "value": "[createArray('a', 'b', 'c')]"
        },
        "intArray": {
            "type": "array",
            "value": "[createArray(1, 2, 3)]"
        },
        "objectArray": {
            "type": "array",
            "value": "[createArray(parameters('objectToTest'))]"
        },
        "arrayArray": {
            "type": "array",
            "value": "[createArray(parameters('arrayToTest'))]"
        }
    }
}
```

### <a name="return-value"></a>戻り値

配列。

<a id="empty" />

## <a name="empty"></a>empty

`empty(itemToTest)`

配列、オブジェクト、または文字列が空かどうかを判断します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| itemToTest |あり |配列、オブジェクト、文字列 |空かどうかを確認する値。 |

### <a name="examples"></a>例

次の例では、配列、オブジェクト、および文字列が空かどうかを確認します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": []
        },
        "testObject": {
            "type": "object",
            "defaultValue": {}
        },
        "testString": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testArray'))]"
        },
        "objectEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testObject'))]"
        },
        "stringEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testString'))]"
        }
    }
}
```

### <a name="return-value"></a>戻り値

値が空の場合は **True** を、値が空でない場合は **False** を返します。

<a id="first" />

## <a name="first"></a>first
`first(arg1)`

配列の最初の要素、または文字列の最初の文字を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列または文字列 |最初の要素または文字を取得する値。 |

### <a name="examples"></a>例

次の例では、first 関数を配列および文字列と共に使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[first(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[first('One Two Three')]"
        }
    }
}
```

### <a name="return-value"></a>戻り値

配列の最初の要素の型 (文字列、整数、配列、オブジェクト)、または文字列の最初の文字。

<a id="intersection" />

## <a name="intersection"></a>共通部分
`intersection(arg1, arg2, arg3, ...)`

パラメーターから共通の要素を持つ 1 つの配列またはオブジェクトを返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列またはオブジェクト |共通の要素の検索に使用する 1 番目の値。 |
| arg2 |はい |配列またはオブジェクト |共通の要素の検索に使用する 2 番目の値。 |
| 残りの引数 |なし |配列またはオブジェクト |共通の要素の検索に使用する残りの値。 |

### <a name="examples"></a>例

次の例では、intersection を配列およびオブジェクトと共に使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "z", "three": "c"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[intersection(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[intersection(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

### <a name="return-value"></a>戻り値

共通の要素を持つ配列またはオブジェクト。

<a id="last" />

## <a name="last"></a>last
`last (arg1)`

配列の最後の要素、または文字列の最後の文字を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列または文字列 |最後の要素または文字を取得する値。 |

### <a name="examples"></a>例

次の例では、last 関数を配列および文字列と共に使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[last(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[last('One Two Three')]"
        }
    }
}
```

### <a name="return-value"></a>戻り値

配列の最後の要素の型 (文字列、整数、配列、オブジェクト)、または文字列の最後の文字。

<a id="length" />

## <a name="length"></a>length
`length(arg1)`

配列内の要素の数、または文字列内の文字の数を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |あり |配列または文字列 |要素の数を取得するために使用する配列、または文字の数を取得するために使用する文字列。 |

### <a name="examples"></a>例

次の例では、length を配列および文字列と共に使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "stringToTest": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "arrayLength": {
            "type": "int",
            "value": "[length(parameters('arrayToTest'))]"
        },
        "stringLength": {
            "type": "int",
            "value": "[length(parameters('stringToTest'))]"
        }
    }
}
```

この関数を配列と共に使用して、リソースを作成するときのイテレーション数を指定できます。 次の例では、 **siteNames** パラメーターは、Web サイトの作成時に使用する名前の配列を参照します。

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

この関数を配列と共に使用する方法の詳細については、「 [Azure リソース マネージャーでリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」をご覧ください。

### <a name="return-value"></a>戻り値

整数。 

<a id="min" />

## <a name="min"></a>Min
`min(arg1)`

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

最小値を表す整数。

<a id="max" />

## <a name="max"></a>max
`max(arg1)`

整数の配列または整数のコンマ区切りリストから最大値を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数の配列、または整数のコンマ区切りリスト |最大値を取得するコレクション。 |

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

最大値を表す整数。

<a id="range" />

## <a name="range"></a>range
`range(startingInteger, numberOfElements)`

始点となる整数から、指定した数の項目が含まれる配列を作成します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| startingInteger |はい |int |配列の最初の整数です。 |
| numberofElements |はい |int |配列内の整数の数。 |

### <a name="examples"></a>例

次の例では、range 関数を使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "startingInt": {
            "type": "int",
            "defaultValue": 5
        },
        "numberOfElements": {
            "type": "int",
            "defaultValue": 3
        }
    },
    "resources": [],
    "outputs": {
        "rangeOutput": {
            "type": "array",
            "value": "[range(parameters('startingInt'),parameters('numberOfElements'))]"
        }
    }
}
```

### <a name="return-value"></a>戻り値

整数の配列。

<a id="skip" />

## <a name="skip"></a>skip
`skip(originalValue, numberToSkip)`

配列内の指定した数の要素の後にあるすべての要素から成る配列を返します。または、文字列内の指定した数の文字の後にあるすべての文字から成る文字列を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| originalValue |はい |配列または文字列 |スキップ対象の配列または文字列。 |
| numberToSkip |はい |int |スキップする要素または文字の数。 この値が 0 以下である場合は、値内のすべての要素または文字が返されます。 配列または文字列の長さを超える場合は、空の配列または文字列が返されます。 |

### <a name="examples"></a>例

次の例では、配列内の指定した数の要素と、文字列内の指定した数の文字をスキップします。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToSkip": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToSkip": {
            "type": "int",
            "defaultValue": 4
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
        }
    }
}
```

### <a name="return-value"></a>戻り値

配列または文字列。

<a id="take" />

## <a name="take"></a>take
`take(originalValue, numberToTake)`

配列の先頭から指定した数の要素を含む配列、または文字列の先頭から指定した数の文字を含む文字列を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| originalValue |はい |配列または文字列 |要素の取得元となる配列または文字列。 |
| numberToTake |あり |int |取得する要素または文字の数。 この値が 0 以下である場合、空の配列または文字列が返されます。 指定された配列または文字列の長さを超える場合は、その配列または文字列のすべての要素が返されます。 |

### <a name="examples"></a>例

次の例では、指定した数の要素を配列から取得し、指定した数の文字を文字列から取得します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToTake": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToTake": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
        }
    }
}
```

### <a name="return-value"></a>戻り値

配列または文字列。

<a id="union" />

## <a name="union"></a>union
`union(arg1, arg2, arg3, ...)`

パラメーターからすべての要素を持つ 1 つの配列またはオブジェクトを返します。 重複する値またはキーは、1 回のみ含まれます。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列またはオブジェクト |要素の結合に使用される 1 番目の値。 |
| arg2 |あり |配列またはオブジェクト |要素の結合に使用される 2 番目の値。 |
| 残りの引数 |いいえ |配列またはオブジェクト |要素の結合に使用される残りの値。 |

### <a name="examples"></a>例

次の例では、union を配列およびオブジェクトと共に使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"four": "d", "five": "e", "six": "f"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["four", "five"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[union(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[union(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

### <a name="return-value"></a>戻り値

配列またはオブジェクト。

## <a name="next-steps"></a>次のステップ
* Azure Resource Manager テンプレートのセクションの説明については、[Azure Resource Manager テンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。
* 複数のテンプレートをマージするには、[Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)に関するページを参照してください。
* 1 種類のリソースを指定した回数分繰り返し作成するには、「 [Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」を参照してください。
* 作成したテンプレートをデプロイする方法を確認するには、[Azure Resource Manager テンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)に関するページを参照してください。


