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
ms.date: 07/18/2019
ms.author: assafi
ms.openlocfilehash: 09713528f51675f6e9d7f3073b6c81b095d23631
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356961"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>クイック スタート:.NET SDK と C# を使用して Text Analytics サービスを呼び出す
<a name="HOLTop"></a>

このクイック スタートは、Azure SDK for .NET と C# を使用して言語の分析を開始するのに役立ちます。 [Text Analytics](//go.microsoft.com/fwlink/?LinkID=759711) REST API はほとんどのプログラミング言語と互換性がありますが、この SDK は、サービスをアプリケーションに統合するための簡単な方法を提供します。

> [!NOTE]
> このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics) で入手できます。

技術的な詳細については、SDK for .NET の [Text Analytics のリファレンス](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet)を参照してください。

## <a name="prerequisites"></a>前提条件

* [Visual Studio 2017 以降] の任意のエディション
* Text Analytics [SDK for .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

サインアップ中に自動的に生成された[エンドポイントとアクセス キー](../How-tos/text-analytics-how-to-access-key.md)も必要です。

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
        SentimentAnalysisExample(client).Wait();
        // DetectLanguageExample(client).Wait();
        // RecognizeEntitiesExample(client).Wait();
        // KeyPhraseExtractionExample(client).Wait();
        Console.ReadLine();
    }
```

以降のセクションでは、各サービス機能を呼び出す方法について説明します。

## <a name="perform-sentiment-analysis"></a>感情分析を実行する

1. 前に作成したクライアントを取得する新しい関数 `SentimentAnalysisExample()` を作成します。
2. 分析するドキュメントを含む `MultiLanguageInput` オブジェクトの一覧を生成します。

    ```csharp
    public static async Task SentimentAnalysisExample(TextAnalyticsClient client)
    {
        // The documents to be analyzed. Add the language of the document. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "I had the best day of my life.")
            });
        //...
    }
    ```

3. 同じ関数内で、`client.SentimentAsync()` を呼び出して結果を取得します。 その後、結果を反復処理します。 各ドキュメントの ID とセンチメント スコアを印刷します。 0 に近いスコアが否定的な感情を示すのに対して、1 に近いスコアは肯定的な感情を示します。

    ```csharp
    var result = await client.SentimentAsync(false, inputDocuments);

    // Printing sentiment results
    foreach (var document in result.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
    }
    ```

### <a name="output"></a>Output

```console
Document ID: 1 , Sentiment Score: 0.87
```

## <a name="perform-language-detection"></a>言語検出を実行する

1. 前に作成したクライアントを取得する新しい関数 `DetectLanguageExample()` を作成します。
2. ドキュメントを含む `LanguageInput` オブジェクトの一覧を生成します。

    ```csharp
    public static async Task DetectLanguageExample(TextAnalyticsClient client)
    {

        // The documents to be submitted for language detection. The ID can be any value.
        var inputDocuments = new LanguageBatchInput(
                new List<LanguageInput>
                    {
                        new LanguageInput(id: "1", text: "This is a document written in English.")
                    });
        //...
    }
    ```

3. 同じ関数内で、`client.DetectLanguageAsync()` を呼び出して結果を取得します。 その後、結果を反復処理します。 各ドキュメントの ID と最初に返された言語を印刷します。

    ```csharp
    var langResults = await client.DetectLanguageAsync(false, inputDocuments);

    // Printing detected languages
    foreach (var document in langResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
    }
    ```

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
```

## <a name="perform-entity-recognition"></a>エンティティの認識を実行する

1. 前に作成したクライアントを取得する新しい関数 `RecognizeEntitiesExample()` を作成します。
2. ドキュメントを含む `MultiLanguageBatchInput` オブジェクトの一覧を生成します。

    ```csharp
    public static async Task RecognizeEntitiesExample(TextAnalyticsClient client)
    {
        // The documents to be submitted for entity recognition. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.")
            });
        //...
    }
    ```

3. 同じ関数内で、`client.EntitiesAsync()` を呼び出して結果を取得します。 その後、結果を反復処理します。 各ドキュメントの ID を印刷します。 検出されたエンティティごとに、その Wikipedia 名、型、サブタイプ (存在する場合)、および元のテキスト内の場所を出力します。

    ```csharp
    var entitiesResult = await client.EntitiesAsync(false, inputDocuments);

    // Printing recognized entities
    foreach (var document in entitiesResult.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Entities:");
        foreach (var entity in document.Entities)
        {
            Console.WriteLine($"\t\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
            foreach (var match in entity.Matches)
            {
                Console.WriteLine($"\t\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
            }
        }
    }
    ```

### <a name="output"></a>Output

```console
Document ID: 1
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
2. ドキュメントを含む `MultiLanguageBatchInput` オブジェクトの一覧を生成します。

    ```csharp
    public static async Task KeyPhraseExtractionExample(TextAnalyticsClient client)
    {
        var inputDocuments = new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("en", "1", "My cat might need to see a veterinarian.")
                    });
        //...
    }
    ```

3. 同じ関数内で、`client.KeyPhrasesAsync()` を呼び出して結果を取得します。 その後、結果を反復処理します。 各ドキュメントの ID と検出されたキー フレーズを印刷します。

    ```csharp
    var kpResults = await client.KeyPhrasesAsync(false, inputDocuments);

    // Printing keyphrases
    foreach (var document in kpResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Key phrases:");

        foreach (string keyphrase in document.KeyPhrases)
        {
            Console.WriteLine($"\t\t{keyphrase}");
        }
    }
    ```

### <a name="output"></a>Output

```console
Document ID: 1
         Key phrases:
                cat
                veterinarian
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Text Analytics と Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Text Analytics の概要](../overview.md)
* [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md)
