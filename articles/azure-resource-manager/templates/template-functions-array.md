---
title: テンプレート関数 - 配列
description: Azure Resource Manager テンプレート (ARM テンプレート) で配列を操作するために使用する関数について説明します。
ms.topic: conceptual
ms.date: 09/08/2021
ms.openlocfilehash: 25fb249e1840a7538fa1dd14f3b1a930bbefdfee
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755997"
---
# <a name="array-functions-for-arm-templates"></a>ARM テンプレート用の配列関数

Resource Manager では、Azure Resource Manager テンプレート (ARM テンプレート) で配列を操作するために、次の関数が提供されています。

* [array](#array)
* [concat](#concat)
* [contains](#contains)
* [createArray](#createarray)
* [empty](#empty)
* [first](#first)
* [intersection](#intersection)
* [last](#last)
* [length](#length)
* [max](#max)
* [min](#min)
* [range](#range)
* [skip](#skip)
* [take](#take)
* [union](#union)

文字列値の配列をある値で区切られた状態にするには、「 [split](template-functions-string.md#split)」を参照してください。

## <a name="array"></a>array

`array(convertToArray)`

値を配列に変換します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| convertToArray |はい |整数、文字列、配列、オブジェクト |配列に変換する値。 |

### <a name="return-value"></a>戻り値

配列。

### <a name="example"></a>例

次の例では、array 関数をさまざまな型で使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/array.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| intOutput | Array | [1] |
| stringOutput | Array | ["efgh"] |
| objectOutput | Array | [{"a": "b", "c": "d"}] |

## <a name="concat"></a>concat

`concat(arg1, arg2, arg3, ...)`

複数の配列を結合し、連結された配列を返します。または複数の文字列値を結合し、連結文字列を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列または文字列 |連結する最初の配列または文字列。 |
| その他の引数 |いいえ |配列または文字列 |順次連結するその他の配列または文字列。 |

この関数は、任意の数の引数を取ることができ、パラメーターに文字列または配列を使用できます。 ただし、パラメーターに配列と文字列の両方を指定することはできません。 配列は、他の配列とのみ連結されます。

### <a name="return-value"></a>戻り値

連結された値の文字列または配列。

### <a name="example"></a>例

次の例では、2 つの配列を結合する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/concat-array.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| 戻り値 | Array | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

次の例は、2 つの文字列値を結合して 1 つの連結文字列を返す方法を示しています。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/concat-string.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| concatOutput | String | prefix-5yj4yjf5mbg72 |

## <a name="contains"></a>contains

`contains(container, itemToFind)`

配列に値が含まれるかどうか、オブジェクトにキーが含まれるかどうか、または文字列に部分文字列が含まれるかどうかを確認します。 文字列比較では大文字・小文字を区別します。 ただし、オブジェクトにキーが含まれているかどうかをテストする場合、比較で大文字・小文字を区別しません。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| container |はい |配列、オブジェクト、文字列 |検索対象の値を含む値。 |
| itemToFind |はい |文字列または整数 |検索対象の値。 |

### <a name="return-value"></a>戻り値

項目が見つかった場合は **True**、それ以外の場合は **False** です。

### <a name="example"></a>例

次の例では、contains をさまざまな型で使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/contains.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="createarray"></a>createArray

`createArray (arg1, arg2, arg3, ...)`

パラメーターから配列を作成します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| args |いいえ |文字列、整数、配列、オブジェクト |配列内の値。 |

### <a name="return-value"></a>戻り値

配列。 パラメーターが指定されていない場合は、空の配列を返します。

### <a name="example"></a>例

次の例では、createArray をさまざまな型で使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/createarray.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | [値] |
| ---- | ---- | ----- |
| stringArray | Array | ["a", "b", "c"] |
| intArray | Array | [1, 2, 3] |
| objectArray | Array | [{"one": "a", "two": "b", "three": "c"}] |
| arrayArray | Array | [["one", "two", "three"]] |
| emptyArray | Array | [] |

## <a name="empty"></a>empty

`empty(itemToTest)`

配列、オブジェクト、または文字列が空かどうかを判断します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| itemToTest |はい |配列、オブジェクト、文字列 |空かどうかを確認する値。 |

### <a name="return-value"></a>戻り値

値が空の場合は **True** を、値が空でない場合は **False** を返します。

### <a name="example"></a>例

次の例では、配列、オブジェクト、および文字列が空かどうかを確認します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/empty.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="first"></a>first

`first(arg1)`

配列の最初の要素、または文字列の最初の文字を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列または文字列 |最初の要素または文字を取得する値。 |

### <a name="return-value"></a>戻り値

配列の最初の要素の型 (文字列、整数、配列、またはオブジェクト)、または文字列の最初の文字。

### <a name="example"></a>例

次の例では、first 関数を配列および文字列と共に使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/first.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| arrayOutput | String | one |
| stringOutput | String | O |

## <a name="intersection"></a>intersection

`intersection(arg1, arg2, arg3, ...)`

パラメーターから共通の要素を持つ 1 つの配列またはオブジェクトを返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列またはオブジェクト |共通の要素の検索に使用する 1 番目の値。 |
| arg2 |はい |配列またはオブジェクト |共通の要素の検索に使用する 2 番目の値。 |
| その他の引数 |いいえ |配列またはオブジェクト |共通の要素の検索に使用するその他の値。 |

### <a name="return-value"></a>戻り値

共通の要素を持つ配列またはオブジェクト。

### <a name="example"></a>例

次の例では、intersection を配列およびオブジェクトと共に使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/intersection.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "three": "c"} |
| arrayOutput | Array | ["two", "three"] |

## <a name="last"></a>last

`last (arg1)`

配列の最後の要素、または文字列の最後の文字を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列または文字列 |最後の要素または文字を取得する値。 |

### <a name="return-value"></a>戻り値

配列の最後の要素の型 (文字列、整数、配列、またはオブジェクト)、または文字列の最後の文字。

### <a name="example"></a>例

次の例では、last 関数を配列および文字列と共に使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/last.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| arrayOutput | String | three |
| stringOutput | String | e |

## <a name="length"></a>length

`length(arg1)`

配列内の要素、文字列内の文字、またはオブジェクト内のルート レベル プロパティの数を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |array、string、または object |要素の数を取得するために使用する配列、文字の数を取得するために使用する文字列、またはルート レベル プロパティの数を取得するために使用するオブジェクト。 |

### <a name="return-value"></a>戻り値

整数。

### <a name="example"></a>例

次の例では、length を配列および文字列と共に使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/length.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| arrayLength | int | 3 |
| stringLength | int | 13 |
| objectLength | int | 4 |

この関数を配列と共に使用して、リソースを作成するときのイテレーション数を指定できます。 次の例では、 **siteNames** パラメーターは、Web サイトの作成時に使用する名前の配列を参照します。

```json
"copy": {
  "name": "websitescopy",
  "count": "[length(parameters('siteNames'))]"
}
```

配列でこの関数を使用する方法の詳細については、「[ARM テンプレートでのリソースの反復処理](copy-resources.md)」を参照してください。

## <a name="max"></a>max

`max(arg1)`

整数の配列または整数のコンマ区切りリストから最大値を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数の配列、または整数のコンマ区切りリスト |最大値を取得するコレクション。 |

### <a name="return-value"></a>戻り値

最大値を表す整数。

### <a name="example"></a>例

次の例では、max を配列および整数のリストと共に使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/max.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| arrayOutput | int | 5 |
| intOutput | int | 5 |

## <a name="min"></a>min

`min(arg1)`

整数の配列または整数のコンマ区切りリストから最小値を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数の配列、または整数のコンマ区切りリスト |最小値を取得するコレクション。 |

### <a name="return-value"></a>戻り値

最小値を表す整数。

### <a name="example"></a>例

次の例では、min を配列および整数のリストと共に使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/min.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| arrayOutput | int | 0 |
| intOutput | int | 0 |

## <a name="range"></a>range

`range(startIndex, count)`

始点となる整数から、指定した数の項目が含まれる配列を作成します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| startIndex |はい |INT |配列の最初の整数です。 startIndex と count の合計は、2147483647 より大きくてはいけません。 |
| count |はい |INT |配列内の整数の数。 10000 までの負でない整数にする必要があります。 |

### <a name="return-value"></a>戻り値

整数の配列。

### <a name="example"></a>例

次の例では、range 関数を使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/range.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| rangeOutput | Array | [5, 6, 7] |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

配列内の指定した数の要素の後にあるすべての要素から成る配列を返します。または、文字列内の指定した数の文字の後にあるすべての文字から成る文字列を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| originalValue |はい |配列または文字列 |スキップ対象の配列または文字列。 |
| numberToSkip |はい |INT |スキップする要素または文字の数。 この値が 0 以下である場合は、値内のすべての要素または文字が返されます。 配列または文字列の長さを超過している場合は、空の配列または文字列が返されます。 |

### <a name="return-value"></a>戻り値

配列または文字列。

### <a name="example"></a>例

次の例では、配列内の指定した数の要素と、文字列内の指定した数の文字をスキップします。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/skip.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| arrayOutput | Array | ["three"] |
| stringOutput | String | two three |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

配列または文字列を返します。 配列の先頭から指定された数の要素を含む配列。 文字列の先頭から指定された数の文字を含む文字列。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| originalValue |はい |配列または文字列 |要素の取得元となる配列または文字列。 |
| numberToTake |はい |INT |取得する要素または文字の数。 この値が 0 以下である場合、空の配列または文字列が返されます。 指定された配列または文字列の長さを超過している場合は、その配列または文字列のすべての要素が返されます。 |

### <a name="return-value"></a>戻り値

配列または文字列。

### <a name="example"></a>例

次の例では、指定した数の要素を配列から取得し、指定した数の文字を文字列から取得します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/take.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | String | on |

## <a name="union"></a>union

`union(arg1, arg2, arg3, ...)`

パラメーターからすべての要素を持つ 1 つの配列またはオブジェクトを返します。 重複する値またはキーは、1 回のみ含まれます。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列またはオブジェクト |要素の結合に使用される 1 番目の値。 |
| arg2 |はい |配列またはオブジェクト |要素の結合に使用される 2 番目の値。 |
| その他の引数 |いいえ |配列またはオブジェクト |要素の結合に使用されるその他の値。 |

### <a name="return-value"></a>戻り値

配列またはオブジェクト。

### <a name="example"></a>例

次の例では、union を配列およびオブジェクトと共に使用する方法を示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/union.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Array | ["one", "two", "three", "four"] |

## <a name="next-steps"></a>次のステップ

* ARM テンプレートのセクションの説明については、「[ARM テンプレートの構造と構文について](./syntax.md)」を参照してください。
