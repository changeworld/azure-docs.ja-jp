---
title: "Azure HDInsight での Apache Spark を使用した Event Hubs からのデータのストリーミング | Microsoft Docs"
description: "Azure Event Hub にデータ ストリームを送信し、Scala アプリケーションを使用して Spark でイベントを受信する方法の詳細な手順を説明します"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 68894e75-3ffa-47bd-8982-96cdad38b7d0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: ef0757914828128ed4edf569aeb3716300b17dee
ms.lasthandoff: 01/24/2017


---
# <a name="spark-streaming-process-events-from-azure-event-hubs-with-apache-spark-cluster-on-hdinsight"></a>Spark ストリーミング: HDInsight で Apache Spark クラスターを使用して Azure Event Hubs からのイベントを処理する
Spark ストリーミングは、コア Spark API を拡張して、スケーラビリティ、高スループット、フォールト トレランスを備えたストリーム処理アプリケーションを構築します。 多くのソースからデータを取り込むことができます。 この記事では、Azure Event Hubs を使用してデータを取り込みます。 Event Hubs は、スケーラブルなインジェスト システムであり、1 秒間に数百万件のイベントを取り込むことができます。 

このチュートリアルでは、Azure Event Hubs を作成する方法、Java のコンソール アプリケーションを使用して Event Hubs にメッセージを取り込む方法、および Scala で記述された Spark アプリケーションを使用してメッセージを並列に取得する方法について説明します。 このアプリケーションでは、Event Hubs を通してストリーミングされたデータを処理し、さまざまな出力 (Azure Storage BLOB、Hive テーブル、および SQL テーブル) にルーティングします。

