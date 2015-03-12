<properties
   pageTitle="Visual Studio | Azure を使用して HDInsight で Apache Storm の C# トポロジを開発する"
   description="HDInsight Tools for Visual Studio を使用して、Visual Studio で簡単なワード カウントトポロジを作成することで、C# で Storm トポロジを作成する方法を説明します。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Visual Studio を使用して HDInsight で Apache Storm の C# トポロジを開発する

HDInsight tools for Visual Studio を使用して C# Storm トポロジを作成する方法を説明します。このドキュメントでは、Visual Studio で新しい Storm プロジェクトを作成し、ローカルでテストして、HDInsight クラスターで Apache Storm に展開する手順について説明します。

また、C# と Java コンポーネントの両方を使用するハイブリッド トポロジの作成方法についても説明します。

##前提条件

* 下記いずれかのバージョンの Visual Studio

	* Visual Studio 2012 <a href="http://www.microsoft.com/ja-jp/download/details.aspx?id=39305" target="_blank">(アップデート 4)</a>

	* Visual Studio 2013 <a href="http://www.microsoft.com/ja-jp/download/details.aspx?id=44921" target="_blank">(アップデート 4)</a> または <a href="http://go.microsoft.com/fwlink/?LinkId=517284" target="_blank">Visual Studio 2013 Community</a>

	* <a href="http://visualstudio.com/ja-jp/downloads/visual-studio-2015-ctp-vs" target="_blank">Visual Studio 2015 CTP6</a>

* Azure SDK 2.5.1 以降

* HDInsight Tools for Visual Studio -  <a href="../hdinsight-hadoop-visual-studio-tools-getting-started/" target="_blank">「Get started using HDInsight Tools for Visual Studio (HDInsight Tools for Visual Studio を使用する)」</a> で HDInsight tools for Visual Studio のインストールと構成方法をご確認ください。

* HDInsight クラスターの Apache Storm -  <a href="../hdinsight-storm-getting-started/" target="_blank">「HDInsight (Hadoop) での Storm の使用」</a> でクラスターの作成手順をご確認ください。

	> [AZURE.NOTE] 現在、HDInsight Tools for Visual Studio では HDInsight cluster バージョン 3.2 の Storm のみサポートしています。

##C# トポロジの作成

1. 最新バージョンの HDInsight Tools for Visual Studio をまだインストールしていない場合は、 <a href="../hdinsight-hadoop-visual-studio-tools-getting-started/" target="_blank">「Get started using HDInsight Tools for Visual Studio (HDInsight Tools for Visual Studio を使用する)」</a>をご覧ください。

2. Visual Studio を開いて、**[ファイル]**、**[新規]**、**[プロジェクト]** の順に選択します。

3. **[新しいプロジェクト]** ダイアログで、**[インストール済]**、**[テンプレート]** の順に展開して **[HDInsight]** を選択します。テンプレートの一覧から、**[Storm Application]** を選択します。ダイアログの下部に、アプリケーションの名前として「**WordCount**」と入力します。

	![image](./media/hdinsight-storm-develop-csharp-visual-studio/new-project.png)

4. プロジェクトの作成後、次ファイルが生成されます。

	* **Program.cs** - プロジェクトのトポロジを定義します。既定では、スパウトとボルト 1 つずつで構成される既定のトポロジが作成されます。

	* **Spout.cs** - ランダムな数字を出力するサンプル スパウト

	* **Bolt.cs** - スパウトが出力する数字をカウントするサンプル ボルト

	プロジェクトの一部として、最新の <a href="https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/" target="_blank">SCP.NET パッケージ</a> が Nuget からダウンロードされます。

次のセクションでは、このプロジェクトを基本的な WordCount アプリケーションに変更します。

###スパウトを実装する

5. **Spout.cs** を開きます - スパウトは、外部ソースからデータをトポロジに読み込む際に使用されます。スパウトの主なコンポーネントは次のとおりです。

	* **NextTuple** - スパウトで複数のタプルの出力が許可されている場合、Storm によって呼び出されます。

	* **Ack** - (トランザクションのトポロジのみ) - トポロジの他のコンポーネントが開始した、このスパウトから送信されたタプルの受信確認を処理します。タプルの受信確認によって、ダウンストリーム コンポーネントで処理が正常に完了したことがスパウトに通知されます。

	* **Fail** - (トランザクションのトポロジのみ) - トポロジの他のコンポーネントの処理に失敗したタプルを処理します。これにより、再処理用のタプルを再出力できるようになります。

