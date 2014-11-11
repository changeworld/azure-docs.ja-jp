<properties linkid="manage-services-hdinsight-recommendation-engine-using-mahout" urlDisplayName="Hadoop Recommendation Engine" pageTitle="Hadoop recommendation engine (.NET) | Azure" metaKeywords="Azure Apache Mahout, Azure recommendation example, Azure recommendation tutorial, Azure recommendation engine" description="A tutorial that teaches how to use the Apache Mahout recommendation engine with Azure to create song suggestions based on listening habits." disqusComments="1" umbracoNaviHide="1" title="Simple recommendation engine using Apache Mahout" authors="jgao" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao" />

# Apache Mahout を使用した単純なリコメンデーション エンジン

Apache Mahout™ は、スケーラブルな機械学習アプリケーションのために構築された機械学習ライブラリです。Mahout などのリコメンデーション エンジンは、現在使用されている機械学習アプリケーションの中で最も一般的なものであり、マーケティング分野で盛んに利用されています。

Apache Mahout にはアイテムベースの協調フィルタリングが組み込まれています。この手法はリコメンデーション データ マイニングで広く用いられています。Amazon.com によって開発されたアイテムベースの協調フィルタリングは、ユーザーの嗜好に関するデータを分析してアイテム間の嗜好の関連性を見出し、同様のグループに属するユーザーの好みを予測しようというものです。

このチュートリアルでは、[Million Song Dataset][Million Song Dataset] サイトから[データセット][データセット]をダウンロードし、ユーザーの過去の再生傾向に基づいてリコメンデーションを作成します。

学習内容:

-   リコメンデーション エンジンを使用する方法

このチュートリアルで学習する内容は次のとおりです。

1.  [セットアップと構成][セットアップと構成]
2.  [データの調査と書式設定][データの調査と書式設定]
3.  [Mahout のインストール][Mahout のインストール]
4.  [Mahout ジョブの実行][Mahout ジョブの実行]

## <a name="setup"></a>セットアップと構成

このチュートリアルは、Azure と HDinsight プレビューのセットアップが完了しており、サンプルを実行できる HDInsight クラスターが既に作成されていることを前提としています。これらの準備が整っていない場合は、「[Azure HDInsight の概要][Azure HDInsight の概要]」チュートリアルを参照してセットアップしてください。

## <a name="segment1"></a>データの調査と書式設定

このサンプルでは、特定の楽曲に対するユーザーの嗜好を調査します。ユーザーが楽曲を再生した回数によって、その楽曲に対するユーザーの嗜好度を判断します。嗜好データから何らかのパターンを導き出せば、過去に示した音楽の好みに基づいてそのユーザーの今後の嗜好を予測できます。「[Echo Nest Taste Profile Subset][Million Song Dataset]」Web ページの「**説明**」セクションで、このデータセットのサンプルを参照できます。

![Echo Nest 嗜好データ サブセット][Echo Nest 嗜好データ サブセット]

### Million Song Dataset のサンプル データ

このデータセットを Mahout で使用するには、次の 2 つの操作を行う必要があります。

1.  楽曲 ID とユーザー ID を整数値に変換します。
2.  この新しい値をランキングと共に、コンマ区切りファイルに保存します。

Visual Studio 2010 がインストールされていない場合は、この手順をスキップして「Mahout ジョブの実行」セクションへ進み、事前生成バージョンを取得してください。

