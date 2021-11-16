---
title: 'チュートリアル: デザイナー - コードのないモデルをデプロイする'
titleSuffix: Azure Machine Learning
description: 機械学習モデルをデプロイし、Azure Machine Learning デザイナーを使用して自動車の価格を予測します。
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 10/21/2021
ms.custom: designer, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 3348bddec5700ba2a5ddf1112ad20f9319e04d79
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554889"
---
# <a name="tutorial-designer---deploy-a-machine-learning-model"></a>チュートリアル: デザイナー - 機械学習モデルをデプロイする

デザイナーを使用して、機械学習モデルをデプロイし、自動車の価格を予測します。 このチュートリアルは、2 部構成のシリーズのパート 2 です。


[チュートリアルのパート 1](tutorial-designer-automobile-price-train-score.md) では、自動車価格の線形回帰モデルをトレーニングしました。 パート 2 では、そのモデルをデプロイして、他のユーザーがそれを使用できるようにします。 このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * リアルタイム推論パイプラインを作成する。
> * 推論クラスターを作成する。
> * リアルタイム エンドポイントをデプロイする。
> * リアルタイム エンドポイントをテストする。

## <a name="prerequisites"></a>前提条件

[チュートリアルのパート 1](tutorial-designer-automobile-price-train-score.md) を完了して、デザイナーで機械学習モデルをトレーニングしてスコア付けする方法を学習します。

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-real-time-inference-pipeline"></a>リアルタイム推論パイプラインを作成する

パイプラインをデプロイするためには、まずトレーニング パイプラインをリアルタイム推論パイプラインに変換する必要があります。 このプロセスにより、トレーニング コンポーネントが削除され、要求を処理するための Web サービスの入力と出力が追加されます。

### <a name="create-a-real-time-inference-pipeline"></a>リアルタイム推論パイプラインを作成する

