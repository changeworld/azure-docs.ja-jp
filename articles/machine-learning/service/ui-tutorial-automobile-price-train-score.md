---
title: チュートリアル:ビジュアル インターフェイスで自動車価格を予測する
titleSuffix: Azure Machine Learning service
description: ドラッグ アンド ドロップ ビジュアル インターフェイスを使用して、機械学習モデルのトレーニング、スコア付け、およびデプロイを行う方法を学習します。 このチュートリアルは、線形回帰を使用した自動車価格の予測に関する 2 部構成のシリーズのパート 1 です。
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: e37e99323c92adad0b9e897af8c276a8ac153371
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515631"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>チュートリアル:ビジュアル インターフェイスで自動車価格を予測する

このチュートリアルでは、Azure Machine Learning service のビジュアル インターフェイスでの予測分析ソリューションの開発についてさらに見ていきます。 このチュートリアルの目的は、送信の技術仕様に基づいてすべての自動車価格を予測できるソリューションを用意することです。

このチュートリアルは[クイック スタート](ui-quickstart-run-experiment.md)の続きで、**2 部構成のチュートリアル シリーズのパート 1** です。 ただし、このチュートリアルを開始する前に、クイック スタートを完了する必要はありません。

チュートリアル シリーズのパート 1 で学習する内容は次のとおりです。

> [!div class="checklist"]
> * データのインポートとクリーンアップ (クイック スタートと同じ手順)
> * 機械学習モデルのトレーニング
> * モデルのスコア付けと評価

チュートリアル シリーズの[パート 2](ui-tutorial-automobile-price-deploy.md) では、予測モデルを Azure Web サービスとしてデプロイする方法を学習します。

> [!NOTE]
> このチュートリアルの完成版は、実験のサンプルとして使用できます。
> [実験] ページで、 **[新規追加]**  > **サンプル 1 - 回帰: Automobile Price Prediction(Basic)** (自動車価格の予測 (Basic)) に移動します。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

## <a name="create-a-workspace"></a>ワークスペースの作成

