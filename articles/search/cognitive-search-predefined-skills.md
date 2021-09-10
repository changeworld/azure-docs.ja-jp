---
title: インデックス作成中の組み込みのテキストとイメージの処理
titleSuffix: Azure Cognitive Search
description: データ抽出、自然言語、画像処理に関するスキルは、Azure Cognitive Search エンリッチメント パイプラインの生コンテンツにセマンティクスと構造を追加します。
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 161813b7da42a5697fdb595e6a7050923e28fca2
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038894"
---
# <a name="built-in-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>インデックス作成時にテキストとイメージを処理するための組み込みスキル (Azure Cognitive Search)

この記事では、Azure Cognitive Search で提供され、生の非構造化テキストやイメージ ファイルからコンテンツと構造を抽出するために[スキルセット](cognitive-search-working-with-skillsets.md)に含めることのできるスキルについて説明します。 *スキル* とは、コンテンツを何らかの方法で変換するアトミック操作のことです。 スキルは、多くの場合はテキストを認識または抽出する操作ですが、既に作成されているエンリッチメントを再形成するユーティリティ スキルの場合もあります。 通常、出力はテキストベースで、フル テキスト クエリで使用できます。 

## <a name="built-in-skills"></a>組み込みのスキル

組み込みのスキルは、Microsoft によって事前にトレーニングされたモデルに基づいています。そのモデルをユーザーが独自のトレーニング データを使用してトレーニングすることはできません。 Cognitive Resources API を呼び出すスキルは、それらのサービスに対して依存関係があり、[リソースをアタッチする](cognitive-search-attach-cognitive-services.md)ときに Cognitive Services の従量課金制の料金が請求されます。 その他のスキルは、Azure Cognitive Search によって課金されるか、無料で提供されるユーティリティ スキルです。

次の表で、組み込みのスキルを一覧で表示して説明します。

| OData 型  | 説明 | 課金元 |
|-------|-------------|-------------|
|[Microsoft.Skills.Text.CustomEntityLookupSkill](cognitive-search-skill-custom-entity-lookup.md) | ユーザーが定義したカスタムの単語と語句のリストからテキストを検索します。| Azure Cognitive Search ([価格](https://azure.microsoft.com/pricing/details/search/)) |
| [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md) | このスキルでは、トレーニング済みモデルを使用し、語句の配置、言語規則、他の語句との近さ、ソース データ内での語句の不自然さの程度に基づいて、重要な語句を検出します。 | Cognitive Services ([価格](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | このスキルでは、トレーニング済みモデルを使用し、使用されている言語を検出します (ドキュメントごとに 1 つの言語 ID)。 同じテキスト セグメント内で複数の言語が使用されている場合、出力は、主に使用されている言語の LCID になります。 | Cognitive Services ([価格](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | フィールドのコレクションからのテキストを 1 つのフィールドに統合します。  | 該当なし |
| [Microsoft.Skills.Text.V3.EntityLinkingSkill](cognitive-search-skill-entity-linking-v3.md) | このスキルでは、事前トレーニング済みのモデルを使用し、認識されたエンティティに対して Wikipedia の記事へのリンクを生成します。 | Cognitive Services ([価格](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) | このスキルでは、トレーニング済みモデルを使用し、`"Person"`、`"Location"`、`"Organization"`、`"Quantity"`、`"DateTime"`、`"URL"`、`"Email"`、`"PersonType"`、`"Event"`、`"Product"`、`"Skill"`、`"Address"`、`"Phone Number"` および `"IP Address"`の各フィールドのカテゴリの固定したセットに対してエンティティを確立します。 | Cognitive Services ([価格](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)  | このスキルは、事前トレーニングされたモデルを使用して、特定のテキストから個人情報を抽出します。 また、このスキルには、テキスト内で検出された個人情報エンティティをマスクするためのさまざまなオプションも用意されています。  | Cognitive Services ([価格](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md)  | このスキルでは、トレーニング済みのモデルを使用し、レコードごとに文章とドキュメントレベルでサービスによって検出された最も高い信頼度スコアに基づいて、センチメント ラベル ("ネガティブ"、"中立"、"ポジティブ" など) を割り当てます。 | Cognitive Services ([価格](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | コンテンツを徐々に強化または拡張できるように、テキストをページに分割します。 | 該当なし |
| [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md) | このスキルは、正規化やローカライズのユース ケース向けに、トレーニング済みモデルを使用して入力テキストをさまざまな言語に翻訳します。 | Cognitive Services ([価格](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | このスキルでは、画像検出アルゴリズムを使用して画像の内容を識別し、テキストの説明を生成します。 | Cognitive Services ([価格](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | 光学式文字認識。 | Cognitive Services ([価格](https://azure.microsoft.com/pricing/details/cognitive-services/)) |
| [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md) | 条件に基づくフィルター処理、既定値の割り当て、データのマージを行うことができます。 | 該当なし |
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | エンリッチメント パイプライン内のファイルからコンテンツを抽出します。 | Azure Cognitive Search ([価格](https://azure.microsoft.com/pricing/details/search/))
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | 出力を複合型 (フル ネーム、複数行の住所、または姓と個人識別子の組み合わせに使用される可能性があるマルチパート データ型) にマップします。 | 該当なし |

## <a name="custom-skills"></a>カスタム スキル

[カスタム スキル](cognitive-search-custom-skill-web-api.md)は、設計、開発、および Web へのデプロイを行うモジュールです。 その後、スキルセット内からカスタム スキルとしてモジュールを呼び出すことができます。

| 型  | 説明 | 課金元 |
|-------|-------------|-------------|
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | カスタム Web API に対して HTTP 呼び出しを行うことで、AI エンリッチメント パイプラインを拡張することができます | ソリューションで従量制課金の Azure サービスを使用していない場合は、なし |
| [Microsoft.Skills.Custom.AmlSkill](cognitive-search-aml-skill.md) | Azure Machine Learning モデルで AI エンリッチメント パイプラインを拡張できるようにします | ソリューションで従量制課金の Azure サービスを使用していない場合は、なし |

カスタム スキルの作成ガイダンスについては、[カスタム インターフェイスの定義](cognitive-search-custom-skill-interface.md)と [AI エンリッチメント用のカスタム スキルを作成する](cognitive-search-create-custom-skill-example.md)方法に関する記事を参照してください。

## <a name="see-also"></a>関連項目

+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [カスタム スキルのインターフェイスの定義](cognitive-search-custom-skill-interface.md)
+ [チュートリアル:AI によるインデックスのエンリッチ](cognitive-search-tutorial-blob.md)
