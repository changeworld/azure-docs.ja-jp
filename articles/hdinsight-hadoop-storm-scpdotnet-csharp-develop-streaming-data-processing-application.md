<properties 
	pageTitle="Storm で SCP.NET を使用したストリーミング データ処理アプリケーションの開発 | Azure" 
	description="HDInsight の Storm で SCP.NET および C# を使用してストリーミング データ処理アプリケーションを開発する方法について説明します。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="weedqian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2014" 
	ms.author="qixia"/>

#HDInsight の Storm で Stream Computing Platform と C# を使用したストリーミング データ処理アプリケーションの開発

Stream Computing Platform (SCP) は、.NET を使用する、リアルタイムで信頼性が高く、分散型で整合性のとれた、高パフォーマンスのデータ処理アプリケーションを構築するためのプラットフォームです。HDInsight で使用できる [Apache Storm ](http://storm.incubator.apache.org/) (オープン ソースのリアルタイム ストリーミング処理システム) 上に構築されます。

この記事では、次のことについて説明します。

* SCP の概要、および Storm での使用方法

* SCP ソリューションの作成方法

* SCP ソリューションのテスト方法

* HDInsight 上の Storm クラスターへの SCP ソリューションのデプロイ方法

##前提条件

* Azure サブスクリプション

* HDInsight でのストーム クラスター

* Visual Studio 2010 または 2013

##目次

* [SCP および Storm](#scpandstorm)

##<a id="scpandstorm"></a>SCP および Storm

Apache Storm は分散型のコンピューター システムで、Hadoop クラスターで実行されます。また、リアルタイムでデータ処理を実行できます。Storm は Java Virtual Machine (JVM) で実行されますが、ソリューション (**トポロジ**ともいいます) をさまざまなプログラミング言語で実装できるように設計されています。複数の言語で記述されたコンポーネントを組み合わせてトポロジを作成することも可能です。

SCP にはライブラリもあるため、.NET を使用して簡単に Storm ソリューションを作成できます。HDInsight のストーム クラスターには、作成した SCP ソリューションを実行するために必要なサーバー側コンポーネントが含まれます。

HDInsight Storm の詳細については、[「HDInsight Storm の概要」](http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-storm-overview/)を参照してください。

###SCP ソリューションの設計

SCP で提供されるインターフェイスにより、次のような Storm コンポーネントを作成できます。

* スパウト - データをソース (ファイル、データベース、API など) から使用し、タプルの 1 つ以上のストリーム (要素の一覧) を出力します。
* ボルト -  1 つ以上のストリームを使用し、必要に応じて 1 つ以上のストリームを出力します。
* トポロジ - スパウトとボルトのデータ フロー方法、スパウトとボルトの並列処理、および構成情報を定義します。

> [AZURE.NOTE] スパウトとボルトは一般的な処理も実装します。たとえば、スパウトが受信したメッセージを複数のタプルに分断する場合もあれば、タプルを 1 つだけ出力し、必要な値はボルトが抽出します。同様に、データ ストアにデータを書き込む必要がある場合もボルトに実装します。

ソリューションの設計には、次の手順が含まれます。

* 使用するデータ ソースの内容。このデータ ソースを実装するスパウトが必要です。
* 発生する処理内容。この処理は、スパウトとボルトのどちらか、または両方に実装します。
* 処理が複数のボルトに分かれる場合の、ボルト間のデータ フロー。このデータ フローはトポロジから記述する必要があります。
* HDInsight クラスターのノード間における処理の配布方法。この配布方法はトポロジから記述する必要があります。

##SCP SDK のインストール

SCP SDK は、HDInsight Storm クラスターで提供されています。[HDInsight Storm クラスターの作成](http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-storm-getting-started/)後、次の手順に従い、SDK をローカル開発環境にダウンロードします。

1. [Azure 管理ポータル](https://manage.windowsazure.com)にサインインします。

2. 左側ウィンドウの **[HDINSIGHT]** をクリックします。展開した HDInsight クラスターが一覧表示されます。

3. 接続する HDInsight クラスターをクリックします。

4. ページの上部にある **[構成]** をクリックします。

5. ページの下部にある **[リモートを有効にする]** をクリックします。

6. リモート デスクトップの構成ウィザードで、リモート デスクトップの**ユーザー名**と**パスワード**を入力します。**[有効期限]** ボックスに有効期限の日付を入力し、チェック アイコンをクリックします。

7. リモート デスクトップが有効になったら、ページの下部にある **[接続]** をクリックし、画面の指示に従います。

8. リモート デスクトップからクラスターに接続されたら、**エクスプローラー**を開き、アドレス バーに「**%storm_home%\examples**」と入力します。

9. **SDK** フォルダーを右クリックしてから **[コピー]** を選択します。

10. ローカル開発環境で**エクスプローラー**を開き、SDK を保存する場所を参照します。右クリックして **[貼り付け]** を選択します。

##SCP ソリューションの作成

1. Visual Studio で、**コンソール アプリケーション**用の新しいプロジェクトを作成します。このソリューションに **WordCount** という名前を付けます。

2. **ソリューション エクスプローラー**で **[参照設定]** を右クリックし、**[参照の追加]** を選択します。

3. **[参照マネージャー]** の下にある **[参照]** ボタンを選択し、先ほどダウンロードした SDK のフォルダーを参照します。**Microsoft.SCPNet.dll** および **Microsoft.SCPLogger.dll** を選択し、**[追加]** をクリックします。**[OK]** をクリックして [参照マネージャー] ウィンドウを閉じます。

###スパウトの作成

1. **ソリューション エクスプローラー**で **[WordCount]** を右クリックし、**[追加]、[新しいアイテム]** の順に選択します。**[クラス]** を選択し、「**WordSpout.cs**」という名前を入力します。最後に **[追加]** をクリックします。

2. **WordSpout.cs** ファイルを開き、既存のコードを次のコードに置き換えます。コメントを読んでこのコードのしくみを理解しておいてください。

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;
        using System.Threading;

        namespace WordCount
        {
            class WordSpout : ISCPSpout
            {
                //Context
                private Context ctx;

                private Random rand = new Random();
                //The sentences to emit
                string[] sentences = new string[] {
                                                  "the cow jumped over the moon",
                                                  "an apple a day keeps the doctor away",
                                                  "four score and seven years ago",
                                                  "snow white and the seven dwarfs",
                                                  "i am at two with nature"};

                //Constructor
                public WordSpout(Context ctx)
                {
                    //Log that we are starting
                    Context.Logger.Info("WordSpout constructor called");
                    //Store the context that was passed
                    this.ctx = ctx;

                    //Define the schema for the emitted tuples
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //In this case, just a string tuple
                    outputSchema.Add("default", new List<Type>() { typeof(string) });
                    //Declare the schema for the stream
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
                }

                //Return a new instance of the spout
                public static WordSpout Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new WordSpout(ctx);
                }

                //Emit the next tuple
                //NOTE: When using data from an external data source
                //such as Service Bus, Event Hub, Twitter, etc.,
                //you would read and emit it in NextTuple
                public void NextTuple(Dictionary<string, object> parms)
                {
                    Context.Logger.Info("NextTuple enter");

                    //Get a random sentence
                    string sentence = sentences[rand.Next(0, sentences.Length - 1)];
                    Context.Logger.Info("Emit: {0}", sentence);
                    //Emit the sentence
                    this.ctx.Emit(new Values(sentence));

                    Context.Logger.Info("NextTuple exit");
                }

                //Ack's are not implemented
                public void Ack(long seqId, Dictionary<string, object> parms)
                {
                    throw new NotImplementedException();
                }

                //Ack's are not implemented, so
                //fail should never be called
                public void Fail(long seqId, Dictionary<string, object> parms)
                {
                    throw new NotImplementedException();
                }
            }
        }

###テストの作成

1. **ソリューション エクスプローラー**で **[WordCount]** を右クリックし、**[追加]、[新しいアイテム]** の順に選択します。**[クラス]** を選択し、「**LocalTest.cs**」という名前を入力します。最後に **[追加]** をクリックします。

2. **LocalTest.cs** ファイルを開き、既存のコードを次のコードに置き換えます。スパウトのテストに使用するコードは、ボルトのテストに使用するコードとよく似ています。

        using Microsoft.SCP;
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;

        namespace WordCount
        {
            class LocalTest
            {
                //Run tests
                public void RunTestCase()
                {
                    Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();
                    //Spout tests
                    {
                        //Get local context
                        LocalContext spoutCtx = LocalContext.Get();
                        //Get an instance of the spout
                        WordSpout spout = WordSpout.Get(spoutCtx, emptyDictionary);
                        //Call NextTuple to emit data
                        for (int i = 0; i < 10; i++)
                        {
                            spout.NextTuple(emptyDictionary);
                        }
                        //Store the stream for the next component
                        spoutCtx.WriteMsgQueueToFile("spout.txt");
                    }
                }
            }
        }

3. **Program.cs** を開き、既存のコードを次のコードに置き換えます。

        using Microsoft.SCP;
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;

        namespace WordCount
        {
            class Program
            {
                static void Main(string[] args)
                {
                    if (args.Count() > 0)
                    {
                        //Code to run on HDInsight cluster will go here
                    }
                    else
                    {
                        //Set log prefix information for the component being tested
                        System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
                        //Initialize the runtime
                        SCPRuntime.Initialize();

                        //If we are not running under the local context, throw an error
                        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
                        {
                            throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
                        }
                        //Create an instance of LocalTest
                        LocalTest localTest = new LocalTest();
                        //Run the tests
                        localTest.RunTestCase();
                    }
                }
            }
        }

3. アプリケーションを実行します。作業が完了したら、Visual Studio プロジェクトの下にある **WordCount\bin\debug** ディレクトリを参照します。このディレクトリに **spout.txt** というファイルがあります。このファイルには、このテスト中にスパウトから出力されたデータが含まれます。このデータの内容は、次のようなテキストになります。

        {"__isset":{"streamId":true,"tupleId":true,"evt":true,"data":true},"StreamId":"default","TupleId":"","Evt":1000,"Data":[[97,110,32,97,112,112,108,101,32,97,32,100,97,121,32,107,101,101,112,115,32,116,104,101,32,100,111,99,116,111,114,32,97,119,97,121]]}
        {"__isset":{"streamId":true,"tupleId":true,"evt":true,"data":true},"StreamId":"default","TupleId":"","Evt":1000,"Data":[[116,104,101,32,99,111,119,32,106,117,109,112,101,100,32,111,118,101,114,32,116,104,101,32,109,111,111,110]]}

    > [AZURE.NOTE] 上記のテキスト中、"Data" はスパウトにより出力される文字列ですが、バイト配列として保存されます。たとえば、`[[97,110,32,97,112,112,108,101,32,97,32,100,97,121,32,107,101,101,112,115,32,116,104,101,32,100,111,99,116,111,114,32,97,119,97,121]]` は "an apple a day keeps the doctor away" です。

###ボルトの作成

1. **ソリューション エクスプローラー**で **[WordCount]** を右クリックし、**[追加]、[新しいアイテム]** の順に選択します。**[クラス]** を選択し、「**SplitterBolt.cs**」という名前を入力します。最後に **[追加]** をクリックします。この手順を繰り返して、**CounterBolt.cs** という名前のクラスを追加します。

2. **SplitterBolt.cs** ファイルを開き、既存のコードを次のコードに置き換えます。

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;
        using System.Threading;

        namespace WordCount
        {
            class SplitterBolt : ISCPBolt
            {
                //Context
                private Context ctx;

                //Constructor
                public SplitterBolt(Context ctx)
                {
                    Context.Logger.Info("Splitter constructor called");
                    //Set context
                    this.ctx = ctx;
                    //Define the schema for the incoming tuples from spout
                    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
                    //In this case, just a string tuple
                    inputSchema.Add("default", new List<Type>() {typeof (string)});
                    //Define the schema for tuples to be emitted from this bolt
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //We are also only emitting a string tuple
                    outputSchema.Add("default", new List<Type>() {typeof (string)});
                    //Declare both incoming and outbound schemas
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
                }

                //Get a new instance
                public static SplitterBolt Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new SplitterBolt(ctx);
                }

                //Process a tuple from the stream
                public void Execute(SCPTuple tuple)
                {
                    Context.Logger.Info("Execute enter");
                    //Get the incomin tuple value
                    string sentence = tuple.GetString(0);
                    //Split it
                    foreach (string word in sentence.Split(' '))
                    {
                        //Emit each word to the outbound stream
                        Context.Logger.Info("Emit: {0}", word);
                        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word));
                    }
                    Context.Logger.Info("Execute exit");
                }
            }
        }

