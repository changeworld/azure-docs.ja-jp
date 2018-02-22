---
title: "Azure HDInsight で Event Hubs と共に Apache Spark 構造化ストリーミングを使用する | Microsoft Docs"
description: "Apache Spark ストリーミング サンプルを作成して、Azure Event Hub にデータ ストリームを送信し、Scala アプリケーションを使用して HDInsight Spark クラスターでイベントを受信します。"
keywords: "apache spark ストリーミング,spark ストリーミング,spark サンプル,apache spark ストリーミング例,event hub azure サンプル,spark サンプル"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jgao
ms.openlocfilehash: 14cc32c22653d5d8bd3dd5a1a41d2f64cfd8c73c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="apache-spark-structured-streaming-on-hdinsight-to-process-events-from-event-hubs"></a>HDInsight の Apache Spark 構造化ストリーミングで Event Hubs からのイベントを処理する

この記事では、Spark 構造化ストリーミングを使ったリアルタイム テレメトリの処理について説明します。 この処理のための大まかな手順は次のとおりです。

1. Event Hubs に送信するイベントの生成元となるサンプルのイベント プロデューサー アプリケーションをローカル ワークステーションでコンパイルして実行します。
2. [Spark シェル](apache-spark-shell.md)で簡単な Spark 構造化ストリーミング アプリケーションを定義して実行します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

* HDInsight での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。

* Oracle Java Development kit。 [こちら](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)からインストールできます。

