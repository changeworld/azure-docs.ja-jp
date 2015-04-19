<properties
   pageTitle="HDInsight で Storm を使用して Event Hub のイベントを処理する | Azure"
   description="Visual Studio で HDInsight Tools for Visual Studio を使用して作成した C# Storm トポロジによって Event Hub のデータを処理する方法について説明します。"
   services="hdinsight"
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
   ms.date="03/03/2015"
   ms.author="larryfr"/>

#HDInsight で Storm を使用して Azure Event Hub のイベントを処理する (＃C)

Azure Event Hub では、Web サイト、アプリ、デバイスで発生する大量のデータを処理できます。Event Hub Spout では、HDInsight で Apache Storm を使用してこのデータをリアルタイムで簡単に分析できます。また、Event Hub Bolt を使用して Storm から Event Hub にデータを書き込むこともできます。 

このドキュメントでは、HDInsight Tools for Visual Studio と Event Hub Spout および Event Hub Bold を使用して、次の 2 つのハイブリッド C#/Java トポロジを作成する方法について説明します。

* **EventHubWriter** - データをランダムに生成して Event Hub に書き込む

* **EventHubReader** - Event Hub からデータを読み取って Azure テーブル ストレージに書き込む

##前提条件

* <a href="../hdinsight-storm-getting-started/" target="_blank">HDInsight クラスターでの Apache Storm</a>

* <a href="../service-bus-event-hubs-csharp-ephcs-getstarted/" target="_blank">Azure Event Hub</a>

* <a href="http://azure.microsoft.com/downloads/" target="_blank">Azure .NET SDK</a>

* <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">HDInsight Tools for Visual Studio</a> - 2015/2/17 バージョンまたはそれ以降

##完成したプロジェクト

