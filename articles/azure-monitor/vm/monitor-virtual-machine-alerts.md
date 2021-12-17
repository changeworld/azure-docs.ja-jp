---
title: 'Azure Monitor を使用して仮想マシンを監視する: アラート'
description: Azure Monitor を使用して、仮想マシンとそのゲストワークロードからアラートを作成する方法について説明します。
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: b06d7c573514e0fe0471e13df3476bf5b13f20e3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252689"
---
# <a name="monitor-virtual-machines-with-azure-monitor-alerts"></a>Azure Monitor を使用して仮想マシンを監視する: アラート

この記事は、シナリオ「[Azure Monitor で仮想マシンとそのワークロードを監視する](monitor-virtual-machine.md)」の一部です。 仮想マシンとそのゲスト オペレーティング システムのアラート ルールを作成するためのガイダンスを提供します。 [Azure Monitor のアラート](../alerts/alerts-overview.md)では、監視データの興味深いデータやパターンを事前に通知します。 仮想マシン用に事前に構成されたアラート ルールはありませんが、VM の分析情報によって収集されたデータに基づいて独自のルールを作成することができます。 

> [!NOTE]
> この記事で説明されるアラートには、現在パブリック プレビュー中の機能である [Azure Monitor for VM ゲストの正常性](vminsights-health-overview.md)によって作成されたアラートは含まれません。 この機能は近日中に一般公開され、アラートに関するガイダンスが統合されます。

> [!IMPORTANT]
> ほとんどのアラート ルールには、ルールの種類、含まれるディメンションの数、実行頻度に応じてかかるコストがあります。 アラート ルールを作成する前に、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」で **アラート ルール** に関して参照してください。

## <a name="choose-the-alert-type"></a>アラートの種類を選択する
Azure Monitor における最も一般的な種類のアラート ルールは、[メトリック アラート](../alerts/alerts-metric.md)と[ログ クエリ アラート](../alerts/alerts-log-query.md)です。 特定のシナリオのために作成するアラート ルールの種類は、アラートの対象となるデータの場所に応じて異なります。 場合によっては、特定のアラート シナリオのデータをメトリックとログの両方で使用できる場合があります。また、使用するルールの種類を決定する必要があります。 また、特定のデータを収集する方法を設定し、アラート ルールの種類を決定してデータ収集方法を決定する柔軟性も得られます。

通常、最適な方法は、ログ アラートの代わりにメトリック アラートを使用することです。これは、応答性が高くステートフルであるためです。 メトリック アラートを使用するには、アラート対象のデータが、メトリックで利用可能である必要があります。 現在、VM の分析情報はすべてのデータをログに送信します。そのため、Azure Monitor エージェントをインストールして、メトリック アラートをゲスト オペレーティング システムからのデータと共に使用する必要があります。 メトリック データがメトリックで使用できない場合、またはメトリック アラート ルールに比較的単純なロジックを超えるロジックが必要な場合は、ログ クエリ アラートをメトリック データと一緒に使用します。

### <a name="metric-alert-rules"></a>メトリック アラート ルール
[メトリック アラート ルール](../alerts/alerts-metric.md)は、特定のメトリックがしきい値を超えたときにアラートが生成されるようにするのに役立ちます。 たとえば、コンピューターの CPU が高い使用率で実行されているときなどです。 メトリック アラート ルールの対象には、特定のマシン、リソース グループ、またはサブスクリプションを指定できます。 このインスタンスでは、コンピューターのグループに適用される 1 つのルールを作成できます。

仮想マシンのメトリック ルールでは、次のデータを使用できます。

- 自動的に収集される Azure 仮想マシンのホスト メトリック。 
- Azure Monitor エージェントによってゲスト オペレーティング システムから収集されるメトリック。 

> [!NOTE]
> 現在パブリック プレビュー段階にある Azure Monitor エージェントが VM の分析情報でサポートされている場合、メトリック アラートを使用できるように、ゲスト オペレーティング システムからメトリックにパフォーマンスデータが送信されます。

### <a name="log-alerts"></a>ログ アラート
[ログ アラート](../alerts/alerts-metric.md)では、ログ クエリの結果について 2 つの異なる測定値を実行できます。このそれぞれで、仮想マシンを監視するための個別のシナリオがサポートされます。

