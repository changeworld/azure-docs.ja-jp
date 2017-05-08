---
title: "HDInsight で Ambari Tez ビューを使用する | Microsoft Docs"
description: "HDInsight で Ambari Tez ビューを使用して Tez ジョブをデバッグする方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 9c39ea56-670b-4699-aba0-0f64c261e411
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 08dac5205e50dd5c33d71ba15277da66fd7b22fe
ms.lasthandoff: 04/18/2017


---
# <a name="use-ambari-views-to-debug-tez-jobs-on-hdinsight"></a>HDInsight で Ambari ビューを使用して Tez ジョブをデバッグする

HDInsight の Ambari Web UI には Tez ビューが含まれています。Tez ビューは、Tez を使用するジョブの確認とデバッグに使用できます。 Tez ビューを使用すると、関連付けられた項目のグラフとしてジョブを可視化し、各項目をドリルダウンして、統計情報やログ情報を取得することができます。

> [!IMPORTANT]
> このドキュメントの手順では、Linux を使用する HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[HDInsight コンポーネントのバージョン管理](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* Linux ベースの HDInsight クラスター。 クラスターの作成手順については、「[Hadoop チュートリアル: Linux 上の HDInsight で Hive と Hadoop を使用する](hdinsight-hadoop-linux-tutorial-get-started.md)」を参照してください。
* HTML5 をサポートする最新の Web ブラウザー

## <a name="understanding-tez"></a>Tez について

Tez は、Hadoop でデータを処理するための拡張可能なフレームワークで、処理速度が従来の MapReduce よりも向上します。 Linux ベースの HDInsight クラスターでは、これが Hive の既定のエンジンになります。

Tez は、ジョブで必要なアクションの順序を記述する有向非巡回グラフ (DAG) を作成します。 個々のアクションは頂点と呼ばれ、ジョブ全体の一部分を実行します。 頂点によって表される処理を実際に実行することはタスクと呼ばれ、クラスター内の複数のノードに分散される場合があります。

### <a name="understanding-the-tez-view"></a>Tez ビューについて

Tez ビューには、履歴情報と、実行中のプロセスに関する情報の両方が表示されます。 この情報は、ジョブがクラスター間でどのように分散されるかを示しています。 タスク、頂点、およびジョブに関連するエラー情報によって使用されるカウンターも表示されます。 次のシナリオで役立つ情報が提供される場合があります。

* Map タスクと Reduce タスクの進行状況を表示して、実行時間の長いプロセスの監視する。
* 成功または失敗したプロセスの履歴データを分析して、処理の改善方法や失敗した理由を確認する。

## <a name="generate-a-dag"></a>DAG の生成

Tez ビューには、Tez エンジンを使用するジョブが現在実行中か以前に実行されていた場合にのみデータが表示されます。 単純な Hive クエリは、Tez を使用しなくても解決できます。 フィルター処理、グループ化、並べ替え、結合などを行うより複雑なクエリは、 Tez エンジンを使用します。

Tez を使用する Hive クエリを実行するには、次の手順に従います。

1. Web ブラウザーで https://CLUSTERNAME.azurehdinsight.net に移動します。**CLUSTERNAME** は実際の HDInsight クラスターの名前です。

2. ページの上部にあるメニューの **[Views]** アイコンを選択します。 このアイコンは、四角形が並んだ形をしています。 表示されたドロップダウン リストで **[Hive View]** を選択します。

    ![Selecting Hive View](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Hive ビューが読み込まれたら、クエリ エディターに次のクエリを貼り付けて、**[Execute]** をクリックします。

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    ジョブが完了すると、**[Query Process Results]** セクションに出力が表示されることがわかります。 結果は次のテキストのようになります。

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica

4. **[Log]** タブを選択します。 次のテキストのような情報が表示されます。

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    **App id** 値を保存します。この値は次のセクションで使用します。

## <a name="use-the-tez-view"></a>Tez ビューの使用

1. ページの上部にあるメニューの **[Views]** アイコンを選択します。 表示されたドロップダウン リストで **[Tez View]** を選択します。

    ![Selecting Tez view](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Tez ビューが読み込まれると、クラスターで現在実行されている DAG や実行されていた DAG の一覧が表示されます。

    ![All DAGS](./media/hdinsight-debug-ambari-tez-view/alldags.png)

3. エントリが 1 つしかない場合、それは、前のセクションで実行したクエリのエントリです。 エントリが複数ある場合は、**[Application ID]** フィールドにアプリケーション ID を入力して検索してから、Enter キーを押します。

4. **DAG 名**を選択します。 DAG の情報が表示されます。 この情報が含まれている JSON ファイルの zip ファイルをダウンロードすることもできます。

    ![DAG Details](./media/hdinsight-debug-ambari-tez-view/dagdetails.png)

5. **[DAG Details]** の上にはいくつかのリンクがあります。これらのリンクを使用すると、DAG に関する情報を表示できます。

   * **[DAG Counters]**: この DAG のカウンターの情報が表示されます。
   * **[Graphical View]**: この DAG がグラフィカルに表示されます。
   * **[All Vertices]**: この DAG 内の頂点の一覧が表示されます。
   * **[All Tasks]**: この DAG 内のすべての頂点のタスクの一覧が表示されます。
   * **[All TaskAttempts]**: この DAG のタスクの実行に関する情報が表示されます。

     > [!NOTE]
     > [Vertices]、[Tasks]、[TaskAttempts] が表示されるように列をスクロールする場合は、行ごとに**カウンター**を表示するためのリンクと**ログを表示またはダウンロードする**ためのリンクがあることに注意してください。

     ジョブでエラーが発生した場合、[DAG Details] には、[FAILED] という状態と、失敗したタスクに関する情報へのリンクが表示されます。 診断情報は、DAG の詳細の下に表示されます。

     ![A DAG Details screen detailing a failure](./media/hdinsight-debug-ambari-tez-view/faileddag.png)

6. **[Graphical View]**を選択します。 このビューには、DAG がグラフィカルに表示されます。 ビューの各頂点にマウス ポインターを置くと、その項目に関する情報を表示できます。

    ![[Graphical View]](./media/hdinsight-debug-ambari-tez-view/dagdiagram.png)

7. 頂点を選択すると、その項目の **[Vertex Details]** が読み込まれます。 **Map 1** の頂点を選択して、この項目の詳細を表示します。

    ![[Vertex Details]](./media/hdinsight-debug-ambari-tez-view/vertexdetails.png)

8. これで、ページの上部に頂点とタスクに関連するリンクが表示されます。

   > [!NOTE]
   > **[DAG Details]** に戻り、**[Vertex Details]** を選択して、**Map 1** の頂点を選択することで、このページに移動することもできます。

   * **[Vertex Counters]**: この頂点のカウンター情報が表示されます。
   * **[Tasks]**: この頂点のタスクが表示されます。
   * **[Task Attempts]**: この頂点のタスクの実行に関する情報が表示されます。
   * **[Sources & Sinks]**: この頂点のデータ ソースとシンクが表示されます。

     > [!NOTE]
     > 前のメニューと同様に、[Tasks]、[Task Attempts]、[Sources & Sinks__] が表示されるように列をスクロールすると、各項目の詳細情報へのリンクを表示できます。

9. **[Tasks]** を選択した後、**00_000000** という名前の項目をクリックします。 このタスクの **[Task Details]** が表示されます。 この画面から、**[Task Counters]** と **[Task Attempts]** を表示できます。

   ![タスクの詳細](./media/hdinsight-debug-ambari-tez-view/taskdetails.png)

## <a name="next-steps"></a>次のステップ

ここでは、Tez ビューの使用方法を説明しました。詳細については、[HDInsight での Hive の使用](hdinsight-use-hive.md)に関する記事を参照してください。

Tez に関する技術的な情報の詳細については、 [Hortonworks の Tez に関するページ](http://hortonworks.com/hadoop/tez/)を参照してください。

HDInsight での Ambari の使用の詳細については、「[Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)

