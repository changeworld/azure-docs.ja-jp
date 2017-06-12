---
title: "Azure Cosmos DB での Change Feed サポートの使用 | Microsoft Docs"
description: "Azure Cosmos DB の Change Feed サポートを使用して、ドキュメントの変更を追跡し、トリガーなどのイベント ベースの処理を実行したり、キャッシュや分析システムを最新の状態に維持したりします。"
keywords: Change Feed
services: cosmos-db
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 03/23/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 8e0516585e2191caaef70bc973f027925df91bf6
ms.contentlocale: ja-jp
ms.lasthandoff: 05/31/2017


---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Azure Cosmos DB での Change Feed サポートの使用
[Azure Cosmos DB](../cosmos-db/introduction.md) は、グローバルにレプリケートされた柔軟な高速データベース サービスです。大量のトランザクション データや運用データを格納するために使用され、読み取りと書き込みで数ミリ秒 (1 桁) の予測可能な待機時間を実現します。 このため、IoT、ゲーム、小売、操作ログといったアプリケーションに最適です。 これらのアプリケーションの一般的な設計パターンでは、Azure Cosmos DB のデータの変更を追跡し、その変更内容に基づいて、具体化されたビューの更新、リアルタイム分析の実行、コールド ストレージへのデータのアーカイブ、特定のイベントに関する通知のトリガーなどのアクションを実行します。 Azure Cosmos DB の **Change Feed サポート**により、これらの各パターンに対応する効率的でスケーラブルなソリューションを構築できます。

Change Feed サポートを使用すると、Azure Cosmos DB コレクション内のドキュメントの一覧が、ドキュメントの更新日時順に並べ替えられて表示されます。 このフィードを利用すれば、コレクション内のデータに対する変更をリッスンし、以下のようなアクションを実行できます。

* ドキュメントが挿入または変更された場合に API の呼び出しをトリガーする
* 更新内容に対してリアルタイムの (ストリーム) 処理を実行する
* キャッシュ、検索エンジン、データ ウェアハウスとデータを同期する

Azure Cosmos DB での変更は保持され、非同期的に処理できます。また、1 つ以上のコンシューマーに分散させて並列処理することもできます。 Change Feed の API と、これらの API を使用してスケーラブルなリアルタイム アプリケーションを構築する方法を見ていきましょう。 この記事では、Azure Cosmos DB DocumentDB API を使用して地理空間データを操作する方法について説明します。 

![Azure Cosmos DB の Change Feed を使用してリアルタイム分析とイベント ドリブンのコンピューティング シナリオを強化](./media/change-feed/changefeed.png)

## <a name="use-cases-and-scenarios"></a>ユース ケースとシナリオ
Change Feed を使用すると、大量の書き込みが発生する大規模なデータセットを効率的に処理できるので、データセット全体に対してクエリを実行する代わりに、Change Feed を使用して変更を特定できます。 たとえば、次のタスクを効率よく実行できます。

* Azure Cosmos DB に格納されたデータで、キャッシュ、検索インデックス、データ ウェアハウスを更新する。
* アプリケーション レベルのデータの階層化とアーカイブを実装する ("ホット データ" を Azure Cosmos DB に格納し、"コールド データ" を [Azure Blob Storage](../storage/storage-introduction.md) または [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) にエイジアウトする)。
* [Apache Hadoop](run-hadoop-with-hdinsight.md) を使用してデータの一括分析を実装する。
* Azure Cosmos DB を使用して [Azure でラムダ パイプライン](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/)を実装する。 Azure Cosmos DB は、取り込みとクエリの両方を処理し、低 TCO でラムダ アーキテクチャを実装できる、スケーラブルなデータベース ソリューションを提供します。 
* パーティション スキームが異なる別の Azure Cosmos DB アカウントへの移行をダウンタイムなしに実行する。

**取り込みとクエリに対応する Azure Cosmos DB を使用したラムダ パイプライン:**

![取り込みとクエリに対応する Azure Cosmos DB ベースのラムダ パイプライン](./media/change-feed/lambda.png)

Azure Cosmos DB を使用すると、デバイス、センサー、インフラストラクチャ、アプリケーションからイベント データを受信して格納し、[Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md)、[Apache Storm](../hdinsight/hdinsight-storm-overview.md)、または [Apache Spark](../hdinsight/hdinsight-apache-spark-overview.md) を使用して、これらのイベントをリアルタイムで処理できます。 

