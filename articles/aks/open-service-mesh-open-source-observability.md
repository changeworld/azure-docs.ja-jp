---
title: OSM OSS の監視
description: Open Service Mesh のオープンソースの監視機能を構成する方法
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: ce03fc4007ad55485150feb715242d4cc216433e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440095"
---
# <a name="manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>Prometheus、Grafana、Jaeger を手動でデプロイして、監視のために Open Service Mesh (OSM) メトリックを表示する

> [!WARNING]
> Prometheus、Grafana、Jaeger のインストールは、これらのツールを使用して OSM メトリック データを表示する方法を示すための一般的なガイダンスとして提供されています。 インストール ガイドは、運用環境でのセットアップには使用されません。 インストールをどのようにしてニーズに合わせるのかが最適かについては、各ツールのドキュメントを参照してください。 最も注目すべき点は、永続的なストレージがないことです。つまり、Prometheus、Grafana、Jaeger のポッドが終了すると、すべてのデータが失われます。

Open Service Mesh (OSM) では、メッシュ内のすべてのトラフィックに関連する詳細なメトリックが生成されます。 これらのメトリックによって、ユーザーがアプリケーションのトラブルシューティング、保守、分析を行うのに役立つメッシュ内のアプリケーションの動作に関する洞察が提供されます。

現時点では、OSM ではサイドカー プロキシ (エンボイ) からメトリックを直接収集します。 OSM によって、メッシュ内のすべてのサービスの受信および送信トラフィックについての豊富なメトリックが提供されます。 ユーザーはこれらのメトリックを使用して、トラフィックの全体的な量、トラフィック内のエラー、要求の応答時間に関する情報を取得できます。

OSM では Prometheus を使用して、メッシュ内で実行されているすべてのアプリケーションについて、一貫性のあるトラフィック メトリックと統計情報を収集して保存します。 Prometheus はオープンソースの監視およびアラート ツールキットであり、Kubernetes およびサービス メッシュ環境で一般的に使用されます (ただし、これらには限定されません)。

メッシュの一部である各アプリケーションは、Prometheus 形式でメトリック (プロキシ メトリック) を公開するエンボイ サイドカーを含むポッド内で実行されます。 さらに、メッシュの一部であるすべてのポッドに Prometheus の注釈があるため、Prometheus サーバーでアプリケーションを動的にスクレイピングすることができます。 このメカニズムにより、新しい名前空間/ポッド/サービスがメッシュに追加されるたびに、メトリックのスクレイピングが自動的に有効になります。

オープンソースの視覚化および分析ソフトウェアである Grafana を使用して OSM メトリックを表示できます。 メトリックのクエリ、視覚化、操作、またメトリックに基づいたアラートの作成を行うために使用できます。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
>
> - Prometheus インスタンスを作成してデプロイする
> - Prometheus スクレイピングを許可するように OSM を構成する
> - Prometheus `Configmap` を更新する
> - Grafana インスタンスを作成してデプロイする
> - Prometheus データ ソースで Grafana を構成する
> - Grafana の OSM ダッシュボードをインポートする
> - Jaeger インスタンスを作成してデプロイする
> - OSM の Jaeger トレースを構成する

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>開始する前に

次のリソースがインストールされている必要があります。

- Azure CLI バージョン 2.20.0 以降
- `aks-preview` 拡張機能バージョン 0.5.5 以降
- OSM バージョン v0.8.0 以降
- JSON プロセッサ "jq" バージョン 1.6 以上

## <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>OSM の Prometheus インスタンスをデプロイして構成する

Helm を使用して Prometheus インスタンスをデプロイします。 次のコマンドを実行して、Helm を介して Prometheus をインストールします。

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

インストールが正常に完了した場合は、下のような出力が表示されます。 Prometheus サーバー ポートとクラスター DNS 名をメモしておきます。 この情報は、後で Grafana のデータ ソースとして Prometheus を構成するために使用されます。

