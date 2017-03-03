---
title: "HDInsight で Storm を使用して Event Hubs のイベントを処理する | Microsoft Docs"
description: "Visual Studio で HDInsight Tools for Visual Studio を使用して作成した C# Storm トポロジによって Event Hubs のデータを処理する方法について説明します。"
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 67f9d08c-eea0-401b-952b-db765655dad0
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: c9a5091973395dd888939432292fbd06dcbf0680
ms.openlocfilehash: c0349b5890a75c6ffaa6b7eca93baa3101912cf6
ms.lasthandoff: 01/18/2017


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (＃C)

Azure Event Hubs では、Web サイト、アプリ、デバイスで発生する大量のデータを処理できます。 Event Hubs スパウトでは、HDInsight で Apache Storm を使用してこのデータをリアルタイムで簡単に分析できます。 また、Event Hubs のボルトを使用して Storm から Event Hub にデータを書き込むこともできます。

このチュートリアルでは、HDInsight Tools for Visual Studio と共にインストールされる Visual Studio テンプレートを使用して、Azure Event Hubs で動作する&2; つのトポロジを作成する方法を説明します。

* **EventHubWriter**: データをランダムに生成して Event Hubs に書き込む
* **EventHubReader**: Event Hubs からデータを読み取り、Storm ログにデータを記録する

> [!NOTE] 
> このプロジェクトの Java バージョンについては、「[HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (Java)](hdinsight-storm-develop-java-event-hub-topology.md)」を参照してください。

## <a name="scpnet"></a>SCP.NET

これらのプロジェクトでは、SCP.NET を使用します。SCP.NET は、HDInsight の Storm で使用する C# トポロジとコンポーネントを作成しやすくする NuGet パッケージです。

> [!IMPORTANT]
> このドキュメントの手順は Visual Studio を使う Windows 開発環境でのものですが、コンパイル済みのプロジェクトは、Linux を使用する HDInsight クラスターの Storm に送信できます。 __SCP.NET トポロジをサポートする Linux ベースのクラスターは、2016 年 10 月 28 日より後に作成されたものだけです。__

### <a name="cluster-versioning"></a>クラスターのバージョン管理

プロジェクトによって使用される Microsoft.SCP.Net.SDK NuGet パッケージは、HDInsight にインストールされた Storm のメジャー バージョンと一致する必要があります。 HDInsight バージョン 3.3 および 3.4 上の Storm は Storm バージョン 0.10.x を使用するため、これらのクラスターでは SCP.NET バージョン 0.10.x.x を使用する必要があります。 HDInsight 3.5 は Storm 1.0.x を使用するため、このクラスターのバージョンでは SCP.NET バージョン 1.0.x.x を使用する必要があります。

> [!IMPORTANT]
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関するセクションを参照してください。

HDInsight 3.4 以降では、Mono を使用して C# トポロジを実行します。 ほとんどの機能は Mono で動作しますが、[Mono の互換性](http://www.mono-project.com/docs/about-mono/compatibility/)のドキュメントで互換性のない可能性がある機能について確認する必要があります。

C# トポロジは .NET 4.5 も対象にする必要があります。

## <a name="how-to-work-with-event-hubs"></a>イベント ハブを使用する方法

Microsoft では、Storm トポロジ からの Azure イベント ハブとの通信に使用できる Java コンポーネントのセットを提供します。 これらのコンポーネントの最新バージョンを含む jar ファイルは、[https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/) にあります。

> [!IMPORTANT]
> コンポーネントは Java で記述されていますが、C# トポロジから簡単に使用することができます。

主に扱うコンポーネントは次のとおりです。

* __EventHubSpout__: イベント ハブからデータを読み取ります。
* __EventHubBolt__: イベント ハブにデータを書き込みます。
* __EventHubSpoutConfig__: EventHubSpout の構成に使用します。
* __EventHubBoltConfig__: EventHubBolt の構成に使用します。
* __UnicodeEventDataScheme__: イベント ハブから読み取るときに、UTF-8 のエンコーディングを使用するスパウトを構成するために使用します。 これを使用しない場合、文字列のエンコーディングがスパウトの規定値になります。

### <a name="example-spout-usage"></a>スパウトの使用例

