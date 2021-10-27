---
title: Bicep 関数 - 配列
description: 配列で作業する際に Bicep ファイルで使用する関数について説明します。
author: mumian
ms.topic: conceptual
ms.author: jgao
ms.date: 10/18/2021
ms.openlocfilehash: f0f9132818a8708ad5ef2a3205eb2f3a497006d6
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130160815"
---
# <a name="array-functions-for-bicep"></a>Bicep の配列関数

この記事では、配列を操作するための Bicep 関数について説明します。

## <a name="array"></a>array

`array(convertToArray)`

値を配列に変換します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| convertToArray |はい |整数、文字列、配列、オブジェクト |配列に変換する値。 |

### <a name="return-value"></a>戻り値

配列。

### <a name="example"></a>例

次の例では、array 関数をさまざまな型で使用する方法を示します。

```bicep
param intToConvert int = 1
param stringToConvert string = 'efgh'
param objectToConvert object = {
  'a': 'b'
  'c': 'd'
}

output intOutput array = array(intToConvert)
output stringOutput array = array(stringToConvert)
output objectOutput array = array(objectToConvert)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| intOutput | Array | [1] |
| stringOutput | Array | ["efgh"] |
| objectOutput | Array | [{"a": "b", "c": "d"}] |

## <a name="concat"></a>concat

`concat(arg1, arg2, arg3, ...)`

複数の配列を結合し、連結された配列を返します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |array |連結の最初の配列。 |
| その他の引数 |いいえ |array |連結する順のその他の配列。 |

この関数は、任意の数の配列を受け取り、それらを結合します。

### <a name="return-value"></a>戻り値

連結された値の配列。

### <a name="example"></a>例

次の例では、2 つの配列を結合する方法を示します。

```bicep
param firstArray array = [
  '1-1'
  '1-2'
  '1-3'
]
param secondArray array = [
  '2-1'
  '2-2'
  '2-3'
]

output return array = concat(firstArray, secondArray)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| 戻り値 | Array | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>contains

`contains(container, itemToFind)`

配列に値が含まれるかどうか、オブジェクトにキーが含まれるかどうか、または文字列に部分文字列が含まれるかどうかを確認します。 文字列比較では大文字・小文字を区別します。 ただし、オブジェクトにキーが含まれているかどうかをテストする場合、比較で大文字・小文字を区別しません。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| container |はい |配列、オブジェクト、文字列 |検索対象の値を含む値。 |
| itemToFind |はい |文字列または整数 |検索対象の値。 |

### <a name="return-value"></a>戻り値

項目が見つかった場合は **True**、それ以外の場合は **False** です。

### <a name="example"></a>例

次の例では、contains をさまざまな型で使用する方法を示します。

```bicep
param stringToTest string = 'OneTwoThree'
param objectToTest object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output stringTrue bool = contains(stringToTest, 'e')
output stringFalse bool = contains(stringToTest, 'z')
output objectTrue bool = contains(objectToTest, 'one')
output objectFalse bool = contains(objectToTest, 'a')
output arrayTrue bool = contains(arrayToTest, 'three')
output arrayFalse bool = contains(arrayToTest, 'four')
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="empty"></a>empty

`empty(itemToTest)`

配列、オブジェクト、または文字列が空かどうかを判断します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| itemToTest |はい |配列、オブジェクト、文字列 |空かどうかを確認する値。 |

### <a name="return-value"></a>戻り値

値が空の場合は **True** を、値が空でない場合は **False** を返します。

### <a name="example"></a>例

次の例では、配列、オブジェクト、および文字列が空かどうかを確認します。

```bicep
param testArray array = []
param testObject object = {}
param testString string = ''

output arrayEmpty bool = empty(testArray)
output objectEmpty bool = empty(testObject)
output stringEmpty bool = empty(testString)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="first"></a>first

`first(arg1)`

配列の最初の要素、または文字列の最初の文字を返します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列または文字列 |最初の要素または文字を取得する値。 |

### <a name="return-value"></a>戻り値

配列の最初の要素の型 (文字列、整数、配列、またはオブジェクト)、または文字列の最初の文字。

### <a name="example"></a>例

次の例では、first 関数を配列および文字列と共に使用する方法を示します。

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = first(arrayToTest)
output stringOutput string = first('One Two Three')
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| arrayOutput | String | one |
| stringOutput | String | O |

## <a name="intersection"></a>intersection

`intersection(arg1, arg2, arg3, ...)`

パラメーターから共通の要素を持つ 1 つの配列またはオブジェクトを返します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列またはオブジェクト |共通の要素の検索に使用する 1 番目の値。 |
| arg2 |はい |配列またはオブジェクト |共通の要素の検索に使用する 2 番目の値。 |
| その他の引数 |いいえ |配列またはオブジェクト |共通の要素の検索に使用するその他の値。 |

### <a name="return-value"></a>戻り値

