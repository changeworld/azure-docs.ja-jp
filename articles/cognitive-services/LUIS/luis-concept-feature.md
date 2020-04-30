---
title: フィーチャー - LUIS
titleSuffix: Azure Cognitive Services
description: フィーチャーを言語モデルに追加することで、ラベル付けまたは分類する必要がある入力を認識する方法についてのヒントが提供されます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: 5b8257e24cf52d01be8065d97db17fd685aa316d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531900"
---
# <a name="machine-learned-features"></a>機械学習の特徴

機械学習では、"_特徴_" がデータを区別するための特性または属性であり、システムを使ってその観察と学習を行います。 Language Understanding (LUIS) では、特徴によって、意図とエンティティに関する重要度が記述および説明されます。

[プレビューの LUIS ポータル](https://preview.luis.ai)における特徴は "_記述子_" です。意図またはエンティティを "_記述_" するために用いられるからです。

## <a name="features-_descriptors_-in-language-understanding"></a>Language Understanding における特徴 ("_記述子_")

特徴 (記述子とも呼ばれます) を使って、Language Understanding による発話例の識別を促進するためのヒントを記述します。 次のような機能が含まれます。

* 意図やエンティティに対する特徴としての語句一覧
* 意図やエンティティに対する特徴としてのエンティティ

特徴は、モデル分解のスキーマに不可欠な部分として考慮する必要があります。

## <a name="what-is-a-phrase-list"></a>語句一覧とは

語句一覧とは、識別しようとしている概念の識別を促進するための、単語、語句、数字、または他の文字の一覧のことです。 この一覧では、大文字と小文字が区別されません。

## <a name="when-to-use-a-phrase-list"></a>語句一覧を使用する条件

LUIS では、語句一覧を使ってコンテキストの検討と一般化が行われ、類似しているが完全なテキスト一致ではない項目が識別されます。 お使いの LUIS アプリで、新しい項目の一般化と識別を行えるようにする必要がある場合は、語句一覧を使用します。

新しいインスタンスを認識できるようにしたい場合 (たとえば、新しい連絡先の名前を認識する会議スケジューラや、新しい製品を認識するインベントリ アプリなど) は、機械学習エンティティから始めます。 次に、LUIS が類似した意味を持つ単語を見つけるのに役立つ、語句一覧を作成します。 この語句一覧によって、それらの単語の値に重要度が追加され、LUIS による例の識別が促進されます。

フレーズ リストはドメイン固有のボキャブラリのようなもので、意図とエンティティの両方の解釈の質を高めるうえで役に立ちます。

## <a name="considerations-when-using-a-phrase-list"></a>語句一覧を使用する場合の考慮事項

既定では、アプリ内のすべてのモデルに語句一覧が適用されます。 これは、すべての意図とエンティティにわたる語句一覧の場合に適切です。 分解可能性のためには、関連するモデルに対してのみ語句一覧を適用する必要があります。

語句一覧を作成し (既定ではグローバルに作成されます)、後ほど特定のモデルに対して記述子 (特徴) として適用すると、それは他のモデルからは削除されます。 この削除によって、その語句一覧に、その適用先のモデルとの関連性が追加されるため、そのモデルでの精度が向上します。

API では、`enabledForAllModels` フラグによって、このモデルのスコープが制御されます。

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>語句一覧の使用方法

使用する意図またはエンティティに、次のような重要な単語または語句が含まれている場合は、[語句一覧を作成します](luis-how-to-add-features.md)。

* 業界用語
* スラング
* 省略形
* 会社固有の言語
* 別の言語に属するが、アプリでよく使用される言語
* 発話の例におけるキーワードやフレーズ

考えられるすべての単語または語句を追加**しないでください**。 代わりに、一度にいくつかの単語または語句を追加してから、再トレーニングと公開を行ってください。 時間の経過と共に一覧が大きくなっていくと、一部の用語には多くの形式 (シノニム) があることがわかります。 これらは、別の一覧に分割します。

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>エンティティを特徴として使用する場合

意図またはエンティティのレベルで、エンティティを特徴として追加することができます。

### <a name="entity-as-a-feature-to-an-intent"></a>意図に対する特徴としてのエンティティ

意図にとってあるエンティティの検出が重要である場合は、そのエンティティを記述子 (特徴) としてその意図に追加します。

たとえば、意図がフライトを予約することであり、エンティティがチケット情報 (座席番号、出発地、目的地など) である場合、チケット情報のエンティティを検索することで、フライトを予約する意図の予測に重みが追加されます。

### <a name="entity-as-a-feature-to-another-entity"></a>別のエンティティに対する特徴としてのエンティティ

エンティティ (A) の検出が、エンティティ (B) の予測にとって重要である場合は、エンティティ (B) に対する特徴として、エンティティ (A) を追加する必要があります。

たとえば、番地のエンティティ (A) が検出された場合、番地 (A) を検索することで、配送先住所のエンティティ (B) の予測に重みが追加されます。

## <a name="best-practices"></a>ベスト プラクティス
[ベスト プラクティス](luis-concept-best-practices.md)を確認します。

## <a name="next-steps"></a>次のステップ

* 予測ランタイムにアプリ モデルを[拡張](schema-change-prediction-runtime.md)する
* [フィーチャーの追加](luis-how-to-add-features.md)に関するページで、フィーチャーを LUIS アプリに追加する方法の詳細を確認します。
