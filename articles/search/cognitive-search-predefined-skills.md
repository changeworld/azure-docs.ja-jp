---
title: 定義済みのデータ抽出、自然言語、画像処理のスキル (Azure Search) | Microsoft Docs
description: データ抽出、自然言語、画像処理に関するコグニティブ スキルは、Azure Seach パイプラインの生コンテンツにセマンティクスと構造を追加します。
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: c342eca8f27db713e139c187147abddd80eb854e
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734536"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>コンテンツ エンリッチメントのための定義済みスキル (Azure Search)

この記事では、Azure Search で提供されるコグニティブ スキルについて説明します。 *コグニティブ スキル*は、何らかの方法でコンテンツを変換する操作です。 多くの場合、これはデータの抽出や構造の推論を行うコンポーネントであるため、入力データの解釈を強化することになります。 ほとんどの場合、出力はテキスト ベースです。 *スキルセット*は、エンリッチメント パイプラインを定義するスキルのコレクションです。 

> [!NOTE]
> コグニティブ検索はパブリック プレビュー段階です。 スキルセットの実行および画像の抽出と正規化は、現在無料で提供されています。 これらの機能の価格は後日発表される予定です。 

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
+ [チュートリアル: コグニティブ検索によって強化されたインデックス作成](cognitive-search-tutorial-blob.md)