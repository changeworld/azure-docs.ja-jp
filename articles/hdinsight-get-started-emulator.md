<properties linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Get Started" pageTitle="Get started with the HDInsight Emulator | Azure" metaKeywords="hdinsight, Azure hdinsight, hdinsight azure, get started hdinsight, emulator, hdinsight emulator" description="Learn how to use HDInsight Emulator for Azure." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" title="Get started with the HDInsight Emulator" author="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# HDInsight Emulator の概要

このチュートリアルでは、Microsoft HDInsight Emulator for Azure (旧 HDInsight サーバー開発者プレビュー) における Hadoop クラスターの概要を紹介します。HDInsight Emulator には、Azure の HDInsight と同じ Hadoop エコシステムのコンポーネントが付属します。デプロイされたバージョンの情報も含めて、詳細については、「[Azure HDInsight でサポートされている Hadoop のバージョン][]」を参照してください。

HDInsight Emulator は、Azure HDInsight 用のローカル開発環境を提供します。Hadoop に慣れている場合は、HDFS を使用して Emulator を使い始めることができます。しかし、HDInsight では、既定のファイル システムが Azure BLOB ストレージ (WASB: Azure Storage - BLOB) であるため、最終的には WASB を使用してジョブを開発することになります。Azure ストレージ エミュレーターを使用して WASB に対して開発を開始できます。ここでは、おそらくデータの一部しか使いません (HDInsight Emulator では構成の変更が不要で、ストレージ アカウント名を変えるだけで済みます)。次に、Windows Azure のストレージに対してローカルでジョブをテストします。今度もデータの一部だけを使用します (HDInsight Emulator で構成を変更する必要があります)。これで、ジョブの計算部分を HDInsight に移動して、運用データに対してジョブを実行する準備が整いました。

> [WACOM.NOTE] HDInsight Emulator では 1 つのノードでのデプロイのみが使用できます。

HDInsight を使用したチュートリアルについては、「[Azure の HDInsight の概要][]」を参照してください。

**前提条件**
このチュートリアルを開始する前に、以下の条件を満たしている必要があります。

-   HDInsight Emulator には 64 ビット版 Windows が必要です。次の要件のうち 1 つを満たしている必要があります。

    -   Windows 7 Service Pack 1
    -   Windows Server 2008 R2 Service Pack 1
    -   Windows 8
    -   Windows Server 2012
-   Azure PowerShell のインストールおよび構成。手順については、[Azure PowerShell のインストールおよび構成に関するページ][]を参照してください。

## このチュートリアルの内容

-   [HDInsight Emulator のインストール][]
-   [ワード カウント サンプルの実行][]
-   [入門サンプルの実行][]
-   [Azure BLOB ストレージへの接続][]
-   [HDInsight PowerShell の実行][]
-   [次のステップ][]

## <a name="install"></a>HDInsight Emulator のインストール

Microsoft HDInsight Emulator は、Microsoft Web Platform Installer を使用してインストールします。

> [WACOM.NOTE] 現時点で HDInsight Emulator がサポートしているのは英語版 OS のみです。

> [WACOM.NOTE] Microsoft HDInsight 開発者プレビューをインストールしている場合は、まず、次の 2 つのコンポーネントを [コントロール パネル] の [プログラムと機能] からアンインストールする必要があります。
>
> -   HDInsight 開発者プレビュー
> -   Hortonworks Data Platform 開発者プレビュー
>
> </p>

**HDInsight Emulator をインストールするには**

