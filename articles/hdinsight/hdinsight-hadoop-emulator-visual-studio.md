<properties
pageTitle="Hortonworks サンドボックスで Microsoft Azure Data Lake Tools for Visual Studio を使用する | Microsoft Azure"
description="(ローカル VM で実行される) Hortonworks サンドボックスで Azure Data Lake Tools for Visual Studio を使用する方法について説明します。 このツールを使用すると、サンドボックスで Hive ジョブと Pig ジョブを作成して実行し、ジョブの出力と履歴を表示できます。"
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
ms.date="08/26/2016"
ms.author="larryfr"/>

# Hortonworks サンドボックスで Azure Data Lake Tools for Visual Studio を使用する

Azure Data Lake Tools for Visual Studio には、Azure Data Lake と HDInsight を操作するためのツールに加えて、汎用的な Hadoop クラスターを操作するためのツールも含まれています。このドキュメントでは、ローカルの仮想マシンで実行されている Hortonworks サンドボックスで、Azure Data Lake ツールを使用するために必要な手順を説明します。

Hortonworks サンドボックスを使用すると、ローカルの開発環境で Hadoop を使用できます。開発したソリューションを大規模にデプロイしたい場合に、HDInsight クラスターに移行できます。

## 前提条件

* 開発環境の仮想マシンで実行されている Hortonworks サンドボックス。このドキュメントに記載されている内容は、Oracle VirtualBox で実行されるサンドボックスを使用してテストされています。また、サンドボックスは、[Hadoop エコシステムの使用方法](hdinsight-hadoop-emulator-get-started.md)に関するドキュメントに記載された情報を使用して構成されています。

* Visual Studio 2013 または 2015 の任意のエディション。

