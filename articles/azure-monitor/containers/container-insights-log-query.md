---
title: Container insights のログのクエリを実行する方法
description: Container insights により、メトリックとログ データが収集されます。この記事では、レコードについて説明し、サンプル クエリを紹介します。
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: af48255c1895142107e693595c256d2c76fdee65
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179216"
---
# <a name="how-to-query-logs-from-container-insights"></a>Container insights のログのクエリを実行する方法

Container insights により、コンテナー ホストおよびコンテナーからパフォーマンスのメトリック、インベントリ データ、および正常性状態の情報が収集されます。 データは 3 分ごとに収集され、Azure Monitor の Log Analytics ワークスペースに転送され、Azure Monitor の [Log Analytics](../logs/log-analytics-overview.md) を使用して[ログ分析](../logs/log-query-overview.md)に使用できます。 このデータは、移行計画、容量の分析、探索、必要に応じたパフォーマンスのトラブルシューティングといったシナリオに適用できます。 Azure Monitor ログを使用することにより、傾向の特定、ボトルネックの診断、予想を行ったり、データを関連付けて現在のクラスター構成のパフォーマンスが最適化されているかどうかを判断したりできます。

これらのクエリの使用方法の詳細については、「[Azure Monitor Log Analytics でのクエリの使用](../logs/queries.md)」を参照し、クエリを実行してそれらの結果を処理するための Log Analytics の使用に関する完全なチュートリアルについては、「[Log Analytics のチュートリアル](../logs/log-analytics-tutorial.md)」を参照してください。

## <a name="open-log-analytics"></a>Log Analytics を開く
Log Analytics を開始するには複数のオプションがあり、それぞれが異なる[スコープ](../logs/scope.md)で始まります。 ワークスペース内のすべてのデータにアクセスするには、 **[監視]** メニューから **[ログ]** を選択します。 データを単一の Kubernetes クラスターに制限するには、そのクラスターのメニューから **[ログ]** を選択します。 

:::image type="content" source="media/container-insights-log-query/start-log-analytics.png" alt-text="Log Analytics の起動" lightbox="media/container-insights-log-query/start-log-analytics.png":::

## <a name="existing-log-queries"></a>既存のログ クエリ
Log Analytics を使用するために、必ずしもログ クエリの記述方法について理解している必要はありません。 複数の事前定義済みのクエリがあり、そこからクエリを選択して、変更せずに実行するか、カスタム クエリの開始として使用することができます。 Log Analytics 画面の上部にある **[クエリ]** をクリックし、 **[Kubernetes Services]** の **[リソースの種類]** でクエリを表示します。 

:::image type="content" source="media/container-insights-log-query/log-analytics-queries.png" alt-text="Kubernetes の Log Analytics クエリ" lightbox="media/container-insights-log-query/log-analytics-queries.png":::

