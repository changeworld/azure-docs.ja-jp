---
title: Bing Spell Check API の概要 - Azure Cognitive Services | Microsoft Docs
description: Bing Spell Check API では、コンテキストに応じたスペル チェックを行うために機械学習と統計的機械翻訳を使用しています。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.assetid: 64ABDFD4-0118-4B6C-A592-68E5EDDB8491
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: overview
ms.date: 05/03/2018
ms.author: nolachar
ms.openlocfilehash: 15c5f7eeb7d94d7e80533ee1fd12e33fa3bcd134
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "43114295"
---
# <a name="what-is-bing-spell-check-api"></a>Bing Spell Check API とは

Bing Spell Check API を使用して、コンテキストに応じた文法とスペルのチェックを実行できます。

通常のスペル チェックと Bing の第 3 世代のスペル チェックでは、何が違うのでしょうか。 現在のスペル チェックは、定期的に更新されて展開されるディクショナリ ベースのルール セットの検証に依存しています。 つまり、スペル チェック機能はそれをサポートしているディクショナリの充実度と、ディクショナリをサポートしている編集スタッフの有能さに依存しています。 この種のスペル チェック機能には、Microsoft Word や他のワード プロセッサーがあります。

これに対し、Bing では、機械学習と統計的機械翻訳を活用して、常に進化する高度にコンテキストに応じたアルゴリズムを動的にトレーニングする Web ベースのスペル チェック機能を開発しています。 このスペル チェック機能は、Web 検索とドキュメントの大規模なコーパスに基づいています。

このスペル チェック機能では、任意の言語処理シナリオを処理できます。

- スラングやくだけた言葉遣いを認識します。
- よくある氏名の書き間違いをコンテキスト内で認識します。
- 単語分割に関する問題を 1 つのフラグで修正します。
- コンテキストに応じて異形同音異義語を修正し、その他の気付きにくい間違いを修正できます。
- 新しいブランド、デジタル エンターテイメント、および一般的な表現が出現したときに、それらをサポートします。
- 異形同音異義語 (例: “see” と “sea”) を認識します。

## <a name="spell-check-modes"></a>スペル チェック モード

API は、2 つの校正モード (`Proof` と `Spell`) をサポートします。  [こちら](https://azure.microsoft.com/en-us/services/cognitive-services/spell-check/)の例を参照してください。
### <a name="proof---for-documents-scenario"></a>Proof - ドキュメント シナリオ用
既定のモードは `Proof` です。 `Proof` スペル チェック モードでは最も包括的なチェックが行われ、大文字と小文字の修正、基本的な句読点の調整、ドキュメントの作成を支援するその他の機能が提供されます。 ただし、これは、en-US (英語 (米国))、es-ES (スペイン語)、pt-BR (プルとガル語) 市場でのみ利用できます (注: スペイン語とポルトガル語はベータ版です)。 その他のすべての市場では、mode クエリ パラメーターを Spell に設定してください。 
<br /><br/>**注:** クエリ テキストの長さが 4,096 を超える場合は 4.096 文字に切り捨てられた後で処理されます。 
### <a name="spell----for-web-searchesqueries-scenario"></a>Spell - Web 検索/クエリ シナリオ用
`Spell` は、より良い検索結果を返すためにもっと積極的に機能します。 `Spell` モードは、ほとんどのスペル ミスを検出しますが、`Proof` ではキャッチされる一部の文法エラーは検出されません (大文字小文字エラーや単語の繰り返しなど)。
<br /></br>**注:** サポートされているクエリの最大長を次に示します。 クエリがこの境界を超えている場合は、クエリは変更されないことを示す結果が表示されます。
<ul><li>言語コード en、de、es、fr、pl、pt、sv、ru、nl、nb、tr-tr、it、zh、ko では 130 文字。 </li>
<li>それ以外は 65 文字。</li></ul>

## <a name="market-setting"></a>市場の設定
要求 URL のクエリ パラメーターに市場を指定する必要があります。指定がない場合、スペル チェックは、IP アドレスに基づく既定の市場になります。


## <a name="post-vs-get"></a>POST と GET

この API は、HTTP POST と HTTP GET のどちらもサポートします。 どちらを使用するかは、校正する予定のテキストの長さによって決まります。 文字列が常に 1,500 文字未満の場合は、GET を使用します。 最大 10,000 文字の文字列をサポートする場合は、POST を使用します。 テキスト文字列には、任意の有効な UTF-8 文字を含めることができます。

次の例は、テキスト文字列のスペルと文法のチェックを要求する POST 要求を示しています。 この例には、すべてを網羅するために [mode](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#mode) クエリ パラメーターが含まれています (`mode` の既定値は Proof であるため、除外される可能性があります)。 [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#text) クエリ パラメーターには、校正対象の文字列が含まれます。
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

HTTP GET を使用する場合は、URL のクエリ文字列に `text` クエリ パラメーターを含めます。
  
前述の要求への応答は次のようになります。 応答には、[SpellCheck](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#spellcheck) オブジェクトが含まれます。 
  
```  
{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  
  
API が [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#text) 文字列内で検出したスペルと文法のエラーが、[flaggedTokens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#flaggedtokens) フィールドに一覧表示されます。 `token` フィールドには、置換される単語が含まれます。 `offset` フィールドの 0 から始まるオフセットを使用して、`text` 文字列内のトークンを検出します。 その後、その場所にある単語を`suggestion` フィールド内の単語に置き換えます。 

`type` フィールドが RepeatedToken の場合でも、トークンを `suggestion` に置き換えますが、おそらく末尾のスペースを削除する必要があります。

## <a name="throttling-requests"></a>スロットル リクエスト

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>次の手順

実際に要求を送信してみるには、[最初の要求を行う](quickstarts/csharp.md)に関するページを参照してください。

[Bing Spell Check API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)を理解します。 このリファレンスには、検索結果を要求する際に使用するエンドポイント、ヘッダー、クエリ パラメーターの一覧と、応答オブジェクトの定義が記載されています。 

結果の使用に関するルールを逸脱しないようにするために、[Bing の使用上および表示上の要件](./useanddisplayrequirements.md)に関するページを必ずお読みください。
