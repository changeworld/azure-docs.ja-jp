---
title: Translator Transliterate メソッド
titleSuffix: Azure Cognitive Services
description: Translator Transliterate メソッドを利用し、ある言語のテキストをあるスクリプトから別のスクリプトに変換します。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: lajanuar
ms.openlocfilehash: 11574542116fe90629f84fc572f404a1b42b078b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730195"
---
# <a name="translator-30-transliterate"></a>Translator 3.0:Transliterate

ある言語のテキストを、あるスクリプトから別のスクリプトに変換します。

## <a name="request-url"></a>要求 URL

`POST` 要求の送信先は次のとおりです。

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
```

## <a name="request-parameters"></a>要求パラメーター

クエリ文字列に渡される要求パラメーターを次に示します。

| Query parameter (クエリ パラメーター) | 説明 |
| --- | --- |
| api-version | "*必須のパラメーター*"。<br/>クライアントによって要求される API のバージョン。 値は `3.0` とする必要があります。 |
| language | "*必須のパラメーター*"。<br/>スクリプト間の変換を行うテキストの言語を指定します。 指定可能な言語は、[サポートされている言語](./v3-0-languages.md)をサービスに照会することによって得られる `transliteration` スコープにリストされます。 |
| fromScript | "*必須のパラメーター*"。<br/>入力テキストによって使用されるスクリプトを指定します。 選択した言語で使用可能な入力スクリプトを確認するには、`transliteration`スコープを使用して[サポートされている言語](./v3-0-languages.md)を検索します。 |
| toScript | "*必須のパラメーター*"。<br/>出力スクリプトを指定します。 選択した、入力言語と入力スクリプトの組み合わせに対して使用可能な出力スクリプトを確認するには、`transliteration` スコープを使用して [サポートされている言語](./v3-0-languages.md)を検索します。 |

要求ヘッダーには次のものがあります。

| ヘッダー | 説明 |
| --- | --- |
| 認証ヘッダー | "<em>必須の要求ヘッダー</em>" です。<br/>[認証に使用できるオプション](./v3-0-reference.md#authentication)に関するページをご覧ください。 |
| Content-Type | "*必須の要求ヘッダー*" です。<br/>ペイロードのコンテンツ タイプを指定します。 使用できる値: `application/json` |
| Content-Length | "*必須の要求ヘッダー*" です。<br/>要求本文の長さです。 |
| X-ClientTraceId | *オプション*。<br/>要求を一意に識別する、クライアントで生成された GUID。 クエリ パラメーター `ClientTraceId` を使ってクエリ文字列内にトレース ID を含める場合、このヘッダーを省略できることに注意してください。 |

## <a name="request-body"></a>要求本文

要求の本文は JSON 配列です。 各配列要素は、`Text` という名前の文字列プロパティ (変換する文字列を表す) を持つ JSON オブジェクトです。

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

次の制限事項が適用されます。

* 配列に含めることができる要素は、最大でも 10 個です。
* 配列要素のテキスト値は、スペースも含めて 1,000 文字を超えてはなりません。
* 要求に含めるテキスト全体では、スペースも含めて 5,000 文字を超えてはなりません。

## <a name="response-body"></a>応答本文

正常な応答は、入力配列内の要素ごとに 1 つの結果が含まれる JSON 配列となります。 結果オブジェクトには次のプロパティが含まれています。

  * `text`:入力文字列を出力スクリプトに変換した結果である文字列。
  
  * `script`:出力で使用されるスクリプトを指定する文字列。

JSON 応答の例を次に示します。

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
]
```

## <a name="response-headers"></a>応答ヘッダー

| ヘッダー | 説明 |
| --- | --- |
| X-RequestId | 要求を識別するためにサービスによって生成される値。 トラブルシューティングの目的で使用されます。 |

## <a name="response-status-codes"></a>応答状態コード

要求によって返される可能性のある HTTP 状態コードを次に示します。 

| 状態コード | 説明 |
| --- | --- |
| 200 | 正常終了しました。 |
| 400 | クエリ パラメーターの 1 つが欠落しているか無効です。 再試行する前に要求パラメーターを修正してください。 |
| 401 | 要求を認証できませんでした。 資格情報が指定され、有効であることを確認してください。 |
| 403 | 要求が承認されていません。 詳細なエラー メッセージを確認してください。 これは、多くの場合、試用版サブスクリプションで提供されるすべての無料翻訳が使い果たされたことを示します。 |
| 429 | クライアントが要求の制限を超えたため、サーバーは要求を拒否しました。 |
| 500 | 予期しないエラーが発生しました。 エラーが解決しない場合は、エラー発生の日時、応答ヘッダー `X-RequestId` からの要求識別子、要求ヘッダー `X-ClientTraceId` からのクライアント識別子を添えてその旨をご報告ください。 |
| 503 | サーバーが一時的に使用できません。 要求をやり直してください。 エラーが解決しない場合は、エラー発生の日時、応答ヘッダー `X-RequestId` からの要求識別子、要求ヘッダー `X-ClientTraceId` からのクライアント識別子を添えてその旨をご報告ください。 |

エラーが発生した場合、要求から JSON エラー応答も返されます。 このエラーコードは 3 桁の HTTP ステータス コードの後に､エラーをさらに分類するための 3 桁の数字を続けた 6 桁の数字です｡ 一般的なエラー コードは、[v3 Translator のリファレンス ページ](./v3-0-reference.md#errors)で確認できます。 

## <a name="examples"></a>例

次の例では、2 つの日本語文字列をローマ字化された日本語に変換する方法を示します。

この例の要求に対する JSON ペイロードを次に示します。

```json
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Unicode 文字をサポートしていないコマンドライン ウィンドウで cURL を使用する場合は、次の JSON ペイロードを取得して、`request.txt` という名前のファイルに保存します。 必ずファイルは `UTF-8` エンコードで保存してください。

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```