4. **CounterBolt.cs** ファイルを開き、既存のコードを次のコードに置き換えます。

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;

        namespace WordCount
        {
            class CounterBolt : ISCPBolt
            {
                //Context
                private Context ctx;

                //Store each word and a count of how many times it has
                //been emitted by the splitter
                private Dictionary<string, int> counts = new Dictionary<string, int>();

                //Constructor
                public CounterBolt(Context ctx)
                {
                    Context.Logger.Info("Counter constructor called");

                    //Set context
                    this.ctx = ctx;
                    //Define the schema for the incoming tuples from spout
                    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
                    //Just a string, which will contain a word from the splitter
                    inputSchema.Add("default", new List<Type>() { typeof(string) });
                    //Define the schema for tuples to be emitted from this bolt
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //In this case, a string (containing a word) and an int (containing the count)
                    outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
                }

                //Get an instance
                public static CounterBolt Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new CounterBolt(ctx);
                }

                //Process a tuple from the stream
                public void Execute(SCPTuple tuple)
                {
                    Context.Logger.Info("Execute enter");
                    //Get the word that was emitted from the splitter
                    string word = tuple.GetString(0);
                    //Have we seen this word before?
                    int count = counts.ContainsKey(word) ? counts[word] : 0;
                    //Increment and store the count for this word
                    count++;
                    counts[word] = count;

                    Context.Logger.Info("Emit: {0}, count: {1}", word, count);
                    //Emit the word and the count
                    this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));

                    Context.Logger.Info("Execute exit");
                }
            }
        }

    > [AZURE.NOTE] テストに役立つストリームがボルトから出力されます。実際のソリューションでは、処理の最後にデータをデータベース、キュー、またはその他の固定ストアに保存します。