Web やモバイル アプリ内で、お客様のプロファイル、設定、または場所への変更などのイベントを追跡し、[Azure Functions](../azure-functions/functions-bindings-documentdb.md) や [App Services](https://azure.microsoft.com/services/app-service/) を使用して各デバイスにプッシュ通知を送信するなど特定のアクションをトリガーできます。 たとえば、Azure Cosmos DB を使用してゲームを構築する場合、Change Feed を使用して完了したゲームのスコアに基づくリアルタイムのスコアボードを実装できます。

## <a name="how-change-feed-works-in-azure-cosmos-db"></a>Azure Cosmos DB での Change Feed のしくみ
Azure Cosmos DB では、Azure Cosmos DB コレクションの更新を増分的に読み取ることができます。 この Change Feed には、次の特長があります。

* 変更は Azure Cosmos DB 内で保持され、非同期的に処理できます。
* コレクション内のドキュメントへの変更は、Change Feed ですぐに利用できます。
* 各ドキュメントに対する変更は Change Feed に 1 回だけ反映されます。 変更ログには、特定のドキュメントの最新の変更のみが含まれます。 途中の変更は利用できない場合があります。
* Change Feed はパーティション キー値ごとに変更日時順に並べ替えられます。 パーティション キー値が異なる場合、順序は保証されません。
* 変更の同期は任意の時点から行うことが可能です。つまり、変更内容を利用できるデータ保持期間は固定されていません。
* 変更内容は、パーティション キーの範囲として利用できます。 この機能により、大規模コレクションの変更を複数のコンシューマーやサーバーで並行処理できるようになります。
* アプリケーションは、同じコレクションに対して複数の Change Feed を同時に要求できます。

Azure Cosmos DB の Change Feed はすべてのアカウントに対して既定で有効になっており、追加コストは発生しません。 Azure Cosmos DB の他の操作と同様に、書き込みリージョンや Change Feed から読み取る[読み取りリージョン](distribute-data-globally.md)で[プロビジョニング スループット](request-units.md)を使用できます。 Change Feed には、コレクション内のドキュメントに加えられた挿入操作や更新操作が含まれています。 削除の代わりに、ドキュメント内で「論理的な削除」フラグを設定することで削除をキャプチャできます。 または、[TTL 機能](time-to-live.md)からドキュメントの有限の有効期限 (24 時間など) を設定し、そのプロパティの値を使用して削除をキャプチャできます。 この場合は、TTL の有効期限よりも短い期間に含まれる変更を処理する必要があります。 Change Feed は、ドキュメント コレクション内の各パーティション キー範囲で利用できるため、1 人または複数のコンシューマーの間で分散し並列処理できます。 

![Azure Cosmos DB の Change Feed の分散処理](./media/change-feed/changefeedvisual.png)

次のセクションでは、Azure Cosmos DB REST API と SDK を使用して Change Feed にアクセスする方法について説明します。 .NET アプリケーションでは、Change Feed のイベントの処理に [Change Feed プロセッサ ライブラリ]()を使用することをお勧めします。

## <a id="rest-apis"></a>REST API と SDK の使用
Azure Cosmos DB では、**コレクション**と呼ばれるストレージとスループットの弾力性のあるコンテナーを提供します。 コレクション内のデータはスケーラビリティとパフォーマンスのために、[パーティション キー](partition-data.md)を使用して論理的にグループ化されます。 Azure Cosmos DB には、ID による検索 (Read/Get)、クエリ、読み取りフィード (スキャン) など、このデータにアクセスするためのさまざまな API が用意されています。 Change Feed は、DocumentDB の `ReadDocumentFeed` API に 2 つの新しい要求ヘッダーを設定することで取得でき、複数のパーティション キー範囲で並列処理できます。

### <a name="readdocumentfeed-api"></a>ReadDocumentFeed API
ReadDocumentFeed のしくみを簡単に説明します。 Azure Cosmos DB は、`ReadDocumentFeed` API を使用して、コレクション内のドキュメントのフィードの読み取りをサポートします。 たとえば、次の要求は、`serverlogs` コレクション内でドキュメントのページを返します。 

    GET https://mydocumentdb.documents.azure.com/dbs/smalldb/colls/serverlogs HTTP/1.1
    x-ms-date: Tue, 22 Nov 2016 17:05:14 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dgo7JEogZDn6ritWhwc5hX%2fNTV4wwM1u9V2Is1H4%2bDRg%3d
    Cache-Control: no-cache
    x-ms-consistency-level: Strong
    User-Agent: Microsoft.Azure.Documents.Client/1.10.27.5
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

結果は `x-ms-max-item-count` を使用して制限でき、読み取りは前回の応答で返された `x-ms-continuation` ヘッダーで要求を再送信することで再開できます。 単一のクライアントから実行する場合、`ReadDocumentFeed` はパーティション全体の結果を逐次に反復処理します。 

**ドキュメント フィードの逐次読み取り**

また、サポートされている [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) のいずれかを使用してドキュメントのフィードを取得できます。 たとえば次のスニペットは、.NET で ReadDocumentFeed を実行する方法を示します。

    FeedResponse<dynamic> feedResponse = null;
    do
    {
        feedResponse = await client.ReadDocumentFeedAsync(collection, new FeedOptions { MaxItemCount = -1 });
    }
    while (feedResponse.ResponseContinuation != null);

### <a name="distributed-execution-of-readdocumentfeed"></a>ReadDocumentFeed の分散実行
数テラバイト以上のデータを含んでいるか、大量の更新を取り込むコレクションでは、1 台のクライアント マシンにフィードの読み取りを直列処理させるのが現実的に難しい場合があります。 これらのビッグ データ シナリオをサポートするために、Azure Cosmos DB では、複数のクライアント リーダー/コンシューマーに `ReadDocumentFeed` 呼び出しを透過的に分散する API を提供します。 

**ドキュメント フィードの分散読み取り**

増分の変更をスケーラブルに処理できるように、Azure Cosmos DB はパーティション キー範囲に基づいた Change Feed API のスケールアウト モデルをサポートします。

* `ReadPartitionKeyRanges` 呼び出しを実行するコレクションのパーティション キー範囲の一覧を取得できます。 
* 各パーティション キー範囲では、`ReadDocumentFeed` を実行して範囲内のパーティション キーでドキュメントを読み取ることができます。

### <a name="retrieving-partition-key-ranges-for-a-collection"></a>コレクションのパーティション キー範囲を取得
コレクション内で `pkranges` リソースを要求すれば、パーティション キー範囲を取得できます。 次のような要求で `serverlogs` コレクションのパーティション キー範囲の一覧を取得できます。

    GET https://querydemo.documents.azure.com/dbs/bigdb/colls/serverlogs/pkranges HTTP/1.1
    x-ms-date: Tue, 15 Nov 2016 07:26:51 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dEConYmRgDExu6q%2bZ8GjfUGOH0AcOx%2behkancw3LsGQ8%3d
    x-ms-consistency-level: Session
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: querydemo.documents.azure.com

この要求では、パーティション キー範囲についてのメタデータを含む次の応答が返されます。

    HTTP/1.1 200 Ok
    Content-Type: application/json
    x-ms-item-count: 25
    x-ms-schemaversion: 1.1
    Date: Tue, 15 Nov 2016 07:26:51 GMT

    {
       "_rid":"qYcAAPEvJBQ=",
       "PartitionKeyRanges":[
          {
             "_rid":"qYcAAPEvJBQCAAAAAAAAUA==",
             "id":"0",
             "_etag":"\"00002800-0000-0000-0000-580ac4ea0000\"",
             "minInclusive":"",
             "maxExclusive":"05C1CFFFFFFFF8",
             "_self":"dbs\/qYcAAA==\/colls\/qYcAAPEvJBQ=\/pkranges\/qYcAAPEvJBQCAAAAAAAAUA==\/",
             "_ts":1477100776
          },
          ...
       ],
       "_count": 25
    }


**パーティション キー範囲のプロパティ**: それぞれのパーティション キー範囲には、次の表にあるメタデータ プロパティが含まれています。

<table>
    <tr>
        <th>ヘッダー名</th>
        <th>説明</th>
    </tr>
    <tr>
        <td>id</td>
        <td>
            <p>パーティション キー範囲の ID です。 これは、各コレクション内で一意の固定 ID です。</p>
            <p>パーティション キー範囲別の変更を読み取るために次の呼び出しで使用する必要があります。</p>
        </td>
    </tr>
    <tr>
        <td>maxExclusive</td>
        <td>パーティション キー範囲のパーティション キー ハッシュ最大値です。 内部使用のみ。</td>
    </tr>
    <tr>
        <td>minInclusive</td>
        <td>パーティション キー範囲のパーティション キー ハッシュ最小値です。 内部使用のみ。</td>
    </tr>        
</table>

サポートされている [ Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) のいずれかを使用してこれを実行できます。 たとえば次のスニペットは、.NET でパーティション キー範囲を取得する方法を示します。

    string pkRangesResponseContinuation = null;
    List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();

    do
    {
        FeedResponse<PartitionKeyRange> pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
            collectionUri, 
            new FeedOptions { RequestContinuation = pkRangesResponseContinuation });

        partitionKeyRanges.AddRange(pkRangesResponse);
        pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    }
    while (pkRangesResponseContinuation != null);

