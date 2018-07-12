---
title: Azure Machine Learning サービス (プレビュー) であやめの分類のチュートリアル用のデータを準備する | Microsoft Docs
description: このチュートリアルでは、Azure Machine Learning サービス (プレビュー) の使い方をエンド ツー エンドで詳しく説明します。 これはパート 1 であり、データの準備について説明します。
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs, gcampanella
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/7/2018
ms.openlocfilehash: 56f1d26d5d687982366b9a8fb20235ff338a9573
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38722985"
---
# <a name="tutorial-1-classify-iris---preparing-the-data"></a>チュートリアル 1: あやめの分類 - データを準備する

Azure Machine Learning サービス (プレビュー) は、データ サイエンスと高度な分析をエンドツーエンドで支援する統合ソリューションです。データの準備、実験の開発、モデルのデプロイをクラウド スケールで行うプロフェッショナルなデータ サイエンティストを対象としています。

このチュートリアルは、**3 部構成のシリーズのパート 1** になります。 このチュートリアルでは、Azure Machine Learning サービス (プレビュー) の基礎を確認し、以下の方法について説明します。

> [!div class="checklist"]
> * Azure Machine Learning Workbench でプロジェクトを作成する
> * データ準備パッケージを作成する
> * データ準備パッケージを呼び出す Python/PySpark コードを生成する

