---
title: "Azure Machine Learning サービス (プレビュー) のモデル構築チュートリアル | Microsoft Docs"
description: "このチュートリアルでは、Azure Machine Learning サービス (プレビュー) の使い方をエンド ツー エンドで詳しく説明します。 このパート 2 では、実験について説明します。"
services: machine-learning
author: hning86
ms.author: haining, j-martens
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/28/2018
ms.openlocfilehash: 9a1613e2137e178d00a24f9f5b3c802f8a894b15
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2018
---
# <a name="tutorial-classify-iris-part-2---build-a-model"></a>チュートリアル: あやめの分類 (パート 2) - モデルを構築する
Azure Machine Learning サービス (プレビュー) は、データ サイエンスと高度な分析をエンド ツー エンドで支援する統合ソリューションです。データの準備、実験の開発、モデルのデプロイをクラウド スケールで行うプロフェッショナルなデータ サイエンティストを対象としています。

このチュートリアルは、**3 部構成のシリーズのパート 2** になります。 チュートリアルのこのパートでは、Azure Machine Learning サービス (プレビュー) を使って次の作業を行います。

> [!div class="checklist"]
> * Azure Machine Learning Workbench を使用する。
> * スクリプトを開いてコードを確認する。
> * ローカル環境でスクリプトを実行する。
> * 実行履歴を確認する。
> * ローカル Docker 環境でスクリプトを実行する。
> * ローカル Azure CLI ウィンドウでスクリプトを実行する。
> * リモート Docker 環境でスクリプトを実行する。
> * クラウド Azure HDInsight 環境でスクリプトを実行する。

