---
title: Bicep の比較演算子
description: 値を比較する Bicep の比較演算子について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/07/2021
ms.openlocfilehash: 1a28da26a3c97982bb0e06deebae6ae68b1eb7d9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744422"
---
# <a name="bicep-comparison-operators"></a>Bicep の比較演算子

比較演算子を使用すると、値が比較され、`true` または `false` のいずれかが返されます。 この例を実行するには、Azure CLI または Azure PowerShell を使用して、[Bicep ファイルをデプロイ](./quickstart-create-bicep-use-visual-studio-code.md#deploy-the-bicep-file)します。

| 演算子 | Name |
| ---- | ---- |
| `>=` | [[次の値以上]](#greater-than-or-equal-) |
| `>`  | [より大きい](#greater-than-) |
| `<=` | [[次の値以下]](#less-than-or-equal-) |
| `<`  | [次の値より小さい](#less-than-) |
| `==` | [[等しい]](#equals-) |
| `!=` | [等しくない](#not-equal-) |
| `=~` | [大文字と小文字の区別を無視し、等しい](#equal-case-insensitive-) |
| `!~` | [大文字と小文字の区別を無視し、等しくない](#not-equal-case-insensitive-) |

## <a name="greater-than-or-equal-"></a>以上 >=

`operand1 >= operand2`

1 番目の値が 2 番目の値以上かどうかを評価します。

### <a name="operands"></a>オペランド

| オペランド | 種類 | 説明 |
| ---- | ---- | ---- |
| `operand1` | 整数、文字列 | 比較の 1 番目の値。 |
| `operand2` | 整数、文字列 | 比較の 2 番目の値。 |

### <a name="return-value"></a>戻り値

1 番目の値が 2 番目の値以上の場合は、`true` が返されます。 それ以外の場合は、`false` が返されます。

### <a name="example"></a>例

整数のペアと文字列のペアを比較します。

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'A'
param secondString string = 'A'

output intGtE bool = firstInt >= secondInt
output stringGtE bool = firstString >= secondString
```

例の出力を次に示します。

| 名前 | 種類 | 値 |
| ---- | ---- | ---- |
| `intGtE` | boolean | true |
| `stringGtE` | boolean | true |

## <a name="greater-than-"></a>より大きい >

`operand1 > operand2`

1 番目の値が 2 番目の値より大きいかどうかを評価します。

### <a name="operands"></a>オペランド

| オペランド | 種類 | 説明 |
| ---- | ---- | ---- |
| `operand1` | 整数、文字列 | 比較の 1 番目の値。 |
| `operand2` | 整数、文字列 | 比較の 2 番目の値。 |

### <a name="return-value"></a>戻り値

1 番目の値が 2 番目の値より大きい場合は、`true` が返されます。 それ以外の場合は、`false` が返されます。

### <a name="example"></a>例

整数のペアと文字列のペアを比較します。

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'bend'
param secondString string = 'band'

output intGt bool = firstInt > secondInt
output stringGt bool = firstString > secondString
```

例の出力を次に示します。

**bend** に含まれる **e** により、1 つ目の文字列の方が大きくなります。

| Name | 種類 | 値 |
| ---- | ---- | ---- |
| `intGt` | boolean | true |
| `stringGt` | boolean | true |

## <a name="less-than-or-equal-"></a>以下 <=

`operand1 <= operand2`

1 番目の値が 2 番目の値以下かどうかを評価します。

### <a name="operands"></a>オペランド

| オペランド | 種類 | 説明 |
| ---- | ---- | ---- |
| `operand1` | 整数、文字列 | 比較の 1 番目の値。 |
| `operand2` | 整数、文字列 | 比較の 2 番目の値。 |

### <a name="return-value"></a>戻り値

1 番目の値が 2 番目の値以下の場合は、`true` が返されます。 それ以外の場合は、`false` が返されます。

### <a name="example"></a>例

整数のペアと文字列のペアを比較します。

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'demo'

output intLtE bool = firstInt <= secondInt
output stringLtE bool = firstString <= secondString
```

例の出力を次に示します。

| 名前 | 種類 | 値 |
| ---- | ---- | ---- |
| `intLtE` | boolean | true |
| `stringLtE` | boolean | true |

## <a name="less-than-"></a>より小さい <

`operand1 < operand2`

1 番目の値が 2 番目の値より小さいかどうかを評価します。

### <a name="operands"></a>オペランド

| オペランド | 種類 | 説明 |
| ---- | ---- | ---- |
| `operand1` | 整数、文字列 | 比較の 1 番目の値。 |
| `operand2` | 整数、文字列 | 比較の 2 番目の値。 |

### <a name="return-value"></a>戻り値

1 番目の値が 2 番目の値より小さい場合は、`true` が返されます。 それ以外の場合は、`false` が返されます。

### <a name="example"></a>例

整数のペアと文字列のペアを比較します。

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'Demo'

output intLt bool = firstInt < secondInt
output stringLt bool = firstString < secondString
```

例の出力を次に示します。

小文字は大文字よりも小さいので、文字列は `true` です。

| Name | 種類 | 値 |
| ---- | ---- | ---- |
| `intLt` | boolean | true |
| `stringLt` | boolean | true |

## <a name="equals-"></a>等しい ==

`operand1 == operand2`

値が等しいかどうかを評価します。

### <a name="operands"></a>オペランド

| オペランド | 種類 | 説明 |
| ---- | ---- | ---- |
| `operand1` | 文字列、整数、ブール値、配列、オブジェクト | 比較の 1 番目の値。 |
| `operand2` | 文字列、整数、ブール値、配列、オブジェクト | 比較の 2 番目の値。 |

### <a name="return-value"></a>戻り値

オペランドが等しい場合は、`true` が返されます。 オペランドが異なる場合は、`false` が返されます。

### <a name="example"></a>例

整数、文字列、ブール値のペアが比較されます。

```bicep
param firstInt int = 5
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'demo'

param firstBool bool = true
param secondBool bool = true

output intEqual bool = firstInt == secondInt
output stringEqual bool = firstString == secondString
output boolEqual bool = firstBool == secondBool
```

例の出力を次に示します。

| 名前 | 種類 | 値 |
| ---- | ---- | ---- |
| `intEqual` | boolean | true |
| `stringEqual` | boolean | true |
| `boolEqual` | boolean | true |

配列を比較する場合、2 つの配列の要素と順序は同じである必要があります。 配列を互いに割り当てる必要はありません。

```bicep
var array1 = [
  1
  2
  3
]

var array2 = [
  1
  2
  3
]

var array3 = array2

var array4 = [
  3
  2
  1
]

output sameElements bool = array1 == array2 // returns true because arrays are defined with same elements
output assignArray bool = array2 == array3 // returns true because one array was defined as equal to the other array
output differentOrder bool = array4 == array1 // returns false because order of elements is different
```

例の出力を次に示します。

| 名前 | 種類 | 値 |
| ---- | ---- | ---- |
| sameElements | bool | true |
| assignArray | bool | true |
| differentOrder | [bool] | false |

オブジェクトを比較する場合、プロパティ名と値は同じである必要があります。 プロパティを同じ順序で定義する必要はありません。

```bicep
var object1 = {
  prop1: 'val1'
  prop2: 'val2'
}

var object2 = {
  prop1: 'val1'
  prop2: 'val2'
}

var object3 = {
  prop2: 'val2'
  prop1: 'val1'
}

var object4 = object3

var object5 = {
  prop1: 'valX'
  prop2: 'valY'
}

output sameObjects bool = object1 == object2 // returns true because both objects defined with same properties
output differentPropertyOrder bool = object3 == object2 // returns true because both objects have same properties even though order is different
output assignObject bool = object4 == object1 // returns true because one object was defined as equal to the other object
output differentValues bool = object5 == object1 // returns false because values are different
```

例の出力を次に示します。

| 名前 | 種類 | 値 |
| ---- | ---- | ---- |
| sameObjects | bool | true |
| differentPropertyOrder | bool | true |
| assignObject | bool | true |
| differentValues | [bool] | false |

## <a name="not-equal-"></a>等しくない !=

`operand1 != operand2`

2 つの値が等しく **ない** かどうかを評価します。

### <a name="operands"></a>オペランド

| オペランド | 種類 | 説明 |
| ---- | ---- | ---- |
| `operand1` | 文字列、整数、ブール値、配列、オブジェクト | 比較の 1 番目の値。 |
| `operand2` | 文字列、整数、ブール値、配列、オブジェクト | 比較の 2 番目の値。 |

### <a name="return-value"></a>戻り値

オペランドが等しく **ない** 場合は、`true` が返されます。 オペランドが等しい場合は、`false` が返されます。

### <a name="example"></a>例

整数、文字列、ブール値のペアが比較されます。

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'test'

param firstBool bool = false
param secondBool bool = true

output intNotEqual bool = firstInt != secondInt
output stringNotEqual bool = firstString != secondString
output boolNotEqual bool = firstBool != secondBool
```

例の出力を次に示します。

| 名前 | 種類 | 値 |
| ---- | ---- | ---- |
| `intNotEqual` | boolean | true |
| `stringNotEqual` | boolean | true |
| `boolNotEqual` | boolean | true |

配列とオブジェクトについては、「[等しい](#equals-)」の例を参照してください。

## <a name="equal-case-insensitive-"></a>大文字と小文字の区別を無視し、等しい =~

`operand1 =~ operand2`

大文字と小文字の区別を無視して、2 つの値が等しいかどうかを判定します。

### <a name="operands"></a>オペランド

| オペランド | 種類 | 説明 |
| ---- | ---- | ---- |
| `operand1`  | string | 比較の 1 番目の文字列。 |
| `operand2`  | string | 比較の 2 番目の文字列。 |

### <a name="return-value"></a>戻り値

文字列が等しい場合は、`true` が返されます。 それ以外の場合は、`false` が返されます。

### <a name="example"></a>例

大文字と小文字が混在した文字列を比較します。

```bicep
param firstString string = 'demo'
param secondString string = 'DEMO'

param thirdString string = 'demo'
param fourthString string = 'TEST'

output strEqual1 bool = firstString =~ secondString
output strEqual2 bool = thirdString =~ fourthString
```

例の出力を次に示します。

| 名前 | 種類 | 値 |
| ---- | ---- | ---- |
| `strEqual1` | boolean | true |
| `strEqual2` | boolean | false |

## <a name="not-equal-case-insensitive-"></a>大文字と小文字の区別を無視し、等しくない !~

`operand1 !~ operand2`

大文字と小文字の区別を無視して、2 つの値が等しく **ない** かどうかを判定します。

### <a name="operands"></a>オペランド

| オペランド | 種類 | 説明 |
| ---- | ---- | ---- |
| `operand1` | string | 比較の 1 番目の文字列。 |
| `operand2` | string | 比較の 2 番目の文字列。 |

### <a name="return-value"></a>戻り値

文字列が等しく **ない** 場合は、`true` が返されます。 それ以外の場合は、`false` が返されます。

### <a name="example"></a>例

大文字と小文字が混在した文字列を比較します。

```bicep
param firstString string = 'demo'
param secondString string = 'TEST'

param thirdString string = 'demo'
param fourthString string = 'DeMo'

output strNotEqual1 bool = firstString !~ secondString
output strEqual2 bool = thirdString !~ fourthString
```

例の出力を次に示します。

| 名前 | 種類 | 値 |
| ---- | ---- | ---- |
| `strNotEqual1` | boolean | true |
| `strNotEqual2` | boolean | false |

## <a name="next-steps"></a>次のステップ

- Bicep ファイルの作成方法については、「[クイックスタート: Visual Studio Code を使用して Bicep ファイルを作成する](./quickstart-create-bicep-use-visual-studio-code.md)」を参照してください。
- Bicep の型のエラーを解決する方法については、「[Bicep の any 関数](./bicep-functions-any.md)」を参照してください。
- Bicep と JSON の構文を比較するには、「[テンプレートにおける JSON と Bicep の比較](./compare-template-syntax.md)」を参照してください。
- Bicep 関数の例については、「[Bicep 関数](./bicep-functions.md)」を参照してください。
