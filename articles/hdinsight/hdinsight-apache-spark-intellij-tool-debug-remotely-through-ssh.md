---
title: "Azure Toolkit for IntelliJ – ssh を使用して Spark アプリケーションをリモートでデバッグする | Microsoft Docs"
description: "Azure Toolkit for IntelliJ のHDInsight ツールを使用し、HDInsight クラスターで ssh によりアプリケーションをリモート デバッグする方法の手順を示すガイダンス"
keywords: "デバッグ、intellij のリモート デバッグ、ssh、intellij、hdinsight、intellij のデバッグ、デバッグ"
services: hdinsight
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 06/05/2017
ms.author: Jenny Jiang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 5ee6504cd4bf69c8f2c14a3623ff537e66030ce1
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="remotely-debug-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Azure Toolkit for IntelliJ を使用して HDInsight クラスター上で SSH により Spark アプリケーションをリモートでデバッグする

この記事では、Azure Toolkit for IntelliJ の HDInsight ツールを使って HDInsight クラスター上でアプリケーションをリモート デバッグするための詳細な手順を紹介します。 [ビデオ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)に従ってプロジェクトをデバッグできます。

**前提条件:**

* **Azure Toolkit for IntelliJ のHDInsight ツール**。 このツールは、Azure Toolkit for IntelliJ に付属しています。 詳細については、「 [Azure Toolkit for IntelliJ のインストール](https://docs.microsoft.com/en-us/azure/azure-toolkit-for-intellij-installation)」を参照してください。
* **Azure Toolkit for IntelliJ**。 HDInsight クラスター向けの Spark アプリケーションの作成に使用します。 詳細については、「[Azure Toolkit for IntelliJ を使用して HDInsight クラスター向けの Spark アプリケーションを作成する](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin)」を参照してください。
* **ユーザー名とパスワードを使って管理された HDInsight SSH サービス。** 詳細については、「[SSH を使用して HDInsight (Hadoop) に接続する](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)」と「[SSH トンネリングを使用して Ambari Web UI、JobHistory、NameNode、Oozie、およびその他の Web UI にアクセスする](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel)」を参照してください。 
 

## <a name="create-a-spark-scala-application-and-configure-it-for-remote-debugging"></a>Spark Scala アプリケーションを作成し、リモート デバッグ用に構成する
1. IntelliJ IDEA を起動し、プロジェクトを作成します。 [New Project (新規プロジェクト)] ダイアログ ボックスで、次の選択を行い、 **[Next (次へ)]**をクリックします。 この記事では、例として **HDInsight の Spark のクラスター実行サンプル (Scala)** を使用します。

     ![デバッグ プロジェクトを作成します。](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-create-projectfor-debug-remotely.png)
   - 左側のウィンドウで、**[HDInsight]** を選択します。
   - 右側のウィンドウで、設定に基づいて Java または Scala テンプレートを選択します。 以下のオプションのいずれかを選択します。 
      - **HDInsight の Spark (Scala)**
      - **HDInsight の Spark (Java)**
      - **HDInsight の Spark のクラスター実行サンプル (Scala)**
   - ビルド ツール: Scala プロジェクト作成ウィザードでは、依存関係の管理と Scala プロジェクトのビルドのために Maven または SBT をサポートしています。 必要に応じていずれかを選択してください。
2. 次のウィンドウで、プロジェクトの詳細を指定します。

   ![Spark SDK の選択](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-new-project.png)
   - プロジェクト名とプロジェクトの場所を指定します。
   - **[Project SDK]\(プロジェクト SDK\)** では、**Spark 2.x** クラスターの場合は **Java 1.8** を使用し、**Spark 1.x** クラスターの場合は **Java 1.7** を使用します。
   - **[Spark Version]\(Spark のバージョン\)** では、Scala プロジェクト作成ウィザードで Spark SDK と Scala SDK の適切なバージョンが統合されます。 Spark クラスターのバージョンが 2.0 より前の場合は、**Spark 1.x** を選択します。 それ以外の場合は、**Spark 2.x** を選択します。 この記事では、例として **Spark 2.0.2 (Scala 2.11.8)** を使用します。
3. **[src]** > **[main]** > **[scala]** を選択してプロジェクトのコードを開きます。 この記事では、例として **SparkCore_wasbloTest** スクリプトを使用します。
4. **[構成の編集]** メニューにアクセスするには、右上のアイコンを選択します。 このメニューでは、リモート デバッグの構成を作成または編集できます。

   ![構成の編集](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-edit-configurations.png) 
5. **[実行/デバッグ構成]** ウィンドウで、プラス記号 (**+**) をクリックします。 次に、**[Submit Spark Job]\(Spark ジョブの送信\)** を選択します。

   ![構成の編集](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-add-new-Configuration.png)
6. **[名前]**、**[Spark cluster]\(Spark クラスター\)**、**[Main class name]\(メイン クラス名\)** に情報を入力します。 次に、**[詳細構成]** を選択します。 

   ![デバッグ構成の実行](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-debug-configurations.png)
7. **[Spark Submission Advanced Configuration]\(Spark 送信の詳細構成\)** ダイアログ ボックスで、**[Enable Spark remote debug]\(Spark のリモート デバッグを有効化\)** を選択します。 SSH のユーザー名とパスワードを入力するか、秘密キー ファイルを使用します。 保存するには、**[OK]** をクリックします。

   ![[Enable Spark remote debug]\(Spark のリモート デバッグを有効化\)](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-enable-spark-remote-debug.png)
8. 指定した名前で構成が保存されます。 構成の詳細を表示するには、構成名を選択します。 変更するには、**[構成の編集]** を選択します。 
9. 構成の設定が完了したら、リモート クラスターでプロジェクトを実行したり、リモート デバッグを実行したりすることができます。

## <a name="learn-how-to-perform-remote-debugging-and-remote-run"></a>リモート デバッグとリモート実行の方法
### <a name="scenario-1-perform-remote-run"></a>シナリオ 1: リモートで実行する
1. プロジェクトをリモートで実行するには、**[実行]** アイコンを選択します。

   ![[実行] アイコンのクリック](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-icon.png)

2. **[HDInsight Spark Submission]\(HDInsight Spark の送信\)** ウィンドウにアプリケーションの実行状態が表示されます。 表示された情報に基づいて Scala ジョブの進行状況を監視できます。

   ![[実行] アイコンのクリック](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-result.png)

### <a name="scenario-2-perform-remote-debugging"></a>シナリオ 2: リモート デバッグを実行する
1. ブレークポイントを設定し、**[デバッグ]** アイコンを選択します。

    ![デバッグ アイコンのクリック](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-icon.png)
2. プログラムの実行がブレーク ポイントに達すると、下部ウィンドウに **[デバッガー]** タブが表示されます。 また、**[変数]** ウィンドウに表示パラメーターと変数の情報が表示されます。 **[ステップ オーバー]** アイコンをクリックして次のコード行に進みます。 以後、コードの先のステップに進むことができます。 **[Resume Program]\(プログラムの再開\)** アイコンを選択すると、コードの実行が続行されます。 **[HDInsight Spark Submission]\(HDInsight Spark の送信\)** ウィンドウで実行状態を確認できます。 

   ![[デバッグ] タブ](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>シナリオ 3: リモート デバッグを実行し、バグを修正する
このセクションでは、IntelliJ の簡易修正用デバッグ機能を使って、変数の値を動的に更新する方法を説明します。 次のコード例では、ターゲット ファイルが既に存在するため、例外が発生します。
  
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext

        object SparkCore_WasbIOTest {
          def main(arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkCore_WasbIOTest")
            val sc = new SparkContext(conf)
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            // Find the rows that have only one digit in the sixth column.
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)

            try {
              var target = "wasb:///HVACout2_testdebug1";
              rdd1.saveAsTextFile(target);
            } catch {
              case ex: Exception => {
                throw ex;
              }
            }
          }
        }


#### <a name="to-perform-remote-debugging-and-bug-fixing"></a>リモート デバッグを実行してバグを修正するには
1. ブレーク ポイントを 2 か所に設定し、**[デバッグ]** アイコンをクリックして、リモート デバッグの処理を開始します。

2. 最初のブレーク ポイントでコードが停止し、**[変数]** ウィンドウにパラメーターと変数の情報が表示されます。 

3. 続行するには、**[Resume Program]\(プログラムの再開\)** アイコンをクリックします。 2 番めのブレーク ポイントでコードが停止します。 例外が想定どおりに捕捉されます。

  ![エラーのスロー](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-throw-error.png) 

4. もう一度、**[Resume Program]\(プログラムの再開\)** アイコンをクリックします。 **[HDInsight Spark Submission]\(HDInsight Spark の送信\)** ウィンドウに、実行に失敗したジョブのエラーが表示されます。

  ![エラーの送信](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-error-submission.png) 

5. IntelliJ のデバッグ機能を使って変数の値を動的に更新するには、もう一度 **[デバッグ]** を選択します。 変数ウィンドウが再度表示されます。 

6. **[デバッグ]** タブでターゲットを右クリックし、**[値の設定]** を選択します。 次に、変数に新しい値を入力します。 **[Enter]\(確定\)** を選択して値を保存します。 

  ![値の設定](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-set-value.png) 

7. **[Resume Program]\(プログラムの再開\)** アイコンをクリックして、プログラムの実行を続行します。 今度は、例外は発生しません。 プロジェクトは例外なしに正常に実行されていることが表示されます。

  ![デバッグで例外が発生しない場合](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="demo"></a>デモ
* Scala プロジェクトの作成 (ビデオ): [Spark Scala アプリケーションの作成](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* リモート デバッグ (ビデオ): [Azure Toolkit for IntelliJ を使用して HDInsight クラスター上で Spark アプリケーションをリモートでデバッグする](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Azure Toolkit for IntelliJ の HDInsight ツールを使用して Spark Scala アプリケーションを作成して送信する](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for IntelliJ を使用して VPN により HDInsight Spark 上でアプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Hortonworks Sandbox と IntelliJ 用 HDInsight ツールを使用する](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Azure Toolkit for Eclipse の HDInsight ツールを使用して Spark アプリケーションを作成する](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)
 