共通の要素を持つ配列またはオブジェクト。 要素の順序は、最初の配列パラメーターによって決まります。

### <a name="example"></a>例

次の例では、intersection を配列およびオブジェクトと共に使用する方法を示します。

```bicep
param firstObject object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}

param secondObject object = {
  'one': 'a'
  'two': 'z'
  'three': 'c'
}

param firstArray array = [
  'one'
  'two'
  'three'
]

param secondArray array = [
  'two'
  'three'
]

output objectOutput object = intersection(firstObject, secondObject)
output arrayOutput array = intersection(firstArray, secondArray)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "three": "c"} |
| arrayOutput | Array | ["two", "three"] |

最初の配列パラメーターによって、交差する要素の順序が決まります。 次の例は、最初の配列に基づいて、返される要素の順序がどのようになるかを示しています。

```bicep
var array1 = [
  1
  2
  3
  4
]

var array2 = [
  3
  2
  1
]

var array3 = [
  4
  1
  3
  2
]

output commonUp array = intersection(array1, array2, array3)
output commonDown array = intersection(array2, array3, array1)
```

前の例からの出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| commonUp | array | [1, 2, 3] |
| commonDown | array | [3, 2, 1] |

## <a name="items"></a>items

`items(object)`

ディクショナリ オブジェクトを配列に変換します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| object |はい |object |配列に変換するディクショナリ オブジェクト。 |

### <a name="return-value"></a>戻り値

変換されたディクショナリのオブジェクトの配列。 配列内の各オブジェクトには、ディクショナリのキー値を含む `key` プロパティがあります。 各オブジェクトには、オブジェクトのプロパティを含む `value` プロパティもあります。

### <a name="example"></a>例

次の例では、ディクショナリ オブジェクトが配列に変換されます。 配列内のオブジェクトごとに、変更された値を使用して新しいオブジェクトが作成されます。

```bicep
var entities = {
  item001: {
    enabled: true
    displayName: 'Example item 1'
    number: 300
  }
  item002: {
    enabled: false
    displayName: 'Example item 2'
    number: 200
  }
}

var modifiedListOfEntities = [for entity in items(entities): {
  key: entity.key
  fullName: entity.value.displayName
  itemEnabled: entity.value.enabled
}]

output modifiedResult array = modifiedListOfEntities
```

上記の例では、以下が返されます。

```json
"modifiedResult": {
  "type": "Array",
  "value": [
    {
      "fullName": "Example item 1",
      "itemEnabled": true,
      "key": "item001"
    },
    {
      "fullName": "Example item 2",
      "itemEnabled": false,
      "key": "item002"
    }
  ]
}
```

次の例は、items 関数から返される配列を示しています。

```bicep
var entities = {
  item001: {
    enabled: true
    displayName: 'Example item 1'
    number: 300
  }
  item002: {
    enabled: false
    displayName: 'Example item 2'
    number: 200
  }
}

var entitiesArray = items(entities)

output itemsResult array = entitiesArray
```

この例では、以下が返されます。

```json
"itemsResult": {
  "type": "Array",
  "value": [
    {
      "key": "item001",
      "value": {
        "displayName": "Example item 1",
        "enabled": true,
        "number": 300
      }
    },
    {
      "key": "item002",
      "value": {
        "displayName": "Example item 2",
        "enabled": false,
        "number": 200
      }
    }
  ]
}
```

## <a name="last"></a>last

`last(arg1)`

配列の最後の要素、または文字列の最後の文字を返します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列または文字列 |最後の要素または文字を取得する値。 |

### <a name="return-value"></a>戻り値

配列の最後の要素の型 (文字列、整数、配列、またはオブジェクト)、または文字列の最後の文字。

### <a name="example"></a>例

次の例では、last 関数を配列および文字列と共に使用する方法を示します。

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = last(arrayToTest)
output stringOutput string = last('One Two three')
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| arrayOutput | String | three |
| stringOutput | String | e |

## <a name="length"></a>length

`length(arg1)`

配列内の要素、文字列内の文字、またはオブジェクト内のルート レベル プロパティの数を返します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |array、string、または object |要素の数を取得するために使用する配列、文字の数を取得するために使用する文字列、またはルート レベル プロパティの数を取得するために使用するオブジェクト。 |

### <a name="return-value"></a>戻り値

整数。

### <a name="example"></a>例

次の例では、length を配列および文字列と共に使用する方法を示します。

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]
param stringToTest string = 'One Two Three'
param objectToTest object = {
  'propA': 'one'
  'propB': 'two'
  'propC': 'three'
  'propD': {
    'propD-1': 'sub'
    'propD-2': 'sub'
  }
}

output arrayLength int = length(arrayToTest)
output stringLength int = length(stringToTest)
output objectLength int = length(objectToTest)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| arrayLength | int | 3 |
| stringLength | int | 13 |
| objectLength | int | 4 |

## <a name="max"></a>max

`max(arg1)`

整数の配列または整数のコンマ区切りリストから最大値を返します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数の配列、または整数のコンマ区切りリスト |最大値を取得するコレクション。 |

### <a name="return-value"></a>戻り値

最大値を表す整数。

### <a name="example"></a>例

次の例では、max を配列および整数のリストと共に使用する方法を示します。

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutput int = max(arrayToTest)
output intOutput int = max(0,3,2,5,4)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| arrayOutput | int | 5 |
| intOutput | int | 5 |

## <a name="min"></a>min

`min(arg1)`

整数の配列または整数のコンマ区切りリストから最小値を返します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数の配列、または整数のコンマ区切りリスト |最小値を取得するコレクション。 |

### <a name="return-value"></a>戻り値

最小値を表す整数。

### <a name="example"></a>例

次の例では、min を配列および整数のリストと共に使用する方法を示します。

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutput int = min(arrayToTest)
output intOutput int = min(0,3,2,5,4)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| arrayOutput | int | 0 |
| intOutput | int | 0 |

## <a name="range"></a>range

`range(startIndex, count)`

始点となる整数から、指定した数の項目が含まれる配列を作成します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| startIndex |はい |INT |配列の最初の整数です。 startIndex と count の合計は、2147483647 より大きくてはいけません。 |
| count |はい |INT |配列内の整数の数。 10000 までの負でない整数にする必要があります。 |

### <a name="return-value"></a>戻り値

整数の配列。

### <a name="example"></a>例

次の例では、range 関数を使用する方法を示します。

```bicep
param startingInt int = 5
param numberOfElements int = 3

