---
title: Azure Cosmos DB での Change Feed サポートの使用 | Microsoft Docs
description: Azure Cosmos DB の Change Feed サポートを使用して、ドキュメントの変更を追跡し、トリガーなどのイベント ベースの処理を実行したり、キャッシュや分析システムを最新の状態に維持したりします。
keywords: Change Feed
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: 6b0aaa075b8b2881e269d79a67e75528d0d9a86a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129860"
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

*   [Azure Functions の使用](#azure-functions)
*   [Azure Cosmos DB SDK の使用](#sql-sdk)
*   [Azure Cosmos DB 変更フィード プロセッサ ライブラリの使用](#change-feed-processor)

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

<a id="sql-sdk"></a>
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

> [!NOTE]
> `ChangeFeedOptions.PartitionKeyRangeId` の代わりに `ChangeFeedOptions.PartitionKey` を使用して、変更フィードを取得するパーティション キーを 1 つ指定できます。 たとえば、「`PartitionKey = new PartitionKey("D8CFA2FD-486A-4F3E-8EA6-F3AA94E5BD44")`」のように入力します。
> 
>

複数の閲覧者がいる場合、**ChangeFeedOptions** を使用して、読み取りの負荷を異なるスレッドや異なるクライアントに分散させることができます。

以上の数行のコードだけで、変更フィードの読み取りを開始できます。 この記事に使用されているコードの完成版は、[GitHub リポジトリ](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed)から入手できます。

前述の手順 4 のコードで、最終行の **ResponseContinuation** には、ドキュメントの最終論理シーケンス番号 (LSN) が含まれています。このシーケンス番号は、次にその番号の後の新しいドキュメントを読み取るときに使用されます。 **ChangeFeedOption** の **StartTime** を使用することで、より広い範囲のドキュメントを取得できます。 そのため、**ResponseContinuation** を null にして、**StartTime** を過去に変更すると、**StartTime** 以降に変更されたすべてのドキュメントが取得されます。 ただし、**ResponseContinuation** に値が指定されている場合、その LSN 以降のすべてのドキュメントが取得されます。

そのため、チェックポイントの配列は各パーティションの LSN のみを維持しています。 ただし、パーティション、チェックポイント、LSN、開始時間などを扱わない場合は、変更フィード プロセッサ ライブラリを使用する方法が簡単です。

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>変更フィード プロセッサ ライブラリの使用 

[Azure Cosmos DB 変更フィード プロセッサ ライブラリ](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed)を使用すると、複数のコンシューマーにイベント処理を簡単に分散させることができます。 このライブラリで、並列で実行されている複数のパーティションと複数のスレッド全体の変更の読み取りが簡単になります。

変更フィード プロセッサ ライブラリの主な利点は、各パーティションと継続トークンを管理する必要がないことと、各コレクションを手動でポーリングする必要がないことです。

変更フィード プロセッサ ライブラリにより、並列で実行されている複数のパーティションと複数のスレッド全体の変更の読み取りが簡単になります。  リース メカニズムを使用して、パーティション全体の変更の読み取りが自動的に管理されます。 次の図のように、変更フィード プロセッサ ライブラリを使用している 2 つのクライアントを起動すると、2 つのクライアント間で作業が分割されます。 クライアント数を増やし続けると、クライアント全体で作業が分割されます。

![Azure Cosmos DB の Change Feed の分散処理](./media/change-feed/change-feed-output.png)

左側のクライアントが最初に起動され、すべてのパーティションの監視が開始されます。次に、2 つ目のクライアントが起動され、1 つ目のクライアントは 2 つ目のクライアントにリースの一部を渡します。 この図からわかるように、複数のコンピューターとクライアント間で作業を分散させるにはこの方法が推奨されます。

同じコレクションを監視し、同じリースを使用する 2 つのサーバーレス Azure 関数がある場合、プロセッサ ライブラリがパーティションを処理する方法によっては、2 つの関数が異なるドキュメントを取得する可能性があります。

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>変更フィード プロセッサ ライブラリの概要

変更フィード プロセッサ ライブラリの実装には、監視対象コレクション、リース コレクション、プロセッサ ホスト、コンシューマーの 4 つの主要コンポーネントがあります。 

> [!WARNING]
> コレクションの作成は料金に影響します。これは、Azure Cosmos DB と通信するためにアプリケーションのスループットを予約するためです。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/cosmos-db/)をご覧ください。
> 
> 

**監視対象コレクション:** 監視対象コレクションは、Change Feed の生成元のデータです。 監視対象コレクションに対する挿入と変更は、コレクションの Change Feed に反映されます。 

**リース コレクション:** リース コレクションは、複数の worker 間で Change Feed の処理を調整します。 個別のコレクションを使用して、リース (パーティションごとに 1 リース) が保存されます。 このリース コレクションは、変更フィード プロセッサ が実行される場所の近くに書き込みリージョンを持つ別のアカウントに保存すると有利です。 リース オブジェクトには次の属性があります。 
* Owner: リースを所有するホストを指定します。
* Continuation: 特定のパーティションの Change Feed 内の位置 (継続トークン) を指定します。
* Timestamp: リースの最終更新時刻。タイムスタンプを使用して、リースを期限切れと見なすかどうかを確認できます。 

**プロセッサ ホスト:** 各ホストは、アクティブなリースを保持するホストの他のインスタンスの数に基づいて、処理するパーティションの数を決定します。 
1.  すべてのホスト間でワークロードのバランスを取るために、ホストは起動時にリースを取得します。 ホストは定期的にリースを更新するので、リースはアクティブな状態に維持されます。 
2.  ホストは、読み取りを実行するたびにリースに対して前回の継続トークンのチェックポイント処理を実行します。 同時実行の安全性を確保するために、ホストはリースを更新するたびに ETag をチェックします。 他のチェックポイント戦略もサポートされています。  
3.  シャットダウン時に、ホストはすべてのリースを解放します。ただし、継続情報は保持されるので、後で保存済みのチェックポイントから読み取りを再開できます。 

現時点では、ホストの数がパーティション (リース) の数を超えることはできません。

**コンシューマー:** コンシューマー (worker) は、各ホストによって開始された Change Feed の処理を実行するスレッドです。 プロセッサ ホストごとに複数のコンシューマーを使用できます。 各コンシューマーは、割り当てられているパーティションから Change Feed を読み取り、変更と期限切れのリースをホストに通知します。

変更フィード プロセッサのこれら 4 要素の連携のしくみについて理解を深めるために、次の図の例を見てみましょう。 監視対象コレクションでは、ドキュメントを保存し、パーティション キーとして "city" を使用します。 青色のパーティションには、"A-E" の "city" フィールドを含むドキュメントが格納されていることがわかります (他のパーティションも同様)。 2 つのホストがあり、各ホストは 4 つのパーティションから並列で読み取る 2 つのコンシューマーを使用しています。 矢印は、コンシューマーが Change Feed の特定の場所から読み取ることを示しています。 最初のパーティションでは、濃い青はまだ読み取られていない変更を表し、薄い青は Change Feed で既に読み取られた変更を表しています。 ホストはリース コレクションを使用して "Continuation" 値を保存することで、各コンシューマーの現在の読み取り位置を追跡します。 

![Azure Cosmos DB Change Feed プロセッサ ホストの使用](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>変更フィード プロセッサ ライブラリの使用

変更フィード プロセッサ NuGet パッケージをインストールする前に、まず以下をインストールします。 

* Microsoft.Azure.DocumentDB、最新バージョン。
* Newtonsoft.Json、最新バージョン

次に [Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget パッケージ](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)をインストールし、参照に追加します。

変更フィード プロセッサ ライブラリを実装するには、次の手順を実行する必要があります。

1. **DocumentFeedObserver** オブジェクトを実装します。これで **IChangeFeedObserver** が実装されます。
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;
    using Microsoft.Azure.Documents.Client;

    /// <summary>
    /// This class implements the IChangeFeedObserver interface and is used to observe 
    /// changes on change feed. ChangeFeedEventHost will create as many instances of 
    /// this class as needed. 
    /// </summary>
    public class DocumentFeedObserver : IChangeFeedObserver
    {
    private static int totalDocs = 0;

        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserver" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        /// <param name="client"> Client connected to destination collection </param>
        /// <param name="destCollInfo"> Destination collection information </param>
        public DocumentFeedObserver()
        {
            
        }

        /// <summary>
        /// Called when change feed observer is opened; 
        /// this function prints out observer partition key id. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task OpenAsync(IChangeFeedObserverContext context)
        {
            Console.ForegroundColor = ConsoleColor.Magenta;
            Console.WriteLine("Observer opened for partition Key Range: {0}", context.PartitionKeyRangeId);
            return Task.CompletedTask;
        }

        /// <summary>
        /// Called when change feed observer is closed; 
        /// this function prints out observer partition key id and reason for shut down. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <param name="reason">Specifies the reason the observer is closed.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task CloseAsync(IChangeFeedObserverContext context, ChangeFeedObserverCloseReason reason)
        {
            Console.ForegroundColor = ConsoleColor.Cyan;
            Console.WriteLine("Observer closed, {0}", context.PartitionKeyRangeId);
            Console.WriteLine("Reason for shutdown, {0}", reason);
            return Task.CompletedTask;
        }

        public Task ProcessChangesAsync(IChangeFeedObserverContext context, IReadOnlyList<Document> docs, CancellationToken cancellationToken)
        {
            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine("Change feed: PartitionId {0} total {1} doc(s)", context.PartitionKeyRangeId, Interlocked.Add(ref totalDocs, docs.Count));
            foreach (Document doc in docs)
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine(doc.Id.ToString());
            }

            return Task.CompletedTask;
        }
    }
    ```

2. **DocumentFeedObserverFactory** オブジェクトを実装します。これで **IChangeFeedObserverFactory** が実装されます。
    ```csharp
     using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;

    /// <summary>
    /// Factory class to create instance of document feed observer. 
    /// </summary>
    public class DocumentFeedObserverFactory : IChangeFeedObserverFactory
    {
        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserverFactory" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        public DocumentFeedObserverFactory()
        {
        }

        /// <summary>
        /// Creates document observer instance with client and destination collection information
        /// </summary>
        /// <returns>DocumentFeedObserver with client and destination collection information</returns>
        public IChangeFeedObserver CreateObserver()
        {
            DocumentFeedObserver newObserver = new DocumentFeedObserver();
            return newObserver as IChangeFeedObserver;
        }
    }
    ```

3. *CancellationTokenSource* と *ChangeFeedProcessorBuilder* を定義します

    ```csharp
    private readonly CancellationTokenSource cancellationTokenSource = new CancellationTokenSource();
    private readonly ChangeFeedProcessorBuilder builder = new ChangeFeedProcessorBuilder();
    ```

5. 関連オブジェクトを定義した後、**ChangeFeedProcessorBuilder** をビルドします 

    ```csharp
            string hostName = Guid.NewGuid().ToString();
      
            // monitored collection info 
            DocumentCollectionInfo documentCollectionInfo = new DocumentCollectionInfo
            {
                Uri = new Uri(this.monitoredUri),
                MasterKey = this.monitoredSecretKey,
                DatabaseName = this.monitoredDbName,
                CollectionName = this.monitoredCollectionName
            };
            
            DocumentCollectionInfo leaseCollectionInfo = new DocumentCollectionInfo
                {
                    Uri = new Uri(this.leaseUri),
                    MasterKey = this.leaseSecretKey,
                    DatabaseName = this.leaseDbName,
                    CollectionName = this.leaseCollectionName
                };
            DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory();
            ChangeFeedOptions feedOptions = new ChangeFeedOptions();

            /* ie customize StartFromBeginning so change feed reads from beginning
                can customize MaxItemCount, PartitonKeyRangeId, RequestContinuation, SessionToken and StartFromBeginning
            */

            feedOptions.StartFromBeginning = true;
        
            ChangeFeedProcessorOptions feedProcessorOptions = new ChangeFeedProcessorOptions();

            // ie. customizing lease renewal interval to 15 seconds
            // can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay 
            feedProcessorOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);

            this.builder
                .WithHostName(hostName)
                .WithFeedCollection(documentCollectionInfo)
                .WithLeaseCollection(leaseCollectionInfo)
                .WithProcessorOptions (feedProcessorOptions)
                .WithObserverFactory(new DocumentFeedObserverFactory());               
                //.WithObserver<DocumentFeedObserver>();  If no factory then just pass an observer

            var result =  await this.builder.BuildAsync();
            await result.StartAsync();
            Console.Read();
            await result.StopAsync();    
            ```

That’s it. After these few steps documents will start showing up into the **DocumentFeedObserver.ProcessChangesAsync** method.

Above code is for illustration purpose to show different kind of objects and their interaction. You have to define proper variables and initiate them with correct values. You can get the complete code used in this article from the [GitHub repo](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessorV2).

> [!NOTE]
> You should never have a master key in your code or in config file as shown in above code. Please see [how to use Key-Vault to retrive the keys](https://sarosh.wordpress.com/2017/11/23/cosmos-db-and-key-vault/).


## FAQ

### What are the different ways you can read Change Feed? and when to use each method?

There are three options for you to read change feed:

* **[Using Azure Cosmos DB SQL API .NET SDK](#sql-sdk)**
   
   By using this method, you get low level of control on change feed. You can manage the checkpoint, you can access a particular partition key etc. If you have multiple readers, you can use [ChangeFeedOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) to distribute read load to different threads or different clients. .

* **[Using the Azure Cosmos DB change feed processor library](#change-feed-processor)**

   If you want to outsource lot of complexity of change feed then you can use change feed processor library. This library hides lot of complexity, but still gives you complete control on change feed. This library follows an [observer pattern](https://en.wikipedia.org/wiki/Observer_pattern), your processing function is called by the SDK. 

   If you have a high throughput change feed, you can instantiate multiple clients to read the change feed. Because you are using “change feed processor library”, it will automatically divide the load among different clients. You do not have to do anything. All the complexity is handled by SDK. However, if you want to have your own load balancer, then you can implement IParitionLoadBalancingStrategy for custom partition strategy. Implement IPartitionProcessor – for custom processing changes on a partition. However, with SDK, you can process a partition range but if you want to process a particular partition key then you have to use SDK for SQL API.

* **[Using Azure Functions](#azure-functions)** 
   
   The last option Azure Function is the simplest option. We recommend using this option. When you create an Azure Cosmos DB trigger in an Azure Functions app, you select the Azure Cosmos DB collection to connect to and the function is triggered whenever a change to the collection is made. watch a [screen cast](https://www.youtube.com/watch?v=Mnq0O91i-0s&t=14s) of using Azure function and change feed

   Triggers can be created in the Azure Functions portal, in the Azure Cosmos DB portal, or programmatically. Visual Studio and VS Code has great support to write Azure Function. You can write and debug the code on your desktop, and then deploy the function with one click. For more information, see [Azure Cosmos DB: Serverless database computing using Azure Functions](serverless-computing-database.md) article.

### What is the sort order of documents in change feed?

Change feed documents comes in order of their modification time. This sort order is guaranteed only per partition.

### For a multi-region account, what happens to the change feed when the write-region fails-over? Does the change feed also failover? Would the change feed still appear contiguous or would the fail-over cause change feed to reset?

Yes, change feed will work across the manual failover operation and it will be contiguous.

### How long change feed persist the changed data if I set the TTL (Time to Live) property for the document to -1?

Change feed will persist forever. If data is not deleted, it will remain in change feed.

### How can I configure Azure functions to read from a particular region, as change feed is available in all the read regions by default?

Currently it’s not possible to configure Azure Functions to read from a particular region. There is a GitHub issue in the Azure Functions repo to set the preferred regions of any Azure Cosmos DB binding and trigger.

Azure Functions uses the default connection policy. You can configure connection mode in Azure Functions and by default, it reads from the write region, so it is best to co-locate Azure Functions on the same region.

### What is the default size of batches in Azure Functions?

100 documents at every invocation of Azure Functions. However, this number is configurable within the function.json file. Here is complete [list of configuration options](../azure-functions/functions-run-local.md). If you are developing locally, update the application settings within the [local.settings.json](../azure-functions/functions-run-local.md) file.

### I am monitoring a collection and reading its change feed, however I see I am not getting all the inserted document, some documents are missing. What is going on here?

Please make sure that there is no other function reading the same collection with the same lease collection. It happened to me, and later I realized the missing documents are processed by my other Azure functions, which is also using the same lease.

Therefore, if you are creating multiple Azure Functions to read the same change feed then they must use different lease collection or use the “leasePrefix” configuration to share the same collection. However, when you use change feed processor library you can start multiple instances of your function and SDK will divide the documents between different instances automatically for you.

### My document is updated every second, and I am not getting all the changes in Azure Functions listening to change feed.

Azure Functions polls change feed for every 5 seconds, so any changes made between 5 seconds are lost. Azure Cosmos DB stores just one version for every 5 seconds so you will get the 5th change on the document. However, if you want to go below 5 second, and want to poll change Feed every second, You can configure the polling time “feedPollTime”, see [Azure Cosmos DB bindings](../azure-functions/functions-bindings-cosmosdb.md#trigger---configuration). It is defined in milliseconds with a default of 5000. Below 1 second is possible but not advisable, as you will start burning more CPU.

### I inserted a document in the Mongo API collection, but when I get the document in change feed, it shows a different id value. What is wrong here?

Your collection is Mongo API collection. Remember, change feed is read using the SQL client and serializes items into JSON format. Because of the JSON formatting, MongoDB clients will experience a mismatch between BSON formatted documents and the JSON formatted change feed. You are seeing is the representation of a BSON document in JSON. If you use binary attributes in a Mongo accounts, they are converted to JSON.

### Is there a way to control change feed for updates only and not inserts?

Not today, but this functionality is on roadmap. Today, you can add a soft marker on the document for updates.

### Is there a way to get deletes in change feed?

Currently change feed doesn’t log deletes. Change feed is continuously improving, and this functionality is on roadmap. Today, you can add a soft marker on the document for delete. Add an attribute on the document called “deleted” and set it to “true” and set a TTL on the document so that it can be automatically deleted.

### Can I read change feed for historic documents(for example, documents that were added 5 years back) ?

Yes, if the document is not deleted you can read the change feed as far as the origin of your collection.

### Can I read change feed using JavaScript?

Yes, Node.js SDK initial support for change feed is recently added. It can be used as shown in the following example, please update documentdb module to current version before you run the code:

```js

var DocumentDBClient = require('documentdb').DocumentClient;
const host = "https://your_host:443/";
const masterKey = "your_master_key==";
const databaseId = "db";
const collectionId = "c1";
const dbLink = 'dbs/' + databaseId;
const collLink = dbLink + '/colls/' + collectionId;
var client = new DocumentDBClient(host, { masterKey: masterKey });
let options = {
    a_im: "Incremental feed",
    accessCondition: {
        type: "IfNoneMatch",        // Use: - empty condition (or remove accessCondition entirely) to start from beginning.
        //      - '*' to start from current.
        //      - specific etag value to start from specific continuation.
        condition: ""
    }
};
 
var query = client.readDocuments(collLink, options);
query.executeNext((err, results, headers) =&gt; {
    // Now we have headers.etag, which can be used in next readDocuments in accessCondition option.
    console.log(results);
    console.log(headers.etag);
    console.log(results.length);
    options.accessCondition = { type: "IfNoneMatch", condition: headers.etag };
    var query = client.readDocuments(collLink, options);
    query.executeNext((err, results, headers) =&gt; {
        console.log("next one:", results[0]);
    });
});<span id="mce_SELREST_start" style="overflow:hidden;line-height:0;"></span>

```

### <a name="can-i-read-change-feed-using-java"></a>Java を使って変更フィードを読み取ることは可能ですか?

変更フィードを読み取るための Java ライブラリは、[Github リポジトリ](https://github.com/Azure/azure-documentdb-changefeedprocessor-java)に用意されています。 ただし、現在の Java ライブラリは .NET ライブラリから数バージョン遅れています。 両方のライブラリは、近日中に同期される予定です。

### <a name="can-i-use-etag-lsn-or-ts-for-internal-bookkeeping-which-i-get-in-response"></a>応答で取得する内部ブックキーピング用の _etag、_lsn、_ts は利用できますか?

_etag は内部形式であり、いつ変更されてもおかしくないため、これに依存することはできません (解析しないでください)。
_ts は変更または作成のタイムスタンプです。 _ts は時系列比較に利用できます。
_lsn は変更フィードのみに追加されるバッチ ID であり、ストアからのトランザクション ID を表します。 _lsn は多くのドキュメントで同じである可能性があります。
もう 1 つ注意すべきことは、FeedResponse の ETag がドキュメントの _etag とは異なることです。 _etag は同時実行に使用される内部識別子であり、ドキュメントのバージョンを伝えますが、ETag はフィードのシーケンス処理に使用されます。

### <a name="does-reading-change-feed-add-any-additional-cost-"></a>変更フィードの読み取りには追加コストがかかりますか?

課金対象は使用 RU です。Azure Cosmos DB コレクション内外へのデータの移動には常に RU が使用されます。 ユーザーは、リース コレクションが使用する分の RU を課金されます。

### <a name="can-multiple-azure-functions-read-one-collections-change-feed"></a>複数の Azure 関数が 1 つのコレクションの変更フィードを読み取ることは可能ですか?

はい。 複数の Azure 関数が同一コレクションの変更フィードを読み取ることができます。 ただし、Azure 関数には leaseCollectionPrefix を個別に定義する必要があります。

### <a name="should-the-lease-collection-be-partitioned"></a>リース コレクションをパーティション分割するべきですか?

いいえ、リース コレクションは固定できます。 パーティション分割されたリース コレクションは不要であり、現時点ではサポートされていません。

### <a name="can-i-read-change-feed-from-spark"></a>Spark からの変更フィードを読み取ることは可能ですか?

はい、できます。 詳しくは、[Azure Cosmos DB Spark コネクタ](spark-connector.md)に関する記事を参照してください。 こちらの[スクリーン キャスト](https://www.youtube.com/watch?v=P9Qz4pwKm_0&t=1519s)は、変更フィードを構造化ストリームとして処理する方法を示しています。

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-say-a-batch-of-10-documents-and-i-get-an-error-at-7th-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-documentie-7th-document-in-my-next-feed"></a>変更フィードの処理に Azure 関数を使用していますが、たとえば、10 ドキュメントのバッチで 7 番目のドキュメントでエラーが発生するとします。 この場合、最後の 3 ドキュメントは処理されません。次回のフィードで失敗したドキュメント ( 7 番目のドキュメント) から処理を開始するにはどうすればよいですか?

エラーへの対応でお勧めできるパターンは、try-catch ブロックでコードをラップすることです。 エラーをキャッチしてドキュメントをキュー (配達不能) に配置し、エラーを生じたドキュメントに対応するロジックを定義します。 この方法であれば、200 ドキュメントのバッチで 1 つのドキュメントのみが失敗する場合でも、バッチ全体を無駄にする必要はありません。

エラーが生じても、チェックポイントを先頭まで巻き戻さないでください。これらのドキュメントは、変更フィードから取得し続けることができます。 変更フィードは、ドキュメントの最終スナップショットを維持します。そのために、ドキュメントの以前のスナップショットを失っても問題はありません。 変更フィードは、ドキュメント最終バージョンのみを維持し、間に別の処理が行われると、ドキュメントは変更されます。

コードの修正を続けていると、配達不能キュー内のドキュメントがすぐになくなることに気が付くはずです。
Azure 関数は変更フィード システムによって自動的に呼び出され、チェックポイントなどは Azure 関数によって内部的に維持されます。 チェックポイントをロールバックしてすべての側面をコントロールするのであれば、変更フィード プロセッサ SDK の使用を検討してください。


## <a name="next-steps"></a>次の手順

Azure Cosmos DB と Azure Functions の使用方法については、「[Azure Cosmos DB: Azure Functions を使用したサーバーなしのデータベースのコンピューティング](serverless-computing-database.md)」を参照してください。

変更フィード プロセッサ ライブラリの使用方法については、次のリソースを参照してください。

* [情報ページ](sql-api-sdk-dotnet-changefeed.md) 
* [NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [前述の手順 1 ～ 6 を示すサンプル コード](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [GitHub のその他のサンプル](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

SDK で変更フィードを使用する方法については、次のリソースを参照してください。

* [SDK の情報ページ](sql-api-sdk-dotnet.md)