3. **LocalTest.cs** を開き、前のスパウトのテスト ブロックに続いて、SplitterBolt および CounterBolt 向けに次のテストを追加します。

        //SplitterBolt tests
        {
            LocalContext splitterCtx = LocalContext.Get();
            SplitterBolt splitter = SplitterBolt.Get(splitterCtx, emptyDictionary);
            //Read from the 'stream' emitted by the spout
            splitterCtx.ReadFromFileToMsgQueue("spout.txt");
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            //Store the stream for the next component
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
        }
        //CounterBolt tests
        {
            LocalContext counterCtx = LocalContext.Get();
            CounterBolt counter = CounterBolt.Get(counterCtx, emptyDictionary);
            //Read from the 'stream' emitted by the splitter
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            //Store the stream for the next component
            counterCtx.WriteMsgQueueToFile("counter.txt");
        }

4. ソリューションを実行する作業を終了すると、次のファイルが **WordCount\bin\debug** ディレクトリに追加されています。

    * **spout.txt** - WordSpout により出力されたデータ。
    * **splitter.txt** - SplitterBolt により出力されたデータ。
    * **counter.txt** - CounterBolt により出力されたデータ。

###実行するコードをクラスターに追加する

1. **Program.cs** を開き、 `//Code to run on HDInsight cluster will go here` という行を次の行に置き換え、プロジェクトを再構築します。

        //The component to run
        string compName = args[0];
        //Run the component
        if ("wordspout".Equals(compName))
        {
            //Set the prefix for logging
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Spout");
            //Initialize the runtime
            SCPRuntime.Initialize();
            //Run the plugin (WordSpout)
            SCPRuntime.LaunchPlugin(new newSCPPlugin(WordSpout.Get));
        }
        else if ("splitterbolt".Equals(compName))
        {
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Splitter");
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(SplitterBolt.Get));
        }
        else if ("counterbolt".Equals(compName))
        {
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Counter");
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(CounterBolt.Get));
        }
        else
        {
            throw new Exception(string.Format("unexpected compName: {0}", compName));
        }