1.  Internet Explorer を開き、[Microsoft HDInsight Emulator for Azure インストール ページ][]にアクセスします。
2.  **[今すぐインストール]** をクリックします。
3.  ページ下部に HDINSIGHT.exe のインストールに関するメッセージが表示されたら、**[実行]** をクリックします。
4.  **[ユーザー アカウント制御]** ウィンドウで **[はい]** をクリックして、インストールを完了します。[Web Platform Installer 4.6] ウィンドウが表示されます。
5.  ページの下部にある **[インストール]** をクリックします。
6.  **[同意する]** をクリックして、ライセンス条項に同意します。
7.  Web Platform Installer に **[次の製品が正常にインストールされました]** と表示されるのを確認して、**[完了]** をクリックします。
8.  **[終了]** をクリックして [Web Platform Installer 4.6] ウィンドウを閉じます。

    インストールが終わると、デスクトップにアイコンが 3 つインストールされます。3 つのアイコンは次のようにリンクされています。

    -   **Hadoop コマンド ライン**: HDInsight Emulator で MapReduce、Pig、および Hive のジョブを実行する Hadoop コマンド プロンプト。

    -   **Hadoop 名前ノードのステータス**: NameNode は HDFS のすべてのファイルを含むツリー式ディレクトリを維持します。また、Hadoop クラスターに保持されたすべてのファイルのデータの場所も追跡されます。クライアントは、すべてのファイルのデータ ノードがどこに格納されているか見つけるために、NameNode と通信します。

    -   **Hadoop MapReduce のステータス**: クラスターのノードに MapReduce タスクを配分するジョブ トラッカー。

    インストールが終わると、いくつかのローカル サービスもインストールされます。[サービス] ウィンドウのスクリーンショットは次のようになります。

    ![HDI.Emulator.Services][]

    HDInsight Server のインストールと実行に関する既知の問題については、[HDInsight Emulator のリリース ノート][]を参照してください。インストール ログは **C:\\HadoopFeaturePackSetup\\HadoopFeaturePackSetupTools\\gettingStarted.winpkg.install.log** にあります。

## <a name="runwordcount"></a>ワード カウント MapReduce ジョブの実行

これで使っているコンピューター上に HDInsight Emulator を構成しました。MapReduce ジョブを実行して、正しくインストールされているかテストできます。まず、HDFS にテキスト ファイルをいくつかアップロードして、そのファイルに登場する単語の頻度を計算するワード カウント MapReduce ジョブを実行します。

ワード カウント MapReduce プログラムは *hadoop-examples.jar* にパッケージ化されています。jar ファイルは *C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT* フォルダーにあります。

jar コマンドの構文は次のとおりです。

    hadoop jar <jar> [mainClass] args...

また、いくつかの fs コマンドも使用します。Hadoop コマンドの詳細については、[Hadoop コマンド マニュアル][]を参照してください。

ワード カウント MapReduce ジョブは、入力フォルダーと出力フォルダーの 2 つの引数をとります。*hdfs://localhost/user/HDIUser* を入力フォルダーとして、*hdfs://localhost/user/HDIUser/WordCount\_Output* を出力ディレクトリとして使用します。出力フォルダーは既存のフォルダーにすることはできません。既存のフォルダーの場合、MapReduce ジョブは失敗します。2 回目に MapReduce ジョブを実行する場合は、別の出力フォルダーを指定するか、既存の出力フォルダーを削除します。

**ワード カウント MapReduce ジョブを実行するには**

1.  デスクトップで **[Hadoop Command Line]** をダブルクリックして、Hadoop コマンド ライン ウィンドウを開きます。現在のフォルダーは次のようになっているはずです。

        c:\Hadoop\hadoop-1.1.0-SNAPSHOT>

    そうでない場合は、次のコマンドを実行します。

        cd %hadoop_home%

2.  次の Hadoop コマンドを実行して、入出力ファイルを格納する HDFS フォルダーを作成します。

        hadoop fs -mkdir /user/HDIUser

3.  次の Hadoop コマンドを実行して、ローカル ファイルを HDFS にコピーします。

        hadoop fs -copyFromLocal *.txt /user/HDIUser/

