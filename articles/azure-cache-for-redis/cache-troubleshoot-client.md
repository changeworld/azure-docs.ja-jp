---
title: Azure Cache for Redis のクライアント側の問題に関するトラブルシューティング
description: Redis クライアントのメモリ負荷、トラフィック増加、高い CPU、限られた帯域幅、要求または応答の肥大化など、クライアント側で頻繁に発生する問題を Azure Cache for Redis で解決する方法について説明します。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: bd0ca3b20cc37ecf2107e03eea5d6e4a62633f16
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129807189"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Azure Cache for Redis のクライアント側の問題に関するトラブルシューティング

このセクションでは、アプリケーションで使用する Redis クライアントの状態が原因で発生する問題のトラブルシューティングについて説明します。

- [Redis クライアントでのメモリ不足](#memory-pressure-on-redis-client)
- [トラフィックのバースト](#traffic-burst)
- [クライアントでの高い CPU 使用率](#high-client-cpu-usage)
- [クライアント側の帯域幅の制限](#client-side-bandwidth-limitation)
<!-- [Large request or response size](#large-request-or-response-size) -->

## <a name="memory-pressure-on-redis-client"></a>Redis クライアントでのメモリ不足

クライアント コンピューターでのメモリ不足は、キャッシュからの応答の処理を遅延させる可能性のあるすべての種類のパフォーマンスの問題につながります。 メモリ不足になると、システムはデータをディスクにページングする可能性があります。 この _ページ フォールト_ により、システムの処理速度が大幅に低下します。

クライアントでのメモリ不足を検出するには:

- コンピューターでのメモリ使用量を監視して、使用可能なメモリを超えることがないようにします。
- クライアントの `Page Faults/Sec` パフォーマンス カウンターを監視します。 通常の動作中、ほとんどのシステムでいくつかのページ フォールトが発生します。 要求タイムアウトに対応するページ フォールトのスパイクは、メモリ不足を示している場合があります。

クライアントでの深刻なメモリ不足は、次のいくつかの方法で緩和できます。

- メモリの使用パターンを詳しく調査して、クライアントでのメモリ使用量を削減します。
- クライアント VM をメモリ量の多いより大きいサイズにアップグレードします。

## <a name="traffic-burst"></a>トラフィックのバースト

`ThreadPool` の設定が適切でないトラフィックのバーストが原因で、Redis Server から既に送信されていてもクライアント側ではまだ使用されていないデータの処理に遅延が発生することがあります。

[`ThreadPoolLogger` の例](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs)を使用して、`ThreadPool` 統計が時間の経過と共にどのように変化するかを監視します。 さらに調査するには、次のように StackExchange.Redis の `TimeoutException` メッセージを使用できます。

```output
    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)
```

前の例外には、次のような興味深いいくつかの問題が存在します。

- `IOCP` セクションと `WORKER` セクションに、`Busy` 値が `Min` 値より大きいことが示されていることに注目してください。 この違いは、`ThreadPool` 設定に調整が必要なことを示します。
- `in: 64221`にも注目してください。 この値は、64,211 バイトがクライアントのカーネル ソケット レイヤーで受信されたが、まだアプリケーションによって読み取られていないことを示します。 この違いは、通常、アプリケーション (StackExchange.Redis など) がネットワークからデータを読み取る速度がサーバーのデータ送信速度より遅いことを示します。

バースト シナリオのもとでスレッド プールがすばやくスケールアップするように [`ThreadPool` 設定を構成](cache-management-faq.yml#important-details-about-threadpool-growth)できます。

## <a name="high-client-cpu-usage"></a>クライアントでの高い CPU 使用率

クライアントでの高い CPU 使用率は、実行を依頼されている作業にシステムがついていけないことを示します。 キャッシュがすばやく応答を送信したとしても、クライアントは適切なタイミングでその応答を処理できない可能性があります。

Azure Portal で使用可能なメトリックか、またはコンピューター上のパフォーマンス カウンターを使用して、クライアントのシステム全体での CPU 使用率を監視します。 1 つのプロセスの CPU 使用率は低くても、システム全体では高くなる場合があるため、*プロセス* CPU を監視しないように注意してください。 タイムアウトに対応する CPU 使用率が急激に増えていないか監視します。 CPU が高くなると、「[トラフィックのバースト](#traffic-burst)」セクションで説明されているように、`TimeoutException` エラー メッセージ内の `in: XXX` 値も高くなる可能性があります。

> [!NOTE]
> StackExchange.Redis 1.1.603 以降では、`TimeoutException` エラー メッセージに `local-cpu` メトリックが含まれます。 最新バージョンの [StackExchange.Redis NuGet パッケージ](https://www.nuget.org/packages/StackExchange.Redis/)を使用するようにしてください。 タイムアウトの信頼性を高めるため、コードに示されるバグは常に修正されています。したがって、最新バージョンを使用することが重要です。
>

クライアントの高い CPU 使用率を緩和するには:

- CPU スパイクの原因を調査します。
- クライアントを CPU 容量の多いより大きい VM サイズにアップグレードします。

## <a name="client-side-bandwidth-limitation"></a>クライアント側の帯域幅の制限

クライアント マシンには、そのアーキテクチャに応じて、それぞれ使用できるネットワーク帯域幅に制限がある場合があります。 クライアントがネットワーク容量を過負荷にすることで使用可能な帯域幅を超えている場合、データは、クライアント側でサーバーから送信される速度より速くは処理されません。 この状況により、タイムアウトが発生する場合があります。

[`BandwidthLogger` の例](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)を使用して、帯域幅の使用量が時間の経過と共にどのように変化するかを監視します。 (Azure Web サイトのような) アクセス許可が制限された一部の環境では、このコードが正常に実行されない場合があります。

緩和するには、ネットワーク帯域幅の消費を削減するか、またはクライアント VM サイズをネットワーク容量の多いサイズに増やします。

<!-- 
## Large request or response Size

A large request/response can cause timeouts. As an example, suppose your timeout value configured on your client is 1 second. Your application requests two keys (for example, 'A' and 'B') at the same time (using the same physical network connection). Most clients support request "pipelining", where both requests 'A' and 'B' are sent one after the other without waiting for their responses. The server sends the responses back in the same order. If response 'A' is large, it can eat up most of the timeout for later requests.

In the following example, request 'A' and 'B' are sent quickly to the server. The server starts sending responses 'A' and 'B' quickly. Because of data transfer times, response 'B' must wait behind response 'A' times out even though the server responded quickly.

```console
|-------- 1 Second Timeout (A)----------|
|-Request A-|
     |-------- 1 Second Timeout (B) ----------|
     |-Request B-|
            |- Read Response A --------|
                                       |- Read Response B-| (**TIMEOUT**)
```

This request/response is a difficult one to measure. You could instrument your client code to track large requests and responses.

Resolutions for large response sizes are varied but include:

1. Optimize your application for a large number of small values, rather than a few large values.
    - The preferred solution is to break up your data into related smaller values.
    - See the post [What is the ideal value size range for redis? Is 100 KB too large?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) for details on why smaller values are recommended.
1. Increase the size of your VM to get higher bandwidth capabilities
    - More bandwidth on your client or server VM may reduce data transfer times for larger responses.
    - Compare your current network usage on both machines to the limits of your current VM size. More bandwidth on only the server or only on the client may not be enough.
1. Increase the number of connection objects your application uses.
    - Use a round-robin approach to make requests over different connection objects.

 -->
## <a name="additional-information"></a>関連情報

- [Azure Cache for Redis のサーバー側の問題に関するトラブルシューティング](cache-troubleshoot-server.md)
- [キャッシュのベンチマークを実行およびテストする方法](cache-management-faq.yml#how-can-i-benchmark-and-test-the-performance-of-my-cache-)
