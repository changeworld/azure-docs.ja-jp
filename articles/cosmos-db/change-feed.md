---
title: Azure Cosmos DB での Change Feed サポートの使用 | Microsoft Docs
description: Azure Cosmos DB の Change Feed サポートを使用して、ドキュメントの変更を追跡し、トリガーなどのイベント ベースの処理を実行したり、キャッシュや分析システムを最新の状態に維持したりします。
keywords: Change Feed
services: cosmos-db
author: rafats
manager: kfile
documentationcenter: ''
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: article
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: 8cc4d8110db0a650b8355f96fee490093826ac30
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Azure Cosmos DB での Change Feed サポートの使用

[Azure Cosmos DB](../cosmos-db/introduction.md) は、世界中にレプリケートされている高速で柔軟なデータベースです。IoT、ゲーム、小売、操作ログ記録アプリケーションに最適です。 このようなアプリケーションの一般的な設計パターンは、データの変更を使用して、追加のアクションを開始する方法です。 このような追加アクションの例を次に示します。 

* ドキュメントが挿入または変更された場合に通知または API の呼び出しをトリガーする。
* IoT または分析実行のためのストリーム処理。
* キャッシュ、検索エンジン、またはデータ ウェアハウスと同期するか、データをコールド ストレージにアーカイブすることによる追加のデータ移動。

Azure Cosmos DB の **Change Feed サポート**により、次の図のようにこれらの各パターンに対応する効率的でスケーラブルなソリューションを構築できます。

