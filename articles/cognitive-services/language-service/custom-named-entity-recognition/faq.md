---
title: カスタム固有表現認識 (NER) の FAQ
titleSuffix: Azure Cognitive Services
description: カスタム固有表現認識を使うときによくあるご質問について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: c413e03e9106a3063e9cfdf26a1ab4b7fd7d5f25
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092524"
---
# <a name="frequently-asked-questions-for-custom-named-entity-recognition"></a>カスタム固有表現認識についてよくあるご質問

Azure Cognitive Service for Language でのカスタム NER に関する概念とシナリオについてのよくあるご質問に対する回答を示します。

## <a name="how-many-tagged-files-are-needed"></a>タグ付けされたファイルはどれくらい必要ですか?

一般に、タグ付けが正確に、一貫して、完全に行われていれば、[タグ付けされたデータ](how-to/tag-data.md)が多様で代表的であるほど、良い結果が得られます。 タグ付けされたエンティティの数に、すべてのモデルのパフォーマンスがよくなる決まった値はありません。 パフォーマンスは、スキーマと、エンティティのあいまいさに大きく依存します。 あいまいなエンティティの種類には、より多くのタグが必要です。 パフォーマンスは、タグ付けの質にも依存します。 エンティティあたりのタグ付けされたインスタンスの推奨数は 200 です。 

## <a name="what-are-the-service-limits"></a>サービスの制限とは何ですか。

詳細については、[サービスの制限に関する記事](service-limits.md)をご覧ください。

## <a name="what-to-do-if-my-model-scores-poorly"></a>モデルのスコアが低い場合はどうすればよいですか?

モデルの評価は、必ずしも包括的ではない場合があります。特定のクラスが見つからない場合、またはテスト セットでの出現が少なすぎる場合は特にそうです。 パフォーマンスを向上させ、より代表的なテスト セットにするには、タグ付けされたデータをモデルにさらに追加することを検討します。

## <a name="how-do-i-improve-model-performance"></a>モデルのパフォーマンスを向上させるにはどうすればよいですか?

[混同行列](how-to/view-model-evaluation.md)を調べて、スキーマのあいまいさを明らかにします。 次に、[テスト セットを調べて](how-to/improve-model.md)、予測されたエンティティとタグ付けされたエンティティを並べて表示すると、モデルのパフォーマンスをよりよく理解し、スキーマまたはタグの変更が必要かどうかを判断できます。  

## <a name="i-trained-my-model-but-i-cant-test-it"></a>モデルをトレーニングしましたが、テストできません

テストする前に、[モデルをデプロイする](quickstart.md#deploy-your-model)必要があります。 

## <a name="how-do-i-use-the-analyze-api"></a>Analyze API はどのように使えばよいですか?

モデルをデプロイした後、[ランタイム API を呼び出します](how-to/call-api.md)。 詳細については、[Analyze API のリファレンス](https://aka.ms/ct-runtime-swagger)をご覧ください。

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

データは Azure ストレージ アカウントにのみ格納され、カスタム NER にはトレーニングと評価の間にそこから読み取るアクセス権のみがあります。 

<!-- ## How to clone my project?

To clone your project you need to [export]() project assests and then [import]() them into a new project. -->

## <a name="next-steps"></a>次のステップ

[カスタム NER の概要](overview.md)
