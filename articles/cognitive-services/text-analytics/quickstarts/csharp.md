---
title: クイック スタート:Azure SDK for .NET と C# を使用して Text Analytics サービスを呼び出す
titleSuffix: Azure Cognitive Services
description: Text Analytics サービスと C# の使用を開始するのに役立つ情報およびサンプル コード。
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: assafi
ms.openlocfilehash: 6bd3907392dad626c1eeb1823c929f1a35d544dd
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697670"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>クイック スタート:.NET SDK と C# を使用して Text Analytics サービスを呼び出す
<a name="HOLTop"></a>

このクイック スタートは、Azure SDK for .NET と C# を使用して言語の分析を開始するのに役立ちます。 [Text Analytics](//go.microsoft.com/fwlink/?LinkID=759711) REST API はほとんどのプログラミング言語と互換性がありますが、この SDK は、サービスをアプリケーションに統合するための簡単な方法を提供します。

> [!NOTE]
> この記事のデモ コードでは、簡単にするため、Text Analytics .NET SDK の同期メソッドを使用します。 ただし、運用環境シナリオの実際のアプリケーションでは、スケーラビリティと応答性を維持するため、バッチ処理される非同期メソッドを使用することをお勧めします。 たとえば、`Sentiment` の代わりに、`SentimentBatchAsync` を使用できます。
>
> この例のバッチ処理される非同期バージョンは、[GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics) にあります。

技術的な詳細については、SDK for .NET の [Text Analytics のリファレンス](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet)を参照してください。

## <a name="prerequisites"></a>前提条件

* Visual Studio 2017 以降の任意のエディション
* Text Analytics [SDK for .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

## <a name="create-the-visual-studio-solution-and-install-the-sdk"></a>Visual Studio ソリューションの作成と SDK のインストール

1. 新しいコンソール アプリ (.NET Core) プロジェクトを作成します。 [Visual Studio にアクセスします](https://visualstudio.microsoft.com/vs/)。
1. ソリューションを右クリックし、 **[ソリューションの NuGet パッケージの管理]** を選択します。
1. **[参照]** タブを選択します。**Microsoft.Azure.CognitiveServices.Language.TextAnalytics** を検索します。

## <a name="authenticate-your-credentials"></a>資格情報の認証

1. メイン クラス ファイル (既定では Program.cs) に次の `using` ステートメントを追加します。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Net.Http;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Rest;
    ```

2. 資格情報を格納し、要求ごとにそれらを追加するための新しい `ApiKeyServiceClientCredentials` クラスを作成します。

    ```csharp
    class ApiKeyServiceClientCredentials : ServiceClientCredentials
    {
        private readonly string apiKey;

        public ApiKeyServiceClientCredentials(string apiKey)
        {
            this.apiKey = apiKey;
        }

        public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            if (request == null)
            {
                throw new ArgumentNullException("request");
            }
            request.Headers.Add("Ocp-Apim-Subscription-Key", this.apiKey);
            return base.ProcessHttpRequestAsync(request, cancellationToken);
        }
    }
    ```

3. `Program` クラスを更新します。 定数メンバーを Text Analytics API キー用に 1 つと、サービス エンドポイント用にもう 1 つ追加します。 Text Analytics リソースには、必ず適切な Azure の場所を使用してください。

    ```csharp
    //Enter your Text Analytics (TA) API Key (available in Azure Portal -> your TA resource -> Keys)
    private const string ApiKey = "enter-your-textanalytics-api-key-here";
    //You can get the resource location from Azure Portal -> your TA resource -> Overview
    private const string Endpoint = "enter-your-service-endpoint-here"; // For example: "https://<your-location>.api.cognitive.microsoft.com";
    ```

> [!Tip]
> 運用システムでのシークレットのセキュリティを向上させるために、[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net) を使用することをお勧めします。
>

## <a name="create-a-text-analytics-client"></a>Text Analytics クライアントの作成

プロジェクトの `Main` 関数で、起動するサンプル メソッドを呼び出します。 定義した `Endpoint` および `ApiKey` パラメーターを渡します。

```csharp
    public static void Main(string[] args)
    {
        var credentials = new ApiKeyServiceClientCredentials(ApiKey);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = Endpoint
        };

        // Change the console encoding to display non-ASCII characters.
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        SentimentAnalysisExample(client);
        // DetectLanguageExample(client);
        // RecognizeEntitiesExample(client);
        // KeyPhraseExtractionExample(client);
        Console.ReadLine();
    }
