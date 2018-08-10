---
title: 自転車シェアのチュートリアル - Azure Machine Learning Workbench による詳細データの準備
description: このチュートリアルでは、Azure Machine Learning Workbench を使用したデータの準備作業をエンド ツー エンドで行います。
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/21/2017
ms.openlocfilehash: 2a50350b9ba49d82a20b92804ffb92ec6906186d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439902"
---
# <a name="tutorial-use-azure-machine-learning-workbench-for-advanced-data-preparation-bike-share-data"></a>チュートリアル: Azure Machine Learning Workbench を使用した高度なデータ準備 (自転車シェア データ)
Azure Machine Learning (プレビュー) は、データ サイエンスと高度な分析をエンド ツー エンドで支援する統合ソリューションです。データの準備、実験の開発、モデルのデプロイをクラウド スケールで行うプロフェッショナルなデータ サイエンティストを対象としています。

ここでは、Machine Learning (プレビュー) を使って次の作業を行う方法について説明します。
> [!div class="checklist"]
> * Machine Learning データ準備ツールを使用して対話形式でデータを準備する。
> * テスト データセットをインポート、変換、作成する。
> * データ準備パッケージを生成する。
> * Python を使用してデータ準備パッケージを実行する。
> * 追加の入力ファイルのデータ準備パッケージを再利用して、トレーニング データセットを生成する。
> * ローカル Azure CLI ウィンドウでスクリプトを実行する。
> * クラウド Azure HDInsight 環境でスクリプトを実行する。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning Workbench のローカル インストール。 詳細については、[インストールのクイック スタート](../service/quickstart-installation.md)に関する記事をご覧ください。
* Azure CLI をインストールしていない場合は、指示に従って、[Azure CLI の最新バージョンをインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)してください。
* Azure に作成された [HDInsights Spark クラスター](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal)。
* Azure ストレージ アカウント。
* Workbench で新しいプロジェクトを作成する方法についての知識。
* 必須ではありませんが、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) をインストールすると、ストレージ アカウントで BLOB をアップロード、ダウンロード、表示できるようになり、便利です。

