---
title: Azure Cache for Redis のトラブルシューティング | Microsoft Docs
description: Azure Cache for Redis のデータ損失に関する問題を解決する方法について説明します
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/17/2019
ms.author: yegu
ms.openlocfilehash: 507a8aef36e4ea5db0b4fdecb54876158398d96a
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795247"
---
# <a name="troubleshoot-data-loss"></a>データ損失に関するトラブルシューティング

このセクションでは、Azure Cache for Redis で発生する可能性がある実際のデータ損失または認識されたデータ損失を診断する方法について説明します。

- [部分的なキーの損失](#partial-loss-of-keys)
- [大部分または完全なキーの損失](#major-or-complete-loss-of-keys)

> [!NOTE]
> このガイドのトラブルシューティング手順のいくつかには、Redis コマンドを実行し、さまざまなパフォーマンス メトリックを監視する手順が含まれています。 詳細および手順については、「 [追加情報](#additional-information) 」セクションの記事を参照してください。
>

## <a name="partial-loss-of-keys"></a>部分的なキーの損失

Redis では、メモリに格納されたキーがランダムに削除されることはありません。 キーの削除は、有効期限や削除のポリシー、および明示的なキーの削除コマンドに応じて行われます。 さらに、Premium または Standard の Azure Cache for Redis のマスター ノードに書き込まれたキーは、レプリカですぐに使用可能にならない可能性があります。 データは、非同期および非ブロッキング方式でマスターからレプリカにレプリケートされます。

キャッシュからキーが消失していることに気付いた場合は、原因を特定するために次のことを確認してください。

| 原因 | 説明 |
|---|---|
| [キーの有効期限](#key-expiration) | 設定されているタイムアウトが原因でキーが削除された |
| [キーの強制削除](#key-eviction) | メモリ不足のためにキーが削除された |
| [キーの削除](#key-deletion) | 明示的な削除コマンドによってキーが削除された |
| [非同期レプリケーション](#async-replication) | データ レプリケーションの遅延のため、レプリカでキーが使用できない |

### <a name="key-expiration"></a>キーの有効期限

Redis は、タイムアウトが割り当てられ、その期間が経過したキーを自動的に削除します。 Redis キーの有効期限の詳細については、[EXPIRE](http://redis.io/commands/expire) コマンドのドキュメントを参照してください。 タイムアウト値は、[SET](http://redis.io/commands/set)、[SETEX](https://redis.io/commands/setex)、[GETSET](https://redis.io/commands/getset)、およびその他の \*STORE コマンドを使用して設定することもできます。

[INFO](http://redis.io/commands/info) コマンドを使用すると、有効期限が切れたキーの数に関する統計を取得できます。 *Stats* セクションに、有効期限が切れたキーの総数が示されます。 *Keyspace* セクションには、タイムアウトが設定されているキーの数と、平均タイムアウト値に関する追加情報が提供されます。

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

さらに、キャッシュの診断メトリックを参照して、キーが消失したタイミングと、有効期限が切れたキーの急増との間に相関関係があるかどうかを確認することもできます。 キースペース通知や MONITOR を使用してこれらの種類の問題をデバッグする方法については、「[付録](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)」を参照してください。

### <a name="key-eviction"></a>キーの強制削除

Redis には、データを格納するためのメモリ領域が必要です。 必要に応じて、使用可能なメモリを解放するためにキーが消去されます。 [INFO](http://redis.io/commands/info) コマンドの **used_memory** または **used_memory_rss** の値が、構成されている **maxmemory** 設定に近づくと、Redis は [キャッシュ ポリシー](http://redis.io/topics/lru-cache)に基づいて、メモリからのキーの強制削除を開始します。

[INFO](http://redis.io/commands/info) コマンドを使用して、強制削除されたキーの数を監視できます。

```
# Stats

evicted_keys:13224
```

さらに、キャッシュの診断メトリックを参照して、キーが消失したタイミングと、強制削除されたキーの急増との間に相関関係があるかどうかを確認することができます。 キースペース通知や MONITOR を使用してこれらの種類の問題をデバッグする方法については、「[付録](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)」を参照してください。

### <a name="key-deletion"></a>キーの削除

Redis クライアントは、[DEL](http://redis.io/commands/del) コマンドまたは [HDEL](http://redis.io/commands/hdel) コマンドを発行して、Redis から明示的にキーを削除することができます。 [INFO](http://redis.io/commands/info) コマンドを使用して、削除操作の回数を追跡できます。 DEL コマンドまたは HDEL コマンドが呼び出された場合は、*Commandstats* セクションに一覧表示されます。

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>非同期レプリケーション

Standard レベルまたは Premium レベルの Azure Cache for Redis は、マスター ノードと少なくとも 1 つのレプリカを使用して構成されます。 バックグラウンド プロセスを使用して、データがマスターからレプリカに非同期的にコピーされます。 [redis.io](http://redis.io/topics/replication) Web サイトでは、Redis データ レプリケーションの一般的な動作について説明しています。 クライアントが Redis に頻繁に書き込みを行っているシナリオでは、このレプリケーションは瞬間的に行われることが保証されているため、部分的なデータ損失が発生する可能性があります。 たとえば、クライアントによってキーがマスターに書き込まれた "_後_"、バックグラウンド プロセスによってこのキーがレプリカに送信される "_前_" に、マスターがダウンした場合、キーは、レプリカが新しいマスターとして引き継いだときに消失します。

## <a name="major-or-complete-loss-of-keys"></a>大部分または完全なキーの損失

キャッシュからほとんど、またはすべてのキーが消失していることに気付いた場合は、原因を特定するために次のことを確認できます。

| 原因 | 説明 |
|---|---|
| [キーのフラッシュ](#key-flushing) | キーは手動で削除された |
| [データベースの選択が正しくない](#incorrect-database-selection) | Redis が既定以外のデータベースを使用するように設定されている |
| [Redis インスタンスのエラー](#redis-instance-failure) | 明示的な削除コマンドによってキーが削除された |

### <a name="key-flushing"></a>キーのフラッシュ

クライアントは、[FLUSHDB](http://redis.io/commands/flushdb) コマンドを呼び出して、**1 つの**データベース内のすべてのキーを削除することも、[FLUSHALL](http://redis.io/commands/flushall) を使用して Redis キャッシュ内の**すべての**データベースからすべてのキーを削除することもできます。 [INFO](http://redis.io/commands/info) コマンドを使用して、キーがフラッシュされているかどうかを確認することができます。 *Commandstats* セクションに、FLUSH コマンドが呼び出されているかどうかが示されます。

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>データベースの選択が正しくない

Azure Cache for Redis では、既定で **db0** データベースが使用されます。 別のデータベース (db1 など) に切り替えて、そこからキーを読み取ろうとしても、そこでは検出されません。これは、すべてのデータベースが論理的に独立したユニットであり、異なるデータセットを保持しているためです。 [SELECT](http://redis.io/commands/select) コマンドを使用して、他の使用可能なデータベースを使用し、そのそれぞれでキーを検索してください。

### <a name="redis-instance-failure"></a>Redis インスタンスのエラー

Redis は、インメモリ データ ストアです。 データは、Redis をホストする物理マシンまたは仮想マシン上に保持されます。 Basic レベルの Azure Cache for Redis インスタンスは、1 つの仮想マシン (VM) 上でのみ実行されます。 その VM がダウンすると、キャッシュに格納されているすべてのデータが失われます。 Standard レベルと Premium レベルのキャッシュでは、レプリケートされた構成で 2 つの VM を使用することにより、データ損失に対してより高い回復性が提供されます。 そのようなキャッシュのマスター ノードで障害が発生すると、レプリカ ノードが自動的に引き継いでデータを提供します。 これらの VM は、両方が同時に使用できなくなる可能性を最小限に抑えるために、別々のフォールト ドメインと更新ドメインに配置されます。 ただし、それでも大規模なデータセンターの停止が発生した場合には、VM は一緒にダウンする可能性があります。 これらのまれなケースでは、データは消失します。

これらのインフラストラクチャ障害に対するデータの保護を強化するには、[Redis のデータ永続化](http://redis.io/topics/persistence)および [geo レプリケーション](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication)を使用することを検討してください。

## <a name="additional-information"></a>追加情報

- [Azure Cache for Redis のサーバー側の問題に関するトラブルシューティング](cache-troubleshoot-server.md)
- [Azure Cache for Redis のサービス内容と適切なサイズの選択](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Azure Cache for Redis を監視する方法](cache-how-to-monitor.md)
- [Redis コマンドの実行方法](cache-faq.md#how-can-i-run-redis-commands)
