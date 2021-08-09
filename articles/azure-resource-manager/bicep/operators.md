---
title: Bicep 演算子
description: Azure Resource Manager のデプロイに使用できる Bicep 演算子について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 154a42d1bcdc78eee63241286e8f65ab7777bc6b
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "111027225"
---
# <a name="bicep-operators"></a>Bicep 演算子

この記事では、Bicep テンプレートを作成し、Azure Resource Manager を使用してリソースをデプロイするときに使用できる Bicep 演算子について説明します。 演算子は、値の計算、値の比較、または条件の評価に使用されます。 Bicep 演算子には、次の 3 つの種類があります。

- [比較](#comparison)
- [論理](#logical)
- [numeric](#numeric)

`(` と `)` の間に式を挿入することで、既定の Bicep 演算子の優先順位をオーバーライドできます。 たとえば、"x + y / z" という式では、最初に除算、その次に加算が評価されます。 一方、“(x + y) / z” という式では、最初に加算、2 番目に除算が評価されます。

`::` オペレーターを使用してリソースにアクセスする方法については、「[Set name and type for child resources in Bicep](child-resource-name-type.md) (Bicep での子リソースの名前と型の設定)」を参照してください。

## <a name="comparison"></a>比較

比較演算子を使用すると、値が比較され、`true` または `false` のいずれかが返されます。

| 演算子 | Name | 説明 |
| ---- | ---- | ---- |
| `>=` | [[次の値以上]](./operators-comparison.md#greater-than-or-equal-) | 1 番目の値が 2 番目の値以上かどうかを評価します。 |
| `>`  | [より大きい](./operators-comparison.md#greater-than-) | 1 番目の値が 2 番目の値より大きいかどうかを評価します。 |
| `<=` | [[次の値以下]](./operators-comparison.md#less-than-or-equal-) | 1 番目の値が 2 番目の値以下かどうかを評価します。 |
| `<`  | [次の値より小さい](./operators-comparison.md#less-than-) | 1 番目の値が 2 番目の値より小さいかどうかを評価します。 |
| `==` | [[等しい]](./operators-comparison.md#equals-) | 2 つの値が等いかどうかを評価します。 |
| `!=` | [等しくない](./operators-comparison.md#not-equal-) | 2 つの値が等しく **ない** かどうかを評価します。 |
| `=~` | [大文字と小文字の区別を無視し、等しい](./operators-comparison.md#equal-case-insensitive-) | 大文字と小文字の区別を無視して、2 つの値が等しいかどうかを判定します。 |
| `!~` | [大文字と小文字の区別を無視し、等しくない](./operators-comparison.md#not-equal-case-insensitive-) | 大文字と小文字の区別を無視して、2 つの値が等しく **ない** かどうかを判定します。 |

## <a name="logical"></a>論理

論理演算子は、ブール値を評価するか、null 以外の値を返すか、または条件式を評価します。

| 演算子 | Name | 説明 |
| ---- | ---- | ---- |
| `&&` | [And](./operators-logical.md#and-) | すべての値が true の場合は `true` を返します。 |
| `||`| [Or](./operators-logical.md#or-) | どちらかの値が true の場合は `true` を返します。 |
| `!` | [Not](./operators-logical.md#not-) | ブール値を否定します。 |
| `??` | [Coalesce](./operators-logical.md#coalesce-) | 最初の null 以外の値を返します。 |
| `?` `:` | [条件式](./operators-logical.md#conditional-expression--) | true または false の条件を評価し、値を返します。 |

## <a name="numeric"></a>数値

数値演算子では整数を使用して計算を行い、整数値を返します。

| 演算子 | Name | 説明 |
| ---- | ---- | ---- |
| `*` | [Multiply](./operators-numeric.md#multiply-) | 2 つの整数を乗算します。 |
| `/` | [除算](./operators-numeric.md#divide-) | 整数を整数で除算します。 |
| `%` | [剰余](./operators-numeric.md#modulo-) | 整数を整数で除算し、剰余を返します。 |
| `+` | [追加](./operators-numeric.md#add-) | 2 つの整数を加算します。 |
| `-` | [Subtract](./operators-numeric.md#subtract--) | 整数から整数を減算します。 |
| `-` | [Minus](./operators-numeric.md#minus--) | 整数を `-1` で乗算します。 |

> [!NOTE]
> 減算とマイナスには同じ演算子を使用します。 減算では 2 つのオペランドを使用し、マイナスでは 1 つのオペランドを使用するため、機能は異なります。

## <a name="next-steps"></a>次のステップ

- Bicep ファイルの作成方法については、「[Quickstart: Create Bicep files with Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md) (クイックスタート: Visual Studio Code を使用して Bicep ファイルを作成する)」を参照してください。
- Bicep の型のエラーを解決する方法については、「[Bicep の any 関数](./bicep-functions-any.md)」を参照してください。
- Bicep と JSON の構文を比較するには、「[テンプレートにおける JSON と Bicep の比較](./compare-template-syntax.md)」を参照してください。
- Bicep 関数の例については、「[Bicep functions](./bicep-functions.md) (Bicep 関数)」を参照してください。