4. **WordCount.spec** という名前のファイルを新規作成し、次のデータをコンテンツに使用します。このデータにより、コンポーネント、クラスター内のノード間で作成するインスタンス数、ノード間のデータ フローなどのトポロジが定義されます。このトポロジは、[トポロジ仕様言語]を使用して記述されます(#spec)。

        {
          :name "WordCount"
          :topology
            (nontx-topolopy
              "WordCount"

              {
                "spout"

                (spout-spec
                  (scp-spout
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["wordspout"]
                      "output.schema" {"default" ["sentence"]}
                    })

                  :p 1)
              }

              {
                "splitter"

                (bolt-spec
                  {
                    "spout" :shuffle
                  }

                  (scp-bolt
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["splitterbolt"]
                      "output.schema" {"default" ["word"]}
                    })

                  :p 1)

                "counter"

                (bolt-spec
                  {
                    "splitter" :global
                  }

                  (scp-bolt
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["counterbolt"]
                      "output.schema" {"default" ["word" "count"]}
                    })

                  :p 1)
              })

          :config
            {
              "topology.kryo.register" ["[B"]
            }
        }

4. リモート デスクトップを使用して HDInsight Storm クラスターに接続し、ローカル WordCount プロジェクトの **bin\debug** フォルダーを HDInsight Storm クラスターにコピーします。たとえば、このフォルダーを **%storm_home%\examples** フォルダーにコピーし、名前を **WordCount** に変更します。

3. **WordCount.spec** を HDInsight サーバーにもコピーしておきます。このフォルダーを **%storm_home%\examples** ディレクトリに置きます。

4. HDInsight Storm クラスターで、デスクトップの **Storm Command-Line** アイコンを使用してコマンドラインを開き、次のコマンドを使用して WordCount トポロジを開始します。

        bin\runspec examples\WordCount.spec temp examples\WordCount

    これで **temp** という名前の新しいフォルダーが作成されます。次に WordCount ソリューションの仕様とファイルを使用して **WordCount.jar** ファイルを作成します。さらにこのファイルを Storm に送信します。

5. コマンドが終了したら、デスクトップから **Storm UI** を開きます。**WordCount** トポロジが一覧表示されます。トポロジを **Storm UI** から選択すると、統計が表示されます。

6. トポロジを停止するには、**Storm UI** で **WordCount** を選択し、次に **[トポロジのアクション]** から **[強制終了]** を選択します。

##まとめ

上記の手順では、Stream Computing Platform を使用して、基本的なワード カウントのトポロジを作成、テスト、およびデプロイする方法について学習しました。詳細については、HDInsight クラスターにある **%storm_home%\examples** ディレクトリを参照してください。SCP の詳細については、次のリファレンスを参照してください。

##SCP リファレンス

###SCP プラグイン インターフェイス

SCP アプリケーション (プラグインともいいます) は、Storm のパイプラインにプラグインされます。プラグインは .NET コンソール アプリケーションで、次のインターフェイスを 1 つ以上実装します。  

-	**ISCPSpout** - 非トランザクションのスパウトを実装する場合に使用します。
- 	**ISCPTxSpout** - トランザクションのスパウトを実装する場合に使用します。
-	**ISCPBolt** - 非トランザクションのボルトを実装する場合に使用します。
-	**ISCPBatchBolt** - トランザクションのボルトを実装する場合に使用します。

####ISCPPlugin

ISCPPlugin は基本的なインターフェイスで、その他すべての SCP インターフェイスに継承されます。現在は、ダミー インターフェイスです。  

    public interface ISCPPlugin
    {
    }  

####ISCPSpout

ISCPSpout は非トランザクション スパウト用のインターフェイスです。  

    public interface ISCPSpout : ISCPPlugin
    {
        void NextTuple(Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

 `NextTuple()` が呼び出されると、コードで 1 つまたは複数のタプルを出力できます。出力するものがない場合、このメソッドは何も出力せずに戻ります。

`Ack()` と  `Fail()` は、spec ファイルで ACK メカニズムが有効である場合にのみ呼び出されます。 `seqId` は、確認されたか失敗したタプルを特定するために使用されます。そのため、ACK が非トランザクション トポロジで有効である場合は、スパウトで次の emit 関数を使用する必要があります。  

    public abstract void Emit(string streamId, List<object> values, long seqId);  

ACK が非トランザクション トポロジでサポートされていない場合、 `Ack()` と  `Fail()` を空の関数としておくことができます。  

これらの関数の  `parms` パラメーターは、空の Dictionary オブジェクトで、将来の使用のために予約されています。  

> [AZURE.NOTE] `NextTuple()`、 `Ack()`、および  `Fail()` はすべて、単一のスレッドの厳密なループで呼び出されます。出力するタプルがない場合は、CPU サイクルが浪費されないように、短時間 (10 ミリ秒など) の  `sleep` を使用することをお勧めします。

####ISCPTxSpout

ISCPTxSpout はトランザクション スパウト用のインターフェイスです。  

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

`NextTx()` が呼び出されると、新しいトランザクションが開始されます。 `seqId` はトランザクションを特定するために使用され、 `Ack()` と  `Fail()` でも使用されます。 `NextTx()` から出力されたデータは、再生をサポートするために ZooKeeper に格納されます。ZooKeeper のストレージ能力は限定的であるため、ユーザーはトランザクション スパウトでメタデータのみを出力し、大量のデータを出力しないようにする必要があります。  

Storm はトランザクションが失敗すると自動的に再生するため、Fail() は通常のケースでは呼び出されません。ただし、SCP がトランザクション スパウトによって出力されたメタデータを確認できる場合は、メタデータが無効のときに Fail() が呼び出されることがあります。

これらの関数の  `parms` パラメーターは、空の Dictionary オブジェクトで、将来の使用のために予約されています。

> [AZURE.NOTE] `NextTx()`、 `Ack()`、および  `Fail()` はすべて、単一のスレッドの厳密なループで呼び出されます。出力するタプルがない場合は、CPU サイクルが浪費されないように、短時間 (10 ミリ秒など) の  `sleep` を使用することをお勧めします。

####ISCPBolt

ISCPBolt は非トランザクション ボルト用のインターフェイスです。  

    public interface ISCPBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
    }

新しいタプルが利用可能になると、そのタプルを処理するために  `Execute()` 関数が呼び出されます。

####ISCPBatchBolt

ISCPBatchBolt はトランザクション ボルト用のインターフェイスです。  

    public interface ISCPBatchBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);
    }  

