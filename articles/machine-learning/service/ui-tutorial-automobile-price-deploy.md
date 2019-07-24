---
title: チュートリアル:ビジュアル インターフェイスで機械学習モデルをデプロイする
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service のビジュアル インターフェイスで予測分析ソリューションを構築する方法について説明します。 ドラッグ アンド ドロップ モジュールを使用して、機械学習モデルのトレーニング、スコア付け、およびデプロイを行います。 このチュートリアルは、線形回帰を使用した自動車価格の予測に関する 2 部構成のシリーズのパート 2 です。
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 8512ca2fe01c772d7e4c21a5cb09303b9804899c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389220"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>チュートリアル:ビジュアル インターフェイスで機械学習モデルをデプロイする

このチュートリアルでは、Azure Machine Learning service のビジュアル インターフェイスでの予測分析ソリューションの開発についてさらに見ていきます。 このチュートリアルは、**2 部構成のチュートリアル シリーズのパート 2 です**。 チュートリアルの[パート 1](ui-tutorial-automobile-price-train-score.md) では、自動車価格を予測するモデルのトレーニング、スコア付け、および評価を行いました。 チュートリアルのこのパートでは、次のことを行います。

> [!div class="checklist"]
> * モデルをデプロイできるよう準備する
> * Web サービスのデプロイ
> * Web サービスをテストする
> * Web サービスを管理する
> * Web サービスを使用する

## <a name="prerequisites"></a>前提条件

[このチュートリアルのパート 1](ui-tutorial-automobile-price-train-score.md) を完了しました。

## <a name="prepare-for-deployment"></a>デプロイの準備をする

このチュートリアルで作成した予測モデルを他のユーザーが使用できるように、Azure Web サービスとしてデプロイできます。

これまでは、モデルのトレーニングを実験してきました。 ここでは、ユーザー入力に基づいて新しい予測を生成しましょう。

デプロイの準備は、次の 2 つの手順から成るプロセスです。  

1. 作成した "*トレーニング実験*" を "*予測実験*" に変換する
1. Web サービスとして予測実験をデプロイする

まずは実験キャンバスの下にある **[名前を付けて保存]** を選択して、実験のコピーを作成します。

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>トレーニング実験を予測実験に変換する

このモデルをデプロイできるよう準備するには、このトレーニング実験を予測実験に変換します。 通常、この操作は 3 つの手順から成ります。

1. トレーニングしたモデルを保存し、トレーニング モジュールを置き換える
1. 実験をトリミングしてトレーニングのためのみに必要だったモジュールを削除します。
1. Web サービスが入力データを受け取る場所と出力を生成する場所を定義する

これらの手順は、手動で行うか、または実験キャンバスの下にある **[Set Up Web Service]\(Web サービスの設定\)** を選択して自動的に行うことができます。