![Azure Cosmos DB の Change Feed を使用してリアルタイム分析とイベント ドリブンのコンピューティング シナリオを強化](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> 変更フィードのサポートは、Azure Cosmos DB のすべてのデータ モデルとコンテナーに提供されています。 ただし、変更フィードは SQL クライアントを使用して読み取られ、項目は JSON 形式でシリアル化されます。 JSON 形式なので、MongoDB クライアントでは BSON 形式のドキュメントと JSON 形式の変更フィード間で不一致が発生します。

次の動画では、Azure Cosmos DB プログラム マネージャー、Andrew Liu が Azure Cosmos DB 変更フィードのしくみを紹介しています。

> [!VIDEO https://www.youtube.com/embed/mFnxoxeXlaU]
>
>

## <a name="how-does-change-feed-work"></a>変更フィードのしくみ

Azure Cosmos DB の変更フィードのサポートは、Azure Cosmos DB コレクションの変更をリッスンすることで機能します。 変更されたドキュメントは、変更された順に並べ替えられた一覧に出力されます。 変更は保持され、非同期的に増分処理できます。また、出力を 1 つ以上のコンシューマーに分散させて並列処理することもできます。 

変更フィードの読み取り方法は後述するように 3 つあります。

1.  [Azure Functions の使用](#azure-functions)
2.  [Azure Cosmos DB SDK の使用](#rest-apis)
3.  [Azure Cosmos DB Change Feed Processor ライブラリの使用](#change-feed-processor)

Change Feed は、ドキュメント コレクション内の各パーティション キー範囲で利用できるため、次の図のように 1 人または複数のコンシューマーの間で分散し並列処理できます。

![Azure Cosmos DB の Change Feed の分散処理](./media/change-feed/changefeedvisual.png)

追加情報:
* 変更フィードは、すべてのアカウントで既定で有効になっています。
* Azure Cosmos DB の他の操作と同様に、書き込みリージョンや Change Feed から読み取る[読み取りリージョン](distribute-data-globally.md)で[プロビジョニング スループット](request-units.md)を使用できます。
* Change Feed には、コレクション内のドキュメントに加えられた挿入操作や更新操作が含まれています。 削除の代わりに、ドキュメント内で「論理的な削除」フラグを設定することで削除をキャプチャできます。 または、[TTL 機能](time-to-live.md)からドキュメントの有限の有効期限 (24 時間など) を設定し、そのプロパティの値を使用して削除をキャプチャできます。 この場合は、TTL の有効期限よりも短い期間に含まれる変更を処理する必要があります。
* ドキュメントに加えられた変更は Change Feed 内に 1 回だけ出現し、クライアントがそれらのチェックポイント処理ロジックを管理します。 Change Feed プロセッサ ライブラリは、自動チェックポイント処理と "最低 1 回" というセマンティクスを提供します。
* 変更ログには、特定のドキュメントの最新の変更のみが含まれます。 途中の変更は利用できない場合があります。
* Change Feed はパーティション キー値ごとに変更日時順に並べ替えられます。 パーティション キー値が異なる場合、順序は保証されません。
* 変更の同期は任意の時点から行うことが可能です。つまり、変更内容を利用できるデータ保持期間は固定されていません。
* 変更内容は、パーティション キーの範囲として利用できます。 この機能により、大規模コレクションの変更を複数のコンシューマーやサーバーで並行処理できるようになります。
* アプリケーションは、同じコレクションに対して複数の Change Feed を同時に要求できます。
* ChangeFeedOptions.StartTime は、指定された時刻に対応する継続トークンを検出するためなど、最初の始点を指定するために使用できます。 ContinuationToken を指定している場合、StartTime 値と StartFromBeginning 値よりも優先されます。 ChangeFeedOptions.StartTime の精度は 5 秒以下です。 

## <a name="use-cases-and-scenarios"></a>ユース ケースとシナリオ

変更フィードを使用すると、大量の書き込みが発生する大規模なデータセットを効率的に処理できるので、データセット全体に対してクエリを実行する代わりに、変更フィードを使用して変更を特定できます。 

たとえば、変更フィードを使用すると、次のタスクを効率よく実行できます。

* Azure Cosmos DB に格納されたデータで、キャッシュ、検索インデックス、データ ウェアハウスを更新する。
* アプリケーション レベルのデータの階層化とアーカイブを実装する ("ホット データ" を Azure Cosmos DB に格納し、"コールド データ" を [Azure Blob Storage](../storage/common/storage-introduction.md) または [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) にエイジアウトする)。
* パーティション スキームが異なる別の Azure Cosmos DB アカウントへの移行をダウンタイムなしに実行する。
* Azure Cosmos DB を使用して [Azure でラムダ パイプライン](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/)を実装する。 Azure Cosmos DB は、取り込みとクエリの両方を処理し、低 TCO でラムダ アーキテクチャを実装できる、スケーラブルなデータベース ソリューションを提供します。 
* デバイス、センサー、インフラストラクチャ、アプリケーションからイベント データを受信し、格納したり、こうしたイベントを [Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md)、[Apache Storm](../hdinsight/storm/apache-storm-overview.md)、[Apache Spark](../hdinsight/spark/apache-spark-overview.md) でリアルタイムに処理できます。 

次の図は、Azure Cosmos DB を使用して取り込みとクエリの両方を実行するラムダ パイプラインが、変更フィードのサポートを使用する方法を示しています。 

![取り込みとクエリに対応する Azure Cosmos DB ベースのラムダ パイプライン](./media/change-feed/lambda.png)

また、[サーバーレス](http://azure.com/serverless) Web やモバイル アプリ内で、お客様のプロファイル、設定、または場所への変更などのイベントを追跡し、[Azure Functions](#azure-functions) を使用して各デバイスにプッシュ通知を送信するなど特定のアクションをトリガーできます。 たとえば、Azure Cosmos DB を使用してゲームを構築する場合、Change Feed を使用して完了したゲームのスコアに基づくリアルタイムのスコアボードを実装できます。

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>Azure Functions の使用 

Azure Functions を使用してインストール場合、Azure Cosmos DB の変更フィードに接続する最も簡単な方法は、Azure Cosmos DB トリガーを Azure Functions アプリケーションに追加することです。 Azure Functions アプリケーションで Azure Cosmos DB トリガーを作成するときに、接続先の Azure Cosmos DB コレクションを選択します。これで、コレクションに変更が加えられるたびに関数がトリガーされます。 

トリガーを作成するには、Azure Functions ポータル、Azure Cosmos DB ポータル、またはプログラムを使用します。 詳細については、「[Azure Cosmos DB: Azure Functions を使用したサーバーなしのデータベースのコンピューティング](serverless-computing-database.md)」を参照してください。

<a id="rest-apis"></a>
## <a name="using-the-sdk"></a>SDK を使用する

Azure Cosmos DB 用の [SQL SDK](sql-api-sdk-dotnet.md) は、変更フィードの読み取りと管理に必要な機能がすべてそろっています。 ただし、強力な機能には多くの責任も伴います。 チェックポイントを管理し、ドキュメントのシーケンス番号を扱い、パーティション キーを細かく制御する場合、SDK の使用が推奨される方法の可能性があります。

このセクションでは、SQL SDK を使用して変更フィードを操作する手順について説明します。

1. まず appconfig から次のリソースを読み取ります。 エンドポイントと承認キーの取得手順については、「[接続文字列を更新する](create-sql-api-dotnet.md#update-your-connection-string)」を参照してください。

    ``` csharp
    DocumentClient client;
    string DatabaseName = ConfigurationManager.AppSettings["database"];
    string CollectionName = ConfigurationManager.AppSettings["collection"];
    string endpointUrl = ConfigurationManager.AppSettings["endpoint"];
    string authorizationKey = ConfigurationManager.AppSettings["authKey"];
    ```

2. 次のようにクライアントを作成します。

    ```csharp
    using (client = new DocumentClient(new Uri(endpointUrl), authorizationKey,
    new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    {
    }
    ```

3. パーティション キーの範囲を取得します。

    ```csharp
    FeedResponse pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri,
        new FeedOptions
            {RequestContinuation = pkRangesResponseContinuation });
     
    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    ```

4. 各パーティション キーの範囲について ExecuteNextAsync を呼び出します。

    ```csharp
    foreach (PartitionKeyRange pkRange in partitionKeyRanges){
        string continuation = null;
        checkpoints.TryGetValue(pkRange.Id, out continuation);
        IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
            collectionUri,
            new ChangeFeedOptions
            {
                PartitionKeyRangeId = pkRange.Id,
                StartFromBeginning = true,
                RequestContinuation = continuation,
                MaxItemCount = -1,
                // Set reading time: only show change feed results modified since StartTime
                StartTime = DateTime.Now - TimeSpan.FromSeconds(30)
            });
        while (query.HasMoreResults)
            {
                FeedResponse<dynamic> readChangesResponse = query.ExecuteNextAsync<dynamic>().Result;
    
                foreach (dynamic changedDocument in readChangesResponse)
                    {
                         Console.WriteLine("document: {0}", changedDocument);
                    }
                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
    }
    ```

複数の閲覧者がいる場合、**ChangeFeedOptions** を使用して、読み取りの負荷を異なるスレッドや異なるクライアントに分散させることができます。

以上の数行のコードだけで、変更フィードの読み取りを開始できます。 この記事に使用されているコードの完成版は、[GitHub リポジトリ](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)から入手できます。

前述の手順 4 のコードで、最終行の **ResponseContinuation** には、ドキュメントの最終論理シーケンス番号 (LSN) が含まれています。このシーケンス番号は、次にその番号の後の新しいドキュメントを読み取るときに使用されます。 **ChangeFeedOption** の **StartTime** を使用することで、より広い範囲のドキュメントを取得できます。 そのため、**ResponseContinuation** を null にして、**StartTime** を過去に変更すると、**StartTime** 以降に変更されたすべてのドキュメントが取得されます。 ただし、**ResponseContinuation** に値が指定されている場合、その LSN 以降のすべてのドキュメントが取得されます。

そのため、チェックポイントの配列は各パーティションの LSN のみを維持しています。 ただし、パーティション、チェックポイント、LSN、開始時間などを扱わない場合は、Change Feed Processor ライブラリを使用する方法が簡単です。

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>Change Feed Processor ライブラリの使用 

[Azure Cosmos DB Change Feed Processor ライブラリ](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed)を使用すると、複数のコンシューマーにイベント処理を簡単に分散させることができます。 このライブラリで、並列で実行されている複数のパーティションと複数のスレッド全体の変更の読み取りが簡単になります。

Change Feed Processor ライブラリの主な利点は、各パーティションと継続トークンを管理する必要がないことと、各コレクションを手動でポーリングする必要がないことです。

Change Feed Processor ライブラリで、並列で実行されている複数のパーティションと複数のスレッド全体の変更の読み取りが簡単になります。  リース メカニズムを使用して、パーティション全体の変更の読み取りが自動的に管理されます。 次の図のように、Change Feed Processor ライブラリを使用している 2 つのクライアントを起動すると、2 つのクライアント間で作業が分割されます。 クライアント数を増やし続けると、クライアント全体で作業が分割されます。

![Azure Cosmos DB の Change Feed の分散処理](./media/change-feed/change-feed-output.png)

左側のクライアントが最初に起動され、すべてのパーティションの監視が開始されます。次に、2 つ目のクライアントが起動され、1 つ目のクライアントは 2 つ目のクライアントにリースの一部を渡します。 この図からわかるように、複数のコンピューターとクライアント間で作業を分散させるにはこの方法が推奨されます。

同じコレクションを監視し、同じリースを使用する 2 つのサーバーレス Azure 関数がある場合、プロセッサ ライブラリがパーティションを処理する方法によっては、2 つの関数が異なるドキュメントを取得する可能性があります。

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>Change Feed Processor ライブラリの概要

Change Feed Processor の実装には、監視対象コレクション、リース コレクション、プロセッサ ホスト、コンシューマーの 4 つの主要コンポーネントがあります。 

> [!WARNING]
> コレクションの作成は料金に影響します。これは、Azure Cosmos DB と通信するためにアプリケーションのスループットを予約するためです。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/cosmos-db/)をご覧ください。
> 
> 

**監視対象コレクション:** 監視対象コレクションは、Change Feed の生成元のデータです。 監視対象コレクションに対する挿入と変更は、コレクションの Change Feed に反映されます。 

**リース コレクション:** リース コレクションは、複数の worker 間で Change Feed の処理を調整します。 個別のコレクションを使用して、リース (パーティションごとに 1 リース) が保存されます。 このリース コレクションは、Change Feed Processor が実行されている場所の近くに書き込みリージョンを持つ別のアカウントに保存すると便利です。 リース オブジェクトには次の属性があります。 
* Owner: リースを所有するホストを指定します。
* Continuation: 特定のパーティションの Change Feed 内の位置 (継続トークン) を指定します。
* Timestamp: リースの最終更新時刻。タイムスタンプを使用して、リースを期限切れと見なすかどうかを確認できます。 

**プロセッサ ホスト:** 各ホストは、アクティブなリースを保持するホストの他のインスタンスの数に基づいて、処理するパーティションの数を決定します。 
1.  すべてのホスト間でワークロードのバランスを取るために、ホストは起動時にリースを取得します。 ホストは定期的にリースを更新するので、リースはアクティブな状態に維持されます。 
2.  ホストは、読み取りを実行するたびにリースに対して前回の継続トークンのチェックポイント処理を実行します。 同時実行の安全性を確保するために、ホストはリースを更新するたびに ETag をチェックします。 他のチェックポイント戦略もサポートされています。  
3.  シャットダウン時に、ホストはすべてのリースを解放します。ただし、継続情報は保持されるので、後で保存済みのチェックポイントから読み取りを再開できます。 

現時点では、ホストの数がパーティション (リース) の数を超えることはできません。

**コンシューマー:** コンシューマー (worker) は、各ホストによって開始された Change Feed の処理を実行するスレッドです。 プロセッサ ホストごとに複数のコンシューマーを使用できます。 各コンシューマーは、割り当てられているパーティションから Change Feed を読み取り、変更と期限切れのリースをホストに通知します。

Change Feed Processor のこの 4 つの要素の連携のしくみについて理解を深めるために、次の図の例を見てみましょう。 監視対象コレクションでは、ドキュメントを保存し、パーティション キーとして "city" を使用します。 青色のパーティションには、"A-E" の "city" フィールドを含むドキュメントが格納されていることがわかります (他のパーティションも同様)。 2 つのホストがあり、各ホストは 4 つのパーティションから並列で読み取る 2 つのコンシューマーを使用しています。 矢印は、コンシューマーが Change Feed の特定の場所から読み取ることを示しています。 最初のパーティションでは、濃い青はまだ読み取られていない変更を表し、薄い青は Change Feed で既に読み取られた変更を表しています。 ホストはリース コレクションを使用して "Continuation" 値を保存することで、各コンシューマーの現在の読み取り位置を追跡します。 

![Azure Cosmos DB Change Feed プロセッサ ホストの使用](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>Change Feed Processor ライブラリの使用

Change Feed Processor NuGet パッケージをインストールする前に、まず以下をインストールします。 

* Microsoft.Azure.DocumentDB バージョン 1.13.1 以上。 
* Newtonsoft.Json バージョン 9.0.1 以上

次に [Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget パッケージ](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)をインストールし、参照に追加します。

Change Feed Processor ライブラリを実装するには、次の手順を実行する必要があります。

1. **DocumentFeedObserver** オブジェクトを実装します。これで **IChangeFeedObserver** が実装されます。

2. **DocumentFeedObserverFactory** オブジェクトを実装します。これで **IChangeFeedObserverFactory** が実装されます。

3. **DocumentFeedObserverFacory** の **CreateObserver** メソッドで、手順 1 で作成した **ChangeFeedObserver** をインスタンス化して返します。

    ```
    public IChangeFeedObserver CreateObserver()
    {
              DocumentFeedObserver newObserver = new DocumentFeedObserver(this.client, this.collectionInfo);
              return newObserver;
    }
    ```

4. **DocumentObserverFactory** をインスタンス化します。

5. **ChangeFeedEventHost** をインスタンス化します。

    ```csharp
    ChangeFeedEventHost host = new ChangeFeedEventHost(
                     hostName,
                     documentCollectionLocation,
                     leaseCollectionLocation,
                     feedOptions,
                     feedHostOptions);
    ```

6. **DocumentFeedObserverFactory** をホストに登録します。

手順 4 ～ 6 のコードは次のとおりです。 

```
ChangeFeedOptions feedOptions = new ChangeFeedOptions();
feedOptions.StartFromBeginning = true;

ChangeFeedHostOptions feedHostOptions = new ChangeFeedHostOptions();
 
// Customizing lease renewal interval to 15 seconds.
// Can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay
feedHostOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);
 
using (DocumentClient destClient = new DocumentClient(destCollInfo.Uri, destCollInfo.MasterKey))
{
        DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory(destClient, destCollInfo);
        ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation, feedOptions, feedHostOptions);
        await host.RegisterObserverFactoryAsync(docObserverFactory);
        await host.UnregisterObserversAsync();
}
```

これで完了です。 これらのいくつかの手順の後に、**DocumentFeedObserver ProcessChangesAsync** メソッドが実行されます。

## <a name="next-steps"></a>次の手順

Azure Cosmos DB と Azure Functions の使用方法については、「[Azure Cosmos DB: Azure Functions を使用したサーバーなしのデータベースのコンピューティング](serverless-computing-database.md)」を参照してください。

Change Feed Processor ライブラリの使用方法については、次のリソースを参照してください。

* [情報ページ](sql-api-sdk-dotnet-changefeed.md) 
* [NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [前述の手順 1 ～ 6 を示すサンプル コード](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [GitHub のその他のサンプル](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

SDK で変更フィードを使用する方法については、次のリソースを参照してください。

* [SDK の情報ページ](sql-api-sdk-dotnet.md)