6. Spout クラスの内容を次のように置き換えます。これにより、トポロジにセンテンスをランダムに出力するスパウトが作成されます。

		private Context ctx;
        private Random r = new Random();
        string[] sentences = new string[] {
            "the cow jumped over the moon",
            "an apple a day keeps the doctor away",
            "four score and seven years ago",
            "snow white and the seven dwarfs",
            "i am at two with nature"
        };

        public Spout(Context ctx)
        {
			// Set the instance context
            this.ctx = ctx;

            Context.Logger.Info("Generator constructor called");

            // Declare Output schema
            Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
            // The schema for the default output stream is
            // a tuple that contains a string field
            outputSchema.Add("default", new List<Type>() { typeof(string) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        }

		// Get an instance of the spout
        public static Spout Get(Context ctx, Dictionary<string, Object> parms)
        {
            return new Spout(ctx);
        }

        public void NextTuple(Dictionary<string, Object> parms)
        {
            Context.Logger.Info("NextTuple enter");
            // The sentence to be emitted
            string sentence;

            // Get a random sentence
            sentence = sentences[r.Next(0, sentences.Length - 1)];
            Context.Logger.Info("Emit: {0}", sentence);
            // Emit it
            this.ctx.Emit(new Values(sentence));

            Context.Logger.Info("NextTuple exit");
        }

        public void Ack(long seqId, Dictionary<string, Object> parms)
        {
			// Only used for transactional topologies
        }

        public void Fail(long seqId, Dictionary<string, Object> parms)
        {
			// Only used for transactional topologies
        }

	コメントに目を通して、このコードが行う内容をご確認ください。

###ボルトを実装する

1. プロジェクトから既存の **Bolt.cs** を削除します。

2. **[ソリューション エクスプローラー]** で、プロジェクトを右クリックして**[追加]**、**[新しいアイテム]** を選択します。一覧から **[Storm Bolt]** を選択して、名前として「**Splitter.cs**」と入力します。この操作を繰り返して「**Counter.cs**」という名前の 2 つ目のボルトを作成します。

	* **Splitter.cs** - センテンスを個別の単語に分けて、新しい単語のストリームに出力するボルトを実装します。

	* **Counter.cs** - 各単語をカウントし、新しい単語のストリームに出力して各単語をカウントするボルトを実装します。

	> [AZURE.NOTE] これらのボルトはストリームに読み書きするだけですが、ボルトを使用してデータベースやサービスとやりとりすることもできます。

6. **Splitter.cs** を開きます。既定では、**Execute** のメソッドのみがあります。これは、ボルトが処理のタプルを受信したときに呼び出されます。ここで、受信したタプルを読み取って処理し、送信タプルを出力できます。

4. **Splitter** クラスの内容を次のように置き換えます。

		private Context ctx;

        // Constructor
        public Splitter(Context ctx)
        {
            Context.Logger.Info("Splitter constructor called");
            this.ctx = ctx;

            // Declare Input and Output schemas
            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            // Input contains a tuple with a string field (the sentence)
            inputSchema.Add("default", new List<Type>() { typeof(string) });
            Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
            // Outbound contains a tuple with a string field (the word)
            outputSchema.Add("default", new List<Type>() { typeof(string) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
        }

        // Get a new instance of the bolt
        public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
        {
            return new Splitter(ctx);
        }

        // Called when a new tuple is available
        public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Execute enter");

            // Get the sentance from the tuple
            string sentence = tuple.GetString(0);
            // Split at space characters
            foreach (string word in sentence.Split(' '))
            {
                Context.Logger.Info("Emit: {0}", word);
                //Emit each word
                this.ctx.Emit(new Values(word));
            }

            Context.Logger.Info("Execute exit");
        }

	コードに目を通して、このコードが行う内容をご確認ください。

6. **Counter.cs** を開いて、クラスの内容を次のように置き換えます。

		private Context ctx;

        // Dictionary for holding words and counts
        private Dictionary<string, int> counts = new Dictionary<string, int>();

        // Constructor
        public Counter(Context ctx)
        {
            Context.Logger.Info("Counter constructor called");
            // Set instance context
            this.ctx = ctx;

            // Declare Input and Output schemas
            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            // A tuple containing a string field - the word
            inputSchema.Add("default", new List<Type>() { typeof(string) });

            Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
            // A tuple containing a string and integer field - the word and the word count
            outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
        }

        // Get a new instance
        public static Counter Get(Context ctx, Dictionary<string, Object> parms)
        {
            return new Counter(ctx);
        }

        // Called when a new tuple is available
        public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Execute enter");

            // Get the word from the tuple
            string word = tuple.GetString(0);
            // Do we already have an entry for the word in the dictionary?
            // If no, create one with a count of 0
            int count = counts.ContainsKey(word) ? counts[word] : 0;
            // Increment the count
            count++;
            // Update the count in the dictionary
            counts[word] = count;

            Context.Logger.Info("Emit: {0}, count: {1}", word, count);
            // Emit the word and count information
            this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));

            Context.Logger.Info("Execute exit");
        }

	コードに目を通して、このコードが行う内容をご確認ください。