* Apache Maven。 [ここ](https://maven.apache.org/download.cgi)からダウンロードできます。 Maven をインストールする方法については、[こちら](https://maven.apache.org/install.html)を参照してください。

## <a name="build-configure-and-run-the-event-producer"></a>イベント プロデューサーのビルド、構成、実行
このタスクでは、ランダムなイベントを作成して構成済みのイベント ハブに送信するサンプル アプリケーションを複製します。 このサンプル アプリケーションは GitHub ( [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer)) で入手できます。

1. Git ツールがインストールされていることを確認します。 [Git のダウンロード](http://www.git-scm.com/downloads) ページからダウンロードできます。 
2. コマンド プロンプトまたはターミナル シェルを開き、任意のディレクトリで次のコマンドを実行してプロジェクトを複製します。
        
        git clone https://github.com/hdinsight/eventhubs-sample-event-producer.git eventhubs-client-sample

3. これにより、eventhubs-client-sample という名前の新しいフォルダーが作成されます。 シェルまたはコマンド プロンプト内でこのフォルダーに移動します。
4. 次のコマンドで Maven を実行してアプリケーションをビルドします。

          mvn package

5. シェルまたはコマンド プロンプト内で、``com-microsoft-azure-eventhubs-client-example-0.2.0.jar`` ファイルが格納されている作成済みのターゲット ディレクトリに移動します。
6. 次に、実際の Event Hubs に対してイベント プロデューサーを実行するためのコマンド ラインを構築する必要があります。 次のようにコマンド内の値を置き換えてください。

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "<namespace>" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "<policyKey>" --message-length 32 --thread-count 1 --message-count -1

7. たとえば、完成したコマンドは次のようになります。

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "sparkstreaming" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU=" --message-length 32 --thread-count 1 --message-count -1

8. コマンドの実行が開始され、構成が正しければ数分後には、Event Hubs に送信されているイベントに関連した出力結果が表示されます。その例を次に示します。

        Map('policyKey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, 'eventhubsName -> hub1, 'policyName -> RootManageSharedAccessKey, 'eventhubsNamespace -> sparkstreaming, 'messageCount -> -1, 'messageLength -> 32, 'threadCount -> 1)
        Events per thread: -1 (-1 for unlimited)
        10 > Sun Jun 18 11:32:58 PDT 2017 > 1024 > 1024 > Sending event: ZZ93958saG5BUKbvUI9wHVmpuA2TrebS
        10 > Sun Jun 18 11:33:46 PDT 2017 > 2048 > 2048 > Sending event: RQorGRbTPp6U2wYzRSnZUlWEltRvTZ7R
        10 > Sun Jun 18 11:34:33 PDT 2017 > 3072 > 3072 > Sending event: 36Eoy2r8ptqibdlfCYSGgXe6ct4AyOX3
        10 > Sun Jun 18 11:35:19 PDT 2017 > 4096 > 4096 > Sending event: bPZma9V0CqOn6Hj9bhrrJT0bX2rbPSn3
        10 > Sun Jun 18 11:36:06 PDT 2017 > 5120 > 5120 > Sending event: H2TVD77HNTVyGsVcj76g0daVnYxN4Sqs

9. イベント プロデューサーを実行したまま以降の手順に進んでください。

## <a name="run-spark-shell-on-your-hdinsight-cluster"></a>HDInsight クラスターでの Spark シェルの実行
このタスクでは、HDInsight クラスターのヘッド ノードに SSH で接続して Spark シェルを起動し、Event Hubs からイベントを取得して処理する Spark ストリーミング アプリケーションを実行します。 

この時点で、HDInsight クラスターを使用する準備は完了しているかと思います。 まだ準備が完了していない場合は、プロビジョニングが完了するまで待ってください。 準備が整ったら、次の手順に進みます。

1. SSH を使用して HDInsight クラスターに接続します。 手順については、「[SSH を使用して HDInsight に接続する](../hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。

5. 作成するアプリケーションには、Spark Streaming Event Hubs パッケージが必要です。 この依存関係を [Maven Central](https://search.maven.org) から自動的に取得するように Spark シェルを実行するには、packages スイッチに Maven コーディネートを指定してください。その例を次に示します。

        spark-shell --packages "com.microsoft.azure:spark-streaming-eventhubs_2.11:2.1.5"

6. Spark シェルの読み込みが完了すると、次の画面が表示されます。

        Welcome to
            ____              __
            / __/__  ___ _____/ /__
            _\ \/ _ \/ _ `/ __/  '_/
        /___/ .__/\_,_/_/ /_/\_\   version 2.1.1.2.6.2.3-1
            /_/
                
        Using Scala version 2.11.8 (OpenJDK 64-Bit Server VM, Java 1.8.0_151)
        Type in expressions to have them evaluated.
        Type :help for more information.

        scala> 

7. 次のコード スニペットをテキスト エディターにコピーし、実際の Event Hubs に合わせてポリシー キーと名前空間を変更します。

        val eventhubParameters = Map[String, String] (
            "eventhubs.policyname" -> "RootManageSharedAccessKey",
            "eventhubs.policykey" -> "<policyKey>",
            "eventhubs.namespace" -> "<namespace>",
            "eventhubs.name" -> "hub1",
            "eventhubs.partition.count" -> "2",
            "eventhubs.consumergroup" -> "$Default",
            "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            "eventhubs.sql.containsProperties" -> "true"
            )
            
8. 次の形式の EventHub 互換エンドポイントを見ると、`iothub-xxxxxxxxxx` という部分は EventHub 互換の名前空間名であり、`eventhubs.namespace` に使用できます。 フィールド `SharedAccessKeyName` は `eventhubs.policyname` に使用でき、`SharedAccessKey` は `eventhubs.policykey` に使用できます。 

        Endpoint=sb://iothub-xxxxxxxxxx.servicebus.windows.net/;SharedAccessKeyName=xxxxx;SharedAccessKey=xxxxxxxxxx 

9. 待機中の scala> プロンプトに変更後のスニペットを貼り付けて Enter キーを押します。 次のような出力が表示されます。

        scala> val eventhubParameters = Map[String, String] (
            |       "eventhubs.policyname" -> "RootManageSharedAccessKey",
            |       "eventhubs.policykey" -> "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU",
            |       "eventhubs.namespace" -> "sparkstreaming",
            |       "eventhubs.name" -> "hub1",
            |       "eventhubs.partition.count" -> "2",
            |       "eventhubs.consumergroup" -> "$Default",
            |       "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            |       "eventhubs.sql.containsProperties" -> "true"
            |     )
        eventhubParameters: scala.collection.immutable.Map[String,String] = Map(eventhubs.sql.containsProperties -> true, eventhubs.name -> hub1, eventhubs.consumergroup -> $Default, eventhubs.partition.count -> 2, eventhubs.progressTrackingDir -> /eventhubs/progress, eventhubs.policykey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, eventhubs.namespace -> hdiz-docs-eventhubs, eventhubs.policyname -> RootManageSharedAccessKey)

10. 次に、Spark 構造化ストリーミング クエリを作成し、ソースを指定します。 Spark シェルに次のコードを貼り付けて Enter キーを押します。

        val inputStream = spark.readStream.
        format("eventhubs").
        options(eventhubParameters).
        load()

11. 次のような出力が表示されます。

        inputStream: org.apache.spark.sql.DataFrame = [body: binary, offset: bigint ... 5 more fields]

12. 次に、出力先がコンソールとなるようにクエリを記述します。 Spark シェルに次のコードを貼り付けて Enter キーを押してください。

        val streamingQuery1 = inputStream.writeStream.
        outputMode("append").
        format("console").start().awaitTermination()

13. 次のような出力内容で始まるいくつかのバッチが表示されます。

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        [Stage 0:>                                                          (0 + 2) / 2]

14. その後に、イベントの小さなバッチごとの処理から得られた出力結果が表示されます。 

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        17/06/18 18:57:39 WARN TaskSetManager: Stage 1 contains a task of very large size (419 KB). The maximum recommended task size is 100 KB.
        +--------------------+------+---------+------------+---------+------------+----------+
        |                body|offset|seqNumber|enqueuedTime|publisher|partitionKey|properties|
        +--------------------+------+---------+------------+---------+------------+----------+
        |[7B 22 74 65 6D 7...|     0|        0|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   112|        1|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   224|        2|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   336|        3|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   448|        4|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   560|        5|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   672|        6|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   784|        7|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   896|        8|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1008|        9|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1120|       10|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1232|       11|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1344|       12|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1456|       13|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1568|       14|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1680|       15|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1792|       16|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1904|       17|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2016|       18|  1497734889|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2128|       19|  1497734889|     null|        null|     Map()|
        +--------------------+------+---------+------------+---------+------------+----------+
        only showing top 20 rows

15. 新しいイベントがイベント プロデューサーから届くと、それらのイベントがこの構造化ストリーミング クエリによって処理されます。
16. このサンプルの実行を終えたら必ず、HDInsight クラスターを削除してください。



## <a name="see-also"></a>関連項目

* [Spark ストリーミングの概要](apache-spark-streaming-overview.md)













