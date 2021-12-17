---
title: Container insights からのログ アラート | Microsoft Docs
description: この記事では、Container insights からのメモリおよび CPU の使用率に対するカスタム ログ アラートを作成する方法について説明します。
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 82d6629ba903b656db9932b3c6bd6f5a2b92ea6a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738487"
---
# <a name="how-to-create-log-alerts-from-container-insights"></a>Container insights からログ アラートを作成する方法

Container insights により、マネージド Kubernetes クラスターまたは自己管理型 Kubernetes クラスターにデプロイされているコンテナー ワークロードのパフォーマンスが監視されます。 重要なことについてアラートを生成するため、この記事では、AKS クラスターを使用して次の状況でログベースのアラートを作成する方法について説明します。

- クラスター ノードで CPU またはメモリの使用率がしきい値を超えたとき
- 対応するリソースに設定されている上限と比較して、コントローラー内のいずれかのコンテナーで CPU またはメモリの使用率がしきい値を超えたとき
- *NotReady* 状態のノード数
- *Failed*、*Pending*、*Unknown*、*Running*、*Succeeded* の各ポッドフェーズ数
- クラスター ノードで空きディスク領域がしきい値を超えたとき

クラスター ノードで CPU またはメモリの使用率が高いこと、またはクラスター ノードで空きディスク領域が少ないことを警告するには、メトリック アラートまたはメトリック測定アラートを作成するために提供されているクエリを使用します。 メトリック アラートはログ アラートよりも待ち時間が少ない一方で、ログ アラートは高度なクエリと、より洗練された機能に対応しています。 ログ アラートのクエリは、*now* 演算子を使用して 1 時間戻ることで、日時を現在と比較します。 (Container insights により、すべての日付は協定世界時 (UTC) 形式で保存されます)。

> [!IMPORTANT]
> ほとんどのアラート ルールには、ルールの種類、含まれるディメンションの数、実行頻度に応じてかかるコストが含まれます。 アラート ルールを作成する前に、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」内の「**アラート ルール**」を参照してください。

Azure Monitor のアラートに詳しくない場合は、事前に「[Microsoft Azure のアラートの概要](../alerts/alerts-overview.md)」を参照してください。 ログ クエリを使用したアラートの詳細については、「[Azure Monitor でのログ アラート](../alerts/alerts-unified-log.md)」を参照してください。 メトリック アラートの詳細については、[Azure Monitor でのメトリック アラート](../alerts/alerts-metric-overview.md)に関するページを参照してください。

## <a name="log-query-measurements"></a>ログ クエリの測定
ログ クエリ アラートでは、ログ クエリの結果について 2 つの異なる測定を実行できます。それぞれで、仮想マシンを監視するための個別のシナリオがサポートされます。

[メトリック測定](../alerts/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)では、アラート ルールで定義されているしきい値を超える数値が含まれるクエリ結果内の各レコードに対して個別のアラートを作成します。 これらは、CPU などの数値データに最適です。

[結果の数](../alerts/alerts-unified-log.md#count-of-the-results-table-rows)では、少なくとも指定された数のレコードがクエリから返された場合に、単一のアラートを作成します。 これらは、複数のコンピューター間のパフォーマンスの傾向を分析する場合など、数値以外のデータに適しています。 また、アラートの数を最小限に抑える必要がある場合や、複数のコンポーネントで同じエラー状態が発生したときにのみアラートを作成する場合にも、この方法を選択できます。

> [!NOTE]
> 現在パブリック プレビュー中のリソース中心のログ アラート ルールを使用すると、ログ クエリ アラートを簡素化し、メトリック測定クエリによって現在提供されている機能を置き換えることができます。 AKS クラスターをルールのターゲットとして使用すると、影響を受けるリソースとしてそれをより適切に識別できます。 リソース中心のログ クエリ アラートが一般公開されると、このシナリオのガイダンスが更新されます。

## <a name="create-a-log-query-alert-rule"></a>ログ クエリ アラート ルールを作成する
[ログ クエリ アラート測定の比較](../vm/monitor-virtual-machine-alerts.md#comparison-of-log-query-alert-measures)では、測定の種類ごとにログ クエリ アラート ルールの詳細を提供します。これには、それぞれをサポートするログ クエリの比較が含まれます。 これらの同じプロセスを使用することで、この記事に示すクエリと同様のものを使用して AKS クラスターに対するアラート ルールを作成できます。

## <a name="resource-utilization"></a>リソース使用率 

**平均 CPU 使用率: メンバー ノードの 1 分ごとの CPU 使用率の平均 (メトリック測定)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

**平均メモリ使用率: メンバー ノードの 1 分ごとのメモリ使用率の平均 (メトリック測定)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```


>[!IMPORTANT]
>次のクエリでは、プレースホルダー値 \<your-cluster-name> と \<your-controller-name> を使用して、クラスターとコントローラーを表しています。 アラートを設定するときに、プレースホルダーを環境に固有の値に置き換えてください。

**コントローラー内のすべてのコンテナーの平均 CPU 使用率: コントローラー内の全コンテナー インスタンスの 1 分ごとの CPU 使用率の平均 (メトリック測定)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

**コントローラー内のすべてのコンテナーの平均メモリ使用率: コントローラー内の全コンテナー インスタンスの 1 分ごとのメモリ使用率の平均 (メトリック測定)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

## <a name="resource-availability"></a>リソースの可用性 

**状態が Ready および NotReady であるノードとカウント (メトリック測定)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
次のクエリでは、すべてのフェーズに基づくポッド フェーズ数が返されます:*Failed*、*Pending*、*Unknown*、*Running*、または *Succeeded*。  

```kusto
let endDateTime = now(); 
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | summarize PodStatus=any(PodStatus) by TimeGenerated, PodUid, ClusterName
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>*Pending*、*Failed*、*Unknown* などの特定のポッド フェーズについてのアラートを生成するには、クエリの最後の行を変更します。 たとえば、*FailedCount* についてのアラートを生成するには、次のようにします。 <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

次のクエリは、空き領域の使用が 90% を超えるクラスター ノードのディスクを返します。 クラスター ID を取得するには、まず、次のクエリを実行し、`ClusterId` プロパティの値をコピーします。

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```



**個々のコンテナーの再起動 (結果数)**<br>
個々のシステム コンテナーの再起動回数が過去 10 分間でしきい値を超えた場合にアラートを生成します。

 
```kusto
let _threshold = 10m; 
let _alertThreshold = 2;
let Timenow = (datetime(now) - _threshold); 
let starttime = ago(5m); 
KubePodInventory
| where TimeGenerated >= starttime
| where Namespace in ('default', 'kube-system') // the namespace filter goes here
| where ContainerRestartCount > _alertThreshold
| extend Tags = todynamic(ContainerLastStatus)
| extend startedAt = todynamic(Tags.startedAt)
| where startedAt >= Timenow
| summarize arg_max(TimeGenerated, *) by Name
```

## <a name="next-steps"></a>次のステップ

- [ログ クエリの例](container-insights-log-query.md)を表示して、事前定義されたクエリや例を確認し、クラスターのアラート、視覚化、または分析のために評価やカスタマイズを行います。

- Azure Monitor と、Kubernetes クラスターの他の側面を監視する方法の詳細については、[Kubernetes クラスターのパフォーマンスの表示](container-insights-analyze.md)および [Kubernetes クラスターの正常性の表示](./container-insights-overview.md)に関するページをご覧ください。