###トポロジを定義する

スパウトとボルトはコンポーネント間のデータ フローを定義するグラフに配置されます。このトポロジのグラフは次のようになります。

![image of how components are arranged](./media/hdinsight-storm-develop-csharp-visual-studio/wordcount-topology.png)

センテンスはスパウトから出力され、Splitter ボルトのインスタンスに配布されます。Splitter ボルトがセンテンスを単語に分け、Counter ボルトに配布します。

ワード カウントは Counter インスタンスにローカルに保持されるため、特定の 1 つの単語が同じ Counter ボルト インスタンスに届くようにして、その単語を追跡するインスタンスが複数発生しないようにする必要があります。ただし、Splitter ボルトの場合はどのボルトがどのセンテンスを受信しても問題ないため、インスタンス全体の負荷の分散のみを考慮します。

**Program.cs** を開きます。ここで重要なメソッドは **ITopologyBuilder** です。これは、Storm に送信されるトポロジの定義に使用されます。  **ITopologyBuilder** を次のように置き換えて前述のトポロジを実装します。

		// Create a new topology named 'WordCount'
        TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount");

        // Add the spout to the topology.
        // Name the component 'sentences'
        // Name the field that is emitted as 'sentence'
        topologyBuilder.SetSpout(
            "sentences",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
            },
            1);
        // Add the splitter bolt to the topology.
        // Name the component 'splitter'
        // Name the field that is emitted 'word'
        // Use suffleGrouping to distribute incoming tuples
        //   from the 'sentences' spout across instances
        //   of the splitter
        topologyBuilder.SetBolt(
            "splitter",
            Splitter.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
            },
            1).shuffleGrouping("sentences");

        // Add the counter bolt to the topology.
        // Name the component 'counter'
        // Name the fields that are emitted 'word' and 'count'
        // Use fieldsGrouping to ensure that tuples are routed
        //   to counter instances based on the contents of field
        //   position 0 (the word). This could also have been 
        //   List<string>(){"word"}.
        //   This ensures that the word 'jumped', for example, will always
        //   go to the same instance
        topologyBuilder.SetBolt(
            "counter",
            Counter.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
            },
            1).fieldsGrouping("splitter", new List<int>() { 0 });

        // Add topology config
        topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
        {
            {"topology.kryo.register","[\"[B\"]"}
        });

        return topologyBuilder;

コードに目を通して、このコードが行う内容をご確認ください。

##トポロジを送信する

1. **[ソリューション エクスプローラー]** で、プロジェクトを右クリックして **[Submit to Storm on HDInsight (HDInsight の Storm に送信)]** を選択します。

	> [AZURE.NOTE] メッセージが表示されたら、Azure サブスクリプションのログイン資格情報を入力します。2 つ以上のサブスクリプションをお持ちの場合は、HDInsight クラスター の Storm があるサブスクリプションにログインします。

2. **[Storm クラスター]** ドロップダウンから HDInsight クラスターの Storm を選択して、**[送信]** を選択します。送信が成功したかどうかは、**[出力]** ウィンドウを使用して確認できます。

3. トポロジが正常に送信されたら、クラスターの **[Storm トポロジ]** が表示されます。一覧から **[WordCount]** トポロジを選択して、実行中のトポロジに関する情報を表示します。

	> [AZURE.NOTE] また、**Storm トポロジ**は、**[サーバー エクスプローラー]** で **[Azure]**、**[HDInsight]** の順に展開して、HDInsight クラスターの Storm を右クリックして **[View Storm Topologies (Storm トポロジの表示)]** を選択して表示できます。

	スパウトやボルトのリンクを使用してこれらのコンポーネントに関する情報を表示します。各アイテムを選択すると新しいウィンドウが開きます。

