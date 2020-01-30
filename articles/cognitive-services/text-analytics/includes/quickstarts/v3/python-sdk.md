---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 01/23/2019
ms.author: aahi
ms.openlocfilehash: b71c66c9025ea76d9f99f27537c0d4239ce93fdc
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717243"
---
<a name="HOLTop"></a>

[リファレンス ドキュメント](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b1/azure.ai.textanalytics.html) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [パッケージ (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

> [!NOTE]
> * このクイックスタートでは、Text Analytics クライアント ライブラリのバージョン `3.0-preview` を使用します。これには、強化された[感情分析](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)および[名前付きエンティティの認識 (NER)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) のパブリック プレビューが含まれています。
> * この記事のコードでは、単純化するために、同期メソッドと、セキュリティで保護されていない資格情報の格納を使用しています。 運用環境のシナリオでは、パフォーマンスとスケーラビリティを確保するために、バッチ処理された非同期メソッドを使用することをお勧めします。 たとえば、`azure.ai.textanalytics` 名前空間から `analyze_sentiment()` を呼び出すのではなく、`azure.ai.textanalytics.aio` 名前空間からクライアントをインポートして `analyze_sentiment()` を呼び出します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>設定

### <a name="create-a-text-analytics-azure-resource"></a>Text Analytics Azure リソースを作成する

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

Python をインストールしたら、次を使用してクライアント ライブラリをインストールすることができます。

```console
pip install azure-ai-textanalytics
```

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

新しい Python ファイルを作成し、リソースの Azure エンドポイントとサブスクリプション キー用の変数を作成します。

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>オブジェクト モデル

Text Analytics クライアントは、キーを使用して Azure に対して認証を行う `TextAnalyticsClient` オブジェクトです。 このクライアントには、テキストをバッチとして分析するためのメソッドがいくつか備わっています。 このクイックスタートでは、関数のコレクションを使用して、1 つのドキュメントをすばやく送信します。

バッチ処理時に、テキストは、`documents` (使用したメソッドに応じて `id`、`text`、および `language` の各属性の組み合わせを保持する `dictionary` オブジェクト) の一覧として API に送信されます。 `text` 属性には、分析対象のテキストが元の `language` で格納され、`id` には任意の値を指定できます。 1 つのドキュメントを処理する場合は、以下の例で見られるように、`text` 入力のみが必要です。  

応答オブジェクトは、各ドキュメントの分析情報を格納するリストです。 

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Python 用 Text Analytics クライアント ライブラリを使用して以下のタスクを実行する方法が示されています。

* [感情分析](#sentiment-analysis) (パブリック プレビュー)
* [言語検出](#language-detection)
* [名前付きエンティティの認識](#named-entity-recognition-public-preview) (パブリック プレビュー)
* [名前付きエンティティの認識 - 個人情報](#named-entity-recognition---personal-information-public-preview) (パブリック プレビュー)
* [エンティティ リンク設定](#entity-linking)
* [キー フレーズ抽出](#key-phrase-extraction)

## <a name="sentiment-analysis"></a>センチメント分析

> [!NOTE]
> 次のコードは、感情分析 v3 用です。この機能は、パブリック プレビュー段階です。

エンドポイントとキーを引数として受け取り、`single_analyze_sentiment()` 関数を呼び出す、`sentiment_analysis_example()` という名前の新しい関数を作成します。 返される応答オブジェクトには、入力ドキュメント全体のセンチメント ラベルとスコアが含まれます。また、各文の感情分析も含まれます。


```python
from azure.ai.textanalytics import single_analyze_sentiment

def sentiment_analysis_example(endpoint, key):

    document = "I had the best day of my life. I wish you were there with me."

    response = single_analyze_sentiment(endpoint=endpoint, credential=key, input_text=document)
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.3f}; neutral={1:.3f}; negative={2:.3f} \n".format(
        response.document_scores.positive,
        response.document_scores.neutral,
        response.document_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("[Offset: {}, Length: {}]".format(sentence.offset, sentence.length))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.3f}\nNeutral={1:.3f}\nNegative={2:.3f}\n".format(
            sentence.sentence_scores.positive,
            sentence.sentence_scores.neutral,
            sentence.sentence_scores.negative,
        ))

            
sentiment_analysis_example(endpoint, key)
```

### <a name="output"></a>Output

```console
Document Sentiment: positive
Overall scores: positive=0.999; neutral=0.001; negative=0.000 

[Offset: 0, Length: 30]
Sentence 1 sentiment: positive
Sentence score:
positive=0.999
neutral=0.001
negative=0.000

[Offset: 31, Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
positive=0.212
neutral=0.771
negative=0.017
```

## <a name="language-detection"></a>言語検出

エンドポイントとキーを引数として受け取り、`single_detect_languages()` 関数を呼び出す、`language_detection_example()` という名前の新しい関数を作成します。 返される応答オブジェクトには、成功した場合は検出された言語が `detected_languages` に含まれ、そうでない場合は `error` が含まれます。

> [!Tip]
> 場合によっては、入力に基づいて言語を明確に区別するのが困難なことがあります。 `country_hint` パラメーターを使用して、2 文字の国番号を指定できます。 API の既定では、既定の countryHint として "US" が使用されます。この動作を削除するには、この値を空の文字列に設定して (`country_hint : ""`)、このパラメーターをリセットします。 

```python
from azure.ai.textanalytics import single_detect_language

def language_detection_example(endpoint, key):
    try:
        document = "Ce document est rédigé en Français."
        response = single_detect_language(endpoint=endpoint, credential=key, input_text= document)
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(endpoint, key)
```


### <a name="output"></a>Output

```console
Language:  French
```

## <a name="named-entity-recognition-public-preview"></a>名前付きエンティティの認識 (パブリック プレビュー)

> [!NOTE]
> 次のコードは、名前付きエンティティの認識 v3 用です。この機能は、パブリック プレビュー段階です。

エンドポイントとキーを引数として受け取り、`single_recognize_entities()` 関数を呼び出して、結果を反復処理する、`entity_recognition_example` という名前の新しい関数を作成します。 返される応答オブジェクトには、成功した場合は検出されたエンティティの一覧が `entity` に含まれ、そうでない場合は `error` が含まれます。 検出されたエンティティごとに、タイプとサブタイプ (ある場合) を出力します。

```python
from azure.ai.textanalytics import single_recognize_entities

def entity_recognition_example(endpoint, key):

    try:
        document = "I had a wonderful trip to Seattle last week."
        result = single_recognize_entities(endpoint=endpoint, credential=key, input_text= document)
        
        print("Named Entities:\n")
        for entity in result.entities:
                print("\tText: \t", entity.text, "\tType: \t", entity.type, "\tSubType: \t", entity.subtype,
                      "\n\tOffset: \t", entity.offset, "\tLength: \t", entity.offset, 
                      "\tConfidence Score: \t", round(entity.score, 3), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(endpoint, key)
```

### <a name="output"></a>Output

```console
Named Entities:

    Text:    Seattle    Type:    Location   SubType:     None 
    Offset:      26     Length:      26     Confidence Score:    0.806 

    Text:    last week  Type:    DateTime   SubType:     DateRange 
    Offset:      34     Length:      34     Confidence Score:    0.8 
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>名前付きエンティティの認識 - 個人情報 (パブリック プレビュー)

> [!NOTE]
> 次のコードは、名前付きエンティティの認識 v3 を使用して個人情報を検出するためのものです。この機能は、パブリック プレビュー段階です。

エンドポイントとキーを引数として受け取り、`single_recognize_pii_entities()` 関数を呼び出して結果を取得する、`entity_pii_example()` という名前の新しい関数を作成します。 結果を反復処理し、エンティティを出力します。

```python
from azure.ai.textanalytics import single_recognize_pii_entities

def entity_pii_example(endpoint, key):

        document = "Insurance policy for SSN on file 123-12-1234 is here by approved."


        result = single_recognize_pii_entities(endpoint=endpoint, credential=key, input_text= document)
        
        print("Personally Identifiable Information Entities: ")
        for entity in result.entities:
            print("\tText: ",entity.text,"\tType: ", entity.type,"\tSub-Type: ", entity.subtype)
            print("\t\tOffset: ", entity.offset, "\tLength: ", entity.length, "\tScore: {0:.3f}".format(entity.score), "\n")
        
entity_pii_example(endpoint, key)
```

### <a name="output"></a>Output

```console
Personally Identifiable Information Entities: 
    Text:  123-12-1234  Type:  U.S. Social Security Number (SSN)    Sub-Type:  
        Offset:  33     Length:  11     Score: 0.850 
```

## <a name="entity-linking"></a>Entity Linking

エンドポイントとキーを引数として受け取り、`single_recognize_linked_entities()` 関数を呼び出して、結果を反復処理する、`entity_linking_example()` という名前の新しい関数を作成します。 返される応答オブジェクトには、成功した場合は検出されたエンティティの一覧が `entities` に含まれ、そうでない場合は `error` が含まれます。 リンクされたエンティティは一意に識別されるため、同じエンティティの出現は、`match` オブジェクトの一覧として `entity` オブジェクトの下にグループ化されます。

```python
from azure.ai.textanalytics import single_recognize_linked_entities

def entity_linking_example(endpoint, key):

    try:
        document = """Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""
        result = single_recognize_linked_entities(endpoint=endpoint, credential=key, input_text= document)

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tScore: {0:.3f}".format(match.score), "\tOffset: ", match.offset, 
                      "\tLength: {}\n".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(endpoint, key)
```

### <a name="output"></a>Output

```console
Linked Entities:

    Name:  Altair 8800  Id:  Altair 8800    Url:  https://en.wikipedia.org/wiki/Altair_8800 
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Score: 0.777    Offset:  116    Length: 11

    Name:  Bill Gates   Id:  Bill Gates     Url:  https://en.wikipedia.org/wiki/Bill_Gates 
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Score: 0.555    Offset:  25     Length: 10

        Text: Gates
        Score: 0.555    Offset:  161    Length: 5

    Name:  Paul Allen   Id:  Paul Allen     Url:  https://en.wikipedia.org/wiki/Paul_Allen 
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Score: 0.533    Offset:  40     Length: 10

    Name:  Microsoft    Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft 
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Score: 0.469    Offset:  0  Length: 9

        Text: Microsoft
        Score: 0.469    Offset:  150    Length: 9

    Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4 
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Score: 0.248    Offset:  54     Length: 7

    Name:  BASIC    Id:  BASIC  Url:  https://en.wikipedia.org/wiki/BASIC 
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Score: 0.281    Offset:  89     Length: 5
```

## <a name="key-phrase-extraction"></a>キー フレーズの抽出

エンドポイントとキーを引数として受け取り、`single_extract_key_phrases()` 関数を呼び出す、`key_phrase_extraction_example()` という名前の新しい関数を作成します。 成功した場合は `key_phrases` 内で検出されたキー フレーズのリストが結果に含まれ、失敗した場合は `error` が含まれます。 検出されたキー フレーズを出力します。

```python
from azure.ai.textanalytics import single_extract_key_phrases

def key_phrase_extraction_example(endpoint, key):

    try:
        document = "My cat might need to see a veterinarian."

        response = single_extract_key_phrases(endpoint=endpoint, credential=key, input_text= document)

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(endpoint, key)
```


### <a name="output"></a>Output

```console
    Key Phrases:
         cat
         veterinarian
```
