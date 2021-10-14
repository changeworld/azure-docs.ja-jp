---
title: パフォーマンス テストのベスト プラクティス
titleSuffix: Azure Cache for Redis
description: Azure Cache for Redis のパフォーマンスをテストする方法について説明します。
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 5d2152b6e1b2da165303c3a309ca97bf0262b5ee
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129232781"
---
# <a name="performance-testing"></a>パフォーマンス テスト

1. 独自のパフォーマンス テストを記述する前に、まず `redis-benchmark.exe` を使用して、キャッシュの一般的なスループットと待機時間の特性を確認します。 詳細については、[Redis ベンチマーク](#redis-benchmark-utility)に関するセクションを参照してください。

1. テストに使用するクライアント VM は、Redis Cache インスタンスと *同じリージョン内* にある必要があります。

1. 使用するクライアント VM が、テストするキャッシュと *少なくとも同等のコンピューティングと帯域幅* を持っていることを確認します。

1. キャッシュのパフォーマンス テストは、安定状態の条件下のみで行わないようにすることが重要です。 *フェールオーバーの状態でもテスト* し、その間のキャッシュの CPU またはサーバーの負荷を測定します。 フェールオーバーを開始するには、[プライマリ ノードを再起動](cache-administration.md#reboot)します。 フェールオーバー状態でテストを行うと、フェールオーバー状態の間のアプリケーションのスループットと待機時間を確認できます。 フェールオーバーは、更新時や、計画外のイベント時に発生する可能性があります。 パフォーマンスに影響する可能性があるため、フェールオーバー中であっても、CPU またはサーバーの負荷ピークが 80% を超えないようにすることをお勧めします。

1. Premium レベルの Azure Cache for Redis インスタンスの使用を検討します。 これらのキャッシュ サイズでは、CPU およびネットワークの両方が優れたハードウェアで Redis インスタンスが実行されるため、ネットワーク待機時間とスループットが改善します。

   > [!NOTE]
   > 参照用として、当社で観測したパフォーマンス結果を[こちらに公開](./cache-planning-faq.yml#azure-cache-for-redis-performance)しています。 また、SSL/TLS では幾分かのオーバーヘッドが追加されるため、トランスポートの暗号化を使用している場合は、待ち時間とスループット、またはそのいずれかが異なる可能性があることに注意してください。

## <a name="redis-benchmark-utility"></a>Redis ベンチマーク ユーティリティ

**Redis ベンチマーク** のドキュメントは、[こちら](https://redis.io/topics/benchmarks)にあります。

`redis-benchmark.exe` では TLS はサポートされていません。 テストを実行する前に、[ポータルで非 TLS ポートを有効にする](cache-configure.md#access-ports)必要があります。 redis-benchmark.exe の Windows 互換バージョンは[こちら](https://github.com/MSOpenTech/redis/releases)にあります。

## <a name="redis-benchmark-examples"></a>Redis ベンチマークの例

**テスト前のセットアップ**: 待機時間とスループットのテストに必要なデータを使用してキャッシュ インスタンスを準備します。

```dos
redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024
```

**待機時間をテストするには**: 1 K のペイロードを使用して GET 要求をテストします。

```dos
redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4
```

**スループットをテストするには**: 1 K のペイロードを使用した、パイプラインされた GET 要求。

```dos
redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t  GET -n 1000000 -d 1024 -P 50  -c 50
```

## <a name="next-steps"></a>次のステップ

- [開発](cache-best-practices-development.md)
- [Azure Cache for Redis 開発に関してよくあるご質問](cache-development-faq.yml)
- [Azure Cache for Redis のフェールオーバーと修正プログラムの適用](cache-failover.md)
