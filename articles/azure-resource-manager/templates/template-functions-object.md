---
title: テンプレート関数 - オブジェクト
description: Azure Resource Manager テンプレート (ARM テンプレート) でオブジェクトを操作するために使用する関数について説明します。
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 3e6069744b1879e97ef3977916acbfc8aa6a9bd2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820444"
---
# <a name="object-functions-for-arm-templates"></a>ARM テンプレート用のオブジェクト関数

Resource Manager には、Azure Resource Manager テンプレート (ARM テンプレート) でオブジェクトを操作する関数が複数用意されています。

* [contains](#contains)
* [createObject](#createobject)
* [empty](#empty)
* [intersection](#intersection)
* [json](#json)
* [length](#length)
* [null](#null)
* [union](#union)

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

## <a name="createobject"></a>createObject

`createObject(key1, value1, key2, value2, ...)`

キーと値からオブジェクトを作成します。

`createObject` 関数は、Bicep ではサポートされていません。  `{}` を使用してオブジェクトを構築します。 「[オブジェクト](../bicep/data-types.md#objects)」を参照してください。

### <a name="parameters"></a>パラメーター

<<<<<<< HEAD
| パラメーター | 必須 | Type | 説明 |
=======
| パラメーター | 必須 | 種類 | [説明] |
>>>>>>> repo_sync_working_branch
|:--- |:--- |:--- |:--- |
| key1 |いいえ |string |キーの名前です。 |
| value1 |いいえ |int、boolean、string、object、または array |キーの値。 |
| その他のキー |いいえ |string |キーのその他の名前。 |
| その他の値 |いいえ |int、boolean、string、object、または array |キーのその他の値。 |

この関数では、偶数個のパラメーターを指定する必要があります。 各キーには、一致する値が必要です。

### <a name="return-value"></a>戻り値

各キーと値のペアを持つオブジェクト。

### <a name="example"></a>例

次の例では、さまざまな種類の値からオブジェクトが作成されます。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/object/createobject.json":::

既定値を使用した場合の前の例の出力は、次の値を持っている `newObject` という名前のオブジェクトです。

```json
{
  "intProp": 1,
  "stringProp": "abc",
  "boolProp": true,
  "arrayProp": ["a", "b", "c"],
  "objectProp": {"key1": "value1"}
}
```

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

<a id="json"></a>

## <a name="json"></a>json

`json(arg1)`

有効な JSON 文字列を JSON データ型に変換します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |string |JSON に変換する値。 文字列は、適切に書式設定された JSON 文字列である必要があります。 |

### <a name="return-value"></a>戻り値

指定された文字列の JSON データ型。**null** が指定された場合は空のオブジェクト。

### <a name="remarks"></a>解説

JSON オブジェクトにパラメーター値または変数を含める必要がある場合、 [concat](template-functions-string.md#concat)関数を使用し、関数に渡す文字列を作成します。

[null()](#null) を使用して null 値を取得することもできます。

### <a name="example"></a>例

次の例は、`json` 関数の使用法を示しています。 空のオブジェクトに `null` を渡すことができることに注意してください。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/object/json.json":::

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| emptyObjectOutput | Boolean | True |
| objectOutput | Object | {"a": "b"} |
| stringOutput | String | テスト |
| booleanOutput | Boolean | True |
| intOutput | Integer | 3 |
| arrayOutput | Array | [ 1, 2, 3 ] |
| concatObjectOutput | Object | { "a": "demo value" } |

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

## <a name="null"></a>null

`null()`

null を返します。

`null` 関数は、Bicep では使用できません。 代わりに、`null` キーワードを使用します。

### <a name="parameters"></a>パラメーター

null 関数では、パラメーターは受け入れられません。

### <a name="return-value"></a>戻り値

常に null である値。

### <a name="example"></a>例

次の例では、null 関数を使用しています。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/object/null.json":::

前の例からの出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| emptyOutput | Bool | True |

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
