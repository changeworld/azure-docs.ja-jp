<properties linkid="manage-services-hdinsight-hbase-get-started-hdinsight-hadoop" urlDisplayName="Get Started" pageTitle="Get started using HBase with Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using HBase with Hadoop in HDInsight. learn how to created HBase tables and query them with Hive." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using HBase with Hadoop in HDInsight" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="bradsev"></tags>

# HDInsight での HBase と Hadoop の使用の概要

HBase は、ビッグ データのオンライン トランザクション処理を可能にする遅延時間の短い NoSQL データベースです。HBase は、管理されるクラスターとして、Azure 環境に統合されて提供されます。クラスターは、Azure BLOB ストレージにデータを直接格納するよう構成されるため、遅延時間が短くなり、パフォーマンスとコストを考慮した選択肢に大きな弾力性が生まれます。そのため、顧客は大規模なデータセットを処理する対話的な Web サイトを構築できます。これにより、何百万ものエンドポイントからセンサーのデータおよび利用統計情報のデータを格納するサービスを構築したり、Hadoop ジョブによりこれらのデータを分析したりすることができます。

このチュートリアルでは、HDInsight により HBase テーブルを作成および照会する方法について学習します。説明する手順は次のとおりです。

-   Azure ポータルを使用して HBase クラスターをプロビジョニングする方法
-   HBase シェルにアクセスするために RDP を有効にして使用する方法と、HBase シェルを使用して HBase サンプル テーブルを作成し、行を追加し、テーブルの行を一覧表示する方法
-   既存の HBase テーブルにマッピングする Hive テーブルを作成し、HiveQL を使用して HBase テーブルのデータを照会する方法
-   .NET SDK を使用して、新しい HBase テーブルを作成し、アカウントで HBase テーブルを一覧表示する方法。およびテーブルに行を追加し、テーブルから行を取得する方法

