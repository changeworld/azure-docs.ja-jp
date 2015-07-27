<properties 
	pageTitle="HDInsight の Apache Spark で Azure Event Hubs を使用したストリーミング データの処理 | Azure" 
	description="Azure Event Hub にデータ ストリームを送信し、Zeppelin Notebook を使用して Spark でイベントを受信する方法の詳細な手順を説明します" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/10/2015" 
	ms.author="nitinme"/>


# Spark ストリーミング: HDInsight での Apache Spark による Azure Event Hubs からのイベントの処理

Spark ストリーミングは、コア Spark API を拡張して、スケーラビリティ、高スループット、フォールト トレランスを備えたストリーム処理アプリケーションを構築します。多くのソースからデータを取り込むことができます。この記事では、Event Hubs を使用してデータを取り込みます。Event Hubs は、拡張性に優れたインジェスト システムであり、1 秒間に数百万件のイベントを取り込むことができます。

このチュートリアルでは、Azure Event Hubs を作成する方法、C# のコンソール アプリケーションを使用して Event Hubs にメッセージを取り込む方法、および HDInsight で Apache Spark 用に構成されている Zeppelin Notebook を使用して並列にメッセージを取得する方法を説明します。

**前提条件:**

次のものが必要です。

- Azure サブスクリプション。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
- Apache Spark クラスター。手順については、「[Azure HDInsight での Apache Spark クラスターのプロビジョニング](hdinsight-apache-spark-provision-clusters.md)」をご覧ください。
- [Azure Event Hub](service-bus-event-hubs-csharp-ephcs-getstarted.md)。
- Microsoft Visual Studio 2013 がインストールされているワークステーション。手順については、「[Visual Studio のインストール](https://msdn.microsoft.com/library/e2h7fzkw.aspx)」をご覧ください。

##<a name="createeventhub"></a>Azure Event Hub の作成

1. [Azure ポータル](https://manage.windowsazure.com)から、**[新規作成]**、**[Service Bus]**、**[Event Hub]**、**[カスタム作成]** の順に選択します。

2. **[新しい Event Hub の追加]** 画面で **Event Hub 名**を入力し、ハブを作成する **[リージョン]** を選択して、新しい名前空間を作成するか、既存の名前空間を選択します。**矢印**をクリックして続行します。

	![ウィザード ページ 1](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Create.Event.Hub.png "Azure Event Hub を作成します")

	> [AZURE.NOTE]待機時間とコストを削減するために、HDInsight の Apache Spark クラスターと同じ **[場所]** を選択する必要があります。

3. **[Event Hub の構成]** 画面で、**[パーティション数]** と **[メッセージのリテンション期間]** の値を入力して、チェック マークをクリックします。この例では、パーティション カウントに 10 を、メッセージ保持に 1 を使用します。パーティション数を書き留めておきます。この値は後で必要になります。

	![ウィザード ページ 2](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Create.Event.Hub2.png "Event Hub のパーティション サイズおよびリテンション日数を指定します")

4. 作成した Event Hub をクリックし、**[構成]** をクリックして、Event Hub 用に 2 つのアクセス ポリシーを作成します。

	<table>
<tr><th>名前</th><th>アクセス許可</th></tr>
<tr><td>mysendpolicy</td><td>送信</td></tr>
<tr><td>myreceivepolicy</td><td>リッスン</td></tr>
</table>アクセス許可の作成後、ページの下部にある **[保存]** アイコンをクリックします。これにより、この Event Hub に対する送信 (**mysendpolicy**) とリッスン (**myreceivepolicy**) に使用する共有アクセス ポリシーが作成されます。

	![ポリシー](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policies.png "Event Hub のポリシーを作成します")

	
5. 同じページで、2 つのポリシーに対して生成されるポリシー キーを記録しておきます。後で使用するので、これらのキーを保存します。

	![ポリシー キー](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policy.Keys.png "ポリシー キーを保存します")

6. **[ダッシュボード]** ページで、下部にある **[接続情報]** をクリックして、2 つのポリシーを使用する Event Hub に接続文字列を取得し、保存します。

	![ポリシー キー](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policy.Connection.Strings.png "ポリシーの接続文字列を保存します")

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

##<a name="receivezeppelin"></a>Zeppelin を使用した HDInsight 上の Spark でのメッセージの取得

このセクションでは、[Zeppelin](https://zeppelin.incubator.apache.org) Notebook を作成して、Event Hub からのメッセージを HDInsight の Spark クラスターに受け取ります。

1. Zeppelin Notebook を起動します。Azure ポータルで Spark クラスターを選択し、下部にあるポータル タスク バーの **[Zeppelin Notebook]** をクリックします。入力を求められたら、Spark クラスターの管理者資格情報を入力します。表示されるページの指示に従って Notebook を起動します。

2. 新しい Notebook を作成します。ヘッダー ウィンドウで **[Notebook]** をクリックし、ドロップダウンから **[Note の新規作成]** をクリックします。

	![新しい Zeppelin Notebook を作成します](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.CreateNewNote.png "新しい Zeppelin Notebook を作成します")

	同じページの **[Notebook]** 見出しの下に、**Note XXXXXXXXX** で名前が始まる新しい Notebook が表示されます。新しい Notebook をクリックします。

3. 新しい Notebook の Web ページで、見出しをクリックし、必要に応じて Notebook の名前を変更します。Enter キーを押して名前の変更を保存します。また、Notebook のヘッダーの右上隅に **[接続]** というステータスが表示されることを確認します。

	![Zeppelin Notebook のステータス](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.NewNote.Connected.png "Zeppelin Notebook のステータス")

4. 新しい Notebook に既定で作成される空の段落に次のスニペットを貼り付け、Event Hub の構成を使用するようにプレースホルダーを置き換えます。このスニペットでは、Event Hub からストリームを受け取り、**mytemptable** という名前の一時テーブルにストリームを登録します。次のセクションでは、送信側アプリケーションを開始します。その後は、テーブルから直接データを読み取ることができます。

		import org.apache.spark.streaming.{Seconds, StreamingContext}
		import org.apache.spark.streaming.eventhubs.EventHubsUtils
		import sqlContext.implicits._
		
		val ehParams = Map[String, String](
		  "eventhubs.policyname" -> "<name of policy with listen permissions>",
		  "eventhubs.policykey" -> "<key of policy with listen permissions>",
		  "eventhubs.namespace" -> "<service bus namespace>",
		  "eventhubs.name" -> "<event hub in the service bus namespace>",
		  "eventhubs.partition.count" -> "1",
		  "eventhubs.consumergroup" -> "$default",
		  "eventhubs.checkpoint.dir" -> "/<check point directory in your storage account>",
		  "eventhubs.checkpoint.interval" -> "10"
		)
		
		val ssc =  new StreamingContext(sc, Seconds(10))
		val stream = EventHubsUtils.createUnionStream(ssc, ehParams)
		
		case class Message(msg: String)
		stream.map(msg=>Message(new String(msg))).foreachRDD(rdd=>rdd.toDF().registerTempTable("mytemptable"))

		stream.print
		ssc.start


##<a name="runapps"></a>アプリケーションの実行

1. Zeppelin Notebook から、スニペットを追加した段落を実行します。**Shift + Enter** キーを押すか、または右上隅にある **[プレイ]** ボタンをクリックします。

	段落の右上隅にあるステータスが、[準備完了]、[保留中]、[実行中]、[完了] の順に進行します。出力が同じ段落の下に表示されます。スクリーンショットは次のようになります。

	![スニペットの出力](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Zeppelin.Code.Output.png "スニペットの出力")

2. **Sender** プロジェクトを実行し、コンソール ウィンドウで **Enter** キーを押して Event Hub へのメッセージ送信を開始します。

3. Zeppelin Notebook の新しい段落に次のスニペットを入力し、Spark で受信したメッセージを読み取ります。

		%sql select * from mytemptable limit 10

	次のスクリーン キャプチャは、**mytemptable** で受信したメッセージです。

	![Zeppelin でメッセージを受信します](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Zeppelin.Output.png "Zeppelin Notebook でメッセージを受信します")

4. Spark SQL インタープリターを再起動して、アプリケーションを終了します。上部の **[インタープリター]** タブをクリックし、Spark インタープリターの **[再起動]** をクリックします。

	![Zeppelin インタープリターを再起動します](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Zeppelin.Restart.Interpreter.png "Zeppelin インタープリターを再起動します")

##<a name="sparkstreamingha"></a>高可用性のストリーミング アプリケーションの実行

Zeppelin を使用して HDInsight の Spark クラスターでストリーミング データを受け取るのは、アプリケーションのプロトタイプに適した方法です。ただし、高可用性と復元性を備えた実稼動環境セットアップでストリーミング アプリケーションを実行するには、以下を行う必要があります。

1. クラスターに関連付けられたストレージ アカウントに、依存関係の jar をコピーします。
2. ストリーミング アプリケーションをビルドします。
3. クラスターに RDP 接続し、クラスターのヘッドノードにアプリケーションの jar をコピーします。
3. クラスターに RDP 接続し、クラスター ノードでアプリケーションを実行します。

これらの手順を実行する方法の説明およびサンプルのストリーミング アプリケーションは、GitHub ([https://github.com/hdinsight/hdinsight-spark-examples](https://github.com/hdinsight/hdinsight-spark-examples)) からダウンロードできます。


##<a name="seealso"></a>関連項目


* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)
* [クイック スタート: HDInsight の Apache Spark のプロビジョニングと Spark SQL を使用した対話型クエリの実行](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [Machine Learning アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=July15_HO3-->