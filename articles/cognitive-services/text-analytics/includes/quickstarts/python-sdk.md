---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 09/05/2019
ms.author: aahi
ms.openlocfilehash: 6d4076563ac803c72f3749399bed6aaa235a0343
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795673"
---
<a name="HOLTop"></a>


[リファレンス ドキュメント](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [パッケージ (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [サンプル](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>設定

### <a name="create-a-text-analytics-azure-resource"></a>Text Analytics Azure リソースを作成する

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

Python をインストールしたら、次を使用してクライアント ライブラリをインストールすることができます。

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

新しい Python ファイルを作成して次のライブラリをインポートします。

[!code-python[import statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

リソースの Azure エンドポイントとサブスクリプション キー用の変数を作成します。 これらの値は、環境変数 TEXT_ANALYTICS_SUBSCRIPTION_KEY および TEXT_ANALYTICS_ENDPOINT から取得します。 アプリケーションの編集開始後にこれらの環境変数を作成した場合は、その変数へのアクセスに使用しているエディター、IDE、またはシェルを閉じて、もう一度開く必要があります。

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

[!code-python[endpoint and key variables](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=initialVars)]

## <a name="object-model"></a>オブジェクト モデル

Text Analytics クライアントは、ご利用のキーを使用して Azure に対して認証を行う [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) オブジェクトです。 このクライアントには、テキストを単一の文字列として、またはバッチとして分析するためのメソッドがいくつか備わっています。 

テキストは、`documents` (使用したメソッドに応じて `id`、`text`、`language` の各属性の組み合わせを保持する `dictionary` オブジェクト) のリストとして API に送信されます。 `text` 属性には、分析対象のテキストが元の `language` で格納され、`id` には任意の値を指定できます。 

応答オブジェクトは、各ドキュメントの分析情報を格納するリストです。 

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Python 用 Text Analytics クライアント ライブラリを使用して次のことを実行する方法が示されています。

* [クライアントを認証する](#authenticate-the-client)
* [感情分析](#sentiment-analysis)
* [言語検出](#language-detection)
* [エンティティの認識](#entity-recognition)
* [キー フレーズ抽出](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>クライアントを認証する

エンドポイントと、キーを含む `CognitiveServicesCredentials` オブジェクトを使用して、新しい [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) を作成します。

[!code-python[client authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

## <a name="sentiment-analysis"></a>センチメント分析

クライアント オブジェクトを認証し、[sentiment()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 関数を呼び出します。 結果を反復処理し、各ドキュメントの ID とセンチメント スコアを出力します。 0 に近いスコアは否定的センチメント、1 に近いスコアは肯定的センチメントを示します。

[!code-python[sentiment analysis](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=sentimentAnalysis)]

### <a name="output"></a>Output

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>言語検出

以前に作成したクライアントを使用して、[detect_language()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) を呼び出して結果を取得します。 結果を反復処理し、各ドキュメントの ID と最初に返された言語を印刷します。

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>Output

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>エンティティの認識

以前に作成したクライアントを使用して、[entities()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 関数を呼び出して結果を取得します。 続いて、結果を反復処理し、各ドキュメントの ID とそれに含まれているエンティティを印刷します。

[!code-python[Entity recognition](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=entityRecognition)]

### <a name="output"></a>Output

```console
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

## <a name="key-phrase-extraction"></a>キー フレーズの抽出

以前に作成したクライアントを使用して、[key_phrases()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 関数を呼び出して結果を取得します。 続いて、結果を反復処理し、各ドキュメントの ID とそれに含まれているキー フレーズを印刷します。

[!code-python[key phrase extraction](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=keyPhrases)]


### <a name="output"></a>Output

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```
