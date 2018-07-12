---
title: Azure Machine Learning サービス (プレビュー) のモデル構築チュートリアル | Microsoft Docs
description: このチュートリアルでは、Azure Machine Learning サービス (プレビュー) の使い方をエンド ツー エンドで詳しく説明します。 このパート 2 では、実験について説明します。
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/15/2018
ms.openlocfilehash: 77dcad0f3e49b601110f8700245aaf479bde1c4e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38722781"
---
# <a name="tutorial-2-classify-iris---build-a-model"></a>チュートリアル 2: あやめの分類: モデルを構築する
Azure Machine Learning サービス (プレビュー) は、データ サイエンスと高度な分析を支援する統合ソリューションです。データの準備、実験の開発、モデルのデプロイをクラウド スケールで行うプロフェッショナルなデータ サイエンティストを対象としています。

このチュートリアルは、**3 部構成のシリーズのパート 2** になります。 チュートリアルのこのパートでは、Azure Machine Learning サービスを使って次の作業を行います。

> [!div class="checklist"]
> * スクリプトを開いてコードを確認する
> * ローカル環境でスクリプトを実行する
> * 実行履歴を確認する
> * ローカル Azure CLI ウィンドウでスクリプトを実行する
> * ローカル Docker 環境でスクリプトを実行する
> * リモート Docker 環境でスクリプトを実行する
> * クラウド Azure HDInsight 環境でスクリプトを実行する

