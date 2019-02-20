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
ms.date: 02/12/2019
ms.author: magoedte
ms.openlocfilehash: c275d50ab927895eca3aa018b71ab6989a4dde5a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238841"
---
# <a name="how-to-set-up-alerts-in-azure-monitor-for-container-performance-problems"></a>Azure Monitor でコンテナーのパフォーマンスの問題に関するアラートを設定する方法
コンテナーに対する Azure Monitor は、Azure Kubernetes Service (AKS) でホストされた Azure Container Instances またはマネージド Kubernetes クラスターにデプロイされているコンテナー ワークロードのパフォーマンスを監視します。 

この記事では、クラスターのノードでプロセッサおよびメモリの使用率が定義されたしきい値を超えたときにアラートを有効にする方法について説明します。

## <a name="create-alert-on-cluster-cpu-or-memory-utilization"></a>クラスターの CPU またはメモリの使用率に関するアラートを作成する
クラスターの CPU またはメモリの使用率が高いときにアラートするには、提供されたログ クエリに基づくメトリック測定アラート ルールを作成します。 クエリでは、`now` 演算子を使って日時を現在と比較して 1 時間戻ります。 コンテナーに対する Azure Monitor によって格納される日付はすべて UTC 形式です。  

始める前に、Azure Monitor のアラートに慣れていない場合は、「[Microsoft Azure のアラートの概要](../platform/alerts-overview.md)」をご覧ください。 ログ クエリを使ったアラートの詳細については、「[Azure Monitor でのログ アラート](../platform/alerts-unified-log.md)」をご覧ください。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. Azure portal の左側のウィンドウで **[モニター]** を選択します。 **[分析情報]** セクションで **[コンテナー]** を選択します。    
3. **[監視対象クラスター]** タブで、クラスターの名前をクリックして一覧からクラスターを選択します。
4. 左側のウィンドウの **[Monitoring]\(監視\)** セクションの下で、**[ログ]** を選択して Azure Monitor の [ログ] ページを開きます。ここで Azure Log Analytics クエリの記述と実行を行います。
5. **[ログ]** ページで **[+ 新しいアラート ルール]** をクリックします。
6. **[条件]** セクションの下で、定義済みのカスタム ログの状態 **[Whenever the Custom log search is <logic undefined>]\(カスタム ログ検索が次の条件を満たすときは常に\)** をクリックします。 シグナルの種類として **[カスタム ログ検索]** が自動的に選択されます。これは、Azure Monitor の [ログ] ページから直接アラート ルールの作成を開始したためです。  
7. 下記のクエリのいずれかを **[検索クエリ]** フィールドに貼り付けます。 次のクエリでは、平均 CPU 使用率をメンバー ノードの 1 分ごとの平均 CPU 使用率として算出します。

    ```
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

    ```
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

8. 次の情報を指定して、アラートを構成します。

    a. **[基準]** ドロップダウン リストで **[メトリック測定]** を選択します。 メトリック測定では、クエリの対象となったオブジェクトのうち、値が指定されたしきい値を上回っているオブジェクトについて、それぞれ別個にアラートが生成されます。  
    b. **[条件]** で **[より大きい]** を選択し、最初の基準となる **[しきい値]** として「**75**」を入力するか、自分の条件を満たす値を入力します。  
    c. **[アラートをトリガーする基準]** セクションで、**[連続する違反]** を選択し、ドロップダウン リストから **[より大きい]** を選択して値「**2**」を入力します。  
    d. **[評価基準]** セクションで、**[期間]** の値を 60 分に変更します。 ルールは 5 分ごとに実行され、現在の時刻から直近の 1 時間以内に作成されたレコードが返されます。 この期間をより広い時間枠に設定すると、潜在的なデータの待機時間の原因となるため、クエリでは、アラートが決して発生しない検知漏れを回避するために確実にデータが返されるようにします。 

9. **[完了]** をクリックして、アラート ルールを完成させます。
10. **[アラート ルール名]** フィールドで、アラートの名前を指定します。 アラートの詳細を説明する **[説明]** を指定し、表示されたオプションから適切な重大度を選択します。
11. 作成時にアラート ルールをすぐにアクティブ化するには、**[ルールの作成時に有効にする]** の既定値を受け入れます。
12. 最後の手順では、既存の **[アクション グループ]** を選択するか新規作成します。これにより、アラートがトリガーされるたびに同じアクションが実行され、定義する各ルールに同じアクションを使用できます。 お客様の IT または DevOps オペレーションでのインシデントの管理方法に基づいて構成してください。 
13. **[アラート ルールの作成]** をクリックしてアラート ルールを完成させます。 すぐに実行が開始されます。

## <a name="next-steps"></a>次の手順

* [ログ クエリの例](container-insights-analyze.md#search-logs-to-analyze-data)をいくつか確認し、事前定義されたクエリや例を評価して使用したり、他のアラート シナリオ向けにカスタマイズしたりする方法を学習します。 
* Azure Monitor を使用して、AKS クラスターの他の側面を監視する方法を引き続き学習するには、[Azure Kubernetes Service の正常性の表示](container-insights-analyze.md)に関するページをご覧ください。
