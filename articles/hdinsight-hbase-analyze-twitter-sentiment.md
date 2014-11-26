<properties urlDisplayName="Analyze realt-time Twitter sentiment with Hbase in HDInsight" pageTitle="HDInsight 環境の HBase で Twitter のセンチメントをリアルタイム分析する | Azure" metaKeywords="" description="Learn how to do real-time analysis of big data using HBase in an HDInsight (Hadoop) cluster." metaCanonical="" services="hdinsight" documentationCenter="" title="Analyze real-time Twitter sentiment with HBase in HDInsight" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao" />

# HDInsight 環境の HBase で Twitter のセンチメントをリアルタイム分析する

HDInsight (Hadoop) クラスターで、HBase を使用してリアルタイムでビッグ データの [センチメントを分析](http://en.wikipedia.org/wiki/Sentiment_analysis)する方法について取り上げます。


Big Data の多くはソーシャル Web サイトからもたらされます。Twitter などのサイトが公開している API を介して収集したデータは、現在の動向を分析して把握するための有益な情報源となります。このチュートリアルでは、以下の事柄を実行するためのコンソール ストリーミング サービス アプリケーションと ASP.NET Web アプリケーションを開発します。 

![][img-app-arch]

- Twitter streaming API を使用して、ジオタグ付けされたツイートをリアルタイムで取得します。
- そうしたツイートのセンチメントを評価します。
- センチメント情報を、Microsoft HBase SDK を使用して HBase に格納します。
- リアルタイム統計結果を ASP.NET Web アプリケーションを使用して Bing マップにプロットします。ツイートを視覚化すると、次のようになります。

	![hdinsight.hbase.twitter.sentiment.bing.map][img-bing-map]
	
	特定のキーワードを使用してツイートを照会し、ツイートで表明された意見の感情が肯定的、否定的、中立のどれであるかを取得できます。

Visual Studio ソリューションの完全なサンプルは、[https://github.com/maxluk/tweet-sentiment](https://github.com/maxluk/tweet-sentiment) に示されています。






























##この記事の内容

- [前提条件](#prerequisites)
- [Twitter アプリケーションを作成する](#twitter)
- [簡単な Twitter ストリーミング サービスを作成する](#streaming)
- [Azure Website を作成して、Twitter センチメントを視覚化する](#web)
- [次のステップ](#nextsteps)

##<a id="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **HDInsight 環境の HBase クラスター**。クラスター プロビジョニングについては、「[HDInsight で Hadoop の HBase を使用して開始する][ hBase-get-started]」を参照してください。このチュートリアルを読み進めるには、次のデータが必要です。

	<table border="1">
	<tr><th>クラスターのプロパティ</th><th>説明</th></tr>
	<tr><td>HBase クラスター名</td><td>これは使用する HDInsight HBase クラスター名です。次に例を示します。https://myhbase.azurehdinsight.net/</td></tr>
	<tr><td>クラスター ユーザー名</td><td>Hadoop ユーザーのアカウント名です。既定の Hadoop ユーザー名は <strong>admin</strong> です。</td></tr>
	<tr><td>クラスター ユーザーのパスワード</td><td>Hadoop クラスター ユーザーのパスワードです。</td></tr>
	</table>

Visual Studio 2013 がインストールされている- **ワークステーション**。手順については、「[Visual Studio のインストール](http://msdn.microsoft.com/ja-jp/library/e2h7fzkw.aspx)」を参照してください。





##<a id="twitter"></a>Twitter アプリケーション ID とシークレットを作成する

Twitter Streaming API は [OAuth](http://oauth.net/) を使用して要求を承認します。 

OAuth を使用するための最初の手順は、Twitter 開発者サイトで新しいアプリケーションを作成することです。

**Twitter アプリケーション ID とシークレットを作成するには**

1. [https://apps.twitter.com/](https://apps.twitter.com/) にサインインします。Twitter アカウントを持っていない場合は、**[今すぐ登録]** リンクをクリックします。
2. **[Create New App]** をクリックします。
3. **名前**、**説明**、**Web サイト**を入力します。[Website] フィールドは実際には使用しません。有効な URL である必要はありません。次のテーブルは使用する値のサンプルを示しています。

	<table border="1">
	<tr><th>フィールド</th><th>値</th></tr>
	<tr><td>名前</td><td>MyHDInsightHBaseApp</td></tr>
	<tr><td>説明</td><td>MyHDInsightHBaseApp</td></tr>
	<tr><td>Web サイト</td><td>http://www.myhdinsighthbaseapp.com</td></tr>
	</table>

4. **[Yes, I agree]** をオンにして、**[Create your Twitter application]** をクリックします。
5. **[Permissions]** タブをクリックします。既定のアクセス許可は**読み取り専用**です。このチュートリアルにはこれで十分です。 
6. **[API Keys]** タブをクリックします。
7. **[Create my access token]** をクリックします。
8. ページの右上隅にある **[Test OAuth]** をクリックします。
9. **[API key]**、**[API secret]**、**[Access token]**、および **[Access token secret]** の内容を書き留めます。これらの値は後で必要になります。

	![hdi.hbase.twitter.sentiment.twitter.app][img-twitter-app]






























##<a id="streaming"></a> 簡単な Twitter ストリーミング サービスを作成する

ツイートを取得し、ツイート センチメント スコアを計算し、処理したツイート ワードを HBase に送信するコンソール アプリケーションを作成します。

**  Visual Studio ソリューションを作成するには**

1. **Visual Studio** を開きます。
2. **[ファイル]** メニューの**[新規]** をポイントし、**[プロジェクト]** をクリックします。
3. 次の値を入力または選択します。

	- テンプレート: **Visual C#**
	- テンプレート: **コンソール アプリケーション**
	- 名前: **TweetSentimentStreaming** 
	- 場所: **C:\Tutorials**
	- ソリューション名:  **TweetSentimentStreaming**

4. **[OK]** をクリックして続行します。
 


**Nuget パッケージをインストールして SDK 参照を追加するには**

1. **[ツール]** メニューで、**[Nuget パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。コンソール パネルがページ下部で開きます。
2. 以下のコマンドを使用して [Tweetinvi](https://www.nuget.org/packages/TweetinviAPI/) パッケージ (Twitter API にアクセスするために使用します) と、[Protobuf-net](https://www.nuget.org/packages/protobuf-net/) パッケージ (オブジェクトのシリアル化と逆シリアル化に使用します) をインストールします。

		Install-Package TweetinviAPI
		Install-Package protobuf-net 

	> [WACOM.NOTE]Microsoft Hbase SDK Nuget パッケージは、2014 年 8 月 26 日現在使用できません。Github リポジトリは [https://github.com/hdinsight/hbase-sdk-for-net](https://github.com/hdinsight/hbase-sdk-for-net) にあります。この SDK が使用可能になるまでは、dll を自分でビルドする必要があります。手順については、「[HDInsight で Hadoop の HBase を使用して開始する][hBase-get-started]」を参照してください。

3. **ソリューション エクスプローラーで**、**[参照]** を右クリックし、**[参照の追加]** をクリックします。
4. 左のウィンドウで **[アセンブリ]** を展開し、**[フレームワーク]** をクリックします。
5. 右のウィンドウで、**[System.Configuration]** の前にあるチェック ボックスをオンにし、**[OK]** をクリックします。



**Tweeter ストリーミング サービス クラスを定義するには**

1. **ソリューション エクスプローラーで**、**[TweetSentimentStreaming]** を右クリックし、**[追加]** をポイントしてから **[クラス]** をクリックします。
2. **[名前]** に「**HBaseWriter**」と入力して、**[追加]** をクリックします。
3. **HBaseWriter.cs** で、ファイルの上部に using ステートメントを使用した以下の内容を追加します。

		using System.IO;		
		using System.Threading;
		using System.Globalization;
		using Microsoft.HBase.Client;
		using Tweetinvi.Core.Interfaces;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

4. **HbaseWriter.cs** 内に、**DictionaryItem** という新しいクラスを追加します。

	    public class DictionaryItem
	    {
	        public string Type { get; set; }
	        public int Length { get; set; }
	        public string Word { get; set; }
	        public string Pos { get; set; }
	        public string Stemmed { get; set; }
	        public string Polarity { get; set; }
	    }

	このクラス構造は、センチメント辞書ファイルを解析するために使用します。このデータは、それぞれのツイートのセンチメント スコアを計算するときに使用します。

5. **HBaseWriter** クラス内で、以下の定数と変数を定義します。

        // HDinsight HBase クラスターと HBase テーブルの情報
        const string CLUSTERNAME = "https://<HBaseClusterName>.azurehdinsight.net/";
        const string HADOOPUSERNAME = "<HadoopUserName>"; //the default name is "admin"
        const string HADOOPUSERPASSWORD = "<HaddopUserPassword>";
        const string HBASETABLENAME = "tweets_by_words";

        // センチメントの辞書ファイルと句読文字
        const string DICTIONARYFILENAME = @"..\..\data\dictionary\dictionary.tsv";
        private static char[] _punctuationChars = new[] { 
            ' ', '!', '\"', '#', '$', '%', '&', '\'', '(', ')', '*', '+', ',', '-', '.', '/',   //ascii 23--47
            ':', ';', '<', '=', '>', '?', '@', '[', ']', '^', '_', '`', '{', '|', '}', '~' };   //ascii 58--64 + misc.

        // HBase への書き込み用
        HBaseClient client;

        // 予期されるセンチメント用のセンチメント辞書。物理ファイルから読み込まれます。
        Dictionary<string, DictionaryItem> dictionary;
        
        // マルチスレッド書き込みを使用します
        Thread writerThread;
        Queue<ITweet> queue = new Queue<ITweet>();
        bool threadRunning = true;

6. **&lt;HBaseClusterName>**、**&lt;HadoopUserName>**、**&lt;HaddopUserPassword>** などの定数値を設定します。HBase テーブル名を変更する場合には、それに応じて Web アプリケーション内のテーブル名も変更する必要があります。

	このチュートリアルで後ほど、dictionary.tsv ファイルをダウンロードして特定のフォルダーに移動します。

7. **HBaseWriter** クラス内で以下の定数を定義します。

		// この関数は HBase に接続し、センチメントの辞書を読み込み、書き込み用のスレッドを開始します。
        public HBaseWriter()
        {
            ClusterCredentials credentials = new ClusterCredentials(new Uri(CLUSTERNAME), HADOOPUSERNAME, HADOOPUSERPASSWORD);
            client = new HBaseClient(credentials);

            // HBase テーブルが存在しない場合は作成します。
            if (!client.ListTables().name.Contains(HBASETABLENAME))
            {
                TableSchema tableSchema = new TableSchema();
                tableSchema.name = HBASETABLENAME;
                tableSchema.columns.Add(new ColumnSchema { name = "d" });
                client.CreateTable(tableSchema);
                Console.WriteLine("Table \"{0}\" is created.", HBASETABLENAME);
            }

            // ファイルからセンチメント辞書を読み込みます
            LoadDictionary();

			// HBase へ書き込むためのスレッドを開始します
            writerThread = new Thread(new ThreadStart(WriterThreadFunction));
            writerThread.Start();
        }

        ~HBaseWriter()
        {
            threadRunning = false;
        }

        // 受け取ったツイートをエンキューします
        public void WriteTweet(ITweet tweet)
        {
            lock (queue)
            {
                queue.Enqueue(tweet);
            }
        }

        // ファイルからセンチメント辞書を読み込みます
        private void LoadDictionary()
        {
            List<string> lines = File.ReadAllLines(DICTIONARYFILENAME).ToList();
            var items = lines.Select(line =>
            {
                var fields = line.Split('\t');
                var pos = 0;
                return new DictionaryItem
                {
                    Type = fields[pos++],
                    Length = Convert.ToInt32(fields[pos++]),
                    Word = fields[pos++],
                    Pos = fields[pos++],
                    Stemmed = fields[pos++],
                    Polarity = fields[pos++]
                };
            });

            dictionary = new Dictionary<string, DictionaryItem>();
            foreach (var item in items)
            {
                if (!dictionary.Keys.Contains(item.Word))
                {
                    dictionary.Add(item.Word, item);
                }
            }
        }

        // センチメントのスコアを計算します
        private int CalcSentimentScore(string[] words)
        {
            Int32 total = 0;
            foreach (string word in words)
            {
                if (dictionary.Keys.Contains(word))
                {
                    switch (dictionary[word].Polarity)
                    {
                        case "negative":total -= 1; break;
                        case "positive":total += 1; break;
                    }
                }
            }
            if (total > 0)
            {
                return 1;
            }
            else if (total < 0)
            {
                return -1;
            }
            else
            {
                return 0;
            }
        }

		// 一般的な CellSet オブジェクトを HBase に書き込みます
        private void CreateTweetByWordsCells(CellSet set, ITweet tweet)
        {
            // ツイートを単語に分割します
            string[] words = tweet.Text.ToLower().Split(_punctuationChars);

            // ワードに基づいてセンチメント スコアを計算します
            int sentimentScore = CalcSentimentScore(words);
            var word_pairs = words.Take(words.Length - 1)
                                  .Select((word, idx) => string.Format("{0} {1}", word, words[idx + 1]));
            var all_words = words.Concat(word_pairs).ToList();

            // ツイートの 1 ワードごとに 1 行を HBase テーブルに追加します
            foreach (string word in all_words)
            {
                string time_index = (ulong.MaxValue - (ulong)tweet.CreatedAt.ToBinary()).ToString().PadLeft(20) + tweet.IdStr;
                string key = word + "_" + time_index;

                // 行を作成します
                var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };

                // ツイートの識別子、言語、コーディネーター (利用可能な場合)、センチメントなどの列を先の行に追加します 
                var value = new Cell { column = Encoding.UTF8.GetBytes("d:id_str"), data = Encoding.UTF8.GetBytes(tweet.IdStr) };
                row.values.Add(value);
                
                value = new Cell { column = Encoding.UTF8.GetBytes("d:lang"), data = Encoding.UTF8.GetBytes(tweet.Language.ToString()) };
                row.values.Add(value);
                
                if (tweet.Coordinates != null)
                {
                    var str = tweet.Coordinates.Longitude.ToString() + "," + tweet.Coordinates.Latitude.ToString();
                    value = new Cell { column = Encoding.UTF8.GetBytes("d:coor"), data = Encoding.UTF8.GetBytes(str) };
                    row.values.Add(value);
                }

                value = new Cell { column = Encoding.UTF8.GetBytes("d:sentiment"), data = Encoding.UTF8.GetBytes(sentimentScore.ToString()) };
                row.values.Add(value);

                set.rows.Add(row);
            }
        }

        // ツイート (CellSet) を HBase に書き込みます
        public void WriterThreadFunction()
        {
            try
            {
                while (threadRunning)
                {
                    if (queue.Count > 0)
                    {
                        CellSet set = new CellSet();
                        lock (queue)
                        {
                            do
                            {
                                ITweet tweet = queue.Dequeue();
                                CreateTweetByWordsCells(set, tweet);
                            } while (queue.Count > 0);
                        }

                        // ワード セルごとにツイートを書き込み、HBase テーブルに設定します
                        client.StoreCells(HBASETABLENAME, set);
                        Console.WriteLine("\tRows written:{0}", set.rows.Count);
                    }
                    Thread.Sleep(100);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Exception:" + ex.Message);
            }
        }

	このコードにより、以下の機能が提供されます。

	- **Hbase [ HBaseWriter() ]**への接続: この HBase SDK により、クラスター URL と Hadoop ユーザー資格情報を使用して *ClusterCredentials* オブジェクトを作成してから、ClusterCredentials オブジェクトを使用して *HBaseClient* オブジェクトを作成します。
	- **HBase テーブル [ HBaseWriter() ]**の作成: メソッド呼び出しは *HBaseClient.CreateTable()* です。
	- **HBase テーブル [ WriterThreadFunction() ]**に書き込む: メソッド呼び出しは *HBaseClient.StoreCells()* です。

**Program.cs を完成させるには**

1. **ソリューション エクスプローラー**で、**Program.cs** をダブルクリックして、このファイルを開きます。
2. ファイルの先頭に以下の using ステートメントを追加します。

		using System.Configuration;
		using System.Diagnostics;
		using Tweetinvi;

3. **Program** クラス内に、以下の定数を定義します。

        const string TWITTERAPPACCESSTOKEN = "<TwitterApplicationAccessToken";
        const string TWITTERAPPACCESSTOKENSECRET = "TwitterApplicationAccessTokenSecret";
        const string TWITTERAPPAPIKEY = "TwitterApplicationAPIKey";
        const string TWITTERAPPAPISECRET = "TwitterApplicationAPISecret";

4. 使用している Twitter アプリケーション値と一致するように定数値を設定します。

3. **Main()** 関数を次のように変更します。

		static void Main(string[] args)
		{
            TwitterCredentials.SetCredentials(TWITTERAPPACCESSTOKEN, TWITTERAPPACCESSTOKENSECRET, TWITTERAPPAPIKEY, TWITTERAPPAPISECRET);

            Stream_FilteredStreamExample();
		}

4. 以下の関数をこのクラスに追加します。

        private static void Stream_FilteredStreamExample()
        {
            for (; ; )
            {
                try
                {
                    HBaseWriter hbase = new HBaseWriter();
                    var stream = Stream.CreateFilteredStream();
                    stream.AddLocation(Geo.GenerateLocation(-180, -90, 180, 90));

                    var tweetCount = 0;
                    var timer = Stopwatch.StartNew();

                    stream.MatchingTweetReceived += (sender, args) =>
                    {
                        tweetCount++;
                        var tweet = args.Tweet;

                        // HBase にツイートを書き込みます
                        hbase.WriteTweet(tweet);

                        if (timer.ElapsedMilliseconds > 1000)
                        {
                            if (tweet.Coordinates != null)
                            {
                                Console.ForegroundColor = ConsoleColor.Green;
                                Console.WriteLine("\n{0}:{1} {2}", tweet.Id, tweet.Language.ToString(), tweet.Text);
                                Console.ForegroundColor = ConsoleColor.White;
                                Console.WriteLine("\tLocation:{0}, {1}", tweet.Coordinates.Longitude, tweet.Coordinates.Latitude);
                            }

                            timer.Restart();
                            Console.WriteLine("\tTweets/sec:{0}", tweetCount);
                            tweetCount = 0;
                        }
                    };

                    stream.StartStreamMatchingAllConditions();
                }
                catch (Exception ex)
                {
                    Console.WriteLine("Exception:{0}", ex.Message);
                }
            }
        }

**センチメント辞書ファイルをダウンロードするには**

1. [https://github.com/maxluk/tweet-sentiment](https://github.com/maxluk/tweet-sentiment) にアクセスします。
2. **[ZIP のダウンロード]** をクリックします。
3. このファイルをローカルに解凍します。
4. ファイルを **../tweet-sentiment/SimpleStreamingService/data/dictionary/dictionary.tsv** からコピーします。
5. このファイルを **TweetSentimentStreaming/TweetSentimentStreaming/data/dictionary/dictionary.tsv** の下のソリューションに貼り付けます。

**ストリーム サービスを実行するには**

1. Visual Studio で **F5** を押します。コンソール アプリケーションのスクリーンショットは次のようになります。

	![hdinsight.hbase.twitter.sentiment.streaming.service][img-streaming-service]
2. Web アプリケーションの作成中はストリーミング コンソール アプリケーションを実行したままにし、さらに多くのデータを使用できるようにしてください。



























##<a id="web"></a> Azure Website を作成して、Twitter センチメントを視覚化する

このセクションでは、リアルタイムのセンチメント データを HBase から読み取り、データを Bing マップにプロットする ASP.NET MVC Web アプリケーションを作成します。

**ASP.NET MVC Web アプリケーションを作成するには**

1. Visual Studio を開きます。
2. **[ファイル]**、**[新規]**、**[プロジェクト]** の順にクリックします。
3. 次のように入力します。

	- テンプレート カテゴリ: **Visual C#/Web**
	- テンプレート: **ASP.NET Web アプリケーション**
	- 名前: **TweetSentimentWeb**
	- 場所: **C:\Tutorials** 
4. **[OK]** をクリックします。
5. **[テンプレートの選択]** で、**[MVC]** をクリックします。 
6. **[Windows Azure]** で、**[サブスクリプションの管理]** をクリックします。
7. **[Windows Azure サブスクリプションの管理]** で **[サインイン]** をクリックします。
8. Azure 資格情報を入力します。[アカウント] タブに、Azure サブスクリプションの情報が表示されます。
9. **[閉じる]** をクリックし、[Windows Azure サブスクリプションの管理] ウィンドウを閉じます。
10. **[新規 ASP.NET プロジェクト - TweetSentimentWeb]** で **[OK]** をクリックします。
11. **[Windows Azure サイト設定の構成]** で、お近くの **[リージョン]** を選択します。データベース サーバーを指定する必要はありません。 
12. **[OK]** をクリックします。

**Nuget パッケージをインストールするには**

1. **[ツール]** メニューで、**[Nuget パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。コンソール パネルがページ下部で開きます。
2. 以下のコマンドを使用して [Protobuf-net](https://www.nuget.org/packages/protobuf-net/) パッケージをインストールします。このパッケージはオブジェクトのシリアル化および逆シリアル化に使用します。

		Install-Package protobuf-net 

	> [WACOM.NOTE]Microsoft Hbase SDK Nuget パッケージは、2014 年 8 月 20 日現在使用できません。Github リポジトリは [https://github.com/hdinsight/hbase-sdk-for-net](https://github.com/hdinsight/hbase-sdk-for-net) にあります。この SDK が使用可能になるまでは、dll を自分でビルドする必要があります。手順については、「[HDInsight で Hadoop の HBase を使用して開始する][hBase-get-started]」を参照してください。

**HBaseReader クラスを追加するには**

1. **ソリューション エクスプローラー**で、**TweetSentiment** を展開します。
2. **[モデル]** を右クリックして、**[追加]**、**[クラス]** の順にクリックします。
3. [名前] に、「**HBaseReader.cs**」と入力し、**[追加]** をクリックします。
4. コードを次のコードに置き換えます。

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Web;
		
		using System.Configuration;
		using System.Threading.Tasks;
		using System.Text;
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;
		
		namespace TweetSentimentWeb.Models
		{
		    public class HBaseReader
		    {
		        // ツイートのセンチメント データを HDInsight HBase で読み出します
		        HBaseClient client;
		
		        // HDinsight HBase クラスターと HBase テーブルの情報
		        const string CLUSTERNAME = "<HBaseClusterName>";
		        const string HADOOPUSERNAME = "<HBaseClusterHadoopUserName>"
		        const string HADOOPUSERPASSWORD = "<HBaseCluserUserPassword>";
		        const string HBASETABLENAME = "tweets_by_words";
		
		        // コンストラクター
		        public HBaseReader()
		        {
		            ClusterCredentials creds = new ClusterCredentials(
		                            new Uri(CLUSTERNAME),
		                            HADOOPUSERNAME,
		                            HADOOPUSERPASSWORD);
		            client = new HBaseClient(creds);
		        }
		
		        // HBase テーブルのツイートのセンチメント データを非同期的に照会します 
		        public async Task<IEnumerable<Tweet>> QueryTweetsByKeywordAsync(string keyword)
		        {
		            List<Tweet> list = new List<Tweet>();
		
		            // 直近 6 時間のデータの行キーに対するフィルタリングを示します
		            string timeIndex = (ulong.MaxValue -
		                (ulong)DateTime.UtcNow.Subtract(new TimeSpan(6, 0, 0)).ToBinary()).ToString().PadLeft(20);
		            string startRow = keyword + "_" + timeIndex;
		            string endRow = keyword + "|";
		            Scanner scanSettings = new Scanner
		            {
		                batch = 100000,
		                startRow = Encoding.UTF8.GetBytes(startRow),
		                endRow = Encoding.UTF8.GetBytes(endRow)
		            };
		
		            // 非同期のスキャンを呼び出します
		            ScannerInformation scannerInfo =
		                await client.CreateScannerAsync(HBASETABLENAME, scanSettings);
		
		            CellSet next;
		
		            while ((next = await client.ScannerGetNextAsync(scannerInfo)) != null)
		            {
		                foreach (CellSet.Row row in next.rows)
		                {
		                    // 文字列パターンが "d:coor" のセルを検索します 
		                    var coordinates =
		                        row.values.Find(c => Encoding.UTF8.GetString(c.column) == "d:coor");
		
		                    if (coordinates != null)
		                    {
		                        string[] lonlat = Encoding.UTF8.GetString(coordinates.data).Split(',');
		
		                        var sentimentField =
		                            row.values.Find(c => Encoding.UTF8.GetString(c.column) == "d:sentiment");
		                        Int32 sentiment = 0;
		                        if (sentimentField != null)
		                        {
		                            sentiment = Convert.ToInt32(Encoding.UTF8.GetString(sentimentField.data));
		                        }
		
		                        list.Add(new Tweet
		                        {
		                            Longtitude = Convert.ToDouble(lonlat[0]),
		                            Latitude = Convert.ToDouble(lonlat[1]),
		                            Sentiment = sentiment
		                        });
		                    }
		
		                    if (coordinates != null)
		                    {
		                        string[] lonlat = Encoding.UTF8.GetString(coordinates.data).Split(',');
		                    }
		                }
		            }
		
		            return list;
		        }
		    }
		
		    public class Tweet
		    {
		        public string IdStr { get; set; }
		        public string Text { get; set; }
		        public string Lang { get; set; }
		        public double Longtitude { get; set; }
		        public double Latitude { get; set; }
		        public int Sentiment { get; set; }
		    }
		}

4. **HBaseReader** クラス内で、以下の定数値を変更します。

	- **CLUSTERNAME**: HBase クラスター名。たとえば、*https://<HBaseClusterName>.azurehdinsight.net/* などです。 
    - **HADOOPUSERNAME**: HBase クラスターの Hadoop ユーザーのユーザー名。既定の名前は *admin* です。
    - **HADOOPUSERPASSWORD**: HBase クラスターの Hadoop ユーザーのパスワード。
    - **HBASETABLENAME** = "tweets_by_words";

	HBase テーブル名は "tweets_by_words" です。値は、ストリーミング サービスで送信した値と同じである必要があります。そのようにすると、Web アプリケーションは同じ HBase テーブルのデータを読み取ることができます。




**TweetsController コントローラーを追加するには**

1. **ソリューション エクスプローラー**で、**TweetSentimentWeb** を展開します。
2. **[コントローラー]** を右クリックして、**[追加]**、**[コントローラー]** の順にクリックします。
3. **[Web API 2 コントローラー - 空]** をクリックしてから **[追加]** をクリックします。
4. [コントローラー名] に「**TweetsController**」と入力し、**[追加]** をクリックします。
5. **ソリューション エクスプローラー**で TweetsController.cs をダブルクリックしてファイルを開きます。
5. ファイルを以下のように変更します。

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		
		using System.Threading.Tasks;
		using TweetSentimentWeb.Models;
		
		namespace TweetSentimentWeb.Controllers
		{
		    public class TweetsController :ApiController
		    {
		        HBaseReader hbase = new HBaseReader();
		
		        public async Task<IEnumerable<Tweet>> GetTweetsByQuery(string query)
		        {
		            return await hbase.QueryTweetsByKeywordAsync(query);
		        }
		    }
		}

**heatmap.js を追加するには**

1. **ソリューション エクスプローラー**で、**TweetSentimentWeb** を展開します。
2. **[スクリプト]** を右クリックし、**[追加]**、**[JavaScript ファイル]** の順にクリックします。
3. [項目名] に、「**heatmap.js**」と入力します。
4. 以下のコードをコピーして、ファイルに貼り付けます。このコードの作成者は Alastair Aitchison です。詳細については、[http://alastaira.wordpress.com/2011/04/15/bing-maps-ajax-v7-heatmap-library/](http://alastaira.wordpress.com/2011/04/15/bing-maps-ajax-v7-heatmap-library/) を参照してください。

		/*******************************************************************************
		* 執筆者: Alastair Aitchison
		* Web サイト: http://alastaira.wordpress.com
		* 日付: 2011 年 4 月 15 日
		* 
		* 説明:  
		* この JavaScript ファイルは、ヒートマップの
		* オーバーレイを Bing Maps v7 コントロールで追加するために使用できるアルゴリズムを提供します。ヒートマップの輝度と温度のパレット
		* は容易にカスタマイズが可能な設計になっています。
		*
		* 要件: 
		* ヒートマップ レイヤーそのものはクライアント側で動的に、
		* HTML5 <canvas> 要素を使用して作成されるため、この要素をサポートする
		* ブラウザーが必要になります。IE9、Firefox 3.6/4、および 
		* Chrome 10 の各種ブラウザーが検証済みです。動作する他のブラウザー、または
		* 動作しないブラウザーについて確認された場合は、ぜひご連絡ください。
		
		* 用途: 
		* HeatMapLayer コンストラクターの要件: 
		* - マップ オブジェクトへの参照
		* - 配列または Microsoft.Maps.Location 項目
		* - レイヤーの外観をカスタマイズするためのオプションのパラメーター
		*  (Radius、Unit、Intensity、ColourGradient)、およびコールバック関数
		*
		*/
		
		var HeatMapLayer = function (map, locations, options) {
		
		    /* プライベート プロパティ */
		    var _map = map,
		      _canvas,
		      _temperaturemap,
		      _locations = [],
		      _viewchangestarthandler,
		      _viewchangeendhandler;
		
		    // 既定のオプションを設定します
		    var _options = {
		        // 各ヒート ポイントの中心の不透明度
		        intensity: 0.5,
		
		        // 各ヒート ポイントが影響を及ぼす範囲
		        radius: 1000,
		
		        // 範囲は絶対ピクセル値とメートルのどちらか
		        unit:'meters',
		
		        // マップ上でグラデーションによって表される温度の色
		        colourgradient: {
		            "0.00": 'rgba(255,0,255,20)',  // マゼンタ
		            "0.25": 'rgba(0,0,255,40)',    // 青
		            "0.50": 'rgba(0,255,0,80)',    // 緑
		            "0.75": 'rgba(255,255,0,120)', // 黄
		            "1.00": 'rgba(255,0,0,150)'    // 赤
		        },
		
		        // ヒートマップ レイヤーの再描画後に実行されるコールバック関数 
		        callback:null
		    };
		
		    /* プライベート メソッド */
		    function _init() {
		        var _mapDiv = _map.getRootElement();
		
		        if (_mapDiv.childNodes.length >= 3 && _mapDiv.childNodes[2].childNodes.length >= 2) {
		            // キャンバスの要素を作成します
		            _canvas = document.createElement('canvas');
		            _canvas.style.position = 'relative';
		
		            var container = document.createElement('div');
		            container.style.position = 'absolute';
		            container.style.left = '0px';
		            container.style.top = '0px';
		            container.appendChild(_canvas);
		
		            _mapDiv.childNodes[2].childNodes[1].appendChild(container);
		
		            // コンストラクターに渡されたオプションで既定を上書きします
		            _setOptions(options);
		
		            // 位置データの配列を読み込みます
		            _setPoints(locations);
		
		            // 指定されたカラー ストップから色のグラデーションを作成します
		            _temperaturemap = _createColourGradient(_options.colourgradient);
		
		            // イベント ハンドラーを接続してヒートマップのキャンバスを再描画します
		            _viewchangestarthandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangestart', _clearHeatMap);
		            _viewchangeendhandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangeend', _createHeatMap);
		
		            _createHeatMap();
		
		            delete _init;
		        } else {
		            setTimeout(_init, 100);
		        }
		    }
		
		    // ヒート マップをリセットします
		    function _clearHeatMap() {
		        var ctx = _canvas.getContext("2d");
		        ctx.clearRect(0, 0, _canvas.width, _canvas.height);
		    }
		
		    // 初期化時に指定されたカラー ストップから色のグラデーションを作成します
		    function _createColourGradient(colourstops) {
		        var ctx = document.createElement('canvas').getContext('2d');
		        var grd = ctx.createLinearGradient(0, 0, 256, 0);
		        for (var c in colourstops) {
		            grd.addColorStop(c, colourstops[c]);
		        }
		        ctx.fillStyle = grd;
		        ctx.fillRect(0, 0, 256, 1);
		        return ctx.getImageData(0, 0, 256, 1).data;
		    }
		
		    // 輝度のマップに色のグラデーションを適用します
		    function _colouriseHeatMap() {
		        var ctx = _canvas.getContext("2d");
		        var dat = ctx.getImageData(0, 0, _canvas.width, _canvas.height);
		        var pix = dat.data; // pix は 4 バイトのデータと高さおよび幅を乗算したデータ (RGBA) が含まれた CanvasPixelArray 
		        for (var p = 0, len = pix.length; p < len;) {
		            var a = pix[p + 3] * 4; // このピクセルのアルファを取得します
		            if (a != 0) { // プロットするデータが存在する場合
		                pix[p] = _temperaturemap[a]; // このアルファに対応するグラデーションの赤の値を設定します
		                pix[p + 1] = _temperaturemap[a + 1]; // アルファに基づいて緑の値を設定します
		                pix[p + 2] = _temperaturemap[a + 2]; // アルファに基づいて青の値を設定します
		            }
		            p += 4; // 次のピクセルに移動します
		        }
		        ctx.putImageData(dat, 0, 0);
		    }
		
		    // 渡す任意のオプションを設定します
		    function _setOptions(options) {
		        for (attrname in options) {
		            _options[attrname] = options[attrname];
		        }
		    }
		
		    // Microsoft.Maps.Locations の配列からヒートマップ ポイントを設定します  
		    function _setPoints(locations) {
		        _locations = locations;
		    }
		
		    // ヒートマップを描画するメインのメソッド
		    function _createHeatMap() {
		        // キャンバスがマップの現在の寸法に一致することを確認します
		        // これはキャンバスのリセットにも影響を及ぼします
		        _canvas.height = _map.getHeight();
		        _canvas.width = _map.getWidth();
		
		        _canvas.style.top = -_canvas.height / 2 + 'px';
		        _canvas.style.left = -_canvas.width / 2 + 'px';
		
		        // 各ヒートポイントのピクセルの範囲を現在のマップの縮尺で計算します
		        if (_options.unit == "pixels") {
		            radiusInPixel = _options.radius;
		        } else {
		            radiusInPixel = _options.radius / _map.getMetersPerPixel();
		        }
		
		        var ctx = _canvas.getContext("2d");
		
		        // 緯度と経度をピクセル位置に変換します
		        var pixlocs = _map.tryLocationToPixel(_locations, Microsoft.Maps.PixelReference.control);
		        var shadow = 'rgba(0, 0, 0, ' + _options.intensity + ')';
		        var mapWidth = 256 * Math.pow(2, _map.getZoom());
		
		        // 各位置をループして輝度マップを作成します
		        for (var i = 0, len = pixlocs.length; i < len; i++) {
		            var x = pixlocs[i].x;
		            var y = pixlocs[i].y;
		
		            if (x < 0) {
		                x += mapWidth * Math.ceil(Math.abs(x / mapWidth));
		            }
		
		            // この地点を中心とする放射状のグラデーションを作成します
		            var grd = ctx.createRadialGradient(x, y, 0, x, y, radiusInPixel);
		            grd.addColorStop(0.0, shadow);
		            grd.addColorStop(1.0, 'transparent');
		
		            // ヒートポイントをキャンバスに描画します
		            ctx.fillStyle = grd;
		            ctx.fillRect(x - radiusInPixel, y - radiusInPixel, 2 * radiusInPixel, 2 * radiusInPixel);
		        }
		
		        // 輝度のマップに指定色のグラデーションを適用します
		        _colouriseHeatMap();
		
		        // 指定されている場合はコールバック関数を呼び出す
		        if (_options.callback) {
		            _options.callback();
		        }
		    }
		
		    /* パブリック メソッド */
		
		    this.Show = function () {
		        if (_canvas) {
		            _canvas.style.display = '';
		        }
		    };
		
		    this.Hide = function () {
		        if (_canvas) {
		            _canvas.style.display = 'none';
		        }
		    };
		
		    // 輝度、範囲、色のグラデーションなどのオプションを設定します
		    this.SetOptions = function (options) {
		        _setOptions(options);
		    }
		
		    // ヒートマップが作成される Microsoft.Maps.Locations の配列を設定します
		    this.SetPoints = function (locations) {
		        // 既存のヒートマップ レイヤーをリセットします
		        _clearHeatMap();
		        // 新しい位置セットを渡します
		        _setPoints(locations);
		        // レイヤーを再作成します
		        _createHeatMap();
		    }
		
		    // DOM のヒートマップ レイヤーを削除します
		    this.Remove = function () {
		        _canvas.parentNode.parentNode.removeChild(_canvas.parentNode);
		
		        if (_viewchangestarthandler) { Microsoft.Maps.Events.removeHandler(_viewchangestarthandler); }
		        if (_viewchangeendhandler) { Microsoft.Maps.Events.removeHandler(_viewchangeendhandler); }
		
		        _locations = null;
		        _temperaturemap = null;
		        _canvas = null;
		        _options = null;
		        _viewchangestarthandler = null;
		        _viewchangeendhandler = null;
		    }
		
		    // 初期化のルーチンを呼び出します
		    _init();
		};
		
		// Module Loaded メソッドを呼び出します
		Microsoft.Maps.moduleLoaded('HeatMapModule');


**tweetStream.js を追加するには**

1. **ソリューション エクスプローラー**で、**TweetSentimentWeb** を展開します。
2. **[スクリプト]** を右クリックし、**[追加]**、**[JavaScript ファイル]** の順にクリックします。
3. [項目名] に、「**twitterStream.js**」と入力します。
4. 以下のコードをコピーして、ファイルに貼り付けます。

		var liveTweetsPos = [];
		var liveTweets = [];
		var liveTweetsNeg = [];
		var map;
		var heatmap;
		var heatmapNeg;
		var heatmapPos;
		
		function initialize() {
		    // マップを初期化する
		    var options = {
		        credentials:"AvFJTZPZv8l3gF8VC3Y7BPBd0r7LKo8dqKG02EAlqg9WAi0M7la6zSIT-HwkMQbx",
		        center:new Microsoft.Maps.Location(23.0, 8.0),
		        mapTypeId:Microsoft.Maps.MapTypeId.ordnanceSurvey,
		        labelOverlay:Microsoft.Maps.LabelOverlay.hidden,
		        zoom: 2.5
		    };
		    var map = new Microsoft.Maps.Map(document.getElementById('map_canvas'), options);
		
		    // 肯定、中立、否定の各レイヤーのヒートマップ オプション
		
		    var heatmapOptions = {
		        // 各ヒート ポイントの中心の不透明度
		        intensity: 0.5,
		
		        // 各ヒート ポイントが影響を及ぼす範囲
		        radius: 15,
		
		        // 範囲は絶対ピクセル値とメートルのどちらか
		        unit:'pixels'
		    };
		
		    var heatmapPosOptions = {
		        // 各ヒート ポイントの中心の不透明度
		        intensity: 0.5,
		
		        // 各ヒート ポイントが影響を及ぼす範囲
		        radius: 15,
		
		        // 範囲は絶対ピクセル値とメートルのどちらか
		        unit:'pixels',
		
		        colourgradient: {
		            0.0: 'rgba(0, 255, 255, 0)',
		            0.1: 'rgba(0, 255, 255, 1)',
		            0.2: 'rgba(0, 255, 191, 1)',
		            0.3: 'rgba(0, 255, 127, 1)',
		            0.4: 'rgba(0, 255, 63, 1)',
		            0.5: 'rgba(0, 127, 0, 1)',
		            0.7: 'rgba(0, 159, 0, 1)',
		            0.8: 'rgba(0, 191, 0, 1)',
		            0.9: 'rgba(0, 223, 0, 1)',
		            1.0: 'rgba(0, 255, 0, 1)'
		        }
		    };
		
		    var heatmapNegOptions = {
		        // 各ヒート ポイントの中心の不透明度
		        intensity: 0.5,
		
		        // 各ヒート ポイントが影響を及ぼす範囲
		        radius: 15,
		
		        // 範囲は絶対ピクセル値とメートルのどちらか
		        unit:'pixels',
		
		        colourgradient: {
		            0.0: 'rgba(0, 255, 255, 0)',
		            0.1: 'rgba(0, 255, 255, 1)',
		            0.2: 'rgba(0, 191, 255, 1)',
		            0.3: 'rgba(0, 127, 255, 1)',
		            0.4: 'rgba(0, 63, 255, 1)',
		            0.5: 'rgba(0, 0, 127, 1)',
		            0.7: 'rgba(0, 0, 159, 1)',
		            0.8: 'rgba(0, 0, 191, 1)',
		            0.9: 'rgba(0, 0, 223, 1)',
		            1.0: 'rgba(0, 0, 255, 1)'
		        }
		    };
		
		    // Client Side HeatMap モジュールの登録と読み込み
		    Microsoft.Maps.registerModule("HeatMapModule", "scripts/heatmap.js");
		    Microsoft.Maps.loadModule("HeatMapModule", {
		        callback:function () {
		            // 肯定、中立、否定の各ツイートにヒートマップ レイヤーを作成
		            heatmapPos = new HeatMapLayer(map, liveTweetsPos, heatmapPosOptions);
		            heatmap = new HeatMapLayer(map, liveTweets, heatmapOptions);
		            heatmapNeg = new HeatMapLayer(map, liveTweetsNeg, heatmapNegOptions);
		        }
		    });
		
		    $("#searchbox").val("xbox");
		    $("#searchBtn").click(onsearch);
		    $("#positiveBtn").click(onPositiveBtn);
		    $("#negativeBtn").click(onNegativeBtn);
		    $("#neutralBtn").click(onNeutralBtn);
		    $("#neutralBtn").button("toggle");
		}
		
		function onsearch() {
		    var uri = 'api/tweets?query=';
		    var query = $('#searchbox').val();
		    $.getJSON(uri + query)
		        .done(function (data) {
		            liveTweetsPos = [];
		            liveTweets = [];
		            liveTweetsNeg = [];
		
		            // 成功すると、"data" にツイートのリストが含まれます。
		            $.each(data, function (key, item) {
		                addTweet(item);
		            });
		
		            if (!$("#neutralBtn").hasClass('active')) {
		                $("#neutralBtn").button("toggle");
		            }
		            onNeutralBtn();
		        })
		        .fail(function (jqXHR, textStatus, err) {
		            $('#statustext').text('Error:' + err);
		        });
		}
		
		function addTweet(item) {
		    // ヒート マップの配列にツイートを追加します。
		    var tweetLocation = new Microsoft.Maps.Location(item.Latitude, item.Longtitude);
		    if (item.Sentiment > 0) {
		        liveTweetsPos.push(tweetLocation);
		    } else if (item.Sentiment < 0) {
		        liveTweetsNeg.push(tweetLocation);
		    } else {
		        liveTweets.push(tweetLocation);
		    }
		}
		
		function onPositiveBtn() {
		    if ($("#neutralBtn").hasClass('active')) {
		        $("#neutralBtn").button("toggle");
		    }
		    if ($("#negativeBtn").hasClass('active')) {
		        $("#negativeBtn").button("toggle");
		    }
		
		    heatmapPos.SetPoints(liveTweetsPos);
		    heatmapPos.Show();
		    heatmapNeg.Hide();
		    heatmap.Hide();
		
		    $('#statustext').text('Tweets:' + liveTweetsPos.length + "   " + getPosNegRatio());
		}
		
		function onNeutralBtn() {
		    if ($("#positiveBtn").hasClass('active')) {
		        $("#positiveBtn").button("toggle");
		    }
		    if ($("#negativeBtn").hasClass('active')) {
		        $("#negativeBtn").button("toggle");
		    }
		
		    heatmap.SetPoints(liveTweets);
		    heatmap.Show();
		    heatmapNeg.Hide();
		    heatmapPos.Hide();
		
		    $('#statustext').text('Tweets:' + liveTweets.length + "   " + getPosNegRatio());
		}
		
		function onNegativeBtn() {
		    if ($("#positiveBtn").hasClass('active')) {
		        $("#positiveBtn").button("toggle");
		    }
		    if ($("#neutralBtn").hasClass('active')) {
		        $("#neutralBtn").button("toggle");
		    }
		
		    heatmapNeg.SetPoints(liveTweetsNeg);
		    heatmapNeg.Show();
		    heatmap.Hide();;
		    heatmapPos.Hide();;
		
		    $('#statustext').text('Tweets:' + liveTweetsNeg.length + "\t" + getPosNegRatio());
		}
		
		function getPosNegRatio() {
		    if (liveTweetsNeg.length == 0) {
		        return "";
		    }
		    else {
		        var ratio = liveTweetsPos.length / liveTweetsNeg.length;
		        var str = parseFloat(Math.round(ratio * 10) / 10).toFixed(1);
		        return "Positive/Negative Ratio:" + str;
		    }
		}


**layout.cshtml を変更するには**

1. **ソリューション エクスプローラー**で、**[TweetSentimentWeb]**、**[ビュー]**、**[共有]** の順に展開し、**[Layout.cshtml]** をダブルクリックします。
2. 次のコンテンツに置き換えます。

		<!DOCTYPE html>
		<html>
		<head>
		    <meta charset="utf-8" />
		    <meta name="viewport" content="width=device-width, initial-scale=1.0">
		    <title>@ViewBag.Title</title>
		    @Styles.Render("~/Content/css")
		    @Scripts.Render("~/bundles/modernizr")
		    <!-- Bing Maps -->
		    <script type="text/javascript" src="http://ecn.dev.virtualearth.net/mapcontrol/mapcontrol.ashx?v=7.0&mkt=en-gb"></script>
		    <!-- Spatial Dashboard JavaScript -->
		    <script src="~/Scripts/twitterStream.js" type="text/javascript"></script>
		</head>
		<body onload="initialize()">
		    <div class="navbar navbar-inverse navbar-fixed-top">
		        <div class="container">
		            <div class="navbar-header">
		                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                </button>
		            </div>
		            <div class="navbar-collapse collapse">
		                <div class="row">
		                    <ul class="nav navbar-nav col-lg-5">
		                        <li class="col-lg-12">
		                            <div class="navbar-form">
		                                <input id="searchbox" type="search" class="form-control">
		                                <button type="button" id="searchBtn" class="btn btn-primary">Go</button>
		                            </div>
		                        </li>
		                    </ul>
		                    <ul class="nav navbar-nav col-lg-7">
		                        <li>
		                            <div class="navbar-form">
		                                <div class="btn-group" data-toggle="buttons-radio">
		                                    <button type="button" id="positiveBtn" class="btn btn-primary">Positive</button>
		                                    <button type="button" id="neutralBtn" class="btn btn-primary">Neutral</button>
		                                    <button type="button" id="negativeBtn" class="btn btn-primary">Negative</button>
		                                </div>
		                            </div>
		                        </li>
		                        <li><span id="statustext" class="navbar-text"></span></li>
		                    </ul>
		                </div>
		            </div>
		        </div>
		    </div>
		    <div class="map_container">
		        @RenderBody()
		    </div>
		    @Scripts.Render("~/bundles/jquery")
		    @Scripts.Render("~/bundles/bootstrap")
		    @RenderSection("scripts", required:false)
		</body>
		</html>



**Index.cshtml を変更するには**

1. **ソリューション エクスプローラー**で、**[TweetSentimentWeb]**、**[ビュー]**、**[ホーム]** の順に展開し、**[Index.cshtml]** をダブルクリックします。
2. 次のコンテンツに置き換えます。

		@{
		    ViewBag.Title = "Tweet Sentiment";
		}
		
		<div class="map_container">
		    <div id="map_canvas"/>
		</div>

**site.css ファイルを変更するには**

1. **ソリューション エクスプローラー**で、**[TweetSentimentWeb]**、**[コンテンツ]** の順に展開し、**[Site.css]**. をダブルクリックします。
2. ファイルに、以下のコードを追加します。
		
		/* make container, and thus map, 100% width */
		.map_container {
			width: 100%;
			height: 100%;
		}
		
		#map_canvas{
		  height:100%;
		}
		
		#tweets{
		  position:absolute;
		  top:60px;
		  left:75px;
		  z-index:1000;
		  font-size:30px;
		}

**global.asax ファイルを変更するには**

1. **ソリューション エクスプローラー**で、**[TweetSentimentWeb]** を展開し、**[Global.asax]** をダブルクリックします。
2. 次の using ステートメントを追加します。

		using System.Web.Http;

2. 以下の行を **Application_Start()** 関数内に追加します。

		// API ルートの登録
		GlobalConfiguration.Configure(WebApiConfig.Register);
  
	API ルートの登録を変更し、Web API コントローラーが MVC アプリケーション内で動作するようにします。

**Web アプリケーションを実行するには**

1. ストリーミング サービス コンソール アプリケーションが引き続き実行中であることを確認します。実行中の場合には、リアルタイムで変化を確認できます。
2. **F5** を押して、Web アプリケーションを実行します。

	![hdinsight.hbase.twitter.sentiment.bing.map][img-bing-map]
2. テキスト ボックスにキーワードを入力し、**[検索]** をクリックします。HBase テーブルで収集したデータによっては、キーワードが検出できない場合もあります。「love」、「xbox」、「playstation」などの一般的なキーワードを試してください。 
3. **[肯定]**、**[中立]**、**[否定]** を切り替えて、対象のセンチメントを比較します。
4. 別の時間帯にストリーミング サービスを実行し、同じキーワードを検索して結果を比較してください。

 
Azure Web サイトにこのアプリケーションをデプロイすることもできます。手順については、「[Azure Web サイトと ASP.NET の使用][website-get-started]」を参照してください。
 
##<a id="nextsteps"></a>次のステップ

このチュートリアルでは、ツイートを取得する方法、ツイートのセンチメントを分析する方法、センチメント データを HBase に保存する方法、およびリアルタイムの Twitter センチメント データを Bing マップに表示する方法について学習しました。詳細については、次を参照してください。

- [HDInsight で Hadoop 2.4 を使用する][hdinsight-get-started]
- [HDInsight での Hadoop を使用した Twitter データの分析][hdinsight-analyze-twitter-data]
- [HDInsight を使用したフライト遅延データの分析][hdinsight-analyze-flight-delay-data]
- [HDInsight 用 C# Hadoop ストリーミング プログラムの開発][hdinsight-develop-streaming]
- [Develop Java MapReduce program for HDInsight (HDInsight での Hadoop 用 Java MapReduce プログラムの開発)][hdinsight-develop-mapreduce]


[hbase-get-started]: ../hdinsight-hbase-get-started/
[website-get-started]: ../web-sites-dotnet-get-started/



[img-app-arch]: ./media/hdinsight-hbase-analyze-twitter-sentiment/AppArchitecture.png
[img-twitter-app]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterApp.png
[img-streaming-service]: ./media/hdinsight-hbase-analyze-twitter-sentiment/StreamingService.png
[img-bing-map]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterSentimentBingMap.png



[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/
[hdinsight-analyze-twitter-data]: ../hdinsight-analyze-twitter-data/
[hdinsight-hbase-get-started]: ../hdinsight-hbase-get-started/




[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/ja-jp/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell
[powershell-script]: http://technet.microsoft.com/ja-jp/library/ee176949.aspx

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage/#powershell
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/

