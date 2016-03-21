<properties
   pageTitle="HDInsight で Storm を使用して Event Hubs のイベントを処理する | Microsoft Azure"
   description="Visual Studio で HDInsight Tools for Visual Studio を使用して作成した C# Storm トポロジによって Event Hubs のデータを処理する方法について説明します。"
   services="hdinsight,notification hubs"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/28/2016"
   ms.author="larryfr"/>

# HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (＃C)

Azure Event Hubs では、Web サイト、アプリ、デバイスで発生する大量のデータを処理できます。Event Hubs スパウトでは、HDInsight で Apache Storm を使用してこのデータをリアルタイムで簡単に分析できます。また、Event Hubs のボルトを使用して Storm から Event Hub にデータを書き込むこともできます。

このチュートリアルでは、HDInsight Tools for Visual Studio と Event Hubs スパウトおよびボルトを使用して、次の 2 つのハイブリッド C#/Java トポロジを作成する方法について説明します。

* **EventHubWriter**: データをランダムに生成して Event Hubs に書き込む

* **EventHubReader**: Event Hubs からデータを読み取って Azure テーブル ストレージに格納する

[AZURE.NOTE] このドキュメントの手順は、Windows ベースの HDInsight クラスターに対してのみ有効です。このプロジェクトの Linux または Windows ベースのクラスターで機能する Java バージョンについては、「[Process events from Azure Event Hubs with Storm on HDInsight (Java) (HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (Java))](hdinsight-storm-develop-java-event-hub-topology.md)」を参照してください。

## 前提条件

* [HDInsight クラスターでの Apache Storm](hdinsight-apache-storm-tutorial-get-started.md)

