---
title: 組み込みのデータ抽出、自然言語、画像処理 - Azure Search
description: データ抽出、自然言語、画像処理に関するコグニティブ スキルは、Azure Search パイプラインの生コンテンツにセマンティクスと構造を追加します。
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: bc1353ffb4514622ce0ef6e5c3ced76adc7f999f
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314790"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>コンテンツ エンリッチメントのための定義済みスキル (Azure Search)

この記事では、Azure Search で提供されるコグニティブ スキルについて説明します。 *コグニティブ スキル*は、何らかの方法でコンテンツを変換する操作です。 多くの場合、これはデータの抽出や構造の推論を行うコンポーネントであるため、入力データの解釈を強化することになります。 ほとんどの場合、出力はテキスト ベースです。 *スキルセット*は、エンリッチメント パイプラインを定義するスキルのコレクションです。 

> [!NOTE]
> 2018 年 12 月 21 日から、Azure Search のスキルセットに Cognitive Services リソースを関連付けることができるようになります。 これにより、スキルセットの実行への課金を開始できます。 この日付には、ドキュメント クラッキング ステージの一部として画像抽出への課金も開始します。 ドキュメントからのテキスト抽出は、引き続き追加コストなしで提供されます。
>
> 組み込みスキルの実行は、既存の [Cognitive Services の従来課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。 画像抽出の価格はプレビュー価格で課金され、[Azure Search 価格のページ](https://go.microsoft.com/fwlink/?linkid=2042400)で説明されています。 [詳細情報](cognitive-search-attach-cognitive-services.md)。

## <a name="predefined-skills"></a>定義済みのスキル

いくつかのスキルは、何を消費または生成するかに関して柔軟性があります。 通常、ほとんどのスキルはトレーニング済みモデルに基づいています。これは、独自のトレーニング データを使用したモデルのトレーニングはできないことを意味します。 カスタム スキル作成のガイダンスについては、「[カスタム インターフェイスの定義方法](cognitive-search-custom-skill-interface.md)」と「[例: カスタム スキルの作成](cognitive-search-create-custom-skill-example.md)」をご覧ください。 次の表で、Microsoft が提供するスキルを列挙し、それらについて説明します。 

| スキル | 説明 |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | このスキルでは、トレーニング済みモデルを使用し、語句の配置、言語規則、他の語句との近さ、ソース データ内での語句の不自然さの程度に基づいて、重要な語句を検出します。 |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | このスキルでは、トレーニング済みモデルを使用し、使用されている言語を検出します (ドキュメントごとに 1 つの言語 ID)。 同じテキスト セグメント内で複数の言語が使用されている場合、出力は、主に使用されている言語の LCID になります。|
| [Microsoft.Skills.Text.MergerSkill](cognitive-search-skill-textmerger.md) | フィールドのコレクションからのテキストを 1 つのフィールドに統合します。  |
| [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md) | このスキルでは、トレーニング済みモデルを使用し、人、場所、組織から成るカテゴリの固定したセットに対してエンティティを確立します。 |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | このスキルでは、トレーニング済みモデルを使用し、レコード単位で肯定的または否定的センチメントのスコア付けを行います。 スコアは 0 と 1 の間です。 センチメントを検出できない null の場合と、中立と見なさるテキストの両方に対して、中立のスコアが生成されます。  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | コンテンツを徐々に強化または拡張できるように、テキストをページに分割します。 |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | このスキルでは、画像検出アルゴリズムを使用して画像の内容を識別し、テキストの説明を生成します。 |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | 光学式文字認識。 |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | 出力を複合型 (フル ネーム、複数行の住所、または姓と個人識別子の組み合わせに使用される可能性があるマルチパート データ型) にマップします。 |

## <a name="see-also"></a>関連項目

+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [カスタム スキルのインターフェイスの定義](cognitive-search-custom-skill-interface.md)
+ [チュートリアル:コグニティブ検索によって強化されたインデックス作成](cognitive-search-tutorial-blob.md)