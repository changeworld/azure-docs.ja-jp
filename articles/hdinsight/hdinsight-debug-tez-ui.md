<properties
pageTitle="Windows ベースの HDInsight で Tez UI を使用する | Azure"
description="Windows ベースの HDInsight で Tez UI を使用して Tez ジョブをデバッグする方法について説明します。"
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="02/16/2016"
ms.author="larryfr"/>

# Windows ベースの HDInsight で Tez UI を使用して Tez ジョブをデバッグする

Tez UI は、Windows ベースの HDInsight クラスターで実行エンジンとして Tez を使用するジョブの確認とデバッグに使用できる Web ページです。Tez UI を使用すると、関連付けられた項目のグラフとしてジョブを可視化し、各項目をドリルダウンして、統計情報やログ情報を取得することができます。

> [AZURE.NOTE] このドキュメントの情報は、Windows ベースの HDInsight クラスターに固有のものです。Linux ベースの HDInsight で Tez を表示してデバッグする方法については、「[HDInsight で Ambari ビューを使用して Tez ジョブをデバッグする](hdinsight-debug-ambari-tez-view.md)」を参照してください。

##前提条件

* Windows ベースの HDInsight クラスター。新しいクラスターの作成手順については、「[Hadoop チュートリアル: Windows 上の HDInsight で Hadoop を使用する](hdinsight-hadoop-tutorial-get-started-windows.md)」を参照してください。

    > [AZURE.IMPORTANT] Tez UI は、2016 年 2 月 8 日より後に作成された Windows ベースの HDInsight クラスターでのみ利用できます。

* Windows ベースのリモート デスクトップ クライアント。

##Tez について

Tez は、Hadoop でデータを処理するための拡張可能なフレームワークで、処理速度が従来の MapReduce よりも向上します。Windows ベースの HDInsight クラスターの場合、Tez は任意のエンジンです。これは、Hive クエリの一部として次のコマンドを使用することにより、Hive に対して有効にすることができます。

    set hive.execution.engine=tez;

Tez に処理が送信されると、ジョブで必要なアクションの実行順序を記述された有向非巡回グラフ (DAG) が作成されます。個々のアクションは頂点と呼ばれ、ジョブ全体の一部分を実行します。頂点によって表される処理を実際に実行することはタスクと呼ばれ、クラスター内の複数のノードに分散される場合があります。

###Tez UI について

Tez UI は、Tez を使用して実行されているプロセスや過去に実行されたプロセスに関する情報が表示される Web ページです。これを使用すると、Tez によって生成された DAG、DAG がクラスター間でどのように分散されているか、タスクや頂点で使用されるメモリなどのカウンター、エラー情報を表示できます。次のシナリオで役立つ情報が提供される場合があります。

* Map タスクと Reduce タスクの進行状況を表示して、実行時間の長いプロセスの監視する。

* 成功または失敗したプロセスの履歴データを分析して、処理の改善方法や失敗した理由を確認する。

##DAG の生成

Tez UI には、Tez エンジンを使用するジョブが現在実行中か過去に実行されていた場合にのみデータが表示されます。単純な Hive クエリは通常 Tez を使用しなくても解決できますが、フィルター処理、グループ化、順序付け、結合などを実行する複雑なクエリでは、通常 Tez が必要です。

Tez を使用して実行される Hive クエリを実行するには、次の手順に従います。

1. Web ブラウザーで、https://CLUSTERNAME.azurehdinsight.net に移動します。ここでは、__CLUSTERNAME__ は HDInsight クラスターの名前です。

2. ページの上部にあるメニューの __[Hive エディター]__ を選択します。これにより、次のサンプルのクエリを含むページが表示されます。

        Select * from hivesampletable

    サンプルのクエリを削除し、次のクエリに置き換えます。

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

3. __[送信]__ ボタンを選択します。ページの下部にある __[ジョブ セッション]__ セクションに、クエリの状態が表示されます。状態が __[完了]__ に変わったら、__[詳細の表示]__ リンクを選択して結果を表示します。__ジョブの出力__は次のようになります。
        
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

##Tez UI の使用

> [AZURE.NOTE] Tez UI はクラスターのヘッド ノードのデスクトップからしか利用できないため、リモート デスクトップを使用してヘッド ノードに接続する必要があります。