SCP.NET は、トポロジに EventHubSpout を追加するためのメソッドを提供します。 これらのメソッドにより、Java コンポーネントを追加するための一般的なメソッドを使用した場合よりも、追加が簡単になります。 次の例は、SCP.NET によって提供される __SetEventHubSpout__ メソッドと EventHubSpoutConfig メソッドを使用して新しいスパウトを作成する方法を示します。

```csharp
topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        // the shared access signature name and key used to read the data
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        // The namespace that contains the Event Hub to read from
        ConfigurationManager.AppSettings["EventHubNamespace"],
        // The Event Hub name to read from
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        // The number of partitions in the Event Hub
        eventHubPartitions),
    // Parallelism hint for this component. Should be set to the partition count.
    eventHubPartitions);
```

前の例では __EventHubSpout__ という名前の新しいスパウト コンポーネントを作成し、これがイベント ハブと通信するように構成しています。 このコンポーネントの並列処理のヒントはイベント ハブのパーティション数に設定されていることに注意してください。 これによって Storm は、パーティションごとに、コンポーネントのインスタンスを作成することができます。

> [!WARNING]
> 2017 年 1 月 1 日時点では、SetEventHubSpout メソッドと EventHubSpoutConfig メソッドを使用すると、イベント ハブからデータを読み取るときに文字列のエンコーディングを使用するスパウトが作成されます。 UTF-8 エンコーディングを使用する必要がある場合は、次の例を参照してください。

スパウトを作成するときに、一般的な JavaCompoentConstructor メソッドを使用することもできます。 次の例では、JavaComponentConstructor メソッドを使用して新しいスパウトを作成する方法を示します。 また、文字列ではなく UTF-8 エンコーディングを使用してデータを読み取るスパウトを構成する方法も示します。

```csharp
// Create an instance of UnicodeEventDataScheme
var schemeConstructor = new JavaComponentConstructor("com.microsoft.eventhubs.spout.UnicodeEventDataScheme");
// Create an instance of EventHubSpoutConfig
var eventHubSpoutConfig = new JavaComponentConstructor(
    "com.microsoft.eventhubs.spout.EventHubSpoutConfig",
    new List<Tuple<string, object>>()
    {
        // the shared access signature name and key used to read the data
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"]),
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubSharedAccessKey"]),
        // The namespace that contains the Event Hub to read from
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubNamespace"]),
        // The Event Hub name to read from
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubEntityPath"]),
        // The number of partitions in the Event Hub
        Tuple.Create<string, object>("int", eventHubPartitions),
        // The encoding scheme to use when reading
        Tuple.Create<string, object>("com.microsoft.eventhubs.spout.IEventDataScheme", schemeConstructor)
    }
    );
// Create an instance of the spout
var eventHubSpout = new JavaComponentConstructor(
    "com.microsoft.eventhubs.spout.EventHubSpout",
    new List<Tuple<string, object>>()
    {
        Tuple.Create<string, object>("com.microsoft.eventhubs.spout.EventHubSpoutConfig", eventHubSpoutConfig)
    }
    );
// Set the spout in the topology
topologyBuilder.SetJavaSpout("EventHubSpout", eventHubSpout, eventHubPartitions);
```

> [!IMPORTANT]
> UnicodeEventDataScheme は、[https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/) で提供されている、イベント ハブ コンポーネントの 9.5 バージョンでのみ利用可能です。

### <a name="example-bolt-usage"></a>ボルトの使用例

ボルトのインスタンスを作成するには、JavaComponmentConstructor メソッドを使用する必要があります。 次の例では、EventHubBolt の新しいインスタンスを作成および構成する方法を示します。

```csharp
//Create constructor for the Java bolt
JavaComponentConstructor constructor =
    // Use a Clojure expression to create the EventHubBoltCOnfig
    JavaComponentConstructor.CreateFromClojureExpr(
        String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
    // The policy name and key used to read from Event Hubs
    ConfigurationManager.AppSettings["EventHubPolicyName"],
    ConfigurationManager.AppSettings["EventHubPolicyKey"],
    // The namespace that contains the Event Hub
    ConfigurationManager.AppSettings["EventHubNamespace"],
    "servicebus.windows.net", //suffix for the namespace fqdn
    // The Evetn Hub Name)
    ConfigurationManager.AppSettings["EventHubName"],
    "true"));

//Set the bolt
topologyBuilder.SetJavaBolt(
        "EventHubBolt",
        constructor,
        partitionCount). //Parallelism hint uses partition count
    shuffleGrouping("Spout"); //Consume data from spout
```