output rangeOutput array = range(startingInt, numberOfElements)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| rangeOutput | Array | [5, 6, 7] |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

配列内の指定した数の要素の後にあるすべての要素から成る配列を返します。または、文字列内の指定した数の文字の後にあるすべての文字から成る文字列を返します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| originalValue |はい |配列または文字列 |スキップ対象の配列または文字列。 |
| numberToSkip |はい |INT |スキップする要素または文字の数。 この値が 0 以下である場合は、値内のすべての要素または文字が返されます。 配列または文字列の長さを超過している場合は、空の配列または文字列が返されます。 |

### <a name="return-value"></a>戻り値

配列または文字列。

### <a name="example"></a>例

次の例では、配列内の指定した数の要素と、文字列内の指定した数の文字をスキップします。

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToSkip int = 2
param testString string = 'one two three'
param charactersToSkip int = 4

output arrayOutput array = skip(testArray, elementsToSkip)
output stringOutput string = skip(testString, charactersToSkip)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| arrayOutput | Array | ["three"] |
| stringOutput | String | two three |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

配列の先頭から指定した数の要素を含む配列、または文字列の先頭から指定した数の文字を含む文字列を返します。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| originalValue |はい |配列または文字列 |要素の取得元となる配列または文字列。 |
| numberToTake |はい |INT |取得する要素または文字の数。 この値が 0 以下である場合、空の配列または文字列が返されます。 指定された配列または文字列の長さを超過している場合は、その配列または文字列のすべての要素が返されます。 |

### <a name="return-value"></a>戻り値

配列または文字列。

### <a name="example"></a>例

次の例では、指定した数の要素を配列から取得し、指定した数の文字を文字列から取得します。

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToTake int = 2
param testString string = 'one two three'
param charactersToTake int = 2

output arrayOutput array = take(testArray, elementsToTake)
output stringOutput string = take(testString, charactersToTake)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | String | on |

## <a name="union"></a>union

`union(arg1, arg2, arg3, ...)`

パラメーターからすべての要素を持つ 1 つの配列またはオブジェクトを返します。 重複する値またはキーは、1 回のみ含まれます。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列またはオブジェクト |要素の結合に使用される 1 番目の値。 |
| arg2 |はい |配列またはオブジェクト |要素の結合に使用される 2 番目の値。 |
| その他の引数 |いいえ |配列またはオブジェクト |要素の結合に使用されるその他の値。 |

### <a name="return-value"></a>戻り値

配列またはオブジェクト。

### <a name="example"></a>例

次の例では、union を配列およびオブジェクトと共に使用する方法を示します。

```bicep
param firstObject object = {
  'one': 'a'
  'two': 'b'
  'three': 'c1'
}

param secondObject object = {
  'three': 'c2'
  'four': 'd'
  'five': 'e'
}

param firstArray array = [
  'one'
  'two'
  'three'
]

param secondArray array = [
  'three'
  'four'
]

output objectOutput object = union(firstObject, secondObject)
output arrayOutput array = union(firstArray, secondArray)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Array | ["one", "two", "three", "four"] |

## <a name="next-steps"></a>次のステップ

* 文字列値の配列をある値で区切られた状態にするには、「 [split](./bicep-functions-string.md#split)」を参照してください。
