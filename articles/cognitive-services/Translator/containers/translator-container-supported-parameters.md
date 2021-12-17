---
title: 'コンテナー: Translator の Translate メソッド'
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services のテキストを翻訳するための Translator のコンテナー Translate メソッド用のパラメーター、ヘッダー、および本文のメッセージを理解します。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/12/2021
ms.author: lajanuar
ms.openlocfilehash: b2c13a98c6b689594628a258faf18f8f9c0d8c69
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2021
ms.locfileid: "112539558"
---
# <a name="container-translator-translate-method-preview"></a>コンテナー: Translator の Translate メソッド (プレビュー)

テキストを翻訳します。

## <a name="request-url"></a>要求 URL

`POST` 要求の送信先は次のとおりです。

```HTTP
http://localhost:{port}/translate?api-version=3.0
```

例: http://<span></span>localhost:5000/translate?api-version=3.0

## <a name="request-parameters"></a>要求パラメーター

クエリ文字列に渡される要求パラメーターを次に示します。

### <a name="required-parameters"></a>必須のパラメーター

| Query parameter (クエリ パラメーター) | 説明 |
| --- | --- |
| api-version | "_必須のパラメーター_"。  <br>クライアントによって要求される API のバージョン。 値は `3.0` とする必要があります。 |
| from | "_必須のパラメーター_"。  <br>入力テキストの言語を指定します。 `translation` スコープを使用して[サポートされている言語](../reference/v3-0-languages.md)を検索することにより、翻訳することができるソース言語を確認します。|
| to  | "_必須のパラメーター_"。  <br>出力テキストの言語を指定します。 ターゲット言語は、`translation` スコープに含まれている[サポートされている言語](../reference/v3-0-languages.md)のいずれかとする必要があります。 たとえば、ドイツ語に翻訳するには `to=de` を使用します。  <br>クエリ文字列内でパラメーターを繰り返すことにより、同時に複数の言語に翻訳することができます。 たとえば、ドイツ語とイタリア語に翻訳するには、`to=de&to=it` を使用します。 |

### <a name="optional-parameters"></a>省略可能なパラメーター

| Query parameter (クエリ パラメーター) | 説明 |
| --- | --- |
| textType | "_省略可能なパラメーター_"。  <br>翻訳するテキストがプレーン テキストか、それとも HTML テキストかを定義します。 HTML の場合は、適切な形式の完全な要素である必要があります。 指定できる値は `plain` (既定値) または `html` です。 |
| includeSentenceLength | "_省略可能なパラメーター_"。  <br>入力テキストと翻訳済みテキストに対して文の境界を含めるかどうかを指定します。 指定できる値は `true` または `false` (既定値) です。 |

要求ヘッダーには次のものがあります。

