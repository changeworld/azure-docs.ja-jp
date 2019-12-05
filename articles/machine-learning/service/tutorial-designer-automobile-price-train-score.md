---
title: チュートリアル:デザイナーを使用して自動車の価格を予測する
titleSuffix: Azure Machine Learning
description: ドラッグ アンド ドロップ インターフェイスを使用して、機械学習モデルのトレーニング、スコア付け、およびデプロイを行う方法を学習します。 このチュートリアルは、線形回帰を使用した自動車価格の予測に関する 2 部構成のシリーズのパート 1 です。
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 3d0efbaec6905d0d24d9dc1c310d472958011960
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74702061"
---
# <a name="tutorial-predict-automobile-price-with-the-designer-preview"></a>チュートリアル:デザイナーを使用して自動車の価格を予測する (プレビュー)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

この 2 部構成のチュートリアルでは、Azure Machine Learning のデザイナーを使用して、自動車の価格を予測する予測分析ソリューションを開発およびデプロイする方法について説明します。 

パート 1 では、環境を設定してから、対話型のキャンバスにモジュールをドラッグし、それらを相互に接続して Azure Machine Learning パイプラインを作成します。

チュートリアルのパート 1 で学習する内容は次のとおりです。

> [!div class="checklist"]
> * 新しいパイプラインを作成する。
> * データをインポートする。
> * データを準備する。
> * 機械学習モデルをトレーニングする。
> * 機械学習モデルを評価する。

チュートリアルの[パート 2](tutorial-designer-automobile-price-deploy.md) では、予測モデルをリアルタイム推論エンドポイントとしてデプロイし、任意の自動車の価格を送信した技術仕様に基づいて予測する方法を学習します。 

> [!NOTE]
>このチュートリアルの完成版をサンプル パイプラインとして入手できます。
>
>これを見つけるには、ワークスペースのデザイナーに移動します。 **[新しいパイプライン]** セクションで **[Sample 1 - Regression:Automobile Price Prediction(Basic)]\(サンプル 1 - 回帰: 自動車価格の予測 (Basic)\)** を選択してください。

## <a name="create-a-new-pipeline"></a>新しいパイプラインを作成する

Azure Machine Learning パイプラインによって、従属する複数の機械学習ステップとデータ処理ステップが 1 つのリソースに整理されます。 パイプラインは、複数のプロジェクトや複数のユーザーにまたがる複雑な機械学習ワークフローを効率的に整理、管理、再利用するのに役立ちます。 Azure Machine Learning パイプラインを作成するには、Azure Machine Learning ワークスペースが必要です。 このセクションでは、その両方のリソースを作成する方法について説明します。

### <a name="create-a-new-workspace"></a>新しいワークスペースを作成する

Azure Machine Learning ワークスペース (Enterprise Edition) がある場合は、[次のセクションに進みます](#create-the-pipeline)。

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal-enterprise.md)]

### <a name="create-the-pipeline"></a>パイプラインを作成する

