---
title: 接続の回復力に関するベスト プラクティス
titleSuffix: Azure Cache for Redis
description: Azure Cache for Redis 接続の回復性を高める方法について学習します。
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: a0dd6e3e8f4c2a7645da1ceccf77f7607d2b84b3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656951"
---
# <a name="connection-resilience"></a>接続の回復力

## <a name="retry-commands"></a>コマンドを再試行する

指数バックオフを使用してコマンドを再試行するようにクライアント接続を構成します。 詳細については、[再試行のガイドライン](/azure/architecture/best-practices/retry-service-specific#azure-cache-for-redis)を参照してください。

## <a name="test-resiliency"></a>回復性をテストする

[再起動](cache-administration.md#reboot)を使用して修正プログラムをシミュレートし、接続の中断に対するシステムの回復性をテストします。 パフォーマンスのテストについて詳しくは、「[パフォーマンス テスト](cache-best-practices-performance.md)」を参照してください。

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
> `StackExchange.Redis` クライアント ライブラリを使用する場合は、接続文字列で `abortConnect` を `false` に設定します。  `ConnectionMultiplexer` ハンドルの再接続を行うことをお勧めします。 詳細については、[StackExchange.Redis のベスト プラクティス](/azure/azure-cache-for-redis/cache-management-faq#stackexchangeredis-best-practices)に関するページを参照してください。

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
