---
title: チュートリアル:ドラッグ アンド ドロップで予測モデルを作成する (パート 1/2)
titleSuffix: Azure Machine Learning
description: Microsoft Power BI で機械学習の予測モデルを使用して結果を予測できるように、デザイナーを使用して予測モデルを作成、デプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f0e1ffe60069a2379f8eddab1aae74db2b4eac50
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370663"
---
# <a name="tutorial--power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>チュートリアル:Power BI 統合 - ドラッグ アンド ドロップで予測モデルを作成する (パート 1/2)

このチュートリアルのパート 1 では、わずかなコードを記述するだけのドラッグ アンド ドロップ式のユーザー インターフェイスである Azure Machine Learning デザイナーを使用して、予測機械学習モデルをトレーニング、デプロイします。 パート 2 では、Microsoft Power BI でそのモデルを使用して結果を予測します。

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * Azure Machine Learning コンピューティング インスタンスを作成する
> * Azure Machine Learning 推論クラスターを作成する
> * データセットを作成する
> * 回帰モデルをトレーニングする
> * リアルタイム スコアリング エンドポイントにモデルをデプロイする


Power BI で使用するモデルは、3 とおりの方法で作成、デプロイすることができます。  この記事では、オプション B (デザイナーを使用してモデルをトレーニング、デプロイする) を取り上げます。  デザイナーを使用してわずかなコードを記述するだけの作成エクスペリエンス (ドラッグ アンド ドロップ ユーザー インターフェイス) を紹介しています。  

その他、次の方法を使用できます。

* [オプション A: Notebooks を使用してモデルをトレーニング、デプロイする](tutorial-power-bi-custom-model.md) - Azure Machine Learning スタジオにホストされた Jupyter Notebook を使用した、コード優先の作成エクスペリエンス。
* [オプション C: 自動 ML を使用してモデルをトレーニング、デプロイする](tutorial-power-bi-automated-model.md) - データ準備とモデルのトレーニングを完全に自動化する、コーディング不要の作成エクスペリエンス。

## <a name="prerequisites"></a>[前提条件]

