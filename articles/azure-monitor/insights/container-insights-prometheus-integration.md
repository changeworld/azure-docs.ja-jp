---
title: Azure Monitor for containers の Prometheus 統合を構成する | Microsoft Docs
description: この記事では、Azure Kubernetes Service クラスターで Prometheus からメトリックをスクレーピングするために Azure Monitor for containers エージェントを構成する方法について説明します。
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: f1da2142f287bde83be7cede282bd854ce822d23
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75403515"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>Azure Monitor for containers で Prometheus メトリックのスクレーピングを構成する

[Prometheus](https://prometheus.io/) は、人気のあるオープン ソースのメトリック監視ソリューションであり、[Cloud Native Compute Foundation](https://www.cncf.io/) の一部です。 Azure Monitor for containers では、Prometheus メトリックを収集するためのシームレスなオンボード エクスペリエンスをご利用いただけます。 通常、Prometheus を使用するには、ストアで Prometheus サーバーを設定および管理する必要があります。 Azure Monitor と統合することにより、Prometheus サーバーは必要ありません。 エクスポーターまたはポッド (アプリケーション) を使用して Prometheus メトリック エンドポイントを公開するだけで、Azure Monitor for containers 向けのコンテナー化されたエージェントがメトリックを自動的にスクレーピングできます。 

![Prometheus のコンテナー監視アーキテクチャ](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>Prometheus メトリックのスクレーピングでサポートされているエージェントの最小バージョンは ciprod07092019 以降であり、`KubeMonAgentEvents` テーブルへの構成エラーおよびエージェント エラーの書き込みでサポートされているエージェント バージョンは ciprod10112019 です。 エージェント バージョンおよび各リリースに含まれている機能の追加情報については、[エージェントのリリース ノート](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)を参照してください。 お使いのエージェントのバージョンを確認するには、 **[ノード]** タブでノードを選択し、プロパティ ウィンドウで **[エージェント イメージ タグ]** プロパティの値に注目します。

### <a name="prometheus-scraping-settings"></a>Prometheus のスクレーピングの設定

Prometheus からのメトリックのアクティブなスクレーピングは、次の 2 つの観点から実行されます。

* クラスター全体 - リストされたサービスのエンドポイントからの HTTP URL と検出ターゲット。 たとえば、kube-dns や kube-state-metrics などの k8s サービスや、アプリケーションに固有のポッド注釈などです。 このコンテキストで収集されるメトリックは、ConfigMap のセクション *[Prometheus data_collection_settings.cluster]* で定義されています。
* ノード全体 - リストされたサービスのエンドポイントからの HTTP URL と検出ターゲット。 このコンテキストで収集されるメトリックは、ConfigMap のセクション *[Prometheus_data_collection_settings.node]* で定義されています。

| エンドポイント | スコープ | 例 |
|----------|-------|---------|
| ポッド注釈 | クラスター全体 | 注釈: <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Kubernetes サービス | クラスター全体 | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| URL/エンドポイント | ノードごと、またはクラスター全体 (あるいは両方) | `http://myurl:9101/metrics` |

URL が指定されると、コンテナー用 Azure Monitor はエンドポイントのみをスクレーピングします。 Kubernetes サービスが指定されると、クラスター DNS サーバーを使用してサービス名が解決されて IP アドレスが取得された後で、解決されたサービスがスクレーピングされます。

|スコープ | Key | データ型 | 値 | [説明] |
|------|-----|-----------|-------|-------------|
| クラスター全体 | | | | メトリックのエンドポイントを収集するには、次の 3 つの方法のいずれかを指定します。 |
| | `urls` | String | コンマ区切りの配列 | HTTP エンドポイント (指定された IP アドレスまたは有効な URL パス)。 (例: `urls=[$NODE_IP/metrics]`)。 ($NODE_IP は、コンテナーの Azure Monitor の特定のパラメーターであり、ノードの IP アドレスの代わりに使用できます。 すべて大文字である必要があります。) |
| | `kubernetes_services` | String | コンマ区切りの配列 | kube-state-metrics からメトリックを収集する Kubernetes サービスの配列。 例: `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`。|
| | `monitor_kubernetes_pods` | Boolean | true または false | クラスター全体の設定で `true` に設定すると、コンテナーの Azure Monitor エージェントでは、次の Prometheus 注釈についてクラスター全体の Kubernetes ポッドが収集されます。<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Boolean | true または false | ポッドの収集を有効にします。 `monitor_kubernetes_pods` が `true` に設定されていること。 |
| | `prometheus.io/scheme` | String | http または https | 既定値は HTTP 経由での収集です。 必要であれば、`https`に設定します。 | 
| | `prometheus.io/path` | String | コンマ区切りの配列 | メトリックのフェッチ元の HTTP リソース パス。 メトリック パスが `/metrics` ではない場合は、この注釈でそれを定義します。 |
| | `prometheus.io/port` | String | 9102 | 収集するポートを指定します。 ポートが設定されていない場合、既定値は 9102 になります。 |
| | `monitor_kubernetes_pods_namespaces` | String | コンマ区切りの配列 | Kubernetes ポッドからメトリックをスクレーピングするための名前空間の許可リスト。<br> たとえば、`monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` のように指定します。 |
| ノード全体 | `urls` | String | コンマ区切りの配列 | HTTP エンドポイント (指定された IP アドレスまたは有効な URL パス)。 (例: `urls=[$NODE_IP/metrics]`)。 ($NODE_IP は、コンテナーの Azure Monitor の特定のパラメーターであり、ノードの IP アドレスの代わりに使用できます。 すべて大文字である必要があります。) |
| ノード全体またはクラスター全体 | `interval` | String | 60s | 収集間隔の既定値は 1 分 (60 秒) です。 *[prometheus_data_collection_settings.node]* または *[prometheus_data_collection_settings.cluster]* に対する収集を、s、m、h などの時間単位に変更できます。 |
| ノード全体またはクラスター全体 | `fieldpass`<br> `fielddrop`| String | コンマ区切りの配列 | リスティングの許可 (`fieldpass`) および禁止 (`fielddrop`) を設定することにより、エンドポイントから特定のメトリックを収集する、または収集しないように指定できます。 許可リストを最初に設定する必要があります。 |

ConfigMaps はグローバル リストであり、エージェントに適用できる ConfigMap は 1 つだけです。 別の ConfigMaps でコレクションを上書きすることはできません。

## <a name="configure-and-deploy-configmaps"></a>ConfigMap を構成してデプロイする

ConfigMap 構成ファイルを構成してクラスターにデプロイするには、次の手順のようにします。

1. テンプレート ConfigMap の yaml ファイルを[ダウンロード](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml)し、container-azm-ms-agentconfig.yaml として保存します。

2. Prometheus メトリックをスクレーピングするためにご自分のカスタマイズで ConfigMap yaml ファイルを編集します。

    - Kubernetes サービス クラスター全体で収集するには、次の例を使用して ConfigMap ファイルを構成します。

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - クラスター全体で特定の URL からの Prometheus メトリックの収集を構成するには、次の例を使用して ConfigMap ファイルを構成します。

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - クラスター内のすべての個別ノードに対するエージェントの DaemonSet からの Prometheus メトリックの収集を構成するには、ConfigMap で次のように構成します。
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE_IP は、コンテナーの Azure Monitor の特定のパラメーターであり、ノードの IP アドレスの代わりに使用できます。 すべて大文字にする必要があります。 

    - ポッドの注釈を指定して Prometheus メトリックの収集を構成するには、次の手順のようにします。

       1. ConfigMap で、次のように指定します。

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. ポッドの注釈に対して次の構成を指定します。

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          注釈があるポッドの特定の名前空間に監視を制限する (たとえば、運用ワークロード専用のポッドのみを含める) 場合は、ConfigMap で `monitor_kubernetes_pod` を `true` に設定し、スクレーピングする名前空間を指定して名前空間フィルター `monitor_kubernetes_pods_namespaces` を追加します。 たとえば、`monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` のように指定します。

3. 次の kubectl コマンドを実行して、ConfigMap を作成します: `kubectl apply -f <configmap_yaml_file.yaml>`。
    
    例: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    構成の変更が有効になるまでに数分かかる場合があり、クラスター内のすべての omsagent ポッドが再起動されます。 すべての omsagent ポッドが同時に再起動されるのではなく、ローリング再起動で行われます。 再起動が完了すると、次のような結果を含むメッセージが表示されます: `configmap "container-azm-ms-agentconfig" created`。

## <a name="applying-updated-configmap"></a>更新された ConfigMap を適用する

クラスターに ConfigMap を既にデプロイしてあり、それを新しい構成で更新したい場合は、前に使用した ConfigMap ファイルを編集した後、同じコマンド `kubectl apply -f <configmap_yaml_file.yaml` を使って適用できます。

構成の変更が有効になるまでに数分かかる場合があり、クラスター内のすべての omsagent ポッドが再起動されます。 すべての omsagent ポッドが同時に再起動されるのではなく、ローリング再起動で行われます。 再起動が完了すると、次のような結果を含むメッセージが表示されます: `configmap "container-azm-ms-agentconfig" updated`。

## <a name="verify-configuration"></a>構成の確認 

構成が正常に適用されたことを検証するには、次のコマンドを使って、エージェント ポッドからのログを確認します: `kubectl logs omsagent-fdf58 -n=kube-system`。 omsagent ポッドからの構成エラーがある場合は、出力で次のようなエラーが示されます。

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

構成変更の適用に関連するエラーも確認できます。 次のオプションを使用して、構成変更および Prometheus メトリックのスクレーピングに関する追加のトラブルシューティングを実行できます。

- エージェント ポッド ログから。同じ `kubectl logs` コマンドを使用。 

- ライブ ログから。 ライブ ログでは、次のようなエラーが示されます。

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- Log Analytics ワークスペースの **KubeMonAgentEvents** テーブルから。 スクレーピング エラーの場合は "*警告*" の重大度、構成エラーの場合は "*エラー*" の重大度で 1 時間ごとにデータが送信されます。 エラーがない場合、テーブルのエントリには "*情報*" の重大度のデータが含まれ、エラーは報告されません。 **[タグ]** プロパティには、エラーが発生したポッドとコンテナー ID に関する詳細情報のほか、過去 1 時間の最初の発生、最後の発生、および発生回数も含まれます。

エラーがあると omsagent でファイルを解析できず、再起動されて、既定の構成が使用されます。 ConfigMap でエラーを修正した後、yaml ファイルを保存し、次のコマンドを実行して更新された ConfigMap を適用します: `kubectl apply -f <configmap_yaml_file.yaml`。

## <a name="query-prometheus-metrics-data"></a>Prometheus メトリック データのクエリを実行する

Azure Monitor によってスクレーピングされた Prometheus メトリックと、エージェントによって報告された構成エラーまたはスクレーピング エラーを表示するには、「[Prometheus メトリック データのクエリを実行する](container-insights-log-search.md#query-prometheus-metrics-data)」と「[構成エラーまたはスクレーピング エラーのクエリを実行する](container-insights-log-search.md#query-config-or-scraping-errors)」を確認してください。

## <a name="view-prometheus-metrics-in-grafana"></a>Grafana で Prometheus メトリックを表示する

Azure Monitor for Containers では、Grafana ダッシュボードの Log Analytics ワークスペースに格納されているメトリックの表示をサポートしています。 Grafana の[ダッシュボード リポジトリ](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker)からダウンロードできるテンプレートが用意されています。これを使って作業を開始し、監視対象クラスターから追加データのクエリを実行して、カスタム Grafana ダッシュボードで視覚化する方法を学習できます。 

## <a name="review-prometheus-data-usage"></a>Prometheus のデータ使用状況を確認する

各メトリック サイズのインジェスト ボリューム (1 日あたりの GB) を取得して、高いかどうかを把握できるよう、次のクエリが用意されています。

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
出力では、次のような結果が示されます。

![データ インジェスト ボリュームのログ クエリの結果](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

1 か月間の各メトリック サイズ (GB 単位) を推定し、ワークスペースで受け取る取り込まれたデータの量が多いかどうかを把握するため、次のクエリが用意されています。

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

出力では、次のような結果が示されます。

![データ インジェスト ボリュームのログ クエリの結果](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

データの使用状況を監視する方法およびコストを分析する方法について詳しくは、「[Azure Monitor ログで使用量とコストを管理する](../platform/manage-cost-storage.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

コンテナー ワークロードからの stdout、stderr、および環境変数のエージェント収集設定を構成する方法の詳細については、[こちら](container-insights-agent-config.md)をご覧ください。 
