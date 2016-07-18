 <properties
	pageTitle="Eclipse 用 HDInsight プラグインを使用して Spark Scala アプリケーションを作成する | Microsoft Azure"
	description="スタンドアロン Scala アプリケーションを作成して HDInsight Spark クラスターで実行する方法を説明します。"
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2016"
	ms.author="nitinme"/>


# Eclipse 用の HDInsight Tools プラグインを使用して HDInsight Spark Linux クラスター向けの Spark アプリケーションを作成する

この記事では、Eclipse 用の HDInsight プラグインを使用して、Scala で記述された Spark アプリケーションを開発し、HDInsight Spark クラスターに送信するための詳細な手順を説明します。このプラグインは、次のような使い方があります。

* Scala Spark アプリケーションを開発して HDInsight Spark クラスターに送信する
* Azure HDInsight Spark クラスター リソースにアクセスする
* Scala Spark アプリケーションをローカルで開発して実行する

>[AZURE.IMPORTANT] このツールを使用すると、Linux 上で HDInsight Spark クラスター専用のアプリケーションを作成し、送信することができます。


##前提条件

* Azure サブスクリプション。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

* HDInsight Linux での Apache Spark クラスター。手順については、「[Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](hdinsight-apache-spark-jupyter-spark-sql.md)」を参照してください。

* Oracle Java Development Kit Version 7 および Version 8。
	* HDInsight クラスターは Java バージョン 7 をサポートしているため、Spark プロジェクトのコンパイルには **Java SDK 7** が使用されます。Java SDK 7 は、[ここ](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)からダウンロードできます。
	* Eclipse IDE ランタイムには、**Java SDK 8** が使用されます。[ここ](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)からダウンロードできます。