4.  次のコマンドを実行して、/user/HDIUser フォルダーにあるファイルを一覧表示します。

        hadoop fs -ls /user/HDIUser

    次のようにファイルが表示されます。

        c:\Hadoop\hadoop-1.1.0-SNAPSHOT>hadoop fs -ls /user/HDIUser
        Found 8 items
        -rw-r--r--   1 username supergroup      16372 2013-10-30 12:07 /user/HDIUser/CHANGES.branch-1-win.txt
        -rw-r--r--   1 username supergroup     463978 2013-10-30 12:07 /user/HDIUser/CHANGES.txt
        -rw-r--r--   1 username supergroup       6631 2013-10-30 12:07 /user/HDIUser/Jira-Analysis.txt
        -rw-r--r--   1 username supergroup      13610 2013-10-30 12:07 /user/HDIUser/LICENSE.txt
        -rw-r--r--   1 username supergroup       1663 2013-10-30 12:07 /user/HDIUser/Monarch-CHANGES.txt
        -rw-r--r--   1 username supergroup        103 2013-10-30 12:07 /user/HDIUser/NOTICE.txt
        -rw-r--r--   1 username supergroup       2295 2013-10-30 12:07 /user/HDIUser/README.Monarch.txt
        -rw-r--r--   1 username supergroup       1397 2013-10-30 12:07 /user/HDIUser/README.txt

5.  次のコマンドを実行して、ワード カウント MapReduce ジョブを実行します。

        hadoop jar hadoop-examples.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6.  次のコマンドを実行して、出力ファイルから "windows" を含む単語を表示します。

        hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

    出力は次のようになります。

        c:\Hadoop\hadoop-1.1.0-SNAPSHOT>hadoop fs -cat /user/HDIUser/WordCount_Output/pa
        rt-r-00000 | findstr "windows"
        windows 12
        windows+java6.  1
        windows.        3

## <a name="rungetstartedsamples"></a> 入門サンプルの実行

HDInsight Emulator をインストールすると、Windows 上の Apache Hadoop ベースのサービスについて新しいユーザーがすばやく学習できるサンプルもインストールされます。これらのサンプルは、通常、ビッグ データ セットを処理する際に必要なタスクを対象としています。サンプルを試すと、MapReduce プログラミング モデルとそのエコシステムに関連した概念に慣れることができます。

サンプルは、処理する IIS W3C ログ データ シナリオを軸として整理されています。データ生成ツールが用意されていて、さまざまなサイズのデータ セットを HDFS または WASB (Azure BLOB ストレージ) に作成しインポートできます。詳細については、「[HDInsight での Azure BLOB ストレージの使用][]」を参照してください。その後、PowerShell スクリプトによって生成されたデータのページ上で MapReduce、Pig または Hive ジョブを実行できます。使用する Pig スクリプトと Hive スクリプトはどちらも、コンパイルすると MapReduce プログラムになります。ユーザーは一連のジョブを実行して、これらの異なる技術を使用する効果と、処理タスクの実行にデータ サイズが及ぼす影響を自分の目で確認することができます。

### このセクションの内容

-   [IIS W3C ログ データのシナリオ][]
-   [サンプル W3C ログ データの読み込み][]
-   [Java MapReduce ジョブの実行][]
-   [Hive ジョブの実行][]
-   [Pig ジョブの実行][]
-   [サンプルのリビルド][]

### <a name="scenarios"></a> IIS W3C ログ データのシナリオ

W3C シナリオは、IIS W3C ログ データを 3 つのサイズ (1 MB、500 MB、2 GB) で生成し、HDFS または WASB にインポートします。3 種類のジョブがあり、それぞれを C#、Java、Pig、および Hive で実装します。

-   **totalhits**: 所定のページに対する要求の総数を計算します。
-   **avgtime**: ページあたりの要求にかかかった平均時間 (秒単位) を計算します。
-   **errors**: 状態が 404 または 500 であった要求について、ページあたり、時間あたりのエラー数を計算します。

これらのサンプルとそのドキュメントは、主要な Hadoop テクノロジを詳細に調査したり全面的に実装したりするものではありません。使用するクラスターはノードを 1 つしかもたないため、ノードを増やす効果は、今回のリリースでは確認できません。

### <a name="loaddata"></a>サンプル W3C ログ データの読み込み

PowerShell スクリプトの importdata.ps1 を使用して、データを生成し HDFS にインポートします。

**サンプル W3C ログ データをインポートするには**

1.  デスクトップから Hadoop コマンド ラインを開きます。
2.  次のコマンドを実行して、ディレクトリを **C:\\Hadoop\\GettingStarted** に変更します。

        cd \Hadoop\GettingStarted

3.  次のコマンドを実行して、データを生成し HDFS にインポートします。

        powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted 

    代わりに WASB にデータを読み込む場合は、[Azure BLOB ストレージへの接続][]を参照してください。