Azure Cosmos DB では、オプションの `x-ms-documentdb-partitionkeyrangeid` ヘッダーを設定することで、パーティション キー範囲ごとにドキュメントを取得できます。 

### <a name="performing-an-incremental-readdocumentfeed"></a>増分 ReadDocumentFeed の実行
ReadDocumentFeed は、Azure Cosmos DB コレクションの変更の増分処理について、次のシナリオ/タスクをサポートします。

* 最初から (つまりコレクション作成時から) のドキュメントへのすべての変更を読み取る。
* 現在からドキュメントに対する今後の更新へのすべての変更を読み取る。
* コレクションの論理バージョン (ETag) からのドキュメントへのすべての変更を読み取る。 増分のフィード読み取り要求から返された ETag に基づいてコンシューマーのチェックポイントを作成できます。

変更内容にはドキュメントへの挿入と更新が含まれます。 削除をキャプチャするには、ドキュメント内で「論理的な削除」プロパティを使用するか、[組み込み型 TTL プロパティ](time-to-live.md)を使用し、Change Feed で保留中の削除を通知する必要があります。

次の表は、ReadDocumentFeed 操作の要求と応答ヘッダーを示します。

**増分 ReadDocumentFeed の要求ヘッダー**:

<table>
    <tr>
        <th>ヘッダー名</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>A-IM</td>
        <td>「増分フィード」を設定するか、省略する必要があります</td>
    </tr>
    <tr>
        <td>If-None-Match</td>
        <td>
            <p>ヘッダーなし: 最初 (コレクション作成時) からのすべての変更を返します</p>
            <p>"*": コレクションに含まれるデータへのすべての新しい変更を返します</p>
            <p>&lt;etag&gt;: ETag がコレクションに設定されている場合、その論理タイムスタンプ以降に行われたすべての変更を返します</p>
        </td>
    </tr>
    <tr>
        <td>x-ms-documentdb-partitionkeyrangeid</td>
        <td>読み取りデータのパーティション キー範囲の ID です。</td>
    </tr>
