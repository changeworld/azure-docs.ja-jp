---
title: チュートリアル:ドラッグ アンド ドロップで予測モデルを作成する (パート 1/2)
titleSuffix: Azure Machine Learning
description: デザイナーを使用して、機械学習予測モデルを構築およびデプロイする方法を学習します。 その後、Microsoft Power BI でそれを使用して結果を予測できます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.custom: designer
ms.openlocfilehash: 9390fc53f4d10ad770313f1f1459f02fc7e7a998
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2021
ms.locfileid: "112459444"
---
# <a name="tutorial-power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>チュートリアル:Power BI 統合 - ドラッグ アンド ドロップで予測モデルを作成する (パート 1/2)

このチュートリアルのパート 1 では、Azure Machine Learning デザイナーを使用して予測機械学習モデルをトレーニングおよびデプロイします。 デザイナーは、わずかなコードを記述するだけのドラッグ アンド ドロップ式のユーザー インターフェイスです。 パート 2 では、Microsoft Power BI でそのモデルを使用して結果を予測します。

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * Azure Machine Learning コンピューティング インスタンスを作成する。
> * Azure Machine Learning 推論クラスターを作成する。
> * データセットを作成します。
> * 回帰モデルをトレーニングする。
> * リアルタイム スコアリング エンドポイントにモデルをデプロイする。


Power BI で使用するモデルは、3 とおりの方法で作成してデプロイすることができます。  この記事では、"オプション B: デザイナーを使用してモデルをトレーニングおよびデプロイする" について説明します。  このオプションでは、デザイナー インターフェイスを使用してわずかなコードを記述するだけの作成エクスペリエンスです。  

ただし、代わりに、その他のオプションのいずれかを使用することもできます。