4.  Hadoop コマンド ラインから次のコマンドを実行して、HDFS にインポートしたファイルの一覧を表示します。

        hadoop fs -lsr /w3c

    出力は次のようになります。

        c:\Hadoop\GettingStarted\w3c>hadoop fs -lsr /w3c
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:29 /w3c/input
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:29 /w3c/input/large
        -rw-r--r--   1 username supergroup  543692369 2013-10-30 13:29 /w3c/input/large/data_w3c_large.txt
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:28 /w3c/input/medium
        -rw-r--r--   1 username supergroup  272394671 2013-10-30 13:28 /w3c/input/medium/data_w3c_medium.txt
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:28 /w3c/input/small
        -rw-r--r--   1 username supergroup    1058328 2013-10-30 13:28 /w3c/input/small/data_w3c_small.txt

5.  次のコマンドを実行して、データ ファイルの 1 つの内容をコンソール ウィンドウに表示します。

        hadoop fs -cat /w3c/input/small/data_w3c_small.txt

これでデータ ファイルが作成されて HDFS にインポートされました。別の Hadoop ジョブを実行できます。

### <a name="javamapreduce"></a>Java MapReduce ジョブの実行

MapReduce は Hadoop の基本的計算エンジンです。既定で Java で実装されていますが、C# を使用する .NET および Hadoop Streaming を活用した例もあります。MapReduce ジョブを実行する構文は次のとおりです。

    hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

jar ファイルとソース ファイルは C:\\Hadoop\\GettingStarted\\Java フォルダーにあります。

**Web ページのヒット数を計算する MapReduce ジョブを実行するには**

1.  Hadoop コマンド ラインを開きます。
2.  次のコマンドを実行して、ディレクトリを **C:\\Hadoop\\GettingStarted** に変更します。

        cd \Hadoop\GettingStarted

3.  次のコマンドを実行して、出力ディレクトリが存在している場合は削除します。出力ディレクトリが既に存在していると、MapReduce ジョブは失敗します。

        hadoop fs -rmr /w3c/output

4.  次のコマンドを実行します。

        hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

    コマンドの要素を次の表に示します。
    <table border="1">
    <tr><th>パラメーター</th><th>注</th></tr>
    <tr><td>w3c\_scenarios.jar</td><td>この jar ファイルは C:\\Hadoop\\GettingStarted\\Java フォルダーにあります。</td></tr>
    <tr><td>microsoft.hadoop.w3c.TotalHitsForPage</td><td>型は、次のどちらかに置き換えることができます。
    <ul>
	<li>microsoft.hadoop.w3c.AverageTimeTaken</li>
    <li>microsoft.hadoop.w3c.ErrorsByPage</li>
    </ul></td></tr>
    <tr><td>/w3c/input/small/data\_w3c\_small.txt</td><td>入力ファイルは、次のどちらかに置き換えることができます。
     <ul>
	<li>/w3c/input/medium/data\_w3c\_medium.txt</li>
    <li>/w3c/input/large/data\_w3c\_large.txt</li>
    </ul></td></tr>
    <tr><td>/w3c/output</td><td>これは、出力フォルダー名です。</td></tr>
    </table>

5.  次のコマンドを実行して、出力ファイルの内容を表示します。

        hadoop fs -cat /w3c/output/part-00000

    次のように出力されます。

        c:\Hadoop\GettingStarted\Java>hadoop fs -cat /w3c/output/part-00000
        /Default.aspx   3409
        /Info.aspx      1115
        /UserService    1130

    これで、たとえば Default.aspx ページは 3409 ヒットということがわかります。

### <a name="hive"></a>Hive ジョブの実行

Hive クエリ エンジンは、SQL の達人であれば戸惑うことはありません。SQL と同様のインターフェイスと HDFS のリレーショナル データ モデルを提供します。Hive では、HiveQL (または HQL) と呼ばれる言語を使用します。これは SQL の方言です。

**Hive ジョブを実行するには**

