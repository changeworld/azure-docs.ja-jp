---
title: Azure Monitor for containers からログを照会する方法 |Microsoft Docs
description: Azure Monitor for containers は、メトリックとログ データを収集します。この記事では、レコードについて説明し、サンプル クエリを紹介します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: 66fc55d8c3dbb8487d1e796d5f30b08a94f717f6
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012967"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Azure Monitor for containers からログを照会する方法
Azure Monitor for containers では、コンテナー ホストおよびコンテナーからパフォーマンスのメトリック、インベントリ データ、および正常性状態の情報が収集され、Azure Monitor の Log Analytics ワークスペースに転送されます。 データは、3 分ごとに収集されます。 このデータは、Azure Monitor で[クエリ](../../azure-monitor/log-query/log-query-overview.md)用に使用できます。 このデータは、移行計画、容量の分析、探索、必要に応じたパフォーマンスのトラブルシューティングといったシナリオに適用できます。

## <a name="container-records"></a>コンテナー レコード

次の表には、コンテナーの Azure Monitor によって収集されるレコードと、ログ検索結果に表示されるデータ型の例を示します。

| データ型 | ログ検索のデータ型 | フィールド |
| --- | --- | --- |
| ホストとコンテナーのパフォーマンス | `Perf` | Computer、ObjectName、CounterName &#40;%Processor Time、Disk Reads MB、Disk Writes MB、Memory Usage MB、Network Receive Bytes、Network Send Bytes、Processor Usage sec、Network&#41;、CounterValue、TimeGenerated、CounterPath、SourceSystem |
| コンテナー インベントリ | `ContainerInventory` | TimeGenerated、Computer、container name、ContainerHostname、Image、ImageTag、ContainerState、ExitCode、EnvironmentVar、Command、CreatedTime、StartedTime、FinishedTime、SourceSystem、ContainerID、ImageID |
| コンテナー ログ | `ContainerLog` | TimeGenerated、Computer、image ID、container name、LogEntrySource、LogEntry、SourceSystem、ContainerID |
| コンテナー ノード インベントリ | `ContainerNodeInventory`| TimeGenerated、Computer、ClassName_s、DockerVersion_s、OperatingSystem_s、Volume_s、Network_s、NodeRole_s、OrchestratorType_s、InstanceID_g、SourceSystem|
| Kubernetes クラスター内のポッドのインベントリ | `KubePodInventory` | TimeGenerated、Computer、ClusterId、ContainerCreationTimeStamp、PodUid、PodCreationTimeStamp、ContainerRestartCount、PodRestartCount、PodStartTime、ContainerStartTime、ServiceName、ControllerKind、ControllerName、ContainerStatus、ContainerStatusReason、ContainerID、ContainerName、Name、PodLabel、Namespace、PodStatus、ClusterName、PodIp、SourceSystem |
| Kubernetes クラスター内のノード部分のインベントリ | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes イベント | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Kubernetes クラスター内のサービス | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Kubernetes クラスターのノード部分のパフォーマンス メトリック | Perf &#124; where ObjectName == “K8SNode” | Computer、ObjectName、CounterName &#40;cpuAllocatableBytes、memoryAllocatableBytes、cpuCapacityNanoCores、memoryCapacityBytes、memoryRssBytes、cpuUsageNanoCores、memoryWorkingsetBytes、restartTimeEpoch&#41;、CounterValue、TimeGenerated、CounterPath、SourceSystem | 
| Kubernetes クラスターのコンテナー部分のパフォーマンス メトリック | Perf &#124; where ObjectName == “K8SContainer” | CounterName &#40;cpuRequestNanoCores、memoryRequestBytes、cpuLimitNanoCores、memoryWorkingSetBytes、restartTimeEpoch、cpuUsageNanoCores、memoryRssBytes&#41;、CounterValue、TimeGenerated、CounterPath、SourceSystem | 
| InsightsMetrics | Computer、Name、Namespace、Origin、SourceSystem、Tags、TimeGenerated、Type、Value |

## <a name="search-logs-to-analyze-data"></a>データを分析するためのログの検索
Azure Monitor ログを使用することにより、傾向の特定、ボトルネックの診断、予想を行ったり、データを関連付けて現在のクラスター構成のパフォーマンスが最適化されているかどうかを判断したりできます。 すぐに使用できる事前定義のログ検索が提供されています。また、検索結果として返される情報の表示方法をカスタマイズすることもできます。 

プレビュー ウィンドウで **[View Kubernetes event logs]\(Kubernetes イベント ログの表示\)** または **[コンテナー ログの表示]** オプションを選択することにより、ワークスペース内のデータを対話式に分析できます。 **[ログ検索]** ページは、元の Azure portal ページの右側に表示されます。

![Log Analytics でデータを解析する](./media/container-insights-analyze/container-health-log-search-example.png)   

ワークスペースに転送されるコンテナー ログ出力は、STDOUT および STDERR です。 Azure Monitor では、Azure マネージド Kubernetes (AKS) が監視され、大量のデータが生成されるため、現時点では Kube システムのデータは収集されません。 

### <a name="example-log-search-queries"></a>検索クエリの例
多くの場合、1、2 個の例を使ってクエリを作成し、その後、要件に合わせて変更するとうまくいきます。 より高度なクエリを作成できるように、次のサンプル クエリを試すことができます。

| Query | 説明 | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | コンテナーのライフ サイクル情報をすべて一覧表示します| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Kubernetes イベント|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | イメージ インベントリ | 
| **[折れ線] グラフの表示オプションを選択する**:<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" &#124; summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | コンテナー CPU | 
| **[折れ線] グラフの表示オプションを選択する**:<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes" &#124; summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | コンテナー メモリ |

## <a name="next-steps"></a>次の手順
Azure Monitor for containers には、定義済みの一連のアラートは含まれません。 [Azure Monitor for containers を使用したパフォーマンス アラートの作成](container-insights-alerts.md)に関するページを読んで、CPU やメモリの使用率が高い場合に推奨アラートを作成し、DevOps や運用プロセスまたは手順をサポートする方法について学習します。 