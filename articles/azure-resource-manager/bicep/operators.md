---
title: Bicep 演算子
description: Azure Resource Manager のデプロイに使用できる Bicep 演算子について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: 13591112171919d6c58959c40dffa1340f3e8ebd
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124764316"
---
# <a name="bicep-operators"></a>Bicep 演算子

この記事では、Bicep 演算子について説明します。 演算子は、値の計算、値の比較、または条件の評価に使用されます。 Bicep 演算子には、次の 4 つの種類があります。

- [アクセサー (accessor)](#accessor)
- [比較](#comparison)
- [論理](#logical)
- [numeric](#numeric)

## <a name="operator-precedence-and-associativity"></a>演算子の優先順位と結合規則

次の演算子は、優先順位の降順で一覧表示されます (位置が高いほど優先順位が高くなります)。 同じレベルの演算子の優先順位は、同じです。

| Symbol | 演算の種類 | 結合規則 |
|:-|:-|:-|
| `(` `)` `[` `]` `.` `::` | かっこ、配列インデクサー、プロパティ アクセサー、入れ子になったリソース アクセサー  | 左から右 |
| `!` `-` | 単項 | 右から左 |
| `%` `*` `/` | 乗法 | 左から右 |
| `+` `-` | 加法 | 左から右 |
| `<=` `<` `>` `>=` | 関係 | 左から右 |
| `==` `!=` `=~` `!~` | 等価比較 | 左から右 |
| `&&` | 論理積 | 左から右 |
| `||` | 論理和 | 左から右 |
| `?` `:` | 条件式 (三項) | 右から左
| `??` | Coalesce | 左から右

## <a name="parentheses"></a>かっこ

式をかっこで囲むことで、既定の Bicep 演算子の優先順位をオーバーライドできます。 たとえば、`x + y / z` という式では、最初に除算、その次に加算が評価されます。 一方、`(x + y) / z` という式では、最初に加算、2 番目に除算が評価されます。

## <a name="accessor"></a>アクセサー

アクセサー演算子は、オブジェクトの入れ子になったリソースとプロパティにアクセスするために使用されます。

| 演算子 | 名前 | 説明 |
| ---- | ---- | ---- |
| `[]` | [インデックス アクセサー](./operators-access.md#index-accessor) | オブジェクトの配列またはプロパティの要素にアクセスします。 |
| `.` | [関数アクセサー](./operators-access.md#function-accessor) | リソースで関数を呼び出します。 |
| `::` | [入れ子になったリソース アクセサー](./operators-access.md#nested-resource-accessor) | 親リソースの外部から入れ子になったリソースにアクセスします。 |
| `.` | [プロパティ アクセサー](./operators-access.md#property-accessor) | オブジェクトのプロパティにアクセスします。 |

## <a name="comparison"></a>比較

比較演算子を使用すると、値が比較され、`true` または `false` のいずれかが返されます。

| 演算子 | 名前 | 説明 |
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

| 演算子 | 名前 | 説明 |
| ---- | ---- | ---- |
| `&&` | [And](./operators-logical.md#and-) | すべての値が true の場合は `true` を返します。 |
| `||`| [Or](./operators-logical.md#or-) | どちらかの値が true の場合は `true` を返します。 |
| `!` | [Not](./operators-logical.md#not-) | ブール値を否定します。 1 つのオペランドを受け取ります。 |
| `??` | [Coalesce](./operators-logical.md#coalesce-) | 最初の null 以外の値を返します。 |
| `?` `:` | [条件式](./operators-logical.md#conditional-expression--) | true または false の条件を評価し、値を返します。 |

## <a name="numeric"></a>数値

数値演算子では整数を使用して計算を行い、整数値を返します。

| 演算子 | 名前 | 説明 |
| ---- | ---- | ---- |
| `*` | [Multiply](./operators-numeric.md#multiply-) | 2 つの整数を乗算します。 |
| `/` | [除算](./operators-numeric.md#divide-) | 整数を整数で除算します。 |
| `%` | [剰余](./operators-numeric.md#modulo-) | 整数を整数で除算し、剰余を返します。 |
| `+` | [追加](./operators-numeric.md#add-) | 2 つの整数を加算します。 |
| `-` | [Subtract](./operators-numeric.md#subtract--) | 1 つの整数を別の整数から減算します。 2 つのオペランドを受け取ります。 |
| `-` | [マイナス](./operators-numeric.md#minus--) (単項) | 整数を `-1` で乗算します。 1 つのオペランドを受け取ります。 |

> [!NOTE]
> 減算とマイナスには同じ演算子を使用します。 減算では 2 つのオペランドを使用し、マイナスでは 1 つのオペランドを使用するため、機能は異なります。


## <a name="next-steps"></a>次のステップ

- Bicep ファイルの作成方法については、「[クイックスタート: Visual Studio Code を使用して Bicep ファイルを作成する](./quickstart-create-bicep-use-visual-studio-code.md)」を参照してください。
- Bicep の型のエラーを解決する方法については、「[Bicep の any 関数](./bicep-functions-any.md)」を参照してください。
- Bicep と JSON の構文を比較するには、「[テンプレートにおける JSON と Bicep の比較](./compare-template-syntax.md)」を参照してください。
- Bicep 関数の例については、「[Bicep 関数](./bicep-functions.md)」を参照してください。
