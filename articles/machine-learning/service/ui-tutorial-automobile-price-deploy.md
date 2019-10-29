---
title: チュートリアル:ビジュアル インターフェイスで機械学習モデルをデプロイする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning のビジュアル インターフェイスで予測分析ソリューションを構築する方法について説明します。 ドラッグ アンド ドロップ モジュールを使用して、機械学習モデルのトレーニング、スコア付け、およびデプロイを行います。
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 10/22/2019
ms.openlocfilehash: 6f8717f70a2cb03a7fd683cfe61f1198461f4305
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792668"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>チュートリアル:ビジュアル インターフェイスで機械学習モデルをデプロイする

[チュートリアルのパート 1](ui-tutorial-automobile-price-train-score.md) で作成した予測モデルを他のユーザーが使用できるようにするには、モデルをリアルタイム エンドポイントにデプロイします。 パート 1 では、モデルをトレーニングしました。 ここでは、ユーザー入力に基づいて新しい予測を生成しましょう。 チュートリアルのこのパートでは、次のことを行います。

> [!div class="checklist"]
> * リアルタイム エンドポイントをデプロイする
> * 推論クラスターを作成する
> * リアルタイム エンドポイントをテストする

## <a name="prerequisites"></a>前提条件

[チュートリアルのパート 1](ui-tutorial-automobile-price-train-score.md) を完了して、ビジュアル インターフェイスで機械学習モデルをトレーニングしてスコア付けする方法を学習します。

## <a name="deploy-a-real-time-endpoint"></a>リアルタイム エンドポイントをデプロイする

パイプラインをデプロイするためには、次の作業が必要となります。

1. トレーニング パイプラインをリアルタイム推論パイプラインに変換します。これにより、トレーニング モジュールが削除され、推論要求のための入力と出力が追加されます。
1. 推論パイプラインをデプロイします。

### <a name="create-a-real-time-inference-pipeline"></a>リアルタイム推論パイプラインを作成する

1. パイプライン キャンバスの上部で **[Create inference pipeline]\(推論パイプラインの作成\)**  >  **[Real-time inference pipeline]\(リアルタイム推論パイプライン\)** の順に選択します。

    **[Create inference pipeline]\(推論パイプラインの作成\)** を選択すると、以下のようないくつかの処理が行われます。
    
    * トレーニング済みのモデルは、モジュール パレットに **[Dataset]\(データセット\)** モジュールとして格納されます。 これは **[マイ データセット]** で見つかります。
    * **[Train Model]\(モデルのトレーニング\)** や **[Split Data]\(データの分割\)** など、トレーニングに使用されたモジュールは削除されます。
    * 保存したトレーニング済みのモデルが再びパイプラインに追加されます。
    * **[Web Service Input]\(Web サービスの入力\)** モジュールと **[Web Service Output]\(Web サービスの出力\)** モジュールが追加されます。 これらのモジュールでは、ユーザー データがモデルに入力される位置と、データが返される位置が特定されます。

    > [!Note]
    > **トレーニング パイプライン**は、パイプライン キャンバスの上部にある新しいタブに保存されます。 また、公開されたパイプラインとしてビジュアル インターフェイスでも確認できます。
    >

    これでパイプラインは次のようになっているはずです。  

   ![デプロイ準備が完了したパイプラインの予測される構成を示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

1. **[Run]\(実行\)** を選択し、パート 1 で使用したものと同じコンピューティング先と実験を使用します。

1. **[Score Model]\(モデルのスコア付け\)** モジュールを選択します。

1. [プロパティ] ウィンドウで、 **[出力]**  >  **[Visualize]\(視覚化\)** の順に選択し、引き続きモデルが正しく機能していることを確認します。 元のデータが、予測価格 ("Scored Labels" (スコア付けラベル)) と共に表示されます。

1. **[デプロイ]** を選択します。

### <a name="create-an-inferencing-cluster"></a>推論クラスターを作成する

表示されたダイアログで、自分のワークスペースから既存の Azure Kubernetes Service (AKS) クラスターを選択して自分のモデルをデプロイできます。 既存の AKS クラスターがない場合は、次の手順を使用して作成してください。

1. ダイアログの **[Compute]\(コンピューティング\)** を選択して **[Compute]\(コンピューティング\)** ページに移動します。

1. ナビゲーション リボンで、 **[Inference Clusters]\(推論クラスター\)**  >  **[+ New]\(+ 新規\)** の順に選択します。

    ![推論クラスターの新規作成ウィンドウに移動する方法を示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/new-inference-cluster.png)

1. 推論クラスター ウィンドウで、新しい Kubernetes サービスを構成します。

1. **[Compute name]\(コンピューティング名\)** に「aks-compute」と入力します。
    
1. 近くにある利用可能な**リージョン**を選択します。

1. **作成** を選択します。

    > [!Note]
    > 新しい AKS サービスの作成には約 15 分かかります。 プロビジョニングの状態は、 **[Inference Clusters]\(推論クラスター\)** ページで確認できます。
    >

### <a name="deploy-the-real-time-endpoint"></a>リアルタイム エンドポイントをデプロイする

AKS サービスのプロビジョニングが完了したら、リアルタイム推論パイプラインに戻ってデプロイを完了します。

1. キャンバスの上にある **[Deploy]\(デプロイ\)** を選択します。

1. **[Deploy new real-time endpoint]\(新しいリアルタイム エンドポイントのデプロイ\)** を選択します。 

1. 作成した AKS クラスターを選択します。

1. **[デプロイ]** を選択します。

    ![新しいリアルタイム エンドポイントの設定方法を示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/setup-endpoint.png)

    デプロイが完了するとキャンバスの上に成功通知が表示されます。完了までに数分かかる場合があります。

## <a name="test-the-real-time-endpoint"></a>リアルタイム エンドポイントをテストする

ワークスペースの左側にあるナビゲーション ウィンドウで **[Endpoints]\(エンドポイント\)** ページに移動して、リアルタイム エンドポイントをテストできます。

1. **[Endpoints]\(エンドポイント\)** ページで、デプロイ済みのエンドポイントを選択します。

    ![リアルタイム エンドポイントのタブを示すスクリーンショット (強調表示されているのは、先ほど作成したエンドポイント)](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. **[テスト]** を選択します。

1. テスト データを入力するか、自動入力されたサンプル データを使用し、 **[テスト]** を選択します。

    テスト要求がエンドポイントに送信され、ページに結果が表示されます。 価格値は入力データに対して生成されますが、予測値を生成するためには使用されません。

    ![リアルタイム エンドポイントのテスト方法を示すスクリーンショット (強調表示されているのは価格のスコア付けラベル)](./media/ui-tutorial-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、ビジュアル インターフェイスで機械学習モデルを作成、デプロイ、および使用する際の主な手順を学習しました。 ビジュアル インターフェイスを使用して他の種類の問題を解決する方法の詳細については、他のサンプル パイプラインを参照してください。

> [!div class="nextstepaction"]
> [クレジット リスク分類のサンプル](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