</table>

**増分 ReadDocumentFeed の応答ヘッダー**:

<table>
    <tr>
        <th>ヘッダー名</th>
        <th>説明</th>
    </tr>
    <tr>
        <td>etag</td>
        <td>
            <p>応答で返された最後のドキュメントの論理シーケンス番号 (LSN)。</p>
            <p>増分 ReadDocumentFeed は、If-None-Match でこの値を再送信すると再開できます。</p>
        </td>
    </tr>
</table>

これは、論理バージョン/Etag `28535` とパーティション キー範囲 = `16` からコレクション内のすべての増分変更を返す要求のサンプルです。

    GET https://mydocumentdb.documents.azure.com/dbs/bigdb/colls/bigcoll/docs HTTP/1.1
    x-ms-max-item-count: 1
    If-None-Match: "28535"
    A-IM: Incremental feed
    x-ms-documentdb-partitionkeyrangeid: 16
    x-ms-date: Tue, 22 Nov 2016 20:43:01 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dzdpL2QQ8TCfiNbW%2fEcT88JHNvWeCgDA8gWeRZ%2btfN5o%3d
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

変更は、パーティション キー範囲に含まれる各パーティション キー値内の時間に基づいて並べ替えられます。 パーティション キー値が異なる場合、順序は保証されません。 結果が 1 ページに収まりきらない場合、前回の応答から `etag` に等しい価値の `If-None-Match` ヘッダーで要求を再送信すると、結果の次のページを読み取ることができます。 ストアド プロシージャまたはトリガー内でトランザクションを使用して複数のドキュメントが挿入または更新された場合、それらのドキュメントはすべて同じ応答ページ内で返されます。