4. **[トポロジの概要]** ビューで、**[強制終了]** を選択してトポロジを停止します。

	> [AZURE.NOTE] Storm トポロジは強制終了されるまで、またはクラスターが削除されるまで実行し続けます。

##トランザクションのトポロジ

前のトポロジはトランザクションではなく、トポロジ内のコンポーネントはトポロジのコンポーネントによって処理が失敗した場合のリプレイ メッセージに機能を実装することはありません。トランザクションのトポロジの例として、新しいプロジェクトを作成してプロジェクト種類に **[Storm サンプル]** を選択します。

トランザクションのトポロジは次の内容を実装してデータのリプレイをサポートします。

* **Metadata caching** - スパウトは出力されたデータに関するメタデータを保存し、エラーが生じた場合にデータを再度取得して出力できるようにする必要があります。サンプルが出力するデータは少量であるため、各タプルの生データはリプレイのディクショナリに保存されます。

* **Ack** - トポロジ内の各ボルトは  `this.ctx.Ack(tuple)` を呼び出してタプルが正常に処理されたことを確認できます。すべてのボルトでタプルを確認したら、スパウトの  `Ack` メソッドが呼び出されます。これで、データが完全に処理されたため、スパウトはリプレイのキャッシュされたデータを削除できます。

* **Fail** - 各ボルトは  `this.ctx.Fail(tuple)` を呼び出してタプルの処理が失敗したことを示すことができます。エラーがスパウトの  `Fail` メソッドに伝達され、そこでキャッシュされたメタデータを使用してタプルをリプレイされます。

* **Sequence ID** - タプルの出力時、シーケンス ID を指定できます。これは、リプレイ (Ack と Fail) 処理するタプルを示す値である必要があります。たとえば、**Storm サンプル** プロジェクトではデータの出力時に次の値を使用します。

		this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

	これで、センテンスを含む新しいタプルが、lastSeqId に含まれるシーケンス ID と共に既定のストリームに出力されます。この例では、lastSeqId は出力されるタプルごとに増加します。

**Storm サンプル** プロジェクトのように、コンポーネントがトランザクションであるかどうかは、構成に基づいて実行時に設定できます。

##ハイブリッド トポロジ

また、HDInsight tools for Visual Studio は、C# のコンポーネントと Java のコンポーネントが混在するハイブリッド トポロジの作成に使用できます。

ハイブリッド トポロジの例として、新しいプロジェクトを作成して **[Storm ハイブリッド サンプル]** を選択します。これで、次を示す複数のトポロジを含む、完全にコメントされたサンプルが作成されます。

* **Java スパウト**と **C# ボルト** - **HybridTopology_javaSpout_csharpBolt** で定義

	* **HybridTopologyTx_javaSpout_csharpBolt** で定義されたトランザクション バージョン

* **C# スパウト**と **Java ボルト** - **HybridTopology_csharpSpout_javaBolt** で定義

	* **HybridTopologyTx_csharpSpout_javaBolt** で定義されたトランザクション バージョン

		> [AZURE.NOTE] このバージョンは、closure コードをテキスト ファイルから Java コンポーネントとして使用する方法も示しています。

プロジェクトの送信時に使用されるトポロジを切り替えるには、単純にクラスターの送信前に `[Active(true)]` のステートメントを使用するトポロジに移動します。

> [AZURE.NOTE] 必要なすべての Java ファイルは、このプロジェクトの一部として **JavaDependency** フォルダーに提供されています。

ハイブリッド トポロジの作成と送信時には、次のものが使用されます。

* **JavaComponentConstructor** は、スパウトやボルトの Java クラスの新しいインスタンスの作成に使用する必要があります。

* **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** は、Java オブジェクトから JSON に、Java コンポーネントをシリアル化する際に使用します。

* トポロジをサーバーに**送信するとき**、**[追加の構成]** オプションを使用して **[Java ファイルのパス]** を指定する必要があります。ここで指定するパスは、Java クラスを含む jar ファイルのあるディレクトリである必要があります。

##トラブルシューティング

###トポロジをローカルでテストする

