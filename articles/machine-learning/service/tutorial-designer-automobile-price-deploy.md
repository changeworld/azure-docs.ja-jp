---
title: チュートリアル:デザイナーで機械学習モデルをデプロイする
titleSuffix: Azure Machine Learning
description: このチュートリアルでは、Azure Machine Learning デザイナー (プレビュー) で予測分析ソリューションを構築する方法について説明します。 ドラッグ アンド ドロップ モジュールを使用して、機械学習モデルのトレーニング、スコア付け、およびデプロイを行います。
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 31b06f3ad102f39d1a9f95dee2bd98b5d0a3b310
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483319"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>チュートリアル:デザイナー (プレビュー) で機械学習モデルをデプロイする
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

[チュートリアルのパート 1](tutorial-designer-automobile-price-train-score.md) で作成した予測モデルを、他のユーザーが使用できるようデプロイしてみましょう。 パート 1 では、モデルをトレーニングしました。 ここでは、ユーザー入力に基づいて新しい予測を生成しましょう。 チュートリアルのこのパートでは、次のことを行います。

> [!div class="checklist"]
> * リアルタイム推論パイプラインを作成する。
> * 推論クラスターを作成する。
> * リアルタイム エンドポイントをデプロイする。
> * リアルタイム エンドポイントをテストする。

## <a name="prerequisites"></a>前提条件

[チュートリアルのパート 1](tutorial-designer-automobile-price-train-score.md) を完了して、デザイナーで機械学習モデルをトレーニングしてスコア付けする方法を学習します。

## <a name="create-a-real-time-inference-pipeline"></a>リアルタイム推論パイプラインを作成する

パイプラインをデプロイするためには、まずトレーニング パイプラインをリアルタイム推論パイプラインに変換する必要があります。 このプロセスにより、トレーニング モジュールが削除され、推論要求のための入力と出力が追加されます。

### <a name="create-a-real-time-inference-pipeline"></a>リアルタイム推論パイプラインを作成する

1. パイプライン キャンバス上で **[Create inference pipeline]\(推論パイプラインの作成\)**  >  **[Real-time inference pipeline]\(リアルタイム推論パイプライン\)** の順に選択します。

    これでパイプラインは次のようになっているはずです。 

   ![デプロイ準備が完了したパイプラインの予測される構成を示すスクリーンショット](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    **[Create inference pipeline]\(推論パイプラインの作成\)** を選択すると、以下のようないくつかの処理が行われます。
    
    * トレーニング済みのモデルは、モジュール パレットに **[Dataset]\(データセット\)** モジュールとして格納されます。 これは **[マイ データセット]** で見つかります。
    * **[Train Model]\(モデルのトレーニング\)** や **[Split Data]\(データの分割\)** などのトレーニング モジュールは削除されます。
    * 保存したトレーニング済みのモデルが再びパイプラインに追加されます。
    * **[Web Service Input]\(Web サービスの入力\)** モジュールと **[Web Service Output]\(Web サービスの出力\)** モジュールが追加されます。 ユーザー データがモデルに入力される位置と、データが返される位置が、これらのモジュールによって示されます。

    > [!NOTE]
    > *トレーニング パイプライン*は、パイプライン キャンバスの上部にある新しいタブに保存されます。 また、デザイナーでは発行済みのパイプラインとして検出される可能性があります。
    >

1. **[Run]\(実行\)** を選択し、パート 1 で使用したものと同じコンピューティング先と実験を使用します。

1. **[Score Model]\(モデルのスコア付け\)** モジュールを選択します。

1. [プロパティ] ウィンドウで、 **[出力]**  >  **[Visualize]\(視覚化\)** の順に選択し、引き続きモデルが正しく機能していることを確認します。 元のデータが、予測価格 ("Scored Labels" (スコア付けラベル)) と共に表示されます。

1. **[デプロイ]** を選択します。

## <a name="create-an-inferencing-cluster"></a>推論クラスターを作成する

表示されたダイアログ ボックスで、既存の Azure Kubernetes Service (AKS) クラスターを選択して自分のモデルをデプロイできます。 既存の AKS クラスターがない場合は、次の手順を使用して作成してください。

1. 表示されたダイアログ ボックスの **[Compute]\(コンピューティング\)** を選択して **[Compute]\(コンピューティング\)** ページに移動します。

1. ナビゲーション リボンで、 **[Inference Clusters]\(推論クラスター\)**  >  **[+ New]\(+ 新規\)** の順に選択します。

    ![推論クラスターの新規作成ペインに移動する方法を示すスクリーンショット](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)

1. 推論クラスター ウィンドウで、新しい Kubernetes サービスを構成します。

1. **[Compute name]\(コンピューティング名\)** に「*aks-compute*」と入力します。
    
1. **[Region]\(リージョン\)** には、使用できる近くのリージョンを選択します。

1. **作成** を選択します。

    > [!NOTE]
    > 新しい AKS サービスの作成には約 15 分かかります。 プロビジョニングの状態は、 **[Inference Clusters]\(推論クラスター\)** ページで確認できます。
    >

## <a name="deploy-the-real-time-endpoint"></a>リアルタイム エンドポイントをデプロイする

AKS サービスのプロビジョニングが完了したら、リアルタイム推論パイプラインに戻ってデプロイを完了します。

1. キャンバスの上にある **[Deploy]\(デプロイ\)** を選択します。

1. **[Deploy new real-time endpoint]\(新しいリアルタイム エンドポイントのデプロイ\)** を選択します。 

1. 作成した AKS クラスターを選択します。

1. **[デプロイ]** を選択します。

    ![新しいリアルタイム エンドポイントの設定方法を示すスクリーンショット](./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png)

    デプロイが完了すると、キャンバスの上に成功通知が表示されます。 これには数分かかる可能性があります。

## <a name="test-the-real-time-endpoint"></a>リアルタイム エンドポイントをテストする

デプロイが完了したら、 **[Endpoints]\(エンドポイント\)** ページに移動して、リアルタイム エンドポイントをテストできます。

1. **[Endpoints]\(エンドポイント\)** ページで、デプロイ済みのエンドポイントを選択します。

    ![リアルタイム エンドポイントのタブを示すスクリーンショット (強調表示されているのは、先ほど作成したエンドポイント)](./media/tutorial-designer-automobile-price-deploy/endpoints.png)

1. **[テスト]** を選択します。

1. テスト データを手動で入力するか、自動入力されたサンプル データを使用し、 **[テスト]** を選択します。

    ポータルからエンドポイントにテスト要求が送信されて、その結果が表示されます。 価格値は入力データに対して生成されますが、予測値を生成するためには使用されません。

    ![リアルタイム エンドポイントのテスト方法を示すスクリーンショット (強調表示されているのは価格のスコア付けラベル)](./media/tutorial-designer-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、デザイナーで機械学習モデルを作成、デプロイ、および使用する際の主な手順を学習しました。 デザイナーを使用して他の種類の問題を解決する方法の詳細については、他のサンプル パイプラインを参照してください。

> [!div class="nextstepaction"]
> [クレジット リスク分類のサンプル](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