```Output
NAME: stable
LAST DEPLOYED: Fri Mar 26 13:34:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Prometheus server can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-server.default.svc.cluster.local


Get the Prometheus server URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9090


The Prometheus alertmanager can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-alertmanager.default.svc.cluster.local


Get the Alertmanager URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=alertmanager" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9093
#################################################################################
######   WARNING: Pod Security Policy has been moved to a global property.  #####
######            use .Values.podSecurityPolicy.enabled with pod-based      #####
######            annotations                                               #####
######            (e.g. .Values.nodeExporter.podSecurityPolicy.annotations) #####
#################################################################################


The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
stable-prometheus-pushgateway.default.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9091

For more information on running Prometheus, visit:
https://prometheus.io/
```

### <a name="configure-osm-to-allow-prometheus-scraping"></a>Prometheus スクレイピングを許可するように OSM を構成する

OSM コンポーネントが Prometheus スクレイピング用に構成されていることを確認するには、osm-config 構成ファイルにある **prometheus_scraping** 構成を確認します。 次のコマンドを使用して構成を表示します。

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data.prometheus_scraping'
```

OSM が Prometheus スクレイピング用に構成されている場合、前のコマンドの出力では `true` が返されます。 戻り値が `false` の場合は、構成を `true` に更新する必要があります。 次のコマンドを実行して、OSM Prometheus スクレイピングを **オン** にします。

```azurecli-interactive
kubectl patch configmap -n kube-system osm-config --type merge --patch '{"data":{"prometheus_scraping":"true"}}'
```

次の出力が表示されます。

```Output
configmap/osm-config patched
```

### <a name="update-the-prometheus-configmap"></a>Prometheus の Configmap を更新する

Prometheus の既定のインストールには、2 つの Kubernetes `configmaps` が含まれています。 次のコマンドを使用して、Prometheus `configmaps` の一覧を表示できます。

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

**stable-prometheus-server** `configmap` にある prometheus.yml 構成を次の OSM 構成に置き換える必要があります。 このタスクを実行するには、いくつかのファイル編集手法があります。 簡単かつ安全な方法は、`configmap` をエクスポートし、そのコピーをバックアップ用に作成した後、Visual Studio Code などのエディターを使用してそれを編集することです。

> [!NOTE]
> Visual Studio Code インストールされていない場合は、[こちら](https://code.visualstudio.com/Download)からダウンロードしてインストールすることができます。

まず、**stable-prometheus-server** configmap をエクスポートしてから、バックアップ用のコピーを作成してみましょう。

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

次に、Visual Studio Code を使用してファイルを開いて編集します。

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

Visual Studio Code エディターで `configmap` を開いたら、prometheus.yml ファイルを下の OSM 構成に置き換えて、ファイルを保存します。

> [!WARNING]
> yaml ファイルのインデント構造を保持することが非常に重要です。 yaml ファイル構造を変更すると、configmap を再適用できなくなる可能性があります。

```OSM Prometheus Configmap Configuration
prometheus.yml: |
    global:
      scrape_interval: 10s
      scrape_timeout: 10s
      evaluation_interval: 1m

    scrape_configs:
      - job_name: 'kubernetes-apiservers'
        kubernetes_sd_configs:
        - role: endpoints
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
          # TODO need to remove this when the CA and SAN match
          insecure_skip_verify: true
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(apiserver_watch_events_total|apiserver_admission_webhook_rejection_count)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
          action: keep
          regex: default;kubernetes;https

      - job_name: 'kubernetes-nodes'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics

      - job_name: 'kubernetes-pods'
        kubernetes_sd_configs:
        - role: pod
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(envoy_server_live|envoy_cluster_upstream_rq_xx|envoy_cluster_upstream_cx_active|envoy_cluster_upstream_cx_tx_bytes_total|envoy_cluster_upstream_cx_rx_bytes_total|envoy_cluster_upstream_cx_destroy_remote_with_active_rq|envoy_cluster_upstream_cx_connect_timeout|envoy_cluster_upstream_cx_destroy_local_with_active_rq|envoy_cluster_upstream_rq_pending_failure_eject|envoy_cluster_upstream_rq_pending_overflow|envoy_cluster_upstream_rq_timeout|envoy_cluster_upstream_rq_rx_reset|^osm.*)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        - source_labels: [__meta_kubernetes_namespace]
          action: replace
          target_label: source_namespace
        - source_labels: [__meta_kubernetes_pod_name]
          action: replace
          target_label: source_pod_name
        - regex: '(__meta_kubernetes_pod_label_app)'
          action: labelmap
          replacement: source_service
        - regex: '(__meta_kubernetes_pod_label_osm_envoy_uid|__meta_kubernetes_pod_label_pod_template_hash|__meta_kubernetes_pod_label_version)'
          action: drop
        # for non-ReplicaSets (DaemonSet, StatefulSet)
        # __meta_kubernetes_pod_controller_kind=DaemonSet
        # __meta_kubernetes_pod_controller_name=foo
        # =>
        # workload_kind=DaemonSet
        # workload_name=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          target_label: source_workload_kind
        - source_labels: [__meta_kubernetes_pod_controller_name]
          action: replace
          target_label: source_workload_name
        # for ReplicaSets
        # __meta_kubernetes_pod_controller_kind=ReplicaSet
        # __meta_kubernetes_pod_controller_name=foo-bar-123
        # =>
        # workload_kind=Deployment
        # workload_name=foo-bar
        # deplyment=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          regex: ^ReplicaSet$
          target_label: source_workload_kind
          replacement: Deployment
        - source_labels:
          - __meta_kubernetes_pod_controller_kind
          - __meta_kubernetes_pod_controller_name
          action: replace
          regex: ^ReplicaSet;(.*)-[^-]+$
          target_label: source_workload_name

      - job_name: 'smi-metrics'
        kubernetes_sd_configs:
        - role: pod
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: 'envoy_.*osm_request_(total|duration_ms_(bucket|count|sum))'
          action: keep
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_(\d{3})_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: response_code
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_total
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_total
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_total)
          target_label: __name__

        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_duration_ms_(bucket|sum|count))
          target_label: __name__

      - job_name: 'kubernetes-cadvisor'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(container_cpu_usage_seconds_total|container_memory_rss)'
          action: keep
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
```

次のコマンドを使用して、更新された `configmap` yaml ファイルを適用します。

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> 必要な kubernetes 注釈が見つからないことに関するメッセージが表示される場合があります。 これは、ここでは無視できます。

### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>Prometheus が OSM メッシュおよび API エンドポイントをスクレイピングするように構成されていることを確認します

Prometheus が OSM メッシュと API エンドポイントをスクレイピングするように正しく構成されていることを確認するために、Prometheus ポッドにポート転送し、ターゲット構成を表示します。 次のコマンドを実行します。

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

ブラウザーで `http://localhost:9090/targets` を開きます