| ヘッダー | 説明 |
| --- | --- |
| 認証ヘッダー | "_必須の要求ヘッダー_" です。  <br>[認証に使用できるオプション](../reference/v3-0-reference.md#authentication)に関するページをご覧ください。 |
| Content-Type | "_必須の要求ヘッダー_" です。  <br>ペイロードのコンテンツ タイプを指定します。  <br>指定できる値は `application/json; charset=UTF-8` です。 |
| Content-Length | "_必須の要求ヘッダー_" です。  <br>要求本文の長さです。 |
| X-ClientTraceId | _オプション_。  <br>要求を一意に識別する、クライアントで生成された GUID。 `ClientTraceId` という名前のクエリ パラメーターを使用してクエリ文字列内にトレース ID を含める場合、このヘッダーは省略できます。 |

## <a name="request-body"></a>要求本文

要求の本文は JSON 配列です。 各配列要素は、`Text` という名前の文字列プロパティ (翻訳するテキストを表す) を持つ JSON オブジェクトです。

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

次の制限事項が適用されます。

* 配列に含めることができる要素は、最大でも 100 個です。
* 要求に含めるテキスト全体では、スペースも含めて 10,000 文字を超えてはなりません。

## <a name="response-body"></a>応答本文

正常な応答は、入力配列内の文字列ごとに 1 つの結果が含まれる JSON 配列となります。 結果オブジェクトには次のプロパティが含まれています。

* `translations`:翻訳結果の配列です。 配列のサイズは、`to` クエリ パラメーターを通して指定されたターゲット言語の数に一致します。 配列内の各要素は次のとおりです。

* `to`:ターゲット言語の言語コードを表す文字列です。

* `text`:翻訳済みテキストを提供する文字列です。

* `sentLen`:入力テキストと出力テキスト内で文の境界を返すオブジェクトです。

* `srcSentLen`:入力テキスト内の文の長さを表す整数配列です。 配列の長さは文の数であり、値は各文の長さです。

* `transSentLen`:翻訳済みテキスト内の文の長さを表す整数配列です。 配列の長さは文の数であり、値は各文の長さです。

    文の境界は、要求パラメーター `includeSentenceLength` が `true` の場合にのみ含められます。

  * `sourceText`:`text` という名前の 1 つの文字列プロパティを持つオブジェクトです。これにより、ソース言語の既定のスクリプトで入力テキストが提供されます。 `sourceText` プロパティは、言語の通常のスクリプトではないスクリプトで入力が表されている場合にのみ存在します。 たとえば、入力がラテン文字で記述されたアラビア語であるならば、`sourceText.text` は同じアラビア語のテキストをアラビア文字に変換したものとなります。

JSON 応答の例については、「[例](#examples)」セクションを参照してください。

## <a name="response-headers"></a>応答ヘッダー

| ヘッダー | 説明 |
| --- | --- |
| X-RequestId | 要求を識別するためにサービスによって生成される値。 トラブルシューティングの目的で使用されます。 |
| X-MT-System | 翻訳するように要求された 'to' 言語への翻訳を実行するために使用されたシステムの種類を示します。 値は、文字列のコンマ区切りの一覧です。 各文字列は種類を示します。  </br></br>&FilledVerySmallSquare; Custom - 要求にはカスタム システムが含まれ、翻訳中に少なくとも 1 つのカスタム システムが使用されています。</br>&FilledVerySmallSquare; Team - それ以外のすべての要求 |

## <a name="response-status-codes"></a>応答状態コード

エラーが発生した場合は、要求の結果として JSON エラー応答も返されます。 このエラーコードは 3 桁の HTTP ステータス コードの後に､エラーをさらに分類するための 3 桁の数字を続けた 6 桁の数字です｡ 一般的なエラー コードは、[v3 Translator のリファレンス ページ](../reference/v3-0-reference.md#errors)で確認できます。

## <a name="examples"></a>例

### <a name="translate-a-single-input"></a>単一の入力を翻訳する

この例では、1 つの文を英語から簡体中国語に翻訳する方法を示します。

```curl
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

応答本文を次に示します。

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

`translations` 配列には 1 つの要素が含まれており、これによって入力内の単一のテキストの翻訳が提供されます。

### <a name="translate-multiple-pieces-of-text"></a>複数のテキストを変換する

一度に複数の文字列を翻訳するには、要求本文に文字列の配列を指定するだけです。

```curl
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

応答には、テキストの全箇所の翻訳が要求とまったく同じ順序で含まれます。
応答本文を次に示します。

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>複数の言語に変換する

この例では、1 つの要求で同じ入力を複数の言語に翻訳する方法を示します。

```curl
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

応答本文を次に示します。

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>マークアップを含むコンテンツの翻訳および翻訳対象の決定

HTML ページからのコンテンツや XML ドキュメントからのコンテンツなど、マークアップを含むコンテンツを翻訳するのが一般的です。 タグ付きのコンテンツを翻訳する場合は、クエリ パラメーター `textType=html` を含めます。 さらに、特定のコンテンツを翻訳から除外すると便利な場合があります。 属性 `class=notranslate` を使用すると、元の言語のまま残す必要があるコンテンツを指定できます。 次の例では、1 番目の `div` 要素内のコンテンツは翻訳されませんが、2 番目の `div` 要素内のコンテンツは翻訳されます。

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

要求の例を次に示します。

```curl
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

応答は次のとおりです。

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>動的ディクショナリを使用して翻訳する

単語や語句に適用する翻訳があらかじめわかっている場合は、それを要求内でマークアップとして指定することができます。 動的ディクショナリは、人名や製品名などの固有名詞に対してのみ安全に使用できます。

指定するマークアップでは、次の構文を使用します。

```
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

たとえば、"The word wordomatic is a dictionary entry." という英文を考えてみます。 単語 _wordomatic_ を翻訳内でそのまま使用するには、次の要求を送信します。

```
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

結果は次のとおりです。

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

この機能は、`textType=text` または `textType=html` の場合も同じように動作します。 この機能は慎重に使用する必要があります。 翻訳をカスタマイズするには、Custom Translator を使用するのが適切で望ましい方法です。 Custom Translator では、コンテキストおよび統計的確率を最大限に活用します。 コンテキスト内の単語または語句を表示するトレーニング データを作成する余裕がある場合、非常に良い結果が得られます。 [Custom Translator の詳細については、こちらを参照してください](../customization.md)。

## <a name="request-limits"></a>要求の制限

各翻訳要求は、翻訳先のすべての対象言語で合計 10,000 文字までに制限されています。 たとえば、3,000 文字を 3 つの異なる言語に翻訳する翻訳要求を送信すると、要求のサイズは 3,000 x 3 = 9,000 文字となり、要求の制限が満たされます。 要求の数ではなく、文字単位で課金されます。 送信する要求を短くすることをお勧めします。

次の表には、Translator の **翻訳** 操作に関する配列要素および文字の制限が一覧表示されています。

| 操作 | 配列要素の最大サイズ | 配列要素の最大数 | 最大要求サイズ (文字数) |
|:----|:----|:----|:----|
| translate | 10,000 | 100 | 10,000 |