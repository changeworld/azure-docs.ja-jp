---
title: クイック スタート:C# 用 Text Analytics v3 クライアント ライブラリ | Microsoft Docs
description: C# 用 Text Analytics v3 クライアント ライブラリの概要を紹介します
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 2cf9a006e1f6f1edb996aa9beaf8934a14af29df
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281171"
---
<a name="HOLTop"></a>

[リファレンスのドキュメント](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [パッケージ (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [サンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

> [!NOTE]
> * このクイックスタートでは、Text Analytics クライアント ライブラリのバージョン `3.0-preview` を使用します。これには、強化された[感情分析](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)および[名前付きエンティティの認識 (NER)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) のパブリック プレビューが含まれています。
> * この記事のコードでは、単純化するために、同期メソッドと、セキュリティで保護されていない資格情報の格納を使用しています。 運用環境のシナリオでは、パフォーマンスとスケーラビリティを確保するために、バッチ処理された非同期メソッドを使用することをお勧めします。 たとえば、`AnalyzeSentiment()` ではなく、`AnalyzeSentimentAsync()` を呼び出します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)

## <a name="setting-up"></a>設定

### <a name="create-a-text-analytics-azure-resource"></a>Text Analytics Azure リソースを作成する

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-net-core-application"></a>新しい .NET Core アプリを作成する

Visual Studio IDE を使用して新しい .NET Core コンソール アプリを作成します。 1 つの C# ソース ファイル (*program.cs*) を含んだ "Hello World" プロジェクトが作成されます。

**ソリューション エクスプローラー**でソリューションを右クリックし、 **[NuGet パッケージの管理]** を選択して、クライアント ライブラリをインストールします。 パッケージ マネージャーが開いたら、 **[参照]** を選択し、 **[プレリリースを含める]** をオンにして、`Azure.AI.TextAnalytics` を検索します。 それをクリックして **[インストール]** をクリックします。 [パッケージ マネージャー コンソール](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)を使用してもかまいません。

*program.cs* ファイルを開き、次の `using` ディレクティブを追加します。

```csharp
using System;
using Azure.AI.TextAnalytics;
```

アプリケーションの `Program` クラスに、リソースのキーとエンドポイントの変数を作成します。

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

アプリケーションの `Main` メソッドを置き換えます。 ここで呼び出されるメソッドは、後で定義します。

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, key);

    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityPIIExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

## <a name="object-model"></a>オブジェクト モデル

Text Analytics クライアントは、キーを使用して Azure に対して認証を行う `TextAnalyticsClient` オブジェクトであり、テキストを単一の文字列またはバッチとして受け取る機能を提供します。 テキストは、同期的または非同期的に API に送信できます。 応答オブジェクトには、送信する各ドキュメントの分析情報が格納されます。 オプションの `TextAnalyticsClientOptions` インスタンスを使用すると、クライアントをさまざまな既定の設定 (既定の言語や国ヒントなど) で初期化できます。

AAD 認証、クライアントの既定の設定の使用など、その他の例については、[こちら](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)を参照してください。

## <a name="code-examples"></a>コード例

* [感情分析](#sentiment-analysis) (パブリック プレビュー)
* [言語検出](#language-detection)
* [名前付きエンティティの認識](#named-entity-recognition-public-preview) (パブリック プレビュー)
* [名前付きエンティティの認識 - 個人情報](#named-entity-recognition---personal-information-public-preview) (パブリック プレビュー)
* [エンティティ リンク設定](#entity-linking)
* [キー フレーズ抽出](#key-phrase-extraction)

プログラムの `main()` メソッドで、認証メソッドを呼び出してクライアントをインスタンス化します。

## <a name="sentiment-analysis-public-preview"></a>感情分析 (パブリック プレビュー)

> [!NOTE]
> 次のコードは、感情分析 v3 用です。この機能は、パブリック プレビュー段階です。

前に作成したクライアントを受け取る `SentimentAnalysisExample()` という新しい関数を作成し、その `AnalyzeSentiment()` 関数を呼び出します。 返される `Response<AnalyzeSentimentResult>` オブジェクトには、入力ドキュメント全体のセンチメント ラベルとスコアが含まれます。また、成功した場合は各文の感情分析、そうでない場合は `Value.ErrorMessage` が含まれます。

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    var response = client.AnalyzeSentiment("I had the best day of my life. I wish you were there with me.");
    Console.WriteLine($"Document sentiment: {response.Value.DocumentSentiment.SentimentClass}\n");
    foreach (var sentence in response.Value.SentenceSentiments)
    {
        Console.WriteLine($"\tSentence [offset {sentence.Offset}, length {sentence.Length}]");
        Console.WriteLine($"\tSentence sentiment: {sentence.SentimentClass}");
        Console.WriteLine($"\tPositive score: {sentence.PositiveScore:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.NegativeScore:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.NeutralScore:0.00}\n");
    }
}
```

### <a name="output"></a>Output

```console
Document sentiment: Positive

        Sentence [offset 0, length 30]
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [offset 31, length 30]
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

## <a name="language-detection"></a>言語検出

前に作成したクライアントを受け取る `LanguageDetectionExample()` という新しい関数を作成し、その `DetectLanguage()` 関数を呼び出します。 返される `Response<DetectLanguageResult>` オブジェクトには、成功した場合は検出された言語が `Value.PrimaryLanguage` に含まれ、そうでない場合は `Value.ErrorMessage` が含まれます。

> [!Tip]
> 場合によっては、入力に基づいて言語を明確に区別するのが困難なことがあります。 `countryHint` パラメーターを使用して、2 文字の国番号を指定できます。 API の既定では、既定の countryHint として "US" が使用されます。この動作を削除するには、この値を空の文字列に設定して (`countryHint = ""`)、このパラメーターをリセットします。 別の既定値を設定するには、`TextAnalyticsClientOptions.DefaultCountryHint` プロパティを設定し、クライアントの初期化中に渡します。

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    var response = client.DetectLanguage("Ce document est rédigé en Français.");
    var detectedLanguage = response.Value.PrimaryLanguage;
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Output

```console
Language:
        French, ISO-6391: fr
```

## <a name="named-entity-recognition-public-preview"></a>名前付きエンティティの認識 (パブリック プレビュー)

> [!NOTE]
> 次のコードは、名前付きエンティティの認識 v3 用です。この機能は、パブリック プレビュー段階です。

前に作成したクライアントを受け取る `EntityRecognitionExample()` という新しい関数を作成し、その `RecognizeEntities()` 関数を呼び出して、結果を反復処理します。 返される `Response<RecognizeEntitiesResult>` オブジェクトには、成功した場合は検出されたエンティティの一覧が `Value.NamedEntities` に含まれ、そうでない場合は `Value.ErrorMessage` が含まれます。 検出されたエンティティごとに、タイプとサブタイプ (ある場合) を出力します。

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Output

```console
Named Entities:
        Text: Seattle,  Type: Location, Sub-Type: N/A
                Offset: 26,     Length: 7,      Score: 0.806

        Text: last week,        Type: DateTime, Sub-Type: N/A
                Offset: 34,     Length: 9,      Score: 0.800
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>名前付きエンティティの認識 - 個人情報 (パブリック プレビュー)

> [!NOTE]
> 次のコードは、名前付きエンティティの認識 v3 を使用して個人情報を検出するためのものです。この機能は、パブリック プレビュー段階です。

前に作成したクライアントを受け取る `EntityPIIExample()` という新しい関数を作成し、その `RecognizePiiEntities()` 関数を呼び出して、結果を反復処理します。 前の関数と同様に、返される `Response<RecognizeEntitiesResult>` オブジェクトには、成功した場合は検出されたエンティティの一覧が `Value.NamedEntities` に含まれ、そうでない場合は `Value.ErrorMessage` が含まれます。

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    var response = client.RecognizePiiEntities("Insurance policy for SSN on file 123-12-1234 is here by approved.");
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Output

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Type: U.S. Social Security Number (SSN),        Sub-Type: N/A
                Offset: 33,     Length: 11,     Score: 0.850
```

## <a name="entity-linking"></a>Entity Linking

前に作成したクライアントを受け取る `EntityLinkingExample()` という新しい関数を作成し、その `RecognizeLinkedEntities()` 関数を呼び出して、結果を反復処理します。 返される `Response<RecognizeLinkedEntitiesResult>` オブジェクトには、成功した場合は検出されたエンティティの一覧が `Value.LinkedEntities` に含まれ、そうでない場合は `Value.ErrorMessage` が含まれます。 リンクされたエンティティは一意に識別されるため、同じエンティティの出現は、`LinkedEntityMatch` オブジェクトの一覧として `LinkedEntity` オブジェクトの下にグループ化されます。

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value.LinkedEntities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.Id},\tURL: {entity.Uri}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.Score:F3}\n");
        }
    }
}
```

### <a name="output"></a>Output

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Offset: 116,    Length: 11,     Score: 0.777

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25,     Length: 10,     Score: 0.555

                Text: Gates
                Offset: 161,    Length: 5,      Score: 0.555

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40,     Length: 10,     Score: 0.533

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0,      Length: 9,      Score: 0.469

                Text: Microsoft
                Offset: 150,    Length: 9,      Score: 0.469

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54,     Length: 7,      Score: 0.248

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89,     Length: 5,      Score: 0.281
```

## <a name="key-phrase-extraction"></a>キー フレーズの抽出

前に作成したクライアントを受け取る `KeyPhraseExtractionExample()` という新しい関数を作成し、その `ExtractKeyPhrases()` 関数を呼び出します。 結果には、成功した場合は検出されたキー フレーズの一覧が `Value.KeyPhrases` に含まれ、そうでない場合は `Value.ErrorMessage` が含まれます。 検出されたキー フレーズを出力します。

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value.KeyPhrases)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Output

```console
Key phrases:
    cat
    veterinarian
```