新しいタプルが使用可能な場合、そのタプルを処理するために  `Execute()` 関数が呼び出されます。 `FinishBatch()` はこのトランザクションが終了したときに呼び出されます。

これらの関数の  `parms` パラメーターは、空の Dictionary オブジェクトで、将来の使用のために予約されています。

> [AZURE.NOTE]  `ISCPBatchBolt` を実装するボルトにより  `parms` から . `StormTxAttempt` を取得できます。 `StormTxAttempt` は、タプルがオリジナルか再生用かを判定する場合に使用できます。これは通常、コミット ボルトで実行されます。これについては、**HelloWorldTx** の例で説明します。  

###オブジェクト モデル

SCP.NET は、開発者がプログラムに使用できる重要なオブジェクトの簡単なセットも提供しています。

* `Context` - アプリケーションに実行環境の情報を提供します。

* `StateStore` - メタデータ サービス、モノトニックなシーケンスの生成、および wait-free 調整を提供します。高度なレベルでの分散同時実行の抽象化は、 `StateStore` に基づいて構築されています。これには、分散ロック、分散キュー、バリア、およびトランザクション サービスが含まれます。

* `SCPRuntime` - Storm でソリューションを実行するときに、実行環境を初期化してプラグインを起動します。

* `LocalContext` - Visual Studio でローカルにアプリケーションをテストするときに、出力されたタプルをローカル ファイルにシリアル化および逆シリアル化するメソッドを提供します。

####Context

各 ISCPPlugin インスタンス (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) には対応する Context インスタンスがあります。Context が提供する機能は、次の 2 つに分かれています。

* **静的部分** - C# プロセス全体で使用できます。
* **動的部分** - 特定のインスタンスのみが使用できます。  

**静的部分の Context**

* `public static ILogger Logger = null;` - ログ記録の目的で提供されます。  

* `public static SCPPluginType pluginType;` - C# プロセスのプラグインの種類を取得します。C# プロセスをローカル テスト モード (Java なし) で実行する場合、プラグインの種類は "SCP_NET_LOCAL" です。

        public enum SCPPluginType
        {
            SCP_NET_LOCAL = 0,
            SCP_NET_SPOUT = 1,
            SCP_NET_BOLT = 2,
            SCP_NET_TX_SPOUT = 3,
            SCP_NET_BATCH_BOLT = 4
        }  

* `public static Config Config { get; set; }` - JVM からの構成パラメーターを取得します。パラメーターは、プラグインの初期化時に、JVM から渡されます。 `Config` には 2 つのディクショナリが含まれています。

    * `public Dictionary<string, Object> stormConf { get; set; }` - Storm で定義されているパラメーターが含まれています。

    * `public Dictionary<string, Object> pluginConf { get; set; }` - SCP で定義されたパラメーターが含まれています。

* `public static TopologyContext TopologyContext { get; set; } `  - トポロジのコンテキストを取得します。複数の並列処理を含むコンポーネントで最も役に立ちます。トポロジのコンテキストにアクセスする方法を次に示します。

        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
        {
            Context.Logger.Info("TopologyContext info:");
            TopologyContext topologyContext = Context.TopologyContext;
            Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
            taskIndex = topologyContext.GetThisTaskIndex();
            Context.Logger.Info("taskIndex: {0}", taskIndex);
            string componentId = topologyContext.GetThisComponentId();
            Context.Logger.Info("componentId: {0}", componentId);
            List<int> componentTasks = topologyContext.GetComponentTasks(componentId);
            Context.Logger.Info("taskNum: {0}", componentTasks.Count);
        }

**動的部分の Context**

* `public void DeclareComponentSchema(ComponentStreamSchema schema);` - ストリームの入力スキーマと出力を宣言します。次の例では、文字列のタプルのみからなる入力スキーマと、文字列と整数値の両方のタプルからなる出力スキーマを宣言します。

        this.ctx = context;
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));

* `public abstract void Emit(List<object> values);` - 既定のストリームに 1 つまたは複数のタプルを出力します。次の例では、2 つのタプルを既定のストリームに出力します。

        this.ctx.Emit(new Values(word, count));

* `public abstract void Emit(string streamId, List<object> values);` - 指定したストリームに 1 つまたは複数のタプルを出力します。次の例では、2 つのタプルを  'mystream' という名前のストリームに出力します。

        this.ctx.Emit("mystream", new Values(word, count));

ACK が有効な非トランザクションのスパウトとボルトを使用する場合は、次のコードを使用します。

* `public abstract void Emit(string streamId, List<object> values, long seqId);` - 1 つまたは複数のタプルとシーケンス ID を**スパウトから**指定のストリームに出力します。次の例では、タプルおよびシーケンスの識別子を既定のストリームに出力します。

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(word), lastSeqId);

* `public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);` - 1 つまたは複数のタプルとシーケンス ID を**ボルトから**指定のストリームに出力します。出力されたタプルは、 `anchors` として指定される着信タプルにアンカーされます。これにより、一連の発着信タプルに続いて ACK をパイプラインに戻すことができます。次の例では、タプルとシーケンスの識別子を既定のストリームに出力し、出力されたタプルを  `tuple` に含まれる着信タプルにアンカーします。

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word));