> [!NOTE]
> この例では、スパウトの例で行ったように、独立した EventHubBoltConfig を作成する JavaComponentConstructor を使用する代わりに、文字列として渡される Clojure 式を使用します。 どちらでも、最適な方法をお使いください。

## <a name="download-the-completed-project"></a>完成したプロジェクトをダウンロードする

このチュートリアルで作成したプロジェクトの完全なバージョンを GitHub からダウンロードできます ( [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub))。 ただし、このチュートリアルの手順に従って構成設定を指定する必要があります。

### <a name="prerequisites"></a>前提条件

* [HDInsight クラスター バージョン 3.5 での Apache Storm](hdinsight-apache-storm-tutorial-get-started.md)

    > [!WARNING]
    > このドキュメントで使用している例では、HDInsight バージョン 3.5 での Storm が必要です。 以前のクラスターでのバージョンと HDInsight 3.5 に付属の Storm のバージョン間で、コア Storm コンポーネントに使用されるクラス名に変更がありました。 以前のクラスターで動作するこの例のバージョンについては、[https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases) を参照してください。

* [Azure Event Hub](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

* [Azure .NET SDK](http://azure.microsoft.com/downloads/)

* [HDInsight Tools for Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

* Java JDK 1.7 以降を持つ開発環境。 JDK のダウンロードは [http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html) から行うことができます。

  * **JAVA_HOME** 環境変数は、Java があるディレクトリを指している必要があります。
  * **%JAVA_HOME%/bin** ディレクトリはパス内にある必要があります。

## <a name="download-the-event-hub-components"></a>イベント ハブのコンポーネントをダウンロードする

スパウトとボルトは、**eventhubs-storm-spout-#.#-jar-with-dependencies.jar**という名前の&1; つの Java アーカイブ (.jar) ファイルとして配布されます。

HDInsight バージョン 3.5 の Storm で動作する jar ファイルのバージョンは、[https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/) からダウンロードできます。

`eventhubspout` という名前の新しいディレクトリを作成し、ファイルをディレクトリに保存します。

## <a name="configure-event-hubs"></a>Event Hubs を構成する

Event Hubs は、この例のデータ ソースです。 「[Event Hubs の使用](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)」ページの「**Event Hub を作成する**」セクションの情報を使用します。

1. Event Hub が作成されたら、Azure Portal の EventHub ブレードを表示し、**[共有アクセス ポリシー]** を選択します。 **[+ 追加]** エントリを使用して、次のポリシーを追加します。
   
   | 名前 | アクセス許可 |
   | --- | --- |
   | ライター |送信 |
   | リーダー |リッスン |
   
    ![ポリシー](./media/hdinsight-storm-develop-csharp-event-hub-topology/sas.png)

2. **reader** ポリシーと **writer** ポリシーを選択します。 両方のポリシーの **PRIMARY KEY** 値をコピーして保存します (これらは後で使用します)。

## <a name="configure-the-eventhubwriter"></a>EventHubWriter の構成

1. HDInsight Tools for Visual Studio の最新バージョンをまだインストールしていない場合は、「[HDInsight Tools for Visual Studio の使用開始](hdinsight-hadoop-visual-studio-tools-get-started.md)」をご覧ください。

2. [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub) からソリューションをダウンロードします。 ソリューションを開き、コード全体をよく見て **EventHubWriter** プロジェクトを確認します。

3. **EventHubWriter** プロジェクトで、**App.config** ファイルを開きます。 以前に構成した Event Hub の情報を使用して、次のキーの値を入力します。
   
   | キー | 値 |
   | --- | --- |
   | EventHubPolicyName |writer (*Send* 権限を持つポリシーに別の名前を使用した場合は、その名前を使用) |
   | EventHubPolicyKey |writer ポリシーのキー |
   | EventHubNamespace |Event Hub が含まれている名前空間 |
   | EventHubName |Event Hub 名 |
   | EventHubPartitionCount |Event Hub のパーティションの数 |

4. **App.config** ファイルを保存して閉じます。

## <a name="configure-the-eventhubreader"></a>EventHubReader の構成

1. **EventHubReader** プロジェクトを開き、コード全体をよく見ます。

2. **EventHubWriter**の **App.config** を開きます。 以前に構成した Event Hub の情報を使用して、次のキーの値を入力します。
   
   | キー | 値 |
   | --- | --- |
   | EventHubPolicyName |reader (*listen* 権限を持つポリシーに別の名前を使用した場合は、その名前を使用) |
   | EventHubPolicyKey |reader ポリシーのキー |
   | EventHubNamespace |Event Hub が含まれている名前空間 |
   | EventHubName |Event Hub 名 |
   | EventHubPartitionCount |Event Hub のパーティションの数 |

3. **App.config** ファイルを保存して閉じます。

## <a name="deploy-the-topologies"></a>トポロジのデプロイ

1. **ソリューション エクスプローラー**で **EventHubReader** プロジェクトを右クリックし、**[HDInsight での Storm に送信]** を選択します。
   
    ![Storm に送信](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. **[トポロジの送信]** 画面で該当する **[Storm クラスター]** を選択します。 **[追加の構成]** を展開し、**[Java ファイル パス]**、**[...]** の順に選択し、前の手順でダウンロードした jar ファイルがあるディレクトリを選択します。 最後に、 **[送信]**をクリックします。
   
    ![送信ダイアログの画像](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. トポロジが送信されると、 **[Storm トポロジ ビューアー]** が表示されます。 ダイアログの左側にある **EventHubReader** トポロジを選択し、トポロジの統計情報を表示します。 Event Hubs に書き込まれたイベントはまだないため、現時点では、何も発生していません。
   
    ![ストレージ ビューの例](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. **ソリューション エクスプローラー**で **EventHubWriter** プロジェクトを右クリックし、**[HDInsight での Storm に送信]** を選択します。

5. **[トポロジの送信]** 画面で該当する **[Storm クラスター]** を選択します。 **[追加の構成]** を展開し、**[Java ファイル パス]**、**[...]** の順に選択し、前の手順でダウンロードした jar ファイルがあるディレクトリを選択します。 最後に、 **[送信]**をクリックします。

6. トポロジが送信されたら、 **[Storm トポロジ ビューアー]** でトポロジ一覧を最新情報に更新し、両方のトポロジがクラスターで実行中であることを確認します。

7. **[Storm トポロジ ビューアー]** で **EventHubReader** トポロジを選択します。

8. グラフ ビューで、**LogBolt** コンポーネントをダブルクリックします。 これによって、ボルトの **[コンポーネントの概要]** ページが開きます。

9. **[Executors]** セクションで、**[ポート]** 列のリンクを&1; つ選択します。 これによって、コンポーネントで記録された情報が表示されます。 次のような情報が記録されています。
   
        2016-10-20 13:26:44.186 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {5769732396213255808=520853934697489134}, [{"deviceId":3,"deviceValue":1379915540}]
        2016-10-20 13:26:44.234 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {7154038361491319965=4543766486572976404}, [{"deviceId":3,"deviceValue":459399321}]
        2016-10-20 13:26:44.335 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:6, stream: default, id: {513308780877039680=-7571211415704099042}, [{"deviceId":5,"deviceValue":845561159}]
        2016-10-20 13:26:44.445 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {-2409895457033895206=5479027861202203517}, [{"deviceId":8,"deviceValue":2105860655}]

## <a name="stop-the-topologies"></a>トポロジの停止

トポロジを停止するには、**[Storm トポロジ ビューアー]** で各トポロジを選択し、**[強制終了]** をクリックします。

![トポロジの強制終了の画像](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>次のステップ

このドキュメントでは、C# トポロジから Java Event Hubs スパウトおよびボルトを使用して、Azure Event Hub のデータを操作する方法について説明しました。 C# トポロジの作成の詳細については、次の記事を参照してください。

* [Visual Studio を使用して HDInsight で Apache Storm の C# トポロジを開発する](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [SCP プログラミング ガイド](hdinsight-storm-scp-programming-guide.md)
* [HDInsight 上の Storm に関するトポロジ例](hdinsight-storm-example-topology.md)


