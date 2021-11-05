---
title: Cognitive Services をスキルセットにアタッチする
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search でマルチサービス Cognitive Services リソースを AI エンリッチメント パイプラインにアタッチする方法について説明します。
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: ce369b60fa1b12823acdb1f5045e403bcaa3f5dd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131037665"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Azure Cognitive Search で Cognitive Services リソースをスキルセットにアタッチする

Azure Cognitive Search で [オプションの AI エン](cognitive-search-concept-intro.md) リッチメント パイプラインを構成する場合は、限られた数のドキュメントを無料で強化できます。 ワークロードのサイズが大きく、頻度が高い場合は、リソース に対して請求可能 [**なマルチサービス Cognitive Services必要があります**](../cognitive-services/cognitive-services-apis-create-account.md)。 マルチサービス リソースでは、個々のサービスではなくオファリングとして "Cognitive Services" を参照し、1 つの API キーを使用してアクセス権が付与されます。

マルチサービス リソース キーはスキルセットで指定され、Microsoft は次の API を使用した場合に課金できます：

+ イメージ分析と光学式文字認識 (OCR) のための [Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/)
+ 言語検出、エンティティ認識、感情分析、およびキー フレーズ抽出のための [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/)
+ [テキストの変換](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)

> [!NOTE]
> AI エンリッチメントには少量の無料処理が用意されています。そのため、リソースにリソースをアタッチすることなく短い演習Cognitive Servicesできます。 無料のエンリッチメントは、インデクサーあたり 1 日あたり 20 ドキュメントです。 演習を [繰り返す場合](search-howto-run-reset-indexers.md) は、インデクサーをリセットしてカウンターをリセットできます。

## <a name="azure-portal"></a>[**Azure portal**](#tab/cogkey-portal)