> [WACOM.NOTE] HBase は現在、(Hadoop 2.2.0 を基盤とする) HDInsight 上の HDInsight 3.0 クラスターと併用したプレビューでのみ使用できます。バージョン情報については、「[What's new in the Hadoop cluster versions provided by HDInsight? (HDInsight で提供される Hadoop クラスター バージョンの新機能)][]」を参照してください。

プレビュー期間は、HBase クラスターで使用したあらゆるデータの元バージョンのバックアップ コピーを、クラスターの外部に保存することをお勧めします。その理由は、将来のバージョンでデータベース ファイルの形式が変更される場合があり、プレビュー バージョンで使用している現在のデータ ファイルの形式がサポートされなくなるか、以降のバージョンにアップグレードできなくなる可能性があるためです。

**前提条件:**

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

-   Azure サブスクリプション。サブスクリプションの入手方法の詳細については、[購入オプション][]、[メンバー プラン][]、または[無料評価版][]に関するページを参照してください。
-   Azure ストレージ アカウント。詳しい手順については、「[ストレージ アカウントの作成方法][]」を参照してください。
-   Visual Studio のコピー。

**所要時間:** 30 分

## このチュートリアルの内容

-   [Azure ポータルで HBase クラスターをプロビジョニングする][]
-   [HBase シェルから HBase サンプル テーブルを作成する][]
-   [Hive を使用して HBase テーブルを照会する][]
-   [HBase C# API を使用して HBase テーブルを作成し、テーブルからデータを取得する][]
-   [まとめ][]

## <a name="create-hbase-cluster"></a>Azure ポータルで HBase クラスターをプロビジョニングする

このセクションでは、Azure ポータルを使用して HBase クラスターをプロビジョニングする方法について説明します。

**Azure ポータルで HDInsight クラスターをプロビジョニングするには**

1.  [Azure 管理ポータル][]にサインインします。

2.  左側にある **[HDInsight]** をクリックして、アカウント内のクラスターの状態を一覧表示し、左下にある **[+新規]** アイコンをクリックします。

    ![][]

3.  左から 2 番目の列にある [HDInsight] アイコンをクリックし、次の列の [HBase] オプションをクリックします。[クラスター名] および [クラスター サイズ] の値、ストレージ アカウントの名前、および新しい HBase クラスターのパスワードを指定します。

    ![][1]

4.  左下にあるチェック アイコンをクリックして、HBase クラスターを作成します。

## <a name="create-sample-table"></a>HBase シェルから HBase サンプル テーブルを作成する

このセクションでは、HBase シェルにアクセスするためにリモート デスクトップ プロトコル (RDP) を有効にして使用する方法と、HBase シェルを使用して HBase サンプル テーブルを作成し、行を追加し、テーブルの行を一覧表示する方法を説明します。

ここでは、最初のセクションで説明した手順を完了し、HBase クラスターが既に正しく作成されていることを前提としています。

**HBase クラスターに対する RDP 接続を有効にする**

1.  HDInsight クラスターに対するリモート デスクトップ接続を有効にするには、作成した HBase クラスターを選択して、**[構成]** タブをクリックします。ページの下部にある **[リモートを有効にする]** ボタンをクリックして、クラスターに対する RDP 接続を有効にします。
2.  **リモート デスクトップの構成**ウィザードで資格情報と有効期限を入力し、右下の丸で囲まれたチェック マークをクリックします (処理が完了するまで数分かかる場合があります)。
3.  HDInsight クラスターに接続するには、**[構成]** タブの下部にある **[接続]** ボタンをクリックします。

**HBase シェルを開く**

1.  RDP セッション内で、デスクトップの **[Hadoop コマンド ライン プロンプト]** ショートカットをクリックします。

2.  次のようにフォルダーを HBase ホーム ディレクトリに変更します。

        cd %HBASE_HOME%\bin

3.  次のように HBase シェルを開きます。

        hbase shell

**サンプル テーブルを作成し、データを追加、取得する**

1.  次のようにサンプル テーブルを作成します。

        create 'sampletable', 'cf1'

2.  次のように行をサンプル テーブルに追加します。

        put 'sampletable', 'row1', 'cf1:col1', 'value1'

3.  次のようにサンプル テーブルの行を一覧表示します。

        scan 'sampletable'

## <a name="hive-query"></a>Hive を使用して HBase テーブルを照会する

これで、HBase クラスターをプロビジョニングし、テーブルを作成したので、Hive を使用してテーブルを照会できます。このセクションでは、HBase テーブルにマッピングする Hive テーブルを作成し、作成した Hive テーブルを使用して HBase テーブルのデータを照会します。

**クラスターのダッシュボードを開くには**

1.  [Azure 管理ポータル][]にサインインします。
2.  左側ウィンドウの **[HDINSIGHT]** をクリックします。先のセクションで作成したクラスターなど、作成したクラスターが一覧表示されます。
3.  Hive ジョブを実行するクラスター名をクリックします。
4.  ページの下部にある **[クラスターの管理]** をクリックし、クラスターのダッシュボードを開きます。これにより、別のブラウザー タブで Web ページが開きます。
5.  Hadoop ユーザー アカウントとパスワードを入力します。デフォルトのユーザー名は **admin** で、パスワードはプロビジョニング処理中に入力したパスワードです。ダッシュボードは、次のようになります。

    ![][2]

**Hive クエリを実行するには**

1.  HBase テーブルにマッピングする Hive テーブルを作成するには、以下の HiveQL スクリプトを Hive コンソール ウィンドウに入力し、**[送信]** ボタンをクリックします。ここで、HBase シェルを使用して、参照するサンプル テーブルを作成したことを確認してから、このステートメントを実行します。

        SET hbase.zookeeper.quorum=zookeepernode0,zookeepernode1,zookeepernode2;  
        CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
        TBLPROPERTIES ('hbase.table.name' = 'sampletable');

2.  HBase のデータに対して Hive クエリを実行するには、以下の HiveQL スクリプトを Hive コンソール ウィンドウに入力し、**[送信]** ボタンをクリックします。

        SET hbase.zookeeper.quorum=zookeepernode0,zookeepernode1,zookeepernode2;
        SET hive.aux.jars.path=file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hive-hbase-handler-0.12.0.2.0.9.0-1677.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hbase-server-0.96.0.2.0.9.0-1677-hadoop2.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hbase-protocol-0.96.0.2.0.9.0-1677-hadoop2.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/htrace-core-2.01.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hbase-client-0.96.0.2.0.9.0-1677-hadoop2.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/guava-12.0.1.jar;
        SELECT count(*) FROM hbasesampletable;

3.  Hive クエリの結果を取得するには、ジョブが実行を終了するときに、**[ジョブ セッション]** ウィンドウで **[詳細の表示]** リンクをクリックします。

注: HBase シェル リンクにより、タブが **[HBase シェル]** に切り替えられます。

**出力ファイルを参照するには**

1.  クラスターのダッシュボードで、上部にある **[ファイル]** をクリックします。
2.  **[Templeton-Job-Status]** をクリックします。
3.  以前書き留めたジョブの開始時刻よりも少し後の最終変更時刻の GUID 番号をクリックします。この GUID をメモしておきます。これは、次のセクションで必要になります。
4.  **stdout** ファイルには、次のセクションで必要なデータが含まれています。必要な場合は、**stdout** をクリックして、このデータ ファイルのコピーをダウンロードできます。

## <a name="hbase-powershell"></a>HBase C# API を使用して HBase テーブルを作成し、テーブルからデータを取得する

Marlin は、REST API 上のシン レイヤーで、C\# の ProtoBuf を使用して HBase との対話を処理します。Marlin プロジェクトを github からダウンロードして、HBase .NET SDK を使用してプロジェクトをビルドする必要があります。

1.  [Marlin のプロジェクト ページ][]の [Download the Marlin project] に説明されているビルド手順に従います。プロジェクトをローカル ディレクトリに解凍します。

2.  Visual Studio でプロジェクトを開きます。**[ツール]** メニューの **[ライブラリ パッケージ マネージャー]** を選択して NuGet パッケージ マネージャーの管理ウィザードを開き、**[ソリューションの NuGet パッケージの管理]** を選択します。

    ![][3]

3.  右上の [オンライン] ボックスで protobuf-net を検索し、v2.0.0.68 をインストールします。**ソリューション エクスプローラー**で **Marlin** プロジェクトを右クリックし、**[ビルド]** を選択して、Marlin プロジェクトをビルドします。

4.  ビルドされた marlin.dll を取得して、C\# プロジェクトに追加します。

5.  次のように、クラスターの資格情報を使用して Marlin の新しいインスタンスを作成し、クラスター バージョンを取得します。

        var credentials = ClusterCredentials.Create("https://yourclustername.azurehdinsight.net/", "user", "password");
            var marlin = new Marlin(credentials);
        // retrieve the version as a test
        var version = marlin.GetVersion();
        Console.WriteLine(version);

6.  クラスターのテーブルを一覧表示するには、以下のコードを使用できます。

        var tables = marlin.ListTables();
        foreach(var tableName in tables.name) 
                Console.WriteLine(tableName);

7.  新しい HBase テーブルを作成するには、次のコードを使用します。

        var schema = new TableSchema();
        schema.name = "sampletable";
        schema.columns.Add(new ColumnSchema() { name = "cf1" });
        schema.columns.Add(new ColumnSchema() { name = "cf2" });
        marlin.CreateTable(schema);

8.  キーを使用して行を取得するには、テーブル名と行キーを指定して、行の値を取得します。

        var cells = marlin.GetCells("sampletable", "row1");
        var row = cells.rows[0];
            foreach(var val in row.values) 
            {
               Console.WriteLine(Encoding.UTF8.GetString(val.data));
            }

9.  データの新しい行を保存するには、以下のコードを使用できます。

        CellSet set = new CellSet();
        CellSet.Row row = new CellSet.Row() { key = BitConverter.GetBytes(1337) };
            var value = new Cell()
                    {
                        column = Encoding.UTF8.GetBytes("cf1:d"),
                        data = Encoding.UTF8.GetBytes("Hello World!")
                    };
            row.values.Add(value);
            set.rows.Add(row);
        marlin.StoreCells("sampletable", set);

## <a name="summary"></a>まとめ

このチュートリアルでは、HBase クラスターのプロビジョニング方法、テーブルの作成方法、および作成したテーブルのデータを HBase シェルから表示する方法について学習しました。また、Hive を使用して HBase テーブルのデータを照会する方法、および HBase C# API を使用して HBase テーブルを作成し、テーブルからデータを取得する方法についても学習しました。

  [What's new in the Hadoop cluster versions provided by HDInsight? (HDInsight で提供される Hadoop クラスター バージョンの新機能)]: ../hdinsight-component-versioning/
  [購入オプション]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [メンバー プラン]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [無料評価版]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [ストレージ アカウントの作成方法]: http://azure.microsoft.com/ja-jp/documentation/articles/storage-create-storage-account/
  [Azure ポータルで HBase クラスターをプロビジョニングする]: #create-hbase-cluster
  [HBase シェルから HBase サンプル テーブルを作成する]: #create-sample-table
  [Hive を使用して HBase テーブルを照会する]: #hive-query
  [HBase C# API を使用して HBase テーブルを作成し、テーブルからデータを取得する]: #hbase-powershell
  [まとめ]: #summary
  [Azure 管理ポータル]: https://manage.windowsazure.com/
  []: http://i.imgur.com/PmGynKZ.jpg
  [1]: http://i.imgur.com/ecxbB9K.jpg
  [2]: http://i.imgur.com/tMwXlj9.jpg
  [Marlin のプロジェクト ページ]: https://github.com/thomasjungblut/marlin
  [3]: http://i.imgur.com/hUNoJDJ.jpg