## <a name="data-acquisition"></a>データの取得
このチュートリアルでは、[ボストンの Hubway 社のデータセット](https://s3.amazonaws.com/hubway-data/index.html)と、[米国立海洋大気庁 (NOAA)](http://www.noaa.gov/) によるボストンの気象データを使用します。

1. 以下のリンクから、ローカルの開発環境にデータ ファイルをダウンロードします。

   * [ボストン気象データ](https://azuremluxcdnprod001.blob.core.windows.net/docs/azureml/bikeshare/BostonWeather.csv)

   * Hubway 社の Web サイトから入手した Hubway の旅行データ

      - [201501-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201501-hubway-tripdata.zip)
      - [201504-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201504-hubway-tripdata.zip)
      - [201510-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201510-hubway-tripdata.zip)
      - [201601-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201601-hubway-tripdata.zip)
      - [201604-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201604-hubway-tripdata.zip)
      - [201610-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201610-hubway-tripdata.zip)
      - [201701-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201701-hubway-tripdata.zip)

1. ダウンロード後に各 .zip ファイルを解凍します。

## <a name="upload-data-files-to-azure-blob-storage"></a>Azure Blob Storage にデータ ファイルをアップロードする
Azure Blob Storage を使用してデータ ファイルをホストできます。

1. ご利用の HDInsight クラスターで使用されているのと同じストレージ アカウントを使用します。

    ![HDInsight クラスターのストレージ アカウント](media/tutorial-bikeshare-dataprep/hdinsightstorageaccount.png)

1. **BikeShare** データ ファイルを保存するために、**data-files** という名前の新しいコンテナーを作成します。

1. データ ファイルをアップロードします。 `weather` という名前のフォルダーに `BostonWeather.csv` をアップロードし、 `tripdata` という名前のフォルダーに旅行データ ファイルをアップロードします。

    ![データ ファイルのアップロード](media/tutorial-bikeshare-dataprep/azurestoragedatafile.png)

> [!TIP]
> Storage Explorer を使用して BLOB をアップロードすることもできます。 このツールは、チュートリアルで生成されたファイルの内容を確認する場合にも使用できます。

## <a name="learn-about-the-datasets"></a>データセットについて
1. __ボストンの気象__のファイルには、時間単位で報告される、次の気象関連フィールドが含まれています。

   * **DATE** (日付)

   * **REPORTTPYE**

   * **HOURLYDRYBULBTEMPF** (1 時間ごとの乾球温度)

   * **HOURLYRelativeHumidity** (1 時間ごとの相対湿度)

   * **HOURLYWindSpeed** (1 時間ごとの風速)

1. __Hubway__ 社のデータは、年月ごとのファイルとして編成されています。 たとえば、`201501-hubway-tripdata.zip` という名前のファイルには、2015 年 1 月のデータを含む .csv ファイルが含まれています。 データには、自転車旅行を表す次のフィールドが含まれています。

   * **旅行時間 (秒単位)**

   * **開始日時**

   * **終了日時**

   * **開始駅の名前および ID**

   * **終了駅の名前および ID**

   * **Bike ID**

   * **ユーザーの種類 (カジュアル = 24 時間または 72 時間パスのユーザー、メンバー = 年間または月単位のメンバー)**

   * **郵便番号 (ユーザーがメンバーである場合)**

   * **性別 (メンバーの自己報告)**

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する
1. スタート メニューまたは起動プログラムから、**Machine Learning Workbench** を起動します。

1. 新しい Machine Learning プロジェクトを作成します。 **[プロジェクト]** ページの **+** ボタンを選択するか、または **[ファイル]** > **[新規]** の順に選択します。

   * **[Bike Share]\(自転車共有\)** テンプレートを使用します。

   * プロジェクトに **BikeShare** という名前を付けます。 

## <a id="newdatasource"></a>新しいデータ ソースの作成

1. 新しいデータ ソースを作成します。 左側のツール バーにある **[データ]** ボタン (シリンダーのアイコン) を選択して**データ** ビューを表示します。

   ![データ ビュー タブ](media/tutorial-bikeshare-dataprep/navigatetodatatab.png)

1. データ ソースを追加します。 **+** アイコンを選択して、**[データ ソースの追加]** をクリックします。

   ![データ ソースの追加オプション](media/tutorial-bikeshare-dataprep/newdatasource.png)

## <a name="add-weather-data"></a>気象データの追加

1. **[データ ストア]**: データを含むデータ ストアを選択します。 ファイルを使用しているため、**[File(s)/Directory]\(ファイル/ディレクトリ\)** を選択します。 **[次へ]** をクリックして続行します。

   ![ファイル/ディレクトリ エントリ](media/tutorial-bikeshare-dataprep/datasources.png)

1. **[ファイルの選択]**: 気象データを追加します。 先ほど Blob Storage にアップロードした `BostonWeather.csv` ファイルを参照して選択します。 **[次へ]** を選択します。

   ![BostonWeather.csv が選択されているファイル選択](media/tutorial-bikeshare-dataprep/azureblobpickweatherdatafile.png)

1. **[ファイルの詳細]**: 検出されたファイルのスキーマを検証します。 Machine Learning Workbench はファイル内のデータを分析し、使用するスキーマを推論します。

   ![ファイルの詳細を検証](media/tutorial-bikeshare-dataprep/fileparameters.png)

   > [!IMPORTANT]
   > 場合によっては、Workbench で適切なスキーマが検出されないことがあります。 お使いのデータセットのパラメーターが正しいことを常に確認してください。 気象データの場合は、次の値に設定されていることを確認します。
   >
   > * __[ファイルの種類]__: 区切りファイル (csv、tsv、txt など)
   > * __[区切り記号]__: コンマ [,]
   > * __[Comment Line Character]\(コメント行の文字\)__: 値の設定なし。
   > * __[Skip Lines Mode]\(行のスキップ モード\)__: スキップしない
   > * __[ファイルのエンコード]__: utf-8
   > * __[Promote Headers Mode]\(ヘッダーの昇格モード\)__: 初期ファイルのヘッダーを使用する

   データのプレビューでは、次の列が表示されます。

   * **パス**

   * **DATE** (日付)

   * **REPORTTYPE** (プロパティの種類)

   * **HOURLYDRYBULBTEMPF** (1 時間ごとの乾球温度)
   
   * **HOURLYRelativeHumidity** (1 時間ごとの相対湿度)

   * **HOURLYWindSpeed** (1 時間ごとの風速)

   **[次へ]** を選択して、続行します。

1. **[データの種類]**: 自動的に検出されたデータの種類を確認します。 Machine Learning Workbench は、ファイルのデータを分析して、使用するデータの種類を推論します。

   a. このデータについては、すべての列の **[データ型]** を **[文字列]** に変更します。

   > [!NOTE]
   > String は、このチュートリアルで後から Workbench の機能を強調するために使用されます。 

   ![データ型の確認](media/tutorial-bikeshare-dataprep/datatypedetection.png)

   b. __[次へ]__ を選択して、続行します。 

1. **[サンプリング]**: サンプリング スキームを作成するには、**[編集]** を選択します。 追加された新しい__上位 10000__ 行を選択して、__[編集]__ を選択します。 __[Sample Strategy]\(サンプル戦略\)__ を **[Full File]\(完全ファイル\)** に設定してから、**[適用]** を選択します。

   ![新しいサンプル戦略の追加](media/tutorial-bikeshare-dataprep/weatherdatasamplingfullfile.png)

   __[Full File]\(完全ファイル\)__ 戦略を使用するために、__[Full File]\(完全ファイル\)__ エントリを選択して __[アクティブとして設定]__ を選びます。 __[Full File]\(完全ファイル\)__ の横に、アクティブな戦略であることを示す星が表示されます。

   ![アクティブな戦略として設定されている [Full File]\(完全ファイル\)](media/tutorial-bikeshare-dataprep/fullfileactive.png)

   **[次へ]** を選択して、続行します。

1. **[Path Column]\(パス列\)**: __[Path Column]\(パス列\)__ セクションを使用すると、インポートされたデータに完全なファイル パスを列として組み入れることができます。 __[Do Not Include Path Column]\(パス列を組み入れない\)__ を選びます。

   > [!TIP]
   > ファイル名の異なる多数のファイルを格納したフォルダーをインポートする場合は、パスを列として組み入れると便利です。 また、後で抽出したい情報がファイル名に含まれている場合にも便利です。

   ![パス列を組み入れない設定](media/tutorial-bikeshare-dataprep/pathcolumn.png)

1. **[完了]**: データ ソースの作成を完了するには、**[完了]** を選択します。

    __BostonWeather__ という名前の新しいデータ ソース タブが開きます。 データのサンプルは、グリッド ビューに表示されます。 サンプルは、前に指定されたアクティブなサンプリング スキームに基づいています。

    画面の右側にある **[ステップ]** ウィンドウには、このデータ ソースの作成中に実行された個々のアクションが表示されます。

   ![データ ソース、サンプル、ステップを表示](media/tutorial-bikeshare-dataprep/weatherdataloaded.png)

### <a name="view-data-source-metrics"></a>データ ソース メトリックの表示

タブのグリッド ビューの左上にある __[メトリック]__ を選択します。 このビューには、サンプル データの分布とその他の集計済みの統計が表示されます。

![メトリック表示](media/tutorial-bikeshare-dataprep/weathermetrics.png)

> [!NOTE]
> 統計の表示を設定するには、**[Choose Metric]\(メトリックの選択\)** ドロップダウン リストを使用します。 そこに表示されるメトリックをオンまたはオフにして、グリッド ビューを変更します。

__データ__ ビューに戻るには、ページの左上の __[データ]__ を選択します。

## <a name="add-a-data-source-to-the-data-preparation-package"></a>データ ソースをデータ準備パッケージに追加する

1. __[準備]__ を選択して、データの準備を開始します。 

1. プロンプトが表示されたら、データ準備パッケージの名前 (**BikeShare Data Prep** など) を入力します。 

1. __[OK]__ を選択して続行します。

   ![[準備] ダイアログ ボックス](media/tutorial-bikeshare-dataprep/dataprepdialog.png)

1. **BikeShare Data Prep** という名前の新しいパッケージが、__[データ]__ タブの __[データ準備]__ セクションに表示されます。 

   パッケージを表示するには、このエントリを選択します。 

1. **>>** ボタンを選択して __[Dataflows]\(データフロー\)__ を展開し、パッケージに含まれているデータフローを表示します。 この例では、__BostonWeather__ が唯一のデータフローです。

   > [!IMPORTANT]
   > パッケージには、複数のデータフローを含めることができます。

   ![パッケージ内のデータフロー](media/tutorial-bikeshare-dataprep/weatherdataloadedingrid.png)

## <a name="filter-data-by-value"></a>値によるデータのフィルター処理
1. データをフィルター処理するには、特定の値を含むセルを右クリックして __[フィルター]__ を選択し、 フィルターの種類を選択します。

1. このチュートリアルでは、値 `FM-15` を含むセルを選択して、 フィルターを **[equals]\(に等しい\)** に設定します。  これで、__REPORTTYPE__ が `FM-15` になっている戻り値の行だけに、データがフィルター処理されました。

   ![[フィルター] ダイアログ ボックス](media/tutorial-bikeshare-dataprep/weatherfilterinfm15.png)

   > [!NOTE]
   > FM-15 は、Meteorological Terminal Aviation Routine (METAR) 気象通報の一種です。 FM-15 のレポートは経験的に、データの欠損がほとんどなく、最も完成されていると考えられています。

## <a name="remove-a-column"></a>列の削除

__REPORTTYPE__ 列は必要なくなりました。 列見出しを右クリックして **[列の削除]** を選択します。

   ![[列の削除] オプション](media/tutorial-bikeshare-dataprep/weatherremovereporttype.png)

## <a name="change-datatypes-and-remove-errors"></a>データ型の変更とエラーの除去
1. Ctrl キー (Mac ではコマンド ⌘ キー) を押したまま列見出しを選択することで、複数の列を一度に選択することができます。 この方法を使用して、以下の列見出しを選択します。

   * **HOURLYDRYBULBTEMPF** (1 時間ごとの乾球温度)

   * **HOURLYRelativeHumidity** (1 時間ごとの相対湿度)

   * **HOURLYWindSpeed** (1 時間ごとの風速)

1. 選択した列見出しの 1 つを右クリックして、**[Convert Field Type to Numeric]\(フィールドの型を数値に変換する\)** を選択します。 このオプションにより、列のデータ型が数値に変換されます。

   ![複数の列を数値に変換する](media/tutorial-bikeshare-dataprep/weatherconverttonumeric.png)

1. エラー値をフィルターで除外します。 一部の列に、データ型の変換の問題が発生します。 この問題は、列の __Data Quality Bar\(データ品質バー\)__ に赤色で示されます。

   エラーを含む行を削除するには、**HOURLYDRYBULBTEMPF** 列の見出しを右クリックします。 **[列のフィルター]** を選択します。 既定の **[I Want To]\(次の設定を使用する\)** を **[Keep Rows]\(行の保持\)** として使用します。 **[条件]** ドロップダウン リストの選択を **[is not error]\(エラーではない\)** に変更します。 **[OK]** を選択して、フィルターを適用します。

   ![エラー値のフィルター](media/tutorial-bikeshare-dataprep/filtererrorvalues.png)

1. 他の列に残っているエラー行を除去するには、**HOURLYRelativeHumidity** 列と **HOURLYWindSpeed** 列に対してこのフィルター処理を繰り返します。

## <a name="use-by-example-transformations"></a>例の使用による変換

2 時間の時間ブロック予測でデータを使用するには、2 時間という期間の平均の気象条件を計算する必要があります。 次の操作を実行してください。

* **DATE** 列を別個の **Date** 列と **Time** 列に分割します。 詳細な手順については、次のセクションを参照してください。

* **Time** 列から **Hour_Range**列を派生します。 詳細な手順については、次のセクションを参照してください。

* **DATE** および **Hour_Range** 列から **Date\_Hour\_Range** 列を派生します。 詳細な手順については、次のセクションを参照してください。

### <a name="split-column-by-example"></a>列の分割の例

1. **DATE** 列を別個の **Date** 列と **Time** 列に分割します。 **DATE** 列の見出しを右クリックし、**[Split Column by Example]\(例による列の分割\)** を選択します。

   ![[Split Column by Example]\(例による列の分割\) 項目](media/tutorial-bikeshare-dataprep/weathersplitcolumnbyexample.png)

1. Machine Learning Workbench では、自動的に意味のある区切り記号を識別し、データを日付と時刻の値に分割することで、2 つの列を作成します。 

1. __[OK]__ を選択して、分割操作の結果を受け入れます。

   ![分割された列 DATE_1 および DATE_2](media/tutorial-bikeshare-dataprep/weatherdatesplitted.png)

### <a name="derive-column-by-example"></a>列の派生の例

1. 2 時間の範囲を派生させるには、__DATE\_2__ 列の見出しを右クリックして、**[Derive Column by Example]\(例による列の派生\)** を選択します。

   ![[Derive Column by Example]\(例による列の派生\) 項目](media/tutorial-bikeshare-dataprep/weatherdate2range.png)

   null 値を使って、新しい空白の列が追加されます。

1. 新しい列にある最初の空白のセルを選択します。 必要な時間範囲の例を指定するために、新しい列に「**12AM-2AM**」と入力して Enter キーを押します。

   ![値に 12AM-2AM が設定された新しい列](media/tutorial-bikeshare-dataprep/weathertimerangeexample.png)

   > [!NOTE]
   > Machine Learning Workbench は、ユーザーから指定された例に基づくプログラムを合成して、残りの行に同じプログラムを適用します。 他のすべての行には、ユーザーが指定した例に基づいて自動的に値が入力されます。 また、ワークベンチでは、データを分析してエッジ ケースを特定しようとします。 

   > [!IMPORTANT]
   > 現在のバージョンの Workbench では、Mac でエッジ ケースの特定が機能しない可能性があります。 Mac では、次の手順 3. と手順 4. をスキップしてください。 代わりに、派生した値がすべての行に設定されたら、__[OK]__ を押します。
   
1. グリッドの上にあるテキスト "**データを分析しています**" は、ワークベンチがエッジ ケースの検出を試行していることを示します。 完了すると、"**Review next suggested row**\(提示された次の行を確認する\)" または "**No suggestions\(提示なし\)**" に状態が切り替わります。 この例では、"**Review next suggested row\(提示された次の行を確認する\)**" が返されます。

1. 提示された変更内容を確認するには、**[Review next suggested row]\(提示された次の行を確認する\)** を選択します。 確認および (必要な場合は) 修正する対象のセルが、画面に強調表示されます。

   ![[Review next suggested row]\(提示された次の行を確認する\)](media/tutorial-bikeshare-dataprep/weatherreviewnextsuggested.png)

    __[OK]__ を選択して、変換を受け入れます。
 
1. __BostonWeather__ データのグリッド ビューに画面が戻ります。 これで、以前に追加した 3 つの列がグリッドに組み入れられました。

   ![追加された行があるグリッド ビュー](media/tutorial-bikeshare-dataprep/timerangecomputed.png)

   > [!TIP]
   > 行われた変更はすべて、**[ステップ]** ウィンドウに保存されます。 **[ステップ]** ウィンドウで、下向き矢印を選択して **[編集]** を選択します。 **[列の派生の例]** の詳細ウィンドウが表示されます。 すべての例がここに保存されています。 以下のグリッドの行をダブルクリックして、手動で例を追加することもできます。 **[キャンセル]** を選択して、変更を適用せずにグリッドのメイン ページに戻ります。 **[Derive Column by Example]\(例による列の派生\)** 変換の実行中に **[Advanced mode]\(詳細設定モード\)** を選択して、このビューにアクセスすることもできます。

1. 列の名前を変更するには、列見出しをダブルクリックして「**Hour Range**」と入力します。 Enter キーを押して変更を保存します。

   ![列名の変更](media/tutorial-bikeshare-dataprep/weatherhourrangecolumnrename.png)

1. 日付と時間の範囲を派生するには、**Date\_1** および **Hour Range** 列を複数選択し、右クリックして **[Derive column by example]\(例による列の派生\)** を選択します。

   ![列の派生の例](media/tutorial-bikeshare-dataprep/weatherderivedatehourrange.png)

   最初の行に対する例として「**Jan 01, 2015 12AM-2AM**」と入力し、Enter キーを押します。

   Workbench は、指定した例に基づいて変換を決定します。 この例では、結果として、日付の形式が変更され、2 時間の枠と連結されました。

   ![Jan 01, 2015 12AM-2AM (2015 年 1 月 1 日 午前 0 時から午前 2 時) の例](media/tutorial-bikeshare-dataprep/wetherdatehourrangeexample.png)

   > [!IMPORTANT]
   > Mac では、手順 8 ではなく次の手順を実行します。
   >
   > * **Feb 01, 2015 12AM-2AM** を含む最初のセルに移動します。 この例では 15 行目です。 この値を **Jan 02, 2015 12AM-2AM** に修正して、Enter キーを押します。 
   

1. **[データを分析しています]** から **[Review next suggested row]\(提示された次の行を確認する\)** に状態が変更するまで待機します。 この処理には数秒かかることがあります。 状態のリンクを選択して、提示された行に移動します。 

   ![提示された確認が必要な行](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguate.png)

   ソースの日付の値が dd/mm/yyyy または mm/dd/yyyy のどちらかに対応にしている可能性があるため、行には null 値が設定されています。 正しい値 (**Jan 13, 2015 2AM-4AM**) を入力し、Enter キーを押します。 Workbench では、残りの行の派生処理を改善するために、2 つの例を使用しています。

   ![正しく書式設定されたデータ](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguated.png)

1. **[OK]** を選択して、変換を受け入れます。

   ![完了した変換グリッド](media/tutorial-bikeshare-dataprep/weatherdatehourrangecomputed.png)

   > [!TIP]
   > この手順では、**[Derive Column by Example]\(例による列の派生\)** の **[Advanced mode]\(詳細設定モード\)** を使用するために、 **[ステップ]** ウィンドウで下矢印を選択します。 データ グリッドでは、**DATE\_1** と **Hour Range** 列の横にチェック ボックスがあります。 **Hour Range** 列の横にあるチェックボックスをオフにして、この設定で出力がどう変化するかを確認します。 入力値に **Hour Range** 列がない場合、**12AM-2AM** は定数として処理され、派生した値にアペンドされます。 **[キャンセル]** を選択して、変更を適用せずにグリッドのメイン ページに戻ります。
   ![[Advanced mode]\(詳細設定モード\)](media/tutorial-bikeshare-dataprep/derivedcolumnadvancededitdeselectcolumn.png)

1. 列の名前を変更するには、見出しをダブルクリックします。 名前を **Date Hour Range** に変更して、Enter キーを押します。

1. **DATE**、**DATE\_1**、**DATE\_2**、および **Hour Range** 列を複数選択します。 右クリックして、**[列の削除]** を選択します。

## <a name="summarize-data-mean"></a>データの集計 (平均) 

次の手順では、時間の範囲でグループ化して、値の平均を取得することで、気象条件を集計します。

1. **Date Hour Range** 列を選択して、**[変換]** メニューから **[集計]** を選択します。

    ![[変換] メニュー](media/tutorial-bikeshare-dataprep/weathersummarizemenu.png)

1. データを集計するには、ページ下部にあるグリッドから上部の左右のウィンドウに列をドラッグします。 左側のウィンドウには、"**Drag columns here to group data\(データをグループ化するには、ここに列をドラッグしててください\)**" というテキストが表示されています。 右側のウィンドウには、"**Drag columns here to summarize data\(データを集計するには、ここに列をドラッグしててください\)**" というテキストが表示されています。 

    a. 下部にあるグリッドから左側のウィンドウに **Date Hour Range** 列をドラッグします。 **HOURLYDRYBULBTEMPF**、**HOURLYRelativeHumidity**、および**HOURLYWindSpeed** を右側のウィンドウにドラッグします。 

    b. 右側のウィンドウで各列の **[集計]** メジャーとして **[平均]** を選択します。 **[OK]** を選択して集計を完了します。

    ![データの集計画面](media/tutorial-bikeshare-dataprep/weathersummarize.png)

## <a name="transform-dataflow-by-using-script"></a>スクリプトを使用したデータフローの変換

数値型の列のデータを 0 から 1 の範囲に変更すると、一部のモデルを迅速に収束させることができます。 現状では、この変換を汎用的に実行する組み込みの変換機能はありません。 この処理は、Python スクリプトを使用して実行できます。

1. **[変換]** メニューの **[Transform Dataflow (Script)]\(データフローの変換 (スクリプト)\)** を選択します。

1. 表示されたテキストボックスに次のコードを入力します。 列名を使用している場合は、変更を伴わずにコードが機能する必要があります。 最小 / 最大正規化の単純なロジックを Python で記述しています。

    > [!WARNING]
    > スクリプトでは、このチュートリアルで以前に使用された列名を期待しています。 別の列名を使用している場合は、スクリプト内の名前を変更する必要があります。

   ```python
   maxVal = max(df["HOURLYDRYBULBTEMPF_Mean"])
   minVal = min(df["HOURLYDRYBULBTEMPF_Mean"])
   df["HOURLYDRYBULBTEMPF_Mean"] = (df["HOURLYDRYBULBTEMPF_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYDRYBULBTEMPF_Mean":"N_DryBulbTemp"},inplace=True)

   maxVal = max(df["HOURLYRelativeHumidity_Mean"])
   minVal = min(df["HOURLYRelativeHumidity_Mean"])
   df["HOURLYRelativeHumidity_Mean"] = (df["HOURLYRelativeHumidity_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYRelativeHumidity_Mean":"N_RelativeHumidity"},inplace=True)

   maxVal = max(df["HOURLYWindSpeed_Mean"])
   minVal = min(df["HOURLYWindSpeed_Mean"])
   df["HOURLYWindSpeed_Mean"] = (df["HOURLYWindSpeed_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYWindSpeed_Mean":"N_WindSpeed"},inplace=True)

   df
   ```

    > [!TIP]
    > Python スクリプトでは、最後に `df` を返す必要があります。 この値は、グリッドの入力に使用されます。
    
   ![[Transform Dataflow (Script)]\(データフローの変換 (スクリプト)\) ダイアログ ボックス](media/tutorial-bikeshare-dataprep/transformdataflowscript.png)

1. __[OK]__ を選択してスクリプトを使用します。 これで、グリッド内の数値列には、0 から 1 の範囲の値が含まれるようになりました。

    ![0 から 1 の値を含むグリッド](media/tutorial-bikeshare-dataprep/datagridwithdecimals.png)

気象データの準備が完了しました。 次は、旅行データを準備します。

## <a name="load-trip-data"></a>旅行データの読み込み

1. `201701-hubway-tripdata.csv` ファイルをインポートするには、「[新しいデータ ソースの作成](#newdatasource)」セクションにある手順に従います。 インポートのプロセスでは、以下のオプションを使用します。

    * __[ファイルの選択]__: 参照画面でファイルを選択するときに **[Azure BLOB]** を選択します。

    * __[Sampling Scheme]\(サンプリング スキーム\)__: **Full File\(完全ファイル\)** サンプリング スキーム。サンプルをアクティブにします。

    * __[データ型]__: 既定値を受け入れます。

1. データのインポート後、__[準備]__ を選択して、データの準備を開始します。 既存の **BikeShare Data Prep.dprep** パッケージを選択して、__[OK]__ を選択します。

    このプロセスでは、**データフロー**を既存の **[データ準備]** ファイルに追加します。新規のパッケージ作成は行いません。

    ![既存のパッケージを選択](media/tutorial-bikeshare-dataprep/addjandatatodprep.png)

1. グリッドが読み込まれたら、__[DATAFLOWS]\(データフロー\)__ を展開します。 **BostonWeather** および **201701 hubway-tripdata** という 2 つのデータフローが表示されています。 **[201701-hubway-tripdata]** エントリを選択します。

    ![201701-hubway-tripdata entry](media/tutorial-bikeshare-dataprep/twodfsindprep.png)

## <a name="use-the-map-inspector"></a>マップ インスペクターの使用

データ準備では、文字列、数値、地理データに対する "インスペクター" と呼ばれる便利な視覚化機能が用意されています。 この機能を活用することで、データへの理解を深め、外れ値を特定することができます。 マップ インスペクターを使用するには、次の手順に従います。

1. **start station latitude** および **start station longitude** 列を複数選択します。 どちらかの列を右クリックして、**[マップ]** を選択します。

    > [!TIP]
    > 複数選択を行うには、Ctrl キー (Mac ではコマンド ⌘ キー) を押しながら各列の見出しを選択します。

    ![マップの視覚化](media/tutorial-bikeshare-dataprep/launchMapInspector.png)

1. マップの視覚化を最大限で行うには、**最大化**のアイコンをクリックします。 マップをウィンドウに合わせるには、視覚化機能の左上にある **[E]** アイコンを選択します。

    ![最大化の画像](media/tutorial-bikeshare-dataprep/maximizedmap.png)

1. **[最小化]** ボタンをクリックすると、グリッドに戻ります。

## <a name="use-the-column-statistics-inspector"></a>列の統計インスペクターの使用

列の統計インスペクターを使用するには、__tripduration__ 列を右クリックし、__[Column Statistics]\(列の統計)__ を選択します。

![列の統計エントリ](media/tutorial-bikeshare-dataprep/tripdurationcolstats.png)

このプロセスでは、__[INSPECTORS]\(インスペクター\)__ ウィンドウにある __[tripduration Statistics]\(旅行時間の統計\)__ というタイトルの新しい視覚化を追加します。

![旅行時間の統計インスペクター](media/tutorial-bikeshare-dataprep/tripdurationcolstatsinwell.png)

> [!IMPORTANT]
> 旅行時間の最大値は 961,814 分であり、約 2 年間に相当します。 データセットにいくつかの外れ値がありそうです。

## <a name="use-the-histogram-inspector"></a>ヒストグラム インスペクターの使用

外れ値の特定を試みるには、__tripduration__ 列を右クリックして、__[ヒストグラム]__ を選択します。

![ヒストグラム インスペクター](media/tutorial-bikeshare-dataprep/tripdurationhistogram.png)

外れ値がグラフを歪めてしまうので、ヒストグラムは役立ちません。

## <a name="add-a-column-by-using-script"></a>スクリプトを使用した列の追加

1. **tripduration** 列を右クリックして、**[列の追加 (スクリプト)]** を選択します。

    ![[列の追加 (スクリプト)] メニュー](media/tutorial-bikeshare-dataprep/computecolscript.png)

1. __[列の追加 (スクリプト)]__ ダイアログ ボックスでは、次の値を使用します。

    * __[新しい列名]__: logtripduration

    * __[Insert this New Column After]\(の次にこの新しい列を挿入します\)__: tripduration

    * __[New Column Code]\(新しい列のコード\)__: `math.log(row.tripduration)`

    * __[Code Block Type]\(コード ブロックの種類\)__: 式

   ![[列の追加 (スクリプト)] ダイアログ ボックス](media/tutorial-bikeshare-dataprep/computecolscriptdialog.png)

1. __[OK]__ を選択して **logtripduration** 列を追加します。

1. 列を右クリックして、**[ヒストグラム]** を選択します。

    ![logtripduration 列のヒストグラム](media/tutorial-bikeshare-dataprep/logtriphistogram.png)

    視覚的には、このヒストグラムは末尾が異常な正規分布のように見えます。

## <a name="use-an-advanced-filter"></a>高度なフィルターの使用

データにフィルターを使用すると、インスペクターが新しい分布に更新されます。 

1. **logtripduration** 列を右クリックして、**[列フィルター]** を選択します。 

1. __[編集]__ ダイアログ ボックスで、次の値を使用します。

    * __[Filter this Number Column]\(この数値列をフィルターする\)__: logtripduration

    * __[I Want To]\(次の設定を使用する\)__: Keep Rows\(行の保持\)

    * __[When\(タイミング\)]__: Any of the Conditions below are True (logical OR)\(以下のいずれかの条件が True になっている (論理和)\)

    * __[If this Column]\(この列の条件\)__: less than\(未満\)

    * __[The Value]\(値\)__: 9

    ![フィルター オプション](media/tutorial-bikeshare-dataprep/loftripfilter.png)

1. __[OK]__ を選択してフィルターを適用します。

    ![フィルターが適用された後の更新後のヒストグラム](media/tutorial-bikeshare-dataprep/loftripfilteredinspector.png)

### <a name="the-halo-effect"></a>ハロー効果

1. **logtripduration** のヒストグラムを最大化します。 青いヒストグラムが、灰色のヒストグラムに重なって表示されます。 この表示は**ハロー効果**と呼ばれています。

    * 灰色のヒストグラムは、処理 (ここでは、フィルター処理) 前の分布を表します。

    * 青いヒストグラムは、処理後のヒストグラムを表します。 

   ハロー効果は、データに対する処理の効果を視覚化するうえで役立ちます。

   ![ハロー効果](media/tutorial-bikeshare-dataprep/loftripfilteredinspectormaximized.png)

    > [!NOTE]
    > 青いヒストグラムが、処理前に比べて短く表示されていることに気が付きます。 この違いは、データが新しい範囲に自動的に再調整されたためです。

1. ハローを削除するには、__[編集]__ を選択して __[Show halo]\(ハローの表示\)__ をオフにします。

    ![ヒストグラムのオプション](media/tutorial-bikeshare-dataprep/uncheckhalo.png)

1. **[OK]** を選択してハロー効果を無効にし、 ヒストグラムを最小化します。

### <a name="remove-columns"></a>列の削除

旅行データの場合、各行は自転車の受け取りイベントを表します。 このチュートリアルでは、**starttime** と **start station id** 列のみが必要です。 これら 2 つの列を複数選択して、列見出しを右クリックし、**[Keep Column]\(列を保持する\)** を選択することで、他の列を除去します。 その他の列が削除されます。

![[Keep Column]\(列を保持する\) オプション](media/tutorial-bikeshare-dataprep/tripdatakeepcolumn.png)

### <a name="summarize-data-count"></a>データの集計 (数)

2 時間の自転車の需要を集計するには、派生列を使用します。

1. **starttime** 列を右クリックし、**[Derive Column by Example]\(例による列の派生\)** を選択します。

    ![[Derive Column by Example]\(例による列の派生\) オプション](media/tutorial-bikeshare-dataprep/tripdataderivebyexample.png)

1. 例では、最初の行に **Jan 01, 2017 12AM-2AM** という値を入力します。

    > [!IMPORTANT]
    > 前の例の派生列では、日時の期間を含む列を派生するために、複数のステップを使用しました。 この例では、最終的な出力の例を提供することで、この処理が 1 つのステップとして実行できることがわかります。

    > [!NOTE]
    > 任意の行に対して、例を付与することができます。 この例では、値 **Jan 01, 2017 12AM-2AM** はデータの最初の行に対して有効です。

    ![サンプル データ](media/tutorial-bikeshare-dataprep/tripdataderivebyexamplefirstexample.png)

   > [!IMPORTANT]
   > Mac では、手順 3. ではなく次の手順を実行します。
   >
   > * **Jan 01, 2017 1AM-2AM** を含む最初のセルに移動します。 この例では 14 行目です。 この値を **Jan 01, 2017 12AM-2AM** に修正して、Enter キーを押します。 

1. アプリケーションがすべての行に対する値を計算するまで待機します。 このプロセスには数秒かかることがあります。 分析が完了したら、__[Review next suggested row]\(提示された次の行を確認する\)__ リンクを使用してデータを確認します。

   ![確認リンクがある完了した分析](media/tutorial-bikeshare-dataprep/tripdatabyexanalysiscomplete.png)

    計算された値が正しいことを確認します。 正しくない場合は、目的の値で更新して Enter キーを押します。 その後、分析が完了するまで待機します。 **[No suggestions]\(提示なし\)** を確認するまで、**[Review next suggested row]\(提示された次の行を確認する\)** プロセスを完了します。 **[No suggestions]\(提示なし\)** は、エッジ ケースがアプリケーションによって検出されたこと、また、合成されたプログラムに問題がないことを示します。 変換を受け入れる前に、変換されたデータを目視で検査することをお勧めします。 

1. **[OK]** を選択して、変換を受け入れます。 新しく作成された列の名前を **Date Hour Range** に変更します。

    ![名前が変更された列](media/tutorial-bikeshare-dataprep/tripdatasummarize.png)

1. **starttime** 列の見出しを右クリックし、**[列の削除]** を選択します。

1. データを集計するには、__[変換]__ メニューの __[集計]__ を選択します。 変換を作成するには、次の手順に従います。

    * __Date Hour Range__ および __start station id__ を左側の **[グループ化]** ウィンドウにドラッグします。

    * 右側の**データの集計**ウィンドウに __start station id__ をドラッグします。

   ![集計オプション](media/tutorial-bikeshare-dataprep/tripdatacount.png)

1. **[OK]** を選択して、集計結果を受け入れます。

## <a name="join-dataflows"></a>データフローを結合する

気象データと旅行データを結合するには、以下の手順に従います。

1. __[変換]__ メニューの __[結合]__ を選択します。

1. __[テーブル]__: **BostonWeather** を**左側**のデータフローとして、**201701 hubway-tripdata** を**右側**のデータフローとして選択します。 **[次へ]** を選択して、続行します。

    ![テーブルの選択](media/tutorial-bikeshare-dataprep/jointableselection.png)

1. __[キー列]__: 両方のテーブルの **Date Hour Range** 列を選択して、__[次へ]__ を選択します。

    ![キー列の選択](media/tutorial-bikeshare-dataprep/joinkeyselection.png)

1. __[結合の種類]__: 結合の種類として __[Matching rows]\(一致する行\)__ を選択し、__[完了]__ を選択します。

    ![結合の種類が [一致する行] になっている](media/tutorial-bikeshare-dataprep/joinscreen.png)

    このプロセスによって、__Join Result\(結合結果\)__ という名前の新しいデータフローが作成されます。

## <a name="create-additional-features"></a>追加機能の作成

1. 曜日を格納する列を作成するには、**Date Hour Range** 列を右クリックし、**[Derive Column by Example]\(例による列の派生\)** を選択します。 日曜日だった日付には、"__Sun__" という値を使用します。 たとえば、**Jan 01, 2017 12AM-2AM** です。 Enter キーを押して、**[OK]** を選択します。 この列の名前を __Weekday__ に変更します。

    ![曜日に使用する列を新たに作成](media/tutorial-bikeshare-dataprep/featureweekday.png)

1. 1 つの行に対応する時間単位の期間を格納する列を作成するには、**Date Hour Range** 列を右クリックし、**[Derive Column by Example]\(例による列の派生\)** を選択します。 **Jan 01, 2017 12AM-2AM** を含む行に対して、値 **12AM-2AM** を使用します。 Enter キーを押して、**[OK]** を選択します。 この列の名前を **Period** に変更します｡

    ![期間列](media/tutorial-bikeshare-dataprep/featurehourrange.png)

1. **Date Hour Range** 列と **r_Date Hour Range** 列を削除するには、Ctrl キー (Mac ではコマンド ⌘ キー) を押しながら各列の見出しを選択します。 右クリックして、**[列の削除]** を選択します。

## <a name="read-data-from-python"></a>Python のデータの読み取り

Python または PySpark から、データ準備のパッケージを実行して、**データ フレーム**として結果を取得します。

Python スクリプトの例を生成するには、__BikeShare Data Prep__ を右クリックして、__[Generate Data Access Code File]\(データ アクセス コード ファイルの生成\)__ を選択します。 Python ファイルの例が **[プロジェクト フォルダー]** に作成され、Workbench 内のタブにも読み込まれます。 以下の Python スクリプトは、生成されたコードの例です。

```python
# Use the Azure Machine Learning data preparation package
from azureml.dataprep import package

# Use the Azure Machine Learning data collector to log various metrics
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# This call will load the referenced package and return a DataFrame.
# If run in a PySpark environment, this call returns a
# Spark DataFrame. If not, it will return a Pandas DataFrame.
df = package.run('BikeShare Data Prep.dprep', dataflow_idx=0)

# Remove this line and add code that uses the DataFrame
df.head(10)
```

このチュートリアルでは、ファイルの名前は `BikeShare Data Prep.py` です。 このファイルは、このチュートリアルで後から使用します。

## <a name="save-test-data-as-a-csv-file"></a>テスト データを CSV ファイルとして保存します。

**Join Result\(結合結果\)** データフローを .csv ファイルに保存するには`BikeShare Data Prep.py` スクリプトを変更する必要があります。 

1. Visual Studio Code で編集するプロジェクトを開きます。

    ![Visual Studio Code でプロジェクトを開く](media/tutorial-bikeshare-dataprep/openprojectinvscode.png)

1. 次のコードを使用して、`BikeShare Data Prep.py` ファイルの Python スクリプトを更新します。

    ```python
    import pyspark

    from azureml.dataprep.package import run
    from pyspark.sql.functions import *

    # start Spark session
    spark = pyspark.sql.SparkSession.builder.appName('BikeShare').getOrCreate()

    # dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
    df = run('BikeShare Data Prep.dprep', dataflow_idx=2)
    df.show(n=10)
    row_count_first = df.count()

    # Example file name: 'wasb://data-files@bikesharestorage.blob.core.windows.net/testata'
    # 'wasb://<your container name>@<your azure storage name>.blob.core.windows.net/<csv folder name>
    blobfolder = 'Your Azure Storage blob path'

    df.write.csv(blobfolder, mode='overwrite') 

    # retrieve csv file parts into one data frame
    csvfiles = "<Your Azure Storage blob path>/*.csv"
    df = spark.read.option("header", "false").csv(csvfiles)
    row_count_result = df.count()
    print(row_count_result)
    if (row_count_first == row_count_result):
        print('counts match')
    else:
        print('counts do not match')
    print('done')
    ```

1. `Your Azure Storage blob path` を、作成する出力ファイルのパスに置き換えます。 `blobfolder` と `csvfiles` の両方の変数を置き換えます。

## <a name="create-an-hdinsight-run-configuration"></a>HDInsight 実行構成の作成

1. Machine Learning Workbench でコマンド ライン ウィンドウを開き、**[ファイル]** メニューを選択して、**[コマンド プロンプトを開く]** を選択します。 プロジェクト フォルダーでコマンド プロンプトが起動し、`C:\Projects\BikeShare>` というプロンプトが表示されます。

    ![コマンド プロンプトを開く](media/tutorial-bikeshare-dataprep/opencommandprompt.png)

   >[!IMPORTANT]
   >以降の手順を実行するには、(Workbench から起動した) コマンド ライン ウィンドウを使う必要があります。

1. コマンド プロンプトを使って Azure にサインインします。 

   Workbench アプリと CLI では、Azure リソースに対して認証を行う際に、独立した資格情報キャッシュが使用されます。 キャッシュされたトークンの有効期限が切れない限り、ログインは 1 回だけでかまいません。 `az account list` コマンドを実行すると、ログインで利用できるサブスクリプションの一覧が返されます。 複数のサブスクリプションがある場合は、目的のサブスクリプションの ID 値を使います。 `az account set -s` コマンドでそのサブスクリプションを使用する既定のアカウントとして設定し、サブスクリプション ID 値を指定します。 次に、account `show` コマンドを使って設定を確認します。

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

1. HDInsight 実行構成を作成します。クラスター名と `sshuser` パスワードが必要です。

    ```azurecli
    az ml computetarget attach cluster --name hdinsight --address <yourclustername>.azurehdinsight.net --username sshuser --password <your password>
    az ml experiment prepare -c hdinsight
    ```
> [!NOTE]
> 空のプロジェクトを作成した場合、既定の実行構成は **local** と **docker** です。 この手順では、スクリプトの実行時に Workbench で使用可能な新しい実行構成を作成します。 

## <a name="run-in-an-hdinsight-cluster"></a>HDInsight クラスターでの実行

Machine Learning Workbench アプリケーションに戻り、HDInsight クラスターでスクリプトを実行します。

1. 左側の **[ホーム]** アイコンを選択して、プロジェクトのホーム画面に戻ります。

1. ドロップダウン リストから **[hdinsight]** を選択して、HDInsight クラスターでスクリプトを実行します。

1. **[実行]** を選択します。 スクリプトはジョブとして送信されます。 ストレージ コンテナー内の指定した場所にファイルが書き込まれると、ジョブの状態が __[完了]__ に変わります。

    ![HDInsight 実行スクリプト](media/tutorial-bikeshare-dataprep/hdinsightrunscript.png)


## <a name="substitute-data-sources"></a>データ ソースの置き換え

前の手順で、テスト データの準備に `201701-hubway-tripdata.csv` および `BostonWeather.csv` データ ソースを使用しました。 他の旅行データ ファイルと共にパッケージを使用するには、次の手順に従います。

1. プロセスに以下の変更を加え、前述した手順を使用して新しいデータ ソースを作成します。

    * __[ファイルの選択]__: ファイルの選択時に、残りの旅行データの .csv ファイル 6 個を複数選択します。

    ![残りの 6 個のファイルを読み込む](media/tutorial-bikeshare-dataprep/browseazurestoragefortripdatafiles.png)

    > [!NOTE]
     > __[+5]__ エントリは、表示されているファイル以外に、さらに 5 つのファイルがあることを示します。

    * __[ファイルの詳細]__: __[Promote Headers Mode]\(ヘッダーの昇格モード\)__ を **[All Files Have The Same Headers]\(全ファイルで同じヘッダーにする\)** に設定します。 この値は、各ファイルに同じヘッダーが含まれていることを示します。

    ![ファイルの詳細の選択](media/tutorial-bikeshare-dataprep/headerfromeachfile.png) 

   後の手順で使用するために、このデータ ソースの名前を保存します。

1. フォルダーのアイコンを選択して、プロジェクト内のファイルを表示します。 __aml\_config__ ディレクトリを展開して、`hdinsight.runconfig` ファイルを選択します。

    ![hdinsight.runconfig の場所](media/tutorial-bikeshare-dataprep/hdinsightsubstitutedatasources.png) 

1. **[編集]** ボタンを選択して、Visual Studio Code でファイルを開きます。

1. `hdinsight.runconfig` ファイルの末尾に次の行を追加し、ディスク アイコンを選択してファイルを保存します。

    ```yaml
    DataSourceSubstitutions:
      201701-hubway-tripdata.dsource: 201501-hubway-tripdata.dsource
    ```

    この変更によって、元のデータ ソースが 6 個の旅行データ ファイルを含むデータ ソースに置き換えられます。

## <a name="save-training-data-as-a-csv-file"></a>トレーニング データを CSV ファイルとして保存する

1. 先ほど編集した Python ファイル `BikeShare Data Prep.py` を参照します。 トレーニング データの保存先となるファイル パスを変更します。

    ```python
    import pyspark

    from azureml.dataprep.package import run
    from pyspark.sql.functions import *

    # start Spark session
    spark = pyspark.sql.SparkSession.builder.appName('BikeShare').getOrCreate()

    # dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
    df = run('BikeShare Data Prep.dprep', dataflow_idx=2)
    df.show(n=10)
    row_count_first = df.count()

    # Example file name: 'wasb://data-files@bikesharestorage.blob.core.windows.net/traindata'
    # 'wasb://<your container name>@<your azure storage name>.blob.core.windows.net/<csv folder name>
    blobfolder = 'Your Azure Storage blob path'

    df.write.csv(blobfolder, mode='overwrite') 

    # retrieve csv file parts into one data frame
    csvfiles = "<Your Azure Storage blob path>/*.csv"
    df = spark.read.option("header", "false").csv(csvfiles)
    row_count_result = df.count()
    print(row_count_result)
    if (row_count_first == row_count_result):
        print('counts match')
    else:
        print('counts do not match')
    print('done')
    ```

1. トレーニング データの出力に `traindata` という名前のフォルダーを使用します。

1. 新しいジョブを送信するには、**[実行]** を選択します。 **[hdinsight]** が選択されていることを確認します。 新しい構成内容でジョブが送信されます。 このジョブの出力はトレーニング データです。 このデータは、先ほどと同じデータの準備手順を使用して、作成されています。 このジョブが完了するまで数分かかることがあります。


## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順
自転車シェアのデータ準備のチュートリアルはこれで完了です。 このチュートリアルでは、Machine Learning (プレビュー) を使って次の作業を行う方法について説明しました。
> [!div class="checklist"]
> * Machine Learning データ準備ツールを使用して対話形式でデータを準備する。
> * テスト データセットをインポート、変換、作成する。
> * データ準備パッケージを生成する。
> * Python を使用してデータ準備パッケージを実行する。
> * 追加の入力ファイルのデータ準備パッケージを再利用して、トレーニング データセットを生成する。

次は、データ準備の詳細を確認してください。
> [!div class="nextstepaction"]
> [データ準備ユーザー ガイド](data-prep-user-guide.md)
