---
title: クイック スタート:Node.js 用 Text Analytics v3 クライアント ライブラリ | Microsoft Docs
description: Node.js 用 Text Analytics v3 クライアント ライブラリの概要を紹介します。
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 08/17/2021
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.custom: devx-track-js
ms.openlocfilehash: c00334378ebbc2f399a9daa1c20ed4c47c84df65
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864899"
---
<a name="HOLTop"></a>

# <a name="version-32-preview1"></a>[バージョン 3.2-preview.1](#tab/version-3-2)

[v3.2-preview リファレンス ドキュメント](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-preview) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [v3.2-preview パッケージ (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics/v/5.2.0-beta.1) | [サンプル](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

[v3.1 リファレンス ドキュメント](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-latest) | [v3 ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [v3.1 パッケージ (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics/v/5.1.0) | [サンプル](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/textanalytics/ai-text-analytics/samples)

---

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* 最新バージョンの [Node.js](https://nodejs.org/)。
* [!INCLUDE [contributor-requirement](../../../includes/quickstarts/contributor-requirement.md)]
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Text Analytics リソースを作成"  target="_blank">Text Analytics リソースを作成</a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * アプリケーションを Text Analytics API に接続するには、作成するリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* 分析機能を使用するには、Standard (S) 価格レベルの Text Analytics リソースが必要です。

## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。 

```console
mkdir myapp 

cd myapp
```

`npm init` コマンドを実行し、`package.json` ファイルを使用して node アプリケーションを作成します。 

```console
npm init
```
### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

# <a name="version-32-preview1"></a>[バージョン 3.2-preview.1](#tab/version-3-2)

`@azure/ai-text-analytics` NPM パッケージをインストールします。

```console
npm install @azure/ai-text-analytics@5.2.0-beta.1
```

このバージョンの Text Analytics API に含まれる機能:

* センチメント分析
* 意見マイニング
* 言語検出
* エンティティの認識
* エンティティ リンク設定
* 個人が特定される情報の認識
* キー フレーズの抽出
* 非同期メソッド
* Text Analytics for Health
* テキストの概要作成

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

`@azure/ai-text-analytics` NPM パッケージをインストールします。

```console
npm install --save @azure/ai-text-analytics@5.1.0
```

このバージョンの Text Analytics API に含まれる機能:

* センチメント分析
* 意見マイニング
* 言語検出
* エンティティの認識
* エンティティ リンク設定
* 個人が特定される情報の認識
* キー フレーズの抽出
* 非同期メソッド
* Text Analytics for Health

---

アプリの `package.json` ファイルが依存関係によって更新されます。
`index.js` という名前のファイルを作成し、以下を追加します。

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = '<paste-your-text-analytics-endpoint-here>';
```

## <a name="object-model"></a>オブジェクト モデル

Text Analytics クライアントは、キーを使用して Azure に対して認証を行う `TextAnalyticsClient` オブジェクトです。 このクライアントには、テキストを単一の文字列として、またはバッチとして分析するためのメソッドがいくつか備わっています。

テキストは、`documents` (使用したメソッドに応じて `id`、`text`、`language` の各属性の組み合わせを保持する `dictionary` オブジェクト) のリストとして API に送信されます。 `text` 属性には、分析対象のテキストが元の `language` で格納され、`id` には任意の値を指定できます。 

応答オブジェクトは、各ドキュメントの分析情報を格納するリストです。 

[!INCLUDE [text-analytics-character-limits](../character-limits.md)]

## <a name="authenticate-the-client"></a>クライアントを認証する

キーとエンドポイントをパラメーターとして使用して、新しい `TextAnalyticsClient` オブジェクトを作成します。

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

## <a name="sentiment-analysis"></a>センチメント分析

分析するドキュメントを含む文字列の配列を作成します。 クライアントの `analyzeSentiment()` メソッドを呼び出し、返される `SentimentBatchResult` オブジェクトを取得します。 結果の一覧を反復処理し、各ドキュメントの ID、ドキュメント レベルのセンチメントと信頼度スコアを出力します。 各ドキュメントの結果には、文レベルのセンチメントと、オフセット、長さ、および信頼度スコアが含まれます。

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```


## <a name="opinion-mining"></a>意見マイニング

オピニオン マイニングを使用した感情分析を行うには、分析するドキュメントを含む文字列の配列を作成します。 クライアントの `analyzeSentiment()` メソッドに別途オプション フラグ `includeOpinionMining: true` を渡して呼び出し、返される `SentimentBatchResult` オブジェクトを取得します。 結果の一覧を反復処理し、各ドキュメントの ID、ドキュメント レベルのセンチメントと信頼度スコアを出力します。 各ドキュメントの結果には、前述した文レベルのセンチメントだけでなく、アスペクトおよびオピニオン レベルのセンチメントも含まれます。

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```javascript
async function sentimentAnalysisWithOpinionMining(client){

  const sentimentInput = [
    {
      text: "The food and service were unacceptable, but the concierge were nice",
      id: "0",
      language: "en"
    }
  ];
  const results = await client.analyzeSentiment(sentimentInput, { includeOpinionMining: true });

  for (let i = 0; i < results.length; i++) {
    const result = results[i];
    console.log(`- Document ${result.id}`);
    if (!result.error) {
      console.log(`\tDocument text: ${sentimentInput[i].text}`);
      console.log(`\tOverall Sentiment: ${result.sentiment}`);
      console.log("\tSentiment confidence scores:", result.confidenceScores);
      console.log("\tSentences");
      for (const { sentiment, confidenceScores, opinions } of result.sentences) {
        console.log(`\t- Sentence sentiment: ${sentiment}`);
        console.log("\t  Confidence scores:", confidenceScores);
        console.log("\t  Mined opinions");
        for (const { target, assessments } of opinions) {
          console.log(`\t\t- Target text: ${target.text}`);
          console.log(`\t\t  Target sentiment: ${target.sentiment}`);
          console.log("\t\t  Target confidence scores:", target.confidenceScores);
          console.log("\t\t  Target assessments");
          for (const { text, sentiment } of assessments) {
            console.log(`\t\t\t- Text: ${text}`);
            console.log(`\t\t\t  Sentiment: ${sentiment}`);
          }
        }
      }
    } else {
      console.error(`\tError: ${result.error}`);
    }
  }
}
sentimentAnalysisWithOpinionMining(textAnalyticsClient)
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
- Document 0
        Document text: The food and service were unacceptable, but the concierge were nice
        Overall Sentiment: positive
        Sentiment confidence scores: { positive: 0.84, neutral: 0, negative: 0.16 }
        Sentences
        - Sentence sentiment: positive
          Confidence scores: { positive: 0.84, neutral: 0, negative: 0.16 }
          Mined opinions
                - Target text: food
                  Target sentiment: negative
                  Target confidence scores: { positive: 0.01, negative: 0.99 }
                  Target assessments
                        - Text: unacceptable
                          Sentiment: negative
                - Target text: service
                  Target sentiment: negative
                  Target confidence scores: { positive: 0.01, negative: 0.99 }
                  Target assessments
                        - Text: unacceptable
                          Sentiment: negative
                - Target text: concierge
                  Target sentiment: positive
                  Target confidence scores: { positive: 1, negative: 0 }
                  Target assessments
                        - Text: nice
                          Sentiment: positive
```

## <a name="language-detection"></a>言語検出

分析するドキュメントを含む文字列の配列を作成します。 クライアントの `detectLanguage()` メソッドを呼び出し、返される `DetectLanguageResultCollection` を取得します。 次に、結果を反復処理して、各ドキュメントの ID とそれぞれの第一言語を出力します。

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
ID: 0
        Primary Language French
```


## <a name="named-entity-recognition-ner"></a>名前付きエンティティの認識 (NER)

分析するドキュメントを含む文字列の配列を作成します。 クライアントの `recognizeEntities()` メソッドを呼び出し、`RecognizeEntitiesResult` オブジェクトを取得します。 結果の一覧を反復処理し、エンティティ名、タイプ、サブタイプ、オフセット、長さ、およびスコアを出力します。

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

## <a name="personally-identifying-information-pii-recognition"></a>個人を特定できる情報 (PII) の認識

分析するドキュメントを含む文字列の配列を作成します。 クライアントの `recognizePiiEntities()` メソッドを呼び出し、`RecognizePIIEntitiesResult` オブジェクトを取得します。 結果の一覧を反復処理し、エンティティ名、タイプ、スコアを出力します。

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```javascript
async function piiRecognition(client) {

    const documents = [
        "The employee's phone number is (555) 555-5555."
    ];

    const results = await client.recognizePiiEntities(documents, "en");
    for (const result of results) {
        if (result.error === undefined) {
            console.log("Redacted Text: ", result.redactedText);
            console.log(" -- Recognized PII entities for input", result.id, "--");
            for (const entity of result.entities) {
                console.log(entity.text, ":", entity.category, "(Score:", entity.confidenceScore, ")");
            }
        } else {
            console.error("Encountered an error:", result.error);
        }
    }
}
piiRecognition(textAnalyticsClient)
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
Redacted Text:  The employee's phone number is **************.
 -- Recognized PII entities for input 0 --
(555) 555-5555 : Phone Number (Score: 0.8 )
```

## <a name="entity-linking"></a>エンティティ リンク設定

分析するドキュメントを含む文字列の配列を作成します。 クライアントの `recognizeLinkedEntities()` メソッドを呼び出し、`RecognizeLinkedEntitiesResult` オブジェクトを取得します。 結果の一覧を反復処理し、エンティティ名、ID、データ ソース、URL、および一致を出力します。 `matches` 配列内の各オブジェクトには、その一致のオフセット、長さ、およびスコアが含まれています。

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

## <a name="key-phrase-extraction"></a>キー フレーズの抽出

分析するドキュメントを含む文字列の配列を作成します。 クライアントの `extractKeyPhrases()` メソッドを呼び出し、返される `ExtractKeyPhrasesResult` オブジェクトを取得します。 結果を反復処理し、各ドキュメントの ID と検出されたすべてのキー フレーズを出力します。

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

## <a name="extract-health-entities"></a>ヘルス エンティティの抽出

[!INCLUDE [health operation pricing](../health-operation-pricing-caution.md)]

Text Analytics を使用して、テキストからヘルスケア エンティティを抽出する非同期要求を実行できます。 次のサンプルでは、基本的な例を示しています。 より高度なサンプルについては、[GitHub ](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/textanalytics/ai-text-analytics/samples/v5/javascript/beginAnalyzeHealthcareEntities.js) を参照してください。

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```javascript
async function healthExample(client) {
    console.log("== Recognize Healthcare Entities Sample ==");

    const documents = [
        "Prescribed 100mg ibuprofen, taken twice daily."
      ];
    const poller = await client.beginAnalyzeHealthcareEntities(documents, "en", {
      includeStatistics: true
    });
  
    poller.onProgress(() => {
      console.log(
        `Last time the operation was updated was on: ${poller.getOperationState().lastModifiedOn}`
      );
    });
    console.log(
      `The analyze healthcare entities operation was created on ${
        poller.getOperationState().createdOn
      }`
    );
    console.log(
      `The analyze healthcare entities operation results will expire on ${
        poller.getOperationState().expiresOn
      }`
    );
  
    const results = await poller.pollUntilDone();
  
    for await (const result of results) {
      console.log(`- Document ${result.id}`);
      if (!result.error) {
        console.log("\tRecognized Entities:");
        for (const entity of result.entities) {
          console.log(`\t- Entity "${entity.text}" of type ${entity.category}`);
        }
        if (result.entityRelations && (result.entityRelations.length > 0)) {
          console.log(`\tRecognized relations between entities:`);
          for (const relation of result.entityRelations) {
            console.log(
              `\t\t- Relation of type ${relation.relationType} found between the following entities:`
            );
            for (const role of relation.roles) {
              console.log(`\t\t\t- "${role.entity.text}" with the role ${role.name}`);
            }
          }
        }
      } else console.error("\tError:", result.error);
    }
  }
  
  healthExample(textAnalyticsClient).catch((err) => {
    console.error("The sample encountered an error:", err);
  });
```

### <a name="output"></a>出力

```console
- Document 0
    Recognized Entities:
    - Entity "100mg" of type Dosage
    - Entity "ibuprofen" of type MedicationName
    - Entity "twice daily" of type Frequency
    Recognized relations between entities:
        - Relation of type DosageOfMedication found between the following entities:   
                - "100mg" with the role Dosage
                - "ibuprofen" with the role Medication
        - Relation of type FrequencyOfMedication found between the following entities:
                - "ibuprofen" with the role Medication
                - "twice daily" with the role Frequency
```


## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>分析操作で API を非同期的に使用する

分析操作を使用して、NER、キー フレーズ抽出、感情分析、および PII 検出の非同期バッチ要求を実行できます。 次のサンプルは、1 つの操作の基本的な例を示しています。 GitHub には、[JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/textanalytics/ai-text-analytics/samples/v5/javascript/beginAnalyzeActions.js) と [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/textanalytics/ai-text-analytics/samples/v5/typescript/src/beginAnalyzeActions.ts) のより高度なサンプルがあります。

[!INCLUDE [Analyze Batch Action pricing](../analyze-operation-pricing-caution.md)]

`beginAnalyze()` 関数を呼び出す `analyze_example()` という名前の新しい関数を作成します。 この操作の実行には時間がかかり、結果に対してポーリングされます。

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```javascript
async function analyze_example(client) {
    const documents = [
        "Microsoft was founded by Bill Gates and Paul Allen.",
    ];

    const actions = {
        recognizeEntitiesActions: [{ modelVersion: "latest" }],
        extractKeyPhrasesActions: [{ modelVersion: "latest" }]
    };
    const poller = await client.beginAnalyzeActions(documents, actions, "en");

    console.log(
        `The analyze batch actions operation was created on ${poller.getOperationState().createdOn}`
    );
    console.log(
        `The analyze batch actions operation results will expire on ${poller.getOperationState().expiresOn
        }`
    );
    const resultPages = await poller.pollUntilDone();
    for await (const page of resultPages) {
        const entitiesAction = page.recognizeEntitiesResults[0];
        if (!entitiesAction.error) {
            for (const doc of entitiesAction.results) {
                console.log(`- Document ${doc.id}`);
                if (!doc.error) {
                    console.log("\tEntities:");
                    for (const entity of doc.entities) {
                        console.log(`\t- Entity ${entity.text} of type ${entity.category}`);
                    }
                } else {
                    console.error("\tError:", doc.error);
                }
            }
        }
        const keyPhrasesAction = page.extractKeyPhrasesResults[0];
        if (!keyPhrasesAction.error) {
            for (const doc of keyPhrasesAction.results) {
                console.log(`- Document ${doc.id}`);
                if (!doc.error) {
                    console.log("\tKey phrases:");
                    for (const phrase of doc.keyPhrases) {
                        console.log(`\t- ${phrase}`);
                    }
                } else {
                    console.error("\tError:", doc.error);
                }
            }
        }
    }
}
analyze_example(textAnalyticsClient)
```

### <a name="output"></a>出力

```console
The analyze batch actions operation was created on Fri Jun 18 2021 12:34:52 GMT-0700 (Pacific Daylight Time)
The analyze batch actions operation results will expire on Sat Jun 19 2021 12:34:52 GMT-0700 (Pacific Daylight Time)
- Document 0
        Entities:
        - Entity Microsoft of type Organization
        - Entity Bill Gates of type Person
        - Entity Paul Allen of type Person
- Document 0
        Key phrases:
        - Bill Gates
        - Paul Allen
        - Microsoft
```

分析操作を使用して、NER、キー フレーズ抽出、感情分析、PII の検出を実行することもできます。 GitHub で「[JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/textanalytics/ai-text-analytics/samples/v5/javascript/beginAnalyzeActions.js) と [TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/v5/typescript/src) のサンプルを分析する」を参照してください。

## <a name="text-summarization"></a>テキストの概要作成

# <a name="version-32-preview1"></a>[バージョン 3.2-preview.1](#tab/version-3-2)

Text Analytics を使用すると、大量のテキストを要約できます。 このメソッドは非同期であり、長時間の操作が終了すると上位の文が返されます。

```javascript
async function summarization_example(client) {
    const documents = [`The extractive summarization feature in Text Analytics uses natural language processing techniques to locate key sentences in an unstructured text document. 
        These sentences collectively convey the main idea of the document. This feature is provided as an API for developers. 
        They can use it to build intelligent solutions based on the relevant information extracted to support various use cases. 
        In the public preview, extractive summarization supports several languages. It is based on pretrained multilingual transformer models, part of our quest for holistic representations. 
        It draws its strength from transfer learning across monolingual and harness the shared nature of languages to produce models of improved quality and efficiency.`];

    console.log("== Analyze Sample For Extract Summary ==");

    const actions = {
        extractSummaryActions: [{ modelVersion: "latest", orderBy: "Rank", maxSentenceCount: 5 }],
    };
    const poller = await client.beginAnalyzeActions(documents, actions, "en");

    poller.onProgress(() => {
        console.log(
            `Number of actions still in progress: ${poller.getOperationState().actionsInProgressCount}`
        );
    });

    console.log(`The analyze actions operation created on ${poller.getOperationState().createdOn}`);

    console.log(
        `The analyze actions operation results will expire on ${poller.getOperationState().expiresOn}`
    );

    const resultPages = await poller.pollUntilDone();

    for await (const page of resultPages) {
        const extractSummaryAction = page.extractSummaryResults[0];
        if (!extractSummaryAction.error) {
            for (const doc of extractSummaryAction.results) {
                console.log(`- Document ${doc.id}`);
                if (!doc.error) {
                    console.log("\tSummary:");
                    for (const sentence of doc.sentences) {
                        console.log(`\t- ${sentence.text}`);
                    }
                } else {
                    console.error("\tError:", doc.error);
                }
            }
        }
    } 
}
summarization_example(textAnalyticsClient).catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

# <a name="version-31"></a>[Version 3.1](#tab/version-3-1)

この機能はバージョン 3.1 では使用できません

---

クイック スタート ファイルで `node` コマンドを使用して、アプリケーションを実行します。

```console
node index.js
```
