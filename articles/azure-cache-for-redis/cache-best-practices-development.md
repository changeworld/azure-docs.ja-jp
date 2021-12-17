---
title: 開発のベスト プラクティス
titleSuffix: Azure Cache for Redis
description: Azure Cache for Redis のコードを開発する方法について説明します。
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 11/3/2021
ms.author: shpathak
ms.openlocfilehash: 1c0c6b5f79e38dbd400dc048e2cd14cd2f52d35a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850985"
---
# <a name="development"></a>開発

## <a name="connection-resilience-and-server-load"></a>接続の回復力とサーバーの負荷

クライアント アプリケーションを開発する際には、[接続の回復力](cache-best-practices-connection.md)と[サーバー負荷の管理](cache-best-practices-server-load.md)に関連するベスト プラクティスを考慮してください。

## <a name="consider-more-keys-and-smaller-values"></a>キーを増やすことと値を小さくすることを検討する

Azure Cache for Redis は値が小さいとき最適に動作します。 データを複数のキーに分散させるには、より大きなデータ チャンクを小さいチャンクに分割することを検討してください。 理想的な値の大きさについては、[こちらの記事](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)を参照してください。

## <a name="large-request-or-response-size"></a>要求または応答のサイズが大きい

大きい要求/応答が原因でタイムアウトが発生することがあります。 例として、クライアントで構成されているタイムアウト値が 1 秒であるとします。 アプリケーションは同時に 2 つのキー ("A" と "B" など) を (同じ物理ネットワーク接続を使用して) 要求します。 ほとんどのクライアントでは、要求 'A' と要求 'B' の両方がそれぞれの応答を待たずに次々に送信される、要求の "パイプライン処理" がサポートされます。 サーバーは同じ順序で応答を返します。 応答 'A' が大きい場合、その応答は、以降の要求のためのタイムアウトのほとんどを消費する可能性があります。

次の例では、要求 'A' と 'B' がすばやくサーバーに送信されます。 サーバーは、すばやく応答 'A' と 'B' を送信し始めます。 データ転送時間のため、サーバーがすばやく応答したとしても、応答 'B' は応答 'A' が終了するまで待つ必要があります。

```console
|-------- 1 Second Timeout (A)----------|
|-Request A-|
     |-------- 1 Second Timeout (B) ----------|
     |-Request B-|
            |- Read Response A --------|
                                       |- Read Response B-| (**TIMEOUT**)
```

この要求/応答を測定するのは困難です。 大きい要求や応答を追跡するように、クライアント コードをインストルメント化する必要があります。

大きい応答サイズの解決策はさまざまですが、次のものが含まれます。

- アプリケーションを少数の大きい値ではなく、多数の小さい値用に最適化します。
    - データをより小さい関連値に分割することをお勧めします。
    - より小さい値が推奨される理由の詳細については、[Redis に最適な値のサイズ範囲は何ですか? 100 KB では大きすぎますか?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) に関する投稿を参照してください。
- より高い帯域幅機能を使用できるように VM のサイズを増やします。
    - クライアントまたはサーバー VM の帯域幅を増やすと、より大きい応答のデータ転送時間が削減される可能性があります。
    - 両方のコンピューターの現在のネットワーク使用量を現在の VM サイズの制限と比較します。 サーバーのみ、またはクライアントのみの帯域幅を増やすだけでは十分でない可能性があります。
- アプリケーションが使用する接続オブジェクトの数を増やします。
    - ラウンド ロビン方式を使用して、さまざまな接続オブジェクト経由で要求を発行します。

## <a name="key-distribution"></a>キーの配布

Redis クラスタリングを使用する予定の場合は、まず「[キーに関する Redis クラスタリングのベスト プラクティス](https://redislabs.com/blog/redis-clustering-best-practices-with-keys/)」をお読みください。

## <a name="use-pipelining"></a>パイプライン処理の使用

[Redis パイプライン処理](https://redis.io/topics/pipelining)をサポートする Redis クライアントを選択してみます。 パイプラインを使用すると、ネットワークを効率的に使用して、最高のスループットを実現できます。

## <a name="avoid-expensive-operations"></a>コストのかかる操作を避ける

一部の REDIS 操作 ([KEYS](https://redis.io/commands/keys) コマンドなど) はコストが高いため、避ける必要があります。 実行時間の長いコマンドに関する考慮事項については、「[実行時間の長いコマンド](cache-troubleshoot-server.md#long-running-commands)」を参照してください

## <a name="choose-an-appropriate-tier"></a>適切な層を選択する

実稼働システムでは Standard レベルまたは Premium レベルを使用します。  運用環境では Basic レベルを使用しないでください。 Basic レベルは単一ノード システムであり、データ レプリケーション機能や SLA がありません。 また、C1 以上のキャッシュを使用してください。 次の理由から、C0 キャッシュは単純な Dev/Test シナリオ専用です。

- CPU コアを共有する
- メモリをほとんど使用しない
- *近隣ノイズ* の問題の影響を受けやすい

適切な層を選択し、接続設定を検証するために、パフォーマンス テストを行うことをお勧めします。 詳細については、「[パフォーマンス テスト](cache-best-practices-performance.md)」を参照してください。

## <a name="client-in-same-region-as-cache"></a>キャッシュと同じリージョンのクライアント

キャッシュ インスタンスとアプリケーションを同じリージョンに配置します。 異なるリージョンのキャッシュに接続すると、待ち時間が大幅に増加し、信頼性が低下する可能性があります。  

Azure の外部から接続することはできますが、これは、*特に Redis をキャッシュとして使用しているときには* お勧めできません。  Redis サーバーを単なるキー/値ストアとして使用している場合は、待ち時間は一番の問題ではない可能性があります。

## <a name="use-tls-encryption"></a>TLS 暗号化を使用する

Azure Cache for Redis では、TLS で暗号化された通信が既定で必要です。 現在、TLS バージョン 1.0、1.1、および 1.2 がサポートされています。 ただし、TLS 1.0 と 1.1 は業界全体で非推奨になる予定であるため、可能であれば TLS 1.2 を使用してください。

お使いのクライアント ライブラリまたはツールで TLS がサポートされていない場合は、[Azure portal](cache-configure.md#access-ports) または[管理 API](/rest/api/redis/redis/update) を使用して、暗号化されていない接続を有効にすることができます。 暗号化された接続ができない場合は、キャッシュとクライアント アプリケーションを仮想ネットワークに配置することをお勧めします。 仮想ネットワーク キャッシュ シナリオで使用されるポートの詳細については、こちらの[表](cache-how-to-premium-vnet.md#outbound-port-requirements)を参照してください。

## <a name="client-library-specific-guidance"></a>クライアント ライブラリ固有のガイダンス

- [StackExchange.Redis (.NET)](cache-best-practices-connection.md#using-forcereconnect-with-stackexchangeredis)
- [Java - どちらのクライアントを使用すべきか](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
- [Lettuce (Java)](https://github.com/Azure/AzureCacheForRedis/blob/main/Lettuce%20Best%20Practices.md)
- [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
- [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
- [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
- [HiRedisCluster](https://github.com/Azure/AzureCacheForRedis/blob/main/HiRedisCluster%20Best%20Practices.md)
- [ASP.NET セッション状態プロバイダー](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)

## <a name="next-steps"></a>次の手順

- [Azure Cache for Redis 開発に関してよくあるご質問](cache-development-faq.yml)
- [パフォーマンス テスト](cache-best-practices-performance.md)
- [Azure Cache for Redis のフェールオーバーと修正プログラムの適用](cache-failover.md)
