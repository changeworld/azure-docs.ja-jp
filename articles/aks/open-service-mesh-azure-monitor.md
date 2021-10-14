---
title: Azure Monitor と Application Insights の使用
description: Azure Monitor と Application Insights を Open Service Mesh と一緒に使用する方法
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 4e1df1275e225b42bf01bd2a4092cea07f9c4c12
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057816"
---
# <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Azure Monitor と Application Insights を使用した Open Service Mesh (OSM) の監視

Azure Monitor と Azure Application Insights のどちらも、アプリケーションとサービスの可用性とパフォーマンスの最大化を支援します。 これらのサービスは、クラウドおよびオンプレミス環境のテレメトリを収集、分析し、対応する包括的なソリューションを提供します。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

OSM AKS アドオンは、これらの Azure サービスの両方に緊密に統合され、OSM メトリックによって提供される重要な KPI を表示して対応するためにシームレスな Azure のエクスペリエンスが提供されます。 

## <a name="enable-azure-monitor"></a>Azure Monitor を有効にする

OSM AKS アドオンが AKS クラスターで有効になると、Azure Monitor を Azure portal を使用してクラスターで有効にする必要があります。 AKS クラスターをクリックし、[監視] の [分析情報] タブに移動し、[有効にする] を選択します。 

Azure Monitor が有効になると、kube-system 名前空間に次のログが表示されるはずです。 

```
kube-system     omsagent-5pn4c                        1/1     Running   0          24m
kube-system     omsagent-6r6zt                        1/1     Running   0          24m
kube-system     omsagent-j8xrh                        1/1     Running   0          24m
kube-system     omsagent-rs-74b8f7dfd8-rp5vx          1/1     Running   1          24m
```

## <a name="enable-metrics-in-osm-monitored-namespaces"></a>OSM で監視される名前空間でメトリックを有効にする

メッシュで監視する特定の名前空間からメトリクスをスクレイピングするには、次のコマンドを実行する必要があります。

```sh
osm metrics enable --osm-namespace <namespace>
```

たとえば、[bookstore デモ](https://docs.openservicemesh.io/docs/getting_started/quickstart/manual_demo/)を実行している場合、次の名前空間で `osm metrics enable` コマンドを実行します。

```sh
osm metrics enable --osm-namespace bookbuyer
osm metrics enable --osm-namespace bookstore
osm metrics enable --osm-namespace bookthief
osm metrics enable --osm-namespace bookwarehouse
```
## <a name="apply-configmaps"></a>ConfigMaps を適用する

`kube-system` で次の ConfigMap を作成します。監視するべき名前空間が AzMon に通知されます。 たとえば、bookbuyer / bookstore デモの場合、ConfigMap は次のようになります。 

```yaml
kind: ConfigMap
apiVersion: v1
data:
  schema-version: v1
  config-version: ver1
  osm-metric-collection-configuration: |-
    # OSM metric collection settings
    [osm_metric_collection_configuration]
      [osm_metric_collection_configuration.settings]
          # Namespaces to monitor
          monitor_namespaces = ["bookstore", "bookbuyer", "bookthief", "bookwarehouse"]
metadata:
  name: container-azm-ms-osmconfig
  namespace: kube-system

```

次に、[monitor_kubernetes_pods を true に](https://github.com/microsoft/Docker-Provider/blob/24b709f9e3c3b18779102b491fc98b87a99d1335/kubernetes/container-azm-ms-agentconfig.yaml#L72)設定するために 2 つ目の ConfigMap を作成する必要があります。

```yaml
kind: ConfigMap
apiVersion: v1
metadata:
  name: container-azm-ms-agentconfig
  namespace: kube-system
data:
  schema-version: v1
  config-version: ver1
  prometheus-data-collection-settings: |-
    [prometheus_data_collection_settings.cluster]
        interval = "30s"
        monitor_kubernetes_pods = true
```

## <a name="view-metrics-in-the-azure-portal"></a>Azure portal でメトリックを表示する

Azure portal で、Kubernetes クラスターを選択し、[監視] で [ログ] タブを選択します。 これで `InsightsMetrics` テーブルにクエリを実行し、有効な名前空間でメトリクスを表示できます。 たとえば、`bookbuyer` の envoy メトリクスを表示する場合、次のクエリを使用します。

```sh
InsightsMetrics
| where Name contains "envoy"
| extend t=parse_json(Tags)
| where t.app == "bookbuyer"
```

## <a name="additional-information"></a>関連情報

OSM AKS アドオンに対して Azure Monitor と Azure Application Insights を有効にして構成する方法の詳細については、[OSM の Azure Monitor](https://aka.ms/azmon/osmpreview) に関するページを参照してください。