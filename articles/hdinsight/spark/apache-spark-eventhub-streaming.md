---
title: 'チュートリアル: Azure HDInsight での Apache Spark を使用した Azure Event Hubs からのデータの処理 '
description: Azure HDInsight で Apache Spark を Azure Event Hubs に接続し、ストリーミング データを処理します。
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.custom: hdinsightactive,mvc
ms.topic: conceptual
ms.date: 06/14/2018
ms.openlocfilehash: 27c8a51ee3f0274489041f4dafbbf73d906e2fa4
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617648"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-spark-in-hdinsight"></a>チュートリアル: HDInsight での Azure Event Hubs と Spark を使用したツイートの処理

このチュートリアルでは、ツイートを Azure イベント ハブに送信する Apache Spark ストリーミング アプリケーションを作成し、イベント ハブからツイートを読み取る別のアプリケーションを作成する方法を説明します。 Spark ストリーミングの詳細については、[Apache Spark ストリーミングの概要](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview)を参照してください。 HDInsight は、Azure の Spark クラスターに同じストリーミング機能をもたらします。

このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Azure イベント ハブにメッセージを送信する
> * Azure イベント ハブからメッセージを読み取る

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

* **記事「[チュートリアル: Azure HDInsight での Apache Spark クラスターへのデータの読み込みとクエリの実行](./apache-spark-load-data-run-query.md)**」を完了します。

## <a name="create-a-twitter-application"></a>Twitter アプリケーションを作成する

ツイートのストリームを受け取るには、Twitter でアプリケーションを作成します。 手順に従って Twitter アプリケーションを作成し、このチュートリアルの完了に必要な値を書き留めます。

1. [Twitter Application Management](https://apps.twitter.com/) を参照します。
2. **[Create New App]** を選択します。
3. 次の値を指定します。

    - Name: アプリケーション名を指定します。 このチュートリアルで使用されている値は **HDISparkStreamApp0423** です。 この名前は、一意の名前にする必要があります。
    - Description: アプリケーションの簡単な説明を入力します。 このチュートリアルで使用されている値は **A simple HDInsight Spark streaming application** です。
    - Website: アプリケーションの Web サイトを指定します。 有効な Web サイトである必要はありません。  このチュートリアルで使用されている値は **http://www.contoso.com** です。
    - Callback URL: 空白のままにしてかまいません。

4. **[Yes, I have read and agree to the Twitter Developer Agreement]** を選択し、**[Create your Twitter application]** を選択します。
5. **[Keys and Access Tokens]** タブをクリックします。
6. ページの最後の **[Create my access token]** を選択します。
7. ページから、次の値を書き留めます。  これらの値は、このチュートリアルの後の方で必要になります。

    - **コンシューマー キー (API キー)**    
    - **コンシューマー シークレット (API シークレット)**  
    - **Access Token**
    - **アクセス トークン シークレット**   

## <a name="create-an-azure-event-hub"></a>Azure Event Hub を作成します

このイベント ハブを使用して、ツイートを格納します。

1. [Azure Portal](https://ms.portal.azure.com) にサインインします。
2. 画面の左上の **[Create a resource] (リソースの作成)** を選択します。
3. **[モノのインターネット]**、**[Event Hubs]** の順に選択します。

    ![Spark ストリーミング サンプルの Event Hub を作成](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Spark ストリーミング サンプルの Event Hub を作成")
4. 新しいイベント ハブの名前空間に次の値を入力します。

    - **名前**: イベント ハブの名前を入力します。  このチュートリアルで使用されている値は **myeventhubns20180403** です。
    - **価格レベル**: **[Standard]** を選択します。
    - **リソース グループ**: 新規作成するか、または Spark クラスターのリソース グループを選択するオプションがあります。 
    - **場所**: 待機時間とコストを削減するために、HDInsight の Apache Spark クラスターと同じ **[場所]** を選択する必要があります。

    ![Spark ストリーミング サンプルの Event Hub 名を指定](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Spark ストリーミング サンプルの Event Hub 名を指定")
5. **[作成]** をクリックして、名前空間を作成します。

6. 次の手順を使用して、イベント ハブの名前空間を開きます。

    1. Portal で **[すべてのサービス]** を選択します。
    2. フィルター ボックスに、「**イベント ハブ**」と入力します。
    3. 作成した名前空間をダブルクリックします。
    4. **[+ イベント ハブ]** を選択します。

6. Event Hubs 名前空間の一覧で、新しく作成された名前空間を選択します。      
5. **[Event Hubs]** を選択し、**[+ イベント ハブ]** を選択して、新しいイベント ハブを作成します。
  

6. 次の値を入力します。

    - 名前: イベント ハブの名前を指定します。
    - パーティション数: 10
    - メッセージの保持期間： 1。 
   
    ![Spark ストリーミング サンプルの Event Hub 詳細を指定](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Spark ストリーミング サンプルの Event Hub 詳細を指定")

7. **作成**を選択します。
8. 名前空間の **[共有アクセス ポリシー]** (イベント ハブの共有アクセス ポリシーではありません) を選択し、**[RootManageSharedAccessKey]** を選択します。
    
     ![Spark ストリーミング サンプルの Event Hub ポリシーを設定](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Spark ストリーミング サンプルの Event Hub ポリシーを設定")

9. **[主キー]** と **[接続文字列 - プライマリ キー]** の値を、チュートリアルの後半で使用するために保存します。

     ![Spark ストリーミング例の Event Hub ポリシー キーを表示](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Spark ストリーミング例の Event Hub ポリシー キーを表示")


## <a name="send-tweets-to-the-event-hub"></a>ツイートをイベント ハブに送信する

Jupyter Notebook を作成し、それに **SendTweetsToEventHub** と名付ける必要があります。 

1. 次のコードを実行して、外部 Maven ライブラリを追加します。

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. 次のコードを実行して、イベント ハブにツイートを送信します。

    ```
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._
    
    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)
    
    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }
    
    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours   
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"
    
    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)
    
    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!
    
    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }
    
    // Closing connection to the Event Hub
    eventHubClient.get().close()
    ```

3. Azure Portal で、イベント ハブを開きます。  **[概要]** で、イベント ハブに送信されたメッセージを示すいくつかのグラフが表示されます。

## <a name="read-tweets-from-the-event-hub"></a>イベント ハブからツイートを読み取る

別の Jupyter Notebook を作成し、**ReadTweetsFromEventHub** と名付ける必要があります。 

1. 次のコードを実行して、外部 Maven ライブラリを追加します。

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```
2. 次のコードを実行して、イベント ハブからのツイートを読み取ります。

    ```
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    val customEventhubParameters = EventHubsConf(connStr).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema    
    
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")
    
    messages.printSchema
    
    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

HDInsight を使用すると、データは Azure Storage または Azure Data Lake Store に格納されるため、クラスターは、使用されていない場合に安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。 すぐに次のチュートリアルに取り掛かる場合は、クラスターを保持することができます。

Azure Portal で、クラスターを開き、**[削除]** を選択します。

![HDInsight クラスターの削除](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "HDInsight クラスターの削除")

リソース グループ名を選択し、リソース グループ ページを開いて、**[リソース グループの削除]** を選択することもできます。 リソース グループを削除すると、HDInsight Spark クラスターと既定のストレージ アカウントの両方が削除されます。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

* イベント ハブからメッセージを読み取る。
次の記事に進み、Machine Learning アプリケーションを作成できることを確認します。 

> [!div class="nextstepaction"]
> [Machine Learning アプリケーションの作成](./apache-spark-ipython-notebook-machine-learning.md)