1.  Hadoop コマンド ラインを開きます。
2.  ディレクトリを **C:\\Hadoop\\GettingStarted** に変更します。
3.  次のコマンドを実行して、**/w3c/hive/input** フォルダーが存在している場合は削除します。このフォルダーが存在していると、Hive ジョブは失敗します。

        hadoop fs -rmr /w3c/hive/input

4.  次のコマンドを実行して、**/w3c/hive/input** フォルダーを作成し、データ ファイルをコンピューターから HDFS にコピーします。

        hadoop fs -mkdir /w3c/hive/input
        hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5.  次のコマンドを実行して、**w3ccreate.hql** スクリプト ファイルを実行します。このスクリプトは Hive テーブルを作成し、データを Hive テーブルに読み込みます。

        C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

    HiveQL スクリプトの内容は次のとおりです。

        DROP TABLE w3c;

        CREATE TABLE w3c(
         logdate string,
         logtime string,
         c_ip string,
         cs_username string,
         s_ip string,
         s_port string,
         cs_method string,
         cs_uri_stem string,
         cs_uri_query string,
         sc_status int,
         sc_bytes int,
         cs_bytes int,
         time_taken int,
         cs_agent string, 
         cs_Referrer string)
        ROW FORMAT delimited
        FIELDS TERMINATED BY ' ';

        LOAD DATA INPATH '${hiveconf:input}' OVERWRITE INTO TABLE w3c;

    次のように出力されます。

        c:\Hadoop\GettingStarted>C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ccrea    te.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"
        Hive history file=c:\hadoop\hive-0.9.0\logs\history/hive_job_log_username_201310311452_1053491002.txt
        Logging initialized using configuration in file:/C:/Hadoop/hive-0.9.0/conf/hive-log4j.properties
        OK
        Time taken: 0.616 seconds
        OK
        Time taken: 0.139 seconds
        Loading data to table default.w3c
        Moved to trash: hdfs://localhost:8020/apps/hive/warehouse/w3c
        OK
        Time taken: 0.573 seconds

6.  次のコマンドを実行して、**w3ctotalhitsbypate.hql** HiveQL スクリプト ファイルを実行します。

        C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

    コマンドの要素を次の表に示します。
    <table border="1">
    <tr><td>ファイル</td><td>説明</td></tr>
    <tr><td>C:\\Hadoop\\hive-0.9.0\\bin\\hive.cmd</td><td>Hive コマンド スクリプト。</td></tr>
    <tr><td>C:\\Hadoop\\GettingStarted\\Hive\\w3c\\w3ctotalhitsbypage.hql</td><td>Hive スクリプト ファイルは、次のいずれかに置き換えることができます。
    <ul><li>C:\\Hadoop\\GettingStarted\\Hive\\w3c\\w3caveragetimetaken.hql</li>
    <li>C:\\Hadoop\\GettingStarted\\Hive\\w3c\\w3cerrorsbypage.hql</li></ul></td></tr>
    </table>

    w3ctotalhitsbypage.hql HiveQL スクリプトの内容は次のとおりです。

        SELECT filtered.cs_uri_stem,COUNT(*) 
        FROM (
          SELECT logdate,cs_uri_stem from w3c WHERE logdate NOT RLIKE '.*#.*'
        ) filtered
        GROUP BY (filtered.cs_uri_stem);

    出力の最後は次のようになります。

        MapReduce Total cumulative CPU time: 3 seconds 47 msec
        Ended Job = job_201310291309_0006
        MapReduce Jobs Launched:
        Job 0: Map: 1  Reduce: 1   Cumulative CPU: 3.047 sec   HDFS Read: 1058546 HDFS W
        rite: 53 SUCCESS
        Total MapReduce CPU Time Spent: 3 seconds 47 msec
        OK
        /Default.aspx   3409
        /Info.aspx      1115
        /UserService    1130
        Time taken: 34.68 seconds

各ジョブの最初の手順として、テーブルが作成され、先に作成したファイルからそのテーブルにデータが読み込まれています。作成されたファイルは、次のコマンドを使用して HDFS の /Hive ノードの下を探すと見つかります。

    hadoop fs -lsr /apps/hive/

### <a name="pig"></a>Pig ジョブの実行