1. 検索サービス [と同じリージョンCognitive Servicesマルチ](../cognitive-services/cognitive-services-apis-create-account.md) サービス [リソース](#same-region-requirement) を作成します。

1. スキルセット定義にキーを追加します：

   + データのインポート [ウィザードを使用する場合は](search-import-data-portal.md)、2 番目の手順 「AI エンリッチメントの追加」でキーを入力します。

   + 新しいスキルセットまたは既存のスキルセットにキーを追加する場合は、キーを [新しいスキルセット] **タブCognitive Services** します。

   :::image type="content" source="media/cognitive-search-attach-cognitive-services/attach-existing2.png" alt-text="キー ページのスクリーンショット" border="true":::

## <a name="rest"></a>[**REST**](#tab/cogkey-rest)

1. 検索サービス [と同じリージョンCognitive Servicesマルチ](../cognitive-services/cognitive-services-apis-create-account.md) サービス [リソース](#same-region-requirement) を作成します。

1. スキルセット要求の本文で セクションを指定 `cognitiveServices` して、スキルセットを [作成または更新します](/rest/api/searchservice/create-skillset)：

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="net-sdk"></a>[ **.NET SDK**](#tab/cogkey-dotnet)

次のコード スニペットは [、簡単に説明するようにトリミングされた azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/tutorial-ai-enrichment/v11/Program.cs)からの抜粋です。

```csharp
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();

string searchServiceUri = configuration["SearchServiceUri"];
string adminApiKey = configuration["SearchServiceAdminApiKey"];
string cognitiveServicesKey = configuration["CognitiveServicesKey"];

SearchIndexerClient indexerClient = new SearchIndexerClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));

// Create the skills
Console.WriteLine("Creating the skills...");
OcrSkill ocrSkill = CreateOcrSkill();
MergeSkill mergeSkill = CreateMergeSkill();

// Create the skillset
Console.WriteLine("Creating or updating the skillset...");
List<SearchIndexerSkill> skills = new List<SearchIndexerSkill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);

SearchIndexerSkillset skillset = CreateOrUpdateDemoSkillSet(indexerClient, skills, cognitiveServicesKey);
```

---

## <a name="how-the-key-is-used"></a>キーの使い方

キーは課金に使用されますが、接続には使用されません。 接続の場合、検索サービスは内部ネットワークをCognitive Services同じ物理リージョン に同じリソース [に接続します](https://azure.microsoft.com/global-infrastructure/services/?products=search)。 

キーベースの課金は、リソースへの API 呼び出しCognitive Servicesインデクサーあたり 1 日あたり 20 回の API 呼び出しを超えた場合に適用されます。 インデクサーを呼び出すごとにインデクサーをリセットして API カウンターをリセットできますが、自由に行える呼び出しの最大数は 20 に制限されます。

 AI エンリッチメント中、Cognitive Search Cognitive Services APIs、テキスト翻訳、およびテキスト翻訳に基づく[組み込みスキル](cognitive-search-predefined-skills.md)の Computer Vision を呼び出Text Analytics。 Cognitive Services へのバックエンド呼び出しを行う組み込みのスキルには、Entity [Linking、](cognitive-search-skill-entity-linking-v3.md)[エンティティ認識](cognitive-search-skill-entity-recognition-v3.md)、[画像](cognitive-search-skill-image-analysis.md)分析、[キー フレーズ抽出](cognitive-search-skill-keyphrases.md)、[言語検出](cognitive-search-skill-language-detection.md)、[OCR](cognitive-search-skill-ocr.md)、[PII検出](cognitive-search-skill-pii-detection.md)、[センチメント](cognitive-search-skill-sentiment-v3.md)、[テキスト翻訳](cognitive-search-skill-text-translation.md)があります。

カスタム スキルまたはユーティリティ スキルのみで構成されるCognitive Servicesキーとキー のセクションを省略できます。 また、請求可能なスキルの使用量が 1 日あたりインデクサーあたり 20 トランザクションを超える場合は、プロパティを指定しないままにしておく必要があります。

### <a name="exceptions-and-special-cases"></a>例外と特殊なケース

+ プロパティを呼び出すCognitive Servicesユーティリティ スキル（つまり、[条件付き](cognitive-search-skill-conditional.md)、[ドキュメント抽出](cognitive-search-skill-document-extraction.md)、[Shaper](cognitive-search-skill-shaper.md)、[テキストマージ](cognitive-search-skill-textmerger.md)、および[テキスト分割スキル](cognitive-search-skill-textsplit.md)）は請求できません。 

+ [カスタム エンティティ検索](cognitive-search-skill-custom-entity-lookup.md)は、Cognitive Services ではなく Azure Cognitive Search によって測定されますが、インデクサーごとに 1 日あたり 20 を超えるトランザクションのロックを解除するために、Cognitive Services リソース キーが必要です。 このスキルの場合のみ、リソース キーによってトランザクション数のブロックが解除されますが、課金とは無関係です。

### <a name="other-costs-of-ai-enrichment"></a>AIエンリッチメントのその他のコスト

画像抽出は、エンリッチメントに先立ってドキュメントがクラックされるときに行われる Azure Cognitive Search 操作です。 画像抽出は、すべてのレベルで課金対象ですが、Free レベルでの 1 日 20 回の無料抽出を除きます。 画像抽出コストは、BLOB 内の画像ファイル、他のファイル (PDF および他のアプリ ファイル) に埋め込まれた画像、および[ドキュメント抽出](cognitive-search-skill-document-extraction.md)を使用して抽出された画像に適用されます。 画像抽出の価格については、「[Azure Cognitive Search の価格ページ](https://azure.microsoft.com/pricing/details/search/)」をご覧ください。

[ドキュメント解析](search-indexer-overview.md#document-cracking)段階では、テキスト抽出も行われます。 それは課金対象ではありません。

> [!TIP]
> スキルセット処理のコストを削減するには、[インクリメンタル エンリッチメント (プレビュー)](cognitive-search-incremental-indexing-conceptual.md) を有効にして、スキルセットに加えた変更の影響を受けないエンリッチメントをキャッシュして再利用します。 キャッシュには Azure Storage が必要です ([価格](https://azure.microsoft.com/pricing/details/storage/blobs/)を参照してください)。ただし、既存のエンリッチメントを再利用できる場合は、スキルセットの実行の累積コストが低くなります (特に画像の抽出と分析を使用するスキルセットの場合)。

## <a name="same-region-requirement"></a>同一リージョンの要件

Cognitive Search と Cognitive Services は両方とも、[利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=search)のページで示されているように、同じ物理リージョン内に存在する必要があります。 Cognitive Search を提供するほとんどのリージョンでは Cognitive Services も提供されています。

両方のサービスがないリージョンで AI エンリッチメントを試行した場合、"Provided key is not a valid CognitiveServices type key for the region of your search service (指定されたキーは、検索サービスのリージョンに対して有効な CognitiveServices タイプのキーではありません)" というメッセージが表示されます。

> [!NOTE]
> 一部の組み込みスキルは、非リージョンの Cognitive Services ([テキスト翻訳スキル](cognitive-search-skill-text-translation.md)など) をベースとしています。 非リージョン スキルを使用すると、Azure Cognitive Search リージョン以外のリージョンで要求に対してサービスが提供される可能性があります。 非リージョン サービスの詳細については、[Cognitive Services のリージョン別製品](https://aka.ms/allinoneregioninfo)に関するページを参照してください。

## <a name="example-estimate-costs"></a>例: コストの見積もり

Cognitive Search のインデックス作成に関連するコストを見積もるには、まず、いくつかの試算ができるように、平均的なドキュメントがどのようなものかを把握することから開始します。 たとえば、次のように概算することができます。

+ 1000 PDF。
+ それぞれ 6 ページ。
+ 1 ページに 1 画像 (6000 画像)。
+ 1 ページあたり 3,000 文字。

各 PDF のドキュメント解析、画像とテキストの抽出、画像の光学式文字認識 (OCR)、組織のエンティティ認識からなるパイプラインを想定します。

この記事に示されている価格は仮定上のものです。 これらは、見積もりプロセスを説明するために使用されています。 お客様のコストはこれより低い可能性があります。 トランザクションの実際の価格については、[Cognitive Services の価格](https://azure.microsoft.com/pricing/details/cognitive-services)に関するページをご覧ください。

1. テキストと画像のコンテンツを含むドキュメント解読の場合、現在、テキスト抽出は無料です。 6,000 個の画像の場合、1,000 個の画像が抽出されるごとに 1 ドルと想定します。 このステップの場合、6.00 ドルのコストになります。

2. 6,000 画像の英語での OCR については、OCR コグニティブ スキルでは最適なアルゴリズム (DescribeText) が使用されます。 1,000 個のイメージを分析するたびに $2.50 かかるとすると、この手順に対して $15.00 が課金されることになります。

3. エンティティの抽出では、1 ページごとに合計 3 個のテキスト レコードがあります。 各レコードは 1,000 文字です。 1 ページあたり 3 個のテキスト レコードに 6,000 ページを掛けると、18,000 個のテキスト レコードになります。 1,000 個のテキスト レコードごとに $2.00 かかるとすると、この手順のコストは $36.00 になります。

まとめると、示されたスキルセットでこの種類の PDF ドキュメントを 1,000 個取り込むには、約 $57.00 を支払うことなります。

## <a name="next-steps"></a>次のステップ

+ [Azure Cognitive Search の価格ページ](https://azure.microsoft.com/pricing/details/search/)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [スキルセットを作成する (REST)](/rest/api/searchservice/create-skillset)
+ [エンリッチされたフィールドをマップする方法](cognitive-search-output-field-mapping.md)
