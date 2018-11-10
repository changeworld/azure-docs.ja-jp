---
title: Azure Cosmos DB Java Async SDK の診断およびトラブルシューティング| Microsoft Docs
description: クライアント側ログ、他のサード パーティのツールなどの機能を使って、Azure Cosmos DB の問題を特定、診断、およびトラブルシューティングします。
services: cosmos-db
author: moderakh
ms.service: cosmos-db
ms.topic: troubleshoot
ms.date: 10/28/2018
ms.author: moderakh
ms.devlang: java
ms.component: cosmosdb-sql
ms.openlocfilehash: ef1d2d0751bf1b1a7ee88fbf37e44e6316dee8f8
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249873"
---
# <a name="troubleshooting-issues-when-using-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>Azure Cosmos DB SQL API アカウントで Java Async SDK を使用する場合の問題のトラブルシューティング
この記事では、Azure Cosmos DB SQL API アカウントで [Java Async ADK](sql-api-sdk-async-java.md) を使用するときの共通の問題、回避策、診断手順、およびツールについて説明します。
Java Async SDK では、Azure Cosmos DB SQL API にアクセスするクライアント側の論理表現を提供します。 この記事では、問題が発生した場合に役立つツールとアプローチについて説明します。

次の一覧から開始します。
    * この記事の[一般的な問題と対処法]のセクションを確認します。
    * SDK は [github のオープン ソース](https://github.com/Azure/azure-cosmosdb-java)であり、積極的に監視する[問題のセクション](https://github.com/Azure/azure-cosmosdb-java/issues)があります。 回避策が既に提出済みの同様の問題がないか確認してください。
    * [パフォーマンスに関するヒント](performance-tips-async-java.md)をレビューし、推奨されるやり方に従います。
    * この記事の残りの部分に従い、解決策が見つからない場合は、 [GitHub の問題](https://github.com/Azure/azure-cosmosdb-java/issues)を提出します。

## <a name="common-issues-workarounds"></a>一般的な問題と対処法

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>ネットワークの問題、Netty の読み取りタイムアウト エラーの発生、低いスループット、長い待機時間

#### <a name="general-suggestions"></a>一般的な推奨事項
* アプリが Cosmos DB アカウントと同じリージョンで実行されていることを確認します。 
* アプリが実行されているホストの CPU 使用率を確認します。 CPU 使用率が 90% 以上の場合は、高度な構成を持つホストでアプリの実行を検討するか、別のマシンに負荷を分散させます。

#### <a name="connection-throttling"></a>接続の帯域幅調整
接続の帯域幅調整は、[ホスト コンピューターの接続制限]、または [Azure SNAT (PAT) ポート不足]のいずれかが原因で発生します。

##### <a name="connection-limit-on-host"></a>ホスト コンピューターの接続制限
一部の Linux システム (Red Hat など) では、開くファイルの最大数に上限があります。 Linux のソケットはファイルとして実装されるため、この数は接続の合計数も制限します。
次のコマンドを実行します。

```bash
ulimit -a
```
開いているファイルの数 ("nofile") を十分な大きさとする必要があります (少なくとも接続プール サイズの倍)。 詳細は、[パフォーマンスに関するヒント](performance-tips-async-java.md)を参照してください。

##### <a name="snat"></a>Azure SNAT (PAT) ポート不足

アプリを Azure VM に展開した場合、既定では VM 外の任意のエンドポイントへの接続を確立するために [Azure SNAT ポート](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)が使用されます。 VM から Cosmos DB エンドポイントへの許可される接続の数は、[Azure SNAT 構成](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)によって制限されます。

Azure SNAT ポートが使用されるのは、Azure VM がプライベート IP アドレスを持ち、VM からのプロセスが、パブリック IP アドレスへの接続を確立しようとしている場合に限られます。 そのため、Azure SNAT の制限を回避するには次の 2 つの回避策があります。
    * [VNET サービス エンドポイントを有効にする](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)に関するページで説明したように、Azure VM VNET のサブネットに Azure Cosmos DB サービス エンドポイントを追加します。 サービス エンドポイントが有効なとき、要求はパブリック IP から cosmos DB に送信されなくなり、代わりに VNET およびサブネット ID が送信されます。 この変更により、パブリック IP のみが許可された場合はファイアウォール ドロップが発生することがあります。 ファイアウォールを使用している場合は、サービス エンドポイントを有効にするとき、[VNET ACLs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl) を使用してサブネットをファイアウォールに追加します。
    * Azure VM にパブリック IP を割り当てます。

#### <a name="http-proxy"></a>HTTP プロキシ

HttpProxy を使用する場合、HttpProxy が SDK `ConnectionPolicy` で構成されている接続の数をサポートできるか確認します。
できない場合、接続の問題が発生します。

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>コーディング パターンが無効です: Netty IO スレッドのブロック

SDK は [Netty](https://netty.io/) IO ライブラリを使用して、Azure Cosmos DB サービスと通信します。 Async API が存在し、非ブロッキング IO API である netty を使用します。 SDK の IO 操作は IO netty スレッドで実行されます。 IO netty スレッドの数は、アプリマシンの CPU コアの数と同じに構成されます。 netty IO スレッドは、非ブロッキング netty IO 処理のためだけに使用されます。 SDK は、netty IO スレッドの 1 つの API 呼び出しの結果をアプリのコードに返します。 アプリが netty スレッドで結果を受信した後、netty スレッドで時間のかかる動作を実行した場合、SDK が内部の IO 処理を実行するための十分な数の IO スレッドを持たなくなることがあります。 このようなアプリのコーディングでは、スループットが低く、待ち時間が長く、 `io.netty.handler.timeout.ReadTimeoutException` 障害が発生することがあります。 回避策は、操作に時間がかかることがわかっている場合にスレッドを切り替えることです。

   たとえば、次のコード スニペットでは、数ミリ秒以上の時間のかかる処理を netty スレッドで実行する場合、最終的には IO 処理を行う netty IO スレッドが存在しない状態になることがあり、結果として ReadTimeoutException を受け取ります。
```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // time consuming work. For example:
                        // writing to a file, computationally heavy work, or just sleep
                        // basically anything which takes more than a few milliseconds
                        // doing such operation on the IO netty thread
                        // without a proper scheduler, will cause problems.
                        // The subscriber will get ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //will be io.netty.handler.timeout.ReadTimeoutException
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
   回避策は、時間のかかる作業を実行するスレッドを変更することです。 アプリのスケジューラのシングルトン インスタンスを定義します。
   ```java
// have a singleton instance of executor and scheduler
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   時間のかかる処理を行う必要がある場合 (たとえば、IO をブロックする計算負荷の高い処理)、`.observeOn(customScheduler)` API を使用して、`customScheduler` によって提供される worker にスレッドを切り替えます。
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // switches the thread.
        .subscribe(
            // ...
        );
```
`observeOn(customScheduler)` を使用することによって、netty IO スレッドを解放し、customScheduler によって提供される独自のカスタム スレッドに切り替えます。 この変更によって問題が解決され、`io.netty.handler.timeout.ReadTimeoutException` エラーが取得されなくなります。

### <a name="connection-pool-exhausted-issue"></a>接続プール不足の問題

`PoolExhaustedException` はクライアント側のエラーです。 このエラーが頻繁に発生する場合、SDK 接続プールが対応できるよりもアプリのワークロードが高いことを示しています。 接続プールのサイズを増やすか、複数のアプリに負荷を分散すると解決する場合があります。

### <a name="request-rate-too-large"></a>要求率が大きすぎる
このエラーはサーバー側のエラーで、プロビジョニングされたスループットが消費されたため後で再試行する必要があることを示しています。 このエラーが頻繁に発生する場合、コレクション スループットを増やすことを検討してください。

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Azure Cosmos DB エミュレーターへの接続の失敗

Cosmos DB エミュレーターの HTTPS 証明書が自己署名されています。 SDK でエミュレーターを動作させるには、エミュレーター証明書を Java トラスト ストアにインポートする必要があります。 説明は[こちら](local-emulator-export-ssl-certificates.md)にあります。


## <a name="enable-client-sice-logging"></a>クライアント SDK のログ記録を有効にする

async Java SDK は、log4j や logback などの一般的なログ記録フレームワークへのログ記録をサポートするロギング ファサードとして SLF4j を使用 します。

たとえば、ログ記録フレームワークとして log4j を使用する場合は、Java classpath に次の libs を追加します。

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

また log4j の構成を追加します。
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

詳細については、[sfl4j ロギング マニュアル](https://www.slf4j.org/manual.html)をレビューしてください。

## <a name="netstats"></a>OS ネットワーク統計
netstat コマンドを実行して、`Established` 状態や `CLOSE_WAIT` 状態などの接続がいくつあるか把握します。

Linux では、次のコマンドを実行できます。
```bash
netstat -nap
```
Cosmos DB エンドポイントへの接続だけを示すように結果をフィルター処理します。

一見したところ、`Established` 状態の Cosmos DB エンドポイントへの接続の数は、構成済みの接続プール サイズより大きくありません。

`CLOSE_WAIT` 状態の Cosmos DB エンドポイントへの接続数が多い場合 (例えば 1000 件を超える)、接続が確立されてすぐに破棄されることを示しており、問題が発生する可能性があります。 詳細については、[一般的な問題と対処法]のセクションをレビューしてください。

 <!--Anchors-->
[一般的な問題と対処法]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[ホスト コンピューターの接続制限]: #connection-limit-on-host
[Azure SNAT (PAT) ポート不足]: #snat


