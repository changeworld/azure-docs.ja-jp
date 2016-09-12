<properties 
	pageTitle="HDInsight の Apache Spark の既知の問題 | Microsoft Azure" 
	description="HDInsight の Apache Spark の既知の問題" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/25/2016" 
	ms.author="nitinme"/>

# HDInsight Linux の Apache Spark クラスターの既知の問題

このドキュメントでは、HDInsight Spark パブリック プレビューのすべての既知の問題を追跡します。

##Livy が対話型セッションをリークする
 
対話型セッションがまだ有効な状態で Livy が再起動されると (Ambari から、またはヘッドノード 0 仮想マシンの再起動のため)、対話型ジョブ セッションがリークされます。このため、新しいジョブは受け付け済み状態のままになり、起動できません。

**対応策:**

この問題を回避するには、以下の手順を実行します。

1. ヘッドノードに Ssh します。
2. 次のコマンドを実行して、Livy を通じて開始された対話型ジョブのアプリケーション ID を調べます。

        yarn application –list

    明示的に名前を指定せずに、Livy 対話型セッションによってジョブが開始された場合、既定のジョブ名は Livy です。Jupyter Notebook によって開始された Livy セッションでは、ジョブ名は remotesparkmagics\_* で始まります。

3. 次のコマンドを実行して、これらのジョブを強制終了します。

        yarn application –kill <Application ID>

新しいジョブの実行が開始されます。

##Spark History Server が開始されない 

クラスターの作成後、Spark History Server は自動的には開始されません。

**対応策:**

Ambari から履歴サーバーを手動で開始します。

## Spark ログ ディレクトリでアクセス許可の問題が発生する 

hdiuser が spark-submit でジョブを送信すると、java.io.FileNotFoundException: /var/log/spark/sparkdriver\_hdiuser.log (アクセス許可が拒否されました) というエラーになり、ドライバー ログは書き込まれません。

**対応策:**
 
1. hdiuser を Hadoop グループに追加します。
2. クラスターの作成後、/var/log/spark に 777 アクセス許可を指定します。
3. Ambari を使用して、Spark ログの場所を、777 アクセス許可を持つディレクトリに変更します。
4. sudo として spark-submit を実行します。

## Jupyter Notebook に関連する問題

Jupyter Notebook に関連する既知の問題を以下に示します。


### ファイル名での非 ASCII 文字の使用

Spark HDInsight クラスターで使用できる Jupyter Notebook では、ファイル名に非 ASCII 文字を使用することはできません。Jupyter UI を使用して、ファイル名に非 ASCII 文字が含まれたファイルをアップロードしようとすると、通知されずに失敗します (つまり、Jupyter でファイルのアップロード操作ができないわけではありませんが、明確なエラーがスローされるわけでもありません)。

### 大きなサイズの Notebook の読み込み中のエラー

大きなサイズの Notebook の読み込み中にエラー **`Error loading notebook`** が発生する場合があります。

**対応策:**

このエラーが発生した場合、データが壊れたり失われたりしているわけではありません。Notebook はディスク上の `/var/lib/jupyter` に残っているため、クラスターに SSH 接続し、Notebook にアクセスすることができます。バックアップとしてクラスターの Notebook をローカル コンピューター (SCP または WinSCP を使用) にコピーすることで、Notebook 内の重要なデータが失われるのを防ぐことができます。ポート 8001 のヘッドノードへの SSH トンネルを使用すると、ゲートウェイを経由せずに Jupyter にアクセスできます。そこでは、Notebook の出力をクリアしてから再度保存して、Notebook のサイズを最小限に縮小できます。

今後このエラーが発生しないようにするには、次のベスト プラクティスを実行する必要があります。

* Notebook のサイズを小さく保つことが重要です。Jupyter に返送される、Spark ジョブからの出力は Notebook に保持されます。Jupyter でのベスト プラクティスは一般に次のとおりです。大規模な RDD またはデータフレームに対して `.collect()` を実行することは避けます。RDD の内容を参照する場合は、出力が大きくなり過ぎないように `.take()` または `.sample()` の実行を検討します。
* また、Notebook を保存する場合は、出力セルをすべてクリアしてサイズを縮小します。

### Notebook の初期スタートアップに予想より時間がかかる 

Spark マジックを使用した Jupyter Notebook の最初のコード ステートメントには、1 分以上かかる場合があります。

**説明:**
 
これは、最初のコード セルが実行されるタイミングのために発生します。バック グラウンドで、これにより、セッション構成が開始され、Spark、SQL、および Hive コンテキストが設定されます。これらのコンテキストが設定された後に、最初のステートメントが実行されるので、ステートメントの完了までに時間がかかるような印象を受けます。

### Jupyter Notebook がセッションの作成中にタイムアウトする

Spark クラスターがリソース不足になると、Jupyter Notebook の Spark カーネルと Pyspark カーネルは、セッションを作成する試行をタイムアウトにします。

**対応策:**

1. 以下の方法で、Spark クラスターのリソースを解放します。

    - [閉じて停止] メニューに移動するか、Notebook エクスプローラーで [シャットダウン] をクリックして、他の Spark Notebook を停止する。
    - YARN から他の Spark アプリケーションを停止する。

2. スタートアップしようとしていた Notebook を再起動します。今度は、セッションを作成するために十分なリソースが使用可能になっている必要があります。

##関連項目

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

<!---HONumber=AcomDC_0831_2016-->