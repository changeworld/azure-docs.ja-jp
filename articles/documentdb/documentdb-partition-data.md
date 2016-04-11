<properties 
	pageTitle="シャーディングによる DocumentDB のデータのパーティション分割と拡大縮小 | Microsoft Azure"      
	description="シャーディングという手法でデータを拡大縮小する方法を確認します。シャーディング、DocumentDB でのデータのパーティション分割の方法、ハッシュ パーティション分割と範囲パーティション分割を使用する状況について説明します。"         
	keywords="データの拡大縮小, シャード, シャーディング, documentdb, azure, Microsoft azure"
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/30/2016" 
	ms.author="arramac"/>

# Azure DocumentDB でのパーティション分割とスケーリング
[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) は、高速で予測可能なパフォーマンスの実現を支援し、アプリケーションの規模の拡大に合わせてシームレスにスケール設定できるように設計されています。この記事では、DocumentDB でのパーティション分割のしくみの概要と、DocumentDB コレクションを構成してアプリケーションを効率的に拡大/縮小する方法について説明します。

この記事を読むと、次の質問に回答できるようになります。

- Azure DocumentDB でパーティション分割はどのように機能するか。
- DocumentDB でパーティション分割をどのように構成できるか。
- パーティション キーとは何か、また、アプリケーションに適切なパーティション キーをどのように選択するか。

## DocumentDB でのパーティション分割

DocumentDB では、ミリ秒の応答時間順に任意の規模でスキーマのない JSON ドキュメントを格納し、クエリ実行できます。DocumentDB は、**コレクション**と呼ばれるデータを格納するためのコンテナーを提供します。コレクションは論理リソースであり、1 つ以上の物理パーティションまたはサーバーにまたがることができます。パーティション数は、ストレージのサイズやコレクションのプロビジョニング済みスループットに基づいて DocumentDB によって決定されます。DocumentDB のすべてのパーティションには固定された量の SSD を使用したストレージが関連付けられ、またパーティションは可用性を高めるためにレプリケートされます。パーティションの管理は Azure DocumentDB で完全に行われるため、複雑なコードを記述してパーティションを管理する必要がありません。DocumentDB コレクションは、ストレージとスループットに関して**事実上制限がありません**。

パーティション分割は、アプリケーションに対して完全に透過的です。DocumentDB では、すばやい読み取りと書き込み、SQL および LINQ クエリ、JavaScript ベースのトランザクション ロジック、一貫性レベル、単一のコレクション リソースへの REST API 呼び出しを介したきめ細やかなアクセス制御がサポートされます。サービスでは、複数のパーティション間でのデータの分散、適切なパーティションへのクエリ要求のルーティングが処理されます。

しくみ DocumentDB でコレクションを作成するときに、**パーティション キー プロパティ**の構成値を指定できることに注目してください。これは、DocumentDB が複数のサーバーまたはパーティション間にデータを分散するために使用できるドキュメント内の JSON プロパティ (またはパス) です。DocumentDB は、パーティション キーの値をハッシュし、ハッシュの結果を使用して、JSON ドキュメントの格納先となるパーティションを特定します。同じパーティション キーを持つすべてのドキュメントは、同じパーティションに格納されます。

