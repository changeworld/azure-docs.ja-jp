---
title: Azure Cache for Redis を監視する
description: Azure Cache for Redis のインスタンスの正常性とパフォーマンスを監視する方法を学習します
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 64234292051c5f780c33fd55fabf3305f45b756d
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538409"
---
# <a name="monitor-azure-cache-for-redis"></a>Azure Cache for Redis を監視する

Azure Cache for Redis は、[Azure Monitor](../azure-monitor/index.yml) を使用して、キャッシュのインスタンスを監視するための複数のオプションを提供します。 これらのツールによって、Azure Cache for Redis インスタンスの正常性を監視でき、キャッシュ アプリケーションの管理が容易になります。

Azure Monitor は次の目的で使用します。

- メトリックを表示する
- メトリック グラフをスタート画面にピン留めする
- 監視グラフの日付と時間の範囲をカスタマイズする
- グラフのメトリックを追加または削除する
- 特定の条件が満たされたときにアラートを設定する

Azure Cache for Redis インスタンスのメトリックは、Redis [INFO](https://redis.io/commands/info) コマンドを使用して収集されます。 メトリックは 1 分あたり約 2 回収集され、自動的に 30 日間格納されるため、メトリック グラフに表示してアラート ルールによって評価することができます。

別の保持ポリシーを構成するには、「[キャッシュ メトリックをエクスポートする](#export-cache-metrics)」を参照してください。  

各キャッシュ メトリックで使用される各種 INFO コマンドの詳細については、「 [使用可能なメトリックとレポート期間](#available-metrics-and-reporting-intervals)」を参照してください。

<a name="view-cache-metrics"></a>

キャッシュ メトリックを確認するには、[Azure Portal](https://portal.azure.com) で対象のキャッシュ インスタンスに[移動](cache-configure.md#configure-azure-cache-for-redis-settings)します。  Azure Cache for Redis では、左側に **[概要]** と **[Redis メトリック]** を使用した組み込みのグラフがいくつか用意されています。 各グラフは、メトリックの追加や削除、レポート期間の変更など、カスタマイズすることができます。

![6 個のグラフが表示されます。 そのうちの 1 つは、過去 1 時間のキャッシュ ヒットとキャッシュ ミスです。](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>事前に構成されているメトリック グラフを表示する

左側の **[概要]** には、事前に構成されている次の監視グラフが用意されています。

- [監視グラフ](#monitoring-charts)
- [使用状況グラフ](#usage-charts)

### <a name="monitoring-charts"></a>監視グラフ

左側の **[概要]** の **[監視]** セクションには、 **[ヒット数とミス数]** 、 **[取得数と設定数]** 、 **[接続数]** 、および **[コマンド合計数]** のグラフがあります。

![監視グラフ](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>使用状況グラフ

左側の **[概要]** の **[使用状況]** セクションには、 **[Redis サーバーの負荷]** 、 **[メモリ使用量]** 、 **[ネットワーク帯域幅]** 、および **[CPU 使用率]** のグラフのほか、キャッシュ インスタンスの **[価格レベル]** も表示されます。

![使用状況グラフ](./media/cache-how-to-monitor/redis-cache-usage-part.png)

**[価格レベル]** にはキャッシュの価格レベルが表示され、キャッシュを別の価格レベルに [スケーリング](cache-how-to-scale.md) するために使用できます。

## <a name="view-metrics-charts-for-all-your-caches-with-azure-monitor-for-azure-cache-for-redis"></a>Azure Monitor for Azure Cache for Redis ですべてのキャッシュのメトリック グラフを表示する

[Azure Monitor for Azure Cache for Redis](../azure-monitor/insights/redis-cache-insights-overview.md) (プレビュー) を使用すると、すべての Azure Cache for Redis リソースの全体的なパフォーマンス、障害、容量、操作の正常性を表示できます。 個々のリソースの詳細にドリルダウンできる、カスタマイズ可能で統合された対話型のエクスペリエンスでメトリックを表示します。 Azure Monitor for Azure Cache for Redis は、メトリックやその他のデータの豊富な視覚化を提供する [Azure Monitor のブック機能](../azure-monitor/visualize/workbooks-overview.md)に基づいています。 詳細については、記事「[Azure Monitor for Azure Cache for Redis を調べる](../azure-monitor/insights/redis-cache-insights-overview.md)」を参照してください。

## <a name="view-metrics-with-azure-monitor-metrics-explorer"></a>Azure Monitor メトリックス エクスプローラーでメトリックを表示する

Azure Monitor for Azure Cache for Redis の完全な柔軟性が不要なシナリオでは、代わりに Azure Monitor メトリックス エクスプローラーを使用してメトリックを表示し、カスタム グラフを作成できます。 **[リソース メニュー]** から **[メトリック]** を選択し、希望のメトリック、レポート間隔、グラフの種類などを使用してグラフをカスタマイズします。

![Contoso55 の左側のナビゲーション ウィンドウで、[メトリック] は [監視] のオプションであり、強調表示されています。 [メトリック] では、メトリックの一覧が表示されます。 [キャッシュ ヒット] と [キャッシュ ミス] が選択されています。](./media/cache-how-to-monitor/redis-cache-monitor.png)

Azure Monitor を使用してメトリックを操作する方法について詳しくは、「[Microsoft Azure のメトリックの概要](../azure-monitor/data-platform.md)」をご覧ください。

<a name="enable-cache-diagnostics"></a>

## <a name="export-cache-metrics"></a>キャッシュ メトリックをエクスポートする

既定では、Azure Monitor のキャッシュ メトリックは [30 日間格納](../azure-monitor/essentials/data-platform-metrics.md)され、その後削除されます。 キャッシュ メトリックを 30 日を超えて保持するには、[ストレージ アカウントを指定](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)し、対象のキャッシュ メトリックの **[リテンション期間 (日数)]** ポリシーを指定します。

対象のキャッシュ メトリックのストレージ アカウントを構成するには:

1. **[Azure Cache for Redis]** ページの **[監視]** 見出しの下にある **[診断]** を選択します。
1. **[+ 診断設定の追加]** を選択します。
1. 設定に名前を付けます。
1. **[ストレージ アカウントへのアーカイブ]** をオンにします。 診断データをストレージ アカウントに送信する際には、ストレージおよびトランザクションの通常のデータ料金が発生します。
1. **[構成]** を選択してキャッシュ メトリックを格納するストレージ アカウントを選択します。
1. 表の見出しの **[メトリック]** で、**AllMetrics** など、保存する品目の横にあるチェック ボックスをオンにします。 **[保有期間 (日)]** ポリシーを指定します。 指定できる最大保有期間は **365 日** です。 ただし、メトリック データを永続的に保持する場合は、 **[保有期間 (日)]** を「**0**」に設定します。
1. **[保存]** を選択します。

![Redis 診断](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>キャッシュ メトリックをストレージにアーカイブする以外に、[イベント ハブにストリーム配信したり、Azure Monitor ログに送信したり](../azure-monitor/essentials/rest-api-walkthrough.md#retrieve-metric-values)できます。
>

メトリックにアクセスするには、この記事の前の方で説明されているように、それらを Azure portal で表示できます。 また、[Azure Monitor Metrics REST API](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md) を使用してそれらにアクセスすることもできます。

> [!NOTE]
> ストレージ アカウントを変更すると、以前に構成されたストレージ アカウント内のデータは引き続きダウンロードできますが、Azure ポータルには表示されなくなります。  
>

## <a name="available-metrics-and-reporting-intervals"></a>使用可能なメトリックとレポート期間

キャッシュ メトリックは、 **[過去 1 時間]** 、 **[今日]** 、 **[過去 1 週間]** 、 **[カスタム]** などの期間でレポートが作成されます。 左側の各メトリック グラフの **[メトリック]** には、グラフ内の各メトリックの平均値、最小値、および最大値が表示され、一部のメトリックでは指定のレポート期間における合計が表示されます。

各メトリックには 2 つのバージョンがあります。 1 つ目のメトリックは、キャッシュ全体と、[クラスタリング](cache-how-to-premium-clustering.md)を使用するキャッシュのパフォーマンスを測定し、名前に `(Shard 0-9)` が含まれる 2 つ目のバージョンのメトリックは、キャッシュ内の 1 つのシャードのパフォーマンスを測定します。 たとえば、キャッシュに 4 つのシャードがある場合、`Cache Hits` はキャッシュ全体の総ヒット数で、`Cache Hits (Shard 3)` はキャッシュの当該シャードのヒット数のみとなります。

> [!NOTE]
> 集計の種類を表示している場合は、次のようになります。
>
> - "Count" には 2 が表示され、これは時間粒度 (1 分) でメトリックが 2 つのデータ ポイントを受信したことを示しています。
> - "Max" は、時間粒度のデータ ポイントの最大値を示します。
> - "Min" は、時間粒度のデータ ポイントの最小値を示します。
> - "Average" は、時間粒度内のすべてのデータ ポイントの平均値を示します。
> - "Sum" は、時間粒度内のすべてのデータ ポイントの合計を示しますが、特定のメトリックによっては誤解を招く可能性があります。
> 通常の条件下では、"Average" と "Max" は非常に似ています。これは、1 つのノード (マスター ノード) だけがこれらのメトリックを出力するためです。 接続されたクライアントの数が急激に変化するシナリオでは、"Max"、"Average"、および "Min" の値は大きく異なりますが、これも想定される動作です。
>
> 一般に、"Average" を使用すると、目的のメトリックのスムーズなグラフが表示され、時間粒度の変化にもよく反応します。 "Max" と "Min" は、時間粒度が大きい場合はメトリックの大きな変化が隠れてしまうことがありますが、小さい時間粒度で使用すれば、メトリックの大きな変化が発生した正確な時刻を特定するのに役立ちます。
>
> "Count" と "Sum" は、(接続されたクライアントなどの) 特定のメトリックでは誤解を招く可能性があります。
>
> そのため、Sum メトリックではなく Average メトリックを確認することをお勧めします。

> [!NOTE]
> キャッシュがアイドル状態で、アクティブなクライアント アプリケーションに接続されていない場合でも、接続されているクライアント、メモリの使用状況、実行中の操作などのキャッシュ アクティビティが表示されることがあります。 これは Azure Cache for Redis インスタンスの処理中に普通に発生するアクティビティです。
>
>

| メトリック | 説明 |
| --- | --- |
| キャッシュ ヒット数 |指定したレポート期間中に、成功したキー検索の数。 この番号は、Redis [INFO](https://redis.io/commands/info) コマンドの `keyspace_hits` にマップされます。 |
| キャッシュの待機時間 (プレビュー) | キャッシュのノード間待機時間を使用して計算されるキャッシュの待機時間。 このメトリックはマイクロ秒単位で測定され、`Avg`、`Min`、および `Max` の 3 つのディメンションがあります。 これらのディメンションは、指定されたレポート間隔のキャッシュの平均、最短、および最長の待機時間を表します。 |
| キャッシュ ミス数 |指定したレポート期間中に、失敗したキー検索の数。 この番号は、Redis INFO コマンドの `keyspace_misses` にマップされます。 キャッシュ ミスは、必ずしもキャッシュに問題があるということではありません。 たとえば、キャッシュ アサイド プログラミング パターンを使用する場合、アプリケーションはまず項目をキャッシュから検索します。 項目が見つからなかった (キャッシュ ミス) 場合は、データベースから項目を取得して、次回使用するためにキャッシュに追加されます。 キャッシュ ミスは、キャッシュ アサイド プログラミング パターンでは普通の動作です。 キャッシュ ミスの数が予想よりも大きい場合は、キャッシュにデータを入力またはキャッシュからデータを読み取るアプリケーション ロジックを確認してください。 メモリ不足のためにキャッシュから項目が削除される場合はキャッシュ ミスとしてカウントされますが、メモリの負荷の監視には `Used Memory` または `Evicted Keys` のメトリックが適しています。 |
| キャッシュの読み取り |指定したレポート期間中にキャッシュから読み取られた、メガバイト単位での 1 秒あたりのデータ量 (MB/秒)。 この値は、キャッシュをホストする仮想マシンをサポートするネットワーク インターフェイス カードから派生し、Redis 固有のものではありません。 **この値は、このキャッシュで使用されるネットワーク帯域幅に対応しています。サーバー側のネットワーク帯域幅の制限に対してアラートを設定する場合は、この `Cache Read` カウンターを使用してアラートを作成します。キャッシュのさまざまな価格レベルとサイズで観測された帯域幅の制限値については、[こちらの表](./cache-planning-faq.yml#azure-cache-for-redis-performance)を参照してください。** |
| キャッシュの書き込み |指定したレポート期間中にキャッシュに書き込まれた、メガバイト単位での 1 秒あたりのデータ量 (MB/秒)。 この値は、キャッシュをホストする仮想マシンをサポートするネットワーク インターフェイス カードから派生し、Redis 固有のものではありません。 この値は、クライアントからキャッシュに送信されるデータのネットワーク帯域幅に対応しています。 |
| 接続されているクライアント数 |指定したレポート期間中に、キャッシュに接続されるクライアントの数。 この番号は、Redis INFO コマンドの `connected_clients` にマップされます。 [接続の上限](cache-configure.md#default-redis-server-configuration)に達すると、それ以降のキャッシュへの接続の試行は失敗します。 アクティブなクライアント アプリケーションがない場合でも、内部処理や接続によって接続されたクライアントのインスタンスが少数見つかることがあります。 |
| CPU |指定したレポート期間中の Azure Cache for Redis サーバーの CPU 使用率 (パーセント)。 この値は、オペレーティング システム `\Processor(_Total)\% Processor Time` パフォーマンス カウンターにマッピングされます。 |
| エラー | 指定されたレポート間隔中にキャッシュで発生する可能性がある特定のエラーおよびパフォーマンスの問題。 このメトリックには、さまざまなエラーの種類を表す 8 つのディメンションがありますが、今後追加される可能性があります。 現在表されているエラーの種類は次のとおりです。 <br/><ul><li>**Failover**: キャッシュがフェールオーバーした場合 (下位からプライマリへの昇格)</li><li>**Dataloss**: キャッシュにデータ損失がある場合</li><li>**UnresponsiveClients**: 十分な速さでクライアントがサーバーからデータを読み取っていない場合</li><li>**AOF**: AOF 永続化に関連する問題がある場合</li><li>**RDB**: RDB 永続化に関連する問題がある場合</li><li>**Import**: RDB のインポートに関連する問題がある場合</li><li>**Export**: RDB のエクスポートに関連する問題がある場合</li></ul> |
| 削除されたキー数 |指定したレポート期間中に、`maxmemory` の制限によってキャッシュから削除された項目の数。 この番号は、Redis INFO コマンドの `evicted_keys` にマップされます。 |
| 期限切れキー数 |指定したレポート期間中に、期限が切れたキャッシュの項目の数。 この値は Redis INFO コマンドの `expired_keys` にマッピングされます。|
| 取得数 |指定したレポート期間中に、キャッシュから実行された取得操作の数。 この値は、Redis INFO のすべてのコマンド (`cmdstat_get`、`cmdstat_hget`、`cmdstat_hgetall`、`cmdstat_hmget`、`cmdstat_mget`、`cmdstat_getbit`、および `cmdstat_getrange`) からの値の合計で、レポート期間中のキャッシュ ヒット数とキャッシュ ミス数の合計と同じです。 |
| 1 秒あたりの操作回数 | 指定したレポート期間中に、キャッシュ サーバーによって処理されたコマンドの合計数/秒。  この値は Redis INFO コマンドから "instantaneous_ops_per_sec" にマップされます。 |
| Redis サーバーの負荷 |Redis サーバーの処理がビジーで、アイドル状態でメッセージを待機していないサイクルの割合。 このカウンタが 100 に達すると、Redis サーバーのパフォーマンスが上限に達したことを意味し、これ以上 CPU を高速で処理できないことを表します。 Redis サーバーの負荷が高い場合は、クライアントでタイムアウトの例外が表示されます。 この場合は、スケールアップまたはデータを複数のキャッシュにパーティション分割することを検討してください。 |
| セット |指定したレポート期間中に、キャッシュから実行された設定操作の数。 この値は、Redis INFO のすべてのコマンド (`cmdstat_set`、`cmdstat_hset`、`cmdstat_hmset`、`cmdstat_hsetnx`、`cmdstat_lset`、`cmdstat_mset`、`cmdstat_msetnx`、`cmdstat_setbit`、`cmdstat_setex`、`cmdstat_setrange`、および `cmdstat_setnx`) からの値の合計です。 |
| 合計キー数  | 過去のレポート期間におけるキャッシュ内のキーの最大数。 この番号は、Redis INFO コマンドの `keyspace` にマップされます。 クラスタリングが有効になっているキャッシュについての基礎となるメトリック システムの制限により、「合計キー数」では、レポート期間中にキーの数が最大のシャードのキーの最大数が返されます。  |
| 合計処理数 |指定したレポート期間中に、キャッシュ サーバーによって処理されたコマンドの合計数。 この値は Redis INFO コマンドの `total_commands_processed` にマッピングされます。 Azure Cache for Redis をパブリッシュまたはサブスクライブにのみ使用する場合、`Cache Hits`、`Cache Misses`、`Gets`、または `Sets` のメトリックは存在しませんが、パブリッシュまたはサブスクライブ処理のキャッシュの使用状況を反映する `Total Operations` メトリックが用意されています。 |
| メモリ使用量 |指定したレポート期間中にキャッシュ内のキー/値のペアで使用されるキャッシュ メモリの量 (MB)。 この値は Redis INFO コマンドの `used_memory` にマッピングされます。 この値には、メタデータや断片化は含まれません。 |
| 使用されているメモリの割合 | 指定したレポート間隔で使用されている合計メモリの割合 (%)。  この値から Redis INFO コマンドの `used_memory` 値が参照され、割合が計算されます。 |
| RSS メモリ使用量 |指定したレポート期間中に使用されるキャッシュ メモリの量 (MB)。これには断片化とメタデータが含まれます。 この値は Redis INFO コマンドの `used_memory_rss` にマッピングされます。 |

<a name="operations-and-alerts"></a>

## <a name="alerts"></a>警告

メトリックとアクティビティ ログに基づいてアラートを受け取るように設定できます。 Azure Monitor では、アラートがトリガーされたときに次の処理を実行するように構成することができます。

- 電子メール通知を送信する
- Webhook を呼び出す
- Azure Logic App を呼び出す

対象のキャッシュのアラート ルールを構成するには、 **[リソース] メニュー** で **[アラート ルール]** を選択します。

![監視](./media/cache-how-to-monitor/redis-cache-monitoring.png)

アラートを構成して使用する方法について詳しくは、[アラートの概要](../azure-monitor/alerts/alerts-classic-portal.md)に関する記事をご覧ください。

<!-- 
## Activity Logs

Activity logs provide insight into the operations that completed on your Azure Cache for Redis instances. It was previously known as "audit logs" or "operational logs". Using activity logs, you can determine the "what, who, and when" for any write operations (PUT, POST, DELETE) taken on your Azure Cache for Redis instances.

> [!NOTE]
> Activity logs do not include read (GET) operations.
> 

To view activity logs for your cache, select **Activity logs** from the **Resource menu**.

For more information about Activity logs, see [Overview of the Azure Activity Log](../azure-monitor/essentials/platform-logs-overview.md).
-->
