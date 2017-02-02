---
title: "Azure DocumentDB での Change Feed サポートの使用 | Microsoft Docs"
description: "DocumentDB ドキュメントの変更を追跡し、トリガーなどのイベント ベースの処理を実行したり、キャッシュと分析システムを最新に保つには、Azure DocumentDB の Change Feed サポートを使用します。"
keywords: Change Feed
services: documentdb
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 12/13/2016
ms.author: b-hoedid
translationtype: Human Translation
ms.sourcegitcommit: b22e75264345bc9d155bd1abc1fdb6e978dfad04
ms.openlocfilehash: bafc50750381616ecf30c4e41090f342d82007f9


---
# <a name="working-with-the-change-feed-support-in-azure-documentdb"></a>Azure DocumentDB での Change Feed サポートの使用
[Azure DocumentDB](documentdb-introduction.md) は、大量のトランザクション データや運用データの保存に利用される高速かつ柔軟な NoSQL データベース サービスで、読み取り/書き込み処理を 10 ミリ秒未満の予測可能な待機時間で実行します。 このため、IoT、ゲーム、小売、操作ログといったアプリケーションに最適です。 こうしたアプリケーションでは多くの場合、DocumentDB のデータに対する変更を追跡し、その変更内容に基づいて、具体化されたビューの更新、リアルタイム分析の実行、通知のトリガーなどさまざまなアクションを実行する必要があります。 DocumentDB の **Change Feed サポート**により、こうしたパターンに対応できる効率性と拡張性に優れたソリューションを構築できます。

Change Feed サポートにより、DocumentDB コレクション内のドキュメントの一覧が更新日時順に並べ替えられて表示されます。 このフィードを利用すれば、コレクション内のデータに対する変更をリッスンし、以下のようなアクションを実行できます。

* ドキュメントが挿入または変更された場合に API の呼び出しをトリガーする
* 更新内容に対してリアルタイムの (ストリーム) 処理を実行する
* キャッシュ、検索エンジン、データ ウェアハウスとデータを同期する

DocumentDB での変更は保持され、非同期的に処理できます。また単数または複数のコンシューマーに分散し並列処理できます。 Change Feed の API と、拡張性の高いリアルタイム アプリケーションの構築に使用する方法について説明します。

![DocumentDB の Change Feed を使用してリアルタイム分析とイベント ドリブンのコンピューティング シナリオを強化](./media/documentdb-change-feed/changefeed.png)

## <a name="use-cases-and-scenarios"></a>ユース ケースとシナリオ
Change Feed を使用すると、データセット全体に対してクエリを実行する代わりに、大量の書き込みがある大きなデータセットを効率的に処理し、変更内容を特定できます。 たとえば、次のタスクを効率よく実行できます。

* Azure DocumentDB に格納されたデータでキャッシュ、検索インデックス、データ ウェアハウスを更新する。
* アプリケーション レベルのデータの階層化とアーカイブ化 (DocumentDB の「ホット データ」の格納、[Azure Blob Storage](../storage/storage-introduction.md) または [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) への「コールド データ」のエイジアウト) を実装する。
* [Apache Hadoop](documentdb-run-hadoop-with-hdinsight.md) を使用してデータの一括分析を実装する。
* DocumentDB を使用して [Azure でラムダ パイプライン](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/)を実装する。 DocumentDB は、取り込みとクエリの両方を処理し、低い総所有コストでラムダ アーキテクチャを実装できる、拡張性の高いデータベース ソリューションを提供します。 
* 個別のパーティション スキームで別の Azure DocumentDB アカウントへの移行をダウンタイムなく実行する。

**取り込みとクエリに Azure DocumentDB を使った ラムダ パイプライン:**

![取り込みとクエリ向け Azure DocumentDB ベースのラムダ パイプライン](./media/documentdb-change-feed/lambda.png)

DocumentDB を使用して、デバイス、センサー、インフラストラクチャ、アプリケーションからイベント データを受信し、格納したり、こうしたイベントを [Azure Stream Analytics](documentdb-search-indexer.md)、[Apache Storm](../hdinsight/hdinsight-storm-overview.md)、[Apache Spark](../hdinsight/hdinsight-apache-spark-overview.md) でリアルタイムに処理できます。 