Azure Machine Learning service ワークスペースがある場合は、[次のセクション](#open-the-visual-interface-webpage)に進みます。 ワークスペースがない場合は、ここで作成します。

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>ビジュアル インターフェイスの Web ページを開く

1. [Azure Portal](https://portal.azure.com/) でワークスペースを開きます。  

1. ワークスペースで、 **[ビジュアル インターフェイス]** を選択します。  次に、 **[ビジュアル インターフェイスを起動する]** を選択します。  

    ![Machine Learning service ワークスペースからビジュアル インターフェイスにアクセスする方法を示す Azure portal のスクリーンショット](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

    新しいブラウザー ページに、インターフェイスの Web ページが開きます。  

## <a name="import-and-clean-your-data"></a>データをインポートおよびクリーンアップする

まずはクリーンなデータが必要です。 クイック スタートを完了している場合は、ここでデータ準備実験を再利用できます。 クイック スタートを完了していない場合は、次のセクションをスキップし、[新しい実験から開始](#start-from-a-new-experiment)します。

### <a name="reuse-the-quickstart-experiment"></a>クイック スタートの実験を再利用する

1. クイック スタートの実験を開きます。

1. ウィンドウの下部にある **[名前を付けて保存]** を選択します。

1. 表示されるポップアップ ダイアログで、新しい名前を指定します。

    ![実験の名前を "Tutorial - Predict Automobile Price" (チュートリアル - 自動車価格を予測する) に変更する方法を示すスクリーンショット](./media/ui-tutorial-automobile-price-train-score/save-a-copy.png)

1. 実験は以下のようになっているはずです。

    ![実験の予期される状態を示すスクリーンショット。 自動車のデータ セットが Select Columns (列の選択) モジュールに接続され、それが Clean Missing Data (見つからないデータのクリーンアップ) モジュールに接続されています。](./media/ui-tutorial-automobile-price-train-score/save-copy-result.png)

クイック スタートの実験を正常に再利用できた場合は、次のセクションをスキップして、[モデルのトレーニング](#train-the-model)を開始します。

### <a name="start-from-a-new-experiment"></a>新しい実験から開始する

クイック スタートを完了していない場合は、次の手順に従って、自動車のデータ セットをインポートおよびクリーンアップする新しい実験をすばやく作成します。

1. ビジュアル インターフェイス ウィンドウの下部にある **[+ 新規]** を選択して、新しい実験を作成します。

1. **[Experiments]\(実験\)**  >   **[Blank Experiment]\(空の実験\)** の順に選択します。

1. キャンバスの上部にある既定の実験名 "**Experimented Created on ...** " を選択し、わかりやすい名前に変更します。 たとえば、**Automobile price prediction** (自動車価格の予測) です。 名前は一意でなくてもかまいません。

1. 実験キャンバスの左側には、データセットとモジュールのパレットがあります。 モジュールを検索するには、モジュール パレットの上部にある検索ボックスを使用します。 検索ボックスに「**automobile**」と入力し、**Automobile price data (Raw)** (自動車価格データ (Raw)) というラベルが付いたデータセットを検索します。 このデータセットを実験キャンバスにドラッグします。

    ![自動車価格のデータ セットを検索する方法を示すスクリーンショット](./media/ui-tutorial-automobile-price-train-score/automobile-dataset.png)

    これでデータを用意できたので、**normalized-losses** 列を完全に削除するモジュールを追加できます。 次に、データが不足している行を削除する別のモジュールを追加します。

1. 検索ボックスに「**select columns**」と入力して、**Select Columns in Dataset** (データセット内の列の選択) モジュールを見つけます。 その後、それを実験キャンバスにドラッグします。 このモジュールを使用すると、モデルに含める、またはモデルから除外するデータの列を選択できます。

1. **Automobile price data (Raw)** データセットの出力ポートを、Select Columns in Dataset の入力ポートに接続します。

    ![Automobile Price Data (自動車価格データ) モジュールを Select Columns (列の選択) モジュールに接続する方法を示すアニメーション GIF](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. Select Columns in Dataset (データセット内の列の選択) モジュールを選択し、 **[Properties]\(プロパティ\)** ウィンドウの **[Launch column selector]\(列セレクターの起動\)** を選択します。

   1. 左側の **[With rules]\(規則を使用\)** を選択します。

   1. **[Begin With]\(次の値で始まる\)** の横にある **[すべての列]** を選択します。 これらの規則により、**Select Columns in Dataset** (データセット内の列の選択) では、すべての列がフィルターを通過します (これから除外する列を除く)。

   1. ドロップダウンから **[Exclude]\(除外\)** と **[column names]\(列名\)** を選択し、テキスト ボックス内に「**normalized-losses**」と入力します。

   1. (右下の) [OK] ボタンを選択して列セレクターを閉じます。

     これで、**Select Columns in Dataset**のプロパティ ウィンドウに、**normalized-losses** 以外のデータセットのすべての列がフィルターを通過することが示されます。

1. **Select Columns in Dataset** (データセット内の列の選択) モジュールをダブルクリックし、「Exclude normalized losses」(正規化された損失を除外する) と入力することで、このモジュールにコメントを追加します。 これで、実験でモジュールによりどのような処理が行われるかがひとめでわかります。

    ![Select Columns (列の選択) モジュールの正しい構成を示すスクリーンショット](./media/ui-tutorial-automobile-price-train-score/select-columns.png)

1. 検索ボックスに「**Clean**」と入力して、**Clean Missing Data** (見つからないデータのクリーンアップ) モジュールを見つけます。 **Clean Missing Data** (見つからないデータのクリーンアップ) モジュールを実験キャンバスにドラッグし、**Select Columns in Dataset** (データセット内の列の選択) モジュールに接続します。

1. **[Properties]\(プロパティ\)** ウィンドウで、 **[Cleaning mode]\(整理モード\)** の下の **[Remove entire row]\(行全体を削除\)** を選択します。 これらのオプションにより、**Clean Missing Data** (見つからないデータのクリーンアップ) では値が不足している行が削除され、データがクリーンアップされます。 モジュールをダブルクリックして、「Remove missing value rows」(値が不足している行を削除する) というコメントを入力します。

![Clean Missing Data (見つからないデータのクリーンアップ) モジュールの正しい構成を示すスクリーンショット](./media/ui-tutorial-automobile-price-train-score/clean-missing-data.png)

## <a name="train-the-model"></a>モデルをトレーニングする

これでデータを用意できたので、予測モデルを構築できます。 データをモデルのトレーニングに使用します。 その後、モデルをテストして、価格をどの程度の精度で予測できるかを確認します。

"**分類**" と "**回帰**" は、2 種類の教師あり機械学習アルゴリズムです。 **分類**は、色 (赤、青、または緑) のような定義された一連のカテゴリから答えを予測するものです。 **回帰**は、数値を予測する目的で使用されます。

予測したい価格は数値であるため、回帰アルゴリズムを使用できます。 この例では、線形回帰モデルを使用します。

価格が含まれた一連のデータを指定して、モデルをトレーニングします。 モデルによってデータがスキャンされ、自動車のフィーチャーと価格の相関関係が検出されます。 次に、よく知られている自動車のフィーチャーのセットをモデルに与えることで、モデルのテストを行い、モデルがその既知の価格をどの程度の精度で予測できるかを確認します。

データを別個のトレーニング データセットとテスト データセットに分割して、モデルのトレーニングとテストの両方に使用します。

1. 検索ボックスに「**split data**」と入力して、**Split Data** (データの分割) モジュールを見つけ、**Clean Missing Data** (見つからないデータのクリーンアップ) モジュールの左側のポートに接続します。

1. 接続した **Split Data** (データの分割) モジュールを選択します。 [Properties]\(プロパティ\) ウィンドウで、[Fraction of rows in the first output dataset]\(最初の出力データセットにおける列の割合\) を 0.7 に設定します。 このようにして、データの 70% をモデルのトレーニングに使用し、30% をテスト用に保持しておきます。

    ![[Properties]\(プロパティ\) ウィンドウの正しい構成を示すスクリーンショット "Split Data" (データの分割) の各値は、"Split Rows" (行の分割)、0.7、Randomized split (ランダム分割)、0、False である必要があります。](./media/ui-tutorial-automobile-price-train-score/split-data.png)

1. **Split Data** (データの分割) をダブルクリックし、「Split the dataset into training set(0.7) and test set(0.3)」(データセットをトレーニング セット (0.7) とテスト セット (0.3) に分割する) というコメントを入力します。

1. 学習アルゴリズムを選択するには、モジュール パレットの検索ボックスをオフにします。

1. **[Machine Learning]** を展開し、 **[Initialize Model]\(モデルの初期化\)** を展開します。 これにより、機械学習アルゴリズムの初期化に使用できるモジュールのカテゴリが複数表示されます。

1. この実験では、 **[Regression]\(回帰\)**  > **Linear Regression** (線形回帰) モジュールを選択し、実験キャンバスにドラッグします。

    ![[Properties]\(プロパティ\) ウィンドウの正しい構成を示すスクリーンショット "Split Data" (データの分割) の各値は、"Split Rows" (行の分割)、0.7、Randomized split (ランダム分割)、0、False である必要があります。](./media/ui-tutorial-automobile-price-train-score/linear-regression-module.png)

1. **Train Model** (モデルのトレーニング) モジュールを見つけて、実験にドラッグします。 Linear Regression (線形回帰) モジュールの出力を、Train Model (モデルのトレーニング) モジュールの左側の入力に接続します。次に、**Split Data** (データの分割) モジュールのトレーニング データ出力 (左側のポート) を、**Train Model** (モデルのトレーニング) モジュールの右側の入力に接続します。

    ![Train Model (モデルのトレーニング) モジュールの正しい構成を示すスクリーンショット。 Linear Regression (線形回帰) モジュールが、Train Model (モデルのトレーニング) モジュールの左側のポートに接続され、Split Data (データの分割) モジュールが、Train Model (モデルのトレーニング) モジュールの右側のポートに接続されています。](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. **Train Model** (モデルのトレーニング) モジュールを選択します。 [Properties]\(プロパティ\) ウィンドウで [Launch column selector]\(列セレクターの起動\) を選択し、 **[Include]\(含める\)、[column names]\(列名\)** の横にあるボックスに「**price**」と入力します。 price (価格) は、モデルで予測する値です。

    ![列セレクター モジュールの正しい構成を示すスクリーンショット。 [With rules]\(規則を使用\) > [Include]\(含める\)、[column names]\(列名\) > "price"](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    これで、実験の表示は次のようになります。
    ![Train Model (モデルのトレーニング) モジュールを追加した後の実験の正しい構成を示すスクリーンショット。](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

### <a name="run-the-training-experiment"></a>トレーニング実験を実行する

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

## <a name="score-and-evaluate-the-model"></a>モデルにスコアを付け、評価する

これまでにデータの 70% を使用してモデルをトレーニングしました。ここからは残りの 30% のデータにスコアを付け、モデルの精度を確認します。

1. 検索ボックスに「**score model**」と入力して、**Score Model** (モデルのスコア付け) モジュールを見つけ、このモジュールを実験キャンバスにドラッグします。 **Train Model** (モデルのトレーニング) モジュールの出力を、**Score Model** (モデルのスコア付け) の左側の入力ポートに接続します。 **Split Data** (データの分割) モジュールのテスト データの出力 (右側のポート) を、**Score Model** (モデルのスコア付け) の右側の入力ポートに接続します。

1. 検索ボックスに「**evaluate**」と入力して、**Evaluate Model** (モデルの評価) を見つけ、このモジュールを実験キャンバスにドラッグします。 **Score Model** (モデルのスコア付け) モジュールの出力を、**Evaluate Model** (モデルの評価) の左側の入力に接続します。 最終的な実験は次のようになります。

    ![実験の最終的な正しい構成を示すスクリーンショット。](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. 以前に使用したのと同じコンピューティング ターゲットを使用して、実験を実行します。

1. **Score Model** (モデルのスコア付け) の出力ポートをクリックし、 **[Visualize]\(視覚化\)** を選択すると、**Score Model** (モデルのスコア付け) モジュールの出力が表示されます。 出力に、予測された価格の値と、テスト データからの既知の値が表示されます。

    !["Scored Label" (スコア付けラベル) 列が強調表示された状態の出力の視覚化のスクリーンショット](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Evaluate Model (モデルの評価) モジュールの出力を表示するには、出力ポートを選択し、[Visualize]\(視覚化\) を選択します。

    ![最終的な実験の評価結果を示すスクリーンショット。](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

作成したモデルに対して、以下の統計値が表示されます。

* **Mean Absolute Error** (MAE、平均絶対誤差):絶対誤差の平均です (誤差とは、予測された値と実際の値との差です)。
* **Root Mean Squared Error** (RMSE、二乗平均平方根誤差):テスト データセットに対して実行した予測の二乗誤差平均の平方根です。
* **Relative Absolute Error** (相対絶対誤差):実際の値とすべての実際の値の平均との絶対差を基準にした絶対誤差の平均です。
* **Relative Squared Error** (相対二乗誤差):実際の値とすべての実際の値の平均との二乗差を基準にした二乗誤差の平均です。
* **Coefficient of Determination** (決定係数):R-2 乗値ともいいます。どの程度モデルが高い精度でデータと適合するかを示す統計指標です。

この誤差の統計情報は、それぞれ小さいほど良いとされます。 値が小さいほど、予測が実際の値により近いことを示します。 Coefficient of Determination (決定係数) では、値が 1 (1.0) に近づくほど、予測の精度が高くなります。

## <a name="manage-experiments-in-azure-machine-learning-service-workspace"></a>Azure Machine Learning service ワークスペースで実験を管理する

ビジュアル インターフェイスで作成した実験は、Azure Machine Learning service ワークスペースから管理できます。 ワークスペースを使用して、個別の実験の実行、診断ログ、実行グラフなど、より詳細な情報を参照してください。

1. [Azure Portal](https://portal.azure.com/) でワークスペースを開きます。  

1. ワークスペースで、 **[実験]** を選択します。 次に、作成した実験を選択します。

    ![Azure portal で実験に移動する方法を示すスクリーンショット](./media/ui-tutorial-automobile-price-train-score/portal-experiments.png)

    このページに、実験とその最新の実行の概要が表示されます。

    ![Azure portal での実験の統計情報の概要を示すスクリーンショット](./media/ui-tutorial-automobile-price-train-score/experiment-overview.png)

1. 特定の実行に関する詳細情報を表示するには、実行番号を選択します。

    ![詳細な実行レポートを示すスクリーンショット](./media/ui-tutorial-automobile-price-train-score/run-details.png)

    実行レポートはリアルタイムで更新されます。 実験で **Execute Python Script** (Python スクリプトの実行) モジュールを使用した場合は、 **[ログ]** タブで出力にスクリプト ログを指定できます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルのパート 1 では、次の手順を完了しました。

* クイック スタートで作成した実験を再利用する
* データを準備する
* モデルをトレーニングする
* モデルにスコアを付け、評価する

パート 2 では、モデルを Azure Web サービスとしてデプロイする方法を学習します。

> [!div class="nextstepaction"]
> [モデルのデプロイに進む](ui-tutorial-automobile-price-deploy.md)