1. パイプライン キャンバス上で **[Create inference pipeline]\(推論パイプラインの作成\)**  >  **[Real-time inference pipeline]\(リアルタイム推論パイプライン\)** の順に選択します。

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png" alt-text="パイプラインの作成ボタンへのアクセス方法を示すスクリーンショット":::

    これでパイプラインは次のようになっているはずです。 

   ![デプロイ準備が完了したパイプラインの予測される構成を示すスクリーンショット](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    **[Create inference pipeline]\(推論パイプラインの作成\)** を選択すると、以下のようないくつかの処理が行われます。
    
    * トレーニング済みのモデルが、コンポーネント パレットに **[Dataset]\(データセット\)** コンポーネントとして格納されます。 これは **[マイ データセット]** で見つかります。
    * **[Train Model]\(モデルのトレーニング\)** や **[Split Data]\(データの分割\)** などのトレーニング コンポーネントが削除されます。
    * 保存したトレーニング済みのモデルが再びパイプラインに追加されます。
    * **[Web Service Input]\(Web サービスの入力\)** および **[Web Service Output]\(Web サービスの出力\)** コンポーネントが追加されます。 ユーザー データがパイプラインに入力される位置と、データが返される位置が、これらのコンポーネントによって示されます。

    > [!NOTE]
    > 既定で、 **[Web Service Input]\(Web サービスの入力\)** では、同じダウンストリーム ポートに接続するコンポーネント出力データと同じデータ スキーマが予期されます。 このサンプルでは、 **[Web Service Input]\(Web サービスの入力\)** と **[Automobile price data (Raw)]\(自動車価格データ (未加工)\)** は同じダウンストリーム コンポーネントに接続します。そのため、 **[Web Service Input]\(Web サービスの入力\)** では、 **[Automobile price data (Raw)]\(自動車価格データ (未加工)\)** と同じデータ スキーマが予期され、ターゲット変数列 `price` がスキーマに含まれます。
    > ただし、通常、データにスコアを付けるときは、ターゲット変数の値はわかりません。 このような場合は、 **[Select Columns in Dataset]\(データセット内の列の選択\)** コンポーネントを使用して、推論パイプライン内のターゲット変数列を削除できます。 ターゲット変数列を削除する **[Select Columns in Dataset]\(データセット内の列の選択\)** の出力が、 **[Web Service Intput]\(Web サービスの入力\)** コンポーネントの出力と同じポートに接続されていることを確認してください。

1. **[送信]** を選択し、パート 1 で使用したものと同じコンピューティング先と実験を使用します。

    これが最初の実行の場合は、パイプラインの実行が完了するまでに最大 20 分かかることがあります。 既定のコンピューティング設定の最小ノード サイズは 0 です。これは、アイドル状態になった後に、デザイナーによってリソースが割り当てられる必要があることを意味します。 コンピューティング リソースが既に割り当てられているため、パイプラインの反復実行にかかる時間は短くなります。 さらにデザイナーでは、各コンポーネント用にキャッシュされた結果を使用して、効率を向上させます。

1. **[デプロイ]** を選択します。

## <a name="create-an-inferencing-cluster"></a>推論クラスターを作成する

表示されたダイアログ ボックスで、既存の Azure Kubernetes Service (AKS) クラスターを選択して自分のモデルをデプロイできます。 既存の AKS クラスターがない場合は、次の手順を使用して作成してください。

1. 表示されたダイアログ ボックスの **[Compute]\(コンピューティング\)** を選択して **[Compute]\(コンピューティング\)** ページに移動します。

1. ナビゲーション リボンで、 **[Inference Clusters]\(推論クラスター\)**  >  **[+ New]\(+ 新規\)** の順に選択します。

    ![推論クラスターの新規作成ペインに移動する方法を示すスクリーンショット](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)
   
1. 推論クラスター ウィンドウで、新しい Kubernetes サービスを構成します。

1. **[Compute name]\(コンピューティング名\)** に「*aks-compute*」と入力します。
    
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

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png" alt-text="新しいリアルタイム エンドポイントの設定方法を示すスクリーンショット":::

    リアルタイム エンドポイントの **[詳細]** 設定を変更することもできます。
    
    |[詳細] 設定|Description|
    |---|---|
    |Application Insights の診断とデータ収集を有効にする| Azure Application Insights を有効にして、デプロイされたエンドポイントからデータを収集するかどうか。 </br> 既定値: false |
    |スコアリング タイムアウト| Web サービスのスコアリング呼び出しに適用するタイムアウト (ミリ秒)。</br>既定での動作は次のとおりです。60000|
    |Auto scale enabled\(自動スケーリングの有効化\)|   Web サービスの自動スケールを有効にするかどうか。</br>既定値は: true|
    |最小レプリカ数| この Web サービスを自動スケールするときに使用するコンテナーの最小数。</br>既定での動作は次のとおりです。1|
    |最大レプリカ数| この Web サービスを自動スケールするときに使用するコンテナーの最大数。</br> 既定での動作は次のとおりです。10|
    |ターゲット使用率|オートスケーラーがこの web サービスに対してメンテナンスを試行する目標使用率 (最大 100%)。</br> 既定での動作は次のとおりです。70|
    |更新間隔|自動スケーラーがこの Web サービスのスケーリングを試みる頻度 (秒)。</br> 既定での動作は次のとおりです。1|
    |CPU 予約容量|この Web サービスに割り当てる CPU コアの数。</br> 既定での動作は次のとおりです。0.1|
    |メモリ予約容量|この Web サービスに割り当てるメモリの量 (GB 単位)。</br> 既定での動作は次のとおりです。0.5|
        

1. **[デプロイ]** を選択します。 

    デプロイが完了すると、キャンバスの上に成功通知が表示されます。 これには数分かかる可能性があります。

> [!TIP]
> リアルタイム エンドポイント設定ボックスで、 **[コンピューティングの種類]** に **[Azure コンテナー インスタンス]** を選択すると、**Azure コンテナー インスタンス** (ACI) にデプロイすることもできます。
> Azure コンテナー インスタンスは、テストまたは開発に使用されます。 必要な RAM が 48 GB 未満である CPU ベースの小規模なワークロードには、ACI を使用します。

## <a name="test-the-real-time-endpoint"></a>リアルタイム エンドポイントをテストする

デプロイが完了したら、 **[エンドポイント]** ページに移動して、リアルタイム エンドポイントを表示できます。

1. **[Endpoints]\(エンドポイント\)** ページで、デプロイ済みのエンドポイントを選択します。

    **[詳細]** タブでは、REST URI、Swagger 定義、状態、タグなどの詳細情報を確認できます。

    **[Consume]\(使用\)** タブでは、サンプル使用コードやセキュリティ キーを検索し、認証方法を設定できます。

    **[デプロイ ログ]** タブで、リアルタイム エンドポイントの詳細なデプロイ ログを確認できます。

1. エンドポイントをテストするには、 **[テスト]** タブに移動します。ここでは、テスト データを入力し、 **[テスト]** を選択して、エンドポイントの出力を確認します。

Web サービスの使用方法の詳細については、[Web サービスとしてデプロイされたモデルの使用](how-to-consume-web-service.md)に関するページを参照してください

## <a name="limitations"></a>制限事項

### <a name="update-inference-pipeline"></a>推論パイプラインを更新する

トレーニング パイプラインに何らかの変更を加える場合は、トレーニング パイプラインを再送信し、推論パイプラインを **更新** して、推論パイプラインを再度実行する必要があります。

推論パイプラインではトレーニング済みのモデルのみが更新され、データ変換は更新されないことに注意してください。

更新された変換を推論パイプラインで使用するには、変換コンポーネントの変換出力をデータセットとして登録する必要があります。

![変換データセットの登録方法を示すスクリーンショット](./media/tutorial-designer-automobile-price-deploy/register-transformation-dataset.png)

次に、推論パイプラインの **TD-** コンポーネントを、登録したデータセットに手動で置き換えます。

![変換コンポーネントの置換方法を示すスクリーンショット](./media/tutorial-designer-automobile-price-deploy/replace-td-module.png)

その後、更新されたモデルおよび変換と共に推論パイプラインを送信し、デプロイできます。

### <a name="deploy-real-time-endpoint"></a>リアルタイム エンドポイントをデプロイする

データストアのアクセス制限により、推論パイプラインに **データのインポート** または **データのエクスポート** モジュールが含まれている場合、リアルタイム エンドポイントへのデプロイ時にこれらは自動的に削除されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、デザイナーで機械学習モデルを作成、デプロイ、および使用する際の主な手順を学習しました。 デザイナーの使用方法の詳細については、次のリンクを参照してください。

+ [デザイナーのサンプル](samples-designer.md): デザイナーを使用して他の種類の問題を解決する方法を確認します。
+ [Azure 仮想ネットワークで Azure Machine Learning スタジオを使用する](how-to-enable-studio-virtual-network.md)