トポロジのクラスターへの展開は簡単ですが、状況によっては、トポロジをローカルでテストする必要が生じる場合があります。次の手順を使用して、お使いの開発環境のローカルで本書のサンプル トポロジを実行してテストします。

1. **[ソリューション エクスプローラー]** で、プロジェクトを右クリックして **[プロパティ]** を選択します。プロジェクトのプロパティで、**[出力の種類]** を **[コンソール アプリケーション]** に変更します。

	![output type](./media/hdinsight-storm-develop-csharp-visual-studio/outputtype.png)

	> [AZURE.NOTE] トポロジをクラスターに展開する前に、必ず **[出力の種類]** を **[クラス ライブラリ]** に戻すようにしてください。

2. **[ソリューション エクスプローラー]** で、プロジェクトを右クリックして **[追加]**、**[新しいアイテム]** の順に選択します。**[クラス]** を選択して、クラス名として「**LocalTest.cs**」と入力します。最後に **[追加]** を選択します。

3. **LocalTest.cs** を開いて、上部に次の  `using` ステートメントを追加します。

		using Microsoft.SCP;

3. **LocalTest** クラスの内容として次を使用します。

		// Drives the topology components
		public void RunTestCase()
		{
		    // An empty dictionary for use when creating components
		    Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();
		
		    #region Test the spout
		    {
		        Console.WriteLine("Starting spout");
		        // LocalContext is a local-mode context that can be used to initialize
		        // components in the development environment.
		        LocalContext spoutCtx = LocalContext.Get();
		        // Get a new instance of the spout, using the local context
		        Spout sentences = Spout.Get(spoutCtx, emptyDictionary);
		
		        // Emit 10 tuples
		        for (int i = 0; i < 10; i++)
		        {
		            sentences.NextTuple(emptyDictionary);
		        }
		        // Use LocalContext to persist the data stream to file
		        spoutCtx.WriteMsgQueueToFile("sentences.txt");
		        Console.WriteLine("Spout finished");
		    }
		    #endregion
		
		    #region Test the splitter bolt
		    {
		        Console.WriteLine("Starting splitter bolt");
		        // LocalContext is a local-mode context that can be used to initialize
		        // components in the development environment.
		        LocalContext splitterCtx = LocalContext.Get();
		        // Get a new instance of the bolt
		        Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);
		
		        // Set the data stream to the data created by the spout
		        splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
		        // Get a batch of tuples from the stream
		        List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
		        // Process each tuple in the batch
		        foreach (SCPTuple tuple in batch)
		        {
		            splitter.Execute(tuple);
		        }
		        // Use LocalContext to persist the data stream to file
		        splitterCtx.WriteMsgQueueToFile("splitter.txt");
		        Console.WriteLine("Splitter bolt finished");
		    }
		    #endregion
		
		    #region Test the counter bolt
		    {
		        Console.WriteLine("Starting counter bolt");
		        // LocalContext is a local-mode context that can be used to initialize
		        // components in the development environment.
		        LocalContext counterCtx = LocalContext.Get();
		        // Get a new instance of the bolt
		        Counter counter = Counter.Get(counterCtx, emptyDictionary);
		
		        // Set the data stream to the data created by splitter bolt
		        counterCtx.ReadFromFileToMsgQueue("splitter.txt");
		        // Get a batch of tuples from the stream
		        List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
		        // Process each tuple in the batch
		        foreach (SCPTuple tuple in batch)
		        {
		            counter.Execute(tuple);
		        }
		        // Use LocalContext to persist the data stream to file
		        counterCtx.WriteMsgQueueToFile("counter.txt");
		        Console.WriteLine("Counter bolt finished");
		    }
		    #endregion
		}

	コード コメントに目を通してください。このコードでは、**LocalContext** を使用して開発環境でコンポーネントを実行し、ローカル ドライブ上でテキスト ファイルにコンポーネント間のデータ ストリームを保存します。

2. **Program.cs** を開いて、**[メイン]** メソッドに次を追加します。

        Console.WriteLine("Starting tests");
        System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
        // Initialize the runtime
        SCPRuntime.Initialize();

        //If we are not running under the local context, throw an error
        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
        {
            throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
        }
		// Create test instance
        LocalTest tests = new LocalTest();
		// Run tests
        tests.RunTestCase();
        Console.WriteLine("Tests finished");
        Console.ReadKey();

