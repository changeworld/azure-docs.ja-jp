---
title: Azure Cognitive Service for Language とは
titleSuffix: Azure Cognitive Services
description: 情報を抽出したり書かれた言葉を理解したりできる AI をアプリケーションに統合する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: b779fa5963dcc14fce697c6b8ef77cfef1d252e2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132704944"
---
# <a name="what-is-azure-cognitive-service-for-language"></a>Azure Cognitive Service for Language とは

Azure Cognitive Service for Language は、テキストを理解し分析するための自然言語処理 (NLP) 機能を提供するクラウドベースのサービスです。 このサービスを使用すると、Web ベースの Language Studio、REST API、およびクライアント ライブラリを使用してインテリジェントなアプリケーションを構築できます。  

この言語サービスでは、Text Analytics、QnA Maker、および LUIS が統合され、いくつかの新機能も提供されます。 これらの機能は次のいずれかになります。
* 事前構成済み。つまり、この機能で使用される AI モデルはカスタマイズできません。 ユーザーは単にデータを送信し、機能の出力をアプリケーションで使用します。
* カスタマイズ可能。つまり、明確にデータに合うように、ツールを使用して AI モデルをトレーニングします。

## <a name="migrate-from-text-analytics-qna-maker-or-language-understanding"></a>Text Analytics、QnA Maker、または Language Understanding から移行しますか?

Azure Cognitive Services for Language には、Cognitive Services Text Analytics、QnA Maker、Language Understanding (LUIS) の 3 つの個別の言語サービスが統合されています。 これら 3 つのサービスを使用している場合は、新しい Azure Cognitive Services for Language に簡単に移行できます。 手順については、「[Azure Cognitive Services for Language への移行](concepts/migrate.md)」を参照してください。  

## <a name="available-features"></a>利用可能な機能

Azure Cognitive Service for Language には、次の機能が用意されています。

> [!div class="mx-tdCol2BreakAll"]
> |機能  |説明  | 配置オプション| 
> |---------|---------|---------|
> | [固有表現認識 (NER)](named-entity-recognition/overview.md)     | この事前構成済み機能では、いくつかの事前に定義されたカテゴリにわたる、テキスト内のエンティティが識別されます。        | • [Language Studio](language-studio.md) <br> • [REST API とクライアント ライブラリ](named-entity-recognition/quickstart.md) |
> | [個人を特定できる情報 (PII) の検出](personally-identifiable-information/overview.md)     | この事前構成済み機能では、アカウント情報など、機密情報のいくつかの事前に定義されたカテゴリにわたる、テキスト内のエンティティが識別されます。        | • [Language Studio](language-studio.md) <br> • [REST API とクライアント ライブラリ](named-entity-recognition/quickstart.md) |
> | [キー フレーズ抽出](key-phrase-extraction/overview.md)     | この事前構成済み機能では、非構造化テキストが評価され、入力ドキュメントごとに、テキスト内のキー フレーズと主要ポイントのリストが返されます。 | • [Language Studio](language-studio.md) <br> • [REST API とクライアント ライブラリ](key-phrase-extraction/quickstart.md) <br> • [Docker コンテナー](key-phrase-extraction/how-to/use-containers.md)  |
> |[エンティティ リンク設定](entity-linking/overview.md)    | この事前構成済み機能では、テキスト内で検出されたエンティティの ID が明確にされ、Wikipedia 上のエンティティへのリンクが提供されます。        | • [Language Studio](language-studio.md) <br> • [REST API とクライアント ライブラリ](entity-linking/quickstart.md) |
> | [Text Analytics for Health](text-analytics-for-health/overview.md)    | この事前構成済み機能では、臨床メモや医師のメモなど、非構造化医療テキストから情報が抽出されます。  | • [Language Studio](language-studio.md) <br> • [REST API とクライアント ライブラリ](text-analytics-for-health/quickstart.md) <br> • [Docker コンテナー](text-analytics-for-health/how-to/use-containers.md) |
> | [カスタム NER](custom-named-entity-recognition/overview.md)    | 指定した非構造化テキストを使用して、カスタム エンティティ カテゴリを抽出する AI モデルを構築します。 |  • [Language Studio](custom-named-entity-recognition/quickstart.md?pivots=language-studio) <br> • [REST API](custom-named-entity-recognition/quickstart.md?pivots=rest-api) |
> | [感情と意見を分析する](sentiment-opinion-mining/overview.md)     | この事前構成済み機能では、文とドキュメントのセンチメント ラベル ("*否定的*"、"*ニュートラル*"、"*肯定的*" など) が提供されます。 この機能を使用すると、製品やサービスの属性など、テキストに現れる単語に関連した意見に関するより詳細な情報を追加で提供できます。 |  • [Language Studio](language-studio.md) <br> • [REST API とクライアント ライブラリ](sentiment-opinion-mining/quickstart.md) <br> • [Docker コンテナー](sentiment-opinion-mining/how-to/use-containers.md)
> |[言語検出](language-detection/overview.md)    | この事前構成済み機能では、テキストが評価され、記述された言語が判断されます。 言語識別子と、分析の強度を示すスコアが返されます。        | • [Language Studio](language-studio.md) <br> • [REST API とクライアント ライブラリ](language-detection/quickstart.md) <br> • [Docker コンテナー](language-detection/how-to/use-containers.md) |
> |[カスタム テキスト分類 (プレビュー)](custom-classification/overview.md)    | 非構造化テキストを、定義したカスタム クラスに分類する AI モデルを構築します。         | • [Language Studio](custom-classification/quickstart.md?pivots=language-studio)<br> • [REST API](language-detection/quickstart.md?pivots=rest-api) |
> | [テキストの概要作成 (プレビュー)](text-summarization/overview.md)     | この事前構成済み機能では、ドキュメントの本質をまとめて伝える重要な文が抽出されます。 | • [Language Studio](language-studio.md) <br> • [REST API とクライアント ライブラリ](text-summarization/quickstart.md) |
> | [Conversational Language Understanding (プレビュー)](conversational-language-understanding/overview.md)   | アプリ、ボット、IoT デバイスで自然言語を理解できるようにするための AI モデルを構築します。 | • [Language Studio](conversational-language-understanding/quickstart.md)
> | [質問と回答](question-answering/overview.md)     | この事前構成済み機能では、FAQ、マニュアル、ドキュメントなどの半構造化コンテンツを使用して、テキスト入力から抽出された質問に対する回答が提供されます。 | • [Language Studio](language-studio.md) <br> • [REST API とクライアント ライブラリ](question-answering/quickstart/sdk.md) |

