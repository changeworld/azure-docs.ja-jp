---
title: Bicep のデータ型
description: Bicep で使用可能なデータ型について説明します
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 31f2c6e979acb3b0b622bc63ffb8a2845179491d
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026865"
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

Bicep の配列は 0 が基準です。 次の例では、`exampleArray[0]` は 1 に評価され、`exampleArray[2]` は 3 に評価されます。 インデクサーのインデックス自体が別の式である場合があります。 式 `exampleArray[index]` は 2 に評価されます。 整数インデクサーは、配列型の式でのみ使用できます。

```bicep
var index = 1

var exampleArray = [
  1
  2
  3
]
```

Bicep では、文字列ベースのインデクサーを使用できます。

```bicep
param environment string = 'prod'

var environmentSettings = {
  dev: {
    name: 'dev'
  }
  prod: {
    name: 'prod'
  }
}
```

式 environmentSettings['dev'] は次のオブジェクトに評価されます。

```bicep
{
  name: 'dev'
}
```

次の例は、さまざまな型を持つ配列を示します。

```bicep
var mixedArray = [
  resourceGroup().name
  1
  true
  'example string'
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

インライン パラメーターとして渡される整数の場合、値の範囲はデプロイに使用する SDK またはコマンドライン ツールによって制限されることがあります。 たとえば、PowerShell を使用して Bicep をデプロイする場合、整数型は -2147483648 から 2147483647 の範囲で指定できます。 この制限を回避するには、[パラメーター ファイル](parameter-files.md)で大きな整数値を指定します。 リソースの種類によって、整数プロパティに独自の制限が適用されます。

浮動小数点、10 進数、またはバイナリ形式は現在サポートされていません。

## <a name="objects"></a>オブジェクト

オブジェクトは左中かっこ (`{`) で始めて、右中かっこ (`}`) で終わります。 Bicep では、オブジェクは複数の行で宣言する必要があります。 オブジェクト内の各プロパティはキーと値で構成されます。 キーと値はコロン (`:`) で区切られます。 オブジェクトでは、任意の型の任意のプロパティが許可されます。

Bicep では、キーは引用符で囲まれません。 プロパティ間にコンマを使用しないでください。

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

オブジェクトのプロパティにアクセスするために、プロパティ アクセサーが使用されます。 これらは、`.` 演算子を使用して構築されます。 次に例を示します。

```bicep
var x = {
  y: {
    z: 'Hello`
    a: true
  }
  q: 42
}
```

前の宣言を行うと、式 x.y.z はリテラル文字列の "Hello" に評価されます。 同様に、式 x.q は整数リテラルの 42 に評価されます。

プロパティ アクセサーは、オブジェクト型のパラメーターや変数、オブジェクト リテラルなど、任意のオブジェクトとともに使用できます。 オブジェクト型以外の式でプロパティ アクセサーを使用すると、エラーが発生します。

## <a name="strings"></a>文字列

Bicep では、文字列は単一引用符でマークされ、1 行で宣言する必要があります。 コード ポイントが *0* から *10FFFF* までのすべての Unicode 文字が許可されます。

```bicep
param exampleString string = 'test value'
```

次の表に、円記号 (`\`) 文字でエスケープする必要がある予約文字のセットを示します。

| エスケープ シーケンス | 表される値 | メモ |
|:-|:-|:-|
| \\ | \ ||
| \' | ' ||
| \n | ライン フィード (LF) ||
| \r | キャリッジ リターン (CR) ||
| \t | タブ文字 ||
| \u{x} | Unicode コード ポイント *x* | *x* は、*0* から *10FFFF* (両端を含む) の間の 16 進数コード ポイントの値を表します。 先頭に 0 を指定できます。 *FFFF* を超えるコード ポイントは、サロゲート ペアとして生成されます。
| \$ | $ | エスケープする必要があるのは、 *{* が続く場合のみです。 |

```bicep
// evaluates to "what's up?"
var myVar = 'what\'s up?'
```

Bicep 内のすべての文字列は、補間をサポートしています。 式を挿入するには、 *${* と *}` で囲みます。 参照される式は、複数の行にまたがることはできません。

```bicep
var storageName = 'storage${uniqueString(resourceGroup().id)}
```

## <a name="multi-line-strings"></a>複数行の文字列

Bicep では、複数行の文字列は、3 つの単一引用符 (`'''`) の後に、必要に応じて改行 (開始シーケンス) と、3 つの単一引用符文字 (`'''` - 終了シーケンス) を指定して定義されます。 開始シーケンスと終了シーケンスの間に入力された文字は逐語的に読み取られ、エスケープは必要ありません。

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

## <a name="next-steps"></a>次のステップ

Bicep の構造と構文については、[Bicep ファイルの構造](./file.md)に関する記事を参照してください。
