---
title: HDInsight 上のクエリ コンソールで Apache Hive を使用する - Azure
description: Web ベースのクエリ コンソールを使用して、お使いのブラウザーから HDInsight Hadoop クラスター上で Apache Hive クエリを実行する方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 1e638bd348b7a5272dd8bfbe25aa841f38a51b9a
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409702"
---
# <a name="run-apache-hive-queries-using-the-query-console"></a>クエリ コンソールを使用して Apache Hive クエリを実行する
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

この記事では、HDInsight クエリ コンソールを使用して、お使いのブラウザーから HDInsight Hadoop クラスター上で Apache Hive クエリを実行する方法について説明します。

> [!IMPORTANT]  
> HDInsight クエリ コンソールは、Windows ベースの HDInsight クラスターでのみ使用できます。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](../hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。
>
> HDInsight 3.4 以上での Web ブラウザーからの Hive クエリの実行に関する情報については、[Ambari Hive ビューでの Apache Hive クエリの実行](apache-hadoop-use-hive-ambari-view.md)に関するページを参照してください。

## <a id="prereq"></a>前提条件
この記事の手順を完了するには、次のものが必要です。

* Windows ベースの HDInsight Hadoop クラスター
* 最新の Web ブラウザー

## <a id="run"></a> クエリ コンソールを使用して Apache Hive クエリを実行する
1. Web ブラウザーを開き、**https://CLUSTERNAME.azurehdinsight.net** に移動します。**CLUSTERNAME** は実際の HDInsight クラスターの名前です。 プロンプトが表示されたら、クラスターの作成時に使用したユーザー名とパスワードを入力します。
2. ページ上部のリンクから、 **[Hive エディター]** を選択します。 HDInsight クラスターで実行する HiveQL ステートメントの入力に使用できるフォームが表示されます。

    ![Hive エディター](./media/apache-hadoop-use-hive-query-console/queryconsole.png)

    テキスト `Select * from hivesampletable` を次の HiveQL ステートメントに置き換えます。

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    これらのステートメントは次のアクションを実行します。

   * **DROP TABLE**: テーブルが既存の場合にテーブルとデータ ファイルを削除します。
   * **CREATE EXTERNAL TABLE**: 新しい "外部" テーブルを Hive に作成します。 外部テーブルは、Hive にテーブル定義のみを格納し、データは、元の場所に残します。

     > [!NOTE]  
     > 基盤となるデータを外部ソースによって更新する (データの自動アップロード処理など) 場合や別の MapReduce 操作によって更新する場合に、Hive クエリで最新のデータを使用する場合は、外部テーブルを使用する必要があります。
     >
     > 外部テーブルを削除しても、データは削除**されません**。テーブル定義のみが削除されます。
     >
     >
   * **ROW FORMAT**: データの形式を Hive に伝えます。 ここでは、各ログのフィールドは、スペースで区切られています。
   * **STORED AS TEXTFILE LOCATION**: データの格納先 (example/data ディレクトリ) と、データがテキストとして格納されていることを Hive に伝えます
   * **SELECT**: **t4** 列の値が **[ERROR]** であるすべての行の数を指定します。 ここでは、この値を含む行が 3 行あるため、 **3** という値が返されています。
   * **INPUT__FILE__NAME LIKE '%.log'** - Hive に .log で終わるファイルのデータのみを返す必要があることを示します。 これにより、検索はデータを含む sample.log ファイルに制限され、定義したスキーマに一致しない他のサンプル データ ファイルのデータを返すことができなくなります。
3. **[送信]** をクリックします。 ページ下部の **[ジョブ セッション]** にジョブの詳細が表示されます。
4. **[ステータス]** フィールドが **Completed** に変わったら、ジョブの **[詳細の表示]** を選択します。 詳細ページの **[ジョブ出力]** に `[ERROR]    3` が含まれます。 このフィールドの下にある **[ダウンロード]** ボタンを使用して、ジョブの出力を含むファイルをダウンロードします。

## <a id="summary"></a>概要
このように、クエリ コンソールを使用すると、HDInsight クラスターで簡単に Hive クエリを実行し、ジョブ ステータスを監視し、出力を取得できます。

Hive クエリ コンソールを使用した Hive ジョブの実行の詳細については、クエリ コンソールの上部にある **[概要]** を選択し、サンプルを使用します。 各サンプルでは順を追って Hive を使用してデータを分析します。また、サンプルで使用されている HiveQL ステートメントについての説明も含まれています。

## <a id="nextsteps"></a>次のステップ
HDInsight での Hive に関する全般的な情報

* [HDInsight 上の Apache Hadoop で Apache Hive を使用する](hdinsight-use-hive.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight 上の Apache Hadoop で Apache Pig を使用する](hdinsight-use-pig.md)
* [HDInsight 上の Apache Hadoop で MapReduce を使用する](hdinsight-use-mapreduce.md)

Hive で Tez を使用する場合、デバッグ情報については、次のドキュメントを参照してください。

* [Windows ベースの HDInsight で Apache Tez UI を使用する](../hdinsight-debug-tez-ui.md)
* [Linux ベースの HDInsight で Apache Ambari Tez ビューを使用する](../hdinsight-debug-ambari-tez-view.md)

[1]:apache-hadoop-visual-studio-tools-get-started.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
