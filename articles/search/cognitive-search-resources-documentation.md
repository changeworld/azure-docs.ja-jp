---
title: AI エンリッチメントのドキュメント リンク
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search の AI エンリッチメント ワークロードに関する記事、チュートリアル、サンプル、およびブログ記事の注釈付きの一覧です。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 5fb1050fed2ab7318ad5b4ecafec7a96a9324575
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792068"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Azure Cognitive Search の AI エンリッチメントのドキュメント リソース

AI エンリッチメントは、Azure Cognitive Search のインデックス作成の機能であり、テキスト以外のソースや区別されていないテキスト内の潜在的な情報を検索するインデックスを作成して、それを Azure Cognitive Search でフルテキスト検索可能なコンテンツに変換します。

以下の記事では AI エンリッチメントについて詳しく説明しています。

## <a name="getting-started"></a>使用の開始
+ [Azure Cognitive Search での AI エンリッチメントの概要](cognitive-search-concept-intro.md)
+ [クイック スタート:ポータルで AI エンリッチメントを試す](cognitive-search-quickstart-blob.md)
+ [チュートリアル:AI によるインデックスのエンリッチ](cognitive-search-tutorial-blob.md)
+ [例:AI エンリッチメント用のカスタム スキルを作成する](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>使用方法に関するガイダンス
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [スキルセットで注釈を参照する方法](cognitive-search-concept-annotations-syntax.md)
+ [インデックスにフィールドをマップする方法](cognitive-search-output-field-mapping.md)
+ [画像の情報を処理し、抽出する方法](cognitive-search-concept-image-scenarios.md)
+ [Azure Cognitive Search インデックスを再構築する方法](search-howto-reindex.md)
+ [カスタム スキルのインターフェイスを定義する方法](cognitive-search-custom-skill-interface.md)
+ [トラブルシューティングのヒント](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>リファレンス

+ [組み込みのスキル](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ カスタム スキル
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [非推奨のスキル](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST API](https://docs.microsoft.com/rest/api/searchservice/)
  + [スキルセットの作成 (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [インデクサーの作成 (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>関連項目

+ [Azure Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice/)
+ [Azure Cognitive Search のインデクサー](search-indexer-overview.md)
+ [Azure Cognitive Search とは](search-what-is-azure-search.md)
