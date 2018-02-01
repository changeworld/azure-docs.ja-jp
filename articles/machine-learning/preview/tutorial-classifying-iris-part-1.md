---
title: "Azure Machine Learning サービス (プレビュー) であやめの分類のチュートリアル用のデータを準備する | Microsoft Docs"
description: "このチュートリアルでは、Azure Machine Learning サービス (プレビュー) の使い方をエンド ツー エンドで詳しく説明します。 これはパート 1 であり、データの準備について説明します。"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 09/28/2017
ms.openlocfilehash: 4e558518a5a1fb7b4cd0a58fe2453fd4c083b46a
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2018
---
# <a name="classify-iris-part-1-prepare-the-data"></a>あやめの分類 (パート 1): データを準備する
Azure Machine Learning サービス (プレビュー) は、データ サイエンスと高度な分析をエンド ツー エンドで支援する統合ソリューションです。データの準備、実験の開発、モデルのデプロイをクラウド スケールで行うプロフェッショナルなデータ サイエンティストを対象としています。

このチュートリアルは、3 部構成のシリーズのパート 1 になります。 このチュートリアルでは、Azure Machine Learning サービス (プレビュー) の基礎を確認します。 学習内容は次のとおりです。
> [!div class="checklist"]
> * Azure Machine Learning Workbench でプロジェクトを作成する。
> * データ準備パッケージを作成する。
> * データ準備パッケージを呼び出す Python/PySpark コードを生成する。

このチュートリアルでは、経時的な変化を伴わない[あやめデータ セット](https://en.wikipedia.org/wiki/Iris_flower_data_set)を使用します。 スクリーンショットは Windows のものですが、Mac OS を使用した場合も大きな違いはありません。

## <a name="prerequisites"></a>前提条件
- Azure Machine Learning 実験アカウントを作成する。
- Azure Machine Learning Workbench をインストールする。

[インストールと作成のクイックスタート](quickstart-installation.md)に関する記事の手順に従うと、Azure Machine Learning Workbench アプリケーションをインストールできます。 このインストールには、Azure のクロスプラットフォーム コマンドライン ツール (Azure CLI) も含まれます。

## <a name="create-a-new-project-in-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench で新しいプロジェクトを作成する
1. Azure Machine Learning Workbench アプリを開き、必要に応じてログインします。 **[プロジェクト]** ウィンドウでプラス記号 (**+**) を選択し、**新しいプロジェクト**を作成します。

   ![新しいワークスペース](media/tutorial-classifying-iris/new_ws.png)

2. **[新しいプロジェクトの作成]** に詳細を入力します。 

   ![新しいプロジェクト](media/tutorial-classifying-iris/new_project.png)

   - **[プロジェクト名]** ボックスにプロジェクトの名前を入力します。 たとえば、「**myIris**」という値を使用します。
   - プロジェクトを作成する**プロジェクト ディレクトリ**を選択します。 たとえば、`C:\Temp\` という値を使用します。 
   - **プロジェクトの説明**を入力します (省略可能)。 
   - **[Git リポジトリ]** フィールドも省略可能であり、空白にしておくことができます。 Visual Studio Team Services 上にある既存の空の Git リポジトリ (マスター分岐のないリポジトリ) を指定することもできます。 既に存在している Git リポジトリを使用する場合は、ローミングと共有のシナリオを後で実現できます。 詳細については、[Git リポジトリの使用](using-git-ml-project.md)に関するページを参照してください。 
   - **ワークスペース**を選択します (たとえば、このチュートリアルでは **IrisGarden** を使用しています)。 
   - プロジェクト テンプレートの一覧から **[Classifying Iris (あやめの分類)]** テンプレートを選択します。 

3. **[作成]** ボタンを選択します。 プロジェクトが作成され、自動的に開きます。

## <a name="create-a-data-preparation-package"></a>データ準備パッケージを作成する
1. **ファイル ビュー**から **iris.csv** ファイルを開きます。 このファイルは、5 つの列と 150 個の行を含むテーブルです。 4 つの数値特徴列と 1 つの文字列ターゲット列があります。 列ヘッダーはありません。

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

9. 基本的なデータ準備操作を実行してみましょう。 列名を変更します。 それぞれの列ヘッダーを選択して、ヘッダーのテキストを編集可能にします。 

   5 つの列にそれぞれ「**Sepal Length**」、「**Sepal Width**」、「**Petal Length**」、「**Petal Width**」、「**Species**」と入力します。

   ![列名を変更する](media/tutorial-classifying-iris/rename_column.png)

10. 個々の値をカウントするには、**[Species]** 列を選択し、右クリックして選択します。 ドロップダウン メニューから **[Value Counts]\(値のカウント\)** を選択します。 

   ![[Value Counts]\(値のカウント\) を選択する](media/tutorial-classifying-iris/value_count.png)

   この操作により、**[Inspectors]\(インスペクター\)** ウィンドウが開き、4 つのバーから成るヒストグラムが表示されます。 ターゲット列には、**Iris_virginica**、**Iris_versicolor**、**Iris-setosa** の 3 種類の値と、**(null)** 値が含まれています。

11. null 値をフィルターで除外するために、グラフから null 値を表すバーを選択します。 **(null)** 値が含まれる行が 1 つあります。 この行を削除するために、マイナス記号 (**-**) を選択します。

   ![値のカウントのヒストグラム](media/tutorial-classifying-iris/filter_out.png)

12. **[ステップ]** ウィンドウに個々のステップの詳細が表示されます。 列の名前を変更し、null 値の行をフィルター処理したので、各アクションがデータ準備ステップとして記録されています。 個々のステップを編集して設定を調整できるほか、ステップを並べ替えたり、ステップを削除したりすることもできます。

   ![手順](media/tutorial-classifying-iris/steps.png)

13. データ準備エディターを閉じます。 グラフ アイコンが付いた **[iris-1]** タブの **[閉じる]** (x) をクリックします。 作業内容は、**[Data Preparations]\(データ準備\)** という見出しの下に表示された **iris-1.dprep** ファイルに自動的に保存されます。

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>データ準備パッケージを呼び出す Python/PySpark コードを生成する

1. **iris-1.dprep** ファイルを右クリックしてコンテキスト メニューを表示し、**[Generate Data Access Code File]\(データ アクセス コード ファイルの生成\)** を選択します。 

   ![コードを生成する](media/tutorial-classifying-iris/generate_code.png)

2. 次のコード行が含まれた **iris-1.py** という名前の新しいファイルが開きます。

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

   このコード スニペットにより、データ準備パッケージとして作成したロジックが呼び出されます。 このコードが実行されるコンテキストに応じて、`df` はさまざまな種類のデータフレームを表します。 Python ランタイムで実行されるときには [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) が使用され、Spark コンテキストで実行されるときには [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html) が使用されます。 

   Azure Machine Learning Workbench でデータを準備する方法の詳細については、[データ準備の概要](data-prep-getting-started.md)に関するガイドを参照してください。

## <a name="next-steps"></a>次の手順
3 部構成のチュートリアル シリーズのこのパート 1 では、Azure Machine Learning Workbench を使って次のことを行いました。
> [!div class="checklist"]
> * 新しいプロジェクトを作成する。 
> * データ準備パッケージを作成する。
> * データ準備パッケージを呼び出す Python/PySpark コードを生成する。

シリーズの次のパートに移り、Azure Machine Learning モデルをビルドする方法を学習しましょう。
> [!div class="nextstepaction"]
> [モデルのビルド](tutorial-classifying-iris-part-2.md)
