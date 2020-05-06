---
title: チュートリアル:Power BI を使用して Azure HDInsight の Apache Spark データを分析する
description: チュートリアル - Microsoft Power BI を使用して、HDInsight クラスターに格納されている Apache Spark データを視覚化する
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc,seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 4eb10298ac683c991835f86582d82fb952b314b2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82195113"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>チュートリアル:HDInsight での Power BI を使用した Apache Spark データの分析

このチュートリアルでは、Microsoft Power BI を使用して Azure HDInsight で Apache Spark クラスター内のデータを視覚化する方法を学習します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Power BI を使用して Spark データを視覚化する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

* 「[チュートリアル: Azure HDInsight での Apache Spark クラスターへのデータの読み込みとクエリの実行](./apache-spark-load-data-run-query.md)」の記事を完了します。

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/)。

* 省略可能:[Power BI 試用版サブスクリプション](https://app.powerbi.com/signupredirect?pbi_source=web)。

## <a name="verify-the-data"></a>データの検証

[前のチュートリアル](apache-spark-load-data-run-query.md)で作成した [Jupyter Notebook](https://jupyter.org/) には、`hvac` テーブルを作成するためのコードが含まれています。 この表は、`\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv` にあるすべての HDInsight Spark クラスターで使用可能な CSV ファイルに基づいています。 データの検証に使用する手順は、以下のとおりです。

1. Jupyter Notebook から次のコードを貼り付けて、**Shift + Enter** キーを押します。 このコードによってテーブルの存在が検証されます。

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    出力は次のようになります。

    ![Spark でのテーブルの表示](./media/apache-spark-use-bi-tools/apache-spark-show-tables.png)

    このチュートリアルを開始する前に Notebook を閉じると、`hvactemptable` はクリーンアップされるため、出力に含まれません。  BI ツールからアクセスできるのは、metastore に保存された Hive テーブル (**isTemporary** 列に **False** と表示される) のみです。 このチュートリアルでは、作成した **hvac** テーブルに接続します。

2. 次のコードを空のセルに貼り付け、**Shift + Enter** キーを押します。 このコードによってテーブル内のデータが検証されます。

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    出力は次のようになります。

    ![Spark での hvac テーブルの行の表示](./media/apache-spark-use-bi-tools/apache-spark-select-limit.png)

3. ノートブックの **[File]\(ファイル\)** メニューの **[Close and Halt]\(閉じて停止\)** を選びます。 Notebook をシャットダウンしてリソースを解放します。

## <a name="visualize-the-data"></a>データの視覚化

このセクションでは、Power BI を使用して Spark クラスター データから視覚エフェクト、レポート、およびダッシュボードを作成します。

### <a name="create-a-report-in-power-bi-desktop"></a>Power BI Desktop でレポートを作成する

Spark を操作する最初のステップでは、Power BI Desktop のクラスターに接続し、クラスターからデータを読み込み、そのデータを基に基本的な視覚エフェクトを作成します。

1. Power BI Desktop を開きます。 起動のスプラッシュ画面が開いていれば閉じます。

2. **[ホーム]** タブから、 **[データの取得]**  >  **[その他...]** に移動します。

    ![HDInsight Apache Spark から Power BI Desktop にデータを取得](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Apache Spark BI から Power BI にデータを取得")

3. 検索ボックスに「`Spark`」と入力し、 **[Azure HDInsight Spark]** 、 **[接続]** の順に選択します。

    ![Apache Spark BI から Power BI にデータを取得](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Apache Spark BI から Power BI にデータを取得")

4. **[サーバー]** テキスト ボックスにクラスター URL を (`mysparkcluster.azurehdinsight.net` の形式で) 入力します。

5. **[データ接続モード]** で、 **[DirectQuery]** を選択します。 **[OK]** をクリックします。

    Spark では、いずれかのデータ接続モードをご利用いただけます。 DirectQuery を使用する場合、データセット全体を更新しなくても変更がレポートに反映されます。 データをインポートする場合は、データセットを更新して変更内容を確認する必要があります。 DirectQuery をいつ、どのように使用するかの詳細については、「[Power BI で DirectQuery を使用する](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/)」をご覧ください。

6. HDInsight のログイン アカウント情報を入力し、 **[接続]** を選択します。 既定のアカウント名は *admin* です。

7. `hvac` テーブルを選択し、データのプレビューが表示されるのを待ってから、 **[読み込み]** を選択します。

    ![Spark クラスターのユーザー名とパスワード](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark クラスターのユーザー名とパスワード")

    Spark クラスターに接続し、`hvac` テーブルからデータを読み込むために必要な情報が Power BI Desktop に整いました。 **[フィールド]** ウィンドウにテーブルとその列が表示されます。

8. 各ビルの目標温度と実際の温度の差を視覚化します。

    1. **[視覚エフェクト]** ウィンドウで **[面グラフ]** を選択します。

    2. **[BuildingID]** フィールドを**軸**にドラッグし、 **[ActualTemp]** と **[TargetTemp]** の各フィールドを**値**にドラッグします。

        ![値の列を追加](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "値の列を追加")

        図は次のようになります。

        ![面グラフの合計](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "面グラフの合計")

        既定では、**ActualTemp** および **TargetTemp** の合計が表示されます。 [視覚化] ウィンドウ内の **ActualTemp** と **TragetTemp** の横にある下矢印を選択すると、 **[合計]** が選択されていることを確認できます。

    3. [視覚化] ウィンドウ内の **ActualTemp** と **TragetTemp** の横にある下矢印を選択し、 **[平均]** を選択して、各ビルの実際の温度と目標温度の平均を取得します。

        ![値の平均](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "値の平均")

        次のスクリーンショットのようにデータが視覚化されます。 グラフの上にカーソルを移動すると、関連データを含むツール ヒントが表示されます。

        ![面グラフ](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "面グラフ")

9. **[ファイル]**  >  **[保存]** に移動し、ファイルの名前 `BuildingTemperature` を入力して **[保存]** を選択します。

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Power BI サービスにレポートを発行する (省略可能)

Power BI サービスを使用すると、組織全体でレポートとダッシュボードを共有できます。 このセクションでは、まずデータセットとレポートを発行します。 次に、このレポートをダッシュボードにピン留めします。 通常、ダッシュボードは、レポート内のデータのサブセットにフォーカスするために使用します。 レポート内にある視覚エフェクトは 1 つのみですが、それでも手順を実行するのに役立ちます。

1. Power BI Desktop を開きます。

1. **[ホーム]** タブで **[発行]** を選択します。

    ![Power BI Desktop から発行する](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Power BI Desktop から発行する")

1. データセットを発行してレポートするワークスペースを選択して、 **[選択]** をクリックします。 次の図では、既定値の **[マイ ワークスペース]** が選択されています。

    ![データセットを発行してレポートするワークスペースの選択](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "データセットを発行してレポートするワークスペースの選択")

1. 発行に成功したら、 **[Open 'BuildingTemperature.pbix' in Power BI]\('BuildingTemperature.pbix' を Power BI で開く\)** を選択します。

    ![発行の成功、クリックして資格情報を入力](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "発行の成功、クリックして資格情報を入力")

1. Power BI サービスで、 **[資格情報を入力する]** を選択します。

    ![Power BI サービスで資格情報を入力](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Power BI サービスで資格情報を入力")

1. **[資格情報を編集]** を選択します。

    ![Power BI サービスで資格情報を編集](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Power BI サービスで資格情報を編集")

1. HDInsight のログイン アカウント情報を入力し、 **[サインイン]** を選択します。 既定のアカウント名は *admin* です。

    ![Spark クラスターへのサインイン](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Spark クラスターへのサインイン")

1. 左ペインで **[ワークスペース]**  >  **[マイ ワークスペース]**  >  **[レポート]** の順に移動して、 **[BuildingTemperature]** を選択します。

    ![左側のペインのレポートの下に一覧表示されたレポート](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "左側のペインのレポートの下に一覧表示されたレポート")

    左ウィンドウ枠の **[データセット]** にも **[BuildingTemperature]** が表示されます。

    これで、Power BI Desktop で作成したビジュアルが Power BI サービスで使用できるようになりました。

1. 視覚エフェクトにカーソルを合わせ、右上隅のピン アイコンを選択します。

    ![Power BI サービスのレポート](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Power BI サービスのレポート")

1. [新しいダッシュボード] を選択して、名前 `Building temperature` を入力し、 **[ピン留め]** を選択します。

    ![新しいダッシュボードにピン留め](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "新しいダッシュボードにピン留め")

1. レポートで、 **[ダッシュボードへ移動]** を選択します。

ビジュアルはダッシュボードにピン留めされます。他のビジュアルをレポートに追加して、同じダッシュボードにピン留めすることもできます。 レポートとダッシュボードの詳細については、「[Power BI のレポート](https://powerbi.microsoft.com/documentation/powerbi-service-reports/)」のほか、[Power BI のダッシュボード](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)に関する記事を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

チュートリアルを完了したら、必要に応じてクラスターを削除できます。 HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。

クラスターを削除するには、「[ブラウザー、PowerShell、または Azure CLI を使用して HDInsight クラスターを削除する](../hdinsight-delete-cluster.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Microsoft Power BI を使用して Azure HDInsight で Apache Spark クラスター内のデータを視覚化する方法を学習しました。 次の記事に進み、Machine Learning アプリケーションを作成できることを確認します。

> [!div class="nextstepaction"]
> [Machine Learning アプリケーションの作成](./apache-spark-ipython-notebook-machine-learning.md)