* `public abstract void Ack(SCPTuple tuple);` - タプルに ACK を行います。最初にタプルを生成したスパウトの  `ISCPSpout.Ack` 関数を呼び出します。

* `public abstract void Fail(SCPTuple tuple);` - タプルは失敗します。最初にタプルを生成したスパウトの  `ISCPSpout.Fail` 関数を呼び出します。

####StateStore

SCP アプリケーションでは、 `State` オブジェクトを使用して、ZooKeeper に情報 (特にトランザクション トポロジの情報) を保持できます。それによって、トランザクション スパウトがクラッシュした場合、ZooKeeper から状態に関する情報を取得し、パイプラインを再起動できます。  

 `StateStore` オブジェクトは、次のメソッドを提供します。

* `public static StateStore Get(string storePath, string connStr);` - 指定されたパスと接続文字列の  `StateStore` を取得します。

* `public State Create();` - 現在の状態ストア インスタンスに新しい  `State` オブジェクトを作成します。

* `public IEnumerable<State> GetUnCommitted();` - 中止状態を除き、コミットされなかったすべての  `State` オブジェクトを取得します。

* `public IEnumerable<State> States();` -  `StateStore` にあるすべての  'State' オブジェクトを取得します。


* `public T Get<T>(string info = null);` -  `State` オブジェクトまたは  `Registry` オブジェクトを取得します。

    * `info` - 取得する  `Registry` 名。 `Registry` オブジェクトを取得する場合のみ使用します。

    * `T` -  `State` または  `Registry` の種類。

* `public IEnumerable<Registry> Commited();` - コミットされた  `State` を含む  `Registry` オブジェクトを取得します。

* `public IEnumerable<Registry> Aborted();` - 中止された  `State` を含む  `Registry` オブジェクトを取得します。

* `public State GetState(long stateId)` - 指定した状態 ID の  `State` オブジェクトを取得します。

**State** には次のメソッドがあります。

* `public void Commit(bool simpleMode = true);` -  `State` オブジェクトの状態をコミットに設定します。

    > [AZURE.NOTE]  `simpleMode` が true に設定されている場合は、ZooKeeper の対応する ZNode を単純に削除します。それ以外の場合は、現在の ZNode を削除し、 `COMMITTED_PATH` に新しいノードを追加します。

* `public void Abort();` -  `State` オブジェクトの状態を中止に設定します。

* `public void PutAttribute<T>(string key, T attribute);` - 指定したキーの属性値を設定します。

    * `key` - 属性値の設定対象となるキー

    * `attribute` - 属性値

* `public T GetAttribute<T>(string key);` - 指定したキーの属性値を取得します。

####SCPRuntime

SCPRuntime には、次のメソッドがあります。

* `public static void Initialize();` - SCP のランタイム環境を初期化します。このメソッドが呼び出されると、C# プロセスで JVM 側に接続し、構成パラメーターとトポロジ コンテキストを取得します。

* `public static void LaunchPlugin(newSCPPlugin pluginDelegate);` - メッセージの処理ループを開始します。このループでは、C# プラグインで JVM 側から (タプルおよび制御シグナルを含む) メッセージを受信し、そのメッセージを処理します。

    * `pluginDelegate` - ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt を実装するオブジェクトを返すことができるデリゲートです。

####LocalContext


* `List<SCPTuple> RecvFromMsgQueue();` - キューからタプルを受信します。

* `void WriteMsgQueueToFile(string filepath, bool append = false);` - ファイルにタプルを保持します。

* `void ReadFromFileToMsgQueue(string filepath);` - ファイルからタプルを読み取ります。


###<a id="spec"></a>トポロジ仕様言語

