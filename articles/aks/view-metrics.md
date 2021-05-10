---
title: Azure Kubernetes Service (AKS) のクラスター メトリックを表示する
description: Azure Kubernetes Service (AKS) のクラスター メトリックを表示します。
services: container-service
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: 32d8745e0ea780b5f86588fabdc25b244cf2cd8e
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105975814"
---
# <a name="view-cluster-metrics-for-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) のクラスター メトリックを表示する

AKS には、API サーバーとクラスター オートスケーラー、およびクラスター ノードなど、コントロール プレーン用の一連のメトリックが用意されています。 これらのメトリックを使用すると、クラスターの正常性を監視し、問題のトラブルシューティングを行うことができます。 クラスターのメトリックは、Azure portal を使用して表示できます。

> [!NOTE]
> これらの AKS クラスター メトリックは、[Kubernetes で提供されているメトリック][kubernetes-metrics]のサブセットと重複します。

## <a name="view-metrics-for-your-aks-cluster-using-the-azure-portal"></a>Azure portal を使用して AKS クラスターのメトリックを表示する

AKS クラスターのメトリックを表示するには、次のようにします。

1. [Azure portal][azure-portal] にサインインし、AKS クラスターに移動します。
1. 左側の *[監視]* で *[メトリック]* を選択します。
1. 表示するメトリックのグラフを作成します。 たとえば、次のようにしてグラフを作成します。
    1. " *[スコープ]* " では、お使いのクラスターを選択します。
    1. " *[メトリック名前空間]* " では、" *[コンテナーのサービス (マネージド) 標準メトリック]* " を選択します。
    1. " *[メトリック]* " では、" *[ポッド]* " で " *[Number of Pods by phase]\(フェーズごとのポッド数\)* " を選択します。
    1. " *[集計]* " では、" *[Avg]\(平均\)* " を選択します。

:::image type="content" source="media/metrics/metrics-chart.png" alt-text="{alt-text}":::

上の例は、*myAKSCluster* の平均ポッド数のメトリックを示しています。

## <a name="available-metrics"></a>使用可能なメトリック

次のクラスター メトリックを使用できます。

| 名前 | グループ | ID | 説明 |
| --- | --- | --- | ---- |
| 配信要求 | API サーバー (プレビュー) |apiserver_current_inflight_requests | 要求の種類ごとの API サーバーで現在アクティブな実行中の要求の最大数。 |
| クラスターの正常性 | クラスター オートスケーラー (プレビュー) | cluster_autoscaler_cluster_safe_to_autoscale | クラスター オートスケーラーがクラスターでアクションを実行するかどうかを決定します。 |
| スケールダウン クールダウン | クラスター オートスケーラー (プレビュー) | cluster_autoscaler_scale_down_in_cooldown | スケールダウンがクールダウン内かどうかを判断します。この期間中、ノードは削除されません。 |
| 不要なノード | クラスター オートスケーラー (プレビュー) | cluster_autoscaler_unneeded_nodes_count | クラスター オートスケーラーではこれらのノードを削除候補としてマークし、それらは最終的に削除されます。 |
| スケジュール不可ポッド | クラスター オートスケーラー (プレビュー) | cluster_autoscaler_unschedulable_pods_count | クラスター内で現在スケジュール不可のポッドの数。 |
| マネージド クラスターで使用可能な CPU コアの合計数 | Nodes | kube_node_status_allocatable_cpu_cores | マネージド クラスターで使用可能な CPU コアの合計数。 |
| マネージド クラスターで使用可能なメモリの合計量 | Nodes | kube_node_status_allocatable_memory_bytes | マネージド クラスターで使用可能なメモリの合計量。 |
| さまざまなノードの状態の条件 | Nodes | kube_node_status_condition | さまざまなノードの状態の条件 |
| CPU 使用率 (ミリコア単位) | ノード (プレビュー) | node_cpu_usage_millicores | クラスター全体で集計された CPU 使用率の測定値 (ミリコア単位)。 |
| CPU 使用率 (%) | ノード (プレビュー) | node_cpu_usage_percentage | クラスター全体で集計された平均 CPU 使用率の測定値 (%)。 |
| メモリ RSS (バイト数) | ノード (プレビュー) | node_memory_rss_bytes | 使用中のコンテナー RSS メモリ (バイト数)。 |
| メモリ RSS (%) | ノード (プレビュー) | node_memory_rss_percentage | 使用中のコンテナー RSS メモリ (%)。 |
| メモリ ワーキング セット (バイト数) | ノード (プレビュー) | node_memory_working_set_bytes | 使用中のコンテナーのワーキング セット メモリ (バイト数)。 |
| メモリ ワーキング セット (%) | ノード (プレビュー) | node_memory_working_set_percentage | 使用中のコンテナーのワーキングセット メモリ (%)。 |
| 使用済みディスク (バイト数) | ノード (プレビュー) | node_disk_usage_bytes | デバイスごとの使用済みディスク領域 (バイト数)。 |
| 使用済みディスク (%) | ノード (プレビュー) | node_disk_usage_percentage | デバイスごとの使用済みディスク領域 (%)。 |
| ネットワーク (バイト数) | ノード (プレビュー) | node_network_in_bytes | ネットワーク受信バイト数。 |
| ネットワーク送信バイト数 | ノード (プレビュー) | node_network_out_bytes | ネットワーク転送バイト数。 |
| 準備完了状態のポッドの数 | ポッド | kube_pod_status_ready | "*準備完了*" 状態のポッドの数。 |
| フェーズごとのポッドの数 | ポッド | kube_pod_status_phase | フェーズごとのポッドの数。 |

> [!IMPORTANT]
> プレビュー段階のメトリックは、名前や説明を含め、プレビュー段階の間に更新または変更される可能性があります。

## <a name="next-steps"></a>次のステップ

AKS のクラスター メトリックに加えて、AKS クラスターで Azure Monitor を使用することもできます。 AKS での Azure Monitor の使用の詳細については、[Azure Monitor for containers][aks-azure-monitory] に関する記事を参照してください。

[aks-azure-monitory]: ../azure-monitor/containers/container-insights-overview.md
[azure-portal]: https://portal.azure.com/
[kubernetes-metrics]: https://kubernetes.io/docs/concepts/cluster-administration/system-metrics/