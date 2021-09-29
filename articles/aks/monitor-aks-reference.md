---
title: AKS データの監視に関するリファレンス
description: AKS を監視する際に必要となる重要なリファレンス資料
ms.service: container-service
ms.custom: subject-monitoring
ms.date: 07/29/2021
ms.topic: reference
ms.openlocfilehash: 9e8a12f8d3075bade1619ce766c74dc8cac23481
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124816056"
---
# <a name="monitoring-aks-data-reference"></a>AKS データの監視に関するリファレンス

AKS の監視データの収集と分析の詳細については、[AKS の監視](monitor-aks.md)に関する記事を参照してください。

## <a name="metrics"></a>メトリック

次の表に、AKS に関して収集されるプラットフォーム メトリックの一覧を示します。  特定の種類ごとのメトリックの詳細な一覧については、各リンクを参照してください。

|メトリックの種類 | リソース プロバイダー/種類の名前空間<br/> および個々のメトリックへのリンク |
|-------|-----|
| マネージド クラスター | [Microsoft.ContainerService/managedClusters](../azure-monitor/essentials/metrics-supported.md#microsoftcontainerservicemanagedclusters)
| 接続されたクラスター | [microsoft.kubernetes/connectedClusters](../azure-monitor/essentials/metrics-supported.md#microsoftkubernetesconnectedclusters)
| 仮想マシン| [Microsoft.Compute/virtualMachines](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachines) |
| 仮想マシン スケール セット | [Microsoft.Compute/virtualMachineScaleSets](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
| 仮想マシン スケール セット仮想マシン | [Microsoft.Compute/virtualMachineScaleSets/virtualMachines](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesetsvirtualmachines)|

詳細については、[Azure Monitor でサポートされているすべてのプラットフォーム メトリック](../azure-monitor/essentials/metrics-supported.md)の一覧を参照してください。

## <a name="metric-dimensions"></a>メトリック ディメンション

次の表に、AKS メトリックの[ディメンション](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)の一覧を示します。 

<!-- listed here /azure/azure-monitor/essentials/metrics-supported#microsoftcontainerservicemanagedclusters-->

| ディメンション名 | 説明 |
| ------------------- | ----------------- |
| requestKind | 要求の種類で分割するために、*配信要求* などのメトリックで使用。 |
| condition | 条件の種類で分割するために、*さまざまなノードの状態の条件* や *準備完了状態のポッドの数* などのメトリックで使用。 |
| status | 条件の状態で分割するために、*さまざまなノードの状態の条件* などのメトリックで使用。 |
| status2 | 条件の状態で分割するために、*さまざまなノードの状態の条件* などのメトリックで使用。  |
| node | ノードの名前で分割するために、*CPU 使用率 (ミリコア単位)* などのメトリックで使用。 |
| phase | ポッドのフェーズで分割するために、*フェーズごとのポッドの数* などのメトリックで使用。 |
| namespace | ポッドの名前空間で分割するために、*フェーズごとのポッドの数* などのメトリックで使用。 |
| ポッド | ポッドの名前で分割するために、*フェーズごとのポッドの数* などのメトリックで使用。 |
| nodepool | ノードプールの名前で分割するために、*使用済みディスク (バイト数)* などのメトリックで使用。 |
| device | デバイスの名前で分割するために、*使用済みディスク (バイト数)* などのメトリックで使用。 |

## <a name="resource-logs"></a>リソース ログ

次の表に、AKS に関して収集できるリソース ログのカテゴリを一覧で示します。 これらは、AKS コントロール プレーン コンポーネントのログです。 これらのログを収集するための診断設定の作成に関する情報と、どれを有効にすべきかに関する推奨事項については、「[監視の構成](monitor-aks.md#configure-monitoring)」を参照してください。 クエリの例については、「[Container insights のログのクエリを実行する方法](../azure-monitor/containers/container-insights-log-query.md#resource-logs)」を参照してください。

[Azure Monitorでサポートされているすべてのリソース ログのカテゴリの種類](../azure-monitor/essentials/resource-logs-schema.md)の一覧も参照してください。 

| カテゴリ                | 説明 |
|:---|:---|
| cluster-autoscaler       | 予想できないことのある AKS クラスターのスケールアップまたはスケールダウンの理由を理解できます。 この情報は、クラスター内で興味深い事象が発生した可能性のある期間を関連付けるのにも役立ちます。 |
| guard                   | マネージド Azure Active Directory および Azure RBAC の監査。 マネージド Azure AD の場合、これにはトークン入力とユーザー情報出力が含まれます。Azure RBAC の場合は、アクセス レビューの入力と出力が含まれます。 |
| kube-apiserver          | API サーバーからのログ。 |
| kube-audit              | get、list、create、update、delete、patch、post を含む、各監査イベントの監査ログ データ。 |
| kube-audit-admin        | kube-audit ログ カテゴリのサブセット。 ログから get および list の監査イベントを除外することで、ログの数を大幅に削減します。 |
| kube-controller-manager | Kubernetes と Azure コントロール プレーンの間で発生する可能性がある問題を、より詳しく把握できます。 一般的な例は、AKS クラスターに Azure と対話するためのアクセス許可がない場合です。 |
| kube-scheduler          | スケジューラからのログ。 |
| AllMetrics              | すべてのプラットフォーム メトリックが含まれます。 これらの値を Log Analytics ワークスペースに送信します。そのワークスペースで、ログ クエリを使用して他のデータと合わせて評価されます。 |

## <a name="azure-monitor-logs-tables"></a>Azure Monitor ログ テーブル

このセクションでは、AKS に関連し、Log Analytics によるクエリに使用できるすべての Azure Monitor ログのテーブルを参照します。 



|リソースの種類 | メモ |
|-------|-----|
| [Kubernetes サービス](/azure/azure-monitor/reference/tables/tables-resourcetype#kubernetes-services) | AKS によって使用されるすべてのテーブルの一覧とその構造体の説明については、このリンクを参照してください。 |


すべての Azure Monitor ログ/Log Analytics テーブルのリファレンスについては、[Azure Monitor ログ テーブル リファレンス](/azure/azure-monitor/reference/tables/tables-resourcetype)に関するページを参照してください。


## <a name="activity-log"></a>アクティビティ ログ

次の表に、[アクティビティ ログ](../azure-monitor/essentials/activity-log.md)で作成される可能性がある AKS に関連する操作の例を一覧で示します。 アクティビティ ログは、クラスターの作成日時やその構成の変更日時などの情報を追跡するために使用されます。 この情報は、ポータルで確認することも、アクティビティ ログ アラートを作成して、イベントの発生時にプロアクティブに通知されるようにすることもできます。

| 操作 | 説明 |
|:---|:---|
| Microsoft.ContainerService/managedClusters/write | マネージド クラスターの作成または更新 |
| Microsoft.ContainerService/managedClusters/delete | マネージド クラスターの削除 |
| Microsoft.ContainerService/managedClusters/listClusterMonitoringUserCredential/action | clusterMonitoringUser 資格情報の一覧表示 |
| Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | clusterAdmin 資格情報の一覧表示 |
| Microsoft.ContainerService/managedClusters/agentpools/write | エージェント プールの作成または更新 |

使用可能なログ エントリの完全な一覧については、[Microsoft.ContainerService リソース プロバイダー オプション](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)に関する記事を参照してください。

アクティビティ ログ エントリのスキーマの詳細については、[アクティビティ ログのスキーマ](../azure-monitor/essentials/activity-log-schema.md)に関する記事を参照してください。 

## <a name="see-also"></a>関連項目

- Azure AKS の監視の詳細については、[Azure AKS の監視](monitor-aks.md)に関する記事を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。