* [Azure Event Hub](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

* [Azure .NET SDK](http://azure.microsoft.com/downloads/)

* [HDInsight Tools for Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

## 完成したプロジェクト

このチュートリアルで作成したプロジェクトの完全なバージョンを GitHub からダウンロードできます ([eventhub-storm-hybrid](https://github.com/Blackmist/eventhub-storm-hybrid))。ただし、このチュートリアルの手順に従って構成設定を指定する必要があります。

> [AZURE.NOTE] 完成したプロジェクトを使用する場合は、**NuGet パッケージ マネージャー**を使用して、このソリューションに必要なパッケージを復元する必要があります。

## Event Hubs スパウトとボルト

Event Hubs スパウトとボルトは Java コンポーネントで、これらのコンポーネントを使用して Apache Storm から Event Hubs を簡単に操作できます。これらのコンポーネントは Java で記述されていますが、HDInsight Tools for Visual Studio を使用して、C# と Java のコンポーネントが混在するハイブリッド トポロジを作成できます。

スパウトとボルトは、**eventhubs-storm-spout-0.9-jar-with-dependencies.jar** という名前の 1 つの Java アーカイブ (.jar) ファイルとして配布されます。

### jar ファイルのダウンロード

**eventhubs-storm-spout-0.9-jar-with-dependencies.jar** ファイルの最新バージョンは、**lib** フォルダーの「<a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">HDInsight Storm examples</a>」プロジェクトに含まれています。ファイルをダウンロードするには、次のいずれかの方法を実行してください。

> [AZURE.NOTE] スパウトとボルトは Apache Storm プロジェクトに組み込むために送信されています。詳細については、GitHub の「<a href="https://github.com/apache/storm/pull/336/files">STORM-583: Initial check-in for storm-event hubs (STORM-583: Storm-event hub の初期チェックイン)</a>」をご覧ください。

* **ZIP ファイルをダウンロードする**: <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">HDInsight Storm examples</a> サイトの右側のウィンドウで **[Download ZIP]** を選択し、プロジェクトが含まれている .zip ファイルをダウンロードします。

	![ZIP のダウンロード ボタン](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)

	ファイルをダウンロードした後に、アーカイブを抽出できます。ファイルは **lib** ディレクトリに保存されます。

* **プロジェクトを複製する**: <a href="http://git-scm.com/" target="_blank">Git</a> をインストール済みの場合は、次のコマンドを使用してリポジトリをローカルに複製し、**lib** ディレクトリにあるファイルを見つけます。

		git clone https://github.com/hdinsight/hdinsight-storm-examples

## Event Hub の構成

Event Hubs は、この例のデータ ソースです。新しい Event Hub を作成するには、次の手順に従います。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)から、**[新規作成]**、**[Service Bus]**、**[Event Hub]**、**[カスタム作成]** の順に選択します。

2. **[新しい Event Hub の追加]** 画面で **Event Hub 名**を入力し、ハブを作成する **[リージョン]** を選択して、新しい名前空間を作成するか、既存の名前空間を選択します。**矢印**をクリックして続行します。

	![ウィザード ページ 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE] 待機時間とコストを削減するために、HDInsight サーバーで使用する Storm と同じ **[場所]** を選択する必要があります。

2. **[Event Hub の構成]** 画面で、**パーティション カウント**と**メッセージ保持**の値を入力します。この例では、パーティション カウントに 10 を、メッセージ保持に 1 を使用します。パーティション数を書き留めておきます。この値は後で必要になります。

	![ウィザード ページ 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. Event Hub が作成されたら、名前空間を選択し、**[Event Hubs]** を選択して、先ほど作成した Event Hub を選択します。

4. **[構成]** を選択し、次の情報を使用して新しいアクセス ポリシーを 2 つ作成します。

	<table>
<tr><th>名前</th><th>アクセス許可</th></tr>
<tr><td>ライター</td><td>送信</td></tr>
<tr><td>閲覧者</td><td>リッスン</td></tr>
</table>アクセス許可の作成後、ページの下部にある **[保存]** アイコンをクリックします。これにより、この Event Hub に対する送信 (ライター) とリッスン (リーダー) に使用する共有アクセス ポリシーが作成されます。

	![ポリシー](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. ポリシーの保存後、ページの下部にある **[共有アクセス キー生成コンポーネント]** を使用して、**ライター**と**リーダー**のポリシーのキーを取得します。後で使用できるように、これらを保存します。

## テーブル ストレージの構成

テーブル ストレージは、Event Hubs から読み取った値を格納するために使用されます。テーブル ストレージは、Visual Studio から**サーバー エクスプローラー**を使用して簡単に表示できます。新しいテーブル ストレージを作成するには、次の手順に従います。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)から、**[新規]**、**[Data Services]**、**[ストレージ]**、**[簡易作成]** の順に選択します。

	![ストレージの簡易作成](./media/hdinsight-storm-develop-csharp-event-hub-topology/storagecreate.png)

2. ストレージ アカウントの **[名前]** を入力し、**[場所]** を選択したら、**チェックマーク**をクリックにしてストレージ アカウントを作成します。

	> [AZURE.NOTE] 待機時間とコストを削減するために、Event Hubs および HDInsight で使用する Storm と同じ **[場所]** を選択する必要があります。

3. 新しいストレージ アカウントがプロビジョニングされたら、アカウントを選択し、ページの下部にある **[アクセス キーの管理]** のリンクを使用して、**[ストレージ アカウント名]** と **[プライマリ アクセス キー]** を取得します。後で使用するため、この情報を保存します。

	![アクセス キー](./media/hdinsight-storm-develop-csharp-event-hub-topology/managekeys.png)

## EventHubWriter の作成

このセクションでは、Event Hubs のボルトを使用して Event Hubs にデータを書き込むトポロジを作成します。

1. HDInsight Tools for Visual Studio の最新バージョンをまだインストールしていない場合は、「<a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">HDInsight Tools for Visual Studio の使用開始</a>」をご覧ください。

2. Visual Studio を開いて、**[ファイル]**、**[新規]**、**[プロジェクト]** の順に選択します。

3. **[新しいプロジェクト]** 画面で、**[インストール済]**、**[テンプレート]**の順に展開して **[HDInsight]** を選択します。テンプレートの一覧から、**[Storm Application]** を選択します。画面の下部に、アプリケーションの名前として「**EventHubWriter**」と入力します。

	![画像](./media/hdinsight-storm-develop-csharp-event-hub-topology/newproject.png)

4. プロジェクトの作成後、次のファイルが生成されます。

	* **Program.cs**: プロジェクトのトポロジを定義します。既定では、スパウトとボルト 1 つずつで構成される既定のトポロジが作成されます。

	* **Spout.cs**: スパウトの例

	* **Bolt.cs**: ボルトの例。Event Hubs のボルトを使用して Event Hub に書き込むため、これは後で削除します。

### 構成

1. **ソリューション エクスプローラー**で **EventHubWriter** を右クリックし、**[プロパティ]** を選択します。

2. プロジェクトのプロパティで、**[設定]** を選択し、**[このプロジェクトには既定の設定ファイルが含まれていません。ファイルを作成するには、ここをクリックしてください。]** を選択します。

3. 次の設定を入力します。**[値]** 列では、前の手順で作成した Event Hub の情報を使用します。

	<table>
<tr><th style="text-align:left">名前</th><th style="text-align:left">型</th><th style="text-align:left">Scope</th></tr>
<tr><td style="text-align:left">EventHubPolicyName</td><td style="text-align:left">string</td><td style="text-align:left">アプリケーション</td></tr>
<tr><td style="text-align:left">EventHubPolicyKey</td><td style="text-align:left">文字列</td><td style="text-align:left">アプリケーション</td></tr>
<tr><td style="text-align:left">EventHubNamespace</td><td style="text-align:left">string</td><td style="text-align:left">アプリケーション</td></tr>
<tr><td style="text-align:left">EventHubName</td><td style="text-align:left">string</td><td style="text-align:left">アプリケーション</td></tr>
<tr><td style="text-align:left">EventHubPartitionCount</td><td style="text-align:left">int</td><td style="text-align:left">アプリケーション</td></tr>
</table>

4. **[プロパティ]** ページを保存して閉じます。

### トポロジの定義

1. **ソリューション エクスプローラー**で **[Bolt.cs]** を右クリックし、**[削除]** を選択します。Java Event Hubs のボルトを使用するので、このファイルは不要です。

2. **Program.cs** ファイルを開き、`TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubWriter");` という行の直後に次のコードを追加します。

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		List<string> javaDeserializerInfo =
            new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer", "java.lang.String" };

	最初の行は、先ほど定義したプロパティのパーティション数です。2 行目は、スパウトによって生成される JSON データを Java コンポーネントで使用できるように、`java.lang.String` にシリアル化解除するデシリアライザーを定義します。

4. 次のコードを見つけます。

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	これを次のコードに置き換えます。

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"Event"}}
            },
            partitionCount).
            DeclareCustomizedJavaDeserializer(javaDeserializerInfo);

	このコードは、スパウトを作成し、このコンポーネントの並列処理のヒントとして Event Hubs パーティション数を使用します。このコードは、各パーティションのスパウトのインスタンスを作成します。

	また、これにより、先ほど作成したデシリアライザーはこのコンポーネントからの出力ストリームに関連付けられます。これにより、ダウンストリームの EventHubSpout コンポーネントで C# スパウトから生成されたデータを使用できます。

5. 前のコードの直後に、次のコードを追加します。

		JavaComponentConstructor constructor =
            JavaComponentConstructor.CreateFromClojureExpr(
            String.Format(@"(com.microsoft.eventhubs.bolt.EventHubBolt. (com.microsoft.eventhubs.bolt.EventHubBoltConfig. " +
            @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
            Properties.Settings.Default.EventHubPolicyName,
            Properties.Settings.Default.EventHubPolicyKey,
            Properties.Settings.Default.EventHubNamespace,
            "servicebus.windows.net", //suffix for servicebus fqdn
            Properties.Settings.Default.EventHubName,
			"true"));

	このコードは、Java ボルトの新しいコンストラクターを作成します。このコンストラクターは、ボルトの新しいインスタンスを構成するために実行時に使用されます。この場合、<a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a> を使用して、前の手順で追加した Event Hubs 構成情報をスパウトに構成します。具体的には、このコードは、次のことを行うために HDInsight によって実行時に使用されます。

	* 指定した Event Hubs 情報を使用して **com.microsoft.eventhubs.bolt.EventHubBoltConfig** の新しいインスタンスを作成します。
	* **EventHubBoltConfig** インスタンスを渡すことで、**com.microsoft.eventhubs.bolt.EventHubBolt** の新しいインスタンスを作成します。

6. 次のコードを見つけます。

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	これを次のコードに置き換えます。

		topologyBuilder.SetJavaBolt(
            "EventHubBolt",
            constructor,
            partitionCount).
            shuffleGrouping("Spout");

	このコードは、前の手順で作成した **JavaComponentConstructor** をボルトとしてトポロジで使用するように指示します。このトポロジのコンポーネントは、"EventHubBolt" というわかりやすい名前で参照できます。 並行処理のヒントには Event Hub のパーティション数が設定され、スパウトによって生成されるデータ ("Spout") にサブスクライブします。

これで **Program.cs** が完了しました。トポロジの定義は完了しましたが、次は、Event Hubs のボルトが使用できる形式でデータを生成するように **Spout.cs** を変更します。

> [AZURE.NOTE] このトポロジは、既定で、例として十分な 1 つのワーカー プロセスを作成します。これを運用環境のクラスターに適用する場合、次を追加して、ワーカーの数を変更する必要があります。

    StormConfig config = new StormConfig();
    config.setNumWorkers(1);
    topologyBuilder.SetTopologyConfig(config);


### スパウトを変更する

Event Hubs のボルトは、Event Hub にルーティングされる単一の文字列値を予期しています。次の例では、JSON 文字列を生成するように既定の **Spout.cs** ファイルを変更します。

1. **ソリューション エクスプローラー**で **Spout.cs** を開き、ファイルの先頭に次の行を追加します。

		using Newtonsoft.Json;
		using Newtonsoft.Json.Linq;

	これにより、JSON データをより簡単に処理できます。
    
    > [AZURE.NOTE] JSON.NET パッケージが既にインストールされています。これは、C# Storm トポロジーに使用される SCP.NET フレームワークで必要です。

3. 次のコードを見つけます。

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));

	これを次のコードに置き換えます。

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer());

	これにより、スパウトによって作成されるデータの定義を変更し、**string** データと先ほどトポロジ (program.cs 内) で宣言された **CustomizedInteropJSONSerializer** を使用するようにします。