> [!NOTE]
> この記事の手順に従うには、両方のバージョンの Azure ポータルを使用する必要があります。 イベント ハブを作成するには、 [Azure クラシック ポータル](https://manage.windowsazure.com)を使用します。 HDInsight Spark クラスターを操作するには、 [Azure ポータル](https://portal.azure.com/)を使用します。  
> 
> 

**前提条件:**

次のものが必要です。

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* HDInsight での Apache Spark クラスター。 手順については、 [Azure HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)に関するページを参照してください。
* Oracle Java Development kit。 [ここ](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)からインストールできます。
* Java IDE。 この記事では、IntelliJ IDEA 15.0.1 を使用します。 [ここ](https://www.jetbrains.com/idea/download/)からインストールできます。
* Microsoft JDBC Driver for SQL Server (v4.1 以降)。 SQL Server データベースにイベント データを書き込むために必要です。 [ここ](https://msdn.microsoft.com/sqlserver/aa937724.aspx)からインストールできます。
* Azure SQL Database。 手順については、[数分での SQL データベースの作成](../sql-database/sql-database-get-started.md)に関する記事を参照してください。

## <a name="what-does-this-solution-do"></a>このソリューションの内容
ストリーミング ソリューションのフローを次に示します。

1. イベントのストリームを受信する Azure Event Hub を作成します。
2. イベントを生成し、それを Azure Event Hub にプッシュするローカルのスタンドアロン アプリケーションを実行します。 サンプル アプリケーションは [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples)に公開されています。
3. Azure Event Hub からストリーミング イベントを読み取り、さまざまな場所 (Azure BLOB、Hive テーブル、および SQL データベース テーブル) にプッシュするリモート ストリーミング アプリケーションを Spark クラスター上で実行します。 

## <a name="create-azure-event-hub"></a>Azure Event Hub の作成
1. [Azure Portal](https://manage.windowsazure.com) で、**[新規作成]** > **[Service Bus]** > **[イベント ハブ]** > **[カスタム作成]** の順に選択します。
2. **[新しい Event Hub の追加]** 画面で **Event Hub 名**を入力し、ハブを作成する **[リージョン]** を選択して、新しい名前空間を作成するか、既存の名前空間を選択します。 **矢印** をクリックして続行します。
   
    ![ウィザード ページ 1](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.create.event.hub.png "Azure Event Hub を作成します")
   
   > [!NOTE]
   > 待機時間とコストを削減するために、HDInsight の Apache Spark クラスターと同じ **[場所]** を選択する必要があります。
   > 
   > 
3. **[イベント ハブの構成]** 画面で、**[パーティション数]** と **[メッセージのリテンション期間]** の値を入力して、チェック マークをクリックします。 この例では、パーティション カウントに 10 を、メッセージ保持に 1 を使用します。 パーティション数を書き留めておきます。この値は後で必要になります。
   
    ![ウィザード ページ 2](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.create.event.hub2.png "Event Hub のパーティション サイズおよびリテンション期間を指定します")
4. 作成した Event Hub をクリックし、 **[構成]**をクリックして、Event Hub 用に&2; つのアクセス ポリシーを作成します。
   
    <table>
    <tr><th>名前</th><th>アクセス許可</th></tr>
    <tr><td>mysendpolicy</td><td>送信</td></tr>
    <tr><td>myreceivepolicy</td><td>リッスン</td></tr>
    </table>
   
    アクセス許可の作成後、ページの下部にある **[保存]** アイコンをクリックします。 これにより、このイベント ハブに対する送信 (**mysendpolicy**) とリッスン (**myreceivepolicy**) に使用する共有アクセス ポリシーが作成されます。
   
    ![ポリシー](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.event.hub.policies.png "Event Hub のポリシーを作成します")
5. 同じページで、2 つのポリシーに対して生成されるポリシー キーを記録しておきます。 後で使用するので、これらのキーを保存します。
   
    ![ポリシー キー](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.event.hub.policy.keys.png "ポリシー キーを保存します")
6. **[ダッシュボード]** ページで、下部にある **[接続情報]** をクリックして、2 つのポリシーを使用するイベント ハブに接続文字列を取得し、保存します。
   
    ![ポリシー キー](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.event.hub.policy.connection.strings.png "ポリシーの接続文字列を保存します")

## <a name="use-a-scala-application-to-send-messages-to-event-hub"></a>Scala アプリケーションを使用して、Event Hub にメッセージを送信する
このセクションでは、ローカルのスタンドアロン Scala アプリケーションを使用して、イベントのストリームを、前の手順で作成した Azure Event Hub に送信します。 このアプリケーションは GitHub ( [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer)) で入手できます。 ここに示す手順は、この GitHub リポジトリが既にフォークされていることを前提とします。

1. IntelliJ IDEA で、アプリケーション **EventhubsSampleEventProducer**を開きます。
2. プロジェクトをビルドします。 **[Build] \(ビルド)** メニューの **[Make Project] \(プロジェクトの作成)** をクリックします。 出力 jar が **\out\artifacts** の下に作成されます。

> [!TIP]
> IntelliJ IDEA で提供されているオプションを使用して、GitHub リポジトリからプロジェクトを直接作成することもできます。 その方法については、ガイダンスの次のセクションに記載された手順を参照してください。 そのセクションで記載されている手順の多くは、この手順で作成する Scala アプリケーションには適用されないので注意してください。 For example:
> 
> * Spark バージョンを取り込むときに POM を更新する必要はありません。 それは、このアプリケーションを作成するための依存関係が Spark に存在しないからです。
> * プロジェクト ライブラリにいくつかの依存関係 jar を追加する必要はありません。 このプロジェクトで、それらの jar は必要ないからです。
> 
> 

## <a name="update-the-scala-streaming-application-for-receiving-the-events"></a>イベントを受信するための Scala ストリーミング アプリケーションを更新する
イベントを受信し、それをさまざまな宛先にルーティングするサンプル Scala アプリケーションは、 [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples)で入手できます。 以下の手順に従ってアプリケーションを更新し、出力 jar を作成してください。

1. IntelliJ IDEA を起動し、起動画面から **[Check out from Version Control]\(バージョン管理からチェックアウト)** を選択し、**[Git]** をクリックします。
   
    ![Git からのソースの取得](./media/hdinsight-apache-spark-eventhub-streaming/get-source-from-git.png)
2. **[Clone Repository] \(リポジトリの複製)** ダイアログ ボックスで、複製元の Git リポジトリの URL および複製先のディレクトリを指定し、**[Clone] \(複製)** をクリックします。
   
    ![Git からの複製](./media/hdinsight-apache-spark-eventhub-streaming/clone-from-git.png)
3. プロジェクトが完全に複製されるまで、画面の指示に従います。 **Alt + 1** キーを押して、**[Project View] \(プロジェクト ビュー)** を開きます。 次のようになります。
   
    ![[Project View (プロジェクト ビュー)]](./media/hdinsight-apache-spark-eventhub-streaming/project-view.png)
4. アプリケーション コードが Java8 でコンパイルされるように設定します。 このためには、**[File] \(ファイル)**、**[Project Structure] \(プロジェクトの構造)** の順にクリックし、**[Project] \(プロジェクト)** タブで [Project language level] \(プロジェクトの言語レベル) を **[8 - Lambdas, type annotations, etc.] \(8 - ラムダ、型注釈など)** に設定します。
   
    ![プロジェクト構造](./media/hdinsight-apache-spark-eventhub-streaming/java-8-compiler.png)
5. **pom.xml** を開き、Spark のバージョンが正しいことを確認します。 DNS サーバー <properties> ノードで、次のスニペットを検索し、Spark のバージョンを確認します。
   
        <scala.version>2.10.4</scala.version>
        <scala.compat.version>2.10.4</scala.compat.version>
        <scala.binary.version>2.10</scala.binary.version>
        <spark.version>1.6.2</spark.version>
6. アプリケーションでは、次の&2; つの依存関係 jar が必要です。
   
   * **EventHub receiver jar**。 Spark で Event Hub からメッセージを受信するために必要です。 この jar を使用するために、**pom.xml** で `<dependencies>` の下に次のコードを追加します。
     
           <dependency>
             <groupId>com.microsoft.azure</groupId>
             <artifactId>spark-streaming-eventhubs_2.10</artifactId>
             <version>1.6.0</version>
           </dependency> 
   * **JDBC driver jar**。 Event Hub から受信したメッセージを Azure SQL データベースに書き込むために必要です。 この jar ファイルの v4.1 以降を、 [ここ](https://msdn.microsoft.com/sqlserver/aa937724.aspx)からダウンロードできます。 プロジェクト ライブラリでこの jar への参照を追加します。 次の手順に従います。
     
     1. アプリケーションが開かれている IntelliJ IDEA ウィンドウで、**[File] \(ファイル)**、**[Project Structure] \(プロジェクトの構造)**、**[Libraries] \(ライブラリ)** の順にクリックします。 
     2. 追加アイコン (![追加アイコン](./media/hdinsight-apache-spark-eventhub-streaming/add-icon.png)) をクリックし、 **[Java]**をクリックして、JDBC driver jar をダウンロードした場所に移動します。 画面の指示に従って、Jar ファイルをプロジェクト ライブラリに追加します。
        
         ![不足している依存関係の追加](./media/hdinsight-apache-spark-eventhub-streaming/add-missing-dependency-jars.png "不足している依存関係 jar の追加")
     3. **[Apply]**をクリックします。
7. 出力 jar ファイルを作成します。 次の手順に従います。
   
   1. **[Project Structure] \(プロジェクトの構造)** ダイアログ ボックスで、**[Artifacts] \(アーティファクト)** をクリックし、プラス記号をクリックします。 ポップアップ ダイアログ ボックスで、**[JAR]** をクリックし、**[From modules with dependencie] \(依存関係を持つモジュールから)** をクリックします。
      
       ![JAR の作成](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-1.png)
   2. **[Create JAR from Modules] \(モジュールから JAR を作成)** ダイアログ ボックスで、**[Main Class] \(メイン クラス)** の省略記号 (![省略記号](./media/hdinsight-apache-spark-eventhub-streaming/ellipsis.png)) をクリックします。
   3. **[Select Main Class] \(メイン クラスの選択)** ダイアログ ボックスで、使用可能なクラスのいずれかを選択し、**[OK]** をクリックします。
      
       ![JAR の作成](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-2.png)
   4. **[Create JAR from Modules] \(モジュールから JAR を作成)** ダイアログ ボックスで、**[extract to the target JAR] \(ターゲット JAR に抽出する)** オプションが選択されていることを確認し、**[OK]** をクリックします。 これにより、すべての依存関係を持つ&1; つの JAR が作成されます。
      
       ![JAR の作成](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-3.png)
   5. **[Output Layout (出力レイアウト)]** タブに、Maven プロジェクトの一部として取り込まれたすべての jar が一覧表示されます。 Scala アプリケーションと直接的な依存関係がないものについては、選択し削除できます。 ここで作成するアプリケーションの場合は、最後の&1; つ (**microsoft-spark-streaming-examples compile output**) を除き、あとはすべて削除することができます。 削除する jar を選択し、**[Delete] \(削除)** アイコン (![削除アイコン](./media/hdinsight-apache-spark-eventhub-streaming/delete-icon.png)) をクリックします。
      
       ![JAR の作成](./media/hdinsight-apache-spark-eventhub-streaming/delete-output-jars.png)
      
       **[Build on make] \(作成時にビルド)** ボックスが選択されていることを確認します。それにより、プロジェクトがビルドまたは更新されるたびに jar が確実に作成されます。 **[Apply] \(適用)**、**[OK]** の順にクリックします。
   6. **[Output Layout] \(出力レイアウト)** タブで、右側にある **[Available Elements] \(利用可能な要素)** ボックスの下部に、先ほどプロジェクト ライブラリに追加した SQL JDBC jar があります。 これは **[Output Layout (出力レイアウト)]** タブに追加する必要があります。 jar ファイルを右クリックし、 **[Extract Into Output Root (出力ルートに抽出)]**をクリックします。
      
       ![依存関係 jar の抽出](./media/hdinsight-apache-spark-eventhub-streaming/extract-dependency-jar.png)  
      
       これで、 **[Output Layout (出力レイアウト)]** タブは次のようになります。
      
       ![[最終出力] タブ](./media/hdinsight-apache-spark-eventhub-streaming/final-output-tab.png)        
      
       **[Project Structure] \(プロジェクトの構造)** ダイアログ ボックスで、**[Apply] \(適用)** をクリックし、**[OK]** をクリックします。    
   7. メニュー バーの **[Build] \(ビルド)** をクリックし、**[Make Project] \(プロジェクトの作成)** をクリックします。 **[Build Artifact (アーティファクトのビルド)]** をクリックして、jar を作成することもできます。 出力 jar が **\out\artifacts** の下に作成されます。
      
       ![JAR の作成](./media/hdinsight-apache-spark-eventhub-streaming/output.png)

## <a name="run-the-applications-remotely-on-a-spark-cluster-using-livy"></a>Livy を使用して Spark クラスターでアプリケーションをリモートで実行する
Livy を使用して Spark クラスターでストリーミング アプリケーションをリモートで実行します。 HDInsight Spark クラスターで Livy を使用する方法の詳細については、「 [Azure HDInsight の Apache Spark クラスターへのジョブのリモート送信](hdinsight-apache-spark-livy-rest-interface.md)」を参照してください。 Spark を使用してイベントをストリーミングするリモート ジョブを実行するには、事前にいくつかの操作を実行する必要があります。

1. ローカルのスタンドアロン アプリケーションを起動して、イベントを生成し、Event Hub に送信します。 それを行うには、次のコマンドを使用します。
   
        java -cp EventhubsSampleEventProducer.jar com.microsoft.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "mysbnamespace" --eventhubs-name "myeventhub" --policy-name "mysendpolicy" --policy-key "<policy key>" --message-length 32 --thread-count 32 --message-count -1
2. ストリーミング jar (**microsoft-spark-streaming-examples.jar**) を、クラスターに関連付けられた Azure Blob ストレージにコピーします。 これにより、jar で Livy にアクセスできるようになります。 コピーには、[**AzCopy**](../storage/storage-use-azcopy.md) コマンド ライン ユーティリティを使用できます。 データのアップロードに使用できるクライアントは、他にも多数あります。 詳細については、「 [HDInsight での Hadoop ジョブ用データのアップロード](hdinsight-upload-data.md)」を参照してください。
3. これらのアプリケーションを実行するコンピューターに CURL をインストールします。 CURL を使用して、ジョブをリモートで実行する Livy エンドポイントを呼び出します。

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-text"></a>Azure Storage BLOB へのイベントをテキストとして受信するアプリケーションを実行する
コマンド プロンプトを開き、CURL をインストールしたディレクトリに移動し、次のコマンドを実行します (ユーザー名/パスワードおよびクラスター名を置き換える)。

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputBlob.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

**inputBlob.txt** ファイル内のパラメーターは次のように定義されています。

    { "file":"wasbs:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsEventCount", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

入力ファイル内のパラメーターについて説明します。

* **file** は、クラスターに関連付けられた Azure ストレージ アカウントにあるアプリケーション jar ファイルへのパスです。
* **className** は、jar 内のクラスの名前です。
* **args** は、クラスに必要な引数の一覧です。
* **numExecutors** は、ストリーミング アプリケーションを実行するときに Spark が使用するコアの数です。 これは常に Event Hub パーティションの数の少なくとも&2; 倍になる必要があります。
* **executorMemory**、**executorCores**、**driverMemory** は、必要なリソースをストリーミング アプリケーションに割り当てるために使用するパラメーターです。

> [!NOTE]
> パラメーターとして使用される出力フォルダー (EventCheckpoint、EventCount/EventCount10) を作成する必要はありません。 ストリーミング アプリケーションによって自動的に作成されます。
> 
> 

コマンドを実行したとき、出力が次のようになることを確認します。

    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /18
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Tue, 01 Dec 2015 05:39:10 GMT
    < Content-Length: 37
    <
    {"id":1,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

出力の最後行にあるバッチ ID (この例では '1') をメモします。 アプリケーションが正常に実行していることを確認するには、クラスターに関連付けられた Azure ストレージ アカウントを調べて、**/EventCount/EventCount10** フォルダーが作成されていることを確かめます。 このフォルダーには、パラメーター **batch-interval-in-seconds** で指定された期間内に処理されたイベント数をキャプチャする BLOB が含まれている必要があります。

アプリケーションは、強制終了するまで、実行を継続します。 強制終了するには、次のコマンドを使用します。

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/1"

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-json"></a>Azure Storage BLOB へのイベントを JSON として受信するアプリケーションを実行する
コマンド プロンプトを開き、CURL をインストールしたディレクトリに移動し、次のコマンドを実行します (ユーザー名/パスワードおよびクラスター名を置き換える)。

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputJSON.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

**inputJSON.txt** ファイル内のパラメーターは次のように定義されています。

    { "file":"wasbs:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureBlobAsJSON", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-store-folder", "/EventStore10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

パラメーターは、前の手順でテキスト出力用に指定したものと似ています。 繰り返しになりますが、パラメーターとして使用される出力フォルダー (EventCheckpoint、EventCount/EventCount10) を作成する必要はありません。 ストリーミング アプリケーションによって自動的に作成されます。

 コマンドを実行したら、クラスターに関連付けられた Azure ストレージ アカウントを調べて、**/EventCount/EventCount10** フォルダーが作成されていることを確かめます。 **part-** というプレフィックスの付いたファイルを開き、JSON 形式で処理されるイベントがあることを確認します。

### <a name="run-the-applications-to-receive-the-events-into-a-hive-table"></a>Hive テーブルへのイベントを受信するアプリケーションを実行する
Hive テーブルにイベントをストリーミングするアプリケーションを実行するには、追加のコンポーネントがいくつか必要です。 次のとおりです。

* datanucleus-api-jdo-3.2.6.jar
* datanucleus-rdbms-3.2.9.jar
* datanucleus-core-3.2.10.jar
* hive-site.xml

**.jar** ファイルは、`/usr/hdp/current/spark-client/lib` にある HDInsight Spark クラスターで入手できます。 **Hive-site.xml** は、`/usr/hdp/current/spark-client/conf` で入手できます。

これらのファイルは、 [WinScp](http://winscp.net/eng/download.php) を使用して、クラスターからローカル コンピューターにコピーすることができます。 次に、これらのファイルはツールを使用して、クラスターに関連付けられているストレージ アカウントにコピーすることができます。 ストレージ アカウントにファイルをアップロードする方法の詳細については、「 [HDInsight での Hadoop ジョブ用データのアップロード](hdinsight-upload-data.md)」を参照してください。

ファイルが Azure ストレージ アカウントにコピーされたら、コマンド プロンプトを開き、CURL をインストールしたディレクトリに移動し、次のコマンドを実行します (ユーザー名/パスワードおよびクラスター名を置き換える)。

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputHive.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

**inputHive.txt** ファイル内のパラメーターは次のように定義されています。

    { "file":"wasbs:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToHiveTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-hive-table", "EventHiveTable10" ], "jars":["wasbs:///example/jars/datanucleus-api-jdo-3.2.6.jar", "wasbs:///example/jars/datanucleus-rdbms-3.2.9.jar", "wasbs:///example/jars/datanucleus-core-3.2.10.jar"], "files":["wasbs:///example/jars/hive-site.xml"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

パラメーターは、前の手順でテキスト出力用に指定したものと似ています。 繰り返しになりますが、パラメーターとして使用される出力フォルダー (EventCheckpoint、EventCount/EventCount10) または出力 Hive テーブル (EventHiveTable10) を作成する必要はありません。 ストリーミング アプリケーションによって自動的に作成されます。 **jars** および **files** オプションには、ストレージ アカウントにコピーした .jar ファイルおよび hive-site.xml へのパスが含まれています。

Hive テーブルが正常に作成されたことを確認するために、クラスターに SSH 接続して、Hive クエリを実行することができます。 手順については、「 [SSH による HDInsight での Hive と Hadoop の使用](hdinsight-hadoop-use-hive-ssh.md)」を参照してください。 SSH を使用して接続したら、次のコマンドを実行して、 **EventHiveTable10**という Hive テーブルが作成されていることを確認できます。

    show tables;

次のような出力が表示されます。

    OK
    eventhivetable10
    hivesampletable

SELECT クエリを実行して、テーブルの内容を表示することもできます。

    SELECT * FROM eventhivetable10 LIMIT 10;

出力は次のように表示されます。

    ZN90apUSQODDTx7n6Toh6jDbuPngqT4c
    sor2M7xsFwmaRW8W8NDwMneFNMrOVkW1
    o2HcsU735ejSi2bGEcbUSB4btCFmI1lW
    TLuibq4rbj0T9st9eEzIWJwNGtMWYoYS
    HKCpPlWFWAJILwR69MAq863nCWYzDEw6
    Mvx0GQOPYvPR7ezBEpIHYKTKiEhYammQ
    85dRppSBSbZgThLr1s0GMgKqynDUqudr
    5LAWkNqorLj3ZN9a2mfWr9rZqeXKN4pF
    ulf9wSFNjD7BZXCyunozecov9QpEIYmJ
    vWzM3nvOja8DhYcwn0n5eTfOItZ966pa
    Time taken: 4.434 seconds, Fetched: 10 row(s)


### <a name="run-the-applications-to-receive-the-events-into-an-azure-sql-database-table"></a>Azure SQL データベース テーブルへのイベントを受信するアプリケーションを実行する
この手順を実行する前に、Azure SQL データベースが作成されていることを確認してください。 データベース名、データベース サーバー名、およびデータベース管理者の資格情報がパラメーターとして必要です。 ただし、データベース テーブルを作成する必要はありません。 ストリーミング アプリケーションによって自動的に作成されます。

コマンド プロンプトを開き、CURL をインストールしたディレクトリに移動し、次のコマンドを実行します。

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputSQL.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

**inputSQL.txt** ファイル内のパラメーターは次のように定義されています。

    { "file":"wasbs:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureSQLTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--sql-server-fqdn", "<database-server-name>.database.windows.net", "--sql-database-name", "mysparkdatabase", "--database-username", "sparkdbadmin", "--database-password", "<put-password-here>", "--event-sql-table", "EventContent" ], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

アプリケーションが正常に実行していることを確認するには、SQL Server Management Studio を使用して Azure SQL データベースに接続します。 手順については、「 [Connect to SQL Database with SQL Server Management Studio (SQL Server Management Studio を使用して SQL Database に接続する)](../sql-database/sql-database-connect-query-ssms.md)」を参照してください。 データベースに接続したら、ストリーミング アプリケーションによって作成された **EventContent** テーブルに移動します。 簡単なクエリを実行してテーブルからデータを取得します。 次のクエリを実行します。

    SELECT * FROM EventCount

次のような出力が表示されます。

    00046b0f-2552-4980-9c3f-8bba5647c8ee
    000b7530-12f9-4081-8e19-90acd26f9c0c
    000bc521-9c1b-4a42-ab08-dc1893b83f3b
    00123a2a-e00d-496a-9104-108920955718
    0017c68f-7a4e-452d-97ad-5cb1fe5ba81b
    001KsmqL2gfu5ZcuQuTqTxQvVyGCqPp9
    001vIZgOStka4DXtud0e3tX7XbfMnZrN
    00220586-3e1a-4d2d-a89b-05c5892e541a
    0029e309-9e54-4e1b-84be-cd04e6fce5ec
    003333cf-874f-4045-9da3-9f98c2b4ea49
    0043c07e-8d73-420a-9af7-1fcb94575356
    004a11a9-0c2c-4bc0-a7d5-2e0ebd947ab9


## <a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark with Machine Learning: Use Spark in HDInsight to predict food inspection results (Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する)](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (Linux)](hdinsight-apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 

