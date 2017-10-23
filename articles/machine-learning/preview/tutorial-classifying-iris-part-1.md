---
title: "Machine Learning サービス (プレビュー) であやめの分類のチュートリアル用のデータを準備する | Microsoft Docs"
description: "Azure Machine Learning サービス (プレビュー) の使い方をエンド ツー エンドで詳しく紹介したチュートリアルです。 これは、データの準備のパート 1 です。"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/28/2017
ms.openlocfilehash: 975a86c1f9d9692f6eadd232177f33cbbbeeff2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="classifying-iris-part-1-prepare-data"></a>あやめの分類のパート 1: データを準備する
Azure Machine Learning サービス (プレビュー) は、データ サイエンスと高度な分析をエンド ツー エンドで支援する統合ソリューションです。データの準備、実験の開発、モデルのデプロイをクラウド スケールで行うプロフェッショナルなデータ サイエンティストを対象としています。

このチュートリアルは、3 部構成のシリーズのパート 1 になります。 このチュートリアルでは、Azure Machine Learning のサービス (プレビュー) の基礎を確認します。 このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Azure Machine Learning Workbench でプロジェクトを作成する
> * データ準備パッケージを作成する
> * データ準備パッケージを呼び出す Python/PySpark コードを生成する

このチュートリアルでは、単純化するために、経時的な変化を伴わない[あやめデータセット](https://en.wikipedia.org/wiki/Iris_flower_data_set)を使用します。 スクリーンショットは Windows のものですが、macOS を使用した場合も大きな違いはありません。

## <a name="prerequisites"></a>前提条件
- Azure Machine Learning 実験アカウントの作成
- Azure Machine Learning Workbench のインストール

[インストールと作成のクイックスタート](quickstart-installation.md)に従うと、Azure Machine Learning Workbench アプリケーションをインストールできます。 このインストールには、コマンド ライン インターフェイス (CLI) も含まれます。

## <a name="create-a-new-project-in-azure-ml-workbench"></a>Azure ML Workbench で新しいプロジェクトを作成する
1. Azure Machine Learning Workbench アプリを起動し、必要に応じてログインします。 **[PROJECTS (プロジェクト)]** ペインで **+** アイコンをクリックし、**新しいプロジェクト**を作成します。

   ![新しいワークスペース](media/tutorial-classifying-iris/new_ws.png)

2. **[新しいプロジェクトの作成]** に詳細を入力します。 

   ![[新しいプロジェクト]](media/tutorial-classifying-iris/new_project.png)

   - **[Project name (プロジェクト名)]** フィールドにプロジェクトの名前を入力します。 たとえば、「**myIris**」という値を使用します。
   - プロジェクトを作成する **[Project directory (プロジェクト ディレクトリ)]** を選択します。 たとえば、「**C:\Temp**」という値を使用します。 
   - **[プロジェクトの説明]** を入力します。 
   - **[Git repository (Git リポジトリ)]** フィールドは省略可能であり、空白にしておくことができます。 VSTS 上にある既存の空の Git リポジトリ (マスター分岐のないリポジトリ) を指定することもできます。 これにより、後でローミングと共有のシナリオを実行できるようになります。 詳細については、[Git リポジトリの使用](using-git-ml-project.md)に関する記事を参照してください。 
   - **[ワークスペース]** を選択します (たとえば、このチュートリアルでは **[IrisGarden]** を使用しています)。 
   - プロジェクト テンプレートの一覧から **[Classifying Iris (あやめの分類)]** テンプレートを選択します。 

3. **[作成]** ボタンをクリックしてプロジェクトを作成します。 プロジェクトが作成され、自動的に開きます。

## <a name="create-a-data-preparation-package"></a>データ準備パッケージを作成する
1. **ファイル ビュー**から **iris.csv** ファイルを開きます。 このファイルは、5 つの列と 150 の行を含む単純なテーブルです。 4 つの数値特徴列と 1 つの文字列ターゲット列があります。 列ヘッダーはありません。

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > プロジェクト フォルダーにはデータ ファイルを含めないことをお勧めします (特に、ファイル サイズが大きい場合)。 **iris.csv** はサイズが小さいので、デモのためにこのテンプレートに含まれています。 詳細については、[大きなデータ ファイルの読み取りと書き込みの方法](how-to-read-write-files.md)に関する記事を参照してください。

2. **データ ビュー**で **+** アイコンをクリックして、新しいデータ ソースを追加します。 **[データ ソースの追加]** ウィザードが起動します。 

   ![データ ビュー](media/tutorial-classifying-iris/data_view.png)

3. データ準備ウィザードを完了します。 
   - 最初の画面では、**[File(s)/Directory]\(ファイルまたはディレクトリ\)** を選択し、**[次へ]** をクリックします。
   - 2 番目の画面では、**iris.csv** ローカル ファイル (例: C:\Temp\myIris\iris.csv) を選択します。
   - 3 番目の **[ファイルの詳細]** 画面では、既定値をそのまま使用します。
   - 4 番目の **[データ型]** 画面では、_Column1_ から _Column4_ の**データ型**を _String_ から _Numeric_ に変更します。これらの列は数値であるためです。 
   - 5 番目と 6 番目の画面では、既定の設定をそのまま使用します。
   - **[完了]** をクリックします。

   ![iris を選択する](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >この演習用の現在のプロジェクト ディレクトリ内の **iris.csv** ファイルを必ず選択してください。そうしないと、以降の手順が失敗する可能性があります。 

4. **iris-1.dsource** という新しいファイルが作成されます。 サンプル プロジェクトに番号なしの **iris.dsource** ファイルが既に含まれているため、このファイルには "-1" が付いた一意の名前が付けられています。  

   ファイルが開き、データが表示されます。 一連の列ヘッダー (**Column1** から **Column5**) がこのデータセットに自動的に追加されます。 下部までスクロールし、データセットの最後の行が空であることに注意してください。 これは、csv ファイルに余分な改行があるためです。

   ![iris データ ビュー](media/tutorial-classifying-iris/iris_data_view.png)

5. **[メトリック]** ボタンをクリックします。 ヒストグラムと、各列について計算された統計情報の完全なセットを観察します。 **[データ]** ボタンをクリックしてデータを再度表示することもできます。 

   ![iris データ ビュー](media/tutorial-classifying-iris/iris_metrics_view.png)

6. **[準備]** をクリックします。 **[準備]** ダイアログが表示されます。 

   サンプル プロジェクトには **iris.dprep** ファイルが含まれているので、既定ではその既存の **iris.dprep** データ準備パッケージで新しいデータ フローを作成するよう求められます。 

   ドロップダウンの値を **[+New Data Preparation Package (+新しいデータ準備パッケージ)]** に変更し、新しい値「iris-1」を入力して **[OK]** をクリックします。

   ![iris データ ビュー](media/tutorial-classifying-iris/new_dprep.png)

   **iris-1.dprep** という名前の新しいデータ準備パッケージが作成され、データ準備エディターで開かれます。

7. 基本的なデータ準備操作を実行してみましょう。 列の名前を変更するため、それぞれの列ヘッダーをクリックし、ヘッダーのテキストを編集可能にします。 

   5 つの列にそれぞれ「**Sepal Length**」、「**Sepal Width**」、「**Petal Length**」、「**Petal Width**」、「**Species**」と入力します。

   ![列名の変更](media/tutorial-classifying-iris/rename_column.png)

8. 個々の値をカウントするには、**[Species]** 列を選択して右クリックします。 **[Value Counts (値のカウント)]** を選択します。 

   ![[Value Counts]\(値のカウント\) をクリックする](media/tutorial-classifying-iris/value_count.png)

   この操作により、**[Inspectors]\(インスペクター\)** ウィンドウが開き、4 つのバーから成るヒストグラムが表示されます。 ターゲット列には、**Iris_virginica**、**Iris_versicolor**、**Iris-setosa** の 3 種類の値と、**(null)** 値が含まれています。

9. グラフから null 値を表すバーを選択して、null 値をフィルターで除外します。 **(null)** 値が含まれる行が 1 つあります。 この行を削除するには、**[-]** フィルター ボタンをクリックします。

   ![値のカウントのヒストグラム](media/tutorial-classifying-iris/filter_out.png)

10. **[ステップ]** ウィンドウに個々のステップの詳細が表示されます。 列の名前を変更し、null 値の行をフィルター処理したので、各アクションがデータ準備ステップとして記録されています。 個々のステップを編集して設定を調整したり、ステップを並べ替えたりできます。また、ステップを削除することもできます。

   ![手順](media/tutorial-classifying-iris/steps.png)

11. グラフ アイコンが付いた **iris-1** という名前のタブの **[X]** をクリックして、データ準備エディターを閉じます。 作業内容は、**[Data Preparations]\(データ準備\)** という見出しの下に表示された **iris-1.dprep** ファイルに自動的に保存されます。

## <a name="generate-pythonpyspark-code-to-invoke-data-prep-package"></a>データ準備パッケージを呼び出す Python/PySpark コードを生成する

1. **iris-1.dprep** ファイルを右クリックしてコンテキスト メニューを表示し、**[Generate Data Access Code File (データ アクセス コード ファイルの生成)]** を選択します。 

   ![コードを生成する](media/tutorial-classifying-iris/generate_code.png)

2. 次のコード行が含まれた **iris-1.py** という名前の新しいファイルが開きます。

   ```python
   # This code snippet will load the referenced package and return a DataFrame.
   # If the code is run in a PySpark environment, the code will return a
   # Spark DataFrame. If not, the code will return a Pandas DataFrame.

   from azureml.dataprep.package import run
   df = run('iris.dprep', dataflow_idx=0)
   ```

   このコード スニペットにより、データ準備パッケージとして作成したロジックが呼び出されます。 このコードが実行されるコンテキストに応じて、`df` はさまざまな種類のデータ フレームを表します。 Python ランタイムで実行されるときには [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) が使用され、Spark コンテキストで実行されるときには [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html) が使用されます。 

   Azure Machine Learning Workbench でデータを準備する方法の詳細については、「[Getting Started with Data Preparation (Data Preparation の概要)](data-prep-getting-started.md)」ガイドを参照してください。

## <a name="next-steps"></a>次のステップ
3 部構成のチュートリアル シリーズのこのパート 1 では、Azure Machine Learning Workbench を使って次のことを行いました。
> [!div class="checklist"]
> * 新しいプロジェクトを作成する 
> * データ準備パッケージを作成する
> * データ準備パッケージを呼び出す Python/PySpark コードを生成する

シリーズの次のパートに移り、機械学習モデルをビルドすることができます。
> [!div class="nextstepaction"]
> [モデルのビルド](tutorial-classifying-iris-part-2.md)