2. **NextTuple** メソッドを次の内容に置き換えます。

		public void NextTuple(Dictionary<string, Object> parms)
        {
            JObject eventData = new JObject();
            eventData.Add("deviceId", r.Next(10));
            eventData.Add("deviceValue", r.Next());
            ctx.Emit(new Values(eventData.ToString(Formatting.None)));
        }

	このコードは、デバイス ID である値をランダムに生成し、Json.NET を使って、これらの値を使用する JSON オブジェクトを出力します。

3. **Spout.cs** ファイルを保存します。

ここまでで、ランダムなデータを生成し、生成したデータを、Event Hubs のボルトを使用して Event Hubs に格納する基本的なトポロジを作成しました。次にリーダーを作成します。

## EventHubReader の作成

このセクションでは、Event Hubs スパウトを使用して Event Hubs からデータを読み取るトポロジを作成します。

2. Visual Studio で、**[ファイル]**、**[新規]**、**[プロジェクト]** を選択します。

3. **[新しいプロジェクト]** 画面で、**[インストール済]**、**[テンプレート]**の順に展開して **[HDInsight]** を選択します。テンプレートの一覧から、**[Storm Application]** を選択します。画面の下部に、アプリケーションの名前として「**EventHubReader**」と入力します。

### 構成

1. **ソリューション エクスプローラー**で **EventHubReader** を右クリックし、**[プロパティ]** を選択します。

