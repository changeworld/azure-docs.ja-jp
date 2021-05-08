---
title: Bicep 演算子
description: Azure Resource Manager のデプロイに使用できる Bicep 演算子について説明します。
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 4bf1005a11b1dcfea9f4b28d6bd3fa7c33e3278f
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107211268"
---
# <a name="bicep-operators"></a>Bicep 演算子

この記事では、Bicep テンプレートを作成し、Azure Resource Manager を使用してリソースをデプロイするときに使用できる Bicep 演算子について説明します。 演算子は、値の計算、値の比較、または条件の評価に使用されます。 Bicep 演算子には、[比較](#comparison)、[論理](#logical)、[数値](#numeric)の 3 種類があります。

## <a name="comparison"></a>比較

比較演算子を使用すると、値が比較され、`true` または `false` のいずれかが返されます。

| 演算子 | 名前 | 説明 |
| ---- | ---- | ---- |
| `>=` | [[次の値以上]](bicep-operators-comparison.md#greater-than-or-equal-) | 1 番目の値が 2 番目の値以上かどうかを評価します。 |
| `>`  | [より大きい](bicep-operators-comparison.md#greater-than-) | 1 番目の値が 2 番目の値より大きいかどうかを評価します。 |
| `<=` | [[次の値以下]](bicep-operators-comparison.md#less-than-or-equal-) | 1 番目の値が 2 番目の値以下かどうかを評価します。 |
| `<`  | [次の値より小さい](bicep-operators-comparison.md#less-than-) | 1 番目の値が 2 番目の値より小さいかどうかを評価します。 |
| `==` | [[等しい]](bicep-operators-comparison.md#equals-) | 2 つの値が等いかどうかを評価します。 |
| `!=` | [等しくない](bicep-operators-comparison.md#not-equal-) | 2 つの値が等しく **ない** かどうかを評価します。 |
| `=~` | [大文字と小文字の区別を無視し、等しい](bicep-operators-comparison.md#equal-case-insensitive-) | 大文字と小文字の区別を無視して、2 つの値が等しいかどうかを判定します。 |
| `!~` | [大文字と小文字の区別を無視し、等しくない](bicep-operators-comparison.md#not-equal-case-insensitive-) | 大文字と小文字の区別を無視して、2 つの値が等しく **ない** かどうかを判定します。 |

## <a name="logical"></a>論理

論理演算子は、ブール値を評価するか、null 以外の値を返すか、または条件式を評価します。

| 演算子 | 名前 | 説明 |
| ---- | ---- | ---- |
| `&&` | [And](bicep-operators-logical.md#and-) | すべての値が true の場合は `true` を返します。 |
| `||`| [Or](bicep-operators-logical.md#or-) | どちらかの値が true の場合は `true` を返します。 |
| `!` | [Not](bicep-operators-logical.md#not-) | ブール値を否定します。 |
| `??` | [Coalesce](bicep-operators-logical.md#coalesce-) | 最初の null 以外の値を返します。 |
| `?` `:` | [条件式](bicep-operators-logical.md#conditional-expression--) | true または false の条件を評価し、値を返します。 |

## <a name="numeric"></a>数値

数値演算子では整数を使用して計算を行い、整数値を返します。

| 演算子 | 名前 | 説明 |
| ---- | ---- | ---- |
| `*` | [Multiply](bicep-operators-numeric.md#multiply-) | 2 つの整数を乗算します。 |
| `/` | [除算](bicep-operators-numeric.md#divide-) | 整数を整数で除算します。 |
| `%` | [剰余](bicep-operators-numeric.md#modulo-) | 整数を整数で除算し、剰余を返します。 |
| `+` | [追加](bicep-operators-numeric.md#add-) | 2 つの整数を加算します。 |
| `-` | [Subtract](bicep-operators-numeric.md#subtract--) | 整数から整数を減算します。 |
| `-` | [Minus](bicep-operators-numeric.md#minus--) | 整数を `-1` で乗算します。 |

> [!NOTE]
> 減算とマイナスには同じ演算子を使用します。 減算では 2 つのオペランドを使用し、マイナスでは 1 つのオペランドを使用するため、機能は異なります。

## <a name="next-steps"></a>次のステップ

- Bicep ファイルを作成するには、「[チュートリアル: 最初の Azure Resource Manager Bicep ファイルを作成およびデプロイする](bicep-tutorial-create-first-bicep.md)」を参照してください。
- Bicep 型のエラーを解決する方法については、「[Bicep の any 関数](template-functions-any.md)」を参照してください。
- Bicep と JSON の構文を比較するには、「[テンプレートにおける JSON と Bicep の比較](compare-template-syntax.md)」を参照してください。
- Bicep と ARM テンプレート関数の例については、「[ARM テンプレート関数](template-functions.md)」を参照してください。
