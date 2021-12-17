---
title: Azure Monitor のコンテナー正常性機能を使用して Azure Kubernetes Service (AKS) を監視する
description: Azure Monitor を使用して、AKS クラスターとそのワークロードの正常性とパフォーマンスを監視する方法について説明します。
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/29/2021
ms.openlocfilehash: 7c0da13660f3fc987766554f40bc36020845dd85
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287402"
---
# <a name="monitoring-azure-kubernetes-service-aks-with-azure-monitor"></a>Azure Monitor のコンテナー正常性機能を使用して Azure Kubernetes Service (AKS) を監視する
このシナリオでは、Azure Monitor を使用して、Azure Kubernetes Service (AKS) の正常性とパフォーマンスを監視する方法について説明します。 これには、傾向を識別するために収集されたデータの監視、分析、視覚化に不可欠なテレメトリのコレクションと、重大な問題が予防的に通知されるアラートを構成する方法が含まれます。

「[クラウド監視ガイド](/azure/cloud-adoption-framework/manage/monitor/)」では、Azure リソースのために焦点を当てる必要がある[主な監視目標](/azure/cloud-adoption-framework/strategy/monitoring-strategy#formulate-monitoring-requirements)を定義します。 このシナリオでは、Azure Monitor を使用する正常性と状態の監視に焦点を当てます。

## <a name="scope-of-the-scenario"></a>シナリオの範囲
このシナリオは、Azure Monitor を使用して AKS を監視するお客様を対象にしています。 このコンテンツは、シナリオの後続の更新で追加される可能性があります。ただし、以下の内容は含まれません。

- Azure Arc 対応 Kubernetes の既存のコンテンツを参照する場合を除く、Azure 外部の Kubernetes クラスターの監視。 
- Azure Monitor とコンテナーの分析情報のギャップを埋める目的以外の Azure Monitor ではないツールを使用した AKS の監視。

> [!NOTE]
> Azure Monitor は、クラウド リソースの可用性とパフォーマンスを監視するために設計されました。 Azure Monitor に格納されている運用データはセキュリティ インシデントの調査に役立つ場合がある一方で、Azure の他のサービスは、セキュリティを監視するために設計されました。 AKS のセキュリティは [Microsoft Sentinel](../sentinel/overview.md) と [Microsoft Defender for Cloud](../security-center/security-center-introduction.md) によって監視されます。 Azure のセキュリティ監視ツールとその Azure Monitor との関連付けの説明については、「[Azure Monitor を使用して Azure 仮想マシンを監視する](../azure-monitor/vm/monitor-virtual-machine-security.md)」を参照してください。
>
> セキュリティ サービスを使用して AKS を監視する方法の詳細については、[Microsoft Defender for Kubernetes - 利点と機能](../security-center/defender-for-kubernetes-introduction.md)および [Azure Kubernetes Service (AKS) 診断ログの Microsoft Sentinel への接続](../sentinel/data-connectors-reference.md#azure-kubernetes-service-aks)に関する記事を参照してください。
## <a name="container-insights"></a>コンテナーの分析情報
AKS は、他の Azure リソースと同様に、[プラットフォーム メトリックとリソース ログ](monitor-aks-reference.md)を生成します。これは、その基本的な正常性とパフォーマンスを監視するために使用できます。 [コンテナーの分析情報](../azure-monitor/containers/container-insights-overview.md)を有効にして、この監視を広げます。 コンテナーの分析情報は Azure Monitor の機能であり、他のクラスター構成に加えて、AKS でホストされているマネージド Kubernetes クラスターの正常性とパフォーマンスを監視します。 コンテナーの分析情報では、さまざまな監視シナリオで収集されたデータを分析する対話型のビューとブックが提供されます。 

[Prometheus](https://prometheus.io/) と [Grafana](https://www.prometheus.io/docs/visualization/grafana/) は、Kubernetes 監視用の広く普及しているオープン ソース ツールを利用した CNCF です。 AKS では、Prometheus 形式で多くのメトリックが公開されています。これにより、Prometheus は監視のための人気のある選択肢になっています。 [コンテナーの分析情報](../azure-monitor/containers/container-insights-overview.md)には、AKS とのネイティブ統合、重要なメトリックとログの収集、特定された問題に関するアラート、ブックによる視覚化の提供があります。 また、特定の Prometheus メトリックも収集し、多くのネイティブ Azure Monitor 分析情報が Prometheus メトリックの上に構築されます。 コンテナーの分析情報は、スタンドアロン ツールとして Prometheus が提供しないログ収集を含め、AKS の E2E 監視を補完して完備させます。 多くのお客様は、Prometheus の統合と Azure Monitor を E2E 監視のために併用します。

コンテナーの分析情報の使用の詳細については、「[Container insights の概要](../azure-monitor/containers/container-insights-overview.md)」を参照してください。 以下の「[コンテナーの分析情報を使用して AKS のレイヤーを監視する](#monitor-layers-of-aks-with-container-insights)」では、コンテナーの分析情報のさまざまな機能と、それらでサポートされる監視シナリオが紹介されています。

:::image type="content" source="media/monitor-aks/container-insights.png" alt-text="Container insights" lightbox="media/monitor-aks/container-insights.png":::


## <a name="configure-monitoring"></a>監視の構成
以下のセクションでは、Azure Monitor を使用して AKS クラスターの完全な監視を構成するために必要な手順を説明します。
### <a name="create-log-analytics-workspace"></a>Log Analytics ワークスペースの作成
コンテナーの分析情報をサポートし、AKS クラスターに関する他のテレメトリを収集して分析するには、少なくとも 1 つの Log Analytics ワークスペースが必要です。 ワークスペースのコストは発生しませんが、データを収集するときに、取り込みとリテンション期間のコストが発生します。 「[Azure Monitor ログで使用量とコストを管理する](../azure-monitor/logs/manage-cost-storage.md)」をご覧ください。

Azure Monitor を使い始めたばかりの場合は、1 つのワークスペースから始めて、要件の発展に合わせて追加のワークスペースを作成することを検討してください。 多くの環境では、監視しているすべての Azure リソースに対して 1 つのワークスペースが使用されます。 [Microsoft Defender for Cloud と Microsoft Sentinel](../azure-monitor/vm/monitor-virtual-machine-security.md) で使用されるワークスペースを共有することもできますが、多くのお客様は、可用性とパフォーマンスのテレメトリをセキュリティ データから分離することを選択します。 

ワークスペースの構成を設計するために検討する必要があるロジックの詳細については、「[Azure Monitor ログのデプロイの設計](../azure-monitor/logs/design-logs-deployment.md)」を参照してください。

### <a name="enable-container-insights"></a>Container Insights を有効にする
AKS クラスターに対して Container Insights を有効にした場合、コンテナー化されたバージョンの [Log Analytics エージェント](../agents/../azure-monitor/agents/log-analytics-agent.md)がデプロイされ、これによりデータが Azure Monitor に送信されます。 新規または既存の AKS クラスターを使用するかどうかに応じて、これを有効にする方法は複数あります。 前提条件と構成オプションについては、「[コンテナー分析情報を有効にする](../azure-monitor/containers/container-insights-onboard.md)」を参照してください。


### <a name="configure-collection-from-prometheus"></a>Prometheus からコレクションを構成する
Container Insights を使用すると、Prometheus サーバーを必要とせずに、Log Analytics ワークスペースで特定の Prometheus メトリックを収集できます。 Azure Monitor 機能をコンテナーの分析情報によって収集された他のデータと一緒に使用して、このデータを分析できます。 この構成の詳細については、「[Container insights で Prometheus メトリックのスクレイピングを構成する](../azure-monitor/containers/container-insights-prometheus-integration.md)」を参照してください。


### <a name="collect-resource-logs"></a>リソース ログを収集する
AKS コントロール プレーン コンポーネントのログは、[リソース ログ](../azure-monitor/essentials/resource-logs.md)として Azure に実装されます。 現在 Container Insights では、これらのログは使用されていないため、それらを表示および分析するために独自のログ クエリを作成する必要があります。 これらのログの構造とそれらのためのクエリを記述する方法の詳細については、「[Container insights のログのクエリを実行する方法](../azure-monitor/containers/container-insights-log-query.md#resource-logs)」を参照してください。

リソース ログを収集するには、診断設定を作成する必要があります。 複数の診断設定を作成して、異なるログ セットを異なる場所に送信します。 AKS クラスター用の診断の作成については、「[プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。 

リソース ログをワークスペースに送信するにはコストがかかるため、使用するログ カテゴリのみを収集する必要があります。 情報を保持する必要があっても、すぐには分析に使用する必要がない場合、コストを削減するためにログを Azure ストレージ アカウントに送信します。  AKS に対して使用できるカテゴリの説明については「[リソース ログ](monitor-aks-reference.md#resource-logs)」を参照し、ログ データの取り込みと保持のコストの詳細については「[Azure Monitor ログで使用量とコストを管理する](../azure-monitor/logs/manage-cost-storage.md)」を参照してください。 最初は最小限の数のカテゴリを収集することから始め、ニーズの増加と関連するコストの理解に応じて、診断設定を変更して追加のカテゴリを収集します。

最初に有効にするリソース ログがわからない場合は、最も一般的な顧客の要件に基づく次の表の推奨事項を使用します。 後で情報が必要なことがわかったら、その他のカテゴリを有効にします。

| カテゴリ | 有効にするかどうか | 宛先 |
|:---|:---|:---|
| cluster-autoscaler      | 自動スケーリングが有効になっている場合は有効化 | Log Analytics ワークスペース |
| guard                   | Azure Active Directory Domain Service が有効になっている場合は有効化 | Log Analytics ワークスペース |
| kube-apiserver          | 有効化 | Log Analytics ワークスペース |
| kube-audit              | 有効化 | Azure ストレージ。 これにより、コストを最小限に抑えながら、監査ログが監査担当者に必要な場合は維持します。 |
| kube-audit-admin        | 有効化 | Log Analytics ワークスペース |
| kube-controller-manager | 有効化 | Log Analytics ワークスペース |
| kube-scheduler          | 無効化 | |
| AllMetrics              | 有効化 | Log Analytics ワークスペース |





## <a name="access-azure-monitor-features"></a>Azure Monitor 機能にアクセスする

Azure portal の **[監視]** メニューからサブスクリプション内のすべての AKS クラスターに対して、または **[Kubernetes サービス]** メニューの **[監視]** セクションから 1 つの AKS クラスターに対して、Azure Monitor 機能にアクセスします。 次のスクリーンショットは、クラスターの **[監視]** メニューを示しています。

:::image type="content" source="media/monitor-aks/monitoring-menu.png" alt-text="[AKS の監視] メニュー" lightbox="media/monitor-aks/monitoring-menu.png":::

| メニュー オプション | 説明 |
|:---|:---|
| 洞察 | 現在のクラスターのコンテナー分析情報を開きます。 **[コンテナー]** を **[監視]** メニューから選択し、すべてのクラスターのコンテナー分析情報を開きます。  |
| アラート | 現在のクラスターのアラートを表示します。 |
| メトリック | 現在のクラスターに設定されているスコープを使用して、メトリックス エクスプローラーを開きます。 |
| 診断設定 | リソース ログを収集するクラスターの診断設定を作成します。 |
| Advisor | Azure Advisor からの現在のクラスターに関する推奨事項。 |
| ログ | スコープを現在のクラスターに設定して Log Analytics を開き、ログ データを分析し、事前構築済みのクエリにアクセスします。 |
| ブック | Kubernetes サービスのブック ギャラリーを開きます。 |




## <a name="monitor-layers-of-aks-with-container-insights"></a>コンテナーの分析情報を使用して AKS のレイヤーを監視する
Kubernetes の実装の違いが大きいので、各顧客に AKS 監視のための固有の要件があります。 実施する方法は、スケール、トポロジ、組織の役割、マルチクラスター テナントなどの要因に基づいている必要があります。 このセクションでは、インフラストラクチャから始まってアプリケーションまでの、ボトムアップの方法の一般的な戦略を示します。 各レイヤーには、個別の監視要件があります。 これらのレイヤーを次の図に示し、次のセクションで詳しく説明します。

:::image type="content" source="media/monitor-aks/layers.png" alt-text="AKS レイヤー"  border="false":::

### <a name="level-1---cluster-level-components"></a>レベル 1 - クラスター レベルのコンポーネント
クラスター レベルには、次のコンポーネントが含まれます。

| コンポーネント | 要件の監視 |
|:---|:---|
| ノード |  各ノードの CPU、メモリ、ディスクの準備状態とパフォーマンスを把握し、ワークロードをデプロイする前に、使用状況の傾向を事前対応的に監視します。 |


Container Insights の既存のビューとレポートを使用して、クラスター レベルのコンポーネントを監視します。 **[クラスター]** ビューでは、CPU とメモリの使用率など、クラスター内のノードのパフォーマンスを簡単に確認できます。 **[ノード]** ビューを使用すると、各ノードの正常性に加えて、各ノードで実行されているポッドの正常性とパフォーマンスを表示できます。 このビューの使用とノードの正常性とパフォーマンスの分析の詳細については、「[Container insights を使用して Kubernetes クラスターのパフォーマンスを監視する](../azure-monitor/containers/container-insights-analyze.md)」を参照してください。

:::image type="content" source="media/monitor-aks/container-insights-cluster-view.png" alt-text="Container insights クラスター ビュー" lightbox="media/monitor-aks/container-insights-cluster-view.png":::

Container Insights 内の **[ノード]** ブックを使用して、GPU 使用率に加えてディスク容量と IO を分析します。 これらのブックの説明については、「[ノードのブック](../azure-monitor/containers/container-insights-reports.md#node-workbooks)」を参照してください。

:::image type="content" source="media/monitor-aks/container-insights-node-workbooks.png" alt-text="Container insights ノード ブック" lightbox="media/monitor-aks/container-insights-node-workbooks.png":::


トラブルシューティングのシナリオでは、メンテナンスまたは即時ログ収集のために AKS ノードに直接アクセスする必要がある場合があります。 セキュリティのため、AKS ノードはインターネットに公開されませんが、SSH への `kubectl debug` を AKS ノードに対して実行できます。 この処理の詳細については、「[メンテナンスまたはトラブルシューティングのために SSH を使用して Azure Kubernetes Service (AKS) クラスター ノードに接続する](ssh.md)」を参照してください。



### <a name="level-2---managed-aks-components"></a>レベル 2 - マネージド AKS コンポーネント
マネージド AKS レベルには、次のコンポーネントが含まれます。

| コンポーネント | 監視 |
|:---|:---|
| API サーバー | API サーバーの状態を監視し、サービスがダウンした場合の要求負荷とボトルネックの増加を特定します。 |
| kubelet | Kubelet の監視は、ポッド管理の問題、ポッドが起動しない、ノードの準備ができていない、ポッドが強制終了された場合のトラブルシューティングに役立ちます。  |

Azure Monitor と Container insights では、API サーバーに対する完全な監視はまだ提供されていません。 メトリックス エクスプローラーを使用して **配信要求** カウンターを表示できますが、API サーバーのパフォーマンスの完全なビューについては、Prometheus のメトリックを参照する必要があります。 これには、要求の待機時間や作業キューの処理時間などの値が含まれます。 API サーバーの重要なメトリックのビューを提供する Grafana ダッシュボードは、[Grafana Labs](https://grafana.com/grafana/dashboards/12006) で使用できます。 既存の Grafana サーバーでこのダッシュボードを使用するか、「[Grafana での Azure サービスの監視](../azure-monitor/visualize/grafana-plugin.md)」を使用して Azure に新しい Grafana サーバーを設定します

:::image type="content" source="media/monitor-aks/grafana-api-server.png" alt-text="Grafana API サーバー" lightbox="media/monitor-aks/grafana-api-server.png":::

**Kubelet** ブックを使用して、各 kubelet の正常性とパフォーマンスを表示します。 このブックの詳細については、「[リソース監視のブック](../azure-monitor/containers/container-insights-reports.md#resource-monitoring-workbooks)」を参照してください。 トラブルシューティングのシナリオでは、「[Azure Kubernetes Service (AKS) クラスター ノードから kubelet ログを取得する](kubelet-logs.md)」で説明されているプロセスを使用して、kubelet ログにアクセスできます。

:::image type="content" source="media/monitor-aks/container-insights-kubelet-workbook.png" alt-text="Container insights kubelet ブック" lightbox="media/monitor-aks/container-insights-kubelet-workbook.png":::

### <a name="resource-logs"></a>リソース ログ
[リソース ログを含むログ クエリ](../azure-monitor/containers/container-insights-log-query.md#resource-logs)を使用して、AKS コンポーネントによって生成されたコントロール プレーン ログを分析します。 

### <a name="level-3---kubernetes-objects-and-workloads"></a>レベル 3 - Kubernetes オブジェクトとワークロード
Kubernetes オブジェクトとワークロード レベルには、次のコンポーネントが含まれます。

| コンポーネント | 要件の監視 |
|:---|:---|
| デプロイ | デプロイの実際の状態と望ましい状態、およびデプロイで実行されているポッドの状態とリソース使用率を監視します。  | 
| ポッド | AKS クラスターで実行されているポッドの状態と、CPU とメモリを含むリソース使用率を監視します。 |
| Containers | AKS クラスターで実行されているコンテナーの CPU とメモリを含むリソース使用率を監視します。 |


コンテナーとポッドを監視するには、Container Insights の既存のビューとレポートを使用します。 **[ノード]** ビューと **[コントローラー]** ビューを使用して、それらで実行中のポッドの正常性とパフォーマンスを表示し、それらのコンテナーの正常性とパフォーマンスにドリルダウンします。 **[コンテナー]** ビューからコンテナーの正常性とパフォーマンスを直接表示します。 このビューの使用とコンテナーの正常性とパフォーマンスの分析の詳細については、「[Container insights を使用して Kubernetes クラスターのパフォーマンスを監視する](../azure-monitor/containers/container-insights-analyze.md)」を参照してください。

:::image type="content" source="media/monitor-aks/container-insights-containers-view.png" alt-text="Container insights コンテナー ビュー" lightbox="media/monitor-aks/container-insights-containers-view.png":::

Container insights の **デプロイ** ブックを使用して、デプロイ用に収集されたメトリックを表示します。 詳細については、「[Container insights によるデプロイと HPA メトリック](../azure-monitor/containers/container-insights-deployment-hpa-metrics.md)」を参照してください。

> [!NOTE]
> Container insights のデプロイ ビューは、現在パブリック プレビューの段階にあります。

:::image type="content" source="media/monitor-aks/container-insights-deployments-workbook.png" alt-text="Container Insights のデプロイ ブック" lightbox="media/monitor-aks/container-insights-deployments-workbook.png":::

#### <a name="live-data"></a>ライブ データ
トラブルシューティングのシナリオでは、Container insights はライブ AKS コンテナー ログ (stdout または stderror)、イベント、ポッド メトリックにアクセスできます。 この機能の使用の詳細については、「[Kubernetes ログ、イベント、およびポッド メトリックをリアルタイムで表示する方法](../azure-monitor/containers/container-insights-livedata-overview.md)」を参照してください。

:::image type="content" source="media/monitor-aks/container-insights-live-data.png" alt-text="Container Insights ライブ データ" lightbox="media/monitor-aks/container-insights-live-data.png":::

### <a name="level-4--applications"></a>レベル 4 - アプリケーション
アプリケーション レベルには、AKS クラスターで実行されているアプリケーション ワークロードが含まれます。

| コンポーネント | 要件の監視 |
|:---|:---|
| アプリケーション | マイクロサービス アプリケーションのデプロイを監視して、アプリケーションの障害と待機時間の問題を特定します。 要求レート、応答時間、例外などの情報が含まれます。 |

Application Insights は、AKS や他の環境で実行されているアプリケーションの完全な監視を提供します。 Java アプリケーションがある場合は、「[Kubernetes に対するゼロ インストルメンテーション アプリケーション監視 - Azure Monitor Application Insights](../azure-monitor/app/kubernetes-codeless.md)」に従って、コードをインストルメント化せずに監視を提供できます。 しかし、完全な監視を行う場合は、アプリケーションに応じてコードベースの監視を構成する必要があります。

- [ASP.NET アプリケーション](../azure-monitor/app/asp-net.md)
- [ASP.NET Core アプリケーション](../azure-monitor/app/asp-net-core.md)
- [.NET コンソール アプリケーション](../azure-monitor/app/console.md)
- [Java](../azure-monitor/app/java-in-process-agent.md)
- [Node.js](../azure-monitor/app/nodejs.md)
- [Python](../azure-monitor/app/opencensus-python.md)
- [その他のプラットフォーム](../azure-monitor/app/platforms.md)

「[Application Insights とは何か?](../azure-monitor/app/app-insights-overview.md)」を参照してください。 

### <a name="level-5--external-components"></a>レベル 5 - 外部コンポーネント
AKS の外部コンポーネントには、次のものが含まれます。

| コンポーネント | 要件の監視 |
|:---|:---|
| サービス メッシュ、イングレス、エグレス | コンポーネントに基づくメトリック。 |
| データベースとワーク キュー | コンポーネントに基づくメトリック。 |

Prometheus や Grafana などの独自のツールを使用して、サービス メッシュ、イングレス、エグレスなどの外部コンポーネントを監視します。 Azure Monitor の他の機能を使用して、データベースやその他の Azure リソースを監視します。

## <a name="analyze-metric-data-with-metrics-explorer"></a>メトリックス エクスプローラーを使用してメトリック データを分析する
コンテナー用に収集されたメトリック データのカスタム分析を実行する場合は、メトリックス エクスプローラーを使用します。 メトリックス エクスプローラーを使用すると、グラフをプロットしたり、傾向を視覚的に関連付けたり、メトリックの値の急上昇と急降下を調べたりすることができます。 メトリック アラートを作成して、メトリック値がしきい値を超えるときに予防的に通知し、組織のさまざまなメンバーが使用できるようにグラフをダッシュボードにピン留めします。

この機能の使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../azure-monitor/essentials/metrics-getting-started.md)」を参照してください。 AKS のために収集されるプラットフォーム メトリックの一覧については、[AKS データ参照メトリックの監視](monitor-aks-reference.md#metrics)に関する記事を参照してください。 クラスターに対して Container insights が有効になっている場合は、[追加のメトリック値](../azure-monitor/containers/container-insights-update-metrics.md)を使用できます。

:::image type="content" source="media/monitor-aks/metrics-explorer.png" alt-text="メトリックス エクスプローラー" lightbox="media/monitor-aks/metrics-explorer.png":::



## <a name="analyze-log-data-with-log-analytics"></a>Log Analytics を使用してログ データを分析する
リソース ログを分析したり、Container insights でビューを作成するために使用されるデータをさらに掘り下げたりする場合は、Log Analytics を使用します。 Log Analytics を使用すると、ログ データのカスタム分析を実行できます。 

ログ クエリを使用して Container insights が収集したデータを分析する方法の詳細については、「[Container insights のログのクエリを実行する方法](../azure-monitor/containers/container-insights-log-query.md)」を参照してください。 これらのクエリの使用方法の詳細については、「[Azure Monitor Log Analytics でのクエリの使用](../azure-monitor/logs/queries.md)」を参照し、クエリを実行してそれらの結果を処理するための Log Analytics の使用に関する完全なチュートリアルについては、「[Log Analytics のチュートリアル](../azure-monitor/logs/log-analytics-tutorial.md)」を参照してください。

メトリックス エクスプローラーで分析できる AKS 用に収集されたテーブルの一覧については、[AKS データ参照ログの監視](monitor-aks-reference.md#azure-monitor-logs-tables)に関する記事を参照してください。

:::image type="content" source="media/monitor-aks/log-analytics-queries.png" alt-text="Kubernetes の Log Analytics クエリ" lightbox="media/monitor-aks/log-analytics-queries.png":::

Container insights データに加えて、ログ クエリを使用して、AKS からのリソース ログを分析することもできます。 使用可能なログ カテゴリの一覧については、[AKS データ参照リソース ログ](monitor-aks-reference.md#resource-logs)に関する記事を参照してください。 「[監視の構成](#configure-monitoring)」で説明されているように、各カテゴリのデータを収集する前に、その収集のための診断設定を作成する必要があります。 




## <a name="alerts"></a>アラート
[Azure Monitor のアラート](../azure-monitor/alerts/alerts-overview.md)では、監視データの興味深いデータやパターンを事前に通知します。 これにより、ユーザーが気付く前に、管理者が問題を識別して対処できます。 AKS クラスター用に事前に構成されたアラート ルールはありませんが、Container insights によって収集されたデータに基づいて独自のルールを作成することができます。

> [!IMPORTANT]
> ほとんどのアラート ルールには、ルールの種類、含まれるディメンションの数、実行頻度に応じてかかるコストがあります。 アラート ルールを作成する前に、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」で **アラート ルール** に関して参照してください。


### <a name="choosing-the-alert-type"></a>アラートの種類の選択
Azure Monitor における最も一般的な種類のアラート ルールは、[メトリック アラート](../azure-monitor/alerts/alerts-metric.md)と[ログ クエリ アラート](../azure-monitor/alerts/alerts-log-query.md)です。 特定のシナリオのために作成するアラート ルールの種類は、アラートの対象となるデータの場所に応じて異なります。 場合によっては、特定のアラート シナリオのデータをメトリックとログの両方で使用できる場合があります。また、使用するルールの種類を決定する必要があります。 

通常、最適な方法は、ログ アラートの代わりにメトリック アラートを使用することです。これは、応答性が高くステートフルであるためです。 メトリックス エクスプローラーで分析できる任意の値に対して、メトリック アラートを作成できます。 アラート ルールのロジックでログにデータが必要な場合、またはより複雑なロジックが必要な場合は、ログ クエリ アラート ルールを使用できます。

たとえば、アプリケーション ワークロードが過剰な CPU を消費している場合にアラートが生成されるようにするには、CPU メトリックを使用してメトリック アラートを作成します。 コントロール プレーン ログで特定のメッセージが検出されたときにアラートが必要な場合は、ログ アラートが必要になります。
### <a name="metric-alert-rules"></a>メトリック アラート ルール
メトリック アラート ルールでは、メトリックス エクスプローラーと同じメトリック値が使用されます。 実際には、現在分析しているデータを使用して、メトリックス エクスプローラーから直接アラート ルールを作成できます。 メトリック アラート ルールには、[AKS データ参照メトリック](monitor-aks-reference.md#metrics)の値のいずれかを使用できます。

Container insights には、AKS クラスターに対して推奨されるメトリック アラート ルールのセットを作成するパブリック プレビュー段階の機能が含まれています。 この機能では、メトリックス エクスプローラーで使用できるアラート ルールによって使用される新しいメトリック値 (これもプレビュー段階) が作成されます。 この機能の詳細と、AKS のメトリック アラートの作成については、「[Container insights からの推奨メトリック アラート (プレビュー)](../azure-monitor/containers/container-insights-metric-alerts.md)」を参照してください。


### <a name="log-alerts-rules"></a>ログ アラート ルール
ログ アラート ルールを使用して、ログ クエリの結果からアラートを生成します。 これは、Container insights または AKS リソース ログによって収集されたデータである場合があります。 AKS のログ アラート ルールと、アラート ルール用に設計された一連のサンプル クエリの詳細については、「[Container insights からログ アラートを作成する方法](../azure-monitor/containers/container-insights-log-alerts.md)」を参照してください。 また、アラート ルール用に変更できるログ クエリの詳細については、「[Container insights のログのクエリを実行する方法](../azure-monitor/containers/container-insights-log-query.md)」を参照してください。

### <a name="virtual-machine-alerts"></a>仮想マシンのアラート
AKS は、AKS ワークロードを実行するために正常である必要がある仮想マシン スケール セットに依存します。 仮想マシンの CPU、メモリ、ストレージなどの重要なメトリックに関し、「[Azure Monitor を使用して仮想マシンを監視する: アラート](../azure-monitor/vm/monitor-virtual-machine-alerts.md)」のガイダンスを使用してアラートを生成することができます。

### <a name="prometheus-alerts"></a>Prometheus アラート
Azure Monitor がアラート条件に必要なデータを持っていない場合、またはアラートの応答性が不十分な場合は、Prometheus でアラートを構成する必要があります。 1 つの例として、API サーバーのアラートがあります。 Azure Monitor は、使用可能であるか、ボトルネックが発生しているかなど、API サーバーの重要な情報を収集しません。 Kube-apiserver リソース ログ カテゴリのデータを使用してログ クエリ アラートを作成できますが、アラートを受け取るまでに最大で数分かかることがあり、これは要件を十分に満たさない場合があります。 


## <a name="next-steps"></a>次のステップ

- AKS によって作成されるメトリック、ログ、その他の重要な値のリファレンスについては、「[AKS データの監視に関するリファレンス](monitor-aks-reference.md)」を参照してください。