この記事で作成したプロジェクトの完全なバージョンをダウンロードすることができます ([https://github.com/Blackmist/eventhub-storm-hybrid](https://github.com/Blackmist/eventhub-storm-hybrid))。ただし、このドキュメントの手順に従って構成設定を指定する必要があります。

> [AZURE.NOTE] 完成したプロジェクトを使用する場合は、**NuGet パッケージ マネージャー**を使用して、このソリューションに必要なパッケージを復元する必要があります。

##Event Hub のスパウトとボルト

Event Hub のスパウトとボルトは Java コンポーネントで、これらのコンポーネントを使用して Apache Storm から Event Hub を簡単に操作できます。これらのコンポーネントは Java で記述されていますが、HDInsight Tools for Visual Studio を使用して、C# と Java のコンポーネントが混在するハイブリッド トポロジを作成できます。

スパウトとボルトは、**eventhubs-storm-spout-0.9-jar-with-dependencies.jar** という名前の単一の Java アーカイブ (.jar) ファイルとして配布されます。

###jar のダウンロード

**eventhubs-storm-spout-0.9-jar-with-dependencies.jar** ファイルの最新バージョンは、**lib** フォルダーの <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">https://github.com/hdinsight/hdinsight-storm-examples</a> プロジェクトに含まれています。ファイルをダウンロードするには、次のいずれかの方法を実行してください。

> [AZURE.NOTE] スパウトとボルトは Apache Storm プロジェクトに組み込むために送信されています。詳細については、<a href="https://github.com/apache/storm/pull/336/files">プロトコル要求に関するページ</a>を参照してください。

* **ZIP ファイルをダウンロードする** - <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">https://github.com/hdinsight/hdinsight-storm-examples</a> サイトで **[Download ZIP]** を選択し、プロジェクトが含まれている .zip ファイルをダウンロードします。

	![download zip button](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)

	ダウンロードした後、アーカイブを抽出できます。ファイルは **lib** ディレクトリに保存されます。

* **プロジェクトを複製する** - <a href="http://git-scm.com/" target="_blank">Git</a> をインストール済みの場合は、次のコマンドを使用してリポジトリをローカルに複製し、**lib** ディレクトリにあるファイルを見つけます。

		git clone https://github.com/hdinsight/hdinsight-storm-examples

##Event Hub の構成

Event Hub は、この例のデータ ソースです。新しい Event Hub を作成するには、次の手順に従います。

1. [Azure ポータル](https://manage.windowsazure.com)から、**[新規]、[Service Bus]、[Event Hub]、[カスタム作成]** の順に選択します。

2. **[新しい Event Hub の追加]** ダイアログで **Event Hub 名**を入力し、ハブを作成する **[リージョン]** を選択して、新しい名前空間を作成するか、既存の名前空間を選択します。最後に、**矢印**をクリックします。

	![wizard page 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE] 待機時間とコストを削減するために、HDInsight で使用する Storm と同じ **[場所]** を選択する必要があります。

2. **[Event Hub の構成]** ダイアログで、**パーティション カウント**と**メッセージ保持の値**を入力します。この例では、パーティション カウントに 10 を、メッセージ保持に 1 を使用します。パーティション数を書き留めておきます。この値は後で必要になります。

	![wizard page 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. Event Hub が作成されたら、名前空間を選択し、**[Event Hubs]** を選択します。最後に、先ほど作成した Event Hub を選択します。

4. **[構成]** を選択し、次の情報を使用して新しいアクセス ポリシーを 2 つ作成します。

	<table>
	<tr><th>名前</th><th>アクセス許可</th></tr>
	<tr><td>ライター</td><td>Send</td></tr>
	<tr><td>リーダー</td><td>Listen</td></tr>
	</table>

	アクセス許可の作成後、ページの下部にある **[保存]** アイコンをクリックします。これにより、この Event Hub に対する送信 (ライター) とリッスン (リーダー) に使用する共有アクセス ポリシーが作成されます。

	![policies](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. ポリシーの保存後、ページの下部にある **[共有アクセス キー生成コンポーネント]** を使用して、**ライター**と**リーダー**のポリシーの両方のキーを取得します。後で使用できるように、これらを保存します。

##テーブル ストレージの構成

テーブル ストレージは、Event Hub から読み取った値を格納するために使用されます。テーブル ストレージは、Visual Studio から**サーバー エクスプローラー**を使用して簡単に表示できます。新しいテーブル ストレージを作成するには、次の手順に従います。

1. [Azure ポータル](https://manage.windowsazure.com)から、**[新規]、[データ サービス]、[ストレージ]、[簡易作成]** の順に選択します。

	![quick create storage](./media/hdinsight-storm-develop-csharp-event-hub-topology/storagecreate.png)

2. ストレージ アカウントの **[名前]** を入力し、**[場所]** を選択したら、**チェックマーク**をオンにしてストレージ アカウントを作成します。

	> [AZURE.NOTE] 待機時間とコストを削減するために、Event Hub および HDInsight で使用する Storm と同じ **[場所]** を選択する必要があります。

3. 新しいストレージ アカウントがプロビジョニングされたら、アカウントを選択し、ページの下部にある **[アクセス キーの管理]** のリンクを使用して、**[ストレージ アカウント名]** と **[プライマリ アクセス キー]** を取得します。後で使用するため、この情報を保存します。

	![access keys](./media/hdinsight-storm-develop-csharp-event-hub-topology/managekeys.png)

##EventHubWriter の作成

このセクションでは、Event Hub Bolt を使用して Event Hub にデータを書き込むトポロジを作成します。

1. HDInsight Tools for Visual Studio の最新バージョンをまだインストールしていない場合は、「<a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">HDInsight Tools for Visual Studio の使用開始</a>」を参照してください。

2. Visual Studio を開いて、**[ファイル]**、**[新規]**、**[プロジェクト]** の順に選択します。

3. **[新しいプロジェクト]** ダイアログで、**[インストール済]**、**[テンプレート]** の順に展開して **[HDInsight]** を選択します。テンプレートの一覧から、**[Storm Application]** を選択します。ダイアログの下部に、アプリケーションの名前として「**EventHubWriter**」と入力します。

	![image](./media/hdinsight-storm-develop-csharp-event-hub-topology/newproject.png)

4. プロジェクトの作成後、次ファイルが生成されます。

	* **Program.cs** - プロジェクトのトポロジを定義します。既定では、スパウトとボルト 1 つずつで構成される既定のトポロジが作成されます。

	* **Spout.cs** - スパウトの例

	* **Bolt.cs** - ボルトの例。Event Hub Bolt を使用して Event Hub に書き込むため、これは後で削除します。

###構成

1. **ソリューション エクスプローラー**で **EventHubWriter** を右クリックし、**[プロパティ]** を選択します。

2. プロジェクトのプロパティで、**[設定]** を選択し、**[このプロジェクトには既定の設定ファイルが含まれていません。ファイルを作成するには、ここをクリックしてください。**] を選択します。

3. 次の設定を入力します。**[値]** 列では、前の手順で作成した Event Hub の情報を使用します。

	<table>
	<tr><th style="text-align:left">名前</th><th style="text-align:left">種類</th><th style="text-align:left">Scope</th></tr>
	<tr><td style="text-align:left">EventHubPolicyName</td><td style="text-align:left">string</td><td style="text-align:left">アプリケーション</td></tr>
	<tr><td style="text-align:left">EventHubPolicyKey</td><td style="text-align:left">string</td><td style="text-align:left">アプリケーション</td></tr>
	<tr><td style="text-align:left">EventHubNamespace</td><td style="text-align:left">string</td><td style="text-align:left">アプリケーション</td></tr>
	<tr><td style="text-align:left">EventHubName</td><td style="text-align:left">string</td><td style="text-align:left">アプリケーション</td></tr>
	<tr><td style="text-align:left">EventHubPartitionCount</td><td style="text-align:left">int</td><td style="text-align:left">アプリケーション</td></tr>
	</table>

4. プロパティ ページを保存して閉じます。

###トポロジを定義する

1. **ソフトウェア エクスプローラー**で **[Bolt.cs]** を右クリックし、**[削除]** を選択します。Java Event Hub のボルトを使用するので、このファイルは不要です。

2. **Program.cs** ファイルを開き、`TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubWriter");` という行の直後に次の行を追加します。

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		List<string> javaDeserializerInfo =
            new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer", "java.lang.String" };

	最初の行は、先ほど定義したプロパティのパーティション数です。2 行目は、スパウトによって生成される JSON データを Event Hub Bolt で使用できるように、 `java.lang.String` にシリアル化解除するデシリアライザーを定義します。

4. 次のコードを見つけます。

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Replace it with the following.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"Event"}}
            },
            partitionCount).
            DeclareCustomizedJavaDeserializer(javaDeserializerInfo);

	このコードは、スパウトを作成し、このコンポーネントの並列処理のヒントとして Event Hub パーティション数を使用します。このコードは、各パーティションのスパウトのインスタンスを作成します。
	
	また、先ほど作成したデシリアライザーを出力ストリームで使用するように宣言します。

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

	このコードは、Java ボルトの新しいコンストラクターを作成します。このコンストラクターは、ボルトの新しいインスタンスを構成するために実行時に使用されます。この場合、<a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a> を使用して、前の手順で追加した Event Hub 構成情報をスパウトに構成します。具体的には、このコードは、次のことを行うために HDInsight によって実行時に使用されます。

	* 指定した Event Hub 情報を使用して **com.microsoft.eventhubs.bolt.EventHubBoltConfig** の新しいインスタンスを作成します。
	* **EventHubBoltConfig** インスタンスを渡すことで、**com.microsoft.eventhubs.bolt.EventHubBolt** の新しいインスタンスを作成します。

6. 次のコードを見つけます。

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Replace it with the following.

		topologyBuilder.SetJavaBolt(
            "EventHubBolt",
            constructor,
            partitionCount).
            shuffleGrouping("Spout");

	このコードは、前の手順で作成した **JavaComponentConstructor** をボルトとしてトポロジで使用するように指示します。このトポロジのコンポーネントは、"EventHubBolt" というわかりやすい名前で参照できます。並行処理のヒントには Event Hub のパーティション数が設定され、スパウトによって生成されるデータ ("Spout") にサブスクライブします。

これで **Program.cs** が完了しました。トポロジの定義は完了しましたが、次は、Event Hub Bolt が使用できる形式でデータを生成するように **Spout.cs** を変更します。

###スパウトを変更する

Event Hub Bolt は、Event Hub にルーティングされる単一の文字列値を予期しています。次の例では、JSON 文字列を生成するように既定の **Spout.cs** ファイルを変更します。

1. **ソリューション エクスプローラー**で **EventHubWriter** プロジェクトを右クリックし、**[Nuget パッケージの管理]** を選択します。**Json.Net** パッケージを探し、それをソリューションに追加します。これにより、Bolt を使用して Event Hub に送信される JSON データを簡単に作成できます。

1. **Spout.cs** を開き、ファイルの先頭に次の行を追加します。

		using Newtonsoft.Json;
		using Newtonsoft.Json.Linq;

	これにより、JSON データをより簡単に処理できます。

3. 次のコードを見つけます。

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));

	これを次のコードに置き換えます。

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer());

	これにより、スパウトによって作成されるデータの定義を変更し、**string** データと**カスタム JSON シリアライザー**を使用するようにします。