2. プロジェクトのプロパティで、**[設定]** を選択し、**[このプロジェクトには既定の設定ファイルが含まれていません。ファイルを作成するには、ここをクリックしてください。]** を選択します。

3. 次の設定を入力します。**[値]** 列では、前の手順で作成した Event Hub とストレージ アカウントの情報を使用します。

	<table>
<tr><th style="text-align:left">名前</th><th style="text-align:left">型</th><th style="text-align:left">Scope</th></tr>
<tr><th style="text-align:left">EventHubPolicyName</th><th style="text-align:left">string</th><th style="text-align:left">アプリケーション</th></tr>
<tr><th style="text-align:left">EventHubPolicyKey</th><th style="text-align:left">文字列</th><th style="text-align:left">アプリケーション</th></tr>
<tr><th style="text-align:left">EventHubNamespace</th><th style="text-align:left">string</th><th style="text-align:left">アプリケーション</th></tr>
<tr><th style="text-align:left">EventHubName</th><th style="text-align:left">string</th><th style="text-align:left">アプリケーション</th></tr>
<tr><th style="text-align:left">EventHubPartitionCount</th><th style="text-align:left">int</th><th style="text-align:left">アプリケーション</th></tr>
<tr><th style="text-align:left">StorageConnection</th><th style="text-align:left">(接続文字列)</th><th style="text-align:left">アプリケーション</th></tr>
<tr><th style="text-align:left">TableName</th><th style="text-align:left">string</th><th style="text-align:left">アプリケーション</th></tr>
</table>**TableName** には、イベントを格納するテーブルの名前を入力します。

    **StorageConnection** には、`DefaultEndpointsProtocol=https;AccountName=myAccount;AccountKey=myKey;` の値を入力します。**myAccount** と **myKey** を先ほど取得したストレージ アカウント名とキーに置き換えます。

	これらの値は、Event Hubs と Table Storage と通信するために、トポロジによって使用されます。