SCP Topology Specification は、SCP トポロジを記述、構成するためのドメイン固有の言語です。これは Storm の [Clojure DSL](http://storm.incubator.apache.org/documentation/Clojure-DSL.html) に基づいています。  

次の関数を使用してトポロジを定義します。

|新しい関数|	パラメーター|	説明
|-------------|-----------|-----------
|**tx-topolopy**|	topology-name<br> spout-map<br> bolt-map|	 トポロジ名、スパウト定義マップ、およびボルト定義マップでトランザクション トポロジを定義します。
|**scp-tx-spout**|	exec-name<br> args<br> fields|	トランザクション スパウトを定義します。***args*** を使用して ***exec-name*** でアプリケーションを実行します。<br><br>***fields*** はスパウトの出力フィールドです。
|**scp-tx-batch-bolt**|	exec-name<br> args<br> fields| 	トランザクション バッチ ボルトを定義します。***args*** を使用して ***exec-name*** でアプリケーションを実行します。<br><br>*fields* はボルトの出力フィールドです。
|**scp-tx-commit-bolt**|	exec-name<br>args<br>fields|	トランザクション コミッター ボルトを定義します。args を使用して ***exec-name*** でアプリケーションを実行します。<br><br>***fields*** はボルトの出力フィールドです。
|**nontx-topolopy**|	topology-name<br> spout-map<br>bolt-map|	トポロジ名、スパウト定義マップ、およびボルト定義マップを指定して、  非トランザクション トポロジを定義します。
|**scp-spout**|	exec-name<br>args<br>fields<br>parameters|	非トランザクション スパウトを定義します。***args*** を使用して ***exec-name*** でアプリケーションを実行します。<br><br>***fields*** はスパウトの出力フィールドです。<br><br>***parameters*** は省略可能で、"nontransactional.ack.enabled" など、一部のパラメーターを指定する場合に使用します。
|**scp-bolt**|	exec-name<br>args<br>fields<br>parameters|	非トランザクション ボルトを定義します。***args*** を使用して ***exec-name*** でアプリケーションを実行します。<br><br>***fields*** はボルトの出力フィールドです。<br><br>***parameters*** は省略可能で、"nontransactional.ack.enabled" など、一部のパラメーターを指定する場合に使用します。

トポロジを定義する場合に使用するキーワードは、次のとおりです。

|キーワード|	説明
|---------|------------
|**:name**|	トポロジ名を定義します。
|**:topology**|	上の関数を使用してトポロジを定義し、構築します。
|**:p**|	各スパウトまたはボルトに対する並列処理のヒントを定義します。
|**:config**|	構成パラメーターを定義するか、既存のパラメーターを更新します。
|**:schema**|	ストリームのスキーマを定義します。

使用頻度の高いパラメーター

|パラメーター|	説明
|---------|------------
|**"plugin.name"**|	C# プラグインの exe ファイル名
|**"plugin.args"**|	プラグイン引数
|**"output.schema"**|	出力スキーマ
|**"nontransactional.ack.enabled"**|	ACK が非トランザクション トポロジで有効かどうか

トポロジ仕様は直接 Storm クラスターに送信し、***runspec*** コマンドを使用して実行できます。このコマンドは、HDInsight Storm クラスターの **%storm_home%\bin** ディレクトリにあります。  

    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
     ex: runSpec examples\HelloWorld\HelloWorld.spec target examples\HelloWorld\Target

> [AZURE.NOTE] ***resource-dir*** パラメーターは省略可能です。C# アプリケーションを実行する場合は指定する必要があり、このディレクトリにはアプリケーション、依存関係、および構成が含まれます。  

***classpath*** パラメーターも省略可能です。spec ファイルに Java スパウトまたはボルトが含まれている場合に、Java classpath を指定するために使用します。  

###その他の機能

####入力スキーマと出力スキーマの宣言

 `Emit()` を呼び出すと、プラットフォームはそのタプルをバイト配列にシリアル化し、JVM 側に転送します。次に Storm からこのタプルをターゲットに転送します。その後ボルトでタプルを受信します。C# ボルトの場合、タプルは JVM 側から受信し、元の型に変換し直します。

シリアル化と非シリアル化をサポートするためには、入力と出力のスキーマを宣言する必要があります。これらは  `Dictionary<string, List<Type>` オブジェクトとして定義されます。ここで、キーはストリームの ID で、値は出力されるタプルの  `Types` です。コンポーネントでは、マルチストリームを宣言できます。

    public class ComponentStreamSchema
    {
        public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
        public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
        public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
        {
            InputStreamSchema = input;
            OutputStreamSchema = output;
        }
    }

 `Context` オブジェクトは  `DeclareComponentSchema` を提供します。これを使用してシリアル化または逆シリアル化用のスキーマを宣言することができます。

     public void DeclareComponentSchema(ComponentStreamSchema schema)

> [AZURE.NOTE] 出力されたタプルがそのストリームに定義されているスキーマに従っていることを確認する必要があります。そうでないと、システムからランタイム例外を受信します。  

####マルチストリームのサポート

 `Emit()` に対して複数の呼び出しを実行し、書き込み対象の各ストリームの  `streamId` パラメーターを指定すると、複数のストリームに出力できます。

> [AZURE.NOTE] 存在しないストリームに出力すると、ランタイム例外が発生します。

####フィールドのグループ化

Strom に組み込まれているフィールドのグループ化は、SCP.NET では適切に動作しません。JVM プロキシ側では、すべてのフィールドのデータ型が実際には `byte[]` で、フィールドのグループ化では  `byte[]` オブジェクトのハッシュ コードを使用して、グループ化を実行します。`byte[]` オブジェクトのハッシュ コードは、このオブジェクトのメモリ内のアドレスです。そのため、同じコンテンツを共有するが、アドレスが異なる 2 つの `byte[]` オブジェクトをグループ化することはできません。

この問題を解決するには、spec ファイルで  `scp-field-group` を使用します。これにより、`byte[]` のコンテンツを使用してグループ化を実行できます。spec ファイルで `scp-field-group` を使用する方法を、次の例に示します。  

    (bolt-spec
        {
            "spout_test""" (scp-field-group :non-tx [0,1])
        }
        ...
    )  

この例では、次のコードを実行します。

* `scp-field-group` - カスタムのグループ化を使用します。  
* `:tx` または `:non-tx` - トランザクショナルか非トランザクショナルかを示します。
* `[0,1]` - 0 から始まるフィールド ID のハッシュセットを使用します。  

####ハイブリッド トポロジ

ネイティブ Storm のスパウト、ボルト、およびトポロジの多くは、Java で実装されています。C# コンポーネントを使用するソリューションで、これらのコンポーネントを再利用できるようにするために、SCP では、spec ファイルでハイブリッド トポロジの定義を作成できます。

* **Java スパウトまたはボルト** - spec ファイルでは、 `scp-spout` と  `scp-spout` を使用して Java スパウトおよびボルトを指定することもできます。 `microsoft.scp.example.HybridTopology.Generator` というクラス名の Java スパウトを指定する例を次に示します。

        (spout-spec
          (microsoft.scp.example.HybridTopology.Generator.)
          :p 1)

* **Java Classpath** - Java スパウトまたはボルトを使用する場合、まず JAR ファイルにスパウトまたはボルトをコンパイルする必要があります。次に、**runSpec** コマンドを使用する場合、Java Classpath に `-cp` パラメーターを追加します。次の例には、**examples\HybridTopology\java\target** ディレクトリ以下にある JAR ファイルが含まれます。  

        bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*  


* **Java と C# の間のシリアル化と非シリアル化** - Java と C# の間でオブジェクトをシリアル化するには  `CustomizedInteropJSONSerializer` を使用します。

    > [AZURE.NOTE]  `CustomizedInteropJSONSerializer` でサポートされているのは、**Java スパウト**と **C# ボルト**のみです。

    * spec ファイルで Java コンポーネントのシリアライザーを指定します。

            (scp-bolt
              {
                "plugin.name" "HybridTopology.exe"
                "plugin.args" ["displayer"]
                "output.schema" {}
                "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
              })  

    * C# コンポーネントでシリアライザーを使用します。

            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            inputSchema.Add("default", new List<Type>() { typeof(Person) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
            this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());  

    データ型があまり複雑でない場合は、この既定の実装でほとんどのケースに対応できます。  データ型が複雑すぎる場合や、既定の実装のパフォーマンスがユーザーの要求を満たしていない場合には、次のインターフェイスを使用して、ニーズに合わせて独自の実装を作成できます。

    **Java 側でのシリアル化インターフェイス**  

            public interface ICustomizedInteropJavaSerializer {
                public void prepare(String[] args);
                public List<ByteBuffer> serialize(List<Object> objectList);
            }  

    **C# 側での非シリアル化インターフェイス**

            public interface ICustomizedInteropCSharpDeserializer
            {
                List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
            }  

###SCP ホスト モード

ホスト モードでは、プロジェクトを DLL にコンパイルし、**SCPHost.exe** を使用してプロジェクトをホストできます。運用設定でソリューションをホストする場合は、この方法が推奨されます。ホスト モードを使用する場合、spec ファイルで  `SCPHost.exe` がプラグインとして一覧表示されます。

次の例に、HelloWorld の例でホスト モードを使用する spec ファイルを示します。

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

* **HelloWorld.dll** - スパウトとボルトを含むアセンブリ
* **Scp.App.HelloWorld.Generator** - **HelloWorld.dll** に含まれるスパウトのクラス名
* **Get** - スパウトのインスタンスを取得するために呼び出すメソッド


##SCP プログラミングの例

次の例のアプリケーションは SCP により記述されています。このアプリケーションは HDInsight Storm クラスターの **%storm_home%\examples** にあります。

* **HelloWorld** - HelloWorld は、SCP.Net のごく簡単な例で、この記事で前述したワード カウントの例によく似ています。**generator** というスパウトと、**splitter** と **counter** という 2 つのボルトを持つ非トランザクション トポロジを使用します。**generator** スパウトは、複数の文を無作為に生成し、それらの文を **splitter** に出力します。**splitter** ボルトは、文を単語に分割し、それらの単語を **counter** ボルトに出力します。**counter** ボルトはディクショナリを使用して、各単語の出現回数を記録します。

    この例には、**HelloWorld.spec** と **HelloWorld_EnableAck.spec** の 2 つの spec ファイルがあります。**HelloWorld_EnableAck.spec** を使用する場合、サンプルからタプルに ACK が返されてボルトに渡されますが、**splitter** はいくつかのボルトに Fail を無作為に返し、非トランザクション トポロジの処理の失敗を示すように設計されています。

* **HelloWorldTx** - トランザクション トポロジの実装方法を示すサンプル。**generator** というトランザクション スパウト、**partial-count** というバッチ ボルト、および **count-sum** というコミット ボルトがあります。また、このトポロジで使用する、事前作成された 3 つのテキスト ファイルとして、**DataSource0.txt**、**DataSource1.txt**、**DataSource2.txt** があります。

    各トランザクションでは、スパウトが無作為に 2 つのファイルを選択し、その 2 つのファイル名を **partial-count** ボルトに出力します。ボルトは受信したタプルからファイル名を取得し、そのファイルを開いてファイル内の単語数をカウントします。最後に **count-sum** ボルトに単語数を出力します。**count-sum** ボルトは、合計数を集計します。  

    "厳密に 1 回" セマンティクスを実現するには、**count-sum** ボルトで、再生されたトランザクションかどうかを判断する必要があります。この例では、静的メンバー変数があります。  

        public static long lastCommittedTxId = -1;  

    ボルトのインスタンスが作成されると、入力パラメーターからトランザクションの試行 (`txAttempt`) を取得します。  

        public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
        {
            /* for transactional topology, we can get txAttempt from the input parms */
            if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
            {
                StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
                return new CountSum(ctx, txAttempt);
            }
            else
            {
                throw new Exception("null txAttempt");
            }
        }  

     `FinishBatch()` が呼び出されると、再生されたトランザクションでない場合、 `lastCommittedTxId` が更新されます。  

        public void FinishBatch(Dictionary<string, Object> parms)
        {
            /* judge whether it is a replayed transaction? */
            bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

            if (!replay)
            {
                /* If it is not replayed, update the toalCount and lastCommittedTxId vaule */
                totalCount = totalCount + this.count;
                lastCommittedTxId = this.txAttempt.TxId;
            }
            ... ...
        }  

* **HybridTopology** - このトポロジには Java スパウトと C# ボルトが含まれています。SCP が提供する既定のシリアル化と逆シリアル化の実装を使用します。詳細については、**%storm_home%examples\HybridTopology\HybridTopology.spec** を参照してください。トポロジを送信する場合の Java Classpath の指定方法については、**SubmitTopology.bat** を参照してください。  


[1]: ./media/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application-01.png
<!--HONumber=42-->