> [!NOTE]
> Change Feed を使用すると、ストアド プロシージャまたはトリガー内で複数のドキュメントが挿入または更新された場合に、`x-ms-max-item-count` で指定した数よりも多くの項目が 1 ページで返される場合があります。 

.NET SDK には、コレクションに加えられた変更にアクセスする、[CreateDocumentChangeFeedQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) および [ChangeFeedOptions](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.changefeedoptions.aspx) ヘルパー クラスが用意されています。 次のスニペットは、単一のクライアントから .NET SDK を使用して開始からのすべての変更を取得する方法を示します。

    private async Task<Dictionary<string, string>> GetChanges(
        DocumentClient client,
        string collection,
        Dictionary<string, string> checkpoints)
    {
        string pkRangesResponseContinuation = null;
        List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();

        do
        {
            FeedResponse<PartitionKeyRange> pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
                collectionUri, 
                new FeedOptions { RequestContinuation = pkRangesResponseContinuation });

            partitionKeyRanges.AddRange(pkRangesResponse);
            pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
        }
        while (pkRangesResponseContinuation != null);

        foreach (PartitionKeyRange pkRange in partitionKeyRanges)
        {
            string continuation = null;
            checkpoints.TryGetValue(pkRange.Id, out continuation);

            IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
                collection,
                new ChangeFeedOptions
                {
                    PartitionKeyRangeId = pkRange.Id,
                    StartFromBeginning = true,
                    RequestContinuation = continuation,
                    MaxItemCount = 1
                });

            while (query.HasMoreResults)
            {
                FeedResponse<DeviceReading> readChangesResponse = query.ExecuteNextAsync<DeviceReading>().Result;

                foreach (DeviceReading changedDocument in readChangesResponse)
                {
                    Console.WriteLine(changedDocument.Id);
                }

                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
        }

        return checkpoints;
    }

次のスニペットは、Change Feed サポートと上記の関数を使用して、Azure Cosmos DB で変更をリアルタイムで処理する方法を示します。 最初の呼び出しは、コレクション内のすべてのドキュメントを返し、2 つ目の呼び出しは最後のチェックポイント以降に作成された 2 つのドキュメントのみが返されます。

    // Returns all documents in the collection.
    Dictionary<string, string> checkpoints = await GetChanges(client, collection, new Dictionary<string, string>());

    await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-201", MetricType = "Temperature", Unit = "Celsius", MetricValue = 1000 });
    await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-212", MetricType = "Pressure", Unit = "psi", MetricValue = 1000 });

    // Returns only the two documents created above.
    checkpoints = await GetChanges(client, collection, checkpoints);


クライアント側のロジックを使用して Change Feed にフィルターを適用し、選択的にイベントを処理することもできます。 たとえば、これはクライアント側の LINQ を使用して、デバイス センサーから温度変更イベントのみを処理するスニペットです。

    FeedResponse<DeviceReading> readChangesResponse = query.ExecuteNextAsync<DeviceReading>().Result;

    foreach (DeviceReading changedDocument in 
        readChangesResponse.AsEnumerable().Where(d => d.MetricType == "Temperature" && d.MetricValue > 1000L))
    {
        // trigger an action, like call an API
    }

## <a id="change-feed-processor"></a>Change Feed プロセッサ ライブラリ
[Azure Cosmos DB Change Feed プロセッサ ライブラリ](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/ChangeFeedProcessor)を使用すると、Change Feed から複数のコンシューマーにイベント処理を分散できます。 .NET プラットフォームで Change Feed リーダーを作成するときには、この実装を使用する必要があります。 `ChangeFeedProcessorHost` クラスはイベント プロセッサ実装のためにスレッドセーフでマルチプロセスの安全なランタイム環境を提供します。さらに、その環境では、チェックポイント処理とパーティション リースの管理が提供されます。

