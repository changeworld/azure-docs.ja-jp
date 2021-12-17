---
title: 接続の回復力に関するベスト プラクティス
titleSuffix: Azure Cache for Redis
description: Azure Cache for Redis 接続の回復性を高める方法について学習します。
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 11/3/2021
ms.author: shpathak
ms.openlocfilehash: d8e5f95e78db7c46ad1c52401b938acc37af6b4f
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850947"
---
# <a name="connection-resilience"></a>接続の回復力

## <a name="retry-commands"></a>コマンドを再試行する

指数バックオフを使用してコマンドを再試行するようにクライアント接続を構成します。 詳細については、[再試行のガイドライン](/azure/architecture/best-practices/retry-service-specific#azure-cache-for-redis)を参照してください。

## <a name="test-resiliency"></a>回復性をテストする

[再起動](cache-administration.md#reboot)を使用して修正プログラムをシミュレートし、接続の中断に対するシステムの回復性をテストします。 パフォーマンスのテストについて詳しくは、「[パフォーマンス テスト](cache-best-practices-performance.md)」を参照してください。

## <a name="tcp-settings-for-linux-hosted-client-applications"></a>Linux でホストされるクライアント アプリケーションのための TCP の設定

Linux の一部のバージョンでは、既定でオプティミスティック TCP の設定が使用されます。 TCP の設定により、接続を正常に閉じる前に Redis サーバーが応答を停止したときに、キャッシュへのクライアント接続を長時間再確立できない状況が発生する可能性があります。 Azure Cache for Redis のプライマリ ノードが計画外のメンテナンスなどで使用できなくなった場合、接続の再確立が失敗することがあります。

次のように TCP を設定することをお勧めします。

|設定  |値 |
|---------|---------|
| *net.ipv4.tcp_retries2*   | 5 |

このシナリオの詳細については、「[Linux で実行している場合、15 分間接続が再確立されない](https://github.com/StackExchange/StackExchange.Redis/issues/1848#issuecomment-913064646)」を参照してください。 この説明は StackExchange.Redis ライブラリについてですが、Linux で実行されている他のクライアント ライブラリも影響を受けます。 この説明はやはり有効であり、他のライブラリに一般化できます。

## <a name="using-forcereconnect-with-stackexchangeredis"></a>ForceReconnect と StackExchange. Redis の使用

接続が切断された後に、StackExchange. Redis が再接続できないことがまれにあります。 このような場合は、クライアントを再起動するか、新しい `ConnectionMultiplexer` を作成することで、問題が修正されます。 アプリによって定期的な再接続が強制的に行われるのを許可するのと同時に、シングルトン `ConnectionMultiplexer` パターンを使用することをお勧めします。 アプリケーションが使用するフレームワークとプラットフォームに最適なクイックスタート サンプル プロジェクトを見てみましょう。 このコード パターンの例については、「[クイックスタート](https://github.com/Azure-Samples/azure-cache-redis-samples)」を参照してください。

`ConnectionMultiplexer` のユーザーは、古いものを破棄した結果として発生する可能性のある `ObjectDisposedException` エラーを処理する必要があります。

`RedisConnectionExceptions` および `RedisSocketExceptions` に対して `ForceReconnectAsync()` を呼び出し ます。 また、`RedisTimeoutExceptions` に対して `ForceReconnectAsync()` を呼び出すこともできます。ただし、豊富な `ReconnectMinInterval` および `ReconnectErrorThreshold` を使用している場合に限ります。 それ以外の場合、新しい接続を確立すると、既にオーバーロードされているためにタイムアウトになったサーバーでカスケード障害が発生する可能性があります。

## <a name="configure-appropriate-timeouts"></a>適切なタイムアウトを構成する

接続の回復性では、[接続タイムアウト](#connect-timeout)と[コマンド タイムアウト](#command-timeout)の 2 つのタイムアウト値を考慮する必要があります。

### <a name="connect-timeout"></a>Connect timeout

''`connect timeout`'' とは、クライアントが Redis サーバーとの接続が確立されるまで待機する時間のことです。 5 秒間の `connect timeout` を使用するようにクライアント ライブラリを構成してください。これにより、CPU 使用率が高い状況下でも、システムに接続するのに十分な時間が得られます。

''`connection timeout`'' 値が小さいと、その期間での接続の確立が保証されません。 何らかの問題 (高いクライアント CPU 使用率、高いサーバー CPU 使用率など) がある場合、`connection timeout` の値が短いと、接続試行が失敗します。 この動作は、多くの場合、悪い状況をさらに悪化させます。 タイムアウトを短くすると、役に立つ代わりに、システムに強制的に再接続試行プロセスを再開させることにより "*接続 -> 失敗 -> 再試行*" のループに陥る可能性があり、問題が悪化します。

### <a name="command-timeout"></a>コマンド タイムアウト

ほとんどのクライアント ライブラリには、''`command timeouts`'' 用に別のタイムアウト構成があります。これは、クライアントが Redis サーバーからの応答を待機する時間です。 初期設定は 5 秒未満にすることをお勧めしますが、シナリオとキャッシュに格納されている値のサイズに応じて、`command timeout` を高い値または低い値に設定することを検討してください。

''`command timeout`'' が小さすぎると、接続が不安定に見えることがあります。 しかし、`command timeout` の値が大きすぎると、アプリケーションはコマンドがタイムアウトになるかどうかを確認するために、長時間待機する必要がある可能性があります。

## <a name="avoid-client-connection-spikes"></a>クライアント接続のスパイクを回避する

接続が失われた後に再接続するときに、同時に多くの接続を作成することは避けてください。 [接続タイムアウトが短い](#configure-appropriate-timeouts)と停止時間が長くなる可能性があるのと同じように、同時に多くの再接続試行を開始すると、サーバーの負荷が増え、すべてのクライアントが正常に再接続されるまでにかかる時間が長くなる場合もあります。

多くのクライアント インスタンスを再接続する場合は、接続されているクライアントの数が急増しないように、新しい接続のタイミングをずらすことを検討してください。

> [!NOTE]
> `StackExchange.Redis` クライアント ライブラリを使用する場合は、接続文字列で `abortConnect` を `false` に設定します。  `ConnectionMultiplexer` ハンドルの再接続を行うことをお勧めします。 詳細については、[StackExchange.Redis のベスト プラクティス](./cache-management-faq.yml#stackexchangeredis-best-practices)に関するページを参照してください。

## <a name="avoid-leftover-connections"></a>未使用の接続を避ける

キャッシュには、キャッシュ層あたりのクライアント接続数の制限があります。 クライアント アプリケーションによって接続が再作成される際に、古い接続が閉じられ、削除されているようにしてください。

## <a name="advance-maintenance-notification"></a>メンテナンスの事前通知

通知を使用して、今後のメンテナンスを確認します。 詳細については、「[計画メンテナンスを前もって通知できますか?](cache-failover.md#can-i-be-notified-in-advance-of-planned-maintenance)」を参照してください。

## <a name="schedule-maintenance-window"></a>メンテナンス期間のスケジュール

メンテナンスに対応するようにキャッシュ設定を調整します。 キャッシュへの悪影響を減らすためにメンテナンス期間を作成する方法の詳細については、「[更新のスケジュール](cache-administration.md#schedule-updates)」を参照してください。

## <a name="more-design-patterns-for-resilience"></a>回復性のためのその他の設計パターン

回復性のための設計パターンを適用します。 詳細については、「[アプリケーションの回復性を高める方法](cache-failover.md#how-do-i-make-my-application-resilient)」を参照してください。

## <a name="idle-timeout"></a>アイドル タイムアウト

現在、Azure Cache for Redis の接続のアイドル タイムアウトは 10 分であるため、クライアント アプリケーションのアイドル タイムアウト設定は 10 分未満にする必要があります。 最も一般的なクライアント ライブラリには、クライアント ライブラリが Redis `PING` コマンドを Redis サーバーに自動で定期的に送信できるようにする構成設定があります。 ただし、この種の設定なしでクライアント ライブラリを使用する場合は、お客様のアプリケーション自体が接続を維持する必要があります。

## <a name="next-steps"></a>次のステップ

- [開発のベスト プラクティス](cache-best-practices-development.md)
- [Azure Cache for Redis 開発に関してよくあるご質問](cache-development-faq.yml)
- [フェールオーバーと修正プログラムの適用](cache-failover.md)