4. **[プロパティ]** ページを保存して閉じます。

### トポロジの定義

1. **ソリューション エクスプローラー**で **[Spout.cs]** を右クリックし、**[削除]** を選択します。Java Event Hubs スパウトを使用するので、このファイルは不要です。

2. **Program.cs** ファイルを開き、`TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubReader");` という行の直後に次のコードを追加します。

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		EventHubSpoutConfig ehConfig = new EventHubSpoutConfig(
                Properties.Settings.Default.EventHubPolicyName,
                Properties.Settings.Default.EventHubPolicyKey,
                Properties.Settings.Default.EventHubNamespace,
                Properties.Settings.Default.EventHubName,
                partitionCount);

	パーティション数が読み込まれ、ローカル変数に割り当てられます。これは複数回使用されます。

	`EventHubSpoutConfig` は、Event Hub スパウトの構成を定義します。この場合の Event Hubs の構成は、以前に追加しました。バックグラウンドでは、Java Event Hub スパウトを使用します。Event Hubs 情報を使用して、**com.microsoft.eventhubs.spout.EventHubSpoutConfig** の新しいインスタンスを作成します。

5. 次のコードを見つけます。

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	これを次のコードに置き換えます。

        topologyBuilder.SetEventHubSpout(
            "EventHubSpout", 
            ehConfig, 
            partitionCount); 

	これにより、トポロジに新しい Event Hub スパウトを作成し、前の手順から構成として `EventHubSpoutConfig` を使用するよう指示します。"EventHubSpout" は、スパウトの表示名を設定し、`partitionCount` は、並列処理のヒントを設定するために使用します。バックグラウンドで、指定した構成情報を使用して、**com.microsoft.eventhubs.spout.EventHubSpout** Java コンポーネントの新しいインスタンスを作成します。

2. 前のコードの直後に、次のコードを追加します。

         List<string> javaSerializerInfo = new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer" };

	これにより、Java コンポーネント (EventHubSpout など) によって生成された情報を、ダウンストリームの C# コンポーネントで使用できる JSON 形式へのシリアル化に使用されるカスタムのシリアライザーを作成します。

3. 次のコードを見つけます。

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	これを次のコードに置き換えます。

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            partitionCount,
            true).
            DeclareCustomizedJavaSerializer(javaSerializerInfo).
            shuffleGrouping("EventHubSpout");

	このコードは、トポロジでのボルト (Bolt.cs で定義) の使用を指示します。先ほど定義したカスタムのシリアライザーをここで使用し、このボルトがアップストリームの Java コンポーネントで生成されたデータを使用できるようにします。この場合、EventHubSpout です。

    > [AZURE.IMPORTANT] SetBolt の最後のパラメーター (`true` の値を使用) でこのボルトの ACK 機能を有効にします。これは、EventHubSpout コンポーネントでは、出力されるデータの ACK を想定するために必要です。ダウンストリームのコンポーネントで ACK が返されない場合、スパウトは約 1000 件のメッセージを処理した後に受信を停止します。

これで **Program.cs** が完了しました。ここまでで、トポロジを定義しました。次に、データをテーブル ストレージに書き込むためにヘルパー クラスを作成する必要があります。さらに、スパウトによって生成されたデータを認識できるように **Bolt.cs** を変更する必要があります。

> [AZURE.NOTE] このトポロジは、既定で、例として十分な 1 つのワーカー プロセスを作成します。これを運用環境のクラスターに適用する場合、次を追加して、ワーカーの数を変更する必要があります。

    StormConfig config = new StormConfig();
    config.setNumWorkers(1);
    topologyBuilder.SetTopologyConfig(config);