たとえば、従業員とその部門に関するデータを DocumentDB に格納するアプリケーションがあるとします。パーティション キー プロパティとして `"department"` を選択して、部門別のデータをスケールアウトしてみましょう。DocumentDB 内のすべてのドキュメントは、パーティション キー値は同じで(`"Marketing`" など)、各ドキュメントに対して一意の必須の `"id"` プロパティを含む必要があります。コレクションに格納されたすべてのドキュメントには、パーティション キーと ID の一意の組み合わせ (`{ "Department": "Marketing", "id": "0001" }`、`{ "Department": "Marketing", "id": "0002" }`、`{ "Department": "Sales", "id": "0001" }` など) が必要です。言い換えると、パーティション キーと ID の複合プロパティはコレクションのプライマリ キーとなります。

### パーティション キー
パーティション キーの選択は、設計時に行う必要のある重要な決定事項の 1 つです。広範囲の値を持ち、場合によっては複数のパターン間に均等に分散されている可能性のある JSON プロパティ名を選択する必要があります。パーティション キーの選択が、アプリケーションのパフォーマンスにどのように影響するかを見てみましょう。

### パーティション分割とプロビジョニング済みスループット
DocumentDB は、予測可能なパフォーマンス向けに設計されています。コレクションを作成するとき、**要求ユニット (RU)/秒**でスループットを予約します。各要求には、操作で使用されるシステム リソースの量 (CPU、IO など) に比例する、要求ユニットの負担が割り当てられます。Session 一貫性での 1 KB のドキュメントの読み取りでは、1 要求ユニットを使用します。読み取りは、格納された項目の数や、同時に実行する同時要求の数にかかわらず 1 RU になります。サイズの大きいドキュメントでは、サイズに応じた、さらに多くの要求ユニットが必要になります。アプリケーションにサポートする必要のあるエンティティのサイズと読み取りの数がわかっている場合は、アプリケーションの読み取りに必要な正確な量のスループットをプロビジョニングできます。

DocumentDB では、ドキュメントの格納時に、パーティション キーの値に基づいて複数のパーティション間に均等にドキュメントを分散します。スループットも使用可能なパーティションに均等に分散されます (つまり、パーティションあたりのスループット = (コレクションあたりの総スループット) / (パーティション数))。

> [AZURE.NOTE] コレクションのフル スループットを達成するには、個別のパーティション キー値の数間に要求を均等に分散できるパーティション キーを選択する必要があります。

## 単一のパーティションとパーティション分割コレクション
DocumentDB では、単一のパーティションとパーティション分割コレクションの両方の作成がサポートされています。

- **パーティション分割コレクション**は複数のパーティションにまたがることができ、非常に大量のストレージとスループットに対応します。コレクションのパーティション キーを指定する必要があります。
- **単一のパーティション コレクション**には、すべてのコレクション データにわたってクエリ実行したり、トランザクションを実行するための低価格のオプションや機能が用意されています。また、単一のパーティションの拡張性とストレージの制限があります。これらのコレクションに対して、パーティション キーを指定する必要はありません。 

![DocumentDB でのパーティション分割コレクション][2]

単一のパーティションのコレクションは、大量のストレージまたはスループットを必要としないシナリオに適しています。単一のパーティションのコレクションには単一のパーティションに拡張性とストレージの制限 (最大 10 GB のストレージと最大 10,000 の要求ユニット/秒) があることに注意してください。

パーティション分割コレクションでは、非常に大量のストレージとスループットがサポートされます。ただし、既定のプランでは、最大 250 GB のストレージが格納され、1 秒あたり最大 250,000 の要求ユニットをスケールアップするように構成されます。コレクションあたりのストレージまたはスループットを増やす必要がある場合は、お使いのアカウントのストレージまたはスループットを増やすために [Azure サポート](documentdb-increase-limits)にお問い合わせください。

次の表に、単一パーティション コレクションとパーティション分割コレクションの違いを示します。

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>単一パーティション コレクション</strong></p></td>
            <td valign="top"><p><strong>パーティション分割コレクション</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>パーティション キー</p></td>
            <td valign="top"><p>なし</p></td>
            <td valign="top"><p>必須</p></td>
        </tr>
        <tr>
            <td valign="top"><p>ドキュメントのプライマリ キー</p></td>
            <td valign="top"><p>"ID"</p></td>
            <td valign="top"><p>複合キー &lt; パーティション キー > と "ID"</p></td>
        </tr>
        <tr>
            <td valign="top"><p>最小ストレージ容量</p></td>
            <td valign="top"><p>0 GB</p></td>
            <td valign="top"><p>0 GB</p></td>
        </tr>
        <tr>
            <td valign="top"><p>最大ストレージ容量</p></td>
            <td valign="top"><p>10 GB</p></td>
            <td valign="top"><p>制限なし (既定では 250 GB)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>最小スループット</p></td>
            <td valign="top"><p>400 要求ユニット/秒</p></td>
            <td valign="top"><p>10,000 要求ユニット/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>最大スループット</p></td>
            <td valign="top"><p>10,000 要求ユニット/秒</p></td>
            <td valign="top"><p>制限なし (既定では 250,000 要求ユニット/秒)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>API のバージョン</p></td>
            <td valign="top"><p>すべて</p></td>
            <td valign="top"><p>API 2015-12-16 およびそれ以降</p></td>
        </tr>
    </tbody>
</table>

## SDK の操作

Azure DocumentDB に、[REST API バージョン 2015-12-16](https://msdn.microsoft.com/library/azure/dn781481.aspx) による自動パーティション分割のサポートが追加されました。パーティション分割コレクションを作成するには、サポートされたいずれかの SDK プラットフォーム(.NET、Node.js、Java、Python) で SDK バージョン 1.6.0 以降をダウンロードする必要があります。

次のサンプルでは、スループット 1 秒あたり 20,000 要求ユニットのデバイスの製品利用統計情報データを格納するコレクションを作成する .NET スニペットを示しています。SDK は OfferThroughput 値を設定します (これにより REST API に `x-ms-offer-throughput` 要求ヘッダーが設定されます)。ここでは、パーティション キーとして `/deviceId` を設定します。パーティション キーの選択は、残りのコレクション メタデータ (名前、インデックス作成ポリシーなど) と共に保存されます。

このサンプルでは、以下のことがわかっているため `deviceId` を選択しています。(a) デバイスの数が多いため、書き込みをパーティション全体に均等に分散し、膨大な量のデータを取り込むためにデータベースの規模を変更する可能性がある。(b) デバイスの最新の読み取りのフェッチなど、多くの要求は単一の deviceId にスコープが制限され、単一のパーティションから取得される。

    DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
    await client.CreateDatabaseAsync(new Database { Id = "db" });

    // Collection for device telemetry. Here the JSON property deviceId will be used as the partition key to 
    // spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
    // sorting against any number or string property.
    DocumentCollection myCollection = new DocumentCollection();
    myCollection.Id = "coll";
    myCollection.PartitionKey.Paths.Add("/deviceId");

    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri("db"),
        myCollection,
        new RequestOptions { OfferThroughput = 20000 });
        
このメソッドでは、DocumentDB に対する REST API 呼び出しを行います。サービスにより、要求されたスループットに基づいた数のパーティションがプロビジョニングされます。では、DocumentDB にデータを挿入してみましょう。デバイスの新しい読み取りをコレクションに挿入するための、デバイスの読み取りデータを含むサンプル クラスと CreateDocumentAsync への呼び出しを次に示します。

    public class DeviceReading
    {
        [JsonProperty("id")]
        public string Id;

        [JsonProperty("deviceId")]
        public string DeviceId;

        [JsonConverter(typeof(IsoDateTimeConverter))]
        [JsonProperty("readingTime")]
        public DateTime ReadingTime;

        [JsonProperty("metricType")]
        public string MetricType;

        [JsonProperty("unit")]
        public string Unit;

        [JsonProperty("metricValue")]
        public double MetricValue;
      }

    // Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new DeviceReading
        {
            Id = "XMS-001-FE24C",
            DeviceId = "XMS-0001",
            MetricType = "Temperature",
            MetricValue = 105.00,
            Unit = "Fahrenheit",
            ReadingTime = DateTime.UtcNow
        });


