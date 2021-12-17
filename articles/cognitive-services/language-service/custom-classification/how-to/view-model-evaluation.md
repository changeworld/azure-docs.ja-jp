---
title: カスタム分類モデルの評価を表示する - Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: カスタム分類モデルの評価スコアを表示する方法について説明します
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 6b7d56ee6eb7280b285442a2c82001b100d1cf06
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054374"
---
# <a name="view-the-model-evaluation"></a>モデルの評価を表示する

モデルの評価の確認は、カスタム分類モデルを開発する際の重要な手順です。 これは、モデルのパフォーマンスを知るのに役立ち、運用環境で使われた場合にどのような性能になるか確認することができます。 


## <a name="prerequisites"></a>前提条件

モデルをトレーニングする前に、次のものが必要です。
* Azure BLOB ストレージ アカウントが構成されている[カスタム分類プロジェクト](create-project.md)。 
* ストレージ アカウントに[アップロードされた](create-project.md#prepare-training-data)テキスト データ。
* [タグ付けされたデータ](tag-data.md)
* 正常に[トレーニングされたモデル](train-model.md)

詳しくは、[アプリケーション開発ライフサイクル](../overview.md#project-development-lifecycle)に関する記事をご覧ください。

## <a name="model-evaluation"></a>モデルの評価

評価プロセスでは、トレーニング済みのモデルを使って、テスト セット内のファイルのユーザー定義クラスを予測し、それらを指定されたデータ タグと比較します。 テスト セットは、トレーニング プロセス中にモデルに導入されなかったデータで構成されます。 

## <a name="view-the-model-details-using-language-studio"></a>Language Studio を使ってモデルの詳細を表示する

1. [Language Studio](https://aka.ms/languageStudio) でプロジェクトのページに移動します。
    1. Language Studio の **[Classify text]\(テキストを分類する\)** というラベルが付いたセクションを探します。
    2. **[Custom text classification]\(カスタム テキスト分類\)** を選びます。 

2. 左側のメニューから **[View model details]\(モデルの詳細を表示する\)** を選びます。

3. **[状態]** 列でモデルのトレーニング状態を確認し、 **[F1 スコア]** 列でモデルの F1 スコアを確認します。

    :::image type="content" source="../media/model-details-1.png" alt-text="[View model details]\(モデルの詳細を表示する\) ボタン" lightbox="../media/model-details-1.png":::

1. 詳細については、モデル名をクリックしてください。

2. **[概要]** セクションで **モデル レベル** の評価メトリックを確認でき、 **[Class performance metrics]\(クラス パフォーマンス メトリック\)** セクションで **クラス レベル** の評価メトリックを確認できます。 詳細については、[評価メトリック](../concepts/evaluation.md#model-level-and-class-level-evaluation-metrics)に関するページを参照してください。

    :::image type="content" source="../media/model-details-2.png" alt-text="モデル パフォーマンス メトリック" lightbox="../media/model-details-2.png":::

> [!NOTE]
> ここにすべてのクラスが表示されていない場合は、テスト セットにそのクラスのタグ付きファイルがないためです。

モデルの混同行列は、 **[Test set confusion matrix]\(テスト セットの混同行列\)** にあります。

> [!NOTE]
> 現在、混同マトリックスは複数のラベル分類プロジェクトではサポートされていません。

**単一ラベルの分類**

:::image type="content" source="../media/conf-matrix-single.png" alt-text="単一クラス分類の混同行列" lightbox="../media/conf-matrix-single.png":::

<!-- **Multiple Label Classification**

:::image type="content" source="../media/conf-matrix-multi.png" alt-text="Confusion matrix for multiple class classification" lightbox="../media/conf-matrix-multi.png"::: -->

## <a name="next-steps"></a>次のステップ

モデルのパフォーマンスを確認する際に、使用される[評価メトリック](../concepts/evaluation.md)について説明します。 モデルのパフォーマンスを向上させる必要があるかどうかを確認したら、[モデルの改善](improve-model.md)を始められます。
