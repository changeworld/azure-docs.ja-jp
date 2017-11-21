---
title: "Azure HDInsight でデータ視覚化ツールを使用する Spark BI | Microsoft Docs"
description: "HDInsight クラスター上で Apache Spark BI を使用して分析用のデータ視覚化ツールを使用する"
keywords: "apache spark bi,spark bi, spark データ視覚化, spark ビジネス インテリジェンス"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: nitinme
ms.openlocfilehash: 3886923639be8a7bd8167f10db503d7ebf8c1657
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2017
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Azure HDInsight のデータ視覚化ツールを使用する Apache Spark BI

Power BI と Tableau を使用して、Azure HDInsight の Apache Spark クラスター内のデータを視覚化する方法について説明します。

## <a name="prerequisites"></a>前提条件

* HDInsight での Apache Spark クラスター。 手順については、 [Azure HDInsight での Apache Spark クラスターの作成](apache-spark-jupyter-spark-sql.md)に関するページを参照してください。
* クラスター内のサンプル データ。 手順については、[HDInsight Spark クラスターに対する対話型クエリの実行](apache-spark-load-data-run-query.md)に関する記事をご覧ください。
* Power BI: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) と [Power BI 試用版サブスクリプション](https://app.powerbi.com/signupredirect?pbi_source=web) (省略可能)。
* Tableau: [Tableau Desktop](http://www.tableau.com/products/desktop) と [Microsoft Spark ODBC ドライバー](http://go.microsoft.com/fwlink/?LinkId=616229)。


## <a name="hivetable"></a>サンプル データの確認

[前のチュートリアル](apache-spark-load-data-run-query.md)で作成した Jupyter Notebook には、`hvac` テーブルを作成するコードが含まれています。 このテーブルは、**\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv** のすべての HDInsight Spark クラスターで使用可能な CSV ファイルに基づいています。 視覚エフェクトを作成する前に、Spark クラスター内のデータを確認してみましょう。

1. 必要なテーブルが存在することを確認します。 Notebook の空のセルに次のスニペットをコピーして、 **Shift + Enter**キーを押します。

        %%sql
        SHOW TABLES

    以下のような結果が出力されます。

    ![Spark でのテーブルの表示](./media/apache-spark-use-bi-tools/show-tables.png)

    このチュートリアルを開始する前に Notebook を閉じると、`hvactemptable` はクリーンアップされるため、出力に含まれません。
    BI ツールからアクセスできるのは、metastore に保存された Hive テーブル (**isTemporary** 列に **False** と表示される) のみです。 このチュートリアルでは、作成した **hvac** テーブルに接続します。

2. テーブルに目的のデータが含まれていることを確認します。 Notebook の空のセルに次のスニペットをコピーして、 **Shift + Enter**キーを押します。

        %%sql
        SELECT * FROM hvac LIMIT 10

    以下のような結果が出力されます。

    ![Spark での hvac テーブルの行の表示](./media/apache-spark-use-bi-tools/select-limit.png)

3. Notebook をシャットダウンしてリソースを解放します。 そのためには、Notebook の **[ファイル]** メニューの **[Close and Halt]** (閉じて停止) をクリックします。

## <a name="powerbi"></a>Spark データ視覚化のための Power BI の使用

目的のデータが存在することを確認できたので、Power BI を使用してこのデータから視覚エフェクト、レポート、およびダッシュボードを作成できます。 この記事では、静的データを使った単純な例を示しますが、さらに複雑なストリーミングの例を参照したい場合はコメントでお知らせください。

### <a name="create-a-report-in-power-bi-desktop"></a>Power BI Desktop でレポートを作成する
Spark を操作する最初のステップでは、Power BI Desktop のクラスターに接続し、クラスターからデータを読み込み、そのデータを基に基本的な視覚エフェクトを作成します。

> [!NOTE]
> この記事に示されているコネクタは現在プレビュー段階ですが、このコネクタを使用し、そのフィードバックを [Power BI コミュニティ](https://community.powerbi.com/) サイトや [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas) からお寄せください。

1. Power BI Desktop の **[ホーム]** タブで、**[データの取得]**、**[詳細]** の順にクリックします。

2. `Spark` を検索して **[Azure HDInsight Spark]** を選択し、**[接続]** をクリックします。

    ![Apache Spark BI から Power BI にデータを取得](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Apache Spark BI から Power BI にデータを取得")

3. クラスターの URL (`mysparkcluster.azurehdinsight.net` の形式) を入力し、**[DirectQuery]** を選択して **[OK]** をクリックします。

    ![Apache Spark BI に接続](./media/apache-spark-use-bi-tools/connect-to-apache-spark-bi.png "Apache Spark BI に接続")

    > [!NOTE]
    > Spark では、いずれかの接続モードが使用できます。 DirectQuery を使用する場合、データセット全体を更新しなくても変更がレポートに反映されます。 データをインポートする場合は、データセットを更新して変更内容を確認する必要があります。 DirectQuery をいつ、どのように使用するかの詳細については、「[Power BI で DirectQuery を使用する](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/)」をご覧ください。 

4. HDInsight のログイン アカウント情報の **[ユーザー名]** と **[パスワード]** (既定のアカウントは `admin`) を入力して、**[接続]** をクリックします。

    ![Spark クラスターのユーザー名とパスワード](./media/apache-spark-use-bi-tools/user-password.png "Spark クラスターのユーザー名とパスワード")

5. `hvac` テーブルを選択して、データのプレビューが表示されるのを待ちます。 **[読み込み]** をクリックします。

    ![Spark クラスターのユーザー名とパスワード](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark クラスターのユーザー名とパスワード")

    Spark クラスターに接続し、`hvac` テーブルからデータを読み込むために必要なすべての情報が Power BI Desktop に整いました。 **[フィールド]** ウィンドウにテーブルとその列が表示されます。

    ![Apache Spark BI ダッシュボードにテーブルを一覧表示](./media/apache-spark-use-bi-tools/apache-spark-bi-display-tables.png "Apache Spark BI ダッシュボードにテーブルを一覧表示")

7. 各ビルの目標温度と実際の温度の差を示す視覚エフェクトを作成します。 

    1. **[視覚エフェクト]** ウィンドウで **[面グラフ]** を選択します。 **[BuildingID]** フィールドを**軸**にドラッグし、**[ActualTemp]** と **[TargetTemp]** の各フィールドを**値**にドラッグします。

        ![Apache Spark BI を使用して Spark データ視覚化を作成](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Apache Spark BI を使用して Spark データ視覚化を作成")

    2. 既定では、**ActualTemp** および **TargetTemp** の合計が表示されます。 両フィールドでドロップダウンから **[平均]** を選択して、各ビルの実際の温度と目標温度の平均を表示します。

        ![Apache Spark BI を使用して Spark データ視覚化を作成](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Apache Spark BI を使用して Spark データ視覚化を作成")

    3. 次のスクリーンショットのようにデータが視覚化されます。 グラフの上にカーソルを移動すると、関連データを含むツール ヒントが表示されます。

        ![Apache Spark BI を使用して Spark データ視覚化を作成](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Apache Spark BI を使用して Spark データ視覚化を作成")

11. **[ファイル]**、**[保存]** の順にクリックして、ファイル名 `spark.pbix` を入力します。 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Power BI サービスにレポートを発行する (省略可能)
Power BI Desktop で完全なレポートが作成できたのでここでやめることもできますが、多くの人が Power BI サービスを活用したいと考えています。それにより、組織全体でレポートやダッシュボードが共有しやすくなるためです。 

このセクションでは、作成した Power BI Desktop ファイルに含まれているデータセットとレポートを発行します。 その後、レポートからダッシュボードに視覚エフェクトをピン留めします。 通常、ダッシュボードは、レポート内のデータのサブセットにフォーカスするために使用します。レポート内にある視覚エフェクトは 1 つのみですが、それでも手順を実行するのに役立ちます。

1. Power BI Desktop の **[ホーム]** タブで、**[発行]** をクリックします。

    ![Power BI Desktop から発行](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Power BI Desktop から発行")

2. データセットを発行してレポートするワークスペースを選択して、**[選択]** をクリックします。 次の図では、既定値の **[マイ ワークスペース]** が選択されています。

    ![データセットを発行してレポートするワークスペースを選択する](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "データセットを発行してレポートするワークスペースを選択する") 

3. Power BI Desktop が成功メッセージで戻ったら、**[Open 'spark.pbix' in Power BI]\(Power BI で 'spark.pbix' を開く\)** をクリックします。

    ![発行の成功、クリックして資格情報を入力](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "発行の成功、クリックして資格情報を入力") 

4. Power BI サービスで、**[資格情報を入力する]** をクリックします。

    ![Power BI サービスで資格情報を入力する](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Power BI サービスで資格情報を入力する")

5. **[資格情報を編集]** をクリックします。

    ![Power BI サービスで資格情報を編集する](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Power BI サービスで資格情報を編集する")

6. HDInsight ログイン アカウントの情報を入力して (通常 Power BI Desktop で使用される既定の `admin` アカウント)、**[サインイン]** をクリックします。

    ![Spark クラスターへのサインイン](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Spark クラスターへのサインイン")

7. 左ウィンドウ枠で **[ワークスペース]** > **[マイ ワークスペース]** > **[レポート]** の順に移動して、**[Spark]** をクリックします。

    ![左ウィンドウ枠の [レポート] に表示されるレポート](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "左ウィンドウ枠の [レポート] に表示されるレポート")

    左ウィンドウ枠の **[データセット]** にも **[Spark]** が表示されます。

8. これで、Power BI Desktop で作成したビジュアルがサービスで使用できるようになりました。 このビジュアルをダッシュボードにピン留めするには、ビジュアルをポイントしてピン アイコンをクリックします。

    ![Power BI サービスのレポート](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Power BI サービスのレポート")

9. [新しいダッシュボード] を選択して、名前 `SparkDemo` を入力し、**[ピン留め]** をクリックします。

    ![新しいダッシュボードにピン留めする](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "新しいダッシュボードにピン留めする")

10. レポートで、**[ダッシュボードへ移動]** をクリックします。 

    ![ダッシュボードへ移動](./media/apache-spark-use-bi-tools/apache-spark-bi-open-dashboard.png "ダッシュボードへ移動")

ビジュアルはダッシュボードにピン留めされます。他のビジュアルをレポートに追加して、同じダッシュボードにピン留めすることもできます。 レポートとダッシュボードの詳細については、「[Power BI のレポート](https://powerbi.microsoft.com/documentation/powerbi-service-reports/)」および [Power BI のダッシュボード](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)に関する記事をご覧ください。

## <a name="tableau"></a>Spark データ視覚化のための Tableau Desktop の使用

> [!NOTE]
> このセクションは、Azure HDInsight で作成された Spark 1.5.2 クラスターにのみ適用できます。
>
>

1. この Apache Spark BI チュートリアルを行っているコンピューターに [Tableau Desktop](http://www.tableau.com/products/desktop) をインストールします。

2. そのコンピューターに Microsoft Spark ODBC ドライバーもインストールされていることを確認します。 ドライバーは [ここ](http://go.microsoft.com/fwlink/?LinkId=616229)からインストールできます。

1. Tableau Desktop を起動します。 左側のウィンドウの接続先サーバー一覧で **[Spark SQL]**をクリックします。 Spark SQL が既定で左側のウィンドウに表示されない場合は、 **[その他のサーバー]**をクリックして検索できます。
2. Spark SQL 接続ダイアログ ボックスに、次のスクリーンショットのように値を指定して **[OK]** をクリックします。

    ![Apache Spark BI のクラスターに接続](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Apache Spark BI のクラスターに接続")

    **Microsoft Spark ODBC** ドライバーをコンピューターにインストールしてある場合にのみ、認証ドロップダウンに [[Microsoft Azure HDInsight サービス]](http://go.microsoft.com/fwlink/?LinkId=616229) がオプションとして表示されます。
3. 次の画面で、**[スキーマ]** ドロップダウンの **[検索]** アイコンをクリックし、**[デフォルト]** をクリックします。

    ![Apache Spark BI のスキーマを検索](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Apache Spark BI のスキーマを検索")
4. **[テーブル]** フィールドで **[検索]** アイコンをクリックし、クラスターで使用可能なすべての Hive テーブルを一覧表示します。 Notebook を使用して前に作成した **hvac** テーブルが表示されます。

    ![Apache Spark BI のテーブルを検索](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Apache Spark BI のテーブルを検索")
5. テーブルをドラッグして右側上部のボックスにドロップします。 Tableau はデータをインポートし、赤い四角で強調表示されているようにスキーマを表示します。

    ![Apache Spark BI の Tableau にデータを追加](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Apache Spark BI の Tableau にデータを追加")
6. 左下の **[Sheet1]** タブをクリックします。 すべてのビルの各日の目標温度と実際の温度の平均を表示するグラフを作成します。 **[日付]** と **[ビル ID]** を **[列]** に、**[実際の温度]**/**[目標温度]** を **[行]** にドラッグします。 **[マーク]** で **[領域]** を選択して、Spark データ視覚化で領域マップを使用します。

     ![Spark データ視覚化のフィールドを追加](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Spark データ視覚化のフィールドを追加")
7. 既定では、温度フィールドは集計として表示されます。 代わりに平均温度を表示する場合は、次のようにしてドロップダウンから行うことができます。

    ![Spark データ視覚化の平均値の取得](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Spark データ視覚化の平均値の取得")

8. 一方の温度を他の温度にスーパーインポーズして、温度の違いを見やすくすることもできます。 マウスを下部領域マップの隅に移動し、赤い丸で囲んだハンドル形状にします。 マップを上部の他のマップにドラッグし、マウスが赤い四角で囲んだ形状になったら放します。

    ![Spark データ視覚化のマップをマージ](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Spark データ視覚化のマップをマージ")

     データの視覚化が、次のスクリーンショットのように変化します。

    ![Spark データ視覚化の Tableau 出力](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Spark データ視覚化の Tableau 出力")
9. **[保存]** をクリックしてワークシートを保存します。 ダッシュボードを作成して 1 つまたは複数のシートを追加できます。

## <a name="next-steps"></a>次のステップ

これまでに、クラスターを作成し、データを照会するための Spark データ フレームを作成し、BI ツールからそのデータにアクセスする方法を学習しました。 次は、クラスターのリソースを管理し、HDInsight Spark クラスターで実行されているジョブをデバッグする方法を見ていきましょう。

* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)