1. [Azure ポータル](https://portal.azure.com)で HDInsight クラスターを選択します。[HDInsight] ブレードの上部にある __[リモート デスクトップ]__ アイコンを選択します。リモート デスクトップのブレードが表示されます。

    ![Remote desktop icon](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)

2. [リモート デスクトップ] ブレードで、__[接続]__ を選択してクラスターのヘッド ノードに接続します。メッセージが表示されたら、クラスターのリモート デスクトップ ユーザー名とパスワードを使用して接続を認証します。

    ![Remote desktop connect icon](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

    > [AZURE.NOTE] リモート デスクトップ接続を有効にしていない場合は、ユーザー名、パスワード、有効期限を指定し、__[有効にする]__ を選択してリモート デスクトップを有効にします。リモート デスクトップが有効になったら、前の手順に従って接続します。

3. 接続後、リモート デスクトップで Internet Explorer を開き、ブラウザーの右上隅にある歯車アイコンを選択して、__[互換表示設定]__ を選択します。

4. __[互換表示設定]__ の下部で、__[イントラネット サイトを互換表示で表示する]__ チェック ボックスと __[Microsoft 互換性リストの使用]__ チェック ボックスをオフにし、__[閉じる]__ を選択します。

5. Internet Explorer で、http://headnodehost:8188/tezui/#/ を参照します。これで Tez UI が表示されます。

    ![Tez UI](./media/hdinsight-debug-tez-ui/tezui.png)

    Tez UI が読み込まれると、クラスターで現在実行されている DAG や実行されていた DAG の一覧が表示されます。既定のビューには、DAG 名、ID、送信者、状態、開始時刻、終了時刻、期間、アプリケーション ID、キューが表示されます。ページの右側にある歯車アイコンを使用すると、列をさらに追加できます。

    エントリが 1 つしかない場合、それは、前のセクションで実行したクエリのエントリになります。エントリが複数ある場合は、DAG の上にあるフィールドで検索条件を入力して検索してから、__Enter__ キーを押します。

4. 最新の DAG エントリの __DAG 名__を選択します。選択すると、その DAG に関する情報のほか、DAG に関する情報が含まれる JSON ファイルの zip ファイルをダウンロードするオプションが表示されます。

    ![DAG Details](./media/hdinsight-debug-tez-ui/dagdetails.png)

5. __[DAG Details]__ の上にはいくつかのリンクがあります。これらのリンクを使用すると、DAG に関する情報を表示できます。

    * __[DAG Counters]__ をクリックすると、この DAG のカウンターの情報が表示されます。
    
    * __[Graphical View]__ をクリックすると、この DAG がグラフィカルに表示されます。
    
    * __[All Vertices]__ をクリックすると、この DAG 内の頂点の一覧が表示されます。
    
    * __[All Tasks]__ をクリックすると、この DAG 内のすべての頂点のタスクの一覧が表示されます。
    
    * __[All TaskAttempts]__ をクリックすると、この DAG のタスクの実行に関する情報が表示されます。
    
    > [AZURE.NOTE] [Vertices]、[Tasks]、[TaskAttempts] が表示されるように列をスクロールする場合は、行ごとに__カウンター__を表示するためのリンクと__ログを表示またはダウンロードする__ためのリンクがあることに注意してください。

    ジョブでエラーが発生した場合、[DAG Details] には、[FAILED] という状態と、失敗したタスクに関する情報へのリンクが表示されます。診断情報は、DAG の詳細の下に表示されます。

7. __[Graphical View]__ を選択します。これにより、DAG がグラフィカルに表示されます。ビューの各頂点にマウス ポインターを置くと、その項目に関する情報を表示できます。

    ![Graphical view](./media/hdinsight-debug-tez-ui/dagdiagram.png)

8. 頂点をクリックすると、その項目の __[Vertex Details]__ が読み込まれます。__Map 1__ の頂点をクリックして、この項目の詳細を表示します。__[Confirm]__ を選択してナビゲーションを確認します。

    ![Vertex details](./media/hdinsight-debug-tez-ui/vertexdetails.png)

9. これで、ページの上部に頂点とタスクに関連するリンクが表示されます。

    > [AZURE.NOTE] __[DAG Details]__ に戻り、__[Vertex Details]__ を選択して、__Map 1__ の頂点を選択することで、このページに移動することもできます。

    * __[Vertex Counters]__ をクリックすると、この頂点のカウンター情報が表示されます。
    
    * __[Tasks]__ をクリックすると、この頂点のタスクが表示されます。
    
    * __[Task Attempts]__ をクリックすると、この頂点のタスクの実行に関する情報が表示されます。
    
    * __[Sources & Sinks]__ をクリックすると、この頂点のデータ ソースとシンクが表示されます。

    > [AZURE.NOTE] 前のメニューと同様に、[Tasks]、[Task Attempts]、[Sources & Sinks\_\_] が表示されるように列をスクロールすると、各項目の詳細情報へのリンクを表示できます。

10. __[Tasks]__ を選択した後、__00\_000000__ という名前の項目をクリックします。このタスクの __[Task Details]__ が表示されます。この画面から、__[Task Counters]__ と __[Task Attempts]__ を表示できます。

    ![タスクの詳細](./media/hdinsight-debug-tez-ui/taskdetails.png)

##次のステップ

ここでは、Tez ビューの使用方法を学習できました。詳細については、「[HDInsight で Hadoop と共に Hive と HiveQL を使用して Apache log4j サンプル ファイルを分析する](hdinsight-use-hive.md)」を参照してください。

Tez に関する技術的な情報の詳細については、[Hortonworks の Tez に関するページ](http://hortonworks.com/hadoop/tez/)を参照してください。

<!---HONumber=AcomDC_0218_2016-->