![トレーニング実験から予測実験への自動変換を示すアニメーション GIF](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

**[Set Up Web Service]\(Web サービスの設定\)** を選択すると、次の動作が行われます。

* トレーニング済みモデルが、1 つの **Trained Model** (トレーニング済みモデル) モジュールに変換されます。 これは、実験キャンバスの左側にあるモジュール パレットに格納されます。 このモジュールは、 **[Trained Models]\(トレーニング済みモデル\)** の下に表示されます。
* トレーニングに使用したモジュールは削除されます。具体的には次のモジュールが削除されます。
  * モデルのトレーニング
  * データの分割
  * モデルの評価
* 保存したトレーニング済みのモデルが実験に追加されます。
* **Web サービスの入力**と **Web サービスの出力**モジュールが追加されます。 これらのモジュールでは、ユーザーのデータがモデルに入力される位置と、データが返される位置を特定します。

実験は、実験キャンバスの上部にある新しい 2 つのタブに分かれて保存されます。 元のトレーニング実験は **[トレーニング実験]** タブに、新しく作成された予測実験は **[予測実験]** タブに配置されます。 Web サービスとしてデプロイするのは予測実験です。

現在、実験は以下のようになっています。  

![デプロイできるよう準備した後の実験の予測される構成を示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

最後にもう一度実験を実行します ( **[実行]** を選択します)。 ポップアップ ダイアログ ボックスで、実験を実行するコンピューティング ターゲットを選択します。 モデルがまだ機能していることを確認するには、Score Model (モデルのスコア付け) モジュールの出力を選択し、 **[結果の表示]** を選択します。 元のデータが、予測価格 ("Scored Labels" (スコア付けラベル)) と共に表示されます。

## <a name="deploy-the-web-service"></a>Web サービスをデプロイする

実験から派生した新しい Web サービスをデプロイするには:

1. キャンバスの下にある **[Deploy Web Service]\(Web サービスのデプロイ\)** を選択します。
1. Web サービスを実行する **[Compute Target]\(コンピューティング ターゲット\)** を選択します。

    現時点では、ビジュアル インターフェイスでは Azure Kubernetes Service (AKS) のコンピューティング ターゲットへのデプロイのみがサポートされています。 機械学習サービス ワークスペース内の使用可能な AKS コンピューティング ターゲットから選択するか、または表示されるダイアログ ボックスの手順を使用して新しい AKS 環境を構成します。

    ![新しいコンピューティング ターゲットの可能な構成を示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. **[Deploy Web Service]\(Web サービスのデプロイ\)** を選択します。 デプロイが完了したら、次の通知が表示されます。 デプロイには数分かかる場合があります。

    ![デプロイが成功したことを確認するメッセージを示すスクリーンショット。](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>Web サービスをテストする

**Web service input** (Web サービスの入力) モジュール経由で、ユーザー入力データが、デプロイしたモデルに入力されます。 その後、**Score Model** (モデルのスコア付け) モジュールで入力がスコア付けされます。 設定した予測実験の方法では、モデルのデータは、元の自動車価格データセットと同じ形式であることが期待されています。 最後に、**Web service output** (Web サービスの出力) モジュール経由で結果がユーザーに返されます。

Web サービスは、ビジュアル インターフェイスの Web サービス タブでテストできます。

1. Web サービス セクションに移動します。 **Tutorial - Predict Automobile Price[Predictive Exp]** (チュートリアル - 自動車価格を予測する [予測実験]) という名前の、デプロイした Web サービスが表示されます。

     ![最近作成された Web サービスが強調表示された状態の Web サービス タブを示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. 追加情報を表示する Web サービスの名前を選択します。

     ![Web サービス ビューに表示された追加情報を示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/web-service-details.png)

1. **[テスト]** を選択します。

    ![Web サービスのテスト ページを示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)

1. テスト データを入力するか、または自動入力されたサンプル データを使用し、下部にある **[テスト]** を選択します。 テスト要求が Web サービスに送信され、ページに結果が表示されます。 価格値は入力データに対して生成されますが、予測値を生成するためには使用されません。

## <a name="manage-the-web-service"></a>Web サービスを管理する

デプロイした Web サービスは、ビジュアル インターフェイスの **[Web サービス]** タブで管理できます。

Web サービスを削除するには、Web サービスの詳細ページで **[削除]** を選択します。

   ![ウィンドウの下部にある Web サービスの [削除] ボタンの位置を示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/web-service-delete.png)

## <a name="consume-the-web-service"></a>Web サービスを使用する

このチュートリアルの前の手順では、自動車の予測モデルを Azure Web サービスとしてデプロイしました。 ユーザーは、REST API 経由でこれにデータを送信し、結果を受信できるようになりました。

**要求/応答** - ユーザーは、HTTP プロトコルを使用してサービスに 1 つまたは複数の行の自動車データを送信します。 サービスは、1 つまたは複数の結果セットを返します。

REST 呼び出しのサンプルは、Web サービスの詳細ページの **[Consume]\(使用\)** タブに表示されます。

   ![[Consume]\(使用\) タブに表示される REST 呼び出しのサンプルを示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

**[API Doc]\(API ドキュメント\)** タブに移動すると、API の追加情報が表示されます。

  ![[API Doc]\(API ドキュメント\) タブに表示される API の追加情報を示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/web-service-api.png)

## <a name="manage-models-and-deployments-in-azure-machine-learning-service-workspace"></a>Azure Machine Learning service ワークスペースでモデルとデプロイを管理する

ビジュアル インターフェイスで作成したモデルおよび Web サービスのデプロイは、Azure Machine Learning service ワークスペースから管理できます。

1. [Azure Portal](https://portal.azure.com/) でワークスペースを開きます。  

1. ワークスペースで、 **[モデル]** を選択します。 次に、作成した実験を選択します。

    ![Azure portal で実験に移動する方法を示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    このページに、モデルに関する追加情報が表示されます。

    ![Azure portal での実験の統計情報の概要を示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/model-details.png)

1. **[デプロイ]** を選択すると、モデルを使用するすべての Web サービスが一覧表示されます。 Web サービスの名前を選択すると、Web サービスの詳細ページに移動します。 このページでは、Web サービスのより詳細な情報を取得できます。

    ![詳細な実行レポートを示すスクリーンショット](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、ビジュアル インターフェイスで機械学習モデルを作成、デプロイ、および使用する際の主な手順を学習しました。 ビジュアル インターフェイスを使用してその他の種類の問題を解決する方法の詳細については、実験のサンプルをご覧ください。

> [!div class="nextstepaction"]
> [クレジット リスク分類のサンプル](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
