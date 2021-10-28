---
title: Azure Arc 対応 Open Service Mesh (プレビュー)
description: Azure Arc 対応 Kubernetes クラスターの Open Service Mesh (OSM) 拡張機能
ms.service: azure-arc
ms.date: 07/23/2021
ms.topic: article
author: mayurigupta13
ms.author: mayg
ms.openlocfilehash: d09bed41ff7553e79a4d05e616e07fabf85d71a9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130227133"
---
# <a name="azure-arc-enabled-open-service-mesh-preview"></a>Azure Arc 対応 Open Service Mesh (プレビュー)

[Open Service Mesh (OSM)](https://docs.openservicemesh.io/) は、軽量で拡張可能なクラウド ネイティブ サービス メッシュであり、ユーザーは、高度に動的なマイクロサービス環境のために、すぐに使用できる監視機能を統一して管理し、セキュリティで保護することができます。

OSM では、Kubernetes 上でエンボイベースのコントロール プレーンを実行します。また OSM は [SMI](https://smi-spec.io/) API を使用して構成でき、アプリケーションの各インスタンスの横に、エンボイ プロキシをサイドカー コンテナーとして挿入することで機能します。 Open Service Mesh を利用したサービス メッシュの使用方法に関する[ドキュメント](https://docs.openservicemesh.io/#features)をご覧ください。

### <a name="support-limitations-for-azure-arc-enabled-open-service-mesh"></a>Azure Arc 対応 Open Service Mesh のサポートの制約

- Open Service Mesh のインスタンスは、Azure Arc に接続した Kubernetes クラスター 1 つにつき 1 つしかデプロイできません。
- v0.8.4 以上のバージョンの Open Service Mesh で利用できるのは、パブリック プレビュー版です。 最新のバージョンは[こちら](https://github.com/Azure/osm-azure/releases)で確認できます。 サポートされているリリース バージョンには注釈があります。 中間リリースに関連付けられているタグは無視してください。 
- 現在、次の Kubernetes ディストリビューションをサポートしています。
    - AKS Engine
    - HCI 上の AKS
    - Cluster API Azure
    - Google Kubernetes Engine
    - Canonical Kubernetes Distribution
    - Rancher Kubernetes Engine
    - OpenShift Kubernetes Distribution
    - Amazon Elastic Kubernetes Service
- Azure Monitor と Azure Arc 対応 Open Service Mesh の連携は、[制限付きでサポート](https://github.com/microsoft/Docker-Provider/blob/ci_dev/Documentation/OSMPrivatePreview/ReadMe.md)しています。

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>前提条件

- クラスター拡張機能の前提条件をすべて満たしていることを[こちら](extensions.md#prerequisites)で確認します。
- バージョン v0.4.0 以上の az k8s-extension CLI を使用します。

## <a name="install-azure-arc-enabled-open-service-mesh-osm-on-an-azure-arc-enabled-kubernetes-cluster"></a>Azure Arc 対応 Open Service Mesh (OSM) を Azure Arc 対応 Kubernetes クラスターにインストールする

次の手順では、サポートしている Kubernetes ディストリビューションを使用したクラスターが既にあって、それが Azure Arc に接続してあることを想定しています。

### <a name="install-a-specific-version-of-osm"></a>OSM の特定バージョンをインストールする

KUBECONFIG 環境変数に、OSM 拡張機能をインストールする Kubernetes クラスターの kubeconfig を指定します。

環境変数を設定します。

```azurecli-interactive
export VERSION=<osm-arc-version>
export CLUSTER_NAME=<arc-cluster-name>
export RESOURCE_GROUP=<resource-group-name>
```

Azure Arc 対応 Open Service Mesh はプレビュー版ですが、`az k8s-extension create` コマンドでは `--release-train` フラグとして `pilot` だけを使用できます。 `--auto-upgrade-minor-version` は常に `false` に設定されているので、バージョンを指定する必要があります。 OpenShift クラスターを使用している場合は、この[セクション](#install-a-specific-version-of-osm-on-openshift-cluster)の手順に従ってください。

```azurecli-interactive
az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION
```

下に示す出力と同様の出力が表示されます。 OSM helm chart をクラスターにデプロイするには 3 - 5 分程度かかります。 このデプロイが実行されるまで installState は Pending になります。

```json
{
  "autoUpgradeMinorVersion": false,
  "configurationSettings": {},
  "creationTime": "2021-04-29T17:50:11.4116524+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.openservicemesh",
  "id": "/subscriptions/<subscription-id>/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Kubernetes/connectedClusters/$CLUSTER_NAME/providers/Microsoft.KubernetesConfiguration/extensions/osm",
  "identity": null,
  "installState": "Pending",
  "lastModifiedTime": "2021-04-29T17:50:11.4116525+00:00",
  "lastStatusTime": null,
  "location": null,
  "name": "osm",
  "releaseTrain": "pilot",
  "resourceGroup": "$RESOURCE_GROUP",
  "scope": {
    "cluster": {
      "releaseNamespace": "arc-osm-system"
    },
    "namespace": null
  },
  "statuses": [],
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "x.x.x"
}
```

### <a name="install-a-specific-version-of-osm-on-openshift-cluster"></a>特定バージョンの OSM を OpenShift クラスターにインストールする

1. 次の内容を JSON ファイルにコピーして保存します。 構成設定ファイルを作成済みである場合は、これまでの変更を引き継ぐため、既存のファイルに次のコードを追加してください。
   ```json
   {
       "osm.OpenServiceMesh.enablePrivilegedInitContainer": "true"
   }
   ```

   環境変数でファイルのパスを設定します。
   ```azurecli-interactive
   export SETTINGS_FILE=<json-file-path>
   ```
   
2. OSM 拡張機能の作成に使用する `az k8s-extension create` コマンドを実行して 、構成設定 を使用して設定ファイルを渡します。
   ```azurecli-interactive
   az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION --configuration-settings-file $SETTINGS_FILE
   ```
   
3. メッシュでアプリケーションを使用する各サービス アカウントに、特権 [セキュリティ コンテキスト制約](https://docs.openshift.com/container-platform/4.7/authentication/managing-security-context-constraints.html)を追加します。
   ```azurecli-interactive
   oc adm policy add-scc-to-user privileged -z <service account name> -n <service account namespace>
   ```

OSM helm chart をクラスターにデプロイするには 3 - 5 分程度かかります。 このデプロイが実行されるまで installState は Pending になります。

特権を持つ init コンテナーの設定を既定値に戻さないため、これ以降に実行するすべての az k8s-extension create コマンドで、構成設定として "osm.OpenServiceMesh.enablePrivilegedInitContainer" : "true" を指定します。

### <a name="install-azure-arc-enabled-osm-using-arm-template"></a>ARM テンプレートを使用して Azure Arc 対応 OSM をインストールする

クラスターを Azure Arc に接続してから、次の形式の json ファイルを作成します。\<cluster-name\> および \<osm-arc-version\> の値を置き換えることを忘れないでください。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "ConnectedClusterName": {
            "defaultValue": "<cluster-name>",
            "type": "String",
            "metadata": {
                "description": "The Connected Cluster name."
            }
        },
        "ExtensionInstanceName": {
            "defaultValue": "osm",
            "type": "String",
            "metadata": {
                "description": "The extension instance name."
            }
        },
        "ExtensionVersion": {
            "defaultValue": "<osm-arc-version>",
            "type": "String",
            "metadata": {
                "description": "The extension type version."
            }
        },
        "ExtensionType": {
            "defaultValue": "Microsoft.openservicemesh",
            "type": "String",
            "metadata": {
                "description": "The extension type."
            }
        },
        "ReleaseTrain": {
            "defaultValue": "Pilot",
            "type": "String",
            "metadata": {
                "description": "The release train."
            }
        }
    },
    "functions": [],
    "resources": [
        {
            "type": "Microsoft.KubernetesConfiguration/extensions",
            "apiVersion": "2020-07-01-preview",
            "name": "[parameters('ExtensionInstanceName')]",
            "properties": {
                "extensionType": "[parameters('ExtensionType')]",
                "releaseTrain": "[parameters('ReleaseTrain')]",
                "version": "[parameters('ExtensionVersion')]"
            },
            "scope": "[concat('Microsoft.Kubernetes/connectedClusters/', parameters('ConnectedClusterName'))]"
        }
    ]
}
```

次に環境変数を設定します。

```azurecli-interactive
export TEMPLATE_FILE_NAME=<template-file-path>
export DEPLOYMENT_NAME=<desired-deployment-name>
```

最後に az CLI でこのコマンドを実行して、OSM 拡張機能をインストールします。

```azurecli-interactive
az deployment group create --name $DEPLOYMENT_NAME --resource-group $RESOURCE_GROUP --template-file $TEMPLATE_FILE_NAME
```

これで、OSM リソースを表示して、OSM 拡張機能をクラスターで使用できるようになります。

## <a name="validate-the-azure-arc-enabled-open-service-mesh-installation"></a>Azure Arc 対応 Open Service Mesh がインストールされたことを確認する

次のコマンドを実行します。

```azurecli-interactive
az k8s-extension show --cluster-type connectedClusters --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --name osm
```

下の出力と同様の JSON が出力されます。

```json
{
  "autoUpgradeMinorVersion": false,
  "configurationSettings": {},
  "creationTime": "2021-04-29T19:22:00.7649729+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.openservicemesh",
  "id": "/subscriptions/<subscription-id>/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Kubernetes/connectedClusters/$CLUSTER_NAME/providers/Microsoft.KubernetesConfiguration/extensions/osm",
  "identity": null,
  "installState": "Installed",
  "lastModifiedTime": "2021-04-29T19:22:00.7649731+00:00",
  "lastStatusTime": "2021-04-29T19:23:27.642+00:00",
  "location": null,
  "name": "osm",
  "releaseTrain": "pilot",
  "resourceGroup": "$RESOURCE_GROUP",
  "scope": {
    "cluster": {
      "releaseNamespace": "arc-osm-system"
    },
    "namespace": null
  },
  "statuses": [],
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "x.x.x"
}
```
## <a name="osm-controller-configuration"></a>OSM コントローラーの構成
OSM では、arc-osm-system 名前空間内のコントロール プレーンの一部として MeshConfig リソース `osm-mesh-config` がデプロイされます。 この MeshConfig の目的は、メッシュの所有者またはオペレーターに、そのニーズに基づいてメッシュ構成の一部を更新する機能を提供することです。 既定値を表示するには、次のコマンドを使用します。

```azurecli-interactive
kubectl describe meshconfig osm-mesh-config -n arc-osm-system
```
出力には既定値が表示されます。

```azurecli-interactive
Certificate:
    Service Cert Validity Duration:  24h
  Feature Flags:
    Enable Egress Policy:      true
    Enable Multicluster Mode:  false
    Enable WASM Stats:         true
  Observability:
    Enable Debug Server:  false
    Osm Log Level:        info
    Tracing:
      Address:   jaeger.osm-system.svc.cluster.local
      Enable:    false
      Endpoint:  /api/v2/spans
      Port:      9411
  Sidecar:
    Config Resync Interval:            0s
    Enable Privileged Init Container:  false
    Envoy Image:                       mcr.microsoft.com/oss/envoyproxy/envoy:v1.18.3
    Init Container Image:              mcr.microsoft.com/oss/openservicemesh/init:v0.9.1
    Log Level:                         error
    Max Data Plane Connections:        0
    Resources:
  Traffic:
    Enable Egress:                          false
    Enable Permissive Traffic Policy Mode:  true
    Inbound External Authorization:
      Enable:              false
      Failure Mode Allow:  false
      Stat Prefix:         inboundExtAuthz
      Timeout:             1s
    Use HTTPS Ingress:     false
```
詳細については、「[構成 API リファレンス](https://docs.openservicemesh.io/docs/api_reference/config/v1alpha1/)」を参照してください。 **spec.traffic.enablePermissiveTrafficPolicyMode** が **true** に設定されていることに注意してください。 OSM の制限のないトラフィック ポリシー モードは、[SMI](https://smi-spec.io/) トラフィック ポリシーの適用がバイパスされるモードです。 このモードの OSM では、サービス メッシュの一部であるサービスが自動的に検出され、これらのサービスと通信できるように各エンボイ プロキシ サイドカーのトラフィック ポリシー規則がプログラムされます。

### <a name="making-changes-to-osm-controller-configuration"></a>OSM コントローラーの構成の変更

> [!NOTE]
> MeshConfig `osm-mesh-config` の値はアップグレード後も保持されます。

`osm-mesh-config` に対する変更は、kubectl patch コマンドを使用して実行できます。 次の例では、制限の少ないトラフィック ポリシー モードが false に変更されています。

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n arc-osm-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":false}}}' --type=merge
```

正しくない値が使用されている場合、MeshConfig CRD の検証によって、値が無効である理由を説明するエラー メッセージが表示されて変更が阻止されます。 たとえば、次のコマンドは、enableEgress を非ブール値に修正した場合にどうなるかを示しています。

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n arc-osm-system -p '{"spec":{"traffic":{"enableEgress":"no"}}}'  --type=merge

# Validations on the CRD will deny this change
The MeshConfig "osm-mesh-config" is invalid: spec.traffic.enableEgress: Invalid value: "string": spec.traffic.enableEgress in body must be of type boolean: "string"
```

## <a name="osm-controller-configuration-version-v084"></a>OSM コントローラーの構成 (バージョン v0.8.4)

現在、ConfigMap で OSM コントローラーの構成にアクセスし、これを構成できます。 OSM コントローラーの構成設定を見るには、`kubectl` で、構成設定を表示するクエリを `osm-config` ConfigMap に対して実行します。

```azurecli-interactive
kubectl get configmap osm-config -n arc-osm-system -o json
```

出力:

```json
{
  "egress": "false",
  "enable_debug_server": "false",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "true",
  "service_cert_validity_duration": "24h",
  "tracing_enable": "false",
  "use_https_ingress": "false"
}
```

使用できるそれぞれの構成を理解するには、[OSM ConfigMap のドキュメント](https://release-v0-8.docs.openservicemesh.io/docs/osm_config_map/)を参照ください。 

バージョン v0.8.4 のOSM ConfigMap を変更するには、次のガイダンスに従ってください。

1. JSON ファイルへの変更をコピーし、保存します。 この例では、permissive_traffic_policy_mode を true から false に変更します。 `osm-config` を変更するたびに、既定の `osm-config` から変更したすべての項目を、JSON ファイル形式のリストにより指定する必要があります。
    ```json
    {
        "osm.OpenServiceMesh.enablePermissiveTrafficPolicy" : "false"
    }
    ```
    
    環境変数でファイルのパスを設定します。
    
    ```azurecli-interactive
    export SETTINGS_FILE=<json-file-path>
    ```
    
2. 拡張機能を作成したのと同じ `az k8s-extension create` コマンドを実行します。ただし、今回は構成設定ファイルを次のように指定します。 
    ```azurecli-interactive
    az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION --configuration-settings-file $SETTINGS_FILE
    ```
    
    > [!NOTE]
    > ConfigMap の変更を既定値に戻さないため、これ以降に実行するすべての az k8s-extension create コマンドで、同じ構成設定を指定します。

## <a name="using-the-azure-arc-enabled-open-service-mesh"></a>Azure Arc 対応 Open Service Mesh を使用する

OSM の機能を使用するには、アプリケーションの名前空間をサービス メッシュにオンボードしておく必要があります。 OSM CLI を [GitHub の OSM のリリースのページ](https://github.com/openservicemesh/osm/releases/)からダウンロードします。 メッシュに名前空間を追加したら、SMI ポリシーを構成して OSM の機能を自由に使用できます。

### <a name="onboard-namespaces-to-the-service-mesh"></a>名前空間をサービス メッシュにオンボードする

次のコマンドを実行して名前空間をメッシュに追加します。

```azurecli-interactive
osm namespace add <namespace_name>
```

サービスの詳しいオンボード方法は、[こちら](https://docs.openservicemesh.io/docs/guides/app_onboarding/#onboard-services)で説明されています。

### <a name="configure-osm-with-service-mesh-interface-smi-policies"></a>Service Mesh Interface (SMI) ポリシーで OSM を構成する

[デモ アプリケーション](https://release-v0-11.docs.openservicemesh.io/docs/getting_started/quickstart/manual_demo/#deploy-applications)を確認することから始めてもかまいませんし、テスト環境で SMI ポリシーを試すこともできます。

> [!NOTE] 
> 実行する書店アプリのバージョンが、クラスターにインストールした OSM 拡張機能のバージョンと一致することを確認してください。 例: OSM 拡張機能の v0.8.4 を使用している場合は、OSM 上流リポジトリの release-v0.8 ブランチのブックストア デモを使用します。

### <a name="configuring-your-own-jaeger-prometheus-and-grafana-instances"></a>独自の Jaeger、Prometheus、Grafana インスタンスを構成する

OSM 拡張機能では [Jaeger](https://www.jaegertracing.io/docs/getting-started/)、[Prometheus](https://prometheus.io/docs/prometheus/latest/installation/)、[Grafana](https://grafana.com/docs/grafana/latest/installation/) のようなアドオンがインストールされないため、ユーザーは、自分で実行しているこれらのツールのインスタンスに OSM を統合することができます。 独自のインスタンスを連携させる方法は、次のドキュメントをご確認ください。

> [!NOTE]
> OSM の GitHub ドキュメントで提供されているコマンドは注意して使用してください。 `osm-mesh-config` に変更を加える場合は、必ず正しい名前空間名 'arc-osm-system' を使用してください。

- [BYO-Jaeger instance](https://docs.openservicemesh.io/docs/guides/observability/tracing/#byo-bring-your-own) (独自の Jaeger インスタンス)
- [BYO-Prometheus instance](https://docs.openservicemesh.io/docs/guides/observability/metrics/#byo-prometheus) (独自の Prometheus インスタンス)
- [BYO-Grafana dashboard](https://docs.openservicemesh.io/docs/guides/observability/metrics/#importing-dashboards-on-a-byo-grafana-instance) (独自の Grafana ダッシュボード)


## <a name="monitoring-application-using-azure-monitor-and-applications-insights"></a>Azure Monitor と Applications Insights でアプリケーションを監視する

Azure Monitor と Azure Application Insights の両方では、クラウドおよびオンプレミス環境からテレメトリを収集、分析、対応するための包括的なソリューションを提供することで、アプリケーションやサービスの可用性とパフォーマンスを最大限に高めます。

今後、Azure Arc 対応 Open Service Mesh とこれらの Azure サービスの高度な連携を実装する予定であり、OSM のメトリクスに基づく重要な KPI の確認と対応を、Azure でシームレスに実行できるようになります。 下の手順に従って、Azure Monitor でprometheus エンドポイントのスクレイピングを有効にし、アプリケーションのメトリクスを収集できるようにします。 

1. 監視するアプリケーションの名前空間がメッシュにオンボードされていることを確認します。 [こちら](#onboard-namespaces-to-the-service-mesh)に記載された説明に従います。

2. アプリケーションの名前空間の prometheus エンドポイントをデータ収集対象に設定します。
    ```azurecli-interactive
    osm metrics enable --namespace <namespace1>
    osm metrics enable --namespace <namespace2>
    ```
    v0.8.4 の場合は、`osm-config` ConfigMap で `prometheus_scraping` が `true` に設定されています。

3. [こちら](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json)の説明に従って Azure Monitor 拡張機能をインストールします。

4. 監視する名前空間を container-azm-ms-osmconfig ConfigMap に追加します。 [こちら](https://github.com/microsoft/Docker-Provider/blob/ci_prod/kubernetes/container-azm-ms-osmconfig.yaml)で ConfigMap をダウンロードします。
    ```azurecli-interactive
    monitor_namespaces = ["namespace1", "namespace2"]
    ```

5. 次の kubectl コマンドを実行します。
    ```azurecli-interactive
    kubectl apply -f container-azm-ms-osmconfig.yaml
    ```

Log Analytics にメトリクスを表示するには最大 15 分程度かかります。 InsightsMetrics テーブルに対するクエリを実行できます。

```azurecli-interactive
InsightsMetrics
| where Name contains "envoy"
| extend t=parse_json(Tags)
| where t.app == "namespace1"
```
Azure Monitor との連携の詳細は[こちら](https://github.com/microsoft/Docker-Provider/blob/ci_dev/Documentation/OSMPrivatePreview/ReadMe.md)をご覧ください。

### <a name="navigating-the-osm-dashboard"></a>OSM ダッシュボード内を移動する

1. この[リンク](https://aka.ms/azmon/osmarcux)から、Azure Arc に接続した Kubernetes クラスターにアクセスします。
2. Azure Monitor にアクセスして [Report] タブに移動し、OSM ブックにアクセスします。
3. 期間と名前空間を選び、対象サービスを指定します。

![OSM ブック](./media/tutorial-arc-enabled-open-service-mesh/osm-workbook.jpg)

#### <a name="requests-tab"></a>要求タブ

- このタブでは、OSM のサービス間で送信されたすべての HTTP 要求の概要を確認できます。
- すべてのサービスおよび通信先サービスをグリッドで選んで見ることができます。
- 合計要求数、要求エラー率、P90 待機時間を見ることができます。
- 通信先をドリルダウンして、HTTP エラー/成功コード、成功率、ポッド リソース使用率、異なるパーセンタイルの待機時間を見ることができます。

#### <a name="connections-tab"></a>[接続] タブ

- このタブでは、Open Service Mesh のサービス間のすべての接続の概要を確認できます。
- 発信接続: 通信元と通信先の間の合計接続数。
- アクティブな発信接続: 選択期間の最後の時点での、通信元と通信先の間の接続数。
- 失敗発信接続: 通信元と通信先の間の合計接続失敗数。

## <a name="upgrade-the-osm-extension-instance-to-a-specific-version"></a>OSM 拡張機能インスタンスを特定のバージョンにアップグレードする

アップグレード中、コントロール プレーンでダウンタイムが発生する場合があります。 データ プレーンは、CRD のアップグレード中のみ影響を受けます。

### <a name="supported-upgrades"></a>サポートされるアップグレード

OSM 拡張機能は、次のマイナー バージョンにアップグレードできます。 ダウングレードとメジャー バージョンのアップグレードは、現在サポートしていません。

### <a name="crd-upgrades"></a>CRD アップグレード

アップグレード先のバージョンで CRD バージョンが更新されている場合は、既存の CRD を先に削除しなければ、OSM 拡張機能をそのバージョンにアップグレードすることはできません。 アップグレード先の OSM で CRD バージョンが更新されているかどうかは、[OSM リリース ノート](https://github.com/openservicemesh/osm/releases)の CRD Updates (CRD の更新) セクションで確認できます。

アップグレード後にすぐ再作成できるよう、CRD を削除する前に Custom Resource をバックアップします。 その後、次に示すアップグレード手順に従います。

> [!NOTE] 
> CRD をアップグレードすると、削除されてから再び作成されるまでの間 SMI ポリシーが存在しなくなるので、その間データ プレーンが影響を受けます。

### <a name="upgrade-instructions"></a>アップグレード方法の説明

1. 古い CRD とカスタム リソースを削除します ([OSM リポジトリ](https://github.com/openservicemesh/osm)のルートから実行)。 [OSM CRD](https://github.com/openservicemesh/osm/tree/main/cmd/osm-bootstrap/crds) のタグがグラフの新しいバージョンに対応していることを確認します。
    ```azurecli-interactive
    kubectl delete --ignore-not-found --recursive -f ./charts/osm/crds/

2. Install the updated CRDs.
    ```azurecli-interactive
    kubectl apply -f charts/osm/crds/
    ```

3. chart の新しいバージョンを環境変数に設定します。
    ```azurecli-interactive
    export VERSION=<chart version>
    ```
    
4. chart の新しいバージョンを使用して az k8s-extension create を実行します。
    ```azurecli-interactive
    az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION --configuration-settings-file $SETTINGS_FILE
    ```

5. 新しい CRD を使用してカスタム リソースを再作成します

## <a name="uninstall-azure-arc-enabled-open-service-mesh"></a>Azure Arc 対応 Open Service Mesh をアンインストールする

次のコマンドを使用します。

```azurecli-interactive
az k8s-extension delete --cluster-type connectedClusters --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --name osm -y
```

拡張機能のインスタンスが削除されたことを確認します。

```azurecli-interactive
az k8s-extension list --cluster-type connectedClusters --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP
```

この出力に OSM は含まれません。 クラスターにインストールしている拡張機能が他にない場合は、空の配列になります。

az k8s-extension コマンドで OSM 拡張機能を削除するとき、arc-osm-system 名前空間は削除されず、その名前空間にあるリソース (mutating Webhook 構成、OSM コントローラー ポッドなど) が実際に削除されるまで最大で 10 分程度かかります。

> [!NOTE] 
> Arc で管理している OSM コンポーネントを削除するときは az k8s-extension CLI を使用します。Arc では OSM CLI によるアンインストールをサポートしていないので、これを行うと不具合が生じる可能性があります。

## <a name="troubleshooting"></a>トラブルシューティング

[こちら](troubleshooting.md#azure-arc-enabled-open-service-mesh)のトラブルシューティング ガイドを参考にしてください。

## <a name="next-steps"></a>次のステップ

> **試してみたい場合**  
> [Azure Arc Jumpstart](https://aka.ms/arc-jumpstart-osm) の使用パターン (scenario) を参考に、Cluster API を使用して、すぐに作業を開始できます。
