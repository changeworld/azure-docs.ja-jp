---
title: チュートリアル:デザイナーを使用して ML モデルをデプロイする
titleSuffix: Azure Machine Learning
description: このチュートリアルでは、Azure Machine Learning デザイナーで予測分析ソリューションを構築する方法について説明します。 ドラッグ アンド ドロップ モジュールを使用して、機械学習モデルのトレーニング、スコア付け、およびデプロイを行います。
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 06/28/2020
ms.custom: designer
ms.openlocfilehash: 95e5b3ac568cfa370fd1e49fad990b681aef46d9
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92311515"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer"></a>チュートリアル:デザイナーで機械学習モデルをデプロイする


[チュートリアルのパート 1](tutorial-designer-automobile-price-train-score.md) で作成した予測モデルを、他のユーザーが使用できるようデプロイしてみましょう。 パート 1 では、モデルをトレーニングしました。 ここでは、ユーザー入力に基づいて新しい予測を生成しましょう。 チュートリアルのこのパートでは、次のことを行います。

> [!div class="checklist"]
> * リアルタイム推論パイプラインを作成する。
> * 推論クラスターを作成する。
> * リアルタイム エンドポイントをデプロイする。
> * リアルタイム エンドポイントをテストする。

## <a name="prerequisites"></a>前提条件

[チュートリアルのパート 1](tutorial-designer-automobile-price-train-score.md) を完了して、デザイナーで機械学習モデルをトレーニングしてスコア付けする方法を学習します。

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-real-time-inference-pipeline"></a>リアルタイム推論パイプラインを作成する

パイプラインをデプロイするためには、まずトレーニング パイプラインをリアルタイム推論パイプラインに変換する必要があります。 このプロセスにより、トレーニング モジュールが削除され、要求を処理するための Web サービスの入力と出力が追加されます。

### <a name="create-a-real-time-inference-pipeline"></a>リアルタイム推論パイプラインを作成する

1. パイプライン キャンバス上で **[Create inference pipeline]\(推論パイプラインの作成\)**  >  **[Real-time inference pipeline]\(リアルタイム推論パイプライン\)** の順に選択します。

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png"alt-text="パイプラインの作成ボタンへのアクセス方法を示すスクリーンショット":::

    これでパイプラインは次のようになっているはずです。 

   ![デプロイ準備が完了したパイプラインの予測される構成を示すスクリーンショット](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    **[Create inference pipeline]\(推論パイプラインの作成\)** を選択すると、以下のようないくつかの処理が行われます。
    
    * トレーニング済みのモデルは、モジュール パレットに **[Dataset]\(データセット\)** モジュールとして格納されます。 これは **[マイ データセット]** で見つかります。
    * **[Train Model]\(モデルのトレーニング\)** や **[Split Data]\(データの分割\)** などのトレーニング モジュールは削除されます。
    * 保存したトレーニング済みのモデルが再びパイプラインに追加されます。
    * **[Web Service Input]\(Web サービスの入力\)** モジュールと **[Web Service Output]\(Web サービスの出力\)** モジュールが追加されます。 ユーザー データがパイプラインに入力される位置と、データが返される位置が、これらのモジュールによって示されます。

    > [!NOTE]
    > 既定では、 **Web サービスの入力** としては、予測パイプラインの作成に使用されたトレーニング データと同じデータ スキーマが期待されます。 このシナリオでは、価格がスキーマに含まれます。 ただし、価格は予測の間に要因としては使用されません。
    >

1. **[送信]** を選択し、パート 1 で使用したものと同じコンピューティング先と実験を使用します。

    これが最初の実行の場合は、パイプラインの実行が完了するまでに最大 20 分かかることがあります。 既定のコンピューティング設定の最小ノード サイズは 0 です。これは、アイドル状態になった後に、デザイナーによってリソースが割り当てられる必要があることを意味します。 コンピューティング リソースが既に割り当てられているため、パイプラインの反復実行にかかる時間は短くなります。 さらにデザイナーでは、各モジュール用にキャッシュされた結果を使用して、効率を向上させます。

1. **[デプロイ]** を選択します。

## <a name="create-an-inferencing-cluster"></a>推論クラスターを作成する

表示されたダイアログ ボックスで、既存の Azure Kubernetes Service (AKS) クラスターを選択して自分のモデルをデプロイできます。 既存の AKS クラスターがない場合は、次の手順を使用して作成してください。

1. 表示されたダイアログ ボックスの **[Compute]\(コンピューティング\)** を選択して **[Compute]\(コンピューティング\)** ページに移動します。

1. ナビゲーション リボンで、 **[Inference Clusters]\(推論クラスター\)**  >  **[+ New]\(+ 新規\)** の順に選択します。

    ![推論クラスターの新規作成ペインに移動する方法を示すスクリーンショット](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)
   
1. 推論クラスター ウィンドウで、新しい Kubernetes サービスを構成します。

1. **[Compute name]\(コンピューティング名\)** に「 *aks-compute* 」と入力します。
    
1. **[Region]\(リージョン\)** には、使用できる近くのリージョンを選択します。

1. **［作成］** を選択します

    > [!NOTE]
    > 新しい AKS サービスの作成には約 15 分かかります。 プロビジョニングの状態は、 **[Inference Clusters]\(推論クラスター\)** ページで確認できます。
    >

## <a name="deploy-the-real-time-endpoint"></a>リアルタイム エンドポイントをデプロイする

AKS サービスのプロビジョニングが完了したら、リアルタイム推論パイプラインに戻ってデプロイを完了します。

1. キャンバスの上にある **[Deploy]\(デプロイ\)** を選択します。

1. **[Deploy new real-time endpoint]\(新しいリアルタイム エンドポイントのデプロイ\)** を選択します。 

1. 作成した AKS クラスターを選択します。

1. **[デプロイ]** を選択します。
    
    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png"alt-text="パイプラインの作成ボタンへのアクセス方法を示すスクリーンショット":::

    デプロイが完了すると、キャンバスの上に成功通知が表示されます。 これには数分かかる可能性があります。

## <a name="view-the-real-time-endpoint"></a>リアルタイム エンドポイントを表示する

デプロイが完了したら、 **[エンドポイント]** ページに移動して、リアルタイム エンドポイントを表示できます。

1. **[Endpoints]\(エンドポイント\)** ページで、デプロイ済みのエンドポイントを選択します。

1. **[詳細]** タブでは、REST URI、状態、タグなどの詳細情報を確認できます。

1. **[Consume]\(使用\)** タブでは、セキュリティ キーを検索し、認証方法を設定できます。

Web サービスの使用方法の詳細については、[Web サービスとしてデプロイされたモデルの使用](how-to-consume-web-service.md)に関するページを参照してください

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、デザイナーで機械学習モデルを作成、デプロイ、および使用する際の主な手順を学習しました。 デザイナーを使用して他の種類の問題を解決する方法の詳細については、他のサンプル パイプラインを参照してください。

> [!div class="nextstepaction"]
> [デザイナーのサンプル](samples-designer.md)