* Eclipse IDE。この記事では、Eclipse Neon を使用します。[ここ](https://www.eclipse.org/downloads/)からインストールできます。

* Eclipse 用 Scala IDE。
	* **Eclipse IDE をインストール済みの場合は**、Scala IDE プラグインを追加できます。追加するには、**[Help (ヘルプ)]**、**[Install New SoftWare (新しいソフトウェアのインストール)]** の順に移動し、Eclipse 用 Scala プラグインのダウンロード元として [http://download.scala-ide.org/sdk/lithium/e44/scala211/stable/site](http://download.scala-ide.org/sdk/lithium/e44/scala211/stable/site) を追加します。
	* **Eclipse IDE をインストールしていない場合**は、[こちら](http://scala-ide.org/download/sdk.html)から直接 Scala IDE をインストールできます。このリンクから .zip ファイルをダウンロードし、展開して **/eclipse** フォルダーに移動します。次に、そこから **eclipse.exe** ファイルを実行します。
	
	>[AZURE.NOTE] このドキュメントの手順は、Scala プラグインがインストールされている Eclipse IDE の使用を前提としています。

* Spark SDK。[ここ](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409)からダウンロードできます。

* e(fx)clipse を [https://www.eclipse.org/efxclipse/install.html](https://www.eclipse.org/efxclipse/install.html) からインストールしてください。


## Eclipse 用の HDInsight Tools プラグインをインストールする

1. Eclipse IDE を起動します。.zip パッケージをダウンロードして展開した場所に移動し、その下の **/eclipse** フォルダーに移動して、**eclipse.exe** をクリックします。ようこそ画面で **[Help (ヘルプ)]** をクリックし、**[Install New Software (新しいソフトウェアのインストール)]** をクリックします。

	![HDInsight プラグインのインストール](./media/hdinsight-apache-spark-eclipse-tool-plugin/install-hdinsight-plugin-1.png)

2. 次の画面の **[Work with(連携)]** ボックスに「**http://dl.microsoft.com/eclipse**」と入力し、**Enter** キーを押します。**[Azure Toolkit for Java]** を選択し、**[Contact all update sites during install to find required software (必要なソフトウェアを検索するためにインストール中にすべての更新サイトに接続する)]** チェック ボックスをオフにして、**[Next (次へ)]** をクリックします。

	![HDInsight プラグインのインストール](./media/hdinsight-apache-spark-eclipse-tool-plugin/install-hdinsight-plugin-2.png)

3. **[Install Details (インストールの詳細)]** ダイアログ ボックスで、インストールされるコンポーネントを確認し、**[Next (次へ)]** をクリックします。

4. **[Review Licenses (ライセンスの確認)]** ダイアログ ボックスでライセンス契約の条項に同意し、**[Finish (完了)]** をクリックします。

5. インストールが完了すると、Eclipse の再起動を求めるメッセージが表示されます。ダイアログ ボックスの **[Yes (はい)]** をクリックして、Eclipse を再起動します。

## Azure サブスクリプションにログインする

1. Azure Explorer を開きます。IDE の **[Window (ウィンドウ)]** メニューから、**[Show View (ビューの表示)]** をクリックし、**[Other (その他)]** をクリックします。表示されたダイアログ ボックスで **[Azure]** を展開し、**[Azure Explorer]** をクリックして、**[OK]** をクリックします。

	![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-1.png)

2. **Azure Explorer** で **[Azure]** ノードを右クリックし、**[Manage Subscriptions (サブスクリプションの管理)]** をクリックします。

3. **[Manage Subscriptions (サブスクリプションの管理)]** ダイアログ ボックスで **[Sign in (サインイン)]** をクリックし、Azure 資格情報を入力します。

	![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-2.png)

4. ログイン後、**[Manage Subscriptions (サブスクリプションの管理)]** ダイアログ ボックスには、資格情報に関連付けられているすべての Azure サブスクリプションの一覧が表示されます。ダイアログ ボックスの **[Close (閉じる)]** をクリックします。

5. [Azure Explorer] タブで **[HDInsight]** を展開し、自分のサブスクリプションの下にある HDInsight Spark クラスターを表示します。

	![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-3.png)

6. クラスター名のノードをさらに展開すると、そのクラスターに関連付けられているリソース (ストレージ アカウントなど) を表示できます。

	![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## HDInsight Spark クラスター用の Spark Scala プロジェクトを設定する

1. Eclipse IDE の作業領域で、**[File (ファイル)]**、**[New (新規)]**、**[Project (プロジェクト)]** の順にクリックします。

2. **[New Project (新しいプロジェクト)]** ウィザードで、**[HDInsight]** を展開して、**[Spark on HDInsight (Scala) (HDInsight の Spark (Scala))]** を選択し、**[Next (次へ)]** をクリックします。

	![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)

3. **[New HDInsight Scala Project (新しい HDInsight Scala プロジェクト)]** ダイアログ ボックスで、下の図に示すように値を入力または選択し、**[Next (次へ)]** をクリックします。

	![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

	* プロジェクトの名前を入力します。
	* **[JRE]** ボックスで、**[Use an execution environment JRE (実行環境 JRE を使用する)]** が **[JavaSE-1.7]** に設定されていることを確認します。
	* Spark SDK が、SDK をダウンロードした場所に設定されていることを確認します。ダウンロード場所へのリンクは、このトピックで前述した「[前提条件](#prerequisites)」にあります。上の図に示すように、このダイアログ ボックスにあるリンクから SDK をダウンロードすることもできます。

4. 次のダイアログ ボックスで **[Libraries (ライブラリ)]** タブをクリックし、**[JRE System Library [JavaSE-1.7] \(JRE システム ライブラリ [JavaSE 1.7])]** をダブルクリックします。

	![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-4.png)

5. **[Edit Library (ライブラリの編集)]** ダイアログ ボックスで、**[Execution Environment (実行環境)]** が **[JavaSE-1.7(jdk1.7.0\_79)]** に設定されていることを確認します。これをオプションとして選択できない場合は、以下の手順に従ってください。

	1. **[Alternate JRE (代替 JRE)]** オプションを選択し、**[JavaSE-1.7(jdk1.7.0\_79)]** が利用可能かどうかを確認します。
	2. 利用できない場合は、**[Installed JREs (インストールされている JRE)]** ボタンをクリックします。

		  ![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-5.png)

	3. **[Installed JREs (インストールされている JRE)]** ダイアログ ボックスで、**[Add (追加)]** をクリックします。

		  ![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-6.png)

	4. **[JRE Type (JRE の種類)]** ダイアログ ボックスで **[Standard VM (標準 VM)]** を選択し、**[Next (次へ)]** をクリックします。

		  ![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-7.png)

	5. **[JRE Definition (JRE の定義)]** ダイアログ ボックスで [Directory (ディレクトリ)] をクリックし、JDK 7 のインストールの場所に移動して、**jdk1.7.0\_79** のルート フォルダーを選択します。

		  ![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-8.png)

	6. **[完了]** をクリックします。**[Installed JREs (インストールされている JRE)]** ダイアログ ボックスで、新しく追加された JRE を選択し、**[OK]** をクリックします。

		   ![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-9.png)

	7. 新しく追加された JRE が **[Execution Environment (実行環境)]** の一覧に表示されます。**[完了]** をクリックします。

	  	   ![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-10.png)

6. **[Libraries (ライブラリ)]** タブに戻り、**[Scala Library Container[2.11.8]]** をダブルクリックします。**[Edit Library(ライブラリの編集)]** ダイアログ ボックスで、**[Fixed Scala Library container:2.10.6 (修正された Scala ライブラリ コンテナー:2.10.6)]** を選択します。

	![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-11.png)

	プロジェクト設定のダイアログ ボックスが閉じられるまで、**[Finish (完了)]** をクリックします。

## HDInsight Spark クラスター向けの Scala アプリケーションを作成する

1. 既に開かれている Eclipse IDE の **Package Explorer** で、前に作成したプロジェクトを展開し、**[src]** を右クリックして **[New (新規)]** をポイントし、**[Other (その他)]** をクリックします。

2. **[Select a wizard (ウィザードの選択)]** ダイアログ ボックスで **[Scala Wizards (Scala ウィザード)]** を展開し、**[Scala Object (Scala オブジェクト)]**、**[Next (次へ)]** の順にクリックします。

	![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)

3. **[Create New File (新しいファイルの作成)]** ダイアログ ボックスでオブジェクトの名前を入力し、**[Finish (完了)]** をクリックします。

	![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)

4. テキスト エディターで次のコードを貼り付けます。

		import org.apache.spark.SparkConf
		import org.apache.spark.SparkContext
	
		object MyClusterApp{
		  def main (arg: Array[String]): Unit = {
		    val conf = new SparkConf().setAppName("MyClusterApp")
		    val sc = new SparkContext(conf)
		
		    val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		    //find the rows which have only one digit in the 7th column in the CSV
		    val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
		
		    rdd1.saveAsTextFile("wasb:///HVACOut")
		  }		
		}


5. HDInsight Spark クラスターでアプリケーションを実行します。

	1. **Package Explorer** で、プロジェクト名を右クリックし、**[Submit Spark Application to HDInsight (HDInsight への Spark アプリケーションの送信)]** を選択します。

	2. **[Spark Submission (Spark 送信)]** ダイアログ ボックスで、次の値を入力します。

		* **[Cluster Name (クラスター名)]** で、アプリケーションを実行する HDInsight Spark クラスターを選択します。

		* Eclipse プロジェクトからアーティファクトを選択するか、ハード ディスクからアーティファクトを選択する必要があります。

		* **[Main class name (メイン クラス名)]** テキスト ボックスに、コードで指定したオブジェクトの名前を入力します (下図を参照)。

			![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)

		* この例のアプリケーション コードでは、コマンドライン引数を必要とせず、JAR またはファイルを参照することもないので、残りのテキスト ボックスは空のままでかまいません。

		* **[Submit]** をクリックします。

	3. **[Spark Submission (Spark 送信)]** タブで、進行状況の表示が開始されます。[Spark Submission (Spark 送信)] ウィンドウにある赤いボタンをクリックして、アプリケーションを停止できます。地球アイコン (図では青のボックスで示されています) をクリックして、この特定のアプリケーションの実行に関するログを表示することもできます。

        ![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)

    次のセクションでは、Eclipse 用の HDInsight プラグインを使用してジョブ出力にアクセスする方法を説明します。


## Eclipse 用の HDInsight プラグインを使用して HDInsight Spark クラスターにアクセスして管理する

HDInsight プラグインを使用してさまざまな操作を実行できます。

### クラスターのストレージ コンテナーにアクセスする

1. Azure Explorer で **[HDInsight]** ルート ノードを展開して、使用できる HDInsight Spark クラスターの一覧を表示します。

3. クラスター名を展開して、ストレージ アカウントと、クラスターの既定のストレージ コンテナーを表示します。

	![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-5.png)

4. クラスターに関連付けられているストレージ コンテナー名をクリックします。右側のウィンドウに、**HVACOut** という名前のフォルダーが表示されます。フォルダーをダブルクリックして開くと、**part-*** ファイルが表示されます。それらのファイルのいずれかを開いて、アプリケーションの出力を確認します。

### Spark History Server へのアクセス

1. **Azure Explorer** で、Spark クラスター名を右クリックし、**[Open Spark History UI (Spark 履歴 UI を開く)]** を選択します。入力を求められたら、クラスターの管理者資格情報を入力します。これらは、クラスターのプロビジョニング時に指定済みである必要があります。

2. Spark History Server ダッシュ ボードでは、実行が終了したばかりのアプリケーションを、アプリケーション名によって探すことができます。上記のコードでは、`val conf = new SparkConf().setAppName("MyClusterApp")` を使用してアプリケーション名を設定しました。したがって、Spark アプリケーション名は **MyClusterApp** です。

### Ambari ポータルの起動

**Azure Explorer** で、Spark クラスター名を右クリックし、**[Open Cluster Management Portal (Ambari) (クラスター管理ポータルを開く (Ambari))]** を選択します。入力を求められたら、クラスターの管理者資格情報を入力します。これらは、クラスターのプロビジョニング時に指定済みである必要があります。

### Azure サブスクリプションの管理

既定では、HDInsight プラグインは、すべての Azure サブスクリプションからの Spark クラスターを一覧表示します。必要に応じて、クラスターにアクセスするサブスクリプションを指定できます。**Azure Explorer** で、**[Azure]** ルート ノードを右クリックし、**[Manage Subscriptions (サブスクリプションの管理)]** をクリックします。ダイアログ ボックスで、アクセスしないサブスクリプションに対するチェック ボックスをオフにし、**[Close (閉じる)]** をクリックします。Azure サブスクリプションからログオフする場合は、**[Sign Out (サインアウト)]** もクリックします。


## Spark Scala アプリケーションのローカルでの実行

Eclipse 用の HDInsight Tools プラグインを使用すると、ワークステーション上で Spark Scala アプリケーションをローカルに実行することができます。通常、そのようなアプリケーションは、ストレージ コンテナーなどのクラスター リソースにアクセスする必要がなく、ローカルで実行しテストすることができます。

### 前提条件

Windows コンピューターでローカルの Spark Scala アプリケーションを実行中に、[SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) で説明されているような例外が発生する場合があります (これは、Windows OS 上に **WinUtils.exe** がないことが原因です)。このエラーを回避するには、[実行可能ファイルをここからダウンロード](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)して、**C:\\WinUtils\\bin** などの場所に保存する必要があります。次に、環境変数 **HADOOP\_HOME** を追加し、この変数の値を **C\\WinUtils** に設定します。

### ローカル Spark Scala アプリケーションの実行	 

1. Eclipse を起動し、新しいプロジェクトを作成します。[New Project (新規プロジェクト)] ダイアログ ボックスで、次の選択を行い、**[Next (次へ)]** をクリックします。

	![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

	* 左側のウィンドウで、**[HDInsight]** を選択します。
	* 右側のウィンドウで、**[Spark on HDInsight Local Run Sample (Scala) (HDInsight の Spark のローカル実行サンプル (Scala))]** を選択します。
	* **[次へ]** をクリックします。

2. プロジェクトの詳細を指定するには、前のセクション「[HDInsight Spark クラスター用の Spark Scala プロジェクトを設定する](#set-up-a-spark-scala-application-project-for-an-hdinsight-spark cluster)」の手順 3. ～ 6. に従います。

3. テンプレートは、コンピューターでローカルに実行することができるサンプル コード (**LogQuery**) を **src** フォルダーの下に追加します。

	![Spark Application local run result](./media/hdinsight-apache-spark-eclipse-tool-plugin/local-app.png)

4.  **LogQuery** アプリケーションを右クリックし、**[Run As (実行者)]** をポイントして、**[1 Scala Application (1 Scala アプリケーション)]** をクリックします。下部の **[Console (コンソール)]** タブに次のような出力が表示されます。

	![Spark Application local run result](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## フィードバックと既知の問題

現在、Spark の出力を直接表示することはサポートされておらず、その実現に取り組んでいます。

ご提案やフィードバックがある場合、またはこのツールを使用していて問題が発生した場合は、hdivstool@microsoft.com に電子メールをお送りください。

## <a name="seealso"></a>関連項目


* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### シナリオ

* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)

* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)

* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### アプリケーションの作成と実行

* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)

* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### ツールと拡張機能

* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (Linux)](hdinsight-apache-spark-intellij-tool-plugin.md)

* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### リソースの管理

* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)

* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0706_2016-->