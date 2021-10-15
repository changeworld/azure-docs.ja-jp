---
title: Bicep のデータ型
description: Bicep で使用可能なデータ型について説明します
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: e2dffac58c3fec1b6c29d2c5e1542c9fec7ec0e4
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357928"
---
# <a name="data-types-in-bicep"></a>Bicep のデータ型

この記事では、[Bicep](./overview.md) でサポートされているデータ型について説明します。

## <a name="supported-types"></a>サポートされている型

Bicep 内では、こちらのデータ型を使用できます。

* array
* [bool]
* INT
* object
* secureObject - Bicep の修飾子によって示されます
* secureString - Bicep の修飾子によって示されます
* string

## <a name="arrays"></a>配列

配列は左大かっこ (`[`) で始めて、右大かっこ (`]`) で終わります。 Bicep では、配列は複数の行で宣言する必要があります。 値の間にはコンマを使用しないでください。

配列内の各項目は、[any 型](bicep-functions-any.md)によって表現されます。 各項目が同じデータ型である配列、または異なるデータ型を保持する配列を指定できます。

次の例は、整数の配列およびさまざまな型の配列を示したものです。

```bicep
var integerArray = [
  1
  2
  3
]

var mixedArray = [
  resourceGroup().name
  1
  true
  'example string'
]
```

Bicep の配列は 0 オリジンです。 次の例では、`exampleArray[0]` は 1 に評価され、`exampleArray[2]` は 3 に評価されます。 インデクサーのインデックス自体が別の式である場合があります。 式 `exampleArray[index]` は 2 に評価されます。 整数インデクサーは、配列型の式でのみ使用できます。

```bicep
var index = 1

var exampleArray = [
  1
  2
  3
]
```

## <a name="booleans"></a>ブール値

ブール値を指定するとき、`true` または `false` を使用します。 値を引用符で囲まないでください。

```bicep
param exampleBool bool = true
```

## <a name="integers"></a>整数

整数値を指定するとき、引用符を使用しないでください。

```bicep
param exampleInt int = 1
```

Bicep の整数は 64 ビット整数です。 デプロイに使用する SDK またはコマンドライン ツールによっては、インライン パラメーターとして使用できる値の範囲に制約がある場合があります。 たとえば、PowerShell を使用して Bicep をデプロイする場合、整数型は -2147483648 から 2147483647 の範囲で指定できます。 この制限を回避するには、[パラメーター ファイル](parameter-files.md)で大きな整数値を指定します。 リソースの種類によって、整数プロパティに独自の制限が適用されます。

浮動小数点、10 進数、またはバイナリ形式は現在サポートされていません。

## <a name="objects"></a>オブジェクト

オブジェクトは左中かっこ (`{`) で始めて、右中かっこ (`}`) で終わります。 Bicep では、オブジェクは複数の行で宣言する必要があります。 オブジェクト内の各プロパティはキーと値で構成されます。 キーと値はコロン (`:`) で区切られます。 オブジェクトでは、任意の型の任意のプロパティが許可されます。 プロパティ間にコンマを使用しないでください。

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

Bicep では、必要に応じてオブジェクトのプロパティ キーに引用符を使用できます。

```bicep
var test = {
  'my - special. key': 'value'
}
```

前の例では、オブジェクトのプロパティ キーに特殊文字が含まれている場合に、引用符が使用されています。  たとえば、スペース、'-'、'.' などです。 次の例は、オブジェクト プロパティ キーに補間を使用する方法を示しています。

```bicep
var stringVar = 'example value'
var objectVar = {
  '${stringVar}': 'this value'
}
```

オブジェクトのプロパティにアクセスするために、プロパティ アクセサーが使用されます。 これらは、`.` 演算子を使用して構築されます。

```bicep
var a = {
  b: 'Dev'
  c: 42
  d: {
    e: true
  }
}

output result1 string = a.b // returns 'Dev'
output result2 int = a.c // returns 42
output result3 bool = a.d.e // returns true
```

プロパティ アクセサーは、オブジェクト型のパラメーターや変数、オブジェクト リテラルなど、任意のオブジェクトとともに使用できます。 オブジェクト型以外の式でプロパティ アクセサーを使用すると、エラーが発生します。

`[]` 構文を使用してプロパティにアクセスすることもできます。 次の例では、`Development` が返されます。

```bicep
var environmentSettings = {
  dev: {
    name: 'Development'
  }
  prod: {
    name: 'Production'
  }
}

output accessorResult string = environmentSettings['dev'].name
```

## <a name="strings"></a>文字列

Bicep では、文字列は単一引用符でマークされ、1 行で宣言する必要があります。 *0* から *10FFFF* のコード ポイントの Unicode 文字をすべて使用できます。

```bicep
param exampleString string = 'test value'
```