Pig 処理では、*Pig Latin* と呼ばれるデータ フロー言語が使用されます。Pig Latin 抽象化は MapReduce より高度なデータ構造を提供し、SQL で RDBMS システムを操作する場合と同じように Hadoop を操作できます。

**Pig ジョブを実行するには**

1.  Hadoop コマンド ラインを開きます。
2.  ディレクトリを C:\\Hadoop\\GettingStarted に変更します。
3.  次のコマンドを実行して、Pig ジョブを送信します。

        C:\Hadoop\pig-0.9.3-SNAPSHOT\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

    コマンドの要素を次の表に示します。
    <table border="1">
    <tr><th>ファイル</th><th>説明</th></tr>
    <tr><td>C:\\Hadoop\\pig-0.9.3-SNAPSHOT\\bin\\pig.cmd</td><td>Pig コマンド スクリプト。</td></tr>
    <tr><td>C:\\Hadoop\\GettingStarted\\Pig\\w3c\\TotalHitsForPage.pig</td><td>Pig Latin スクリプト ファイルは、次のいずれかに置き換えることができます。
    <ul><li>C:\\Hadoop\\GettingStarted\\Pig\\w3c\\AverageTimeTaken.pig</li>
    <li>C:\\Hadoop\\GettingStarted\\Pig\\w3c\\ErrorsByPage.pig</li></ul></td></tr>
    <tr><td>/w3c/input/small/data\_w3c\_small.txt</td><td>このパラメーターはもっと大きなファイルに置き換えることができます。
    <ul><li>/w3c/input/medium/data\_w3c\_medium.txt</li>
    <li>/w3c/input/large/data\_w3c\_large.txt</li></ul></td></tr>
    </table>

    出力は次のようになります。

        (/Info.aspx,1115)
        (/UserService,1130)
        (/Default.aspx,3409)

Pig スクリプトはコンパイルすると MapReduce ジョブになり、その際、複数のジョブが生成される可能性があるため、Pig ジョブの実行中に複数の MapReduce ジョブが実行されることがあります。

### <a name="rebuild"></a>サンプルのリビルド

サンプルは現在、必要なバイナリをすべて含んでいるため、ビルド作業は不要です。Java または .NET サンプルを変更する場合は、msbuild または付属の PowerShell スクリプトを使用してリビルドすることができます。

**サンプルをリビルドするには**

1.  Hadoop コマンド ラインを開きます。
2.  次のコマンドを実行します。

        powershell -F buildsamples.ps1

## <a name="blobstorage"></a>Azure BLOB ストレージへの接続

Azure HDInsight は、既定のファイル システムとして Azure BLOB ストレージを使用します。詳細については、「[HDInsight での Azure BLOB ストレージの使用][]」を参照してください。

ローカル ストレージの代わりに Azure BLOB ストレージを使用して、HDInsight Emulator でローカル クラスターを構成することができます。このセクションでは次の項目を取り上げます。

-   ストレージ エミュレーターへの接続
-   Azure BLOB ストレージへの接続
-   HDInsight Emulator の既定のファイル システムとして Azure BLOB ストレージを構成

### ストレージ エミュレーターへの接続

Azure ストレージ エミュレーターには [Azure SDK for .NET][] が付属しています。ストレージ エミュレーターは自動的に開始されることはありません。手動で開始する必要があります。アプリケーション名は *Azure ストレージ エミュレーター*です。エミュレーターを開始または終了するには、Windows システム トレイにある青色の [Azure] アイコンを右クリックし、[Show Storage Emulator UI] (ストレージ エミュレーター UI の表示) をクリックします。

> [WACOM.NOTE] ストレージ エミュレーターを開始すると、次のようなエラー メッセージが表示されることがあります。

>     The process cannot access the file because it is being used by another process.

> これは、ユーザー ポート 10000 を Hadoop Hive サービスの 1 つも使用しているためです。問題を回避するには、次のステップを実行します。

> 1.  services.msc を使用する 2 つの Hadoop Hive サービス (Apache Hadoop hiveserver と Apache Hadoop Hiveserver2) を停止します。
> 2.  BLOB ストレージ エミュレーターを開始します。
> 3.  2 つの Hadoop Hive サービスを再起動します。

