---
title: Bicep 数値演算子
description: 値を計算する Bicep 数値演算子について説明します。
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: f2c7f722a3f13648c94b69039d31e5095a3256f7
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211252"
---
# <a name="bicep-numeric-operators"></a>Bicep 数値演算子

数値演算子は整数を使用して計算を行い、整数値を返します。 この例を実行するには、Azure CLI または Azure PowerShell を使用して、[Bicep ファイルをデプロイ](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)します。

| 演算子 | Name |
| ---- | ---- |
| `*` | [Multiply](#multiply-) |
| `/` | [除算](#divide-) |
| `%` | [剰余](#modulo-) |
| `+` | [追加](#add-) |
| `-` | [Subtract](#subtract--) |
| `-` | [マイナス](#minus--) |

> [!NOTE]
> 減算とマイナスには同じ演算子を使用します。 減算では 2 つのオペランドを使用し、マイナスでは 1 つのオペランドを使用するため、機能は異なります。

## <a name="multiply-"></a>乗算 *

`operand1 * operand2`

2 つの整数を乗算します。

### <a name="operands"></a>オペランド

| オペランド | 種類 | 説明 |
| ---- | ---- | ---- |
| `operand1`  | 整数 (integer) | 乗算する数値。 |
| `operand2`  | 整数 (integer)  | 数値の乗数。 |

### <a name="return-value"></a>戻り値

乗算は積を整数として返します。

### <a name="example"></a>例

2 つの整数を乗算し積を返します。

```bicep
param firstInt int = 5
param secondInt int = 2

output product int = firstInt * secondInt
```

例からの出力を次に示します。

| Name | 種類 | 値 |
| ---- | ---- | ---- |
| `product` | 整数 (integer) | 10 |

## <a name="divide-"></a>除算 /

`operand1 / operand2`

整数を整数で除算します。

### <a name="operands"></a>オペランド

| オペランド | 種類 | 説明 |
| ---- | ---- | ---- |
| `operand1` | 整数 (integer) | 除算される整数。 |
| `operand2` | 整数 (integer) | 除算に使用される整数。 ゼロにすることはできません。 |

### <a name="return-value"></a>戻り値

除算は商を整数として返します。

### <a name="example"></a>例

2 つの整数が除算され、商が返されます。

```bicep
param firstInt int = 10
param secondInt int = 2

output quotient int = firstInt / secondInt
```

例からの出力を次に示します。

| Name | 種類 | 値 |
| ---- | ---- | ---- |
| `quotient` | 整数 (integer) | 5 |

## <a name="modulo-"></a>剰余 %

`operand1 % operand2`

整数を整数で除算し、剰余を返します。

### <a name="operands"></a>オペランド

| オペランド | 種類 | 説明 |
| ---- | ---- | ---- |
| `operand1`  | 整数 (integer)  | 除算される整数。 |
| `operand2`  | 整数 (integer)  | 除算に使用される整数。 0 にすることはできません。 |

### <a name="return-value"></a>戻り値

剰余は整数として返されます。 除算で剰余が生成されない場合は、0 が返されます。

### <a name="example"></a>例

2 つの整数ペアが除算され、剰余が返されます。

```bicep
param firstInt int = 10
param secondInt int = 3

param thirdInt int = 8
param fourthInt int = 4

output remainder int = firstInt % secondInt
output zeroRemainder int = thirdInt % fourthInt
```

例からの出力を次に示します。

| Name | 種類 | 値 |
| ---- | ---- | ---- |
| `remainder` | integer | 1 |
| `zeroRemainder` | 整数 (integer) | 0 |

## <a name="add-"></a>加算 +

`operand1 + operand2`

2 つの整数を加算します。

### <a name="operands"></a>オペランド

| オペランド | 種類 | 説明 |
| ---- | ---- | ---- |
| `operand1` | 整数 (integer) | 加算する数値。 |
| `operand2` | 整数 (integer) | 数値に加算される数値。 |

### <a name="return-value"></a>戻り値

加算は合計を整数として返します。

### <a name="example"></a>例

2 つの整数が加算され、合計が返されます。

```bicep
param firstInt int = 10
param secondInt int = 2

output sum int = firstInt + secondInt
```

例からの出力を次に示します。

| Name | 種類 | 値 |
| ---- | ---- | ---- |
| `sum` | 整数 (integer) | 12 |

## <a name="subtract--"></a>減算 -

`operand1 - operand2`

整数から整数を減算します。

### <a name="operands"></a>オペランド

| オペランド | 種類 | 説明 |
| ---- | ---- | ---- |
| `operand1` | 整数 (integer) | 減算される大きい方の数値。 |
| `operand2` | 整数 (integer) | 大きい方の数値から減算された数値。 |

### <a name="return-value"></a>戻り値

減算は差を整数として返します。

### <a name="example"></a>例

整数が減算され、差が返されます。

```bicep
param firstInt int = 10
param secondInt int = 4

output difference int = firstInt - secondInt
```

例からの出力を次に示します。

| Name | 種類 | 値 |
| ---- | ---- | ---- |
| `difference` | 整数 (integer) | 6 |

## <a name="minus--"></a>マイナス -

`-integerValue`

整数を `-1` で乗算します。

### <a name="operand"></a>オペランド

| オペランド | 種類 | 説明 |
| ---- | ---- | ---- |
| `integerValue` | 整数 (integer) | `-1` で乗算した整数。 |

### <a name="return-value"></a>戻り値

整数は、`-1` で乗算されます。 正の整数は負の整数を返し、負の整数は正の整数を返します。 値は括弧で囲むことができます。

### <a name="example"></a>例

```bicep
param posInt int = 10
param negInt int = -20

output startedPositive int = -posInt
output startedNegative int = -(negInt)
```

例からの出力を次に示します。

| Name | 種類 | 値 |
| ---- | ---- | ---- |
| `startedPositive` | 整数 (integer) | -10 |
| `startedNegative` | 整数 (integer) | 20 |

## <a name="next-steps"></a>次のステップ

- Bicep ファイルを作成するには、「[チュートリアル: 最初の Azure Resource Manager Bicep ファイルを作成およびデプロイする](bicep-tutorial-create-first-bicep.md)」を参照してください。
- Bicep 型のエラーを解決する方法については、「[Bicep の any 関数](template-functions-any.md)」を参照してください。
- Bicep と JSON の構文を比較するには、「[テンプレートにおける JSON と Bicep の比較](compare-template-syntax.md)」を参照してください。
- Bicep と ARM テンプレート関数の例については、「[ARM テンプレート関数](template-functions.md)」を参照してください。
