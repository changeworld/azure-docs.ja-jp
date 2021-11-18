---
title: IoT コネクタのカスタム関数-Azure の医療 Api
description: この記事では、IoT Connector デバイスマッピングテンプレートでカスタム関数を使用する方法について説明します。
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 37e720e3623105f5d3a6131a1df5d6d53edb6db7
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733529"
---
# <a name="how-to-use-custom-functions"></a>カスタム関数の使用方法

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

> [!TIP]
> IoT コネクタデバイスと FHIR の宛先マッピングの編集、テスト、トラブルシューティングについては、 [IoMT コネクタデータマッパー](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) ツールをご覧ください。 Azure portal で IoT コネクタにアップロードするか、 [オープンソースバージョン](https://github.com/microsoft/iomt-fhir) の iot コネクタで使用するためのマッピングをエクスポートします。

この記事では、IoT コネクタの顧客関数の使用方法について説明します。

式言語として **Jのパス** を使用する場合は、多くの関数を使用できます。 Jのパス指定の一部として使用できる関数に加えて、多くのカスタム関数を使用することもできます。 この記事では、正規化プロセス中にデバイスマッピングテンプレートで使用する IoT コネクタ固有のカスタム関数について説明します。

> [!TIP]
> Jのパス関数の詳細については、jのパスの [仕様](https://jmespath.org/specification.html#built-in-functions)を参照してください。

## <a name="function-signature"></a>関数シグネチャ

各関数には、Jの Path 仕様に従うシグネチャがあります。 この署名は次のように表すことができます。

```jmespath
return_type function_name(type $argname)
```

シグネチャは、引数の有効な型を示します。 引数に無効な型が渡されると、エラーが発生します。

> [!NOTE]
> 演算に関連する関数が実行される場合、最終的な結果は C# の [long 型](/dotnet/csharp/language-reference/builtin-types/integral-numeric-types#characteristics-of-the-integral-types)の値内に収まる **必要があり** ます。 の最終的な結果が C# の Long 型の値に収まることができない場合は、数学的なエラーが発生します。

## <a name="exception-handling"></a>例外処理

例外は、イベント処理のライフサイクル内のさまざまな時点で発生する可能性があります。 発生する可能性があるさまざまな点を次に示します。

|アクション|When|テンプレートの解析中に発生する可能性のある例外|結果|
|------|----|-------------------------------------------------|-------|
|**テンプレートの解析**|新しいメッセージのバッチが受信されるたびに、デバイスマッピングテンプレートが読み込まれて解析されます。|テンプレートを解析できませんでした。|システムは、解析が成功するまで、最新のデバイスマッピングテンプレートの再読み込みと解析を試行します。 解析が成功するまで、新しいメッセージは処理されません。|
|**テンプレートの解析**|新しいメッセージのバッチが受信されるたびに、デバイスマッピングテンプレートが読み込まれて解析されます。|式を解析できませんでした。|システムは、解析が成功するまで、最新のデバイスマッピングテンプレートの再読み込みと解析を試行します。 解析が成功するまで、新しいメッセージは処理されません。|
|**関数の実行**|メッセージ内のデータに対して関数が実行されるたびに。|入力データが関数の署名と一致しません。|システムは、そのメッセージの処理を停止します。 メッセージは再試行されません。|
|**関数の実行**|メッセージ内のデータに対して関数が実行されるたびに。|関数の説明に示されているその他の例外。|システムは、そのメッセージの処理を停止します。 メッセージは再試行されません。|

## <a name="mathematical-functions"></a>数学関数

### <a name="add"></a>add

```jmespath
number add(number $left, number $right)
```

左の引数を右側に追加した結果を返します。

例 :

| 種類                       | 正規表現       | 結果 |
|-----------------------------|------------------|--------|
| 該当なし                         | 追加 (10, 10)      | 20     |
| {"left":40, "right":50}   | 追加 (左、右) | 90     |
| {"left": 0, "right":50}    | 追加 (左、右) | 50     |

### <a name="divide"></a>8060

```jmespath
number divide(number $left, number $right)
```

左の引数を右側に分割した結果を返します。

例 :

| 種類                       | 正規表現          | 結果                           |
|-----------------------------|---------------------|----------------------------------|
| 該当なし                         | 除算 (10, 10)      | 1                                |
| {"left":40, "right":50}   | 除算 (左、右) | 0.8                              |
| {"left": 0, "right":50}    | 除算 (左、右) | 0                                |
| {"left":50, "right": 0}    | 除算 (左、右) | 数学的エラー: 0 で除算します |

### <a name="multiply"></a>乗じる

```jmespath
number multiply(number $left, number $right)
```

左辺の引数と右辺を乗算した結果を返します。

例 :

| 種類                       | 正規表現            | 結果 |
|-----------------------------|-----------------------|--------|
| 該当なし                         | 乗算 (10, 10)      | 100    |
| {"left":40, "right":50}   | 乗算 (左、右) | 2000   |
| {"left": 0, "right":50}    | 乗算 (左、右) | 0      |

### <a name="pow"></a>pow

```jmespath
number pow(number $left, number $right)
```

左の引数を右側の指数で累乗した結果を返します。

例 :

| 種類                         | 正規表現       | 結果                     |
|-------------------------------|------------------|----------------------------|
| 該当なし                           | pow (10, 10)      | 10000000000                |
| {"left":40, "right":50}     | pow (左、右) | 数学的エラー: オーバーフロー |
| {"left": 0, "right":50}      | pow (左、右) | 0                          |
| {"left": 100, "right": 0.5}   | pow (左、右) | 10                         |

### <a name="subtract"></a>減算 (subtract)

```jmespath
number subtract(number $left, number $right)
```

左から右の引数を減算した結果を返します。

例 :

| 種類                       | 正規表現            | 結果 |
|-----------------------------|-----------------------|--------|
| 該当なし                         | 減算 (10、10)      | 0      |
| {"left":40, "right":50}   | 減算 (左、右) | -10    |
| {"left": 0, "right":50}    | 減算 (左、右) | -50    |

## <a name="string-functions"></a>文字列関数

### <a name="insertstring"></a>insertString

```jmespath
string insertString(string $original, string $toInsert, number pos)
```

*ToInsert* の値を *元* の文字列に挿入することによって、新しい文字列を生成します。 文字列は、*元* の文字列内の位置 *pos* に挿入されます。

位置引数が0から始まる場合、0の位置は文字列内の最初の文字を参照します。 

指定された位置引数が *元* の長さの範囲外の場合、エラーが発生します。

例 :

| 種類                                                     | 正規表現                                         | 結果              |
|-----------------------------------------------------------|----------------------------------------------------|---------------------|
| {"original": "mple", "toInsert": "sa", "pos": 0}          | insertString (original、toInsert、pos)              | サンプル            |
| {"original": "suess", "toInsert": "cc", "pos": 2}         | insertString (original、toInsert、pos)              | ブランド           |
| {"original": "myString", "toInsert": "!!", "pos": 8}      | insertString (original、toInsert、pos)              | "myString!!"        |
| {"original": "myString", "toInsert": "!!"}                | insertString (original, toInsert, length (original)) | "myString!!"        |
| {"original": "myString", "toInsert": "!!", "pos": 100}    | insertString (original、toInsert、pos)              | エラー: 範囲を超えています |
| {"original": "myString", "toInsert": "!!", "pos":-1}     | insertString (original、toInsert、pos)              | エラー: 範囲を超えています |

## <a name="date-functions"></a>データ関数

### <a name="fromunixtimestamp"></a>fromUnixTimestamp

```jmespath
string fromUnixTimestamp(number $unixTimestampInSeconds)
```

指定された Unix タイムスタンプから [ISO 8061](https://en.wikipedia.org/wiki/ISO_8601) 準拠のタイムスタンプを生成します。 タイムスタンプは、エポックからの秒数として表されます (1 1970 年1月)。

例 :

| 種類                 | 正規表現              | 結果                  |
|-----------------------|-------------------------|-------------------------|
| {"unix": 1625677200} | fromUnixTimestamp (unix)  | "2021-07-07T17:00:00 + 0" |
| {"unix": 0}          | fromUnixTimestamp (unix)  | "1970-01-01T00:00:00 + 0" |

### <a name="fromunixtimestampms"></a>fromUnixTimestampMs

```jmespath
string fromUnixTimestampMs(number $unixTimestampInMs)
```

指定された Unix タイムスタンプから [ISO 8061](https://en.wikipedia.org/wiki/ISO_8601) 準拠のタイムスタンプを生成します。 タイムスタンプは、エポックからのミリ秒数で表されます (1 1970 年1月)。

例 :

| 種類                    | 正規表現                | 結果                  |
|--------------------------|---------------------------|-------------------------|
| {"unix": 1626799080000}  | fromUnixTimestampMs (unix) | "2021-07-20T16:38:00 + 0" |
| {"unix": 0}              | fromUnixTimestampMs (unix) | "1970-01-01T00:00:00 + 0" |

## <a name="next-steps"></a>次の手順

この記事では、IoT コネクタのカスタム関数の使用方法について説明しました。 デバイスマッピングでカスタム関数を使用する方法については、「」を参照してください。

>[!div class="nextstepaction"]
>[デバイスマッピングの使用方法](how-to-use-device-mappings.md)

(FHIR&#174;) [HL7](https://hl7.org/fhir/) の登録商標であり、HL7 のアクセス許可と共に使用されます。