## <a name="container-tables"></a>コンテナー テーブル
コンテナーの分析情報で使用されるテーブルのリストとその詳細な説明については、「[Azure Monitor テーブル参照](/azure/azure-monitor/reference/tables/tables-resourcetype#kubernetes-services)」を参照してください。 これらのテーブルはすべて、ログ クエリで使用できます。


## <a name="example-log-queries"></a>ログ クエリの例
多くの場合、1、2 個の例を使ってクエリを作成し、その後、要件に合わせて変更するとうまくいきます。 より高度なクエリを作成できるように、次のサンプル クエリを試すことができます。

### <a name="list-all-of-a-containers-lifecycle-information"></a>コンテナーのライフ サイクル情報をすべて一覧表示します

```kusto
ContainerInventory
| project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime
| render table
```

### <a name="kubernetes-events"></a>Kubernetes イベント

``` kusto
KubeEvents
| where not(isempty(Namespace))
| sort by TimeGenerated desc
| render table
```

### <a name="container-cpu"></a>コンテナー CPU

``` kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" 
| summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName 
```

### <a name="container-memory"></a>コンテナー メモリ

```kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes"
| summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName
```

### <a name="requests-per-minute-with-custom-metrics"></a>カスタム メトリックでの 1 分あたりの要求数

```kusto
InsightsMetrics
| where Name == "requests_count"
| summarize Val=any(Val) by TimeGenerated=bin(TimeGenerated, 1m)
| sort by TimeGenerated asc
| project RequestsPerMinute = Val - prev(Val), TimeGenerated
| render barchart 
```
### <a name="pods-by-name-and-namespace"></a>名前と名前空間別のポッド

```kusto
let startTimestamp = ago(1h);
KubePodInventory
| where TimeGenerated > startTimestamp
| project ContainerID, PodName=Name, Namespace
| where PodName contains "name" and Namespace startswith "namespace"
| distinct ContainerID, PodName
| join
(
    ContainerLog
    | where TimeGenerated > startTimestamp
)
on ContainerID
// at this point before the next pipe, columns from both tables are available to be "projected". Due to both
// tables having a "Name" column, we assign an alias as PodName to one column which we actually want
| project TimeGenerated, PodName, LogEntry, LogEntrySource
| summarize by TimeGenerated, LogEntry
| order by TimeGenerated desc
```

### <a name="pod-scale-out-hpa"></a>ポッドのスケールアウト (HPA)
各デプロイメントでスケールアウトされたレプリカの数を返します。 HPA で構成されたレプリカの最大数を使用してスケールアウトの割合を計算します。


```kusto
let _minthreshold = 70; // minimum threshold goes here if you want to setup as an alert
let _maxthreshold = 90; // maximum threshold goes here if you want to setup as an alert
let startDateTime = ago(60m);
KubePodInventory
| where TimeGenerated >= startDateTime 
| where Namespace !in('default', 'kube-system') // List of non system namespace filter goes here.
| extend labels = todynamic(PodLabel)
| extend deployment_hpa = reverse(substring(reverse(ControllerName), indexof(reverse(ControllerName), "-") + 1))
| distinct tostring(deployment_hpa)
| join kind=inner (InsightsMetrics 
    | where TimeGenerated > startDateTime 
    | where Name == 'kube_hpa_status_current_replicas'
    | extend pTags = todynamic(Tags) //parse the tags for values
    | extend ns = todynamic(pTags.k8sNamespace) //parse namespace value from tags
    | extend deployment_hpa = todynamic(pTags.targetName) //parse HPA target name from tags
    | extend max_reps = todynamic(pTags.spec_max_replicas) // Parse maximum replica settings from HPA deployment
    | extend desired_reps = todynamic(pTags.status_desired_replicas) // Parse desired replica settings from HPA deployment
    | summarize arg_max(TimeGenerated, *) by tostring(ns), tostring(deployment_hpa), Cluster=toupper(tostring(split(_ResourceId, '/')[8])), toint(desired_reps), toint(max_reps), scale_out_percentage=(desired_reps * 100 / max_reps)
    //| where scale_out_percentage > _minthreshold and scale_out_percentage <= _maxthreshold
    )
    on deployment_hpa
```

### <a name="nodepool-scale-outs"></a>Nodepool スケールアウト 
各ノード プール内のアクティブなノードの数を返します。 自動スケーラー設定で使用可能なアクティブ ノードの数と最大ノード構成を計算して、スケールアウトの割合を決定します。 **[結果の数]** のアラート ルールに使用するには、クエリのコメント行を参照してください。

```kusto
let nodepoolMaxnodeCount = 10; // the maximum number of nodes in your auto scale setting goes here.
let _minthreshold = 20;
let _maxthreshold = 90;
let startDateTime = 60m;
KubeNodeInventory
| where TimeGenerated >= ago(startDateTime)
| extend nodepoolType = todynamic(Labels) //Parse the labels to get the list of node pool types
| extend nodepoolName = todynamic(nodepoolType[0].agentpool) // parse the label to get the nodepool name or set the specific nodepool name (like nodepoolName = 'agentpool)'
| summarize nodeCount = count(Computer) by ClusterName, tostring(nodepoolName), TimeGenerated
//(Uncomment the below two lines to set this as an log search alert)
//| extend scaledpercent = iff(((nodeCount * 100 / nodepoolMaxnodeCount) >= _minthreshold and (nodeCount * 100 / nodepoolMaxnodeCount) < _maxthreshold), "warn", "normal")
//| where scaledpercent == 'warn'
| summarize arg_max(TimeGenerated, *) by nodeCount, ClusterName, tostring(nodepoolName)
| project ClusterName, 
    TotalNodeCount= strcat("Total Node Count: ", nodeCount),
    ScaledOutPercentage = (nodeCount * 100 / nodepoolMaxnodeCount),  
    TimeGenerated, 
    nodepoolName
```

### <a name="system-containers-replicaset-availability"></a>システム コンテナー (レプリカセット) の可用性
システム コンテナー (レプリカセット) を返し、使用できない割合をレポートします。 **[結果の数]** のアラート ルールに使用するには、クエリのコメント行を参照してください。

```kusto
let startDateTime = 5m; // the minimum time interval goes here
let _minalertThreshold = 50; //Threshold for minimum and maximum unavailable or not running containers
let _maxalertThreshold = 70;
KubePodInventory
| where TimeGenerated >= ago(startDateTime)
| distinct ClusterName, TimeGenerated
| summarize Clustersnapshot = count() by ClusterName
| join kind=inner (
    KubePodInventory
    | where TimeGenerated >= ago(startDateTime)
    | where Namespace in('default', 'kube-system') and ControllerKind == 'ReplicaSet' // the system namespace filter goes here
    | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus, ServiceName, PodLabel, Namespace, ContainerStatus
    | summarize arg_max(TimeGenerated, *), TotalPODCount = count(), podCount = sumif(1, PodStatus == 'Running' or PodStatus != 'Running'), containerNotrunning = sumif(1, ContainerStatus != 'running')
        by ClusterName, TimeGenerated, ServiceName, PodLabel, Namespace
    )
    on ClusterName
| project ClusterName, ServiceName, podCount, containerNotrunning, containerNotrunningPercent = (containerNotrunning * 100 / podCount), TimeGenerated, PodStatus, PodLabel, Namespace, Environment = tostring(split(ClusterName, '-')[3]), Location = tostring(split(ClusterName, '-')[4]), ContainerStatus
//Uncomment the below line to set for automated alert
//| where PodStatus == "Running" and containerNotrunningPercent > _minalertThreshold and containerNotrunningPercent < _maxalertThreshold
| summarize arg_max(TimeGenerated, *), c_entry=count() by PodLabel, ServiceName, ClusterName
//Below lines are to parse the labels to identify the impacted service/component name
| extend parseLabel = replace(@'k8s-app', @'k8sapp', PodLabel)
| extend parseLabel = replace(@'app.kubernetes.io/component', @'appkubernetesiocomponent', parseLabel)
| extend parseLabel = replace(@'app.kubernetes.io/instance', @'appkubernetesioinstance', parseLabel)
| extend tags = todynamic(parseLabel)
| extend tag01 = todynamic(tags[0].app)
| extend tag02 = todynamic(tags[0].k8sapp)
| extend tag03 = todynamic(tags[0].appkubernetesiocomponent)
| extend tag04 = todynamic(tags[0].aadpodidbinding)
| extend tag05 = todynamic(tags[0].appkubernetesioinstance)
| extend tag06 = todynamic(tags[0].component)
| project ClusterName, TimeGenerated,
    ServiceName = strcat( ServiceName, tag01, tag02, tag03, tag04, tag05, tag06),
    ContainerUnavailable = strcat("Unavailable Percentage: ", containerNotrunningPercent),
    PodStatus = strcat("PodStatus: ", PodStatus), 
    ContainerStatus = strcat("Container Status: ", ContainerStatus)
```

### <a name="system-containers-daemonsets-availability"></a>システム コンテナー (デーモンセット) の可用性
システム コンテナー (デーモンセット) を返し、使用できない割合をレポートします。 **[結果の数]** のアラート ルールに使用するには、クエリのコメント行を参照してください。

```kusto
let startDateTime = 5m; // the minimum time interval goes here
let _minalertThreshold = 50; //Threshold for minimum and maximum unavailable or not running containers
let _maxalertThreshold = 70;
KubePodInventory
| where TimeGenerated >= ago(startDateTime)
| distinct ClusterName, TimeGenerated
| summarize Clustersnapshot = count() by ClusterName
| join kind=inner (
    KubePodInventory
    | where TimeGenerated >= ago(startDateTime)
    | where Namespace in('default', 'kube-system') and ControllerKind == 'DaemonSet' // the system namespace filter goes here
    | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus, ServiceName, PodLabel, Namespace, ContainerStatus
    | summarize arg_max(TimeGenerated, *), TotalPODCount = count(), podCount = sumif(1, PodStatus == 'Running' or PodStatus != 'Running'), containerNotrunning = sumif(1, ContainerStatus != 'running')
        by ClusterName, TimeGenerated, ServiceName, PodLabel, Namespace
    )
    on ClusterName
| project ClusterName, ServiceName, podCount, containerNotrunning, containerNotrunningPercent = (containerNotrunning * 100 / podCount), TimeGenerated, PodStatus, PodLabel, Namespace, Environment = tostring(split(ClusterName, '-')[3]), Location = tostring(split(ClusterName, '-')[4]), ContainerStatus
//Uncomment the below line to set for automated alert
//| where PodStatus == "Running" and containerNotrunningPercent > _minalertThreshold and containerNotrunningPercent < _maxalertThreshold
| summarize arg_max(TimeGenerated, *), c_entry=count() by PodLabel, ServiceName, ClusterName
//Below lines are to parse the labels to identify the impacted service/component name
| extend parseLabel = replace(@'k8s-app', @'k8sapp', PodLabel)
| extend parseLabel = replace(@'app.kubernetes.io/component', @'appkubernetesiocomponent', parseLabel)
| extend parseLabel = replace(@'app.kubernetes.io/instance', @'appkubernetesioinstance', parseLabel)
| extend tags = todynamic(parseLabel)
| extend tag01 = todynamic(tags[0].app)
| extend tag02 = todynamic(tags[0].k8sapp)
| extend tag03 = todynamic(tags[0].appkubernetesiocomponent)
| extend tag04 = todynamic(tags[0].aadpodidbinding)
| extend tag05 = todynamic(tags[0].appkubernetesioinstance)
| extend tag06 = todynamic(tags[0].component)
| project ClusterName, TimeGenerated,
    ServiceName = strcat( ServiceName, tag01, tag02, tag03, tag04, tag05, tag06),
    ContainerUnavailable = strcat("Unavailable Percentage: ", containerNotrunningPercent),
    PodStatus = strcat("PodStatus: ", PodStatus), 
    ContainerStatus = strcat("Container Status: ", ContainerStatus)
```

## <a name="resource-logs"></a>リソース ログ
AKS のリソース ログは [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) テーブルに保存されます。 **[カテゴリ]** 列を使用して、さまざまなログを区別できます。 各カテゴリの説明については、[ [AKS リファレンス リソース ログ]](../../aks/monitor-aks-reference.md) を参照してください。 次の例では、AKS クラスターのリソース ログを Log Analytics ワークスペースに送信するために診断拡張機能が必要です。 詳細については、「[監視の構成](../../aks/monitor-aks.md#configure-monitoring)」を参照してください。

### <a name="api-server-logs"></a>API サーバー ログ

```kusto
AzureDiagnostics 
| where Category == "kube-apiserver"
```

### <a name="count-logs-for-each-category"></a>各カテゴリのログをカウントする

```kusto
AzureDiagnostics
| where ResourceType == "MANAGEDCLUSTERS"
| summarize count() by Category
```

## <a name="query-prometheus-metrics-data"></a>Prometheus メトリック データのクエリを実行する

次の例は、ノードごとのディスクごとの 1 秒あたりのディスク読み取り数を示す Prometheus メトリック クエリです。

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

Azure Monitor によって収集された Prometheus メトリックを名前空間でフィルター処理して表示するには、"prometheus" を指定します。 `default` kubernetes 名前空間から Prometheus メトリックを表示するクエリの例を次に示します。

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

prometheus のデータは、名前で直接照会することもできます。

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>クエリ構成エラーまたはスクラップ エラー

構成エラーまたはスクラップ エラーを調査するために、次のクエリの例では、`KubeMonAgentEvents` テーブルから情報イベントが返されます。

```
KubeMonAgentEvents | where Level != "Info" 
```

出力では、次の例のような結果が示されます。

![エージェントからの情報イベントのクエリ結果をログに記録する](./media/container-insights-log-query/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>次のステップ

Container insights にはアラートの定義済みのセットは含まれていません。 [Container insights を使用したパフォーマンス アラートの作成](./container-insights-log-alerts.md)に関するページを読んで、CPU やメモリの使用率が高い場合に推奨アラートを作成し、DevOps や運用プロセスまたは手順をサポートする方法について学習します。