次の表に、円記号 (`\`) 文字でエスケープする必要がある予約文字のセットを示します。

| エスケープ シーケンス | 表される値 | メモ |
|:-|:-|:-|
| `\\` | `\` ||
| `\'` | `'` ||
| `\n` | ライン フィード (LF) ||
| `\r` | キャリッジ リターン (CR) ||
| `\t` | タブ文字 ||
| `\u{x}` | Unicode コード ポイント `x` | **x** は、*0* から *10FFFF* の範囲にある 16 進数のコード ポイント を表します (0 と 10FFFF を含む)。 先頭に 0 を指定できます。 *FFFF* より大きいコード ポイントはサロゲート ペアとして出力されます。
| `\$` | `$` | `{` が後に続く場合のみエスケープします。 |

```bicep
// evaluates to "what's up?"
var myVar = 'what\'s up?'
```

Bicep 内のすべての文字列は、補間をサポートしています。 式を挿入するには、その式を `${` と `}` で囲みます。 参照される式は、複数の行にまたがることはできません。

```bicep
var storageName = 'storage${uniqueString(resourceGroup().id)}
```

## <a name="multi-line-strings"></a>複数行の文字列

Bicep では、連続した 3 つのシングル クォーテーション記号 2 組ではさんだ部分が複数行文字列となります。最初の 3 つのシングル クォーテーション (`'''`) を開始シーケンスと呼び、必要に応じてこの後に改行を挿入します。最後の 3 つのシングル クォーテーション (`'''`) は終了シーケンスと呼びます。 開始シーケンスと終了シーケンスの間に入力された文字は逐語的に読み取られ、エスケープは必要ありません。

> [!NOTE]
> Bicep パーサーでは、Bicep ファイルの行の終わりに従って、すべての文字がそのまま読み取られるため、改行は `\r\n` または `\n` として解釈される可能性があります。
> 現在、複数行の文字列では、補間はサポートされていません。
> `'''` を含む複数行の文字列はサポートされていません。

```bicep
// evaluates to "hello!"
var myVar = '''hello!'''

// evaluates to "hello!" because the first newline is skipped
var myVar2 = '''
hello!'''

// evaluates to "hello!\n" because the final newline is included
var myVar3 = '''
hello!
'''

// evaluates to "  this\n    is\n      indented\n"
var myVar4 = '''
  this
    is
      indented
'''

// evaluates to "comments // are included\n/* because everything is read as-is */\n"
var myVar5 = '''
comments // are included
/* because everything is read as-is */
'''

// evaluates to "interpolation\nis ${blocked}"
// note ${blocked} is part of the string, and is not evaluated as an expression
myVar6 = '''interpolation
is ${blocked}'''
```

## <a name="secure-strings-and-objects"></a>セキュリティで保護された文字列とオブジェクト

セキュリティで保護された文字列では文字列と同じ形式が使用され、セキュリティで保護されたオブジェクトではオブジェクトと同じ形式が使用されます。 Bicep の使用時、文字列またはオブジェクトに `@secure()` 修飾子を追加します。

パラメーターをセキュリティで保護された文字列またはセキュリティで保護されたオブジェクトに設定した場合、パラメーターの値はデプロイ履歴に保存されず、ログにも記録されません。 ただし、セキュリティで保護された値を、セキュリティで保護された値を想定していないプロパティに設定した場合、その値は保護されません。 たとえば、セキュリティで保護された文字列をタグに設定すると、その値はプレーンテキストとして格納されます。 パスワードとシークレットにはセキュリティで保護された文字列を使用します。

次の例からは、セキュリティで保護された 2 つのパラメーターを確認できます。

```bicep
@secure()
param password string

@secure()
param configValues object
```

## <a name="data-type-assignability"></a>データ型の割り当て可否

Bicep では、ある型 (ソース型) の値をもう 1 つの別の型 (ターゲット型) に割り当てることができます。 次のテーブルは、どのソース型 (横向きに配置) をどのターゲット型 (縦向きに配置) に割り当てられるかを示しています。 このテーブルで、`X` は割り当てられることを、空欄は割り当てられないことを、`?` は互換性がある場合に限り割り当てられることを表します。

| 種類 | `any` | `error` | `string` | `number` | `int` | `bool` | `null` | `object` | `array` | 名前付きリソース | 名前付きモジュール | `scope` |
|-|-|-|-|-|-|-|-|-|-|-|-|-|
| `any`          |X| |X|X|X|X|X|X|X|X|X|X|
| `error`        | | | | | | | | | | | | |
| `string`       |X| |X| | | | | | | | | |
| `number`       |X| | |X|X| | | | | | | |
| `int`          |X| | | |X| | | | | | | |
| `bool`         |X| | | | |X| | | | | | |
| `null`         |X| | | | | |X| | | | | |
| `object`       |X| | | | | | |X| | | | |
| `array`        |X| | | | | | | |X| | | |
| `resource`     |X| | | | | | | | |X| | |
| `module`       |X| | | | | | | | | |X| |
| `scope`        | | | | | | | | | | | |?|
| **名前付きリソース** |X| | | | | | |?| |?| | |
| **名前付きモジュール**   |X| | | | | | |?| | |?| |

## <a name="next-steps"></a>次のステップ

Bicep の構造と構文については、[Bicep ファイルの構造](./file.md)に関する記事を参照してください。
