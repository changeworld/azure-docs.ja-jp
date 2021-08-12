---
title: Hive LLAP Workload Management に関する問題 のトラブルシューティング
titleSuffix: Azure HDInsight
description: Hive LLAP Workload Management に関する問題 のトラブルシューティング
ms.service: hdinsight
ms.topic: troubleshooting
author: guptanikhil007
ms.author: guptan
ms.reviewer: jasonh
ms.date: 05/25/2021
ms.openlocfilehash: 6a06a67b5039bc3a7e25e8300128c85ee008be3a
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110483136"
---
# <a name="troubleshoot-hive-llap-workload-management-issues"></a>Hive LLAP Workload Management に関する問題 のトラブルシューティング

4\.0 以上のバージョンの HDInsight クラスターでは、Workload Management (WLM) を利用できます。 下の情報を、WLM 機能に関する問題の解決に役立ててください。

## <a name="get-wlm-resource-plan-and-plan-entities"></a>WLM リソース計画と計画エンティティを取得する
#### <a name="to-get-all-resource-plans-on-the-cluster"></a>クラスターのすべてのリソース計画を取得するには
```hql
SHOW RESOURCE PLANS;
```

#### <a name="to-get-definition-of-a-given-resource-plan"></a>特定のリソース計画の定義を取得するには
```hql
SHOW RESOURCE PLAN <plan_name>;
```

## <a name="get-wlm-entities-information-from-metastore-database"></a>メタストア データベースから WLM エンティティの情報を取得する
> [!NOTE]
> カスタム ハイブ メタストア データベースだけが対象です

WLM エンティティの情報は、Hive Metastore データベースにある次のテーブルでも見ることができます。 

* **WM_RESOURCEPLANS** (NAME 文字列, STATUS 文字列, QUERY_PARALLELISM int, DEFAULT_POOL_PATH 文字列)
* **WM_POOLS** (RP_NAME 文字列, PATH 文字列, ALLOC_FRACTION double, QUERY_PARALLELISM int, SCHEDULING_POLICY 文字列)
* **WM_MAPPINGS** (RP_NAME 文字列, ENTITY_TYPE 文字列, ENTITY_NAME 文字列, POOL_PATH 文字列, ORDERING int)
* **WM_TRIGGERS** (RP_NAME 文字列, NAME string, TRIGGER_EXPRESSION 文字列, ACTION_EXPRESSION 文字列)
* **WM_POOLS_TO_TRIGGERS** (RP_NAME 文字列, POOL_PATH 文字列, TRIGGER_NAME 文字列)

## <a name="wlm-metrics"></a>WLM メトリクス

WLM Metrics は [メトリクスのダンプ ] タブの [HS2Interactive UI] で直接確認できます。 <br>
:::image type="content" source="./media/hive-workload-management/hs2-interactive-wlm.jpg" alt-text="HS2 Interactive UI。" lightbox="./media/hive-workload-management/hs2-interactive-wlm.jpg":::

WLM が発行した、リソース計画の特定のプールのメトリクスの例。
```
    "name" : "Hadoop:service=hiveserver2,name=WmPoolMetrics.etl",
    "modelerType" : "WmPoolMetrics.etl",
    "tag.Context" : "HS2",
    "tag.SessionId" : "etl",
    "tag.Hostname" : "hn0-c2b-ll.cu1cgjaim53urggr4psrgczloa.cx.internal.cloudapp.net",
    "NumExecutors" : 10,
    "NumRunningQueries" : 2,
    "NumParallelQueries" : 3,
    "NumQueuedQueries" : 0,
    "NumExecutorsMax" : 10
```

HS2Interactive UI は、ESP (Enterprise セキュリティ パッケージ) を使用しているクラスターのうち、2021 年 4 月 より前にリリースしたものに対しては、機能しない場合があります。 その場合、WLM 関連のメトリクスは、カスタマイズした Gafana ダッシュボードで取得できます。
<br>
メトリクスは下の形式で命名されます。
```
default.General.WM_<pool>_numExecutors
default.General.WM_<pool>_numExecutorsMax
default.General.WM_<pool>_numRunningQueries
default.General.WM_<pool>_numParallelQueries
default.General.WM_<pool>_numQueuedQueries
```
grafana でメトリクスを取得する場合は、`<pool>` を当該のプール名に置き換えます。

:::image type="content" source="./media/hive-workload-management/grafana-wlm.jpg" alt-text="Grafana WLM メトリクス。" lightbox="./media/hive-workload-management/grafana-wlm.jpg":::

> 注: 上に示すフィルターとコンポーネント名には、hiveserver2 コンポーネントを選択してください。

<br>

## <a name="wlm-feature-characteristics"></a>WLM 機能の特徴
### <a name="lifecycle-of-tez-ams-in-wlm-enabled-clusters"></a>**WLM を有効にしているクラスターの Tez AM のライフサイクル**
既定の LLAP クラスターと異なり、WLM を有効にしているクラスターには、Tez AM がもう 1 組あります。 ハイブの構成で *hive.server2.tez.interactive.queue=wm* を設定すると、これらの Tez AM を `wm` キューで実行するようスケジュールします。 <br>
これらの Tez AM は、WLM がアクティブになったとき、リソース計画で定義しているすべてのプールの QUERY_PARALLELISM の合計値に基づいて生成されます。 <br>
クラスターで Workload Management を無効にすると、これらの Tez AM は自動的に破棄されます。
`{ DISABLE WORKLOAD MANAGEMENT; }`

