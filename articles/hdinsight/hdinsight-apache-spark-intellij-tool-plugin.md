---
title: "Azure Toolkit for IntelliJ - HDInsight Spark 向けの Scala アプリケーションの作成 | Microsoft Docs"
description: "Azure Toolkit for IntelliJ を使用して Scala で記述された Spark アプリケーションを開発し、HDInsight Spark クラスターに送信します。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 73304272-6c8b-482e-af7c-cd25d95dab4d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: e6e005e07b35bbaffebf1efa216feebafc99df8b
ms.contentlocale: ja-jp
ms.lasthandoff: 05/17/2017


---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-hdinsight-cluster"></a>Azure Toolkit for IntelliJ を使用して HDInsight クラスター向けの Scala アプリケーションを作成する

Azure Toolkit for IntelliJ プラグインを使用して Scala で記述された Spark アプリケーションを開発し、IntelliJ IDE から直接、HDInsight Spark クラスターに送信します。 このプラグインには、次のような複数の使い方があります。

* Scala Spark アプリケーションを開発して HDInsight Spark クラスターに送信する
* Azure HDInsight Spark クラスター リソースにアクセスする
* Scala Spark アプリケーションをローカルで開発して実行する

必要に応じて[ビデオ](https://mix.office.com/watch/1nqkqjt5xonza)もご覧ください。

> [!IMPORTANT]
> このプラグインを使用すると、Linux 上で HDInsight Spark クラスター専用のアプリケーションを作成し、送信することができます。
> 
> 

## <a name="prerequisites"></a>前提条件

* HDInsight Linux での Apache Spark クラスター。 手順については、 [Azure HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)に関するページを参照してください。

* Oracle Java Development Kit。 [Oracle の Web サイト](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)からインストールできます。

* IntelliJ IDEA。 この記事では、バージョン 15.0.1 を使用します。 [JetBrains の Web サイト](https://www.jetbrains.com/idea/download/)からインストールできます。

## <a name="install-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ をインストールする
インストール手順については、「[Azure Toolkit for IntelliJ のインストール](../azure-toolkit-for-intellij-installation.md)」を参照してください。

## <a name="sign-in-to-your-azure-subscription"></a>Azure サブスクリプションにサインインします。
1. IntelliJ IDE を起動し、Azure Explorer を開きます。 **[View]\(ビュー\)** メニューの **[Tool Windows]\(ツール ウィンドウ\)** をクリックし、**[Azure Explorer]** をクリックします。
   
    ![[View]\(ビュー\) メニューで選択したコマンド](./media/hdinsight-apache-spark-intellij-tool-plugin/show-azure-explorer.png)
2. **[Azure]** ノードを右クリックし、**[Sign In]\(サインイン\)** をクリックします。
3. **[Azure Sign In]\(Azure サインイン\)** ダイアログ ボックスの **[Sign in]\(サインイン\)** をクリックし、Azure 資格情報を入力します。
   
    ![Azure サインイン ダイアログ ボックス](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-2.png)
4. サインイン後、**[Select Subscriptions]\(サブスクリプションの選択\)** ダイアログ ボックスに、その資格情報に関連付けられているすべての Azure サブスクリプションの一覧が表示されます。 **[Select]\(選択\)** をクリックしてダイアログ ボックスを閉じます。

    ![[Select Subscriptions]\(サブスクリプションの選択\) ダイアログ ボックス](./media/hdinsight-apache-spark-intellij-tool-plugin/Select-Subscriptions.png)
5. **[Azure Explorer]** タブで **[HDInsight]** を展開し、自分のサブスクリプションの下にある HDInsight Spark クラスターを表示します。
   
    ![Azure Explorer での HDInsight Spark クラスター](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-3.png)
6. クラスター名のノードをさらに展開すると、そのクラスターに関連付けられているリソース (ストレージ アカウントなど) を表示できます。
   
    ![クラスター名を展開してリソースを表示する](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>HDInsight Spark クラスターでの Spark Scala アプリケーションの実行
1. IntelliJ IDEA を起動し、プロジェクトを作成します。 **[New Project]\(新しいプロジェクト\)** ダイアログ ボックスで、次の選択を行い、**[Next]\(次へ\)** をクリックします。
   
   * 左側のウィンドウで、**[HDInsight]** を選択します。
   * 右側のウィンドウで、**[Spark on HDInsight (Scala)]\(HDInsight の Spark (Scala)\)** を選択します。
   
    ![New Project dialog box](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)
2. 次のウィンドウでプロジェクトの詳細を次のように指定し、**[Finish]\(完了\)** をクリックします。
   
   * プロジェクト名とプロジェクトの場所を指定します。
   * **[Project SDK]\(プロジェクト SDK\)** では、Java 1.8 を使用します (Spark 1.6 または Spark 2.0 クラスターの場合)。
   * **Scala SDK** の場合、**[Create (作成)]** をクリックし、**[Download (ダウンロード)]** をクリックして、使用する Scala のバージョンを選択します。
     * ジョブを Spark 2.0 クラスターに送信する場合は、**JDK 1.8 および Scala 2.11.x** を選択します。
     * ジョブを Spark 1.6 クラスターに送信する場合は、**JDK 1.8 (言語レベル 7) と Scala 2.10.x** を選択します。

        ![Scala バージョンの選択](./media/hdinsight-apache-spark-intellij-tool-plugin/show-scala2.11.x-select.png)
   * **[Spark SDK]** では、[GitHub] (http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409) から SDK をダウンロードして使用します。 spark-assembly-2.0.0-hadoop2.7.0-SNAPSHOT.jar は Spark 2.0 クラスターを、spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar は Spark 1.6 クラスターを対象としています。 

     代わりに [Spark Maven リポジトリ](http://mvnrepository.com/search?q=spark)を使用することもできますが、Spark アプリケーションの開発に適した Maven リポジトリがインストールされていることを確認してください。 たとえば、Spark ストリーミングを使用している場合は、Spark ストリーミング部分がインストールされていることを確認してください。 また、Spark 1.6 クラスターの場合は Scala 2.10 とマークされているリポジトリを、Spark 2.0 クラスターの場合は、Scala 2.11 とマークされているリポジトリを使用していることを確認してください。
     
       ![Spark SDK の選択](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)
3. Spark プロジェクトでは、アーティファクトが自動的に作成されます。 アーティファクトを表示するには、次の手順に従います。
   
   1. **[File]\(ファイル\)** メニューの **[Project Structure]\(プロジェクトの構造\)** をクリックします。
   2. **[Project Structure (プロジェクトの構造)]** ダイアログ ボックスの **[Artifacts (アーティファクト)]** をクリックし、作成された既定のアーティファクトを確認します。 **+** アイコンをクリックして、独自のアーティファクトを作成することもできます。
      
       ![アーティファクト情報を示すダイアログ ボックス](./media/hdinsight-apache-spark-intellij-tool-plugin/default-artifact.png)
      
4. **[Project Structure (プロジェクトの構造)]** ダイアログ ボックスの **[Project (プロジェクト)]** をクリックします。 **[Project SDK]\(プロジェクト SDK\)** を 1.8 に設定した場合は、**[Project language level]\(プロジェクト言語レベル\)** が **[7 - Diamonds, ARM, multi-catch, etc]\(7 - Diamonds、ARM、マルチキャッチなど\)** に設定されていることを確認してください (Spark 2.0 クラスターでは省略可能です)。
   
    ![プロジェクトの言語レベルの設定](./media/hdinsight-apache-spark-intellij-tool-plugin/set-project-language-level.png)
5. アプリケーション ソース コードを追加します。
   
   1. Project Explorer で、**[src]** を右クリックし、**[New]\(新規\)** をポイントして、**[Scala Class]\(Scala クラス\)** をクリックします。
      
       ![Project Explorer から Scala クラスを作成するためのコマンド](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)
   2. **[Create New Scala Class]\(新規 Scala クラスの作成\)** ダイアログ ボックスで、名前を指定し、**[Kind]\(種類\)** ボックスの一覧で **[Object]\(オブジェクト\)** を選択して、**[OK]** をクリックします。
      
       ![[Create New Scala Class]\(新規 Scala クラスの作成\) ダイアログ ボックス](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)
   3. **MyClusterApp.scala** ファイルで、次のコードを貼り付けます。 このコードは HVAC.csv (すべての HDInsight Spark クラスターで使用可能) からデータを読み取り、CSV ファイルの 7 番目の列に 1 桁の数字のみが含まれる行を取得し、出力をクラスター用の既定のストレージ コンテナーの下にある **/HVACOut** に書き込みます。

            import org.apache.spark.SparkConf
            import org.apache.spark.SparkContext

            object MyClusterApp{
              def main (arg: Array[String]): Unit = {
                val conf = new SparkConf().setAppName("MyClusterApp")
                val sc = new SparkContext(conf)

                val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

                //find the rows that have only one digit in the seventh column in the CSV file
                val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

                rdd1.saveAsTextFile("wasbs:///HVACOut")
              }

            }

6. HDInsight Spark クラスターでアプリケーションを実行します。
   
   1. Project Explorer で、プロジェクト名を右クリックし、**[Submit Spark Application to HDInsight]\(HDInsight への Spark アプリケーションの送信\)** を選択します。
      
       ![[Submit Spark Application to HDInsight]\(HDInsight への Spark アプリケーションの送信\) の選択](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)
   2. Azure サブスクリプションの資格情報を入力するよう求められます。 **[Spark Submission]\(Spark 送信\)** ダイアログ ボックスで、次の値を入力し、**[Submit]\(送信\)** をクリックします。
      
      * **[Spark clusters (Linux only) (Spark クラスター (Linux のみ))]**では、アプリケーションを実行する HDInsight Spark クラスターを選択します。
      * IntelliJ プロジェクトまたはハード ドライブからアーティファクトを選択します。
      * **[Main class name]\(メイン クラス名\)** ボックスで、省略記号 (![省略記号](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png)) をクリックし、アプリケーションのソース コード内のメイン クラスを選択し、**[OK]** をクリックします。
        
          ![[Select Main Class]\(メイン クラスの選択\) ダイアログ ボックス](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)
      * この例のアプリケーション コードでは、コマンドライン引数を必要とせず、JAR またはファイルを参照することもないので、残りのボックスは空のままでかまいません。
        すべての入力を指定すると、ダイアログ ボックスは次の図のようになります。
        
          ![[Spark Submission]\(Spark 送信\) ダイアログ ボックス](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)
   3. ウィンドウの下部にある **[Spark Submission (Spark 送信)]** タブで、進行状況の表示が開始されます。 **[Spark Submission]\(Spark 送信\)** ウィンドウにある赤いボタンをクリックして、アプリケーションを停止することもできます。
      
       ![[Spark Submission]\(Spark 送信\) ウィンドウ](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      この記事の後の「Azure Toolkit for IntelliJ を使用して HDInsight Spark クラスターにアクセスして管理する」セクションでは、ジョブ出力にアクセスする方法を説明します。

## <a name="choose-azure-data-lake-store-as-spark-scala-application-storage"></a>Spark Scala アプリケーション ストレージとして Azure Data Lake Store を選択する
アプリケーションを Azure Data Lake Store に送信する場合は、Azure のサインイン プロセスで **[Interactive]\(インタラクティブ\)** モードを選択する必要があります。 

   ![サインイン時の [Interactive]\(インタラクティブ\) オプション](./media/hdinsight-apache-spark-intellij-tool-plugin/authentication-interactive.png)

**[Automated]\(自動\)** モードを選択した場合、次のエラーが発生します。

   ![サインイン エラー](./media/hdinsight-apache-spark-intellij-tool-plugin/authentication-error.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ を使用して HDInsight Spark クラスターにアクセスして管理する
Azure Toolkit for IntelliJ を使用してさまざまな操作を実行できます。

### <a name="access-the-job-view"></a>ジョブ ビューにアクセスする
1. Azure Explorer で **[HDInsight]** を展開します。Spark クラスター名を展開した後、**[Jobs]\(ジョブ\)** をクリックします。
2. 右側のウィンドウの **[Spark Job View (Spark ジョブ ビュー)]** タブに、クラスター上で実行されていたすべてのアプリケーションが表示されます。 詳細情報を確認したいアプリケーションの名前をクリックします。
   
    ![Application details](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)

**[Error]\(エラー\)**、**[Driver Log]\(ドライバー ログ\)**、**[OutPut]\(出力\)**、および **[Livy Log]\(Livy ログ\)** の各ボックスに、選択したアプリケーションに応じた値が入力されます。

ウィンドウの上部にあるボタンをクリックして、Spark 履歴 UI と YARN UI を (アプリケーション レベルで) 開くこともできます。

### <a name="access-the-spark-history-server"></a>Spark 履歴サーバーにアクセスする
1. Azure Explorer で、**[HDInsight]** を展開します。Spark クラスター名を右クリックし、**[Open Spark History UI]\(Spark 履歴 UI を開く\)** を選択します。 入力を求められたら、クラスターの管理者資格情報を入力します。 これらは、クラスターのプロビジョニング時に指定済みである必要があります。
2. Spark 履歴サーバーのダッシュボードでは、実行が終了したばかりのアプリケーションをアプリケーション名を使って探すことができます。 上記のコードでは、`val conf = new SparkConf().setAppName("MyClusterApp")` を使用してアプリケーション名を設定しました。 したがって、Spark アプリケーション名は **MyClusterApp**です。

### <a name="start-the-ambari-portal"></a>Ambari ポータルを起動する
1. Azure Explorer で、**[HDInsight]** を展開します。Spark クラスター名を右クリックし、**[Open Cluster Management Portal (Ambari)]\(クラスター管理ポータルを開く (Ambari)\)** を選択します。 
2. 入力を求められたら、クラスターの管理者資格情報を入力します。 これらの資格情報は、クラスターのプロビジョニング プロセス中に指定しました。

### <a name="manage-azure-subscriptions"></a>Azure サブスクリプションの管理
Azure Toolkit for IntelliJ の既定では、すべての Azure サブスクリプションからの Spark クラスターが一覧表示されます。 必要に応じて、クラスターにアクセスするサブスクリプションを指定できます。 

1. Azure Explorer で、**[Azure]** ルート ノードを右クリックし、**[Manage Subscriptions]\(サブスクリプションの管理\)** をクリックします。 
2. ダイアログ ボックスで、アクセスしないサブスクリプションのチェック ボックスをオフにし、**[Close]\(閉じる\)** をクリックします。 Azure サブスクリプションからサインアウトする場合は、**[Sign Out]\(サインアウト\)** をクリックすることもできます。

## <a name="run-a-spark-scala-application-locally"></a>Spark Scala アプリケーションのローカルでの実行
Azure Toolkit for IntelliJ を使用すると、ワークステーション上で Spark Scala アプリケーションをローカルに実行することができます。 通常、そのようなアプリケーションは、ストレージ コンテナーなどのクラスター リソースにアクセスする必要がなく、ローカルで実行しテストすることができます。

### <a name="prerequisite"></a>前提条件
Windows コンピューターでローカルの Spark Scala アプリケーションを実行中に、[SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) で説明されている例外が発生する場合があります。 この例外は、Windows 上に WinUtils.exe がないことが原因で発生します。 

このエラーを回避するには、[実行可能ファイルをダウンロード](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)して、**C:\WinUtils\bin** のような場所に保存する必要があります。 次に、環境変数 **HADOOP_HOME** を追加し、この変数の値を **C\WinUtils** に設定します。

### <a name="run-a-local-spark-scala-application"></a>ローカル Spark Scala アプリケーションの実行
1. IntelliJ IDEA を起動し、プロジェクトを作成します。 **[New Project]\(新しいプロジェクト\)** ダイアログ ボックスで、次の選択を行い、**[Next]\(次へ\)** をクリックします。
   
   * 左側のウィンドウで、**[HDInsight]** を選択します。
   * 右側のウィンドウで、**[Spark on HDInsight Local Run Sample (Scala)]\(HDInsight の Spark のローカル実行サンプル (Scala)\)** を選択します。
   
    ![[New Project]\(新しいプロジェクト\) ダイアログ ボックスでの選択](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)
2. 次のウィンドウでプロジェクトの詳細を次のように指定し、**[Finish]\(完了\)** をクリックします。
   
   * プロジェクト名とプロジェクトの場所を指定します。
   * **[Project SDK]\(プロジェクト SDK\)** では、必ず Java Version 7 以降を指定します。
   * **[Scala SDK]** では、**[Create]\(作成\)** をクリックし、**[Download]\(ダウンロード\)** をクリックして、使用する Scala のバージョン (Spark 2.0 の場合は Scala 2.11.x、Spark 1.6 の場合は Scala 2.10.x) を選択します。
     
       ![Scala バージョンの選択](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)
   * **[Spark SDK]** では、[GitHub](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409) から SDK をダウンロードして使用します。 代わりに [Spark Maven リポジトリ](http://mvnrepository.com/search?q=spark)を使用することもできますが、Spark アプリケーションの開発に適した Maven リポジトリがインストールされていることを確認してください。 たとえば、Spark ストリーミングを使用している場合は、Spark ストリーミング部分がインストールされていることを確認してください。 また、Spark 1.6 クラスターの場合は Scala 2.10 とマークされているリポジトリを、Spark 2.0 クラスターの場合は、Scala 2.11 とマークされているリポジトリを使用していることを確認してください。
     
       ![Spark SDK の選択](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-create-project.png)
3. テンプレートは、コンピューターでローカルに実行することができるサンプル コード (**LogQuery**) を **src** フォルダーの下に追加します。
   
    ![LogQuery の場所](./media/hdinsight-apache-spark-intellij-tool-plugin/local-app.png)
4. **LogQuery** アプリケーションを右クリックし、**[Run 'LogQuery']\('LogQuery' の実行\)** をクリックします。 下部の **[Run]\(実行\)** タブに、次のような出力が表示されます。
   
   ![Spark アプリケーションをローカルに実行した結果](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ を使用できるように既存の IntelliJ IDEA アプリケーションを変換する
Azure Toolkit for IntelliJ に対応するように、IntelliJ IDEA で作成した既存の Spark Scala アプリケーションを変換することができます。 その後、プラグインを使用して、そのアプリケーションを HDInsight Spark クラスターに送信できます。

1. IntelliJ IDEA で作成した既存の Spark Scala アプリケーションについては、関連付けられている .iml ファイルを開きます。
2. ルート レベルに、次のような **module** 要素が表示されます。
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
   **module** 要素が次のようになるように、この要素を編集して `UniqueKey="HDInsightTool"` を追加します。
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
3. 変更を保存します。 これで、アプリケーションは Azure Toolkit for IntelliJ との互換性を持つようになります。 これをテストするには、Project Explorer でプロジェクト名を右クリックします。 これで、ポップアップ メニューで、**[Submit Spark Application to HDInsight]\(HDInsight への Spark アプリケーションの送信\)** を選択できるようになります。

## <a name="troubleshooting"></a>トラブルシューティング
### <a name="please-use-a-larger-heap-size-error-in-local-run"></a>ローカル実行でエラーが発生しヒープ サイズを増やすように求められた場合
Spark 1.6 で 32 ビットの Java SDK を使用している場合、ローカル実行時に次のようなエラーが発生することがあります。

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

これらのエラーは、Spark を実行するのに十分なヒープ サイズがないために発生します  (Spark には少なくとも 471 MB のヒープが必要となります。 詳細については、[SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081) を参照してください)。 簡単な解決方法としては、64 ビットの Java SDK を使用することが考えられます。 または、IntelliJ で JVM 設定に以下のオプションを追加します。

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![IntelliJ の [VM options]\(VM オプション\) ボックスへのオプションの追加](./media/hdinsight-apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="feedback-and-known-issues"></a>フィードバックと既知の問題
現在、Spark の出力を直接表示する機能はサポートされていません。

ご提案やフィードバックがある場合、またはこのプラグインを使用していて問題が発生した場合は、hdivstool@microsoft.com に電子メールをお送りください。

## <a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Azure Toolkit for IntelliJ を使用して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for Eclipse の HDInsight ツールを使用して Spark アプリケーションを作成する](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)