### ヘルパー クラスの作成

テーブル ストレージにデータを書き込む場合、書き込まれるデータを記述したクラスを作成する必要があります。

1. **ソリューション エクスプローラー**で **EventHubReader** プロジェクトを右クリックし、**[追加]**、**[新しいクラス]** の順に選択します。新しいクラスに **Devices.cs** という名前を付けます。

2. **Devices.cs** を開き、既定のコードを次のコードに置き換えます。

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using Microsoft.WindowsAzure.Storage.Table;

		namespace EventHubReader
		{
		    class Device : TableEntity
		    {
		        public int value { get; set; }

		        public Device() { }
		        public Device(int id)
		        {
		            this.PartitionKey = id.ToString();
		            this.RowKey = System.Guid.NewGuid().ToString();
		        }
		    }
		}

	このコードは、テーブル ストレージに、パーティション キー (Event Hub から読み取られたデバイス ID に設定される)、一意の行キー、Event Hub から読み取られた値で構成されるエンティティを作成します。また、各エンティティがテーブルに挿入された時点で、エンティティのタイムスタンプが自動的に作成されます。

### ボルトの変更

1. **ソリューション エクスプローラー**で **EventHubReader** プロジェクトを展開し、**Bolt.cs** ファイルを開きます。ファイルの先頭に次のコードを追加します。

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;

	これにより、ボルトからの JSON データの操作がより簡単になり、テーブル ストレージにデータを書き込めるようになります。

2. `private int count;` ステートメントを見つけて次の内容に置き換えます。

        private CloudTable table;

	これは、テーブルに接続するときに使用されます。

4. 次のコードを見つけます。

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));

	これを次のコードに置き換えます。

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
        this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());

	このコードは、ボルトに対して、**int** ではなく **string** 値を受け取り、先ほどのトポロジ (program.cs ファイル内) で宣言された **CustomizedInteropJSONDeserialzer** を使用してデータをシリアル化解除する必要があることを指示します。

3. 前のコードの直後に、次のコードを追加します。

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(Properties.Settings.Default.StorageConnection);
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
        table = tableClient.GetTableReference(Properties.Settings.Default.TableName);
        table.CreateIfNotExists();

	このコードは、前の手順で構成した接続文字列を使用して **events** テーブルに接続します。テーブルが存在しない場合は、作成されます。

2. **Execute** メソッドを見つけて次の内容に置き換えます。

		public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Processing events");
            string eventValue = (string)tuple.GetValue(0);
            if (eventValue != null)
            {
                JObject eventData = JObject.Parse(eventValue);

                Device device = new Device((int)eventData["deviceId"]);
                device.value = (int)eventData["deviceValue"];

                TableOperation insertOperation = TableOperation.Insert(device);

                table.Execute(insertOperation);
                this.ctx.Ack(tuple);
            }
        }

	これは、スパウトからの JSON データを Json.NET を使用して解析し、**deviceId** および **deviceValue** フィールドを選択します。初期化時に、**deviceId** を使用して新しい **Device** オブジェクトが作成され、テーブルのパーティション キーが設定されます。値が **deviceValue** に設定されて、最後にエンティティがテーブルに挿入されます。

    エンティティがテーブルに挿入された後、タプルに `Ack()` が呼び出され、データが正常に処理されたことをスパウトに通知します。

    > [AZURE.IMPORTANT] EventHubSpout コンポーネントは、このボトルなどのダウンストリームのコンポーネントから各タプルに ACK を想定します。ACK を受信しない場合、EventHubSpout はタプルの処理が失敗したものと見なします。

これで、Event Hub からデータを読み取り、読み取ったデータを、**events** という名前のテーブルにある Table Storage に格納するトポロジが完成しました。

## トポロジのデプロイ

