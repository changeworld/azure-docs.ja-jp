<properties
   pageTitle="HDInsight での Hadoop Hive の使用 | Azure"
   description="Web ベース HDInsight クエリ コンソールにより Hive を使用する方法について説明します。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# クエリ コンソールを使用して Hive クエリを実行

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

この記事では、HDInsight クエリ コンソールを使用して、お使いのブラウザーから HDInsight Hadoop クラスターで Hive クエリを実行する方法について説明します。

> [AZURE.NOTE] クエリ コンソールは、Windows ベースの HDInsight クラスターでのみ使用できます。

##<a id="prereq"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Windows ベースの HDInsight Hadoop クラスター

* 最新の Web ブラウザー

##<a id="run"></a>クエリ コンソールを使用して Hive クエリを実行

1. <a href="https://manage.windowsazure.com" target="_blank">Azure の管理ポータル</a>を開き、HDInsight クラスターを選択します。ページの下部にある、**[クエリ コンソール]** を選択します。メッセージが表示されたら、クラスターの作成時に使用したユーザー名とパスワードを入力します。

    > [AZURE.NOTE] クエリ コンソールにはブラウザーに「**https://CLUSTERNAME.azurehdinsight.net**」と入力することでもアクセスできます。

2. ページ上部のリンクから、**[Hive エディター]** を選択します。HDInsight クラスターで実行する HiveQL ステートメントの入力に使用できるフォームが表示されます。 
	
	![the hive editor](./media/hdinsight-hadoop-use-hive-query-console/queryconsole.png)
	
	テキスト `Select * from hivesampletable` を次の HiveQL ステートメントに置き換えます。

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    これらのステートメントは次のアクションを実行します。

    * **DROP TABLE** - テーブルが既存の場合にテーブルとデータ ファイルを削除します。
    * **CREATE EXTERNAL TABLE** - Hive に新しく '外部'テーブルを作成します。外部テーブルは、Hive にテーブル定義のみを格納し、データは、元の場所に残します。

    > [AZURE.NOTE] 基盤となるデータを外部ソースによって更新する (データの自動アップロード処理など) 場合や別の MapReduce 操作によって更新する場合に、Hive クエリで最新のデータを使用する場合は、外部テーブルを使用する必要があります。
    >
    > 外部テーブルを削除しても、データは削除**されません**。テーブル定義のみが削除されます。

    * **ROW FORMAT** - Hive にデータの形式を示します。ここでは、各ログのフィールドは、スペースで区切られています。
    * **STORED AS TEXTFILE LOCATION** - Hive に、データの格納先 (example/data directory) と、データはテキストとして格納されていることを示します。
    * **SELECT** - **t4** 列の値が **[ERROR]** であるすべての行の数を指定します。ここでは、この値を含む列が 3 行あるため、**3** という値が返されています。

2. **[送信]** を選択します。ページ下部の **[ジョブ セッション]** にジョブの詳細が表示されます。

3. **[ステータス]** フィールドが **Completed** に変わったら、ジョブの **[詳細の表示]** を選択します。詳細ページの **[ジョブ出力]** に `[ERROR]	3` が含まれます。このフィールドの下にある **[ダウンロード]** ボタンを使用して、ジョブの出力を含むファイルをダウンロードします。


##<a id="summary"></a>まとめ

このように、クエリ コンソールを使用すると、HDInsight クラスターで簡単に Hive クエリを実行し、ジョブ ステータスを監視し、出力を取得できます。 

クエリ コンソールを使用した Hive の詳細については、クエリ コンソールの上部にある **[概要]** を選択し、サンプルを使用します。各サンプルでは順を追って Hive を使用してデータを分析します。また、サンプルで使用されている HiveQL ステートメントについいての説明も含まれています。

##<a id="nextsteps"></a>次のステップ

HDInsight での Hive に関する全般的な情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)

* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)

[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/



[hdinsight-storage]: ../hdinsight-use-blob-storage

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!--HONumber=47-->