2. **NextTuple** メソッドを次の内容に置き換えます。

		public void NextTuple(Dictionary<string, Object> parms)
        {
            JObject eventData = new JObject();
            eventData.Add("deviceId", r.Next(10));
            eventData.Add("deviceValue", r.Next());
            ctx.Emit(new Values(eventData.ToString(Formatting.None)));
        }

	このコードは、デバイス ID である値をランダムに生成し、Json.net を使って、これらの値を使用する JSON オブジェクトを出力します。

3. **Spout.cs** ファイルを保存します。

ここまでで、ランダムなデータを生成し、生成したデータを、Event Hub Bolt を使用して Event Hub に格納する基本的なトポロジを作成しました。次にリーダーを作成します。

##EventHubReader の作成

このセクションでは、Event Hub Spout を使用して Event Hub からデータを読み取るトポロジを作成します。

2. Visual Studio を開いて、**[ファイル]**、**[新規]**、**[プロジェクト]** の順に選択します。

3. **[新しいプロジェクト]** ダイアログで、**[インストール済]**、**[テンプレート]** の順に展開して **[HDInsight]** を選択します。テンプレートの一覧から、**[Storm Application]** を選択します。ダイアログの下部に、アプリケーションの名前として「**EventHubReader**」と入力します。

###構成