このチュートリアルでは、経時的な変化を伴わない[あやめデータ セット](https://en.wikipedia.org/wiki/Iris_flower_data_set)を使用します。 

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

このチュートリアルを完了するには、以下が必要です。
- Azure Machine Learning 実験アカウント
- Azure Machine Learning Workbench のインストール

これらの前提条件をまだ満たしていない場合は、[インストールと基本操作のクイックスタート](../service/quickstart-installation.md)の手順に従ってアカウントを設定し、Azure Machine Learning Workbench アプリケーションをインストールしてください。 

## <a name="create-a-new-project-in-workbench"></a>Workbench で新しいプロジェクトを作成する

[インストールと基本操作のクイック スタート](../service/quickstart-installation.md)の記事の手順を終え、このプロジェクトが既に存在する場合は、次のセクションにスキップしてかまいません。

1. Azure Machine Learning Workbench アプリを開き、必要に応じてログインします。 
   
   + Windows の場合は、**Machine Learning Workbench** のデスクトップ ショートカットを使用して起動します。 
   + macOS の場合は、Launchpad で **[Azure ML Workbench]** を選択します。

1. **[プロジェクト]** ウィンドウの正符号 (+) を選択し、**[新しいプロジェクト]** を選択します。  

   ![新しいワークスペース](./media/tutorial-classifying-iris/new_ws.png)

1. フォームの各フィールドに必要事項を入力し、**[作成]** ボタンを選択すると、Workbench で新しいプロジェクトが作成されます。

   フィールド|チュートリアルの推奨値|説明
   ---|---|---
   プロジェクト名 | myIris |アカウントを識別する一意の名前を入力します。 独自の名前を使用できるほか、実験を識別するうえで最適な部門またはプロジェクトの名前を使用できます。 この名前は 2 ～ 32 文字で指定します。 これには、英数字とダッシュ (-) 文字のみを使用してください。 
   プロジェクト ディレクトリ | c:\Temp\ | プロジェクトの作成先となるディレクトリを指定します。
   プロジェクトの説明 | "_空白のまま_" | 省略可能なフィールド。プロジェクトの説明を入力することができます。
   Visualstudio.com の GIT リポジトリの URL |"_空白のまま_" | 省略可能なフィールド。 プロジェクトは、ソース管理とコラボレーションのために、Visual Studio Team Services で Git リポジトリに関連付けることができます。 [セットアップ方法](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/using-git-ml-project#step-3-set-up-a-machine-learning-project-and-git-repo)に関するページを参照してください。 
   選択したワークスペース | IrisGarden (存在する場合) | Azure Portal で実験アカウント用に作成したワークスペースを選択します。 <br/>このクイック スタートの手順に従っている場合、ワークスペースの名前は IrisGarden です。 それ以外の場合は、実験アカウントの作成時に作ったワークスペースを選択してください。それ以外に使いたいワークスペースがあれば、そちらを選んでもかまいません。
   プロジェクト テンプレート | あやめの分類 | テンプレートには、製品に習熟するうえで役立つスクリプトとデータが含まれています。 このクイック スタートに必要なスクリプトとデータ、そしてこのドキュメント サイトに存在する他のチュートリアルで使用するスクリプトとデータも、このテンプレートに含まれています。 

   ![新しいプロジェクト](media/tutorial-classifying-iris/new_project.png)
 
 新しいプロジェクトが作成されて、プロジェクト ダッシュボードにそのプロジェクトが表示されます。 この時点で、プロジェクトのホーム ページ、データ ソース、ノートブック、ソース コード ファイルを確認できます。 

   ![プロジェクトを開く](media/tutorial-classifying-iris/project-open.png)
 

## <a name="create-a-data-preparation-package"></a>データ準備パッケージを作成する

次に、Azure Machine Learning Workbench でデータを確認して準備を開始できます。 Workbench で実行する変換はそれぞれ、JSON 形式でローカルのデータ準備パッケージ (*.dprep ファイル) に格納されます。 このデータ準備パッケージが、Workbench におけるデータ準備作業の主要なコンテナーとなります。

このデータ準備パッケージは、後でランタイム (ローカル C#/CoreCLR、Scala/Spark、Scala/HDI など) に渡すことができます。 

1. フォルダー アイコンを選択してファイル ビューを開き、**iris.csv** を選択して、そのファイルを開きます。

   このファイルには、5 つの列と 50 個の行を含んだテーブルがあります。 4 つの列は数値特徴列です。 5 つ目の列は文字列ターゲット列です。 どの列にもヘッダー名はありません。

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > プロジェクト フォルダーにはデータ ファイルを含めないでください (特に、ファイル サイズが大きい場合)。 **iris.csv** データ ファイルは小さいため、デモのためにこのテンプレートに含まれていました。 詳細については、[大きなデータ ファイルの読み取りと書き込みの方法](how-to-read-write-files.md)に関するページを参照してください。

2. **データ ビュー**で、新しいデータ ソースを追加するために、プラス記号 (**+**) を選択します。 **[データ ソースの追加]** ページが開きます。 

   ![Azure Machine Learning Workbench のデータ ビュー](media/tutorial-classifying-iris/data_view.png)

3. **[テキスト ファイル (\*.csv、\*.json、\*.txt、...)]** を選択し、**[次へ]** をクリックします。
   ![Azure Machine Learning Workbench のデータ ソース](media/tutorial-classifying-iris/data-source.png)

4. **iris.csv** ファイルを参照して、**[完了]** をクリックします。 区切り記号やデータ型などのパラメーターには、既定値が使用されます。

   >[!IMPORTANT]
   >この演習用の現在のプロジェクト ディレクトリ内の **iris.csv** ファイルを必ず選択してください。 そうしないと、以降の手順が失敗する可能性があります。
 
   ![iris を選択する](media/tutorial-classifying-iris/select_iris_csv.png)
   
5. **iris-1.dsource** という名前の新しいファイルが作成されます。 サンプル プロジェクトに番号なしの **iris.dsource** ファイルが既に含まれているため、このファイルには "-1" が付いた一意の名前が付けられています。  

   ファイルが開き、データが表示されます。 一連の列ヘッダー (**Column1** から **Column5**) がこのデータ セットに自動的に追加されます。 下部までスクロールし、データ セットの最後の行が空であることに注意してください。 この行が空になっているのは、CSV ファイルに余分な改行があるためです。

   ![iris データ ビュー](media/tutorial-classifying-iris/iris_data_view.png)

1. **[メトリック]** ボタンを選択します。 ヒストグラムが生成され、表示されます。

   **[データ]** ボタンを選択して、データ ビューに戻ることができます。
   
   ![iris データ ビュー](media/tutorial-classifying-iris/iris_data_view_metrics.png)

1. ヒストグラムを観察します。 各列に対して統計情報の完全なセットが計算されています。 

   ![iris データ ビュー](media/tutorial-classifying-iris/iris_metrics_view.png)

8. **[準備]** ボタンを選択して、データ準備パッケージの作成を開始します。 **[準備]** ダイアログ ボックスが開きます。 

   サンプル プロジェクトには、既定で選択される **iris.dprep** データ準備ファイルが含まれています。 

   ![iris データ ビュー](media/tutorial-classifying-iris/prepare.png)

1. ドロップダウン メニューから **[+ New Data Preparation Package]\(+ 新しいデータ準備パッケージ\)** を選択して、新しいデータ準備パッケージを作成します。

   ![iris データ ビュー](media/tutorial-classifying-iris/prepare_new.png)

1. パッケージの名前に新しい値 (**iris-1** を使用) を入力して、**[OK]** を選択します。

   **iris-1.dprep** という名前の新しいデータ準備パッケージが作成され、データ準備エディターで開かれます。

   ![iris データ ビュー](media/tutorial-classifying-iris/prepare_iris-1.png)

   基本的なデータ準備操作を実行してみましょう。 

1. それぞれの列ヘッダーを選択して、ヘッダーのテキストを編集可能にします。 次に、各列の名前を以下のように変更します。 

   5 つの列にそれぞれ「**Sepal Length**」、「**Sepal Width**」、「**Petal Length**」、「**Petal Width**」、「**Species**」と順に入力します。

   ![列名を変更する](media/tutorial-classifying-iris/rename_column.png)

1. 個別の値をカウントします。
   1. **Species** 列を選択します
   1. 右クリックしてそれを選択します。 
   1. ドロップダウン メニューから **[Value Counts]\(値のカウント\)** を選択します。 

   データの下に **[Inspectors]\(インスペクター\)** ウィンドウが表示されます。 4 つのバーのあるヒストグラムが表示されます。 ターゲット列には、**Iris-virginica**、**Iris-versicolor**、**Iris-setosa**、および **(null)** という 4 種類の値が含まれています。

   ![[Value Counts]\(値のカウント\) を選択する](media/tutorial-classifying-iris/value_count.png)

   ![値のカウントのヒストグラム](media/tutorial-classifying-iris/filter_out.png)

1. null 値を除外するために、"(null)" 棒グラフを選択し、マイナス記号 (**-**) を選択します。 

   (null) の行が灰色に変わり、除外されていることがわかります。 

   ![null 値をフィルター処理する](media/tutorial-classifying-iris/filter_out2.png)

1. **[ステップ]** ウィンドウに列挙されている個々のデータ準備ステップに注目します。 列の名前を変更し、null 値の行をフィルター処理したので、各アクションがデータ準備ステップとして記録されています。 個々のステップを編集して設定を調整できるほか、ステップを並べ替えたり、ステップを削除したりすることもできます。

   ![手順](media/tutorial-classifying-iris/steps.png)

1. データ準備エディターを閉じます。 グラフ アイコンが付いた **[iris-1]** タブの **X** アイコンを選択して、このタブを閉じます。作業内容は、**[Data Preparations]\(データ準備\)** という見出しの下に表示された **iris-1.dprep** ファイルに自動的に保存されます。

   ![閉じます](media/tutorial-classifying-iris/close.png)

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>データ準備パッケージを呼び出す Python/PySpark コードを生成する

 データ準備パッケージの出力は、Python または Jupyter Notebook で直接調査できます。 パッケージは、ローカル Python、Spark (Docker 内を含む)、HDInsight などの複数のランタイムにまたがって実行できます。 

1. [Data Preparations]\(データ準備\) タブで **iris-1.dprep** ファイルを特定します。

1. **[iris-1.dprep]** ファイルを右クリックし、コンテキスト メニューから **[Generate Data Access Code File]\(データ アクセス コード ファイルの生成\)** を選択します。 

   ![コードを生成する](media/tutorial-classifying-iris/generate_code.png)

   **iris-1.py** という名前の新しいファイルが開きます。このファイルには、次のコード行が記述されており、データ準備パッケージとして作成したロジックを呼び出す際に使用されます。

   ```python
   # Use the Azure Machine Learning data preparation package
   from azureml.dataprep import package

   # Use the Azure Machine Learning data collector to log various metrics
   from azureml.logging import get_azureml_logger
   logger = get_azureml_logger()

   # This call will load the referenced package and return a DataFrame.
   # If run in a PySpark environment, this call returns a
   # Spark DataFrame. If not, it will return a Pandas DataFrame.
   df = package.run('iris-1.dprep', dataflow_idx=0)

   # Remove this line and add code that uses the DataFrame
   df.head(10)
   ```

   このコードが実行されるコンテキストに応じて、`df` は異なるデータフレームの種類を表します。
   + Python ランタイムで実行すると、[pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) が使用されます。
   + Spark コンテキストで実行すると、[Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html) が使用されます。 
   
   Azure Machine Learning Workbench でデータを準備する方法について確認するには、[データ準備の概要](data-prep-getting-started.md)に関するガイドを参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Machine Learning Workbench を使用して、次の作業を行いました。
> [!div class="checklist"]
> * 新しいプロジェクトを作成する
> * データ準備パッケージを作成する
> * データ準備パッケージを呼び出す Python/PySpark コードを生成する

チュートリアル シリーズの次のパートに移り、Azure Machine Learning モデルをビルドする方法を学習しましょう。
> [!div class="nextstepaction"]
> [チュートリアル 2 - モデルを構築する](tutorial-classifying-iris-part-2.md)