* [オプション A: Jupyter Notebook を使用してモデルをトレーニングおよびデプロイする](tutorial-power-bi-custom-model.md)。 コード優先のこの作成エクスペリエンスでは、Azure Machine Learning スタジオでホストされている Jupyter Notebook を使用します。
* [オプション C: 自動機械学習を使用してモデルをトレーニングおよびデプロイする](tutorial-power-bi-automated-model.md)。 コーディング不要のこの作成エクスペリエンスでは、データ準備とモデルのトレーニングが完全に自動化されます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 まだサブスクリプションをお持ちでない場合は、[無料試用版](https://azure.microsoft.com/free/)を使用できます。 
- Azure Machine Learning ワークスペース。 ワークスペースがまだない場合は、「[Azure Machine Learning ワークスペースを作成して管理する](./how-to-manage-workspace.md#create-a-workspace)」を参照してください。
- 機械学習ワークフローの基礎知識。


## <a name="create-compute-to-train-and-score"></a>トレーニングとスコア付けを行うためのコンピューティングを作成する

このセクションでは、"*コンピューティング インスタンス*" を作成します。 コンピューティング インスタンスは、機械学習モデルのトレーニングに使用します。 また、リアルタイム スコアリングのためにデプロイするモデルをホストする "*推論クラスター*" も作成します。

[Azure Machine Learning スタジオ](https://ml.azure.com)にサインインします。 左側のメニューで、 **[コンピューティング]** 、 **[新規]** の順に選択します。

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="コンピューティング インスタンスの作成方法を示すスクリーンショット。":::

**[Create compute instance]\(コンピューティング インスタンスの作成\)** ページで、VM サイズを選択します。 このチュートリアルでは、**Standard_D11_v2** VM を選択します。 **[次へ]** を選択します。 

**[設定]** ページで、コンピューティング インスタンスの名前を指定します。 **[作成]** を選択します。 

>[!TIP]
> このコンピューティング インスタンスをノートブックの作成と実行に使用することもできます。

これで、コンピューティング インスタンスの **[状態]** が **[作成中]** になります。 マシンのプロビジョニングには約 4 分かかります。 

待機中に、 **[コンピューティング]** ページで、 **[推論クラスター]** タブを選択します。次に、 **[新規]** を選択します。

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="推論クラスターの作成方法を示すスクリーンショット。":::

**[推論クラスターの作成]** ページで、リージョンと VM サイズを選択します。 このチュートリアルでは、**Standard_D11_v2** VM を選択します。 **[次へ]** を選択します。 

**[Configure Settings]\(構成の設定\)** ページで次の作業を行います。

1. 有効なコンピューティング名を入力します。
1. クラスターの目的として **[Dev-test]\(開発テスト\)** を選択します。 このオプションでは、デプロイしたモデルをホストする単一ノードが作成されます。
1. **［作成］** を選択します

これで、推論クラスターの **[状態]** が **[作成中]** になります。 単一ノード クラスターのデプロイには約 4 分かかります。

## <a name="create-a-dataset"></a>データセットを作成する

このチュートリアルでは、[Diabetes データセット](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)を使用します。 このデータセットは [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) から入手できます。

データセットを作成するために、左側のメニューで **[データセット]** を選択します。 次に **[データセットの作成]** を選択します。 次のオプションが表示されます。

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="新しいデータセットを作成する方法を示すスクリーンショット。":::

**[Open Datasets から]** を選択します。 **[Create dataset from Open Datasets]\(Open Datasets からのデータセットの作成\)** ページで次の作業を行います。

1. 検索バーを使用して、*diabetes* を見つけます。
1. **[Sample: Diabetes]** を選択します。
1. **[次へ]** を選択します。
1. データセットに *diabetes* という名前を付けます。
1. **［作成］** を選択します

データを探索するために、データセットを選択し、 **[探索]** を選択します。

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="データセットの探索方法を示すスクリーンショット。":::

このデータには、10 個のベースライン入力変数が含まれています (年齢、性、肥満度指数、平均血圧、血清に関する 6 つの測定値など)。 また、**Y** という名前のターゲット変数も 1 つあります。このターゲット変数は、調査開始から 1 年経過時の糖尿病の進行を表す定量的測定値です。

## <a name="create-a-machine-learning-model-by-using-the-designer"></a>デザイナーを使用して機械学習モデルを作成する

コンピューティングとデータセットを作成したら、デザイナーを使用して機械学習モデルを作成できます。 Azure Machine Learning スタジオで **[デザイナー]** 、 **[新しいパイプライン]** の順に選択します。

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="新しいパイプラインを作成する方法を示すスクリーンショット。":::

空白の "*キャンバス*" と **[設定]** メニューが表示されます。

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="コンピューティング先の選択方法を示すスクリーンショット。":::

**[設定]** メニューで、 **[Select compute target]\(コンピューティング先を選択\)** を選択します。 先ほど作成したコンピューティング インスタンスを選択し、 **[保存]** を選択します。 **[Draft name]\(ドラフトの名前\)** をもっと覚えやすい名前 (*diabetes-model* など) に変更します。 最後に、説明を入力します。

アセットの一覧で **[データセット]** を展開し、**diabetes** データセットを探します。 このコンポーネントをキャンバスにドラッグします。

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="コンポーネントをキャンバスにドラッグする方法を示すスクリーンショット。":::

次に、以下のコンポーネントをキャンバスにドラッグします。

1. **線形回帰** ( **[Machine Learning Algorithms]\(機械学習アルゴリズム\)** にあります)
1. **モデルのトレーニング** ( **[モデルのトレーニング]** にあります)

ご自分のキャンバスで、コンポーネントの上部と下部に円が表示されていることに注目してください。 これらの円はポートです。

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="未接続のコンポーネントのポートを示すスクリーンショット。":::
 
次に、これらのコンポーネントどうしを "*接続*" します。 **diabetes** データセットの下部にあるポートを選択します。 それを、 **[モデルのトレーニング]** コンポーネントの右上にあるポートにドラッグします。 **[線形回帰]** コンポーネントの下部にあるポートを選択します。 それを、 **[モデルのトレーニング]** コンポーネントの左上にあるポートにドラッグします。

予測するラベル (ターゲット) 変数として使用するデータセット列を選択します。 **[モデルのトレーニング]** コンポーネントを選択し、 **[列の編集]** を選択します。 

ダイアログ ボックスで **[Enter column name]\(列名を入力\)**  >  **[Y]** の順に選択します。

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="ラベル列を選択する方法を示すスクリーンショット。":::

**[保存]** を選択します。 機械学習 "*ワークフロー*" は、次のようになっていると思います。

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="接続済みのコンポーネントを示すスクリーンショット。":::

**[Submit]\(送信\)** をクリックします。 **[実験]** の **[新規作成]** を選択します。 実験の名前を指定し、 **[送信]** を選択します。

>[!NOTE]
> 実験の初回実行には約 5 分かかります。 それ以降の実行は、もっと短時間で完了します。待ち時間を短くするために、実行されたコンポーネントはデザイナーによってキャッシュされるためです。

実験が終了すると、次のビューが表示されます。

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="完了した実行を示すスクリーンショット。":::

実験ログを調べるには、 **[モデルのトレーニング]** を選択し、 **[出力とログ]** を選択します。

## <a name="deploy-the-model"></a>モデルをデプロイする

モデルをデプロイするために、キャンバスの上部で **[Create inference pipeline]\(推論パイプラインの作成\)**  >  **[Real-time inference pipeline]\(リアルタイム推論パイプライン\)** の順に選択します。

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="リアルタイム推論パイプラインを選択する場所を示すスクリーンショット。":::
 
モデルのスコア付けに必要なコンポーネントのみとなるようにパイプラインをしあげていきます。 データにスコアを付けるときは、ターゲット変数の値がわかりません。 そのため、データセットから **Y** を削除できます。 

**Y** を削除するには、 **[Select columns in Dataset]\(データセット内の列の選択\)** コンポーネントをキャンバスに追加します。 diabetes データセットが入力となるようにコンポーネントを接続します。 結果が **[Score Model]\(モデルのスコア付け\)** コンポーネントに出力されます。

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="列を削除する方法を示すスクリーンショット。":::

キャンバス上で **[Select Columns in Dataset]\(データセット内の列の選択\)** コンポーネントを選択し、 **[列の編集]** を選択します。 

**[列の選択]** ダイアログ ボックスで、 **[名前別]** を選択します。 次に、入力変数がすべて選択されているが、ターゲットが選択されて "*いない*" ことを確認します。

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="列の設定を削除する方法を示すスクリーンショット。":::

**[保存]** を選択します。 

最後に、 **[Score Model]\(モデルのスコア付け\)** コンポーネントを選択し、 **[Append score columns to output]\(スコア列を出力に追加する\)** チェック ボックスがオフになっていることを確認します。 待ち時間を減らすために、入力なしで予測が返されます。

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="[Score Model]\(モデルのスコア付け\) コンポーネントの設定を示すスクリーンショット。":::

キャンバス上部の **[Submit]\(送信\)** を選択します。

推論パイプラインが正しく実行されたら、ご自分の推論クラスターにモデルをデプロイすることができます。 **[デプロイ]** を選択します。 

**[Set-up real-time endpoint]\(リアルタイム エンドポイントのセットアップ\)** ダイアログ ボックスで、 **[新しいリアルタイム エンドポイントをデプロイする]** を選択します。 エンドポイントに *my-diabetes-model* という名前を付けます。 先ほど作成した推論を選んでから、 **[デプロイ]** を選択します。

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="リアルタイム エンドポイントの設定を示すスクリーンショット。":::
## <a name="next-steps"></a>次のステップ

このチュートリアルでは、デザイナー モデルをトレーニングしてデプロイする方法を見てきました。 次のパートでは、このモデルを Power BI で利用 (スコア付け) する方法について説明します。

> [!div class="nextstepaction"]
> [チュートリアル: Power BI でモデルを利用する](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