ストレージ エミュレーターにアクセスする構文は次のとおりです。

    wasb://<ContainerName>@storageemulator

次に例を示します。

    hadoop fs -ls wasb://myContainer@storageemulator

> [WACOM.NOTE] 次のようなエラー メッセージが表示されことがあります。

>     ls: No FileSystem for scheme: wasb

> これは、まだ開発者プレビュー バージョンを使用しているためです。この記事の「HDInsight Emulator のインストール」セクションで説明されている手順に従って、開発者プレビュー バージョンをアンインストールし、アプリケーションを再インストールしてください。

### Azure BLOB ストレージへの接続

ストレージ アカウントの作成方法については、「[ストレージ アカウントの作成方法][]」を参照してください。

**コンテナーを作成するには**

1.  [管理ポータル][]にサインインします。
2.  左側の **[ストレージ]** をクリックします。サブスクリプションの下にストレージ アカウントの一覧が表示されます。
3.  コンテナーを作成するストレージ アカウントを一覧からクリックします。
4.  ページの上部にある **[コンテナー]** をクリックします。
5.  ページの下部にある **[追加]** をクリックします。
6.  **[名前]** に値を入力して、**[アクセス]** をクリックします。3 つのアクセス レベルのどれでも使用できます。既定値は **[プライベート]** です。
7.  **[OK]** をクリックして変更を保存します。ポータルの一覧に新しいコンテナーが表示されます。

Azure ストレージ アカウントにアクセスする前に、構成ファイルにアカウント名とアカウント キーを追加する必要があります。

**Azure ストレージ アカウントへの接続を構成するには**

1.  メモ帳で **C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf\\core-site.xml** を開きます。
2.  次の \<property\> タグを他の \<property\> タグの横に追加します。

        <property>
            <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
            <value><StorageAccountKey></value>
        </property>

    \<StorageAccountName\> と \<StorageAccountKey\> は、ストレージ アカウント情報に一致する値に置き換える必要があります。

3.  変更を保存します。Hadoop サービスを再起動する必要はありません。

ストレージ アカウントにアクセスするには次の構文を使用します。

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

次に例を示します。

    hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/

### 既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。

既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用することもできます。Azure HDInsight の場合がそうです。

**Azure BLOB ストレージ コンテナーを使用して既定のファイル システムを構成するには**

1.  メモ帳で **C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf\\core-site.xml** を開きます。
2.  次の \<property\> タグを見つけます。

        <property>
          <name>fs.default.name</name>
          <!-- cluster variant -->
          <value>hdfs://localhost:8020</value>
          <description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
          <final>true</final>
        </property>

3.  次の 2 つの \<property\> タグに置き換えます。

        <property>
          <name>fs.default.name</name>
          <!-- cluster variant -->
          <!--<value>hdfs://localhost:8020</value>-->
          <value>wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net</value>
          <description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
          <final>true</final>
        </property>

        <property>
          <name>dfs.namenode.rpc-address</name>
          <value>hdfs://localhost:8020</value>
          <description>A base for other temporary directories.</description>
        </property>

    \<StorageAccountName\> と \<StorageAccountKey\> は、ストレージ アカウント情報に一致する値に置き換える必要があります。

4.  変更を保存します。
5.  デスクトップから Hadoop コマンド ラインを昇格モードで開きます ([管理者として実行])。
6.  次のコマンドを実行して、Hadoop サービスを再起動します。

        C:\Hadoop\stop-onebox.cmd
        C:\Hadoop\start-onebox.cmd

7.  次のコマンドを実行して、既定のファイル システムへの接続をテストします。

        hadoop fs -ls /

    次のコマンドは同じフォルダーの内容を一覧表示します。

        hadoop fs -ls wasb:///
        hadoop fs -ls wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/
        hadoop fs -ls wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

    HDFS にアクセスするには、次のコマンドを使用します。

        hadoop fs -ls hdfs://localhost:8020/

## <a name="powershell"></a> HDInsight PowerShell の実行

HDInsight PowerShell コマンドレットの一部は HDInsight Emulator でサポートされています。サポートされているコマンドレットは次のとおりです。