1. **ソリューション エクスプローラー**で **EventHubReader** プロジェクトを右クリックし、**[HDInsight での Storm に送信]** を選択します。

	![Storm に送信](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. **[トポロジの送信]** 画面で該当する **[Storm クラスター]** を選択します。**[追加の構成]** を展開し、**[Java ファイル パス]**、**[...]** の順に選択し、前の手順でダウンロードした **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** ファイルがあるディレクトリを選択します。最後に、**[送信]** をクリックします。

	![送信ダイアログの画像](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. トポロジが送信されると、**[Storm トポロジ ビューアー]** が表示されます。ダイアログの左側にある **EventHubReader** トポロジを選択し、トポロジの統計情報を表示します。Event Hubs に書き込まれたイベントはまだないため、現時点では、何も発生していません。

	![ストレージ ビューの例](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. **ソリューション エクスプローラー**で **EventHubWriter** プロジェクトを右クリックし、**[HDInsight での Storm に送信]** を選択します。

2. **[トポロジの送信]** 画面で該当する **[Storm クラスター]** を選択します。**[追加の構成]** を展開し、**[Java ファイル パス]**、**[...]** の順に選択し、前の手順でダウンロードした **eventhubs-storm-spout-0.9-jar-with-dependencies.jar **ファイルがあるディレクトリを選択します。最後に、**[送信]** をクリックします。

5. トポロジが送信されたら、**[Storm トポロジ ビューアー]** でトポロジ一覧を最新情報に更新し、両方のトポロジがクラスターで実行中であることを確認します。

6. 両方のトポロジが実行中である場合は、**[サーバー エクスプローラー]** を選択し、**[Azure]**、**[ストレージ]** の順に展開し、最後に前の手順で作成したストレージ アカウントを選択します。そのストレージ アカウントで、**[テーブル]** を展開します。最後に、**events** テーブルをダブルクリックしてテーブルを開きます。**EventHubReader** トポロジに従ってテーブルに格納されたデータが表示されます。

	* **EventHubWriter** トポロジによってイベントが生成されています。生成されたイベントは Event Hub に書き込まれます。

	* **EventHubReader** が Event Hubs からイベントを読み取り、**events** テーブルのテーブル ストレージに格納します。

## トポロジの停止

トポロジを停止するには、**[Storm トポロジ ビューアー]** で各トポロジを選択し、**[強制終了]** をクリックします。

![トポロジの強制終了の画像](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## メモ

### チェックポイント機能

EventHubSpout は Zookeeper ノードに対する状態へのチェックポイントを定期的に設定し、キューから読み取ったメッセージの現在のオフセットを保存します。これにより、コンポーネントは次のシナリオで保存されたオフセットでメッセージの受信を開始できます。

* コンポーネントのインスタンスは失敗し、再起動されます。

* ノードを追加または削除して、クラスターを拡張または圧縮します。

* トポロジが強制終了され、**同じ名前で**再起動されます。

また、保存されたチェックポイントを WASB (HDInsight クラスターで使用される Azure のストレージ) にエクスポート、インポートできます。 これを実行するスクリプトは、**c:\\apps\\dist\\storm-0.9.3.2.2.1.0-2340\\zkdatatool-1.0\\bin** の HDInsight クラスター上の Storm に配置されています。

>[AZURE.NOTE] クラスターにインストールされた Storm のバージョンは今後変更される可能性があるため、パスのバージョン番号は異なる場合があります。

このディレクトリのスクリプトは次のとおりです。

* **stormmeta\_import.cmd**: クラスターの既定のストレージ コンテナーから Zookeeper にすべての Storm メタデータをインポートします。

* **stormmeta\_export.cmd**: Zookeeper からクラスターの既定のストレージ コンテナーにすべての Storm メタデータをエクスポートします。

* **stormmeta\_delete.cmd**: Zookeeper からのすべての Storm メタデータを削除します。

インポートのエクスポートにより、クラスターを削除する必要がある一方で、新しいクラスターを再びオンラインにする際にハブの現在のオフセットから処理を再開する場合、チェックポイントのデータを保持できます。

> [AZURE.NOTE] データは既定のストレージ コンテナーに保存されるため、新しいクラスターで以前のクラスターと同じストレージ アカウントとコンテナーを使用する**必要があります**。

## 概要

このドキュメントでは、C# トポロジから Java Event Hubs スパウトおよびボルトを使用して、Azure Event Hub のデータを操作する方法について説明しました。C# トポロジの作成の詳細については、次の記事を参照してください。

* [Visual Studio を使用して HDInsight で Apache Storm の C# トポロジを開発する](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [HDInsight 上の Storm に関するトポロジ例](hdinsight-storm-example-topology.md)
 

<!---HONumber=AcomDC_0204_2016-->