1. [ml.azure.com](https://ml.azure.com) にサインインし、使用するワークスペースを選択します。

1. **[デザイナー]** を選択します。

    ![デザイナーへのアクセス方法を示すビジュアル ワークスペースのスクリーンショット](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. **[Easy-to-use prebuilt modules]\(あらかじめ構築された使いやすいモジュール\)** を選択します。

1. キャンバスの上部にある既定のパイプライン名 **Pipeline-Created-on** を選択します。 わかりやすい名前に変更します。 たとえば、*Automobile price prediction* (自動車価格の予測) などです。 名前は一意でなくてもかまいません。

## <a name="import-data"></a>データのインポート

デザイナーには、実験に利用できるいくつかのサンプル データセットが含まれています。 このチュートリアルでは、**Automobile price data (Raw)** を使用します。 

1. パイプライン キャンバスの左側には、データセットとモジュールのパレットがあります。 **[データセット]** を選択し、 **[サンプル]** セクションを見て、利用可能なサンプル データセットを確認します。

1. **Automobile price data (Raw)** データセットを選択し、キャンバスにドラッグします。

   ![データをキャンバスにドラッグする](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>データの視覚化

使用するデータセットを把握するために、データを視覚化することができます。

1. **Automobile price data (Raw)** モジュールを選択します。

1. キャンバスの右側にある [プロパティ] ウィンドウで **[出力]** を選択します。

1. グラフ アイコンを選択してデータを視覚化します。

    ![データの視覚化](./media/tutorial-designer-automobile-price-train-score/visualize-data.png)

1. データ ウィンドウで別の列を選択して、それぞれの情報を表示します。

    各行は自動車を表していて、各自動車に関連付けられている変数が列として表示されます。 このデータセット内には、205 の行と 26 の列があります。

## <a name="prepare-data"></a>データを準備する

通常、データセットには、分析前にある程度の前処理が必要です。 データセットを検査したときに、いくつか不足値があることに気付いたかもしれません。 モデルがデータを正しく分析するには、これらの不足値を整理する必要があります。

### <a name="remove-a-column"></a>列の削除

モデルをトレーニングする際は、欠損データに対処する必要があります。 このデータセットでは、**normalized-losses** 列に多数の欠損値が存在するので、その列全体をモデルから除外します。

1. パレットの上部にある検索ボックスに「**Select**」と入力し、 **[Select Columns in Dataset]\(データセットの列を選択する\)** モジュールを見つけます。

1. **[Select Columns in Dataset]\(データセットの列を選択する\)** モジュールをキャンバスにドラッグします。 データセット モジュールの下のモジュールを削除します。

1. **Automobile price data (Raw)** データセットを、 **[Select Columns in Dataset]\(データセットの列を選択する\)** モジュールに接続します。 データセットの出力ポート (キャンバス上のデータセットの下部にある小さい円) から **[Select Columns in Dataset]\(データセットの列を選択する\)** の入力ポート (モジュールの上部にある小さい円) までドラッグします。

    > [!TIP]
    > 1 つのモジュールの出力ポートを別のモジュールの入力ポートに接続するときに、パイプラインを通じてデータのフローを作成することになります。
    >

    ![モジュールの接続](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. **Select Columns in Dataset (データセットの列を選択する)** モジュールを選択します。

1. キャンバスの右側にある [プロパティ] ウィンドウで、 **[パラメーター]**  >  **[列の編集]** を選択します。

1. **[+]** を選択し、新しいルールを追加します。

1. ドロップダウン メニューから **[Exclude]\(除外\)** と **[列名]** を選択します。
    
1. テキスト ボックスに「*normalized-losses*」と入力します。

1. 右下の **[保存]** を選択して列セレクターを閉じます。

    ![列を除外する](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)
        
    "**normalized-losses**" 列が除外されたことを示すプロパティ ウィンドウ。

1. **Select Columns in Dataset (データセットの列を選択する)** モジュールを選択します。 

1. [プロパティ] ペインで **[パラメーター]**  >  **[コメント]** の順に選択し、「*Exclude normalized losses.* 」と入力します。

### <a name="clean-missing-data"></a>見つからないデータのクリーンアップ

**normalized-losses** 列を削除した後も、データセットにはまだ欠損値が存在します。 残りの欠損データは **[Clean Missing Data]\(見つからないデータのクリーンアップ\)** モジュールを使用して削除できます。

> [!TIP]
> デザイナーでは、欠損値を入力データから取り除くことが、ほとんどのモジュールを使用するための前提条件となっています。

1. 検索ボックスに「**Clean**」と入力して、 **[Clean Missing Data]\(見つからないデータのクリーンアップ\)** モジュールを見つけます。

1. **[Clean Missing Data]\(見つからないデータのクリーンアップ\)** モジュールをパイプライン キャンバスにドラッグします。 それを **[Select Columns in Dataset]\(データセットの列を選択する\)** モジュールに接続します。 

1. [プロパティ] ウィンドウで、 **[Cleaning mode]\(整理モード\)** の **[Remove entire row]\(行全体を削除\)** を選択します。

1. [プロパティ] ウィンドウの **[コメント]** ボックスに「*Remove missing value rows.* 」と入力します。 

    これでパイプラインは次のようになっているはずです。
    
    ![Select-column](./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>機械学習モデルのトレーニング

これでデータの処理が済んだので、予測モデルをトレーニングできます。

### <a name="select-an-algorithm"></a>アルゴリズムを選択する

"*分類*" と "*回帰*" は、2 種類の教師あり機械学習アルゴリズムです。 分類は、色 (赤、青、または緑) のような定義された一連のカテゴリから答えを予測するものです。 回帰は、数値を予測する目的で使用されます。

予測したい価格は数値であるため、回帰アルゴリズムを使用できます。 この例では、線形回帰モデルを使用します。

### <a name="split-the-data"></a>データを分割する

モデルのトレーニング用とテスト用に、データを異なる 2 つのデータセットに分割します。

1. 検索ボックスに「**split data**」と入力して、 **[Split Data]\(データの分割\)** モジュールを見つけます。 それを **[Clean Missing Data]\(見つからないデータのクリーンアップ\)** モジュールの左側のポートに接続します。

1. **[Split Data]\(データの分割\)** モジュールを選択します。

1. [プロパティ] ウィンドウで、 **[Fraction of rows in the first output dataset]\(最初の出力データセット内の行の割合\)** を 0.7 に設定します。

    このオプションによって、データの 70% をモデルのトレーニング、30% をテストに分割します。

1. [プロパティ] ウィンドウの **[コメント]** ボックスに「*Split the dataset into training set (0.7) and test set (0.3)* 」と入力します。

### <a name="train-the-model"></a>モデルをトレーニングする

価格が含まれた一連のデータを指定して、モデルをトレーニングします。 モデルによってデータがスキャンされ、モデルを構築するために自動車のフィーチャーと価格の相関関係が検出されます。

1. 学習アルゴリズムを選択するには、モジュール パレットの検索ボックスをオフにします。

1. **[Machine Learning Algorithms]\(機械学習アルゴリズム\)** を展開します。
    
    このオプションによって、学習アルゴリズムの初期化に使用できるモジュールのカテゴリが複数表示されます。

1. **[Regression]\(回帰\)**  >  **[Linear Regression]\(線形回帰\)** の順に選択し、パイプライン キャンバスにドラッグします。

1. **[Train Model]\(モデルのトレーニング\)** モジュールを見つけて、パイプライン キャンバスにドラッグします。 

1. **[Linear Regression]\(線形回帰\)** モジュールの出力を **[Train Model]\(モデルのトレーニング\)** モジュールの左側の入力に接続します。

1. **[Split Data]\(データの分割\)** モジュールのトレーニング データ出力 (左側のポート) を **[Train Model]\(モデルのトレーニング\)** モジュールの右側の入力に接続します。

    ![Train Model (モデルのトレーニング) モジュールの正しい構成を示すスクリーンショット。 Linear Regression (線形回帰) モジュールが、Train Model (モデルのトレーニング) モジュールの左側のポートに接続され、Split Data (データの分割) モジュールが、Train Model (モデルのトレーニング) モジュールの右側のポートに接続されています。](./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png)

1. **Train Model** (モデルのトレーニング) モジュールを選択します。

1. [プロパティ] ウィンドウで **[列の編集]** セレクターを選択します。

1. **[Label column]\(ラベル列\)** ダイアログ ボックスのドロップダウン メニューを展開して **[列名]** を選択します。 

1. テキスト ボックスに「*price*」(価格) と入力します。 価格は、自分のモデルで予測する値です。

    パイプラインは次のようになっているはずです。

    ![[Train Model]\(モデルのトレーニング\) モジュールを追加した後のパイプラインの正しい構成を示すスクリーンショット。](./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>機械学習モデルを評価する

データの 70% を使用してモデルをトレーニングした後で、残りの 30% にスコアを付け、モデルの精度を確認します。

1. 検索ボックスに「*score model*」と入力して、 **[Score Model]\(モデルのスコア付け\)** モジュールを見つけます。 モジュールをパイプライン キャンバスにドラッグします。 

1. **Train Model** (モデルのトレーニング) モジュールの出力を、**Score Model** (モデルのスコア付け) の左側の入力ポートに接続します。 **Split Data** (データの分割) モジュールのテスト データの出力 (右側のポート) を、**Score Model** (モデルのスコア付け) の右側の入力ポートに接続します。

1. 検索ボックスに「*evaluate*」と入力して、 **[Evaluate Model]\(モデルの評価\)** モジュールを見つけます。 モジュールをパイプライン キャンバスにドラッグします。 

1. **Score Model** (モデルのスコア付け) モジュールの出力を、**Evaluate Model** (モデルの評価) の左側の入力に接続します。 

    最終的なパイプラインは次のようになっているはずです。

    ![パイプラインの正しい構成を示すスクリーンショット。](./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png)

### <a name="run-the-pipeline"></a>パイプラインを実行する

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

### <a name="view-results"></a>結果の表示

実行が完了したら、パイプラインの実行結果を確認できます。 

1. **[Score Model]\(モデルのスコア付け\)** モジュールを選択して、その出力を表示します。

1. [プロパティ] ウィンドウで、 **[出力]**  >  **[Visualize]\(視覚化\)** の順に選択します。

    ここでは、予測された価格と、データのテストによる実際の価格を確認できます。

    ![Scored Label (スコア付けラベル) 列が強調表示された状態の出力の視覚化のスクリーンショット](./media/tutorial-designer-automobile-price-train-score/score-result.png)

1. **[Evaluate Model]\(モデルの評価\)** モジュールを選択して、その出力を表示します。

1. [プロパティ] ウィンドウで、 **[出力]**  >  **[Visualize]\(視覚化\)** の順に選択します。

作成したモデルに対して、以下の統計値が表示されます。

* **Mean Absolute Error** (MAE、平均絶対誤差):絶対誤差の平均。 誤差とは、予測された値と実際の値との差です。
* **Root Mean Squared Error** (RMSE、二乗平均平方根誤差):テスト データセットに対して実行した予測の二乗誤差平均の平方根です。
* **Relative Absolute Error** (相対絶対誤差):実際の値とすべての実際の値の平均との絶対差を基準にした絶対誤差の平均です。
* **Relative Squared Error** (相対二乗誤差):実際の値とすべての実際の値の平均との二乗差を基準にした二乗誤差の平均です。
* **Coefficient of Determination** (決定係数):R-2 乗値ともいいます。どの程度モデルが高い精度でデータと適合するかを示す統計指標です。

この誤差の統計情報は、それぞれ小さいほど良いとされます。 値が小さいほど、予測が実際の値により近いことを示します。 決定係数では、値が 1 (1.0) に近づくほど、予測の精度が高くなります。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルのパート 1 では、以下のタスクを完了しました。

* パイプラインを作成する。
* データを準備する
* モデルをトレーニングする
* モデルにスコアを付け、評価する

パート 2 では、モデルをリアルタイム エンドポイントとしてデプロイする方法を学習します。

> [!div class="nextstepaction"]
> [モデルのデプロイに進む](tutorial-designer-automobile-price-deploy.md)
