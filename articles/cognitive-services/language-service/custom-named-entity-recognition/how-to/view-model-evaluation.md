---
title: カスタム固有表現認識 (NER) モデルの評価を表示する
titleSuffix: Azure Cognitive Services
description: カスタム固有表現認識 (NER) モデルの評価スコアを表示する方法について説明します
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 62724137bd02f8c3ff58665ad92a4346eddc0b53
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092520"
---
# <a name="view-the-models-evaluation-and-details"></a>モデルの評価と詳細を表示する

モデルのトレーニングが完了した後、モデルの詳細を表示し、そのモデルがテスト セットに対してどの程度適切に実行されたかを確認できます。このテスト セットには、[トレーニング](train-model.md#data-split)中に作成されるデータの 10% がランダムに含まれます。 テスト セットは、トレーニング プロセス中にモデルに導入されなかったデータで構成されます。 評価プロセスが完了するには、データセットに少なくとも 10 個のファイルが必要です。 また、[トレーニング済みモデル](train-model.md)を含む[カスタム NER プロジェクト](../quickstart.md)も必要です。

## <a name="prerequisites"></a>前提条件

* 構成済みの Azure Blob Storage アカウントで正常に[作成されたプロジェクト](create-project.md)
    * ストレージ アカウントに[アップロードされた](create-project.md#prepare-training-data)テキスト データ。
* [タグ付けされたデータ](tag-data.md)
* [正常にトレーニングされたモデル](train-model.md)

詳しくは、「[アプリケーション開発ライフサイクル](../overview.md#application-development-lifecycle)」を参照してください。

## <a name="view-the-models-evaluation-details"></a>モデルの評価の詳細を表示する

1. [Language Studio](https://aka.ms/languageStudio) でプロジェクトのページに移動します。
    1. Language Studio で **[情報の抽出]** というラベルが付いたセクションを探します。
    2. **[Custom named entity extraction]\(カスタム固有表現抽出\)** を選択します。

2. 画面の左側にあるメニューから **[モデルの詳細の表示]** を選択します。

3. **[状態]** 列でモデルのトレーニング状態を確認し、 **[F1 スコア]** 列でモデルの F1 スコアを確認します。 モデル名をクリックすると、詳細を確認できます。

4. **[概要]** で **モデル レベル** の評価メトリックを確認でき、 **[Entity performance metrics]\(エンティティ パフォーマンス メトリック\)** で **エンティティ レベル** の評価メトリックを確認できます。 モデルの混同行列は、 **[Test set confusion matrix]\(テスト セットの混同行列\)** にあります
    
    > [!NOTE]
    > ここに表示されるエンティティの一部が見つからない場合、それは、そのエンティティがテスト セット内のどのファイルにも含まれていなかったためです。

    :::image type="content" source="../media/model-details.png" alt-text="Language Studio のモデル パフォーマンス メトリックのスクリーンショット" lightbox="../media/model-details.png":::

## <a name="next-steps"></a>次のステップ

* モデルの評価を確認した後、[モデルの改善](improve-model.md)を開始できます。
* [評価で使用されるメトリック](../concepts/evaluation-metrics.md)について学習してください。 