## <a name="tutorials"></a>チュートリアル

言語サービスの使用を開始する機会があったら、さまざまなシナリオを解決する方法を示すチュートリアルを試してみてください。

* [Power BI に保存されているテキストからキー フレーズを抽出する](key-phrase-extraction/tutorials/integrate-power-bi.md)
* [Power Automate を使用して Microsoft Excel 内の情報を並べ替える](named-entity-recognition/tutorials/extract-excel-information.md) 
* [Flask によるテキストの翻訳、センチメントの分析、音声の合成](../translator/tutorial-build-flask-app-translation-synthesis.md?context=%2fazure%2fcognitive-services%2flanguage-service%2fcontext%2fcontext)
* [キャンバス アプリで Cognitive Services を使用する](/powerapps/maker/canvas-apps/cognitive-services-api?context=/azure/cognitive-services/language-service/context/context)
* [FAQ ボットを作成する](question-answering/tutorials/bot-service.md)

## <a name="additional-code-samples"></a>その他のコード サンプル

次の言語については、その他のコード サンプルが GitHub にあります。

* [C#](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/textanalytics/Azure.AI.TextAnalytics/samples)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics/src/samples)
* [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/textanalytics/ai-text-analytics/samples)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/textanalytics/azure-ai-textanalytics/samples)

## <a name="deploy-on-premises-using-docker-containers"></a>Docker コンテナーを使用してオンプレミスにデプロイする 
言語サービス コンテナーを使用して、API 機能をオンプレミスにデプロイします。 これらの Docker コンテナーを使用すると、コンプライアンス、セキュリティ、またはその他の運用上の理由により、データにより近いところでサービスを使用できます。 言語サービスには、次のコンテナーが用意されています。

* [感情分析](sentiment-opinion-mining/how-to/use-containers.md)
* [言語検出](language-detection/how-to/use-containers.md)
* [キー フレーズ抽出](key-phrase-extraction/how-to/use-containers.md) 
* [Text Analytics for Health](text-analytics-for-health/how-to/use-containers.md)


## <a name="responsible-ai"></a>責任ある AI 

AI システムには、テクノロジだけでなく、それを使う人、それによって影響を受ける人、それがデプロイされる環境も含まれます。 システムでの責任ある AI の使用とデプロイについては、次の記事をご覧ください。

* [言語サービスの透明性に関する注意](/legal/cognitive-services/text-analytics/transparency-note)
* [統合と責任ある使用](/legal/cognitive-services/text-analytics/guidance-integration-responsible-use)
* [データ、プライバシー、セキュリティ](/legal/cognitive-services/text-analytics/data-privacy)