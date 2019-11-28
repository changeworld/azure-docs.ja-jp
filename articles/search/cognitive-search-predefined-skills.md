---
title: インデックス作成中の組み込みのテキストとイメージの処理
titleSuffix: Azure Cognitive Search
description: データ抽出、自然言語、画像処理に関するコグニティブ スキルは、Azure Cognitive Search パイプラインの生コンテンツにセマンティクスと構造を追加します。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f419b19b576ebf6f7f853b41128908487f047df6
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113368"
---
# <a name="built-in-cognitive-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>インデックス作成中のテキストとイメージの処理用の組み込みのコグニティブ スキル (Azure Cognitive Search)

この記事では、Azure Cognitive Search で提供される、コンテンツと構造を抽出するスキルセットに含めることのできるコグニティブ スキルについて説明します。 *コグニティブ スキル*は、なんらかの方法でコンテンツを変換するモジュールまたは操作です。 多くの場合、これはデータの抽出や構造の推論を行うコンポーネントであるため、入力データの解釈を強化することになります。 ほとんどの場合、出力はテキスト ベースです。 *スキルセット*は、エンリッチメント パイプラインを定義するスキルのコレクションです。 

> [!NOTE]
> 処理の頻度を増やす、ドキュメントを追加する、または AI アルゴリズムを追加することによってスコープを拡大する場合は、[課金対象の Cognitive Services リソースをアタッチする](cognitive-search-attach-cognitive-services.md)必要があります。 Cognitive Services の API を呼び出すとき、および Azure コグニティブ検索のドキュメント解析段階の一部として画像抽出するときに、料金が発生します。 ドキュメントからのテキストの抽出には、料金はかかりません。
>
> 組み込みスキルの実行は、既存の [Cognitive Services の従量課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。 画像抽出の価格は、[Azure Cognitive Search の価格ページ](https://go.microsoft.com/fwlink/?linkid=2042400)で説明されています。


## <a name="built-in-skills"></a>組み込みのスキル

いくつかのスキルは、何を消費または生成するかに関して柔軟性があります。 通常、ほとんどのスキルはトレーニング済みモデルに基づいています。これは、独自のトレーニング データを使用したモデルのトレーニングはできないことを意味します。 次の表で、Microsoft が提供するスキルを列挙し、それらについて説明します。 

| スキル | 説明 |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | このスキルでは、トレーニング済みモデルを使用し、語句の配置、言語規則、他の語句との近さ、ソース データ内での語句の不自然さの程度に基づいて、重要な語句を検出します。 |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | このスキルでは、トレーニング済みモデルを使用し、使用されている言語を検出します (ドキュメントごとに 1 つの言語 ID)。 同じテキスト セグメント内で複数の言語が使用されている場合、出力は、主に使用されている言語の LCID になります。|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | フィールドのコレクションからのテキストを 1 つのフィールドに統合します。  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | このスキルでは、トレーニング済みモデルを使用し、人、場所、組織、メール、URL、日時のフィールドから成るカテゴリの固定したセットに対してエンティティを確立します。 |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | このスキルでは、トレーニング済みモデルを使用し、レコード単位で肯定的または否定的センチメントのスコア付けを行います。 スコアは 0 と 1 の間です。 センチメントを検出できない null の場合と、中立と見なさるテキストの両方に対して、中立のスコアが生成されます。  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | コンテンツを徐々に強化または拡張できるように、テキストをページに分割します。 |
| [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md) | このスキルは、正規化やローカライズのユース ケース向けに、トレーニング済みモデルを使用して入力テキストをさまざまな言語に翻訳します。 |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | このスキルでは、画像検出アルゴリズムを使用して画像の内容を識別し、テキストの説明を生成します。 |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | 光学式文字認識。 |
| [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md) | 条件に基づくフィルター処理、既定値の割り当て、データのマージを行うことができます。|
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | エンリッチメント パイプライン内のファイルからコンテンツを抽出します。 |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | 出力を複合型 (フル ネーム、複数行の住所、または姓と個人識別子の組み合わせに使用される可能性があるマルチパート データ型) にマップします。 |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | カスタム Web API に対して HTTP 呼び出しを行うことで、AI エンリッチメント パイプラインを拡張することができます |


[カスタム スキル](cognitive-search-custom-skill-web-api.md)作成のガイダンスについては、[カスタム インターフェイスの定義方法](cognitive-search-custom-skill-interface.md)と[AI エンリッチメント用のカスタム スキルを作成する](cognitive-search-create-custom-skill-example.md)方法に関する記事を参照してください。

## <a name="see-also"></a>関連項目

+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [カスタム スキルのインターフェイスの定義](cognitive-search-custom-skill-interface.md)
+ [チュートリアル:AI によるインデックスの強化](cognitive-search-tutorial-blob.md)