### <a name="resource-contention"></a>**リソースの競合**
WSM を使用している LLAP クラスターでは、リソース計画の構成に基づいて、クエリ間でリソースを共有します。 リソースの共有によってクエリが遅くなることがあります。
リソース計画の調整により、プール内で発生するリソースに対する競合を減らすことができます。 例えば `scheduling_policy` を `fair` に設定すれば、プールに対する各クエリにクラスターのリソースを平等に割り当て、`fifo` に設定すれば、プールに対する最初のクエリにすべてのリソースを割り当てます。<br>
次の例で、リソース計画 `wlm_basic` の `etl` というプールにスケジュール ポリシーを設定する方法を示します。
```hql
ALTER POOL wlm_basic.etl SET SCHEDULING_POLICY = fair;
```
プールを作成するときにスケジュール ポリシーを設定することもできます。
```hql
CREATE POOL wlm_basic.default WITH ALLOC_FRACTION = 0.5, QUERY_PARALLELISM = 2, SCHEDULING_POLICY = fifo;
```

### <a name="query-failures-for-some-specific-use-cases"></a>**特定のユース ケースでのクエリ エラー**
次の場合、WLM で実行したクエリは自動的に破棄されます。
1. Move Trigger をクエリに適用し、使用できる Tez AM が通信先プールにないときは、クエリを実行せずに破棄します。 <br>
これは、WLM 機能の仕様上の制約です。 この機能が実行されることを回避するには、通信先プールの `QUERY_PARALLELISM` プロパティの値を増やし、負荷が最大になった場合にも、クラスターに送信したクエリをこのプールで処理できるようにします。 この変更に対応するため、`wm` キューのサイズも調整します。 <br>
2. WLM を無効にしている場合、すべてのインフライトのクエリは、次の例外パターンによりエラーになります。
   ```
   FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.tez.TezTask. Dag received [DAG_TERMINATE, DAG_KILL] in RUNNING state.
   ```
3. WLM Tez AM を手動で破棄すると、一部のクエリは次のパターンでエラーになります。 <br/>再送信すれば、これらのクエリは問題なく実行されます。
```
java.util.concurrent.CancellationException: Task was cancelled.
    at com.google.common.util.concurrent.AbstractFuture.cancellationExceptionWithCause(AbstractFuture.java:1349) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture.getDoneValue(AbstractFuture.java:550) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture.get(AbstractFuture.java:513) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture$TrustedFuture.get(AbstractFuture.java:90) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.Uninterruptibles.getUninterruptibly(Uninterruptibles.java:237) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.Futures.getDone(Futures.java:1064) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.Futures$CallbackListener.run(Futures.java:1013) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.DirectExecutor.execute(DirectExecutor.java:30) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture.executeListener(AbstractFuture.java:1137) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture.complete(AbstractFuture.java:957) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture.cancel(AbstractFuture.java:611) ~[guava-28.0-jre.jar:?]
    at com.google.common.util.concurrent.AbstractFuture$TrustedFuture.cancel(AbstractFuture.java:118) ~[guava-28.0-jre.jar:?]
    at org.apache.hadoop.hive.ql.exec.tez.WmTezSession$TimeoutRunnable.run(WmTezSession.java:264) ~[hive-exec-3.1.3.4.1.3.6.jar:3.1.3.4.1.3.6]
    at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:511) [?:1.8.0_275]
    at java.util.concurrent.FutureTask.run(FutureTask.java:266) [?:1.8.0_275]
    at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.access$201(ScheduledThreadPoolExecutor.java:180) ~[?:1.8.0_275]
    at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:293) ~[?:1.8.0_275]
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149) [?:1.8.0_275]
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624) [?:1.8.0_275]
    at java.lang.Thread.run(Thread.java:748) [?:1.8.0_275]
```

## <a name="known-issues"></a>既知の問題
* ターゲットの LLAP クラスターで WLM 機能を有効にしている場合、Spark のジョブを [Hive Warehouse Connector (HWC)](apache-hive-warehouse-connector.md) で送信すると、断続的にエラーが発生する場合がある。 <br>
  上の問題を回避するには、2 つの LLAP クラスターを用意し、一方では WLM を有効にし、もう一方では　WLM を使用しないようにします。
  すると、WLM なしで、HWC を使用して、Spark クラスターを LLAP クラスターに接続できるようになります。

* `DISABLE WORKLOAD MANAGEMENT;` コマンドが長時間ストップする場合がある。 <br>
コマンドをキャンセルし、次のコマンドでリソース計画の状態を確認します: `SHOW RESOURCE PLANS;`
`DISABLE WORKLOAD MANAGEMENT` コマンドを再度実行する前に、アクティブなリソース計画が利用可能な状態であるかどうかを確認します。 <br>

* `DISABLE WORKLOAD MANAGEMENT` コマンドまたは HS2 再起動を実行しても、Tez AM の一部が動作し続ける。 <br>
ワークロード管理を無効にしてから `yarn UI` または `yarn console application` でこれらの Tez AM をすべて破棄します。

## <a name="related-articles"></a>関連記事
* [Hive LLAP Workload Management](hive-workload-management.md)
* [Hive LLAP Workload Management のコマンドの概要](workload-management-commands.md)

