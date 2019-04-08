---
title: コンテナーに対する Azure Monitor を使用してパフォーマンス アラートを作成する | Microsoft Docs
description: この記事では、コンテナーに対する Azure Monitor からのメモリおよび CPU 使用率のログ クエリに基づいて、カスタム Azure アラートを作成する方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: magoedte
ms.openlocfilehash: e6fdb0d57a44578647c1f16dc76c557296f20ddb
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886777"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>コンテナー用 Azure Monitor でパフォーマンスの問題に関するアラートを設定する方法
コンテナーに対する Azure Monitor は、Azure Kubernetes Service (AKS) でホストされた Azure Container Instances またはマネージド Kubernetes クラスターにデプロイされているコンテナー ワークロードのパフォーマンスを監視します。 

この記事では、次の状況のアラートを有効にする方法について説明します。

* クラスターのノードで CPU およびメモリの使用率が定義済みのしきい値を超えたとき。
* 対応するリソースに設定されている上限と比較して、コントローラー内のいずれかのコンテナーで CPU またはメモリの使用率が定義済みのしきい値を超えたとき。

クラスターまたはコントローラーの CPU またはメモリの使用率が高いときにアラートを生成するには、提供されたログ クエリに基づくメトリック測定アラート ルールを作成します。 クエリでは、now 演算子を使用して日時を現在と比較し、1 時間戻ります。 コンテナーに対する Azure Monitor によって格納される日付はすべて UTC 形式です。

始める前に、Azure Monitor のアラートに慣れていない場合は、「[Microsoft Azure のアラートの概要](../platform/alerts-overview.md)」をご覧ください。 ログ クエリを使ったアラートの詳細については、「[Azure Monitor でのログ アラート](../platform/alerts-unified-log.md)」をご覧ください。

## <a name="resource-utilization-log-search-queries"></a>リソース使用率のログ検索クエリ
このセクションのクエリは、各アラート シナリオをサポートするために提供されています。 これらのクエリは、後述の[アラートの作成](#create-alert-rule)に関するセクションの手順 7. で必要になります。  

次のクエリでは、平均 CPU 使用率をメンバー ノードの 1 分ごとの平均 CPU 使用率として算出します。  

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

次のクエリでは、平均メモリ使用率をメンバー ノードの 1 分ごとの平均メモリ使用率として算出します。

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
>以下のクエリには、クラスター名とコントローラー名のプレースホルダー文字列値 (<your-cluster-name> と <your-controller-name>) が含まれています。 アラートを設定する前に、プレースホルダーを環境に固有の値に置き換えてください。 


次のクエリでは、コントローラー内のすべてのコンテナーの平均 CPU 使用率を、コントローラー内の各コンテナー インスタンスの 1 分ごとの平均 CPU 使用率として計算し、コンテナーに設定された上限に対する割合で表します。

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

次のクエリでは、コントローラー内のすべてのコンテナーの平均メモリ使用率を、コントローラー内の各コンテナー インスタンスの 1 分ごとの平均メモリ使用率として計算し、コンテナーに設定された上限に対する割合で表します。

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

## <a name="create-alert-rule"></a>アラート ルールの作成
前述のログ検索ルールのいずれかを使用して、Azure Monitor でログ アラートを作成するには、次の手順を実行します。  

>[!NOTE]
>コンテナーのリソース使用率のアラート ルールを作成する場合、次の手順では、「[ログ アラートの API の基本設定を切り替える](../platform/alerts-log-api-switch.md)」の説明に従って、新しい Log Alerts API に切り替える必要があります。 
>

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. Azure portal の左側のウィンドウで **[モニター]** を選択します。 **[分析情報]** セクションで **[コンテナー]** を選択します。    
3. **[監視対象クラスター]** タブで、クラスターの名前をクリックして一覧からクラスターを選択します。
4. 左側のウィンドウの **[Monitoring]\(監視\)** セクションの下で、**[ログ]** を選択して Azure Monitor の [ログ] ページを開きます。ここで Azure Log Analytics クエリの記述と実行を行います。
5. **[ログ]** ページで **[+ 新しいアラート ルール]** をクリックします。
6. **[条件]** セクションの下で、定義済みのカスタム ログの状態 **[Whenever the Custom log search is <logic undefined>]\(カスタム ログ検索が次の条件を満たすときは常に\)** をクリックします。 シグナルの種類として **[カスタム ログ検索]** が自動的に選択されます。これは、Azure Monitor の [ログ] ページから直接アラート ルールの作成を開始したためです。  
7. 前述の[クエリ](#resource-utilization-log-search-queries)のいずれかを **[検索クエリ]** フィールドに貼り付けます。 

8. 次の情報を指定して、アラートを構成します。

    a. **[基準]** ドロップダウン リストで **[メトリック測定]** を選択します。 メトリック測定では、クエリの対象となったオブジェクトのうち、値が指定されたしきい値を上回っているオブジェクトについて、それぞれ別個にアラートが生成されます。  
    b. **[条件]** で **[より大きい]** を選択し、最初の基準となる **[しきい値]** として「**75**」を入力するか、自分の条件を満たす値を入力します。  
    c. **[アラートをトリガーする基準]** セクションで、**[連続する違反]** を選択し、ドロップダウン リストから **[より大きい]** を選択して値「**2**」を入力します。  
    d. コンテナーの CPU またはメモリの使用率に関するアラートを構成する場合は、**[集計]** のドロップダウン リストから **[ContainerName]** を選択します。  
    e. **[評価基準]** セクションで、**[期間]** の値を 60 分に変更します。 ルールは 5 分ごとに実行され、現在の時刻から直近の 1 時間以内に作成されたレコードが返されます。 この期間をより広い時間枠に設定すると、潜在的なデータの待機時間の原因となるため、クエリでは、アラートが決して発生しない検知漏れを回避するために確実にデータが返されるようにします。 

9. **[完了]** をクリックして、アラート ルールを完成させます。
10. **[アラート ルール名]** フィールドで、アラートの名前を指定します。 アラートの詳細を説明する **[説明]** を指定し、表示されたオプションから適切な重大度を選択します。
11. 作成時にアラート ルールをすぐにアクティブ化するには、**[ルールの作成時に有効にする]** の既定値を受け入れます。
12. 最後の手順では、既存の **[アクション グループ]** を選択するか新規作成します。これにより、アラートがトリガーされるたびに同じアクションが実行され、定義する各ルールに同じアクションを使用できます。 お客様の IT または DevOps オペレーションでのインシデントの管理方法に基づいて構成してください。 
13. **[アラート ルールの作成]** をクリックしてアラート ルールを完成させます。 すぐに実行が開始されます。

## <a name="next-steps"></a>次の手順

* [ログ クエリの例](container-insights-analyze.md#search-logs-to-analyze-data)をいくつか確認し、事前定義されたクエリや例を評価して使用したり、他のアラート シナリオ向けにカスタマイズしたりする方法を学習します。 
* Azure Monitor を使用して、AKS クラスターの他の側面を監視する方法を引き続き学習するには、[Azure Kubernetes Service の正常性の表示](container-insights-analyze.md)に関するページをご覧ください。
