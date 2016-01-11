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
	ms.date="12/22/2015" 
	ms.author="jgao"/>

# Azure HDInsight (Linux) の Apache Spark の既知の問題

このドキュメントでは、Spark パブリック プレビューのすべての既知の問題を追跡します。

##Livy が対話型セッションをリークする
 
**症状:**

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

**症状:**
 
クラスターの作成後、Spark History Server は自動的には開始されません。

**対応策:**

Ambari から履歴サーバーを手動で開始します。

##Notebook の初期スタートアップに予想より時間がかかる 

**症状:**

Spark Magic を使用する Jupyter Notebook の最初のステートメントは、1 分以上かかる場合があります。

**対応策:**
 
回避方法はありません。1 分ほどかかる場合があります。

##コア構成やメモリ構成をカスタマイズできない

**症状:**
 
Spark/Pyspark カーネルの既定の構成とは異なるコア構成やメモリ構成を指定することはできません。

**対応策:**
 
この機能は準備中です。

##Jupyter Notebook がセッションの作成中にタイムアウトする

**症状:**

Spark クラスターがリソース不足になると、Jupyter Notebook の Spark カーネルと Pyspark カーネルは、セッションを作成する試行をタイムアウトにします。対応策:

1. 以下の方法で、Spark クラスターのリソースを解放します。

    - [閉じて停止] メニューに移動するか、Notebook エクスプローラーで [シャットダウン] をクリックして、他の Spark Notebook を停止します。
    - YARN から他の Spark アプリケーションを停止します。

2. スタートアップしようとしていた Notebook を再起動します。今度は、セッションを作成するために十分なリソースが使用可能になっている必要があります。

##Notebook の出力結果の形式に問題がある

**症状:**
 
Notebook の出力結果が、Spark および Pyspark の Jupyter カーネルのセルを実行した後で、正しくない形式になります。これには、セル実行の成功した結果も、Spark stacktrace などのエラーも含まれます。

**対応策:**
 
この問題は、今後のリリースで対処されます。

##サンプル Notebook に誤記がある
 
- **Python notebook 4 (Analyze logs with Spark using a custom library)**

    "Let us assume you copy it over to wasb:///example/data/iislogparser.py" は "Let us assume you copy it over to wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py" が正しい記述です。

- **Python notebook 5 (Spark Machine Learning - Predictive analysis on food inspection data using MLLib)**

    "A quick visualization can help us reason about the distribution of these outcomes" に、実行できない誤ったコードがいくつかあります。次のように修正する必要があります。

        countResults = df.groupBy('results').count().withColumnRenamed('count', 'cnt').collect() 
        labels = [row.results for row in countResults] 
        sizes = [row.cnt for row in countResults] 
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral'] 
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors) plt.axis('equal') 
        
- **Python notebook 5 (Spark Machine Learning - Predictive analysis on food inspection data using MLLib)**

    最後のコメントに、偽陰性率と偽陽性率がそれぞれ 12.6% と 16.0% であると記載されています。これらの数値は不正確です。正しいパーセンテージの円グラフを表示するには、コードを実行してください。

- **Python notebooks 6 および 7**

    最初のセルが、Notebook の終了時に呼び出されるように sc.stop() メソッドを登録することに失敗します。特定の状況では、このために Spark リソースがリークする場合があります。この問題を回避するには、これらの Notebook で、終了前に import atexit; atexit.register(lambda: sc.stop()) を実行します。誤ってリソースをリークした場合は、上の指示に従って、リークした YARN アプリケーションを強制終了します。
     
## Spark ログ ディレクトリでアクセス許可の問題が発生する 

**症状:**
 
hdiuser が spark-submit でジョブを送信すると、java.io.FileNotFoundException: /var/log/spark/sparkdriver\_hdiuser.log (アクセス許可が拒否されました) というエラーになり、ドライバー ログは書き込まれません。

**対応策:**
 
1. hdiuser を Hadoop グループに追加します。 
2. クラスターの作成後、/var/log/spark に 777 アクセス許可を指定します。 
3. Ambari を使用して、Spark ログの場所を、777 アクセス許可を持つディレクトリに変更します。  
4. sudo として spark-submit を実行します。 

##関連項目

- [概要: Azure HDInsight (Linux) での Apache Spark](hdinsight-apache-spark-overview.md)
- [概要: Azure HDInsight (Linux) の Apache Spark のプロビジョニングと Spark SQL を使用した対話型クエリの実行](hdinsight-apache-spark-jupyter-spark-sql.md)

<!---HONumber=AcomDC_1223_2015-->