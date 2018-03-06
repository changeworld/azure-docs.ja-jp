---
title: "Azure Machine Learning サービス (プレビュー) であやめの分類のチュートリアル用のデータを準備する | Microsoft Docs"
description: "このチュートリアルでは、Azure Machine Learning サービス (プレビュー) の使い方をエンド ツー エンドで詳しく説明します。 これはパート 1 であり、データの準備について説明します。"
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
ms.openlocfilehash: 0bef557ee1394e3c786fd2c54e821b5dea28fabf
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="tutorial-classify-iris-part-1---preparing-the-data"></a>チュートリアル: あやめの分類 (パート 1) - データを準備する

Azure Machine Learning サービス (プレビュー) は、データ サイエンスと高度な分析をエンド ツー エンドで支援する統合ソリューションです。データの準備、実験の開発、モデルのデプロイをクラウド スケールで行うプロフェッショナルなデータ サイエンティストを対象としています。

このチュートリアルは、3 部構成のシリーズのパート 1 になります。 このチュートリアルでは、Azure Machine Learning サービス (プレビュー) の基礎を確認します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Azure Machine Learning Workbench でプロジェクトを作成する
> * データ準備パッケージを作成する
> * データ準備パッケージを呼び出す Python/PySpark コードを生成する

このチュートリアルでは、経時的な変化を伴わない[あやめデータ セット](https://en.wikipedia.org/wiki/Iris_flower_data_set)を使用します。 スクリーンショットは Windows のものですが、macOS を使用した場合も大きな違いはありません。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

このチュートリアルを完了するには、以下が必要です。
- Azure Machine Learning 実験アカウント
- Azure Machine Learning Workbench のインストール

これらの前提条件をまだ満たしていない場合は、[インストールと基本操作のクイック スタート](quickstart-installation.md)に関する記事の手順に従って、このアカウントを設定し、Azure Machine Learning Workbench アプリケーションをインストールしてください。 

## <a name="create-a-new-project-in-workbench"></a>Workbench で新しいプロジェクトを作成する

[インストールと基本操作のクイック スタート](quickstart-installation.md)の記事の手順を終え、このプロジェクトが既に存在する場合は、次のセクションにスキップしてかまいません。

1. Azure Machine Learning Workbench アプリを開き、必要に応じてログインします。 
   
   + Windows の場合は、**Machine Learning Workbench** のデスクトップ ショートカットを使用して起動します。 
   + macOS の場合は、Launchpad で **[Azure ML Workbench]** を選択します。

1. **[プロジェクト]** ウィンドウの正符号 (+) を選択し、**[新しいプロジェクト]** を選択します。  

   ![新しいワークスペース](media/tutorial-classifying-iris/new_ws.png)

1. フォームの各フィールドに必要事項を入力し、**[作成]** ボタンを選択すると、Workbench で新しいプロジェクトが作成されます。

   フィールド|チュートリアルの推奨値|[説明]
   ---|---|---
   プロジェクト名 | myIris |アカウントを識別する一意の名前を入力します。 独自の名前を使用できるほか、実験を識別するうえで最適な部門またはプロジェクトの名前を使用できます。 この名前は 2 ～ 32 文字で指定します。 これには、英数字とダッシュ (-) 文字のみを使用してください。 
   プロジェクト ディレクトリ | c:\Temp\ | プロジェクトの作成先となるディレクトリを指定します。
   プロジェクトの説明 | "_空白のまま_" | 省略可能なフィールド。プロジェクトの説明を入力することができます。
   Visualstudio.com |"_空白のまま_" | 省略可能なフィールド。 プロジェクトは、ソース管理とコラボレーションのために、必要に応じて Visual Studio Team Services で Git リポジトリに関連付けることができます。 [セットアップ方法](https://docs.microsoft.com/en-us/azure/machine-learning/preview/using-git-ml-project#step-3-set-up-a-machine-learning-project-and-git-repo)に関するページを参照してください。 
   ワークスペース | IrisGarden (存在する場合) | Azure Portal で実験アカウント用に作成したワークスペースを選択します。 <br/>このクイック スタートの手順に従っている場合、ワークスペースの名前は IrisGarden です。 それ以外の場合は、実験アカウントの作成時に作ったワークスペースを選択してください。それ以外に使いたいワークスペースがあれば、そちらを選んでもかまいません。
   プロジェクト テンプレート | あやめの分類 | テンプレートには、製品に習熟するうえで役立つスクリプトとデータが含まれています。 このクイック スタートに必要なスクリプトとデータ、そしてこのドキュメント サイトに存在する他のチュートリアルで使用するスクリプトとデータも、このテンプレートに含まれています。 

   ![新しいプロジェクト](media/tutorial-classifying-iris/new_project.png)
 
 新しいプロジェクトが作成されて、プロジェクト ダッシュボードにそのプロジェクトが表示されます。 この時点で、プロジェクトのホーム ページ、データ ソース、ノートブック、ソース コード ファイルを確認できます。 

## <a name="create-a-data-preparation-package"></a>データ準備パッケージを作成する

チュートリアルのこのパートでは、データを調べて、データの準備プロセスを開始します。 Azure Machine Learning Workbench でデータを準備すると、Workbench で実行した変換が JSON 形式でローカルのデータ準備パッケージ (*.dprep ファイル) に保存されます。 このデータ準備パッケージが、Workbench におけるデータ準備作業の主要なコンテナーとなります。

このデータ準備パッケージをランタイム (ローカル C#/CoreCLR、Scala/Spark、Scala/HDI など) に渡して実行することができます。 そこで適切なランタイム用のコードが生成されて実行されます。 

1. フォルダー アイコンを選択してファイル ビューを開き、**iris.csv** を選択して、そのファイルを開きます。  

   このファイルは、5 つの列と 150 個の行を含むテーブルです。 4 つの数値特徴列と 1 つの文字列ターゲット列があります。 列ヘッダーはありません。

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > プロジェクト フォルダーにはデータ ファイルを含めないでください (特に、ファイル サイズが大きい場合)。 **iris.csv** はサイズが小さいので、デモのためにこのテンプレートに含まれています。 詳細については、[大きなデータ ファイルの読み取りと書き込みの方法](how-to-read-write-files.md)に関するページを参照してください。

2. **データ ビュー**で、新しいデータ ソースを追加するために、プラス記号 (**+**) を選択します。 **[データ ソースの追加]** ページが開きます。 

   ![データ ビュー](media/tutorial-classifying-iris/data_view.png)

3. **[テキスト ファイル (*.csv、.json、.txt, ...)]** を選択し、**[次へ]** をクリックします。
   ![データ ソース](media/tutorial-classifying-iris/data-source.png)
   

4. **iris.csv** ファイルを参照して、**[次へ]** をクリックします。  
 
   ![iris を選択する](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >この演習用の現在のプロジェクト ディレクトリ内の **iris.csv** ファイルを必ず選択してください。 そうしないと、以降の手順が失敗する可能性があります。
   
5. このオプションを既定値のままにし、**[完了]** をクリックします。

6. **iris-1.dsource** という名前の新しいファイルが作成されます。 サンプル プロジェクトに番号なしの **iris.dsource** ファイルが既に含まれているため、このファイルには "-1" が付いた一意の名前が付けられています。  

   ファイルが開き、データが表示されます。 一連の列ヘッダー (**Column1** から **Column5**) がこのデータ セットに自動的に追加されます。 下部までスクロールし、データ セットの最後の行が空であることに注意してください。 この行が空になっているのは、CSV ファイルに余分な改行があるためです。

   ![iris データ ビュー](media/tutorial-classifying-iris/iris_data_view.png)

7. **[メトリック]** ボタンを選択します。 ヒストグラムを観察します。 各列に対して統計情報の完全なセットが計算されています。 **[データ]** ボタンを選択してデータを再度表示することもできます。 

   ![iris データ ビュー](media/tutorial-classifying-iris/iris_metrics_view.png)

8. **[準備]** ボタンを選択します。 **[準備]** ダイアログ ボックスが開きます。 

   サンプル プロジェクトには、**iris.dprep** ファイルが付属しています。 既定では、既存の **iris.dprep** データ準備パッケージに新しいデータ フローを作成するように求められます。 

   ドロップダウン メニューから **[+ 新しいデータ準備パッケージ]** を選択し、パッケージ名の新しい値を入力します。ここでは、**iris-1** を使用し、**[OK]** を選択します。

   ![iris データ ビュー](media/tutorial-classifying-iris/new_dprep.png)

   **iris-1.dprep** という名前の新しいデータ準備パッケージが作成され、データ準備エディターで開かれます。

9. 基本的なデータ準備操作を実行してみましょう。 それぞれの列ヘッダーを選択して、ヘッダーのテキストを編集可能にします。各列の名前を次のように変更してください。 

   5 つの列にそれぞれ「**Sepal Length**」、「**Sepal Width**」、「**Petal Length**」、「**Petal Width**」、「**Species**」と順に入力します。

   ![列名を変更する](media/tutorial-classifying-iris/rename_column.png)

10. 個々の値をカウントするには、**[Species]** 列を選択し、右クリックして選択します。 ドロップダウン メニューから **[Value Counts]\(値のカウント\)** を選択します。 

   この操作によって、データの下に **[Inspectors]\(インスペクター\)** ウィンドウが表示されます。 4 つのバーのあるヒストグラムが表示されます。 ターゲット列には、**Iris_virginica**、**Iris_versicolor**、**Iris-setosa** の 3 種類の値と、**(null)** 値が含まれています。

   ![[Value Counts]\(値のカウント\) を選択する](media/tutorial-classifying-iris/value_count.png)

11. Null 値を除外するために、"Null" ラベルを選択し、負符号 (**-**) を選択します。 Null の行が灰色に変わり、除外されていることがわかります。 

   ![値のカウントのヒストグラム](media/tutorial-classifying-iris/filter_out.png)

12. **[ステップ]** ウィンドウに個々のステップの詳細が表示されます。 列の名前を変更し、null 値の行をフィルター処理したので、各アクションがデータ準備ステップとして記録されています。 個々のステップを編集して設定を調整できるほか、ステップを並べ替えたり、ステップを削除したりすることもできます。

   ![手順](media/tutorial-classifying-iris/steps.png)

13. データ準備エディターを閉じます。 グラフ アイコンが付いた **[iris-1]** タブの **[閉じる]** (x) をクリックします。 作業内容は、**[Data Preparations]\(データ準備\)** という見出しの下に表示された **iris-1.dprep** ファイルに自動的に保存されます。

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>データ準備パッケージを呼び出す Python/PySpark コードを生成する

<!-- The output/results of a Package can be explored in Python or via a Jupyter Notebook. A Package can be executed across multiple runtimes including local Python, Spark (including in Docker), and HDInsight. A Package contains one or more Dataflows that are the steps and transforms applied to the data. A Package may use another Package as a Data Source (referred to as a Reference Data Flow). -->

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

   このコードが実行されるコンテキストに応じて、`df` はさまざまな種類のデータフレームを表します。 Python ランタイムで実行されるときには [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) が使用され、Spark コンテキストで実行されるときには [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html) が使用されます。 
   
   Azure Machine Learning Workbench でデータを準備する方法については、[データ準備の概要](data-prep-getting-started.md)に関するガイドを参照してください。

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
