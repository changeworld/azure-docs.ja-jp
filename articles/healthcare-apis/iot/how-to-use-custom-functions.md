---
title: IoT コネクタのカスタム関数 - Azure Healthcare API
description: この記事では、カスタム関数とデバイス マッピング テンプレートをIoT Connectorする方法について説明します。
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 2bb0d3c17de870017733291bee4680f740afc1ad
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2021
ms.locfileid: "132933840"
---
# <a name="how-to-use-custom-functions"></a>カスタム関数を使用する方法

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

> [!TIP]
> IoT コネクタの Device と FHIR の変換先マッピングの編集、テスト、トラブルシューティングを行う [IoMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) コネクタ データ マッパー ツールを確認してください。 Azure portal で IoT コネクタにアップロードしたり、オープン ソース バージョンの IoT コネクタで使用したりするために [マッピングをエクスポート](https://github.com/microsoft/iomt-fhir) します。

この記事では、IoT コネクタ Customer Functions を使用する方法について説明します。

式言語として **JmesPath を使用する場合、多くの** 関数を使用できます。 JmesPath 仕様の一部として使用できる関数に加え、多くのカスタム関数も使用できます。 この記事では、正規化プロセス中にデバイス マッピング テンプレートで使用する IoT コネクタ固有のカスタム関数について説明します。

> [!TIP]
> JmesPath 関数の詳細については、JmesPath 仕様に関するページを [参照してください](https://jmespath.org/specification.html#built-in-functions)。

## <a name="function-signature"></a>関数シグネチャ

各関数には、JmesPath 仕様に従うシグネチャがあります。 このシグネチャは、次のように表されます。

```jmespath
return_type function_name(type $argname)
```

シグネチャは、引数の有効な型を示します。 引数に無効な型が渡された場合、エラーが発生します。

> [!NOTE]
> 数値演算関連の関数が完了したら、 **結果が** C# の long 値内に収まる [必要](/dotnet/csharp/language-reference/builtin-types/integral-numeric-types#characteristics-of-the-integral-types) があります。 結果が C# の long 値に収まらない場合は、数学的エラーが発生します。

## <a name="exception-handling"></a>例外処理

例外は、イベント処理ライフサイクル内のさまざまなポイントで発生する可能性があります。 発生する可能性があるさまざまなポイントを次に示します。

|アクション|When|テンプレートの解析中に発生する可能性がある例外|結果|
|------|----|-------------------------------------------------|-------|
|**テンプレートの解析**|メッセージの新しいバッチを受信するたび、デバイス マッピング テンプレートが読み込まれ、解析されます。|テンプレートの解析に失敗しました。|解析が成功するまで、最新のデバイス マッピング テンプレートの再読み込みと解析が試行されます。 解析が成功するまで、新しいメッセージは処理されません。|
|**テンプレートの解析**|メッセージの新しいバッチを受信するたび、デバイス マッピング テンプレートが読み込まれ、解析されます。|式を解析できません。|解析が成功するまで、最新のデバイス マッピング テンプレートの再読み込みと解析が試行されます。 解析が成功するまで、新しいメッセージは処理されません。|
|**関数の実行**|メッセージ内のデータに対して関数が実行されるたび。|入力データが関数シグネチャのデータと一致しません。|システムは、そのメッセージの処理を停止します。 メッセージは再試行されません。|
|**関数の実行**|メッセージ内のデータに対して関数が実行されるたび。|関数の説明に記載されているその他の例外。|システムは、そのメッセージの処理を停止します。 メッセージは再試行されません。|

## <a name="mathematical-functions"></a>数学関数

### <a name="add"></a>add

```jmespath
number add(number $left, number $right)
```

左側の引数を右側に追加した結果を返します。

例 :

| 種類                       | 正規表現       | 結果 |
|-----------------------------|------------------|--------|
| 該当なし                         | add(10, 10)      | 20     |
| {"left": 40, "right": 50}   | add(left, right) | 90     |
| {"left": 0, "right": 50}    | add(left, right) | 50     |

### <a name="divide"></a>分割

```jmespath
number divide(number $left, number $right)
```

左引数を右で除算した結果を返します。

例 :

| 種類                       | 正規表現          | 結果                           |
|-----------------------------|---------------------|----------------------------------|
| 該当なし                         | divide(10, 10)      | 1                                |
| {"left": 40, "right": 50}   | divide(left, right) | 0.8                              |
| {"left": 0, "right": 50}    | divide(left, right) | 0                                |
| {"left": 50, "right": 0}    | divide(left, right) | 数学エラー: 0 で除算 |

### <a name="multiply"></a>乗算

```jmespath
number multiply(number $left, number $right)
```

左引数に右を乗算した結果を返します。

例 :

| 種類                       | 正規表現            | 結果 |
|-----------------------------|-----------------------|--------|
| 該当なし                         | multiply(10, 10)      | 100    |
| {"left": 40, "right": 50}   | multiply(left, right) | 2000   |
| {"left": 0, "right": 50}    | multiply(left, right) | 0      |

### <a name="pow"></a>pow

```jmespath
number pow(number $left, number $right)
```

左の引数を右のの値に引き上げる結果を返します。

例 :

| 種類                         | 正規表現       | 結果                     |
|-------------------------------|------------------|----------------------------|
| 該当なし                           | pow(10, 10)      | 10000000000                |
| {"left": 40, "right": 50}     | pow(left, right) | 数学エラー: overflow |
| {"left": 0, "right": 50}      | pow(left, right) | 0                          |
| {"left": 100, "right": 0.5}   | pow(left, right) | 10                         |

### <a name="subtract"></a>減算 (subtract)

```jmespath
number subtract(number $left, number $right)
```

左から右の引数を減算した結果を返します。

例 :

| 種類                       | 正規表現            | 結果 |
|-----------------------------|-----------------------|--------|
| 該当なし                         | subtract(10, 10)      | 0      |
| {"left": 40, "right": 50}   | subtract(left, right) | -10    |
| {"left": 0, "right": 50}    | subtract(left, right) | -50    |

## <a name="string-functions"></a>文字列関数

### <a name="insertstring"></a>insertString

```jmespath
string insertString(string $original, string $toInsert, number pos)
```

*toInsert* の値を元の文字列 に挿入して、新しい文字列を生成 *します*。 文字列は、文字列の元の 内の *位置 pos* に挿入 *されます*。

位置引数が 0 から始る場合、0 の位置は文字列内の最初の文字を参照します。 

指定された位置引数が元の の長さの範囲を外している場合は、エラーが発生します。

例 :

| 種類                                                     | 正規表現                                         | 結果              |
|-----------------------------------------------------------|----------------------------------------------------|---------------------|
| {"original": "mple", "toInsert": "sa", "pos": 0}          | insertString(original, toInsert, pos)              | "sample"            |
| {"original": "suess", "toInsert": "cc", "pos": 2}         | insertString(original, toInsert, pos)              | "success"           |
| {"original": "myString", "toInsert": "!!", "pos": 8}      | insertString(original, toInsert, pos)              | "myString!!"        |
| {"original": "myString", "toInsert": "!!"}                | insertString(original, toInsert, length(original)) | "myString!!"        |
| {"original": "myString", "toInsert": "!!", "pos": 100}    | insertString(original, toInsert, pos)              | error: out of range (エラー: 範囲外) |
| {"original": "myString", "toInsert": "!!", "pos": -1}     | insertString(original, toInsert, pos)              | error: out of range (エラー: 範囲外) |

## <a name="date-functions"></a>データ関数

### <a name="fromunixtimestamp"></a>fromUnixTimestamp

```jmespath
string fromUnixTimestamp(number $unixTimestampInSeconds)
```

指定された Unix [タイムスタンプから ISO 8061](https://en.wikipedia.org/wiki/ISO_8601) 準拠のタイム スタンプを生成します。 タイムスタンプは、エポック (1970 年 1 月 1 日) 以降の秒数として表されます。

例 :

| 種類                 | 正規表現              | 結果                  |
|-----------------------|-------------------------|-------------------------|
| {"unix": 1625677200} | fromUnixTimestamp(unix)  | "2021-07-07T17:00:00+0" |
| {"unix": 0}          | fromUnixTimestamp(unix)  | "1970-01-01T00:00:00+0" |

### <a name="fromunixtimestampms"></a>fromUnixTimestampMs

```jmespath
string fromUnixTimestampMs(number $unixTimestampInMs)
```

指定された Unix [タイムスタンプから ISO 8061](https://en.wikipedia.org/wiki/ISO_8601) 準拠のタイム スタンプを生成します。 タイムスタンプは、エポック (1970 年 1 月 1 日) 以降のミリ秒数で表されます。

例 :

| 種類                    | 正規表現                | 結果                  |
|--------------------------|---------------------------|-------------------------|
| {"unix": 1626799080000}  | fromUnixTimestampMs(unix) | "2021-07-20T16:38:00+0" |
| {"unix": 0}              | fromUnixTimestampMs(unix) | "1970-01-01T00:00:00+0" |

> [!TIP]
> 一般的なエラーと [問題の修正については、IoT](./iot-troubleshoot-guide.md) コネクタのトラブルシューティング ガイドを参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、IoT コネクタのカスタム関数を使用する方法について説明しました。 デバイス マッピングでカスタム関数を使用する方法については、次を参照してください。

>[!div class="nextstepaction"]
>[デバイス マッピングを使用する方法](how-to-use-device-mappings.md)

(FHIR&#174;) は HL7 の商標であり [、HL7](https://hl7.org/fhir/) の許可を得て使用されます。