パーティション キーと ID でドキュメントを読み込んで、更新してから、最後の手順としてパーティション キーと ID でドキュメントを削除してみましょう。読み取りに PartitionKey 値 (REST API の `x-ms-documentdb-partitionkey` 要求ヘッダーに対応) が含まれることに注意してください。

    // Read document. Needs the partition key and the ID to be specified
    Document result = await client.ReadDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new object[] { "XMS-0001" }});

    DeviceReading reading = (DeviceReading)(dynamic)result;

    // Update the document. Partition key is not required, again extracted from the document
    reading.MetricValue = 104;
    reading.ReadingTime = DateTime.UtcNow;

    await client.ReplaceDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      reading);

    // Delete document. Needs partition key
    await client.DeleteDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new object[] { "XMS-0001" } });

パーティション分割コレクション内のデータを照会する際に、DocumentDB により、フィルターに指定したパーティション キー値に対応するパーティションにクエリが自動ルーティングされます。たとえば、ここでのクエリはパーティション キー "XMS-0001" を含むパーティションのみにルーティングされます。

    // Query using partition key
    IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    	UriFactory.CreateDocumentCollectionUri("db", "coll"))
        .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");

次のクエリにはパーティション キー (DeviceId) にフィルターがなく、パーティションのインデックスに対してこのクエリが実行されるすべてのパーティションにファン アウトされます。パーティション全体に対して SDK にクエリを実行させるために、EnableCrossPartitionQuery (REST API 内の `x-ms-documentdb-query-enablecrosspartition`) を指定する必要があることに注意してください。

    // Query across partition keys
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true })
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);

また、単一のドキュメントでデバイスの集計や最新状態を管理するといった場合に、同じデバイス ID を持つドキュメントに対してアトミック トランザクションを実行することもできます。

    await client.ExecuteStoredProcedureAsync<DeviceReading>(
        UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
        "XMS-001-FE24C",
        new RequestOptions { PartitionKey = new PartitionKey("XMS-001") });

ここまで、基礎を学んできました。では、DocumentDB 内のパーティション キーを操作する際の重要な設計上の考慮事項をいくつか見てみましょう。

## パーティション分割の設計
パーティション キーの選択は、設計時に行う必要のある重要な決定事項の 1 つです。このセクションでは、コレクションのパーティション キーの選択に関わるトレードオフについていくつか説明します。

