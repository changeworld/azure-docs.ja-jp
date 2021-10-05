---
title: Bicep の関数 - 数値関数
description: Bicep ファイルで数値を扱う関数について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: 8df0aec96001aa5b1a7cdd7342fe663f0bfa2da0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124758486"
---
# <a name="numeric-functions-for-bicep"></a>Bicep の数値関数

この記事では、整数を操作するための Bicep 関数について説明します。

Azure Resource Manager JSON 数値関数の一部は、[Bicep 数値演算子](./operators-numeric.md)に置き換えられました。

## <a name="int"></a>INT

`int(valueToConvert)`

指定された値を整数に変換します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| valueToConvert |はい |文字列または整数 |整数に変換する値。 |

### <a name="return-value"></a>戻り値

変換された値の整数。

### <a name="example"></a>例

次の例では、ユーザー指定のパラメーター値を整数に変換します。

```bicep
param stringToConvert string = '4'

output inResult int = int(stringToConvert)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| intResult | int | 4 |

## <a name="max"></a>max

`max (arg1)`

整数の配列または整数のコンマ区切りリストから最大値を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数の配列、または整数のコンマ区切りリスト |最大値を取得するコレクション。 |

### <a name="return-value"></a>戻り値

コレクションの最大値を表す整数。

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

output arrayOutPut int = max(arrayToTest)
output intOutput int = max(0,3,2,5,4)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| arrayOutput | int | 5 |
| intOutput | int | 5 |

## <a name="min"></a>min

`min (arg1)`

整数の配列または整数のコンマ区切りリストから最小値を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数の配列、または整数のコンマ区切りリスト |最小値を取得するコレクション。 |

### <a name="return-value"></a>戻り値

コレクションの最小値を表す整数。

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

output arrayOutPut int = min(arrayToTest)
output intOutput int = min(0,3,2,5,4)
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| arrayOutput | int | 0 |
| intOutput | int | 0 |

## <a name="next-steps"></a>次のステップ

* 数値に関連するその他のアクションについては、「[Bicep 数値演算子](./operators-numeric.md)」を参照してください。
