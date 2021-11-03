---
title: Conversational Language Understanding でモデルをトレーニングおよび評価する方法
titleSuffix: Azure Cognitive Services
description: この記事では、モデルをトレーニングし、その評価の詳細を確認して改善します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: a5f4ccb11b759f735618ef991e7dfd85fd071035
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091135"
---
# <a name="train-and-evaluate-models"></a>モデルをトレーニングして評価する

[発話のタグ付け](./tag-utterances.md)が済んだら、モデルをトレーニングできます。 トレーニングとは、プロジェクトのトレーニング データの現在の状態を変換して、予測に使えるモデルを構築する行為です。 トレーニングを行うたびに、トレーニング インスタンスに名前を付けする必要があります。 

同じプロジェクト内で複数のモデルを作成してトレーニングできます。 ただし、特定のモデルを再トレーニングすると、最後の状態が上書きされます。

トレーニング時間は、オーケストレーション ワークフロー プロジェクトの処理のときは数秒で済みますが、発話数の[上限](../service-limits.md)に達したときは数時間かかります。 トレーニングの前に評価を有効にするオプションがあり、これによりモデルのパフォーマンスを確認できます。 

## <a name="train-model"></a>モデルのトレーニング

新しいモデル名を入力するか、 **[モデル名]** ドロップダウンから既存のモデルを選びます。 モデル名を追加した後、Enter キーを押します。 **[Run evaluation with training]\(トレーニングで評価を実行する\)** トグルを変更して、モデルを評価するかどうかを選びます。 有効にすると、タグ付けされた発話が 3 つの部分 (トレーニングに 80%、検証に 10%、テストに 10%) に分割されます。 その後、モデルの評価結果を確認できます。

:::image type="content" source="../media/train-model.png" alt-text="Conversational Language Understanding プロジェクトの [モデルのトレーニング] ページのスクリーンショット。" lightbox="../media/train-model.png":::

**[トレーニング]** ボタンをクリックして、トレーニングが完了するのを待ちます。 モデルの詳細の表示ページに、モデルのトレーニング状態が表示されます。

## <a name="evaluate-model"></a>モデルの評価

トレーニング ステップでの評価を有効にした場合は、モデルのトレーニングが完了したら、モデルの詳細を見て、テスト セットに対するパフォーマンスを確認できます。 モデルのパフォーマンスを観察することを、評価と呼びます。 テスト セットは発話の 20% で構成され、この分割はトレーニング前にランダムに行われます。 テスト セットは、トレーニング プロセス中にモデルに導入されなかったデータで構成されます。 評価プロセスが完了するには、トレーニング セットに少なくとも 10 個の発話が必要です。

**[view model details]\(モデルの詳細の表示\)** ページでは、すべてのモデル、現在のトレーニング状態、最後にトレーニングされた日付を見ることができます。

:::image type="content" source="../media/model-page-1.png" alt-text="Conversational Language Understanding プロジェクトのモデルの詳細ページを示すスクリーンショット。" lightbox="../media/model-page-1.png":::

* 詳細については、モデル名をクリックしてください。 モデル名をクリックできるのは、前もって評価を有効にしてある場合のみです。 
* **[概要]** セクションでは、選択したオプションに基づいて、全体的な意図またはエンティティについてのマクロな精度、リコール、F1 スコアを確認できます。 
* **[Intents]\(意図\)** タブと **[エンティティ]** タブでは、個別の意図またはエンティティについてのミクロな精度、リコール、F1 スコアを確認できます。

> [!NOTE]
> モデルに含まれる意図またはエンティティがここに表示されない場合は、テスト セットに使用されたどの発話にもそれが含まれなかったためです。

画面の上部にある **[Test set confusion matrix]\(テスト セットの混同行列\)** タブをクリックすると、意図とエンティティの [混同行列](../concepts/evaluation-metrics.md#confusion-matrix)を表示できます。 

## <a name="next-steps"></a>次のステップ
* [モデル評価メトリック](../concepts/evaluation-metrics.md)
* [モデルをデプロイしてクエリを実行する](./deploy-query-model.md)