このチュートリアルでは、経時的な変化を伴わない[あやめデータ セット](https://en.wikipedia.org/wiki/Iris_flower_data_set)を使用します。 スクリーンショットは Windows のものですが、Mac OS を使用した場合も大きな違いはありません。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件
このチュートリアル シリーズのパート 1 を済ませます。 このチュートリアルの手順を始める前に、[データの準備に関するチュートリアル](tutorial-classifying-iris-part-1.md)に従って Azure Machine Learning リソースを作成し、Azure Machine Learning Workbench アプリケーションをインストールしてください。

必要に応じて、ローカルの Docker コンテナーに対してスクリプトを実行して実験することができます。 そのためには、ご使用の Windows マシンまたは Mac OS マシンに Docker エンジン (Community Edition で十分です) がインストールされ、ローカルで起動されている必要があります。 Docker のインストールについて詳しくは、[Docker のインストール手順](https://docs.docker.com/engine/installation/)をご覧ください。

リモート Azure VM または Azure HDInsight Spark クラスターの Docker コンテナーで実行するディスパッチ用のスクリプトで実験する場合は、[Ubuntu ベースの Azure データ サイエンス仮想マシンまたは HDInsight クラスターの作成手順](how-to-create-dsvm-hdi.md)に関するページに従ってください。

## <a name="review-irissklearnpy-and-the-configuration-files"></a>iris_sklearn.py と構成ファイルを確認する
1. Azure Machine Learning Workbench アプリケーションを開き、チュートリアル シリーズの前のパートで作成した **myIris** プロジェクトを開きます。

2. プロジェクトが開いたら、左端のウィンドウにある **[ファイル]** ボタン (フォルダー アイコン) を選択して、プロジェクト フォルダーのファイルを一覧表示します。

3. **iris_sklearn.py** ファイルを選択します。 Workbench の新しいテキスト エディター タブに Python コードが表示されます。

   ![ファイルを開く](media/tutorial-classifying-iris/open_iris_sklearn.png)

   >[!NOTE]
   >このサンプル プロジェクトは頻繁に更新されるため、表示されるコードが前出のコードとは厳密に一致しない場合があります。

4. Python スクリプト コードを確認して、コーディングのスタイルを把握します。 このスクリプトは、次を実行します。

   - データ準備パッケージ **iris.dprep** を読み込んで [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) を作成します。 

        >[!NOTE]
        >サンプル プロジェクトに付属する `iris.dprep` データ準備パッケージを使用していますが、おそらく、このチュートリアルのパート 1 で作成した `iris-1.dprep` ファイルと同じです。

   - 問題を解きづらくするためにランダム機能を追加します  あやめデータセットはほぼ 100% の精度で簡単に分類できるほどに小さいため、ランダム性が必要となります。

   - [scikit-learn](http://scikit-learn.org/stable/index.html) 機械学習ライブラリを使ってロジスティック回帰モデルを構築します。 

   - [pickle](https://docs.python.org/2/library/pickle.html) ライブラリを挿入してモデルを `outputs` フォルダーのファイルにシリアル化します。 その後それがスクリプトによって読み込まれ、逆シリアル化されてメモリに展開されます。

   - 逆シリアル化されたモデルを使用して、新しいレコードに対する予測を行います。 

   - [matplotlib](https://matplotlib.org/) ライブラリを使用して、混同行列と多クラス ROC (Receiver Operating Characteristic: 受信者動作特性) 曲線という 2 つのグラフをプロットし、それらを `outputs` フォルダーに保存します。

   - `run_logger` オブジェクトは、正則化項とモデル精度をログに記録する目的で終始使用されます。 また、ログは実行履歴に自動的にプロットされます。


## <a name="execute-irissklearnpy-script-in-a-local-environment"></a>ローカル環境で iris_sklearn.py スクリプトを実行する

**iris_sklearn.py** スクリプトを初めて実行する準備をしましょう。 このスクリプトには、**scikit-learn** パッケージと **matplotlib** パッケージが必要です。 **scikit-learn** パッケージは、Azure Machine Learning Workbench によって既にインストールされています。 **matplotlib** は自分でインストールする必要があります。 

1. Azure Machine Learning Workbench で **[ファイル]** メニューを選択し、**[コマンド プロンプトを開く]** を選択してコマンド プロンプトを開きます。 このコマンド ライン インターフェイス ウィンドウは、"*Azure Machine Learning Workbench CLI ウィンドウ*" または略して "*CLI ウィンドウ*" と呼ばれています。

2. CLI ウィンドウで次のコマンドを入力して、Python パッケージ **matplotlib** をインストールします。 通常は 1 分かからずに完了します。

   ```azurecli
   pip install matplotlib
   ```

   >[!NOTE]
   >上記の `pip install` コマンドを省略しても `iris_sklearn.py` のコードは正常に実行されます。 ただし、単に `iris_sklearn.py` を実行しただけでは、履歴で視覚化されるような混同行列の出力や多クラス ROC 曲線のプロットは生成されません。

3. Workbench アプリのウィンドウに戻ります。 

4. **[iris_sklearn.py]** タブの上部にあるツール バーで、**[保存]** アイコンの横にあるドロップダウン メニューを選択して開き、**[実行構成]** を選択します。 実行環境として **[ローカル]** を選択し、実行するスクリプトとして `iris_sklearn.py` を入力します。

5. 次に、ツール バーの右側に移動し、**[引数]** フィールドに「`0.01`」と入力します。 

   ![実行コントロール](media/tutorial-classifying-iris/run_control.png)

6. **[実行]** ボタンを選択します。 ジョブが直ちにスケジュールされます。 Workbench ウィンドウの右側の **[ジョブ]** ウィンドウにジョブが一覧表示されます。 

7. しばらくすると、ジョブの状態が **[送信しています]** から **[実行中]** になり、その後 **[完了]** になります。

   ![sklearn の実行](media/tutorial-classifying-iris/run_sklearn.png)

8. **[ジョブ]** ウィンドウで、ジョブの状態テキストの **[完了]** をクリックします。 ポップアップ ウィンドウが開いて、実行中のスクリプトの標準出力 (stdout) テキストが表示されます。 stdout テキストを閉じるには、ポップアップ ウィンドウの右上にある **[閉じる]** (**x**) ボタンを選択します。

9. **[ジョブ]** ウィンドウの同じジョブの状態で、**[完了]** 状態と開始時刻のすぐ上に表示されている青色のテキスト **iris_sklearn.py [n]** (_n_ は実行回数) を選択します。 **[Run Properties]\(実行プロパティ\)** ウィンドウが開き、その特定の実行について、次の情報が表示されます。
   - **実行プロパティ**情報
   - **出力**ファイル
   - **視覚化** (存在する場合)
   - **ログ** 

   実行が完了すると、ポップアップ ウィンドウに次の結果が表示されます。

   >[!NOTE]
   >トレーニング セットにはあらかじめランダム化を組み込んでいるため、実際の結果はこちらに示した結果と異なる場合があります。

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

11. 実行を繰り返します。 

    **[引数]** フィールドには、`0.001` ～ `10` の範囲で一連の異なる数値を入力してください。 **[実行]** を選択して、さらに何度かコードを実行します。 コードのロジスティック回帰アルゴリズムに渡す引数の値を毎回変えることで、その都度異なる結果が出力されます。

## <a name="review-the-run-history-in-detail"></a>実行履歴を詳しく確認する
Azure Machine Learning Workbench では、スクリプトの実行がすべて実行履歴レコードとしてキャプチャされます。 **[実行]** ビューを開くと、特定のスクリプトの実行履歴を表示することができます。

1. **実行**の一覧を開くには、左側のツール バーにある **[実行]** ボタン (時計のアイコン) を選択します。 次に、**[iris_sklearn.py]** を選択して `iris_sklearn.py` の **[Run Dashboard]\(実行ダッシュボード\)** を表示します。

   ![実行ビュー](media/tutorial-classifying-iris/run_view.png)

2. **[Run Dashboard]\(実行ダッシュボード\)** タブが表示されます。 複数回の実行を通じてキャプチャされた統計情報を確認します。 タブの上部にはグラフが表示されます。それぞれの実行には連番が付され、その詳細が画面下部のテーブルに一覧表示されます。

   ![実行ダッシュボード](media/tutorial-classifying-iris/run_dashboard.png)

3. テーブルをフィルター処理し、いずれかのグラフを選択すると、各実行の状態、期間、精度、正則化項が表示されます。 

4. **実行**テーブルで 2 ～ 3 個の実行を選択し、**[比較]** ボタンを選択すると、詳細な比較ウィンドウが表示されます。 横並びに表示される比較結果を確認してください。 **[比較]** ウィンドウの左上にある、**実行の一覧**に戻るボタンを選択し、**[Run Dashboard]\(実行ダッシュボード\)** に戻ります。

5. 個々の実行を選択して、実行の詳細ビューを表示します。 **[Run Properties]\(実行プロパティ\)** セクションに、選択した実行の統計情報が一覧表示されることに注目してください。 出力フォルダーに書き込まれたファイルは **[出力]** セクションに一覧表示され、そこからファイルをダウンロードすることができます。

   ![実行の詳細](media/tutorial-classifying-iris/run_details.png)

   **[Visualizations]\(視覚化\)** セクションには、混同行列と多クラス ROC 曲線の 2 つのプロットが表示されます。 ログ ファイルはすべて **[ログ]** セクションでも確認できます。

## <a name="execute-scripts-in-the-local-docker-environment"></a>ローカル Docker 環境でスクリプトを実行する

Machine Learning では、Docker などの追加の実行環境を簡単に構成し、それらの環境でスクリプトを実行することができます。 

>[!IMPORTANT]
>この手順を実施するためには、あらかじめ Docker エンジンをローカルにインストールして起動しておく必要があります。 詳しくは、Docker のインストール手順をご覧ください。

1. 左側のウィンドウで**フォルダー** アイコンを選択して、目的のプロジェクトの**ファイル**を一覧表示します。 `aml_config` フォルダーを展開します。 

2. あらかじめ構成されているいくつかの環境が存在することに注目してください (**docker-python**、**docker-spark**、**local** など)。 

   それぞれの環境には、2 つのファイルがあります (`docker-python.compute` と `docker-python.runconfig` など)。 各ファイルをテキスト エディターで開くと、特定のオプションを構成することができます。  

   邪魔にならないように、開いているテキスト エディターのタブで **[閉じる]** (**x**) を選択してください。

3. **docker-python** 環境を使用して **iris_sklearn.py** スクリプトを実行します。 

   - 左側のツール バーで**時計**アイコンを選択して **[実行]** パネルを開きます。 **[すべての実行]** を選択します。 
   - **[すべての実行]** タブの上部で、既定の **[local]** ではなく、**[docker-python]** を対象の環境として選択します。 
   - 次に右側へ移動して、実行するスクリプトとして **[iris_sklearn.py]** を選択します。 
   - **[引数]** フィールドは空のままにしてください。スクリプトによって既定値が指定されるためです。 
   - **[実行]** ボタンを選択します。

4. 新しいジョブが開始されることがわかります。 これは Workbench ウィンドウの右側の **[ジョブ]** ウィンドウに一覧表示されます。

   Docker に対して初めて実行するときは、完了までに数分程度余分に時間がかかります。 

   Azure Machine Learning Workbench によって新しい Docker ファイルがバックグラウンドで構築されます。 
   その新しいファイルから、`docker.compute` ファイルに指定された基本 Docker イメージと `conda_dependencies.yml` ファイルに指定された Python の依存関係パッケージが参照されます。 
   
   Docker エンジンによって次のタスクが実行されます。

    - Azure から基本イメージをダウンロードする。
    - `conda_dependencies.yml` ファイルに指定された Python パッケージをインストールする。
    - Docker コンテナーを起動する。
    - 実行構成に応じて、プロジェクト フォルダーのローカル コピーをコピーするか、参照する。      
    - `iris_sklearn.py` スクリプトを実行する。

   最終的には、対象の環境に **[local]** を選んだときとまったく同じ結果になります。

5. 今度は Spark を試してみましょう。 Docker の基本イメージには、プレインストールされて構成済みの Spark インスタンスが含まれています。 したがってそのインスタンスで PySpark スクリプトを実行することができます。 これにより、Spark のインストールと構成に時間を費やすことなく、Spark プログラムの開発とテストを簡単に行うことができます。 

   `iris_spark.py` ファイルを開きます。 このスクリプトは、`iris.csv` データ ファイルを読み込み、Spark Machine Learning ライブラリのロジスティック回帰アルゴリズムを使って、あやめデータセットを分類します。 実行環境を **[docker-spark]** に変更し、スクリプトを **[iris_spark.py]** に変更して、再度実行します。 このプロセスは、先ほどよりも少し時間がかかります。これは、Docker コンテナー内で Spark セッションを作成して開始する必要があるためです。 また、stdout も `iris_spark.py` の場合とは異なります。

6. 引数を変えながら、さらに数回実行します。 

7. `iris_spark.py` ファイルを開いて、Spark Machine Learning ライブラリを使って構築されたロジスティック回帰モデルを確認します。 

8. **[ジョブ]** ウィンドウを操作し、履歴リスト ビューのほか、異なる実行環境で行われた実行の詳細ビューを開きます。

## <a name="execute-scripts-in-the-azure-machine-learning-cli-window"></a>Azure Machine Learning CLI ウィンドウでスクリプトを実行する

1. Azure Machine Learning Workbench でコマンド ライン ウィンドウを開き、**[ファイル]** メニューを選択して、**[コマンド プロンプトを開く]** を選択します。 プロジェクト フォルダーでコマンド プロンプトが起動し、`C:\Temp\myIris\>` というプロンプトが表示されます。

   >[!IMPORTANT]
   >以降の手順を実行するには、(Workbench から起動した) コマンド ライン ウィンドウを使う必要があります。

2. コマンド プロンプトを使って Azure にログインします。 

   Workbench アプリと CLI では、Azure リソースに対して認証を行う際に、独立した資格情報キャッシュが使用されます。 キャッシュされたトークンの有効期限が切れない限り、ログインは 1 回だけでかまいません。 **az account list** コマンドを実行すると、ご使用のログインで利用できるサブスクリプションの一覧が返されます。 複数のサブスクリプションがある場合は、目的のサブスクリプションの ID 値を使います。 **az account set -s** コマンドでそのサブスクリプションを使用する既定のアカウントとして設定し、サブスクリプション ID 値を指定します。 次に、account **show** コマンドを使って設定を確認します。

   ```azurecli
   REM login by using the aka.ms/devicelogin site
   az login
   
   REM lists all Azure subscriptions you have access to 
   az account list -o table
   
   REM sets the current Azure subscription to the one you want to use
   az account set -s <subscriptionId>
   
   REM verifies that your current subscription is set correctly
   az account show
   ```

3. 認証が完了し、現在の Azure サブスクリプション コンテキストが設定されたら、CLI ウィンドウに次のコマンドを入力して **matplotlib** をインストールし、実行する実験として Python スクリプトを送信します。

   ```azurecli
   REM you don't need to run this command if you have installed matplotlib locally from the previous steps
   pip install matplotlib
   
   REM kicks off an execution of the iris_sklearn.py file against the local compute context
   az ml experiment submit -c local .\iris_sklearn.py
   ```

4. 出力結果を確認します。 Workbench を使ってスクリプトを実行したときと同じ出力内容と結果であることがわかります。 

5. ご使用のマシンに Docker がインストールされている場合は、Docker 実行環境を使ってもう一度同じスクリプトを実行します。

   ```azurecli
   REM executes iris_sklearn.py in the local Docker container Python environment
   az ml experiment submit -c docker-python .\iris_sklearn.py 0.01
   
   REM executes iris_spark.py in the local Docker container Spark environment
   az ml experiment submit -c docker-spark .\iris_spark.py 0.1
   ```
6. Workbench で、左側のウィンドウにある**フォルダー** アイコンを選択してプロジェクト ファイルを一覧表示し、**run.py** という名前の Python スクリプトを開きます。 

   このスクリプトは、正則化項を変化させながら試すのに便利です。 さまざまな正則化項で実験を複数回実行してください。 このスクリプトは、`10.0` という極端に大きな正則化項で `iris_sklearn.py` ジョブを開始します。 その後、ループで実行を繰り返しながら正則化項を半分にしていき、`0.005` 以下になったところでループを抜けます。 

   ```python
   # run.py
   import os
   
   reg = 10
   while reg > 0.005:
       os.system('az ml experiment submit -c local ./iris_sklearn.py {}'.format(reg))
       reg = reg / 2
   ```

   コマンド ラインで **run.py** スクリプトを開くには、次のコマンドを実行します。

   ```cmd
   REM submits iris_sklearn.py multiple times with different regularization rates
   python run.py
   ```

   `run.py` が完了すると、Workbench の実行履歴リスト ビューにグラフが表示されます。

## <a name="execute-in-a-docker-container-on-a-remote-machine"></a>リモート マシンで Docker コンテナーを実行する
リモート Linux マシンの Docker コンテナーでスクリプトを実行するには、そのリモート マシンへの SSH アクセス (ユーザー名とパスワード) が必要です。 また、このリモート マシンには、Docker エンジンがインストールされ実行されている必要があります。 このような Linux マシンを入手する方法としては、Azure 上に Ubuntu ベースのデータ サイエンス仮想マシン (DSVM) を作成するのが最も簡単です。 [Azure ML Workbench で使用する Ubuntu DSVM を作成する方法](how-to-create-dsvm-hdi.md#create-an-ubuntu-dsvm-in-azure-portal)に関するページを参照してください。

>[!NOTE] 
>CentOS ベースの DSVM はサポート "*されません*"。

1. VM を作成した後、`.runconfig` と `.compute` という一対のファイルを生成すれば、その VM を実行環境としてアタッチすることができます。 次のコマンドを使用してファイルを生成します。 この新しい環境に `myvm` という名前を付けましょう。
 
   ```azurecli
   REM creates an myvm compute target
   az ml computetarget attach remotedocker --name myvm --address <IP address> --username <username> --password <password>
   ```
   
   >[!NOTE]
   >IP アドレスは、パブリックにアドレス指定可能な完全修飾ドメイン名 (FQDN) にすることもできます (例: `vm-name.southcentralus.cloudapp.azure.com`)。 DSVM に FQDN を追加し、ここで、それを IP アドレスの代わりに使うことをお勧めします。 お勧めする理由は、いずれコストを抑えるために VM を停止されることも考えられるからです。 さらに、次回 VM を起動したときには、IP アドレスが変わっている可能性があります。

   次に、次のコマンドを実行して VM に Docker イメージを作成し、スクリプトを実行できる状態にします。
   
   ```azurecli
   REM prepares the myvm compute target
   az ml experiment prepare -c myvm
   ```
   >[!NOTE]
   >`myvm.runconfig` の `PrepareEnvironment` の値は、既定値の `false` から `true` に変更することもできます。 これで初回実行時に Docker コンテナーが自動的に準備されます。

2. `aml_config` に生成された `myvm.runconfig` ファイルを編集し、フレームワークを既定値の `PySpark` から `Python` に変更します。

   ```yaml
   "Framework": "Python"
   ```
   >[!NOTE]
   >フレームワークの設定を PySpark のままにしても動作上の問題はありません。 しかし Python スクリプトを実行するうえで Spark セッションが必要ないなら、やや非効率と言えます。

3. 先ほど CLI ウィンドウで実行したものと同じコマンドを実行します。ただし、対象の環境は _myvm_ とします。
   ```azurecli
   REM executes iris_sklearn.py in a remote Docker container
   az ml experiment submit -c myvm iris_sklearn.py
   ```
   実行場所がリモート Linux VM であることを除けば、`docker-python` 環境を使った場合と同じようにコマンドが実行されます。 CLI ウィンドウには同じ出力情報が表示されます。

4. コンテナー内の Spark を試してみましょう。 エクスプローラーを開きます。 基本的なファイル操作コマンドに慣れている場合は CLI ウィンドウでこの作業を行うこともできます。 `myvm.runconfig` ファイルをコピーして `myvm-spark.runconfig` という名前を付けます。 新しいファイルを編集し、`Framework` の設定を `Python` から `PySpark` に変更します。
   ```yaml
   "Framework": "PySpark"
   ```
   `myvm.compute` ファイルには一切変更を加えないでください。 同じ VM 上の同じ Docker イメージが Spark の実行に使用されます。 新しい `myvm-spark.runconfig` の `target` フィールドから、同じ `myvm.compute` ファイルがその名前 (`myvm`) で参照されます。

5. 次のコマンドを入力し、リモート Docker コンテナーの Spark インスタンスで実行します。
   ```azureli
   REM executes iris_spark.py in a Spark instance on a remote Docker container
   az ml experiment submit -c myvm-spark .\iris_spark.py
   ```

## <a name="execute-script-in-an-hdinsight-cluster"></a>HDInsight クラスターでスクリプトを実行する
このスクリプトは、HDInsight Spark クラスターで実行することもできます。 [Azure ML Workbench で使用する HDInsight Spark クラスターを作成する方法](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal)に関するページを参照してください。

>[!NOTE] 
>HDInsight クラスターは、プライマリ ストレージとして Azure BLOB を使用する必要があります。 Azure Data Lake Storage はまだサポートされていません。

1. Spark for Azure HDInsight クラスターにアクセスできる場合は、次に示した HDInsight 実行構成コマンドを生成します。 パラメーターとして HDInsight クラスター名、HDInsight のユーザー名とパスワードを指定してください。 次のコマンドを使用します。

   ```azurecli
   REM creates a compute target that points to a HDInsight cluster
   az ml computetarget attach cluster --name myhdi --address <cluster head node FQDN> --username <username> --password <password>

   REM prepares the HDInsight cluster
   az ml experiment prepare -c myhdi
   ```

   通常、クラスターのヘッド ノードの FQDN は `<cluster_name>-ssh.azurehdinsight.net` になります。

   >[!NOTE]
   >`username` はクラスターの SSH ユーザー名です。 HDInsight のセットアップ時に変更しなかった場合、既定値は `sshuser` となります。 `admin` (クラスターの管理者 Web サイトへのアクセス用としてセットアップ時に作成される別のユーザー) ではありません。 

2. 次のコマンドを実行すると、スクリプトが HDInsight クラスターで実行されます。

   ```azurecli
   REM executes iris_spark on the HDInsight cluster
   az ml experiment submit -c myhdi .\iris_spark.py
   ```

   >[!NOTE]
   >リモート HDInsight クラスターで実行するときは、`admin` ユーザー アカウントを使って YARN (Yet Another Resource Negotiator) ジョブの実行情報を `https://<cluster_name>.azurehdinsight.net/yarnui` で確認することもできます。


## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順
3 部構成のチュートリアル シリーズのパート 2 では、Azure Machine Learning サービスを使って次の作業を行う方法について説明しました。
> [!div class="checklist"]
> * Azure Machine Learning Workbench を使用する。
> * スクリプトを開いてコードを確認する。
> * ローカル環境でスクリプトを実行する。
> * 実行履歴を確認する。
> * ローカル Docker 環境でスクリプトを実行する。
> * ローカル Azure CLI ウィンドウでスクリプトを実行する。
> * リモート Docker 環境でスクリプトを実行する。
> * クラウド HDInsight 環境でスクリプトを実行する。

シリーズのパート 3 に進む準備が整いました。 次は作成したロジスティック回帰モデルを、リアルタイムの Web サービスとしてデプロイしましょう。

> [!div class="nextstepaction"]
> [チュートリアル 3 - あやめの分類: モデルをデプロイする](tutorial-classifying-iris-part-3.md)