[`ChangeFeedProcessorHost`](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/ChangeFeedProcessor/DocumentDB.ChangeFeedProcessor/ChangeFeedEventHost.cs) クラスを使用するには、[`IChangeFeedObserver`](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/ChangeFeedProcessor/DocumentDB.ChangeFeedProcessor/IChangeFeedObserver.cs) を実装できます。 このインターフェイスには 3 つのメソッドが含まれています。

* OpenAsync
* CloseAsync
* ProcessEventsAsync

イベント処理を開始するには、ChangeFeedProcessorHost をインスタンス化して、Azure Cosmos DB コレクションの適切なパラメーターを提供します。 次に、`RegisterObserverAsync` を呼び出し、`IChangeFeedObserver` 実装をランタイムに登録します。 この時点で、ホストは "貪欲" アルゴリズムを使用して Azure Cosmos DB コレクションのすべてのパーティション キー範囲でリースの取得を試みます。 これらのリースは一定の期間存続し、その後、更新する必要があります。 新しいノード (この場合は worker インスタンス) がオンラインになると、新しいノードはリースを予約し、時間と共にリースの追加取得を試行し、負荷がノード間を移動します。

![Azure Cosmos DB Change Feed プロセッサ ホストの使用](./media/change-feed/changefeedprocessor.png)

時間と共に、均衡が確立されます。 この動的機能により、スケールアップとスケールダウンの両方で、CPU に基づく自動スケールがコンシューマーに適用されます。 コンシューマーが処理できる速度より速く Azure Cosmos DB で変更を利用できれば、コンシューマーの CPU 増加を利用して、worker インスタンス数の自動スケールを実行できます。

`ChangeFeedProcessorHost` クラスは、別の Azure Cosmos DB リース コレクションを使用して、チェックポイント処理メカニズムも実装します。 このメカニズムはパーティションごとにオフセットを保存します。そのため、各コンシューマーは前回のコンシューマーが保存した内容から、最後のチェックポイントを判断できます。 パーティションがリースによってノード間を移動するにつれて、負荷移動を円滑にする同期メカニズムとなります。


変更をコンソールに出力するシンプルな Change Feed プロセッサ ホスト用のコード スニペットを次に示します。

```cs
    class DocumentFeedObserver : IChangeFeedObserver
    {
        private static int s_totalDocs = 0;
        public Task OpenAsync(ChangeFeedObserverContext context)
        {
            Console.WriteLine("Worker opened, {0}", context.PartitionKeyRangeId);
            return Task.CompletedTask;  // Requires targeting .NET 4.6+.
        }
        public Task CloseAsync(ChangeFeedObserverContext context, ChangeFeedObserverCloseReason reason)
        {
            Console.WriteLine("Worker closed, {0}", context.PartitionKeyRangeId);
            return Task.CompletedTask;
        }
        public Task ProcessEventsAsync(IReadOnlyList<Document> docs, ChangeFeedObserverContext context)
        {
            Console.WriteLine("Change feed: total {0} doc(s)", Interlocked.Add(ref s_totalDocs, docs.Count));
            return Task.CompletedTask;
        }
    }
```

次のコード スニペットは、Azure Cosmos DB コレクションの変更をリッスンする新しいホストの登録方法を示します。 ここでは、複数のコンシューマー全体のパーティションのリースを管理する、別のコレクションを構成しています。

```cs
    string hostName = Guid.NewGuid().ToString();
    DocumentCollectionInfo documentCollectionLocation = new DocumentCollectionInfo
    {
        Uri = new Uri("https://YOUR_SERVICE.documents.azure.com:443/"),
        MasterKey = "YOUR_SECRET_KEY==",
        DatabaseName = "db1",
        CollectionName = "documents"
    };

    DocumentCollectionInfo leaseCollectionLocation = new DocumentCollectionInfo
    {
        Uri = new Uri("https://YOUR_SERVICE.documents.azure.com:443/"),
        MasterKey = "YOUR_SECRET_KEY==",
        DatabaseName = "db1",
        CollectionName = "leases"
    };

    ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation);
    await host.RegisterObserverAsync<DocumentFeedObserver>();
```

この記事では、Azure Cosmos DB の Change Feed サポートと、REST API や SDK を使用して Azure Cosmos DB のデータの変更を追跡する方法について説明しました。 

## <a name="next-steps"></a>次のステップ
* [GitHub の Azure Cosmos DB Change Feed コード サンプル](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed)を使ってみる
* [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) または [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) を使用してコーディングを開始する