* [Azure SDK for .NET](https://azure.microsoft.com/downloads/) 2.7.1 以降。

* [Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)。

## サンドボックスのパスワードを構成する

Hortonworks サンドボックスが実行されていることを確認したら、[Hadoop エコシステムの使用方法](hdinsight-hadoop-emulator-get-started.md#set-passwords)に関するドキュメントに記載された手順に従い、SSH `root` アカウントと Ambari `admin` アカウントのパスワードを構成します。これらのパスワードは、Visual Studio からサンドボックスに接続する際に使用されます。

## サンドボックスにツールを接続する

1. Visual Studio を開き、__[表示]__、__[サーバー エクスプローラー]__ の順に選択します。

2. __[サーバー エクスプローラー]__ の __[HDInsight]__ エントリをクリックし、__[Connect to HDInsight Emulator (HDInsight Emulator に接続)]__ を選択します。

    ![Connect to HDInsight Emulator](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. __[Connect to HDInsight Emulator (HDInsight Emulator に接続)]__ ダイアログ ボックスで、Ambari 用に構成したパスワードを入力します。

    ![Enter Ambari password](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    __[次へ]__ をクリックして続行します。

4. __[パスワード]__ フィールドに、`root` アカウント用に構成したパスワードを入力します。他のフィールドは既定値のままにします。

    ![Enter root password](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    __[次へ]__ をクリックして続行します。

5. サービスの検証が完了するのを待ちます。検証が失敗し、構成を更新するよう求められることがあります。その場合は、__[更新]__ をクリックし、サービスの構成と検証が完了するのを待ちます。

    ![Errors and update button](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [AZURE.NOTE] 更新プロセスでは、Ambari を使用して、Azure Data Lake Tools for Visual Studio で必要とされる要件に合わせて Hortonworks サンドボックス構成が変更されます。

    検証が終わったら、__[完了]__ をクリックして、構成を完了します。

    ![Finish connecting](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

    > [AZURE.NOTE] 開発環境の処理速度と仮想マシンに割り当てられたメモリの量によっては、サービスの構成と検証に数分かかることがあります。

上記の手順を終了すると、[サーバー エクスプローラー] の [HDInsight] セクションに [HDInsight local cluster (HDInsight ローカル クラスター)] エントリが表示されます。

## Hive クエリを記述する

Hive には、構造化データを操作するための、SQL に似たクエリ言語 (HiveQL) が用意されています。次の手順では、ローカル クラスターにアドホック クエリを実行する方法を示します。

1. __[サーバー エクスプローラー]__ で、前の手順で追加したローカル クラスターのエントリを右クリックし、__[Write a Hive query (Hive クエリの記述)]__ を選択します。

    ![Write a hive query](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    新しいクエリ ウィンドウが開き、短時間でクエリを入力して、ローカル クラスターに送信できます。

2. 新しいクエリ ウィンドウに、次のように入力します。

        select count(*) from sample_08;
    
    クエリ ウィンドウの上部で、ローカル クラスターの構成が選択されていることを確認し、__[送信]__ をクリックします。他の値 (__[バッチ]__ とサーバー名) は既定値のままにします。

    ![query window and submit button](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    __[送信]__ の横にあるドロップダウン メニューで __[詳細設定]__ を選択することもできます。これを選択するとダイアログが開き、ジョブを送信するときに利用できる追加のオプションが表示されます。

    ![advanced submit](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. クエリを送信すると、ジョブの状態が表示されます。ここには、Hadoop で処理されているジョブの情報が表示されます。__[ジョブの状態]__ エントリは、ジョブの現在の状態を示します。状態は定期的に更新されます。更新アイコンを使用して、状態を手動で更新することもできます。

    ![Job state](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    __[ジョブの状態]__ が __[完了]__ に変わったら、有向非巡回グラフ (DAG) が表示されます。このグラフは、Tez (ローカル クラスター上の Hive の既定の実行エンジン) によって決定される実行パスを示しています。
    
    > [AZURE.NOTE] Linux ベースの HDInsight クラスターを使用する場合も、Tez が既定の実行エンジンです。Windows ベースの HDInsight では既定ではありません。Tez を使用するには、Hive クエリの先頭に `set hive.execution.engine = tez;` という行を追加する必要があります。

    __[ジョブの出力]__ リンクをクリックすると、出力が表示されます。この例では、__823__ と表示されます。これは、sample\_08 テーブル内の行の数です。__[ジョブのログ]__ リンクと __[Download YARN Log (YARN ログのダウンロード)]__ リンクを使用すると、ジョブに関する診断情報を表示できます。

4. また、__[バッチ]__ フィールドを __[対話型]__ に変更し、__[実行]__ をクリックすると、対話形式で Hive ジョブを実行できます。

    ![Interactive query](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    これによって、処理中に生成された出力ログが __[HiveServer2 Output (HiveServer2 出力)]__ ウィンドウにストリーム表示されます。
    
    > [AZURE.NOTE] これは、ジョブが完了した後に __[ジョブのログ]__ リンクから取得できるものと同じ情報です。

    ![HiveServer2 output](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## Hive プロジェクトを作成する

複数の Hive スクリプトを含むプロジェクトを作成することもできます。これは、複数の関連スクリプトを 1 つにまとめておきたい場合や、バージョン管理システムを使用して管理する必要がある場合に便利です。

1. Visual Studio で、__[ファイル]__、__[新規]__、\_\_[プロジェクト]\_\_ の順に選択します。

2. プロジェクトの一覧から、__[テンプレート]__、__[Azure Data Lake]__ の順に展開し、__[HIVE (HDInsight)]__ を選択します。テンプレートの一覧から __[Hive Sample (Hive サンプル)]__ を選択します。名前と場所を入力し、__[OK]__ を選択します。

    ![HIVE (HDInsight) template](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

__[Hive Sample (Hive サンプル)]__ プロジェクトには、__WebLogAnalysis.hql__ と __SensorDataAnalysis.hql__ という 2 つのスクリプトが含まれています。これらのスクリプトを送信する際も、ウィンドウの上部にある __[送信]__ ボタンをクリックします。

## Pig プロジェクトを作成する

Hive には構造化データを操作するための SQL に似た言語が用意されていますが、Pig はデータに適用される変換のパイプラインを作成できる言語 (Pig Latin) を備えています。ローカル クラスターで Pig を使用するには、次の手順に従います。

1. Visual Studio を開いて、__[ファイル]__、__[新規]__、__[プロジェクト]__ の順に選択します。プロジェクトの一覧から、__[テンプレート]__、__[Azure Data Lake]__ の順に展開し、__[Pig (HDInsight)]__ を選択します。テンプレートの一覧から、__[Pig Application (Pig アプリケーション)]__ を選択します。名前と場所を入力し、__[OK]__ を選択します。

    ![Pig (HDInsight) project](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. このプロジェクトで作成した __script.pig__ ファイルの内容として、次のコードを入力します。

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int, 
            ip_address:chararray, 
            date:chararray, 
            time:chararray, 
            landing_page:chararray, 
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Pig では Hive とは異なる言語を使用しますが、ジョブを実行する方法はどちらの言語でも同じで、__[送信]__ ボタンをクリックします。__[送信]__ の横にあるドロップダウンを選択すると、Pig の詳細な送信設定のためのダイアログが表示されます。

    ![Pig advanced submit](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)
    
3. 表示されるジョブの状態と出力は、Hive クエリの場合と同じです。

    ![image of a completed pig job](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## ジョブを表示する

Azure Data Lake Tools では、Hadoop で実行されたジョブに関する情報も簡単に表示できます。ローカル クラスターで実行されたジョブを表示するには、次の手順に従います。

1. __[サーバー エクスプローラー]__ で、ローカル クラスターを右クリックし、__[ジョブの表示]__ を選択します。クラスターに送信されたジョブの一覧が表示されます。

    ![View jobs](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. ジョブの一覧から、詳細を表示するジョブを選択します。

    ![select a job](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    表示される情報は、Hive や Pig のクエリを実行した後に出力とログ情報を表示するためのリンクを使用して得られるものとほとんど同じです。

3. また、ここからジョブの変更と再送信を実行することもできます。

## Hive データベースを表示する

1. __[サーバー エクスプローラー]__ で、__[HDInsight local cluster (HDInsight ローカル クラスター)]__ エントリ、__[Hive Databases (Hive データベース)]__ の順に展開します。ローカル クラスター上の __[既定]__ データベースと __[xademo]__ データベースが表示されます。データベースを展開すると、データベース内のテーブルが表示されます。

    ![expanded databases](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. テーブルを展開すると、そのテーブルの列が表示されます。テーブルを右クリックし、__[View Top 100 Rows (上位 100 行の表示)]__ を選択すると、すぐにデータが表示されます。

    ![hive databases view](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### データベースとテーブルのプロパティ

お気付きかもしれませんが、データベースまたはテーブルで __[プロパティ]__ を選択して表示することができます。選択したアイテムの詳細が [プロパティ] ウィンドウに表示されます。

![プロパティ](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### テーブルを作成する

新しいテーブルを作成するには、データベースを右クリックし、__[テーブルの作成]__ を選択します。

![テーブルを作成する](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

ここでフォームを使用してテーブルを作成できます。このページの下部に表示されている未加工の HiveQL を使用してテーブルを作成します。

![create table form](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## 次のステップ

* [Hortonworks Sandbox の使い方のヒント](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop チュートリアル: HDP の概要](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

<!---HONumber=AcomDC_0921_2016-->