-   HDInsight ジョブ定義コマンドレット

    -   New-AzureHDInsightSqoopJobDefinition
    -   New-AzureHDInsightStreamingMapReduceJobDefinition
    -   New-AzureHDInsightPigJobDefinition
    -   New-AzureHDInsightHiveJobDefinition
    -   New-AzureHDInsightMapReduceJobDefinition
-   Start-AzureHDInsightJob
-   Get-AzureHDInsightJob
-   Wait-AzureHDInsightJob

Hadoop ジョブを送信する例を次に示します。

    $creds = Get-Credential (hadoop as username, password can be anything)
    $hdinsightJob = <JobDefinition>
    Start-AzureHDInsightJob -Cluster http://localhost:50111 -Credential $creds -JobDefinition $hdinsightJob

Get-Credential を呼び出すと入力画面が表示されます。ユーザー名として **hadoop** を使用する必要があります。パスワードは任意の文字列でかまいません。クラスター名は常に **<http://localhost:50111>** です。

Hadoop ジョブの送信方法の詳細については、「[プログラムによる Hadoop ジョブの送信][]」を参照してください。HDInsight PowerShell コマンドレットの詳細については、「[HDInsight cmdlet reference (HDInsight コマンドレット リファレンス)][]」を参照してください。

## <a name="nextsteps"></a>次のステップ

このチュートリアルでは、HDInsight Emulator をインストールして、Hadoop ジョブをいくつか実行しました。詳細については、次の記事を参照してください。

-   [Azure の HDInsight の概要][]
-   [Develop Java MapReduce programs for HDInsight (HDInsight 用 Java MapReduce プログラムの開発)][]
-   [HDInsight 用 C# Hadoop ストリーミング MapReduce プログラムの開発][]
-   [HDInsight Emulator リリース ノート][HDInsight Emulator のリリース ノート]
-   [HDInsight について議論する MSDN フォーラム][]

  [Azure HDInsight でサポートされている Hadoop のバージョン]: ../hdinsight-component-versioning/
  [Azure の HDInsight の概要]: ../hdinsight-get-started/
  [Azure PowerShell のインストールおよび構成に関するページ]: ../install-configure-powershell/
  [HDInsight Emulator のインストール]: #install
  [ワード カウント サンプルの実行]: #runwordcount
  [入門サンプルの実行]: #rungetstartedsamples
  [Azure BLOB ストレージへの接続]: #blobstorage
  [HDInsight PowerShell の実行]: #powershell
  [次のステップ]: #nextsteps
  [Microsoft HDInsight Emulator for Azure インストール ページ]: http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT
  [HDI.Emulator.Services]: ./media/hdinsight-get-started-emulator/HDI.Emulator.Services.png
  [HDInsight Emulator のリリース ノート]: ../hdinsight-emulator-release-notes/
  [Hadoop コマンド マニュアル]: http://hadoop.apache.org/docs/r1.1.1/commands_manual.html
  [HDInsight での Azure BLOB ストレージの使用]: ../hdinsight-use-blob-storage/
  [IIS W3C ログ データのシナリオ]: #scenarios
  [サンプル W3C ログ データの読み込み]: #loaddata
  [Java MapReduce ジョブの実行]: #javamapreduce
  [Hive ジョブの実行]: #hive
  [Pig ジョブの実行]: #pig
  [サンプルのリビルド]: #rebuild
  [Azure SDK for .NET]: http://azure.microsoft.com/en-us/downloads/
  [ストレージ アカウントの作成方法]: ../storage-create-storage-account/
  [管理ポータル]: https://manage.windowsazure.com/
  [プログラムによる Hadoop ジョブの送信]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [HDInsight cmdlet reference (HDInsight コマンドレット リファレンス)]: http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx
  [Develop Java MapReduce programs for HDInsight (HDInsight 用 Java MapReduce プログラムの開発)]: ../hdinsight-develop-deploy-java-mapreduce/
  [HDInsight 用 C# Hadoop ストリーミング MapReduce プログラムの開発]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [HDInsight について議論する MSDN フォーラム]: http://social.msdn.microsoft.com/Forums/en-US/hdinsight
