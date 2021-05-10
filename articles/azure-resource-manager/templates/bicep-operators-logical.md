---
title: Bicep 論理演算子
description: 条件を評価する Bicep 論理演算子について説明します。
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 1eecbe589ecf08e32246dd97d137c60fa7b5078f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211275"
---
# <a name="bicep-logical-operators"></a>Bicep 論理演算子

論理演算子は、ブール値を評価するか、null 以外の値を返すか、または条件式を評価します。 この例を実行するには、Azure CLI または Azure PowerShell を使用して、[Bicep ファイルをデプロイ](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)します。

| 演算子 | 名前 |
| ---- | ---- |
| `&&` | [And](#and-) |
| `||` | [Or](#or-) |
| `!` | [Not](#not-) |
| `??` | [Coalesce](#coalesce-) |
| `?` `:` | [条件式](#conditional-expression--) |

## <a name="and-"></a>And &&

`operand1 && operand2`

両方の値が true かどうかを判断します。

### <a name="operands"></a>オペランド

| オペランド | 種類 | 説明 |
| ---- | ---- | ---- |
| `operand1` | boolean | true かどうかを確認する最初の値。 |
| `operand2` | boolean | true かどうかを確認する 2 番目の値。 |
| 追加のオペランド | boolean | 追加のオペランドを含めることができます。 |

### <a name="return-value"></a>戻り値

両方の値が true の場合は `True`、それ以外の場合は `false` が返されます。

### <a name="example"></a>例

一連のパラメーター値と一連の式を評価します。

```bicep
param operand1 bool = true
param operand2 bool = true

output andResultParm bool = operand1 && operand2
output andResultExp bool = bool(10 >= 10) && bool(5 > 2)
```

出力例を次に示します。

| 名前 | 種類 | 値 |
| ---- | ---- | ---- |
| `andResultParm` | boolean | true |
| `andResultExp` | boolean | true |

## <a name="or-"></a>Or ||

`operand1 || operand2`

どちらかの値が true かどうかを判断します。

### <a name="operands"></a>オペランド

| オペランド | 種類 | 説明 |
| ---- | ---- | ---- |
| `operand1` | boolean | true かどうかを確認する最初の値。 |
| `operand2` | boolean | true かどうかを確認する 2 番目の値。 |
| 追加のオペランド | boolean | 追加のオペランドを含めることができます。 |

### <a name="return-value"></a>戻り値

どちらかの値が true の場合は `True`、それ以外の場合は `false` が返されます。

### <a name="example"></a>例

一連のパラメーター値と一連の式を評価します。

```bicep
param operand1 bool = true
param operand2 bool = false

output orResultParm bool = operand1 || operand2
output orResultExp bool = bool(10 >= 10) || bool(5 < 2)
```

出力例を次に示します。

| 名前 | 種類 | 値 |
| ---- | ---- | ---- |
| `orResultParm` | boolean | true |
| `orResultExp` | boolean | true |

## <a name="not-"></a>Not !

`!boolValue`

ブール値を否定します。

### <a name="operand"></a>オペランド

| オペランド | 種類 | 説明 |
| ---- | ---- | ---- |
| `boolValue` | boolean | 否定されるブール値。 |

### <a name="return-value"></a>戻り値

初期値を否定し、ブール値を返します。 初期値が `true` の場合は、`false` が返されます。

### <a name="example"></a>例

`not` 演算子は、 値を否定します。 値は括弧で囲むことができます。

```bicep
param initTrue bool = true
param initFalse bool = false

output startedTrue bool = !(initTrue)
output startedFalse bool = !initFalse
```

出力例を次に示します。

| 名前 | 種類 | 値 |
| ---- | ---- | ---- |
| `startedTrue` | boolean | false |
| `startedFalse` | boolean | true |

## <a name="coalesce-"></a>Coalesce ??

`operand1 ?? operand2`

オペランドから最初の null 以外の値を返します。

### <a name="operands"></a>オペランド

| オペランド | 種類 | 説明 |
| ---- | ---- | ---- |
| `operand1` | 文字列、整数、ブール値、オブジェクト、配列 | `null` をテストする値。 |
| `operand2` | 文字列、整数、ブール値、オブジェクト、配列 | `null` をテストする値。 |
| 追加のオペランド | 文字列、整数、ブール値、オブジェクト、配列 | `null` をテストする値。 |

### <a name="return-value"></a>戻り値

最初の null 以外の値を返します。 空の文字列、空の配列、および空のオブジェクトは `null` ではなく、\<empty> 値が返されます。

### <a name="example"></a>例

出力ステートメントは、null 以外の値を返します。 出力の型は、比較の型と一致する必要があります。一致しない場合、エラーが生成されます。

```bicep
param myObject object = {
  'isnull1': null
  'isnull2': null
  'string': 'demoString'
  'emptystr': ''
  'integer': 10
  }

output nonNullStr string = myObject.isnull1 ?? myObject.string ?? myObject.isnull2
output nonNullInt int = myObject.isnull1 ?? myObject.integer ?? myObject.isnull2
output nonNullEmpty string = myObject.isnull1 ?? myObject.emptystr ?? myObject.string ?? myObject.isnull2
```

出力例を次に示します。

| 名前 | 種類 | 値 |
| ---- | ---- | ---- |
| `nonNullStr` | string | demoString |
| `nonNullInt` | INT | 10 |
| `nonNullEmpty` | string | \<empty> |

## <a name="conditional-expression--"></a>Conditional expression ? :

`condition ? true-value : false-value`

条件を評価し、条件が true か false かに関係なく値を返します。

### <a name="operands"></a>オペランド

| オペランド | 種類 | 説明 |
| ---- | ---- | ---- |
| `condition` | boolean | true または false として評価する条件。 |
| `true-value` | 文字列、整数、ブール値、オブジェクト、配列 | 条件が true の場合の値。 |
| `false-value` | 文字列、整数、ブール値、オブジェクト、配列 | 条件が false の場合の値。 |

### <a name="example"></a>例

この例では、パラメーターの初期値を評価し、条件が true か false かに関係なく値を返します。

```bicep
param initValue bool = true

output outValue string = initValue ? 'true value' : 'false value'
```

出力例を次に示します。

| 名前 | 種類 | 値 |
| ---- | ---- | ---- |
| `outValue` | string | true 値 |

## <a name="next-steps"></a>次のステップ

- Bicep ファイルを作成するには、「[チュートリアル: 最初の Azure Resource Manager Bicep ファイルを作成およびデプロイする](bicep-tutorial-create-first-bicep.md)」を参照してください。
- Bicep 型のエラーを解決する方法については、「[Bicep の any 関数](template-functions-any.md)」を参照してください。
- Bicep と JSON の構文を比較するには、「[テンプレートにおける JSON と Bicep の比較](compare-template-syntax.md)」を参照してください。
- Bicep と ARM テンプレート関数の例については、「[ARM テンプレート関数](template-functions.md)」を参照してください。