1. **ソリューション エクスプローラー**で **EventHubReader** を右クリックし、**[プロパティ]** を選択します。

2. プロジェクトのプロパティで、**[設定]** を選択し、**[このプロジェクトには既定の設定ファイルが含まれていません。ファイルを作成するには、ここをクリックしてください。**] を選択します。

3. 次の設定を入力します。**[値]** 列では、前の手順で作成した Event Hub とストレージ アカウントの情報を使用します。

	<table>
	<tr><th style="text-align:left">名前</th><th style="text-align:left">種類</th><th style="text-align:left">Scope</th></tr>
	<tr><th style="text-align:left">EventHubPolicyName</th><th style="text-align:left">string</th><th style="text-align:left">アプリケーション</th></tr>
	<tr><th style="text-align:left">EventHubPolicyKey</th><th style="text-align:left">string</th><th style="text-align:left">アプリケーション</th></tr>
	<tr><th style="text-align:left">EventHubNamespace</th><th style="text-align:left">string</th><th style="text-align:left">アプリケーション</th></tr>
	<tr><th style="text-align:left">EventHubName</th><th style="text-align:left">string</th><th style="text-align:left">アプリケーション</th></tr>
	<tr><th style="text-align:left">EventHubPartitionCount</th><th style="text-align:left">int</th><th style="text-align:left">アプリケーション</th></tr>
	<tr><th style="text-align:left">StorageConnection</th><th style="text-align:left">(接続文字列)</th><th style="text-align:left">アプリケーション</th></tr>
	<tr><th style="text-align:left">TableName</th><th style="text-align:left">string</th><th style="text-align:left">アプリケーション</th></tr>
	</table>

	**TableName** には、イベントを格納するテーブルの名前を入力します。

	これらの値は、Event Hub とテーブル ストレージと通信するために、トポロジによって使用されます。