3. 変更を保存して、**F5** キーまたは **[デバッグ]**、[**デバッグの開始**] を使用してプロジェクトを起動します。コンソール ウィンドウが表示され、テストの進行に合わせてステータスを記録します。**[テストの完了]** と表示されたら、いずれかのキーを押してウィンドウを閉じます。

4. **エクスプローラー** を使用して、プロジェクトが含まれているディレクトリに移動します。例: **C:\Users\&lt;your user name>\Documents\Visual Studio 2013\Projects\WordCount\WordCount**。このディレクトリで、**Bin**、**デバッグ** の順に開きます。テストの実行時に生成された、sentences.txt、counter.txt、splitter.txt というテキスト ファイルが表示されます。それぞれのテキスト ファイルを開いてデータを確認します。

	> [AZURE.NOTE] これらのファイルでは、文字列データは 10 進数の値の配列として保存されます。たとえば、**splitter.txt** ファイルの [[97,103,111]] は  'and' の単語を示します。

基本的なワード カウント アプリケーションをローカルでテストすることはとても簡単ですが、外部データ ソースとやりとりしたり、複雑なデータ分析を実行したりする複合的なトポロジを扱う場合には非常に役立ちます。そのようなプロジェクトを扱う際は、コンポーネントのコードにブレークポイントとステップを設定して問題を分離する必要が生じる場合もあります。

> [AZURE.NOTE] HDInsight クラスターの Storm に展開する前に、必ず **[プロジェクトの種類]** を **[クラス ライブラリ]** に戻すようにしてください。

###ログ情報

トポロジ コンポーネントからの情報は、 `Context.Logger` を使用して簡単に記録できます。たとえば、次のようにすると情報ログ エントリが作成されます。

	Context.Logger.Info("Component started");

記録された情報は、**[サーバー エクスプローラー]** の **[Hadoop Service Log (Hadoop サービス ログ)]** で閲覧できます。HDInsight クラスターの Storm のエントリを展開して、**[Hadoop Service Log (Hadoop サービス ログ)]** を展開します。最後に、閲覧するログ ファイルを選択します。

> [AZURE.NOTE] ログは、クラスターで使用する Azure ストレージ アカウントに保存されます。Visual Studio でログインしたサブスクリプションと異なる場合、この情報を閲覧するにはストレージ アカウントのあるサブスクリプションにログインする必要があります。

###エラー情報の表示

トポロジの実行中に発生したエラーを表示するには、次の手順に従います。

1. **[サーバー エクスプローラー]** で、HDInsight クラスターの Storm を右クリックして **[View Storm topologies (Storm トポロジの表示)]** を選択します。

2. **スパウト**と**ボルト**については、**[最新のエラー]** の列に最後に発生したエラーの情報が表示されます。

2. エラーの一覧にあるコンポーネントの **[Spout Id]** または **[Bolt Id]** を選択します。詳細ページが表示され、詳細なエラー情報がページ下部の **[エラー]** セクションに表示されます。

3. さらに詳細な情報を取得するには、**[Executors]** セクションから **[ポート]** を選択すると、過去数分間の Storm ワーカー ログが表示されます。


##次のステップ

ここまでで、HDInsight tools for Visual Studio から Storm トポロジを開発、展開する方法について学習してきました。次のページでは、他の方法で HDInsight を使用する方法を学習できます。

**HDInsight での Apache Storm**

* [Deploy and monitor topologies with Apache Storm on HDInsight (HDInsight の Storm を使用したトポロジの展開と監視)](../hdinsight-storm-deploy-monitor-topology/)

* [Develop Java-based topologies for Apache Storm on HDInsight (HDInsight の Apache Storm での Java ベース トポロジの開発)](../hdinsight-storm-develop-java-topology/)

* [Trending hashtags for Twitter with Apache Storm on HDInsight (HDInsight の Apache Storm を使用した Twitter のトレンディング ハッシュタグ)](../hdinsight-storm-twitter-trending/)

**HDInsight での Apache Hadoop**

* [HDInsight での Hive と Hadoop の使用](../hdinsight-use-hive/)

* [HDInsight の Hadoop での Pig の使用](../hdinsight-use-pig/)

* [HDInsight での Hadoop MapReduce の使用](../hdinsight-use-mapreduce/)

**HDInsight での Apache HBase **

* [HDInsight の Hadoop で Hive を使用して HBase クラスターを設定して照会する](../hdinsight-hbase-get-started/)

<!--HONumber=45--> 