```

以降のセクションでは、各サービス機能を呼び出す方法について説明します。

## <a name="perform-sentiment-analysis"></a>感情分析を実行する

1. 前に作成したクライアントを取得する新しい関数 `SentimentAnalysisExample()` を作成します。
2. 同じ関数内で、`client.Sentiment()` を呼び出して結果を取得します。 成功した場合はセンチメント `Score` が結果に含まれ、失敗した場合は `errorMessage` が含まれます。 0 に近いスコアが否定的な感情を示すのに対して、1 に近いスコアは肯定的な感情を示します。

    ```csharp
    var result = client.Sentiment("I had the best day of my life.", "en");

    // Printing sentiment results
    Console.WriteLine($"Sentiment Score: {result.Score:0.00}");
    ```

### <a name="output"></a>Output

```console
Sentiment Score: 0.87
```

## <a name="perform-language-detection"></a>言語検出を実行する

1. 前に作成したクライアントを取得する新しい関数 `DetectLanguageExample()` を作成します。
2. 同じ関数内で、`client.DetectLanguage()` を呼び出して結果を取得します。 成功した場合は `DetectedLanguages` 内で検出された言語のリストが結果に含まれ、失敗した場合は `errorMessage` が含まれます。 次に、最初に返された言語を印刷します。

    ```csharp
    var result = client.DetectLanguage("This is a document written in English.");

    // Printing detected languages
    Console.WriteLine($"Language: {result.DetectedLanguages[0].Name}");
    ```

> [!Tip]
> 場合によっては、入力に基づいて言語を明確に区別するのが困難なことがあります。 `countryHint` パラメーターを使用して、2 文字の国番号を指定できます。 API の既定では、既定の countryHint として "US" が使用されます。この動作を削除するには、この値を空の文字列 `countryHint = ""` に設定して、このパラメーターをリセットします。

### <a name="output"></a>Output

```console
Language: English
```

## <a name="perform-entity-recognition"></a>エンティティの認識を実行する

1. 前に作成したクライアントを取得する新しい関数 `RecognizeEntitiesExample()` を作成します。
2. 同じ関数内で、`client.Entities()` を呼び出して結果を取得します。 その後、結果を反復処理します。 成功した場合は `Entities` 内で検出されたエンティティのリストが結果に含まれ、失敗した場合は `errorMessage` が含まれます。 検出されたエンティティごとに、その型、サブタイプ、Wikipedia 名 (存在する場合)、および元のテキスト内の場所を出力します。

    ```csharp
    var result = client.Entities("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.");

    // Printing recognized entities
    Console.WriteLine("Entities:");
    foreach (var entity in result.Entities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
        }
    }
    ```

### <a name="output"></a>Output

```console
Entities:
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0,      Length: 9,      Score: 1.000
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25,     Length: 10,     Score: 1.000
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40,     Length: 10,     Score: 0.999
    Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54,     Length: 7,      Score: 0.800
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54,     Length: 13,     Score: 0.800
    Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89,     Length: 5,      Score: 0.800
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116,    Length: 11,     Score: 0.800
```

## <a name="perform-key-phrase-extraction"></a>キー フレーズ抽出を実行する

1. 前に作成したクライアントを取得する新しい関数 `KeyPhraseExtractionExample()` を作成します。
2. 同じ関数内で、`client.KeyPhrases()` を呼び出して結果を取得します。 成功した場合は `KeyPhrases` 内で検出されたキー フレーズのリストが結果に含まれ、失敗した場合は `errorMessage` が含まれます。 次に、検出されたキーフレーズを印刷します。

    ```csharp
    var result = client.KeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in result.KeyPhrases)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
    ```

### <a name="output"></a>Output

```console
Key phrases:
    cat
    veterinarian
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Text Analytics と Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Text Analytics の概要](../overview.md)
* [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md)