Web やモバイル アプリ内で、お客様のプロファイル、設定、または場所への変更などのイベントを追跡し、[Azure Functions](../azure-functions/functions-bindings-documentdb.md) や [App Services](https://azure.microsoft.com/services/app-service/) を使用して各デバイスにプッシュ通知を送信するなど特定のアクションをトリガーできます。 たとえば DocumentDB を使用してゲームを作成している場合、Change Feed を使用して完了したゲームのスコアに基づくリアルタイムのスコアボードを実装できます。

## <a name="how-change-feed-works-in-azure-documentdb"></a>Azure DocumentDB での Change Feed の動作
DocumentDB では、DocumentDB コレクションに対する更新を増分的に読み取ることができます。 この Change Feed には、次の特長があります。

* 変更内容は DocumentDB 内で保持され、非同期で処理できます。
* コレクション内のドキュメントへの変更は、Change Feed ですぐに利用できます。
* 各ドキュメントに対する変更は Change Feed に 1 回だけ反映されます。 変更ログには、特定のドキュメントの最新の変更のみが含まれます。 途中の変更は利用できない場合があります。
* Change Feed はパーティション キー値ごとに変更日時順に並べ替えられます。 パーティション キー値が異なる場合、順序は保証されません。
* 変更の同期は任意の時点から行うことが可能です。つまり、変更内容を利用できるデータ保持期間は固定されていません。
* 変更内容は、パーティション キーの範囲として利用できます。 この機能により、大規模コレクションの変更を複数のコンシューマーやサーバーで並行処理できるようになります。
* アプリケーションは、同じコレクションに対して複数の Change Feed を同時に要求できます。

DocumentDB の Change Feed はすべてのアカウントについて既定で有効になっており、追加料金は発生しません。 なお、DocumentDB の他の操作と同様に、書き込みを実行するリージョンや Change Feed からの[読み取りを実行するリージョン](documentdb-distribute-data-globally.md)では、[プロビジョニングしたスループット](documentdb-request-units.md)を利用できます。 Change Feed には、コレクション内のドキュメントに加えられた挿入操作や更新操作が含まれています。 削除の代わりに、ドキュメント内で「論理的な削除」フラグを設定することで削除をキャプチャできます。 または、[TTL 機能](documentdb-time-to-live.md)からドキュメントの有限の有効期限 (24 時間など) を設定し、そのプロパティの値を使用して削除をキャプチャできます。 この場合は、TTL の有効期限よりも短い期間に含まれる変更を処理する必要があります。 Change Feed は、ドキュメント コレクション内の各パーティション キー範囲で利用できるため、1 人または複数のコンシューマーの間で分散し並列処理できます。 

![DocumentDB の Change Feed の分散処理](./media/documentdb-change-feed/changefeedvisual.png)

次のセクションでは、DocumentDB REST API と SDK を使用して Change Feed にアクセスする方法について説明します。

## <a name="working-with-the-rest-api-and-sdk"></a>REST API と SDK の使用
DocumentDB では、**コレクション**と呼ばれるストレージとスループットの柔軟なコンテナーを提供します。 コレクション内のデータはスケーラビリティとパフォーマンスのために、[パーティション キー](documentdb-partition-data.md)を使用して論理的にグループ化されます。 DocumentDB ではこうしたデータへのアクセスに、ID 別の検索 (Read/Get)、クエリ、読み取りフィード (スキャン) を含む、さまざまな API を提供します。 Change Feed は DocumentDB の `ReadDocumentFeed` API に新しい要求ヘッダーを 2 個設定することで取得でき、さまざまなパーティション キー範囲で並行して処理できます。

### <a name="readdocumentfeed-api"></a>ReadDocumentFeed API
ReadDocumentFeed のしくみを簡単に説明します。 DocumentDB は、`ReadDocumentFeed` API を通じてコレクション内でドキュメントのフィードの読み取りをサポートします。 たとえば、次の要求は、`serverlogs` コレクション内でドキュメントのページを返します。 

    GET https://mydocumentdb.documents.azure.com/dbs/smalldb/colls/smallcoll HTTP/1.1
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

![DocumentDB ReadDocumentFeed の逐次実行](./media/documentdb-change-feed/readfeedserial.png)

また、サポートされている [DocumentDB SDK](documentdb-sdk-dotnet.md) のいずれかを使用してドキュメントのフィードを取得できます。 たとえば次のスニペットは、.NET で ReadDocumentFeed を実行する方法を示します。

    FeedResponse<dynamic> feedResponse = null;
    do
    {
        feedResponse = await client.ReadDocumentFeedAsync(collection, new FeedOptions { MaxItemCount = -1 });
    }
    while (feedResponse.ResponseContinuation != null);

> [!NOTE]
> Change Feed では 1.11.0 以降の SDK バージョンが必要になります (現在はプライベート プレビューで利用可能)

### <a name="distributed-execution-of-readdocumentfeed"></a>ReadDocumentFeed の分散実行
数テラバイト以上のデータを含んでいるか、大量の更新を取り込むコレクションでは、1 台のクライアント マシンにフィードの読み取りを直列処理させるのが現実的に難しい場合があります。 DocumentDB はこれらのビッグ データのシナリオをサポートするために API を提供し、複数のクライアント リーダー/コンシューマーの間で透過的に `ReadDocumentFeed` 呼び出しを分散します。 

**ドキュメント フィードの分散読み取り**

![DocumentDB ReadDocumentFeed の分散実行](./media/documentdb-change-feed/readfeedparallel.png)

増分の変更に拡張性のある処理を提供するため、DocumentDB はパーティション キー範囲に基づいた Change Feed API でスケール アウト モデルをサポートします。

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

サポートされる [DocumentDB SDK](documentdb-sdk-dotnet.md) のいずれかを使用してこれを実行できます。 たとえば次のスニペットは、.NET でパーティション キー範囲を取得する方法を示します。

    List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();
    FeedResponse<PartitionKeyRange> response;

    do
    {
        response = await client.ReadPartitionKeyRangeFeedAsync(collection);
        partitionKeyRanges.AddRange(response);
    }
    while (response.ResponseContinuation != null);

DocumentDB では、任意の `x-ms-documentdb-partitionkeyrangeid` ヘッダーを設定することにより、パーティション キー範囲あたりのドキュメントを取得できます。 

### <a name="performing-an-incremental-readdocumentfeed"></a>増分 ReadDocumentFeed の実行
ReadDocumentFeed は、DocumentDB コレクションの変更の増分処理で次のシナリオ/タスクをサポートします。

* 最初から (つまりコレクション作成時から) のドキュメントへのすべての変更を読み取る。
* 現在からドキュメントに対する今後の更新へのすべての変更を読み取る。
* コレクションの論理バージョン (ETag) からのドキュメントへのすべての変更を読み取る。 増分のフィード読み取り要求から返された ETag に基づいてコンシューマーのチェックポイントを作成できます。

変更内容にはドキュメントへの挿入と更新が含まれます。 削除をキャプチャするには、ドキュメント内で「論理的な削除」プロパティを使用するか、[組み込み型 TTL プロパティ](documentdb-time-to-live.md)を使用し、Change Feed で保留中の削除を通知する必要があります。

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

変更は、パーティション キー範囲に含まれる各パーティション キー値内の時間に基づいて並べ替えられます。 パーティション キー値が異なる場合、順序は保証されません。 結果が 1 ページに収まりきらない場合、前回の応答から `etag` に等しい価値の `If-None-Match` ヘッダーで要求を再送信すると、結果の次のページを読み取ることができます。 複数のドキュメントがストアド プロシージャやトリガー内においてトランザクション全体で更新された場合、すべて同じ応答ページ内で返されます。

.NET SDK では、コレクションに加えられた変更にアクセスするために `CreateDocumentChangeFeedQuery` と `ChangeFeedOptions` ヘルパー クラスが提供されます。 次のスニペットは、単一のクライアントから .NET SDK を使用して開始からのすべての変更を取得する方法を示します。

    private async Task<Dictionary<string, string>> GetChanges(
        DocumentClient client,
        string collection,
        Dictionary<string, string> checkpoints)
    {
        List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();
        FeedResponse<PartitionKeyRange> pkRangesResponse;

        do
        {
            pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(collection);
            partitionKeyRanges.AddRange(pkRangesResponse);
        }
        while (pkRangesResponse.ResponseContinuation != null);

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

また次のスニペットは、Change Feed サポートと上記の関数を使用して DocumentDB で変更をリアルタイムで処理する方法を示します。 最初の呼び出しは、コレクション内のすべてのドキュメントを返し、2 つ目の呼び出しは最後のチェックポイント以降に作成された 2 つのドキュメントのみが返されます。

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

この記事では、DocumentDB における Change Feed のサポートと、DocumentDB REST API や SDK を使用して DocumentDB データに加えられた変更を追跡する方法について説明しました。 

## <a name="next-steps"></a>次のステップ
* [Github で DocumentDB Change フィード コード サンプル](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed)を使ってみる
* [DocumentDB のリソースと階層](documentdb-resources.md)についての詳細を確認する
* [DocumentDB SDK](documentdb-sdk-dotnet.md) や [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) でコーディングを開始する


<!--HONumber=Dec16_HO2-->


