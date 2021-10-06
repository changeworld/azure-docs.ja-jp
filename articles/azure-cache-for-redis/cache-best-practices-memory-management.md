---
title: メモリ管理のベスト プラクティス
titleSuffix: Azure Cache for Redis
description: Azure Cache for Redis のメモリを効果的に管理する方法について説明します。
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 36fe87e03a78a4dee34c2016b8f4723cb8aa95be
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598729"
---
# <a name="memory-management"></a>メモリ管理

## <a name="eviction-policy"></a>削除ポリシー

お使いのアプリケーションで機能する[削除ポリシー](https://redis.io/topics/lru-cache)を選択します。 Azure Cache for Redis の既定のポリシーは `volatile-lru` です。これは、TTL 値が設定されているキーのみが削除対象となることを意味します。  TTL 値を持つキーがない場合は、システムはどのキーも削除しません。  メモリ不足のときに任意のキーをシステムに削除させるようにする場合は、`allkeys-lru` ポリシーを検討することができます。

## <a name="keys-expiration"></a>キーの有効期限

キーに有効期限値を設定します。 有効期限が切れると、メモリ負荷が発生するまで待たずに、事前にキーが削除されます。  メモリ不足のために削除が開始される場合、サーバーにさらに多くの負荷が発生するおそれがあります。 詳細については、[EXPIRE](https://redis.io/commands/expire) コマンドと [EXPIREAT](https://redis.io/commands/expireat) コマンドに関するドキュメントを参照してください。

## <a name="minimize-memory-fragmentation"></a>メモリの断片化を最小限に抑える

大きな値を設定すると、削除のためにメモリが断片化され、メモリの使用量やサーバーの負荷が高くなる可能性があります。

## <a name="monitor-memory-usage"></a>メモリ使用量の監視

メモリ使用量の監視機能を追加して、メモリ不足に陥らないように、問題が発生する前にキャッシュをスケーリングできるようにします。

## <a name="configure-your-maxmemory-reserved-setting"></a>maxmemory-reserved の設定を構成する

システムの応答性を向上させるために、[maxmemory-reserved の設定](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)を構成します。

* 書き込み負荷の高いワークロードにとって、またはキャッシュに 100 KB 以上の値を格納している場合は特に、十分な予約設定にすることが重要です。 キャッシュのサイズの 10% から始めて、書き込みが多い場合はその割合を増やします。

*  `maxmemory-reserved`  設定は、フェールオーバーに伴うレプリケーションなどのキャッシュ以外の操作のために予約されているメモリの量をクラスター内のインスタンスあたりの MB 単位で構成するものです。 この値を設定すると、負荷が変化するときでも、Redis サーバーの稼働状態がより安定します。 大量のデータを書き込むワークロードでは、この値を高く設定する必要があります。 そのような操作に予約されているメモリは、キャッシュ データの保存には使用できません。

*  `maxfragmentationmemory-reserved`  設定では、メモリの断片化に対応するために予約されるメモリ量をクラスター内のインスタンスあたりの MB 単位で構成します。 この値を設定すると、キャッシュがいっぱいになった場合や、キャッシュがほとんどいっぱいで断片化の割合が高い場合でも、Redis サーバーの動作がより安定します。 そのような操作に予約されているメモリは、キャッシュ データの保存には使用できません。

* 新しいメモリ予約値 (`maxmemory-reserved`  または  `maxfragmentationmemory-reserved`) を選択する際には、この変更によって既に大量のデータが入っているキャッシュがどのような影響を受けるのかを考慮する必要があります。 たとえば、53 GB のキャッシュに 49 GB のデータが入っているときに、予約値を 8 GB に変更すると、システムで利用可能な最大メモリは 45 GB まで低下します。 現在の  `used_memory`  または  `used_memory_rss`  が新しい上限値の 45 GB よりも大きい場合、システムでは、 `used_memory`  と  `used_memory_rss`  の両方が 45 GB を下回るまでデータを削除しなければならなくなります。 削除することによってサーバーの負荷やメモリの断片化が増える可能性もあります。  `used_memory`  や  `used_memory_rss` [ などのキャッシュに関するメトリックの詳細については、「](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)使用可能なメトリックとレポート期間」を参照してください。

## <a name="next-steps"></a>次のステップ

* [開発のベスト プラクティス](cache-best-practices-development.md)
* [Azure Cache for Redis 開発に関してよくあるご質問](cache-development-faq.yml)
* [maxmemory-reserved の設定](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)
* [スケーリングに関するベスト プラクティス](cache-best-practices-scale.md)