- [メトリック測定](../alerts/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value): 数値がアラート ルールで定義されているしきい値を超える、クエリ結果内のレコードごとに個別のアラートが作成されます。 メトリック測定は、Log Analytics エージェントによって収集される Windows および Syslog イベントなどの数値以外のデータの場合、あるいは複数のコンピューター間のパフォーマンスの傾向を分析する場合に適しています。
- [結果の数](../alerts/alerts-unified-log.md#count-of-the-results-table-rows): 少なくとも指定された数のレコードがクエリから返された場合に、単一のアラートが作成されます。 結果の数は、[Log Analytics エージェント](../agents/log-analytics-agent.md)によって収集される Windows および Syslog イベントなどの数値以外のデータの場合、あるいは複数のコンピューター間のパフォーマンスの傾向を分析する場合に適しています。 また、アラートの数を最小限に抑える必要がある場合や、複数のコンピューターで同じエラー状態が発生したときにのみアラートを作成する場合にも、この方法を選択できます。

### <a name="target-resource-and-impacted-resource"></a>ターゲット リソースと影響を受けるリソース

> [!NOTE]
> 現在パブリック プレビュー中のリソース中心のログ アラート ルールを使用すると、仮想マシンのログ クエリ アラートを簡素化し、メトリック測定クエリによって現在提供されている機能を置き換えることができます。 このコンピューターをルールのターゲットとして使用することで、影響を受けるリソースとして識別しやすくなります。 また、特定のリソース グループまたはサブスクリプションに含まれるすべてのコンピューターに、1 つのアラート ルールを適用することもできます。 リソース中心のログ クエリ アラートが一般公開されると、このシナリオのガイダンスが更新されます。
> 
Azure Monitor 内の各アラートには、**影響を受けるリソース** プロパティがあります。これは、ルールのターゲットによって定義されます。 メトリック アラート ルールの場合、影響を受けるリソースはそのコンピューターです。これにより、標準のアラート ビューで簡単に識別できます。 ログ クエリ アラートは、コンピューターではなく、ワークスペース リソースに関連付けられます。これは、各コンピューターに対してアラートを作成するメトリック測定アラートを使用するときも同様です。 アラートの詳細を表示して、影響を受けたコンピューターを確認する必要があります。

コンピューター名は、**影響を受けるリソース** プロパティに格納されます。このプロパティは、アラートの詳細で表示できます。 また、アラートから送信される電子メールのディメンションとしても表示されます。

:::image type="content" source="media/monitor-virtual-machines/alert-metric-measurement.png" alt-text="影響を受けたリソースのアラートを示すスクリーンショット。" lightbox="media/monitor-virtual-machines/alert-metric-measurement.png":::

影響を受けるコンピューターのアラートを一覧表示するビューが必要な場合があります。 カスタムの [Azure Resource Graph](../../governance/resource-graph/overview.md) を使用するカスタム ブックを使用して、このビューを提供できます。 次のクエリを使用してアラートを表示し、ブック内のデータ ソース **Azure Resource Graph** を使用します。

```kusto
alertsmanagementresources
| extend dimension = properties.context.context.condition.allOf
| mv-expand dimension
| extend dimension = dimension.dimensions
| mv-expand dimension
| extend Computer = dimension.value
| extend AlertStatus = properties.essentials.alertState
| summarize count() by Alert=name, tostring(AlertStatus), tostring(Computer)
| project Alert, AlertStatus, Computer
```
## <a name="common-alert-rules"></a>一般的なアラート ルール
次のセクションでは、Azure Monitor の仮想マシンに関する一般的なアラート ルールを示します。 メトリック アラートとログ メトリック測定アラートの詳細が表示されます。 使用するアラートの種類のガイダンスについては、「[アラートの種類を選択する](#choose-the-alert-type)」を参照してください。

Azure Monitor でアラート ルールを作成するプロセスに慣れていない場合は、ガイダンスを得るために次の記事を参照してください。

- [Azure Monitor を使用してメトリック アラートを作成、表示、管理する](../alerts/alerts-metric.md)
- [Azure Monitor を使用してログ アラートを作成、表示、管理する](../alerts/alerts-log.md)

### <a name="machine-unavailable"></a>コンピューターを利用できない
最も基本的な要件は、コンピューターが使用できなくなったときにアラートを送信することです。 停止しているか、ゲスト オペレーティング システムが応答していないか、エージェントが応答していない可能性があります。 このアラートはさまざまな方法で構成できますが、最も一般的なのは、Log Analytics エージェントから送信されたハートビートを使用することです。 

#### <a name="log-query-alert-rules"></a>ログ クエリ アラート ルール
ログ クエリ アラートは、各コンピューターから 1 分ごとにハートビート レコードを取得する[ハートビート テーブル](/azure/azure-monitor/reference/tables/heartbeat)を使用します。 

**個別のアラート**

次のクエリでメトリック測定ルールを使用します。

```kusto
Heartbeat
| summarize TimeGenerated=max(TimeGenerated) by Computer
| extend Duration = datetime_diff('minute',now(),TimeGenerated)
| summarize AggregatedValue = min(Duration) by Computer, bin(TimeGenerated,5m)
```

**単一のアラート**

次のクエリを使用して、さまざまな結果アラートを使用します。

```kusto
Heartbeat
| summarize LastHeartbeat=max(TimeGenerated) by Computer 
| where LastHeartbeat < ago(5m)
```

#### <a name="metric-alert-rules"></a>メトリック アラート ルール
各 Log Analytics ワークスペースには、*ハートビート* と呼ばれるメトリックが含まれています。 そのワークスペースに接続されている各仮想マシンは、1 分ごとにハートビート メトリックの値を送信します。 そのコンピューターがメトリックのディメンションであるため、どのコンピューターもハートビートの送信に失敗したときに、アラートが起動するようにできます。 **[集計の種類]** を **[データの個数]** に設定し、 **[しきい値]** を **[評価の粒度]** と一致する値に設定します。

### <a name="cpu-alerts"></a>CPU アラート
#### <a name="metric-alert-rules"></a>メトリック アラート ルール

| 移行先 | メトリック |
|:---|:---|
| Host | CPU 使用率 |
| Windows ゲスト | \Processor Information(_Total)\% Processor Time |
| Linux ゲスト | cpu/usage_active |

#### <a name="log-alert-rules"></a>ログ アラート ルール

**CPU 使用率** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer
```

**サブスクリプション内のすべてのコンピューティング リソースの CPU 使用率**

```kusto
 InsightsMetrics
 | where Origin == "vm.azm.ms"
 | where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/") 
 | where Namespace == "Processor" and Name == "UtilizationPercentage" | summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

**リソース グループ内のすべてのコンピューティング リソースの CPU 使用率** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/" or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/"
| where Namespace == "Processor" and Name == "UtilizationPercentage" | summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId 
```

### <a name="memory-alerts"></a>メモリのアラート

#### <a name="metric-alert-rules"></a>メトリック アラート ルール

| 移行先 | メトリック |
|:---|:---|
| Windows ゲスト | \Memory\% Committed Bytes in Use<br>\Memory\Available Bytes |
| Linux ゲスト | mem/available<br>mem/available_percent |

#### <a name="log-alert-rules"></a>ログ アラート ルール

**使用可能なメモリ (MB)** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer
```

**使用可能なメモリ (%)** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"]) | extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer  
``` 

### <a name="disk-alerts"></a>ディスク アラート

#### <a name="metric-alert-rules"></a>メトリック アラート ルール

| 移行先 | メトリック |
|:---|:---|
| Windows ゲスト | \Logical Disk\(_Total)\% Free Space<br>\Logical Disk\(_Total)\Free Megabytes |
| Linux ゲスト | disk/free<br>disk/free_percent |

#### <a name="log-query-alert-rules"></a>ログ クエリ アラート ルール

**使用されている論理ディスク - 各コンピューター上のすべてのディスク** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

**使用されている論理ディスク - 個々のディスク** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk 
```

**論理ディスクの IOPS**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk 
```
**論理ディスクのデータ速度**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk 
```

## <a name="network-alerts"></a>ネットワーク アラート

#### <a name="metric-alert-rules"></a>メトリック アラート ルール

| 移行先 | メトリック |
|:---|:---|
| Windows ゲスト | \Network Interface\Bytes Sent/sec<br>\Logical Disk\(_Total)\Free Megabytes |
| Linux ゲスト | disk/free<br>disk/free_percent |

### <a name="log-query-alert-rules"></a>ログ クエリ アラート ルール

**ネットワーク インターフェイスの受信バイト数 - すべてのインターフェイス**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId  
```

**ネットワーク インターフェイスの受信バイト数 - 個々のインターフェイス**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface 
```

**ネットワーク インターフェイスの送信バイト数 - すべてのインターフェイス**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

**ネットワーク インターフェイスの送信バイト数 - 個々のインターフェイス**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface 
```

## <a name="comparison-of-log-query-alert-measures"></a>ログ クエリ アラート メジャーの比較
2 つのログ アラート メジャーの動作を比較するために、仮想マシンの CPU が 80% を超えたときにそれぞれがアラートを作成するチュートリアルを次に示します。 必要なデータは、[InsightsMetrics テーブル](/azure/azure-monitor/reference/tables/insightsmetrics)に含まれています。 次のクエリは、アラートのための評価が必要なレコードを返します。 アラート ルールの種類ごとに、このクエリのバリアントが使用されます。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
```

### <a name="metric-measurement"></a>メトリック測定
**メトリック測定** の測定では、値がアラート ルールで定義されているしきい値を超える、クエリ内のレコードごとに個別のアラートが作成されます。 これらのアラート ルールは、仮想マシン パフォーマンス データに最適です。これは、コンピューターごとに個別のアラートを作成できるためです。 このメジャーのログ クエリでは、各マシンの値を返す必要があります。 アラート ルールのしきい値によって、値がアラートを発生させる必要があるかどうかが決定されます。

> [!NOTE]
> 現在パブリック プレビュー中のリソース中心のログ アラート ルールを使用すると、仮想マシンのログ クエリ アラートを簡素化し、メトリック測定クエリによって現在提供されている機能を置き換えることができます。 このコンピューターをルールのターゲットとして使用することで、影響を受けるリソースとして識別しやすくなります。 また、特定のリソース グループまたは説明に含まれるすべてのコンピューターに、1 つのアラート ルールを適用することもできます。 リソース中心のログ クエリ アラートが一般公開されると、このシナリオのガイダンスが更新されます。

#### <a name="query"></a>クエリ
メトリック測定を使用するルールのクエリには、**AggregatedValue** という数値プロパティを持つ各マシンのレコードを含める必要があります。 この値は、アラート ルールのしきい値と比較されます。 しきい値はアラート ルールで定義されているので、クエリはこの値をしきい値と比較する必要がありません。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer
```

#### <a name="alert-rule"></a>アラート ルール
[Azure Monitor] メニューの **[ログ]** を選択し、Log Analytics を開きます。 スコープに対して正しいワークスペースが選択されていることを確認します。 そうでない場合は、左上にある **[スコープの選択]** をクリックし、適切なワークスペースを選択します。 必要なロジックを含むクエリを貼り付け、 **[実行]** を選択して、正しい結果が返されることを確認します。

:::image type="content" source="media/monitor-virtual-machines/log-alert-metric-query-results.png" alt-text="メトリック測定アラートのクエリ結果を示すスクリーンショット。" lightbox="media/monitor-virtual-machines/log-alert-metric-query-results.png":::

**[新しいアラート ルール]** を選択して、現在のクエリでルールを作成します。 このルールでは、**リソース** のワークスペースが使用されます。

**[条件]** を選択して構成を表示します。 クエリには、各コンピューターのクエリから返された値のグラフィカル ビューが既に入力されています。 コンピューターを **[ピボット オン]** ドロップダウン リストから選択します。

**[アラート ロジック]** まで下にスクロールし、 **[メトリック測定]** を **[作成元]** プロパティに選択します。 使用率が 80% を超えたときにアラートを生成したいため、 **[集計値]** を **[より大きい]** に、 **[しきい値]** を「**80**」に設定します。

**[アラート ロジック]** まで下にスクロールし、 **[メトリック測定]** を **[作成元]** プロパティに選択します。 クエリから返された値と比較する **[しきい値]** を指定します。 この例では、**80** を使用します。 **[アラートをトリガーする基準]** には、アラートが作成されるまでにしきい値を超える必要がある回数を指定します。 たとえば、プロセッサがしきい値を 1 回超えてから通常の状態に戻る場合は気にならず、複数の連続した測定値でしきい値を超える状態が続く場合は気になることがあります。 この例では、 **[連続する違反]** を **3** に設定します。

**[評価基準]** まで下にスクロールします。 **[期間]** は、クエリの時間範囲を指定します。 **15** 分の値を指定します。これは、このクエリで過去 15 分間に収集されたデータだけが使用されることを意味します。 **[頻度]** には、クエリの実行頻度を指定します。 値が小さいと、アラート ルールの応答性が高くなりますが、コストも高くなります。 15 分ごとにクエリを実行するには、**15** を指定します。

:::image type="content" source="media/monitor-virtual-machines/log-alert-metric-rule.png" alt-text="メトリック測定アラートのクエリ ルールを示すスクリーンショット。" lightbox="media/monitor-virtual-machines/log-alert-metric-rule.png":::

### <a name="number-of-results-rule"></a>結果の数ルール
**結果の数** ルールでは、少なくとも指定された数のレコードがクエリから返された場合に、単一のアラートが作成されます。 通常、この種類のアラート ルールのログ クエリがアラート条件を特定し、アラート ルールのしきい値が、十分な数のレコードが返されるかどうかを判断します。

#### <a name="query"></a>クエリ
この例では、CPU 使用率のしきい値がクエリに含まれています。 クエリから返されるレコードの数は、そのしきい値を超えるマシンの数です。 アラート ルールのしきい値は、アラートを発生させるために必要なマシンの最小数です。 単一のマシンでエラーが発生した場合にアラートが必要な場合、アラート ルールのしきい値は 0 です。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AverageUtilization = avg(Val) by Computer
| where AverageUtilization > 80
```

#### <a name="alert-rule"></a>アラート ルール
[Azure Monitor] メニューの **[ログ]** を選択し、Log Analytics を開きます。 スコープに対して正しいワークスペースが選択されていることを確認します。 そうでない場合は、左上にある **[スコープの選択]** をクリックし、適切なワークスペースを選択します。 必要なロジックを含むクエリを貼り付け、 **[実行]** を選択して、正しい結果が返されることを確認します。 現在、しきい値を超えるマシンがない可能性があります。そのため、結果を確認するためにしきい値を一時的に低い値に変更します。 その後、アラート ルールを作成する前に、適切なしきい値を設定します。

:::image type="content" source="media/monitor-virtual-machines/log-alert-number-query-results.png" alt-text="結果アラート クエリの結果の数を示すスクリーンショット。" lightbox="media/monitor-virtual-machines/log-alert-number-query-results.png":::

**[新しいアラート ルール]** を選択して、現在のクエリでルールを作成します。 このルールでは、**リソース** のワークスペースが使用されます。

**[条件]** を選択して構成を表示します。 クエリには、過去数分間にクエリから返されたレコード数のグラフィカル ビューが既に入力されています。 

**[アラート ロジック]** まで下にスクロールし、 **[結果の数]** を **[作成元]** プロパティに選択します。 この例では、なんらかのレコードが返された場合にアラートを発生させます。つまり、少なくとも 1 台の仮想マシンに 80% を超えるプロセッサがあります。 **[より大きい]** を **[演算子]** に選択し、 **[しきい値]** に「**0**」を選択します。

**[評価基準]** まで下にスクロールします。 **[期間]** は、クエリの時間範囲を指定します。 **15** 分の値を指定します。これは、このクエリで過去 15 分間に収集されたデータだけが使用されることを意味します。 **[頻度]** には、クエリの実行頻度を指定します。 値が小さいと、アラート ルールの応答性が高くなりますが、コストも高くなります。 15 分ごとにクエリを実行するには、**15** を指定します。

:::image type="content" source="media/monitor-virtual-machines/log-alert-number-rule.png" alt-text="結果のアラート クエリ ルールの数を示すスクリーンショット。" lightbox="media/monitor-virtual-machines/log-alert-number-rule.png":::

## <a name="next-steps"></a>次の手順

* [仮想マシンで実行されているワークロードを監視する](monitor-virtual-machine-workloads.md)。
* [仮想マシン用に収集された監視データを分析する](monitor-virtual-machine-analyze.md)。