4. プロパティ ページを保存して閉じます。

###トポロジを定義する

1. **ソフトウェア エクスプローラー**で **[Spout.cs]** を右クリックし、**[削除]** を選択します。Java Event Hub のスパウトを使用するので、このファイルは不要です。

2. **Program.cs** ファイルを開き、`TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubReader");` という行の直後に次の行を追加します。

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
            String.Format(@"(com.microsoft.eventhubs.spout.EventHubSpout. (com.microsoft.eventhubs.spout.EventHubSpoutConfig. " +
                @"""{0}"" ""{1}"" ""{2}"" ""{3}"" {4} ""{5}""))",
                Properties.Settings.Default.EventHubPolicyName,
                Properties.Settings.Default.EventHubPolicyKey,
                Properties.Settings.Default.EventHubNamespace,
                Properties.Settings.Default.EventHubName,
                partitionCount,
                "")); //Last value is the zookeeper connection string - leave empty

	パーティション数は複数回使用されるため、読み取られてローカル変数に割り当てられます。

	 `JavaComponentConstructor` は、実行時の Java スパウトの構築方法を定義します。この場合、<a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a> を使用して、前の手順で追加した Event Hub 構成情報をスパウトに構成します。具体的には、このコードは、次のことを行うために HDInsight によって実行時に使用されます。

	* 指定した Event Hub 情報を使用して、**com.microsoft.eventhubs.spout.EventHubSpoutConfig** の新しいインスタンスを作成します。
	
	* **EventHubSpoutConfig** インスタンスを渡すことで、**com.microsoft.eventhubs.spout.EventHubSpout** の新しいインスタンスを作成します。

5. 次のコードを見つけます。

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Replace it with the following.

        topologyBuilder.SetJavaSpout(
            "EventHubSpout",
            constructor,
            partitionCount);

	このコードは、前の手順で作成した **JavaComponentConstructor** をスパウトとしてトポロジで使用するように指示し、スパウトに "EventHubSpout" という名前を付けます。また、このコンポーネントの並列処理のヒントに Event Hub のパーティション数を設定します。 

2. 前のコードの直後に、次のコードを追加します。

         List<string> javaSerializerInfo = new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer" };

	このコードは、Java スパウトによって生成された情報を JSON 形式にシリアル化するカスタム シリアライザーを作成します。

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
            partitionCount).
            DeclareCustomizedJavaSerializer(javaSerializerInfo).
            shuffleGrouping("EventHubSpout");

	このコードは、トポロジでのボルト (Bolt.cs で定義) の使用を指示します。追加コードは、Java スパウトである **EventHubSpout** に基づくこのコンポーネントによって使用されるデータに対して、トポロジでカスタム シリアライザーを使用することを指示します。

これで **Program.cs** が完了しました。ここまでで、トポロジを定義しました。次に、データをテーブル ストレージに書き込むためにヘルパー クラスを作成する必要があります。さらに、スパウトによって生成されたデータを認識できるように **Bolt.cs** を変更する必要があります。

###ヘルパー クラスの作成

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

	このコードは、テーブル ストレージに、パーティション キー (Event Hub から読み取られたデバイス ID に設定される)、一意の行キー、および Event Hub から読み取られた値で構成されるエンティティを作成します。また、各エンティティがテーブルに挿入された時点で、エンティティのタイムスタンプが自動的に作成されます。

###ボルトの変更

1. **ソリューション エクスプローラー**で **EventHubReader** プロジェクトを右クリックし、**[Nuget パッケージの管理]** を選択します。**Json.Net** パッケージを探し、それをソリューションに追加します。これにより、スパウトから受信した JSON データを簡単に処理できるようになります。また、テーブル ストレージに書き込みができるように、**Windows Azure Storage** パッケージを追加します。

1. **Bolt.cs** を開き、ファイルの先頭に次の行を追加します。

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

	このコードは、ボルトに対して、**int** ではなく **string** 値を受け取り、**CustomizedInteropJSONDeserialzer** を使用してデータをシリアル化解除する必要があることを指示します。

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
            }
        }

	これは、スパウトからの JSON データを Json.net を使用して解析し、**deviceId** および **deviceValue** フィールドを選択します。初期化時に、**deviceId** を使用して新しい **Device** オブジェクトが作成され、テーブルのパーティション キーが設定されます。値が **deviceValue** に設定されて、最後にエンティティがテーブルに挿入されます。

これで、Event Hub からデータを読み取り、読み取ったデータを、**events** という名前のテーブルにあるテーブル ストレージに格納するトポロジが完成しました。

##トポロジのデプロイ

1. **EventHubReader** ソリューションを開きます。**ソリューション エクスプローラー**で **EventHubReader** プロジェクトを右クリックし、**[HDInsight での Storm に送信]** を選択します。

	![submit to storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. **[トポロジの送信]** ダイアログの **[Storm クラスター]** で、該当するクラスターを選択します。**[追加の構成]** を展開し、**[Java ファイル パス]**、**[...]** の順に選択し、前の手順でダウンロードした **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** があるディレクトリを選択します。最後に、**[送信]** を選択します。

	![Image of submission dialog](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. トポロジが送信されると、**[Storm トポロジ ビューアー]** が表示されます。ダイアログの左側にある **EventHubReader** トポロジを選択し、トポロジの統計情報を表示します。Event Hub に書き込まれたイベントはまだないため、現時点では、何も発生していません。

	![example storage view](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. **EventHubWriter** ソリューションを開きます。**ソリューション エクスプローラー**で **EventHubWriter** プロジェクトを右クリックし、**[HDInsight での Storm に送信]** を選択します。

2. **[トポロジの送信]** ダイアログの **[Storm クラスター]** で、該当するクラスターを選択します。**[追加の構成]** を展開し、**[Java ファイル パス]**、**[...]** の順に選択し、前の手順でダウンロードした **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** があるディレクトリを選択します。最後に、**[送信]** を選択します。

5. トポロジが送信されたら、**[Storm トポロジ ビューアー]** でトポロジ一覧を最新情報に更新し、両方がクラスターで実行中であることを確認します。

6. 両方が実行中である場合は、**[サーバー エクスプローラー]** を選択し、**[Azure]**、**[ストレージ]** の順に展開し、最後に前の手順で作成したストレージ アカウントを展開します。そのストレージ アカウントで、**[テーブル]** を展開します。最後に、**events** テーブルをダブルクリックして開きます。**EventHubReader** トポロジに従ってテーブルに格納されたデータが表示されます。

	* **EventHubWriter** トポロジによってイベントが生成されています。生成されたイベントは Event Hub に書き込まれます。

	* **EventHubReader** が Event Hub からイベントを読み取り、**events** テーブルのテーブル ストレージに格納します。

##トポロジの停止

トポロジを停止するには、**[Storm トポロジ ビューアー]** で各トポロジを選択し、**[削除]** を選択します。

![image of killing a topology](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

##まとめ

このドキュメントでは、C# トポロジから Java Event Hub Spout および Bolt を使用して、Azure Event Hub のデータを操作する方法について説明しました。C# トポロジの作成の詳細については、次の記事を参照してください。

* [Visual Studio を使用して HDInsight で Apache Storm の C# トポロジを開発する](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [HDInsight Storm の例](https://github.com/hdinsight/hdinsight-storm-examples)








<!--HONumber=47-->