### トランザクション境界としてのパーティション キー
選択したパーティション キーは複数のパーティションにエンティティを分散するための要件に対しトランザクションの使用を可能にして、スケーラブルなソリューションを確保するため、必要性のバランスを取る必要があります。極端な例として、すべてのエンティティを 1 つのパーティションに格納することも可能ですが、この場合、ソリューションのスケーラビリティが制限される可能性があります。別の極端な例として、パーティション キーごとに 1 つのドキュメントを格納できますが、これは高い拡張性を実現するものの、ストアド プロシージャやトリガーを介したクロス ドキュメント トランザクションを使用できない可能性があります。理想的なパーティション キーとは、効率的なクエリを使用することができ、スケーラブルなソリューションを確保できるパーティションを持つものを指します。

### ストレージとパフォーマンスのボトルネックの回避 
また、書き込みが多くの個々の値全体に分散されるようにするプロパティを選択することが重要です。同じパーティション キーに対する要求は、単一のパーティションのスループットを超えることはできず、超えた場合は調整されます。そのため、アプリケーション内の **"ホット スポット"** にならないパーティション キーを選択することが重要です。また、同じパーティション キーを持つドキュメントのストレージ サイズの合計は、容量 10 GB を超えてはなりません。

### 適切なパーティション キーの例
次に、アプリケーションのパーティション キー選択のいくつかの例を示します。

* ユーザー プロファイルのバックエンドを実装する場合、ユーザー ID のパーティション キーを選択することをお勧めします。
* IoT データ (デバイス状態など) を格納する場合は、デバイス ID のパーティション キーを使用することをお勧めします。
* 時系列データのログ記録に DocumentDB を使用する場合は、タイムスタンプの日付部分がパーティション キーとして適しています。
* マルチ テナント アーキテクチャがある場合は、テナント ID のパーティション キーをお勧めします。

一部のユース ケース (前述の IoT、ユーザー プロファイルなど) では、パーティション キーはお使いの ID (ドキュメント キー) と同じになる場合があることに注意してください。その他のケース (時系列データなど) では、パーティション キーは ID と異なる可能性があります。

### パーティション分割とマルチテナント方式
DocumentDB を使用したマルチテナント アプリケーションを実装する場合、主に 2 つのパターンで DocumentDB でテナントを実装します。テナントごとに 1 つのパーティション キーを使用する方法と、テナントごとに 1 つのコレクションを使用する方法です。それぞれの長所と短所は次のとおりです。

* テナントあたり 1 つのパーティション キー: このモデルでは、テナントは、単一のコレクション内に併置されます。ただし、1 つのテナント内のドキュメントのクエリや挿入は、1 つのパーティションに対して実行されます。また、1 つのテナント内のすべてのドキュメント全体にトランザクション ロジックを実装できます。複数のテナントで 1 つのコレクションが共有されるため、各テナント用に余分なヘッドルームをプロビジョニングするのではなく、単一のコレクション内のテナントのリソースをプールして、ストレージとスループットのコストを削減できます。欠点は、テナントごとにパフォーマンスを分離できないことです。パフォーマンス/スループットの増加はコレクション全体に適用されますが、テナントごとに適用されません。
* テナントあたり 1 つのコレクション: 各テナントは独自のコレクションを持ちます。このモデルでは、テナントごとにパフォーマンスを予約できます。DocumentDB の新しい消費量ベースの価格モデルを使用することで、このモデルは、テナント数が少ないマルチテナント アプリケーションでより高いコスト効果を得られます。

また、結合型/階層型の手法を使用して、小規模なテナントを併置したり、大規模なテナントを自身のコレクションに移行したりすることもできます。

## 次のステップ
この記事では、Azure DocumentDB でのパーティション分割のしくみ、パーティション分割コレクションの作成方法、またアプリケーションに最適なパーティション キーの選択方法を説明しました。

-   [SDK](documentdb-sdk-dotnet.md) または [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) を使ってコーディングを開始しましょう。
-   詳しくは、[DocumentDB でのプロビジョニング済みスループット](documentdb-performance-levels.md)に関するページを参照してください。
-   アプリケーションでのパーティション分割の実行方法をカスタマイズする必要がある場合は、独自のクライアント側のパーティション分割の実装を採用できます。[クライアント側のパーティション分割のサポート](documentdb-sharding.md)に関するページを参照してください。

[1]: ./media/documentdb-partition-data/partitioning.png
[2]: ./media/documentdb-partition-data/single-and-partitioned.png

 

<!---HONumber=AcomDC_0330_2016------>