まず Visual Studio 2010 を起動します。Visual Studio で、**[ファイル]、[新規作成]、[プロジェクト]** の順に選択します。**[インストールされたテンプレート]** の **[Visual C#]** で **[Windows]** カテゴリを選択し、一覧から **[コンソール アプリケーション]** を選択します。プロジェクト名として「ConvertToMahoutInput」と入力し、**[OK]** をクリックします。

![コンソール アプリケーションの作成][コンソール アプリケーションの作成]

### コンソール アプリケーションの作成

1.  アプリケーションが作成されたら、**Program.cs** ファイルを開き、次の静的メンバーを **Program** クラスに追加します。

        const char tab = '\u0009';
        static Dictionary<string, int> usersMapping = new Dictionary<string, int>();
        static Dictionary<string, int> songMapping = new Dictionary<string, int>(); 

2.  次に、`using System.IO;` ステートメントを追加し、**Main** メソッドに次のコードを入力します。

        var inputStream = File.Open(args[0], FileMode.Open);
        var reader = new StreamReader(inputStream);

        var outStream = File.Open("mInput.txt", FileMode.OpenOrCreate);
        var writer = new StreamWriter(outStream);

        var i = 1;

        var line = reader.ReadLine();
        while (!string.IsNullOrWhiteSpace(line))
        {
            i++;
            if (i > 5000)
            break;
            var outLine = line.Split(tab);

            int user = GetUser(outLine[0]);
            int song = GetSong(outLine[1]);

            writer.Write(user);
            writer.Write(',');
            writer.Write(song);
            writer.Write(',');
            writer.WriteLine(outLine[2]);

            line = reader.ReadLine();
        }

        Console.WriteLine("saved {0} lines to {1}", i, args[0]);

        reader.Close();
        writer.Close();

        SaveMapping(usersMapping, "users.csv");
        SaveMapping(songMapping, "mInput.csv");

        Console.WriteLine("Mapping saved");
        Console.ReadKey();

3.  ここで、ID を整数に変換するための **GetUser** 関数および **GetSong** 関数を作成します。

        static int GetUser(string user)
        {
            if (!usersMapping.ContainsKey(user))
                usersMapping.Add(user, usersMapping.Count + 1);

            return usersMapping[user];
        }

        static int GetSong(string song)
        {
            if (!songMapping.ContainsKey(song))
                songMapping.Add(song, songMapping.Count + 1);

            return songMapping[song];
        }

4.  最後に、ボット マッピング ディクショナリを .csv ファイルに保存するための SaveMapping メソッドを実装するユーティリティを作成します。

        static void SaveMapping(Dictionary<string, int> mapping, string fileName)
        {
            var stream = File.Open(fileName, FileMode.Create);
            var writer = new StreamWriter(stream);

            foreach (var key in mapping.Keys)
            {
                writer.Write(key);
                writer.Write(',');
                writer.WriteLine(mapping[key]);
            }

            writer.Close();
        }

5.  サンプル データを[このリンク][データセット]からダウンロードします。データをダウンロードしたら、**train\_triplets.txt.zip** を開いて **train\_triplets.txt** を抽出します。

    このユーティリティを実行する際、**train\_triplets.txt** の場所をコマンド ライン引数として指定します。そのためには、**ソリューション エクスプローラー**で **ConvertToMahoutInput** プロジェクトを右クリックし、**[プロパティ]** を選択します。プロジェクトのプロパティ ページで、左側の **[デバッグ]** タブを選択し、**[コマンド ライン引数]** ボックスに \<localpath\>train\_triplets.txt のパスを追加します。

    ![コマンド ライン引数の設定][コマンド ライン引数の設定]

### コマンド ライン引数の設定

-   **F5** キーを押して、プログラムを実行します。完了したら、プロジェクトを保存した場所から **bin\\Debug** フォルダーを開き、ユーティリティの出力を確認します。users.txt と mInput.txt が出力されています。

## <a name="segment2"></a>Mahout のインストール

-   HDInsight クラスター ポータルを開き、**[リモート デスクトップ]** アイコンをクリックします。

    ![クラスターの管理 アイコン][クラスターの管理 アイコン]

### [リモート デスクトップ] アイコン

既定では、HDInsight には Mahout が含まれていません。しかし、Mahout は Hadoop エコシステムの構成要素なので、[Mahout][Mahout] の Web サイトからダウンロードできます。最新バージョンは 0.7 ですが、この手順説明はバージョン 0.5 または 0.7 に対応しています。

1.  まず、[Mahout バージョン 0.7][Mahout バージョン 0.7] をローカル コンピューターにダウンロードします。

2.  次に、ダウンロードした Mahout をクラスターにコピーします。ローカル zip ファイルを選択してコピーし、それを Hadoop クラスターに貼り付けてください (Ctrl キーを押しながら V キーを押します)。

    ![Mahout のアップロード][Mahout のアップロード]

### Mahout をヘッドノードにコピー

1.  最後に、コピーした zip ファイルを右クリックして、Mahout を C:\\apps\\dist に展開します。これで mahout を C:\\apps\\dist\\mahout-distriution-0.7 にインストールできました。

2.  操作を簡単にするため、フォルダー名を「c:\\apps\\dist\\mahout-0.7」に変更します。

### <a name="segment3"></a>Mahout ジョブの実行

1.  **bin\\Debug** フォルダー内の **mInput.txt** ファイルを、リモート クラスター上の **c:\\** にコピーします。コピーした mInput.txt ファイルを展開します。前のセクションで説明したように、リモート RDP セッションにファイルをコピーするには、ローカル コンピューター上で目的のファイルをコピーし (Ctrl + C キー)、それを RDP セッション ウィンドウに貼り付けます (Ctrl + V キー)。

2.  リコメンデーションの生成対象となるユーザーの ID が含まれたファイルを作成します。そのためには、単一ユーザーの ID が記述された **users.txt** テキスト ファイルを **c:\\** に作成します。

<div class="dev-callout"> 
<b>注</b> 
<p>複数のユーザーを対象にしてリコメンデーションを生成することもできます。これには、各ユーザーの ID を別々の行に記述します。mInput.txt と users.txt の作成に問題がある場合は、作成済みのファイルをこの github <a href="https://github.com/wenming/BigDataSamples/tree/master/mahout">リポジトリ</a>からダウンロードできます。 

すべてのファイルを 1 つの <a href="https://github.com/wenming/BigDataSamples/archive/master.zip">zip ファイル</a> としてダウンロードするのが一番便利です。users.txt と mInput.txt を探し、これらをリモート クラスターの c:\ フォルダーにコピーします。</p> 
</div>

この時点で、Hadoop ターミナル ウィンドウを開き、users.txt と mInput.txt が保存されているフォルダーへ移動します。

![Mahout コマンド ウィンドウ][Mahout コマンド ウィンドウ]

### Hadoop コマンド ウィンドウ

1.  次に、**mInput.txt** と **users.txt** の両方を HDFS にアップロードします。そのためには、**Hadoop コマンド シェル**を開いて次のコマンドを実行します。

        hadoop dfs -copyFromLocal c:\mInput.txt input\mInput.txt
        hadoop dfs -copyFromLocal c:\users.txt input\users.txt

2.  2 つのファイルが HDFS へコピーされたことを確認します。

        hadoop fs -ls input/

    次のように表示されます。

        Found 2 items
        -rwxrwxrwx   1 writer supergroup      53322 2013-03-08 20:32 /user/writer/input/mInput.txt
        -rwxrwxrwx   1 writer supergroup        353 2013-03-08 20:33 /user/writer/input/users.txt

3.  これで、次のコマンドを使用して Mahout ジョブを実行できます。

        c:\apps\dist\mahout-0.7\bin>hadoop jar c:\Apps\dist\mahout-0.7\mahout-core-0.7-job.jar org.apache.mahout.cf.taste.hadoop.item.RecommenderJob -s SIMILARITY_COOCCURRENCE --input=input/mInput.txt --output=output --usersFile=input/users.txt

    リコメンデーション エンジンでは、その他多数の "distance" 関数を使用して、さまざまなユーザー特性要因を比較できます (SIMILARITY\_COOCCURRENCE、SIMILARITY\_LOGLIKELIHOOD、SIMILARITY\_TANIMOTO\_COEFFICIENT、SIMILARITY\_CITY\_BLOCK、SIMILARITY\_COSINE、SIMILARITY\_PEARSON\_CORRELATION、SIMILARITY\_EUCLIDEAN\_DISTANCE)。目的に応じて Similarity クラスを変更してください。このチュートリアルでは、Mahout のデータ技術については詳しく取り上げません。

4.  Mahout ジョブが数分間実行され、その後で出力ファイルが作成されます。次のコマンドを実行して、出力ファイルのローカル コピーを作成します。

        hadoop fs -copyToLocal output/part-r-00000 c:\output.txt

5.  **c:\\** ルート フォルダーから **output.txt** ファイルを開き、内容を調べます。このファイルの構造は次のとおりです。

        user    [song:rating,song:rating, ...]

6.  Mahout の他の機能をクラスター上で使用する場合は、Mahout.cmd のコピーを Mahout の bin ディレクトリに保存します。

## <a name="summary"></a>まとめ

現在、リコメンデーション エンジンは、ソーシャル ネットワーク サイト、オンライン ショッピング、ストリーミング メディアなど、さまざまなインターネット サイトで重要な役割を果たしています。Mahout はすぐに使用できるリコメンデーション エンジンです。使いやすく、多数の便利な機能を備えており、Hadoop 上での拡張も可能です。

## 次のステップ

この記事では Hadoop コマンド ラインを使用しましたが、HDInsight の対話型コンソールを使用してタスクを実行することもできます。詳細については、「ガイダンス: HDInsight の対話型 JavaScript コンソールと Hive コンソール [interactive-console]」を参照してください。

  [Million Song Dataset]: http://labrosa.ee.columbia.edu/millionsong/tasteprofile
  [データセット]: http://labrosa.ee.columbia.edu/millionsong/sites/default/files/challenge/train_triplets.txt.zip
  [セットアップと構成]: #setup
  [データの調査と書式設定]: #segment1
  [Mahout のインストール]: #Segment2
  [Mahout ジョブの実行]: #segment2
  [Azure HDInsight の概要]: /ja-jp/manage/services/hdinsight/get-started-hdinsight/
  [Echo Nest 嗜好データ サブセット]: ./media/hdinsight-hadoop-recommendation-engine/the-echo-nest-taste-profile-subset.png
  [コンソール アプリケーションの作成]: ./media/hdinsight-hadoop-recommendation-engine/creating-a-console-application.png
  [コマンド ライン引数の設定]: ./media/hdinsight-hadoop-recommendation-engine/setting-command-line-arguments.png
  [クラスターの管理 アイコン]: ./media/hdinsight-hadoop-recommendation-engine/the-manage-cluster-icon.png
  [Mahout]: http://mahout.apache.org/
  [Mahout バージョン 0.7]: http://www.apache.org/dyn/closer.cgi/mahout/
  [Mahout のアップロード]: ./media/hdinsight-hadoop-recommendation-engine/uploading-mahout.PNG
  [Mahout コマンド ウィンドウ]: ./media/hdinsight-hadoop-recommendation-engine/mahout-commandwindow.PNG