- Azure サブスクリプション ([無料試用版を利用できます](https://aka.ms/AMLFree))。 
- Azure Machine Learning ワークスペース。 まだワークスペースをお持ちでない場合は、[Azure Machine Learning ワークスペースの作成方法](./how-to-manage-workspace.md#create-a-workspace)に関する手順に従ってください。
- 機械学習ワークフローの基礎知識。


## <a name="create-compute-for-training-and-scoring"></a>トレーニングとスコアリング用のコンピューティングを作成する

このセクションでは、機械学習モデルのトレーニングに使用する "*コンピューティング インスタンス*" を作成します。 また、リアルタイム スコアリングに使用する、デプロイしたモデルをホストするための "*推論クラスター*" を作成します。

[Azure Machine Learning スタジオ](https://ml.azure.com)にログインして、左側のメニューの **[Compute]\(コンピューティング\)** を選択し、続けて **[New]\(新規\)** を選択します。

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="コンピューティング インスタンスの作成方法を示すスクリーンショット":::

**[Create compute instance]\(コンピューティング インスタンスの作成\)** 画面が表示されたら、VM サイズを選択 (このチュートリアルでは `Standard_D11_v2` を選択) し、 **[次へ]** を選択します。 [設定] ページで、コンピューティング インスタンスに有効な名前を入力し、 **[作成]** を選択します。 

>[!TIP]
> このコンピューティング インスタンスをノートブックの作成と実行に使用することもできます。

コンピューティング インスタンスの **[状態]** が **[作成中]** になっていることがわかります。マシンのプロビジョニングには約 4 分かかります。 待っている間、コンピューティング ページ上の **[推論クラスター]** タブを選択し、 **[New]\(新規\)** を選択します。

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="推論クラスターの作成方法を示すスクリーンショット":::

**[推論クラスターの作成]** ページが表示されたらリージョンを選択し、VM サイズを選択 (このチュートリアルでは `Standard_D11_v2` を選択) して、 **[次へ]** を選択します。 **[Configure Settings]\(構成の設定\)** ページで次の作業を行います。

1. 有効なコンピューティング名を入力します
1. クラスターの用途として **[Dev-test]** を選択します (デプロイしたモデルをホストする単一ノードを作成します)
1. **[作成]**

推論クラスターの **[状態]** が **[作成中]** になっていることがわかります。単一ノード クラスターのデプロイには約 4 分かかります。

## <a name="create-a-dataset"></a>データセットを作成する

このチュートリアルでは、[Diabetes (糖尿病) データセット](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)を使用します。これは [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) から入手できます。

データセットを作成するには、左側のメニューから **[データセット]** を選択し、 **[データセットの作成]** を選択します。次のオプションが表示されます。

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="新しいデータセットを作成する方法を示すスクリーンショット":::

**[Open Datasets から]** を選択し、 **[Open Datasets からのデータセットの作成]** 画面で次の作業を行います。

1. 検索バーを使用して *diabetes* を検索します
1. **[Sample: Diabetes]\(サンプル: Diabetes\)** を選択します
1. **[次へ]** を選択します
1. データセットの名前として「*diabetes*」を入力します
1. **[作成]**

[データセット]、 **[Explore]\(参照\)** の順に選択してデータを参照することができます。

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="データセットの参照方法を示すスクリーンショット":::

このデータには、10 個のベースライン入力変数 (年齢、性、肥満度指数、平均血圧、および血清に関する 6 つの測定値) のほか、**Y** という名前のターゲット変数 (調査開始から 1 年経過時の糖尿病の進行を表す定量的測定値) が 1 つ含まれています。

## <a name="create-a-machine-learning-model-using-designer"></a>デザイナーを使用して機械学習モデルを作成する

コンピューティングとデータセットを作成したら、次はデザイナーを使用して機械学習モデルを作成します。 Azure Machine Learning スタジオで **[デザイナー]** を選択し、 **[新しいパイプライン]** を選択します。

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="新しいパイプラインを作成する方法を示すスクリーンショット":::

空白の "*キャンバス*" が表示され、そこに **[設定] メニュー** も表示されます。

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="コンピューティング先の選択方法を示すスクリーンショット":::

**[設定] メニュー** で **コンピューティング先を選択** し、先ほど作成したコンピューティング インスタンスを選択して、 **[保存]** を選択します。 **[ドラフトの名前]** をもっと覚えやすい名前 (*diabetes-model* など) に変更し、説明を入力します。

次に、一覧表示されている資産の **[データセット]** を展開し、**diabetes** データセットを探します。このモジュールをキャンバスにドラッグ アンド ドロップしてください。

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="コンポーネントをドラッグする方法を示すスクリーンショット":::

次に、以下のコンポーネントをキャンバスにドラッグ アンド ドロップします。

1. 線形回帰 ( **[Machine Learning Algorithms]\(機械学習アルゴリズム\)** にあります)
1. モデルのトレーニング ( **[Model Training]\(モデルのトレーニング\)** にあります)

この時点では、キャンバスは次のようになっていると思います (コンポーネントの上下に、ポートと呼ばれる小さな円が表示されていることに注目してください。下図では赤色で強調表示されています)。

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="未接続状態のコンポーネントを示すスクリーンショット":::
 
次に、これらのコンポーネントどうしを "*接続*" する必要があります。 **diabetes** データセットの下部にあるポートを選択し、 **[Train model]\(モデルのトレーニング\)** コンポーネント上部の右側のポートにドラッグします。 **[Linear regression]\(線形回帰\)** コンポーネント下部のポートを選択し、 **[Train model]\(モデルのトレーニング\)** コンポーネント上部の左側のポートにドラッグします。

予測するラベル (ターゲット) 変数として使用する列をデータセットから選択します。 **[Train model]\(モデルのトレーニング\)** コンポーネントを選択し、 **[列の編集]** を選択します。 ダイアログ ボックスから **[列名を入力してください]** を選択し、ドロップダウン リストから **[Y]** を選択します。

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="ラベル列の選択のスクリーンショット":::

**[保存]** を選択します。 機械学習 "*ワークフロー*" は、次のようになっていると思います。

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="接続状態のコンポーネントを示すスクリーンショット":::

**[送信]** を選択し、実験の **[新規作成]** を選択します。 実験の名前を入力し、 **[送信]** を選択します。

>[!NOTE]
> 初回実行時は、実験が完了するまでに約 5 分かかります。 それ以降の実行は、もっと短時間で完了します。待ち時間を短くするために、既に実行されたコンポーネントはデザイナーによってキャッシュされるためです。

実験が完了すると、次の画面が表示されます。

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="完了した実行を示すスクリーンショット":::

**[モデルのトレーニング]** を選択し、 **[出力 + ログ]** を選択すると、実験のログを詳細に調べることができます。

## <a name="deploy-the-model"></a>モデルをデプロイする

モデルをデプロイするには、キャンバス上部にある **[Create Inference Pipeline]\(推論パイプラインの作成\)** を選択し、 **[Real-time inference pipeline]\(リアルタイム推論パイプライン\)** を選択します。

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="リアルタイム推論パイプラインを示すスクリーンショット":::
 
モデルのスコアリングに必要なコンポーネントのみとなるようにパイプラインをしあげていきます。 データをスコアリングするときは、ターゲット変数の値がわからないので、データセットから **Y** を削除することができます。 削除するには、 **[Select columns in Dataset]\(データセット内の列の選択\)** コンポーネントをキャンバスに追加します。 diabetes データセットを入力として **[Score Model]\(モデルのスコア付け\)** コンポーネントに結果が出力されるように、コンポーネントを接続します。

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="列の削除を示すスクリーンショット":::

キャンバス上で **[Select Columns in Dataset]\(データセット内の列の選択\)** コンポーネントを選択し、 **[列の編集]** を選択します。 [列の選択] ダイアログで **[By name]\(名前で選択\)** を選択し、ターゲット変数を **除く** 入力変数がすべて選択されていることを確認します。

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="列の削除設定を示すスクリーンショット":::

**[保存]** を選択します。 最後に、 **[Score Model]\(モデルのスコア付け\)** コンポーネントを選択し、 **[Append score columns to output]\(スコア列を出力に追加する\)** チェック ボックスをオフにします (入力と予測の "*両方*" ではなく、予測だけが返されるので、待ち時間が短くて済みます)。

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="モデルのスコア付けコンポーネントの設定を示すスクリーンショット":::

キャンバス上部の **[Submit]\(送信\)** を選択します。

推論パイプラインが正しく実行されれば、推論クラスターにモデルをデプロイすることができます。 **[デプロイ]** を選択すると、 **[リアルタイム エンドポイントのセットアップ]** ダイアログ ボックスが表示されます。 **[新しいリアルタイム エンドポイントをデプロイする]** を選択して、エンドポイントに「**my-diabetes-model**」という名前を付け、先ほど作成した推論を選択して **[デプロイ]** を選択します。

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="リアルタイム エンドポイントの設定を示すスクリーンショット":::
## <a name="next-steps"></a>次のステップ

このチュートリアルでは、デザイナー モデルをトレーニングしてデプロイする方法を見てきました。 次のパートでは、このモデルを Power BI から利用 (スコアリング) する方法について説明します。

> [!div class="nextstepaction"]
> [チュートリアル: Power BI でモデルを利用する](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