このチュートリアルでは、経時的な変化を伴わない[あやめデータ セット](https://en.wikipedia.org/wiki/Iris_flower_data_set)を使用します。 

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。
- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 
- 実験アカウントと Azure Machine Learning Workbench (こちらの[クイック スタート](../service/quickstart-installation.md)の説明に従ってインストールされていること)
- プロジェクトおよびあやめデータ ([チュートリアルのパート 1](tutorial-classifying-iris-part-1.md) で準備したもの)
- Docker エンジン (ローカルにインストールされ、実行されていること)。 Docker の Community Edition でかまいません。 Docker のインストール方法については、こちら (https://docs.docker.com/engine/installation/) をご覧ください。

## <a name="review-irissklearnpy-and-the-configuration-files"></a>iris_sklearn.py と構成ファイルを確認する

1. Azure Machine Learning Workbench アプリケーションを起動します。

1. [チュートリアル シリーズのパート 1](tutorial-classifying-iris-part-1.md) で作成した **myIris** プロジェクトを開きます。

2. 開いたプロジェクトで、左端のウィンドウにある **[ファイル]** ボタン (フォルダー アイコン) を選択して、プロジェクト フォルダーのファイルを一覧表示します。

   ![Azure Machine Learning Workbench プロジェクトを開く](media/tutorial-classifying-iris/2-project-open.png)

3. **iris_sklearn.py** Python スクリプト ファイルを選択します。 

   ![スクリプトを選択](media/tutorial-classifying-iris/2-choose-iris_sklearn.png)

   Workbench の新しいテキスト エディター タブにコードが表示されます。 このスクリプトは、このチュートリアル パートの全体にわたって使います。 

   >[!NOTE]
   >このサンプル プロジェクトは頻繁に更新されるため、表示されるコードが前出のコードとは厳密に一致しない場合があります。
   
   ![ファイルを開く](media/tutorial-classifying-iris/open_iris_sklearn.png)

4. Python スクリプト コードを観察して、コーディングのスタイルを把握します。 

   スクリプト **iris_sklearn.py** は、次のタスクを実行します。

   * 既定のデータ準備パッケージ (**iris.dprep**) を読み込んで [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) を作成します。 

   * 問題を解きづらくするためにランダム機能を追加します  あやめデータセットはほぼ 100% の精度で簡単に分類できるほどに小さいため、ランダム性が必要となります。

   * [scikit-learn](http://scikit-learn.org/stable/index.html) 機械学習ライブラリを使ってロジスティック回帰モデルを構築します。  このライブラリには、Azure Machine Learning Workbench が既定で付属しています。

   * [pickle](https://docs.python.org/3/library/pickle.html) ライブラリを使用して、モデルを `outputs` フォルダーのファイルにシリアル化します。 
   
   * シリアル化されたモデルを読み込み、逆シリアル化してメモリに展開します。

   * 逆シリアル化されたモデルを使用して、新しいレコードに対する予測を行います。 

   * [matplotlib](https://matplotlib.org/) ライブラリを使用して、混同行列と多クラス ROC (Receiver Operating Characteristic: 受信者動作特性) 曲線という 2 つのグラフをプロットし、それらを `outputs` フォルダーに保存します。 ご利用の環境にまだ存在しない場合は、このライブラリをインストールしてください。

   * 正則化項とモデル精度を実行履歴に自動でプロットします。 `run_logger` オブジェクトは、正則化項とモデル精度をログに記録する目的で終始使用されます。 


## <a name="run-irissklearnpy-in-your-local-environment"></a>ローカル環境で iris_sklearn.py を実行する

1. Azure Machine Learning のコマンド ライン インターフェイス (CLI) を起動します。
   1. Azure Machine Learning Workbench を起動します。

   1. [ワークベンチ] メニューの **[ファイル]** > **[コマンド プロンプトを開く]** を選択します。 
   
   Azure Machine Learning コマンド ライン インターフェイス (CLI) ウィンドウは、Windows 上のプロジェクト フォルダー `C:\Temp\myIris\>` で起動します。 このプロジェクトは、チュートリアルのパート 1 で作成したものと同じです。

   >[!IMPORTANT]
   >後続の手順を実行するには、この CLI ウィンドウを使用する必要があります。

1. Python プロット ライブラリ **matplotlib** がまだインストールされていない場合は、CLI ウィンドウでこのライブラリをインストールします。

   **iris_sklearn.py** スクリプトは、**scikit-learn** と **matplotlib** の 2 つの Python パッケージに依存しています。  **scikit-learn** パッケージについては、Azure Machine Learning Workbench であらかじめインストールされます。 一方、**matplotlib** については、まだインストールされていなければ、手動でインストールする必要があります。

   **matplotlib** をインストールせずに読み進めた場合でも、このチュートリアルのコードは正常に実行されます。 ただし、履歴で視覚化されるような混同行列の出力や多クラス ROC 曲線のプロットをコードから生成することはできません。

   ```azurecli
   pip install matplotlib
   ```

   このインストールの所要時間は約 1 分です。

1. Workbench アプリケーションに戻ります。 

1. **[iris_sklearn.py]** というタブを見つけます。 

   ![スクリプトのタブを見つける](media/tutorial-classifying-iris/2-iris_sklearn-tab.png)

1. そのタブのツール バーで、実行環境として **[ローカル]** を選択し、実行するスクリプトとして `iris_sklearn.py` を選択します。 そのように選択されている場合もあります。

   ![ローカルとスクリプトの選択](media/tutorial-classifying-iris/2-local-script.png)

1. ツール バーの右側に移動し、**[引数]** フィールドに「`0.01`」と入力します。 

   この値は、ロジスティック回帰モデルの正則化項に対応します。

   ![ローカルとスクリプトの選択](media/tutorial-classifying-iris/2-local-script-arguments.png)

1. **[実行]** ボタンを選択します。 ジョブが直ちにスケジュールされます。 Workbench ウィンドウの右側の **[ジョブ]** ウィンドウにジョブが一覧表示されます。 

   ![ローカルとスクリプトの選択](media/tutorial-classifying-iris/2-local-script-arguments-run.png)

   しばらくすると、ジョブの状態が **[送信しています]** から **[実行中]** になり、最後に **[完了]** に変化します。

1. **[ジョブ]** ウィンドウで、ジョブの状態テキストの **[完了]** をクリックします。 

   ![sklearn の実行](media/tutorial-classifying-iris/2-completed.png)

   ポップアップ ウィンドウが開いて、実行の標準出力 (stdout) テキストが表示されます。 stdout テキストを閉じるには、ポップアップ ウィンドウの右上にある **[閉じる]** (**x**) ボタンを選択します。

   ![標準出力](media/tutorial-classifying-iris/2-standard-output.png)

9. **[ジョブ]** ウィンドウの同じジョブの状態で、**[完了]** 状態と開始時刻のすぐ上に表示されている青色のテキスト **iris_sklearn.py [n]** (_n_ は実行回数) を選択します。 **[Run Properties]\(実行プロパティ\)** ウィンドウが開き、その特定の実行について、次の情報が表示されます。
   - **実行プロパティ**情報
   - **Outputs**
   - **メトリック**
   - **視覚化** (存在する場合)
   - **ログ** 

   実行が完了すると、ポップアップ ウィンドウに次の結果が表示されます。

   >[!NOTE]
   >このチュートリアルでは、トレーニング セットにあらかじめランダム化を組み込んでいるため、実際の結果はこちらに示した結果と異なる場合があります。

   ```text
   Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]
   
   Iris dataset shape: (150, 5)
   Regularization rate is 0.01
   LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
          penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
          verbose=0, warm_start=False)
   Accuracy is 0.6792452830188679
   
   ==========================================
   Serialize and deserialize using the outputs folder.
   
   Export the model to model.pkl
   Import the model from model.pkl
   New sample: [[3.0, 3.6, 1.3, 0.25]]
   Predicted class is ['Iris-setosa']
   Plotting confusion matrix...
   Confusion matrix in text:
   [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
   Confusion matrix plotted.
   Plotting ROC curve....
   ROC curve plotted.
   Confusion matrix and ROC curve plotted. See them in Run History details pane.
   ```
    
10. **[Run Properties]\(実行プロパティ\)** タブを閉じて **[iris_sklearn.py]** タブに戻ります。 

11. 追加の実行を繰り返します。 

    **[引数]** フィールドには、`0.001` から `10` の範囲で一連の値を入力してください。 **[実行]** を選択して、さらに何度かコードを実行します。 コードのロジスティック回帰モデルに渡す引数の値を毎回変えることで、そのたびに異なる結果になります。

## <a name="review-the-run-history-in-detail"></a>実行履歴を詳しく確認する
Azure Machine Learning Workbench では、スクリプトの実行がすべて実行履歴レコードとしてキャプチャされます。 **[実行]** ビューを開くと、特定のスクリプトの実行履歴を表示することができます。

1. **実行**の一覧を開くには、左側のツール バーにある **[実行]** ボタン (時計のアイコン) を選択します。 次に、**[iris_sklearn.py]** を選択して `iris_sklearn.py` の **[Run Dashboard]\(実行ダッシュボード\)** を表示します。

   ![実行ビュー](media/tutorial-classifying-iris/run_view.png)

1. **[Run Dashboard]\(実行ダッシュボード\)** タブが表示されます。 

   複数回の実行を通じてキャプチャされた統計情報を確認します。 タブの上部にはグラフが表示されます。それぞれの実行には連番が付され、その詳細が画面下部のテーブルに一覧表示されます。

   ![実行ダッシュボード](media/tutorial-classifying-iris/run_dashboard.png)

1. テーブルをフィルター処理し、いずれかのグラフを選択すると、各実行の状態、期間、精度、正則化項が表示されます。 

1. **Runs** テーブルで、いくつかの実行の横にあるチェック ボックスをオンにします。 **[比較]** ボタンを選択すると、詳細な比較ウィンドウが表示されます。 横並びに表示される比較結果を確認してください。 

1. **[比較]** ウィンドウの左上にある、**実行の一覧**に戻るボタンを選択し、**[Run Dashboard]\(実行ダッシュボード\)** に戻ります。

   ![実行の一覧に戻る](media/tutorial-classifying-iris/2-compare-back.png)

1. 個々の実行を選択して、実行の詳細ビューを表示します。 **[Run Properties]\(実行プロパティ\)** セクションに、選択した実行の統計情報が一覧表示されることに注目してください。 出力フォルダーに書き込まれたファイルは **[出力]** セクションに一覧表示され、そこからファイルをダウンロードすることができます。

   ![実行の詳細](media/tutorial-classifying-iris/run_details.png)

   **[Visualizations]\(視覚化\)** セクションには、混同行列と多クラス ROC 曲線の 2 つのプロットが表示されます。 ログ ファイルはすべて **[ログ]** セクションでも確認できます。


## <a name="run-scripts-in-local-docker-environments"></a>ローカル Docker 環境でスクリプトを実行する

必要に応じて、ローカルの Docker コンテナーに対してスクリプトを実行して実験することができます。 Docker などの追加の実行環境を構成し、それらの環境でスクリプトを実行することができます。 

>[!NOTE]
>リモート Azure VM または Azure HDInsight Spark クラスターの Docker コンテナーで実行するディスパッチ用のスクリプトで実験する場合は、[Ubuntu ベースの Azure データ サイエンス仮想マシンまたは HDInsight クラスターの作成手順](how-to-create-dsvm-hdi.md)に関するページに従ってください。

1. その作業が済んでいない場合は、お使いの Windows マシンまたは MacOS マシンのローカルに Docker をインストールして起動してください。 詳しくは、Docker のインストール手順 (https://docs.docker.com/install/) をご覧ください。 Community Edition でかまいません。

1. 左側のウィンドウで**フォルダー** アイコンを選択して、目的のプロジェクトの**ファイル**を一覧表示します。 `aml_config` フォルダーを展開します。 

2. あらかじめ構成されているいくつかの環境が存在します (**docker-python**、**docker-spark**、および **local**)。 

   各環境に、2 つのファイルがあります。たとえば、`docker.compute` (**docker-python** および **docker-spark** の場合) と `docker-python.runconfig` などです。 各ファイルをテキスト エディターで開くと、特定のオプションを構成することができます。  

   邪魔にならないように、開いているテキスト エディターのタブで **[閉じる]** (**x**) を選択してください。

3. **docker-python** 環境を使用して **iris_sklearn.py** スクリプトを実行します。 

   - 左側のツール バーで**時計**アイコンを選択して **[実行]** パネルを開きます。 **[すべての実行]** を選択します。 

   - **[すべての実行]** タブの上部で、既定の **[local]** ではなく、**[docker-python]** を対象の環境として選択します。 

   - 次に右側へ移動して、実行するスクリプトとして **[iris_sklearn.py]** を選択します。 

   - **[引数]** フィールドは空のままにしてください。スクリプトによって既定値が指定されるためです。 

   - **[実行]** ボタンを選択します。

4. 新しいジョブが開始されることがわかります。 これは Workbench ウィンドウの右側の **[ジョブ]** ウィンドウに一覧表示されます。

   Docker に対して初めて実行するときは、ジョブの完了までに数分程度余分に時間がかかります。 

   Azure Machine Learning Workbench によって新しい Docker ファイルがバックグラウンドで構築されます。 
   その新しいファイルから、`docker.compute` ファイルに指定された基本 Docker イメージと `conda_dependencies.yml` ファイルに指定された Python の依存関係パッケージが参照されます。 
   
   Docker エンジンによって次のタスクが実行されます。

    - Azure から基本イメージをダウンロードする。
    - `conda_dependencies.yml` ファイルに指定された Python パッケージをインストールする。
    - Docker コンテナーを起動する。
    - 実行構成に応じて、プロジェクト フォルダーのローカル コピーをコピーするか、参照する。      
    - `iris_sklearn.py` スクリプトを実行する。

   最終的には、対象の環境に **[local]** を選んだときとまったく同じ結果になります。

5. 今度は Spark を試してみましょう。 Docker の基本イメージには、プレインストールされて構成済みの Spark インスタンスが含まれており、これを使用して PySpark スクリプトを実行することができます。 この基本イメージを使用すると、Spark のインストールと構成に時間を費やすことなく、Spark プログラムの開発とテストを簡単に行うことができます。 

   `iris_spark.py` ファイルを開きます。 このスクリプトは、`iris.csv` データ ファイルを読み込み、Spark Machine Learning ライブラリのロジスティック回帰アルゴリズムを使って、あやめデータセットを分類します。 実行環境を **[docker-spark]** に変更し、スクリプトを **[iris_spark.py]** に変更して、再度実行します。 このプロセスは、先ほどよりも少し時間がかかります。これは、Docker コンテナー内で Spark セッションを作成して開始する必要があるためです。 また、stdout も `iris_spark.py` の場合とは異なります。

6. 引数を変えながら、さらに数回実行します。 

7. `iris_spark.py` ファイルを開いて、Spark Machine Learning ライブラリを使用して構築されたロジスティック回帰モデルを確認します。 

8. **[ジョブ]** ウィンドウを操作し、履歴リスト ビューのほか、異なる実行環境で行われた実行の詳細ビューを開きます。

## <a name="run-scripts-in-the-cli-window"></a>CLI ウィンドウでスクリプトを実行する

1. Azure Machine Learning のコマンド ライン インターフェイス (CLI) を起動します。
   1. Azure Machine Learning Workbench を起動します。

   1. [ワークベンチ] メニューの **[ファイル]** > **[コマンド プロンプトを開く]** を選択します。 
   
   Windows 上のプロジェクト フォルダー `C:\Temp\myIris\>` で CLI プロンプトが起動します。 これは、チュートリアルのパート 1 で作成したプロジェクトです。

   >[!IMPORTANT]
   >後続の手順を実行するには、この CLI ウィンドウを使用する必要があります。

1. CLI ウィンドウで Azure にログインします。 az login の詳細については、[こちら](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)を参照してください。

   既にログインしていることも考えられます。 その場合は、この手順を省略してかまいません。

   1. コマンド プロンプトに、次のコマンドを入力します。
      ```azurecli
      az login
      ```

      このコマンドを実行すると、お使いのブラウザーから https://aka.ms/devicelogin にアクセスして使用するコードが返されます。

   1. お使いのブラウザーで https://aka.ms/devicelogin にアクセスします。

   1. プロンプトが表示されたら、CLI で受け取ったコードをブラウザーに入力します。

   Workbench アプリと CLI では、Azure リソースに対して認証を行う際に、独立した資格情報キャッシュが使用されます。 ログイン後は、キャッシュされたトークンの有効期限が切れるまで認証は不要です。 

1. 所属する組織に複数の Azure サブスクリプション (エンタープライズ環境) がある場合、使用するサブスクリプションを設定する必要があります。 使用するサブスクリプションを探し、サブスクリプション ID を使って設定した後、そのサブスクリプションをテストします。

   1. 自分が利用できる Azure サブスクリプションをすべてリストするには、次のコマンドを使用します。
   
      ```azurecli
      az account list -o table
      ```

      **az account list** コマンドを実行すると、ご使用のログインで利用できるサブスクリプションの一覧が返されます。 
      複数存在する場合は、使用するサブスクリプションのサブスクリプション ID 値を特定します。

   1. 既定のアカウントとして使用する Azure サブスクリプションを設定します。
   
      ```azurecli
      az account set -s <your-subscription-id>
      ```
      \<your-subscription-id\> には、使用するサブスクリプションの ID 値を指定します。 かっこは含めないでください。

   1. 現在のサブスクリプションに関する詳細情報を要求して、新しいサブスクリプション設定を確認します。 

      ```azurecli
      az account show
      ```    

1. Python プロット ライブラリ **matplotlib** がまだインストールされていない場合は、CLI ウィンドウでこのライブラリをインストールします。

   ```azurecli
   pip install matplotlib
   ```

1. CLI ウィンドウで、**iris_sklearn.py** スクリプトを実験として送信します。

   iris_sklearn.py の実行は、ローカル コンピューティング コンテキストに対して実行されます。

   + Windows の場合:
     ```azurecli
     az ml experiment submit -c local .\iris_sklearn.py
     ```

   + MacOS の場合:
     ```azurecli
     az ml experiment submit -c local iris_sklearn.py
     ```
   
   出力は次のようになります。
    ```text
    RunId: myIris_1521077190506
    
    Executing user inputs .....
    ===========================
    
    Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:52:12) 
    [GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)]
    
    Iris dataset shape: (150, 5)
    Regularization rate is 0.01
    LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
              intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
              penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
              verbose=0, warm_start=False)
    Accuracy is 0.6792452830188679
        
    ==========================================
    Serialize and deserialize using the outputs folder.
    
    Export the model to model.pkl
    Import the model from model.pkl
    New sample: [[3.0, 3.6, 1.3, 0.25]]
    Predicted class is ['Iris-setosa']
    Plotting confusion matrix...
    Confusion matrix in text:
    [[50  0  0]
     [ 1 37 12]
     [ 0  4 46]]
    Confusion matrix plotted.
    Plotting ROC curve....
    ROC curve plotted.
    Confusion matrix and ROC curve plotted. See them in Run History details page.
    
    Execution Details
    =================
    RunId: myIris_1521077190506
    ```

1. 出力結果を確認します。 Workbench を使ってスクリプトを実行したときと同じ出力内容と結果であることがわかります。 

1. お使いのマシンに Docker がインストールされている場合、Docker 実行環境を使用して、CLI ウィンドウから Python スクリプト (**iris_sklearn.py**) を実行します。

   + コンテナーが Windows 上にある場合: 
     |実行<br/>環境|Windows 上のコマンド|
     |---------------------|------------------|
     |Python|`az ml experiment submit -c docker-python .\iris_sklearn.py 0.01`|
     |Spark|`az ml experiment submit -c docker-spark .\iris_spark.py 0.1`|

   + コンテナーが MacOS 上にある場合: 
     |実行<br/>環境|Windows 上のコマンド|
     |---------------------|------------------|
     |Python|`az ml experiment submit -c docker-python iris_sklearn.py 0.01`|
     |Spark|`az ml experiment submit -c docker-spark iris_spark.py 0.1`|

1. Workbench に戻って次の手順を実行します。
   1. 左側のウィンドウにあるフォルダー アイコンを選択してプロジェクト ファイルを一覧表示します。
   
   1. **run.py** という名前の Python スクリプトを開きます。 このスクリプトは、正則化項を変化させながら試すのに便利です。 

   ![実行の一覧に戻る](media/tutorial-classifying-iris/2-runpy.png)

1. さまざまな正則化項で実験を複数回実行してください。 

   このスクリプトは、`10.0` という極端に大きな正則化項で `iris_sklearn.py` ジョブを開始します。 その後、ループで実行を繰り返しながら正則化項を半分にしていき、`0.005` 以下になったところでループを抜けます。 

   このスクリプトには次のコードが記述されています。

   ![実行の一覧に戻る](media/tutorial-classifying-iris/2-runpy-code.png)

1. コマンド ラインから次のように **run.py** スクリプトを実行します。

   ```cmd
   python run.py
   ```

   このコマンドを実行すると、iris_sklearn.py が、異なる正則化項で複数回送信されます。

   `run.py` が完了すると、Workbench の実行履歴リスト ビューにさまざまなメトリックのグラフが表示されます。

## <a name="run-scripts-in-a-remote-docker-container"></a>リモート Docker コンテナーでスクリプトを実行する
リモート Linux マシンの Docker コンテナーでスクリプトを実行するには、そのリモート マシンへの SSH アクセス (ユーザー名とパスワード) が必要です。 また、マシンには Docker エンジンがインストールされ、実行されている必要があります。 このような Linux マシンを入手する方法としては、Azure 上に Ubuntu ベースのデータ サイエンス仮想マシン (DSVM) を作成するのが最も簡単です。 [Azure ML Workbench で使用する Ubuntu DSVM を作成する方法](how-to-create-dsvm-hdi.md#create-an-ubuntu-dsvm-in-azure-portal)に関するページを参照してください。

>[!NOTE] 
>CentOS ベースの DSVM はサポート "*されません*"。

1. VM を作成した後、`.runconfig` と `.compute` という一対のファイルを生成して、その VM を実行環境としてアタッチすることができます。 次のコマンドを使用してファイルを生成します。 

 新しいコンピューティング ターゲットに `myvm` という名前を付けましょう。
 
   ```azurecli
   az ml computetarget attach remotedocker --name myvm --address <your-IP> --username <your-username> --password <your-password>
   ```
   
   >[!NOTE]
   >IP アドレスは、パブリックにアドレス指定可能な完全修飾ドメイン名 (FQDN) にすることもできます (例: `vm-name.southcentralus.cloudapp.azure.com`)。 DSVM に FQDN を追加し、それを IP アドレスの代わりに使うことをお勧めします。 お勧めする理由は、いずれコストを抑えるために VM を停止されることも考えられるからです。 さらに、次回 VM を起動したときには、IP アドレスが変わっている可能性があります。

   >[!NOTE]
   >ユーザー名とパスワードの認証に加えて、`--private-key-file` オプションと `--private-key-passphrase` オプション (必要な場合) を使用して、秘密キーとそれに対応するパスフレーズ (ある場合) を指定することができます。

   次に、**myvm** コンピューティング ターゲットを準備します。次のコマンドを実行してください。
   
   ```azurecli
   az ml experiment prepare -c myvm
   ```
   
   前掲のコマンドによって、VM に Docker イメージが作成され、スクリプトが実行できる状態になります。
   
   >[!NOTE]
   >`myvm.runconfig` の `PrepareEnvironment` の値は、既定値の `false` から `true` に変更することもできます。 これで初回実行の一環として Docker コンテナーが自動的に準備されます。

2. `aml_config` に生成された `myvm.runconfig` ファイルを編集し、フレームワークを既定値の `PySpark` から `Python` に変更します。

   ```yaml
   Framework: Python
   ```
   >[!NOTE]
   >PySpark も機能するはずですが、Python スクリプトを実行するために実際には Spark セッションを必要としない場合は、Python を使用する方が効率的です。

3. 先ほど CLI ウィンドウで実行したものと同じコマンドを実行します。ただし、今度は対象を _myvm_ にして、リモート Docker コンテナーで iris_sklearn.py を実行します。
   ```azurecli
   az ml experiment submit -c myvm iris_sklearn.py
   ```
   実行場所がリモート Linux VM であることを除けば、`docker-python` 環境を使った場合と同じようにコマンドが実行されます。 CLI ウィンドウには同じ出力情報が表示されます。

4. コンテナー内の Spark を試してみましょう。 エクスプローラーを開きます。 `myvm.runconfig` ファイルをコピーして `myvm-spark.runconfig` という名前を付けます。 新しいファイルを編集し、`Framework` の設定を `Python` から `PySpark` に変更します。
   ```yaml
   Framework: PySpark
   ```
   `myvm.compute` ファイルには一切変更を加えないでください。 同じ VM 上の同じ Docker イメージが Spark の実行に使用されます。 新しい `myvm-spark.runconfig` の `Target` フィールドから、同じ `myvm.compute` ファイルがその名前 (`myvm`) で参照されます。

5. 次のコマンドを入力し、リモート Docker コンテナー内で実行されている Spark インスタンスで **iris_spark.py** スクリプトを実行します。
   ```azureli
   az ml experiment submit -c myvm-spark .\iris_spark.py
   ```

## <a name="run-scripts-in-hdinsight-clusters"></a>HDInsight クラスターでスクリプトを実行する
このスクリプトは、HDInsight Spark クラスターで実行することもできます。 [Azure ML Workbench で使用する HDInsight Spark クラスターを作成する方法](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal)に関するページを参照してください。

>[!NOTE] 
>HDInsight クラスターは、プライマリ ストレージとして Azure BLOB を使用する必要があります。 Azure Data Lake Storage はまだサポートされていません。

1. Spark for Azure HDInsight クラスターにアクセスできる場合は、次に示した HDInsight 実行構成コマンドを生成します。 パラメーターとして HDInsight クラスター名、HDInsight のユーザー名とパスワードを指定してください。 

   次のコマンドを使用して、HDInsight クラスターを指すコンピューティング ターゲットを作成します。

   ```azurecli
   az ml computetarget attach cluster --name myhdi --address <cluster head node FQDN> --username <your-username> --password <your-password>
   ```

   HDInsight クラスターを準備するには、次のコマンドを実行します。

   ```
   az ml experiment prepare -c myhdi
   ```

   通常、クラスターのヘッド ノードの FQDN は `<your_cluster_name>-ssh.azurehdinsight.net` になります。

   >[!NOTE]
   >`username` は、HDInsight のセットアップ時に定義した、クラスターの SSH ユーザー名です。 既定値は `sshuser` です。 `admin` (クラスターの管理者 Web サイトへのアクセス用としてセットアップ時に作成される別のユーザー) ではありません。 

2. HDInsight クラスターで **iris_spark.py** スクリプトを実行します。次のコマンドを使用してください。

   ```azurecli
   az ml experiment submit -c myhdi .\iris_spark.py
   ```

   >[!NOTE]
   >リモート HDInsight クラスターで実行するときは、`admin` ユーザー アカウントを使って YARN (Yet Another Resource Negotiator) ジョブの実行情報を `https://<your_cluster_name>.azurehdinsight.net/yarnui` で確認することもできます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順
3 部構成のチュートリアル シリーズのパート 2 では、次の作業を行う方法について説明しました。
> [!div class="checklist"]
> * Workbench でスクリプトを開いてコードを確認する
> * ローカル環境でスクリプトを実行する
> * 実行履歴を確認する
> * ローカル Docker 環境でスクリプトを実行する

それでは、このチュートリアル シリーズのパート 3 に進み、リアルタイム Web サービスとして作成したロジスティック回帰モデルをデプロイしてみましょう。

> [!div class="nextstepaction"]
> [チュートリアル 3 - モデルをデプロイする](tutorial-classifying-iris-part-3.md)