下にスクロールすると、下の図のように定義されている他の OSM メトリックに加えて、すべての SMI メトリック エンドポイントの状態が **[アップ]** と表示されます。

![OSM Prometheus ターゲット メトリックの UI イメージ](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

## <a name="deploy-and-configure-a-grafana-instance-for-osm&quot;></a>OSM の Grafana インスタンスをデプロイして構成する

Helm を使用して Grafana インスタンスをデプロイします。 次のコマンドを実行して、Helm を介して Grafana をインストールします。

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

次に、Grafana サイトにログインするための既定の Grafana パスワードを取得します。

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath=&quot;{.data.admin-password}&quot; | base64 --decode ; echo
```

Grafana のパスワードを書き留めます。

次に、Grafana ポッドを取得して、Grafana ダッシュボードをポート転送してログインします。

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l &quot;app.kubernetes.io/name=grafana&quot; -o jsonpath=&quot;{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

ブラウザーで `http://localhost:3000` を開きます

下の図に示すログイン画面で、ユーザー名として「**admin**」と入力し、前にキャプチャした Grafana パスワードを使用します。

![OSM Grafana ログイン ページの UI イメージ](./media/aks-osm-addon/osm-grafana-ui-login.png)

### <a name="configure-the-grafana-prometheus-data-source"></a>Grafana Prometheus データ ソースを構成する

Grafana に正常にログインしたら、次の手順では、Grafana のデータ ソースとして Prometheus を追加します。 これを行うには、左側のメニューの [構成] アイコンに移動し、下に示すように [データ ソース] を選択します。

![OSM Grafana [データ ソース] ページの UI イメージ](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

**[データ ソースの追加]** ボタンをクリックし、[時系列データベース] で [Prometheus] を選択します。

![OSM Grafana データ ソースの選択ページの UI イメージ](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

**[下の Prometheus データ ソースの構成]** ページで、[HTTP URL] 設定の Prometheus サービスについて Kubernetes クラスター FQDN を入力します。 既定の FQDN は `stable-prometheus-server.default.svc.cluster.local` である必要があります。 Prometheus サービス エンドポイントを入力したら、ページの下部までスクロールして、 **[保存 & テスト]** を選択します。 データ ソースが機能していることを示す緑色のチェック ボックスが表示されます。

### <a name="importing-osm-dashboards"></a>OSM ダッシュボードのインポート

OSM ダッシュボードは、次のどちらを使用しても利用できます。

- [リポジトリ](https://github.com/grafana/grafana)。Web 管理ポータルを使用して json blob としてインポートできます
- または [Grafana.com からオンラインで](https://grafana.com/grafana/dashboards/14145)

ダッシュボードをインポートするには、左側のメニューの `+` 記号を探し、[`import`] を選択します。
ダッシュボードは、`Grafana.com` の ID で直接インポートできます。 たとえば、`OSM Mesh Details` ダッシュボードで ID `14145` が使用されている場合、フォームで ID を直接使用して、[`import`] を選択します。

![OSM Grafana ダッシュボードの [インポート] ページの UI イメージ](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

[インポート] を選択するとすぐに、インポートしたダッシュボードが自動的に表示されます。

![OSM Grafana ダッシュボードの [メッシュの詳細] ページの UI イメージ](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

## <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>OSM の Kubernetes に Jaeger Operator をデプロイして構成する

[Jaeger](https://www.jaegertracing.io/) は、分散システムの監視とトラブルシューティングに使用されるオープンソースのトレース システムです。 OSM を新しいインスタンスとして使用してこれをデプロイすることも、独自のインスタンスを使用することもできます。 次の手順では、Jaeger の新しいインスタンスを AKS クラスターの `jaeger` 名前空間にデプロイします。

### <a name="deploy-jaeger-to-the-aks-cluster"></a>Jaeger を AKS クラスターにデプロイする

Jaeger をインストールするには、次のマニフェストを適用します。

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
        - name: jaeger
          image: jaegertracing/all-in-one
          args:
          - --collector.zipkin.host-port=9411
          imagePullPolicy: IfNotPresent
          ports:
          - containerPort: 9411
          resources:
            limits:
              cpu: 500m
              memory: 512M
            requests:
              cpu: 100m
              memory: 256M
---
kind: Service
apiVersion: v1
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  selector:
    app: jaeger
  ports:
  - protocol: TCP
    # Service port and target port are the same
    port: 9411
  type: ClusterIP
EOF
```

```Output
deployment.apps/jaeger created
service/jaeger created
```

### <a name="enable-tracing-for-the-osm-add-on"></a>OSM アドオンのトレースを有効にする

次に、OSM アドオンのトレースを有効にする必要があります。

OSM アドオンのトレースを有効にするには、次のコマンドを使用します。

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"observability":{"tracing":{"enable":true}}}}' --type=merge
```

```Output
meshconfig.config.openservicemesh.io/osm-mesh-config patched
```

### <a name="view-the-jaeger-ui-with-port-forwarding"></a>ポート フォワーディングを使用して Jaeger UI を表示する

Jaeger の UI はポート 16686 で実行されています。 Web UI を表示するには、kubectl port-forward を使用できます。

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

ブラウザーにサービスのドロップダウンが表示され、bookstore のデモによってデプロイされたさまざまなアプリケーションから選択できます。 サービスを選択して、それからのすべての範囲を表示します。 たとえば、1 時間のルックバックを使用して `bookbuyer` を選択すると、bookstore-v1 および bookstore-v2 との相互作用を時間で並べ替えて表示できます。

![OSM Jaeger トレース ページの UI イメージ](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

項目を選択すると、それが詳細に表示されます。 複数の項目を選択して、トレースを比較します。 たとえば、特定の時点での bookstore および bookstore-v2 と `bookbuyer` との相互作用を比較できます。

また、[システム アーキテクチャ] タブを選択して、さまざまなアプリケーションがどのように相互作用および通信しているかをグラフに表示することもできます。 これにより、アプリケーション間でのトラフィックの流れを把握できます。

![OSM Jaeger [システム アーキテクチャ] の UI イメージ](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)