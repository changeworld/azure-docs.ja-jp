---
title: Azure Arc 対応 Open Service Mesh (プレビュー)
description: Arc 対応 Kubernetes クラスターの Open Service Mesh (OSM) 拡張機能
services: azure-arc
ms.service: azure-arc
ms.date: 05/24/2021
ms.topic: article
author: mayurigupta13
ms.author: mayg
ms.openlocfilehash: 83140603eab8ed28f3ea82d3f9ab205f84569c75
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110483095"
---
# <a name="azure-arc-enabled-open-service-mesh-preview"></a>Azure Arc 対応 Open Service Mesh (プレビュー)

[Open Service Mesh (OSM)](https://docs.openservicemesh.io/) は、軽量で拡張可能なクラウド ネイティブ サービス メッシュであり、ユーザーは、高度に動的なマイクロサービス環境のために、すぐに使用できる監視機能を統一して管理し、セキュリティで保護することができます。

OSM では、Kubernetes 上でエンボイベースのコントロール プレーンを実行します。また OSM は [SMI](https://smi-spec.io/) API を使用して構成でき、アプリケーションの各インスタンスの横に、エンボイ プロキシをサイドカー コンテナーとして挿入することで機能します。 Open Service Mesh を利用したサービス メッシュの使用方法に関する[ドキュメント](https://docs.openservicemesh.io/#features)をご覧ください。

### <a name="support-limitations-for-arc-enabled-open-service-mesh"></a>Arc 対応 Open Service Mesh のサポートの制約

- Open Service Mesh のインスタンスは、Arc に接続した Kubernetes クラスター 1 つにつき、1 つしかデプロイできません。
- v0.8.4 以上のバージョンの Open Service Mesh で利用できるのは、パブリック プレビュー版です。 最新のバージョンは[こちら](https://github.com/Azure/osm-azure/releases)で確認できます。
- 現在、次の Kubernetes ディストリビューションをサポートしています
    - AKS Engine
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

## <a name="install-arc-enabled-open-service-mesh-osm-on-an-arc-enabled-kubernetes-cluster"></a>Arc 対応 Open Service Mesh (OSM) を Arc 対応 Kubernetes クラスターにインストールする

次の手順では、サポートしている Kubernetes ディストリビューションを使用したクラスターが既にあって、それが Azure Arc に接続してあることを想定しています。

### <a name="install-a-specific-version-of-osm"></a>OSM の特定バージョンをインストールする

KUBECONFIG 環境変数に、OSM 拡張機能をインストールする Kubernetes クラスターの kubeconfig を指定します。

環境変数を設定します。

```azurecli-interactive
export VERSION=0.8.4
export $CLUSTER_NAME=<arc-cluster-name>
export $RESOURCE_GROUP=<resource-group-name>
```

Arc 対応 Open Service Mesh はプレビュー版ですが、`az k8s-extension create` コマンドでは `--release-train` フラグとして `pilot` だけを使用できます。 `--auto-upgrade-minor-version` は常に `false` に設定されているので、バージョンを指定する必要があります。 OpenShift クラスターを使用している場合は、この[セクション](#install-a-specific-version-of-osm-on-openshift-cluster)の手順に従ってください。

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
  "version": "0.8.4"
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

### <a name="install-arc-enabled-osm-using-arm-template"></a>ARM テンプレートを使用して Arc 対応 OSM をインストールする

クラスターを Azure Arc に接続してから、次の形式の json ファイルを作成します。<cluster-name> の値を置き換えることを忘れないでください。

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
            "defaultValue": "0.8.4",
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

## <a name="validate-the-arc-enabled-open-service-mesh-installation"></a>Arc 対応 Open Service Mesh がインストールされたことを確認する

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
  "version": "0.8.4"
}
```

## <a name="osm-controller-configuration"></a>OSM コントローラーの構成

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

使用できるそれぞれの構成を理解するには、[OSM ConfigMap のドキュメント](https://release-v0-8.docs.openservicemesh.io/docs/osm_config_map/)を参照ください。 **permissive_traffic_policy_mode** が **true** に構成されていることに注目してください。 OSM の制限のないトラフィック ポリシー モードは、[SMI](https://smi-spec.io/) トラフィック ポリシーの適用がバイパスされるモードです。 このモードの OSM では、サービス メッシュの一部であるサービスが自動的に検出され、これらのサービスと通信できるように各エンボイ プロキシ サイドカーのトラフィック ポリシー規則がプログラムされます。

### <a name="making-changes-to-osm-configmap"></a>OSM ConfigMap を変更する

OSM ConfigMap を変更するには次の説明に従ってください。

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

## <a name="using-the-arc-enabled-open-service-mesh"></a>Arc 対応 Open Service Mesh を使用する

OSM の機能を使用するには、アプリケーションの名前空間をサービス メッシュにオンボードしておく必要があります。 OSM CLI を [GitHub の OSM のリリースのページ](https://github.com/openservicemesh/osm/releases/)からダウンロードします。 メッシュに名前空間を追加したら、SMI ポリシーを構成して OSM の機能を自由に使用できます。

### <a name="onboard-namespaces-to-the-service-mesh"></a>名前空間をサービス メッシュにオンボードする

次のコマンドを実行して名前空間をメッシュに追加します。

```azurecli-interactive
osm namespace add <namespace_name>
```

サービスの詳しいオンボード方法は、[こちら](https://docs.openservicemesh.io/docs/tasks_usage/onboard_services/)で説明されています。

### <a name="configure-osm-with-service-mesh-interface-smi-policies"></a>Service Mesh Interface (SMI) ポリシーで OSM を構成する

[デモ アプリケーション](https://release-v0-8.docs.openservicemesh.io/docs/install/manual_demo/)を確認することから始めてもかまいませんし、テスト環境で SMI ポリシーを試すこともできます。

> [!NOTE] 
> 実行する書店アプリのバージョンが、クラスターにインストールした OSM 拡張機能のバージョンと一致することを確認してください。 例: OSM 拡張機能の v0.8.4 を使用している場合は、OSM 上流リポジトリの release-v0.8 ブランチのブックストア デモを使用します。

### <a name="configuring-your-own-jaeger-prometheus-and-grafana-instances"></a>独自の Jaeger、Prometheus、Grafana インスタンスを構成する

OSM 拡張機能にインストールされた [Jaeger](https://www.jaegertracing.io/docs/getting-started/)、[Prometheus](https://prometheus.io/docs/prometheus/latest/installation/)、[Grafana](https://grafana.com/docs/grafana/latest/installation/) は既定で無効にしてあります。ユーザーが、これらのツールの独自のインスタンスを OSM と連携させられるようにすることが目的です。 独自のインスタンスを連携させる方法は、次のドキュメントをご確認ください。

- [BYO-Jaeger instance](https://github.com/openservicemesh/osm-docs/blob/main/content/docs/tasks_usage/observability/tracing.md#byo-bring-your-own) (独自の Jaeger インスタンス)
    - このドキュメントで説明している値を設定するには、`osm-config` ConfigMap を次の設定で更新する必要があります。
        ```json
        {
          "osm.OpenServiceMesh.tracing.enable": "true",
          "osm.OpenServiceMesh.tracing.address": "<tracing server hostname>",
          "osm.OpenServiceMesh.tracing.port": "<tracing server port>",
          "osm.OpenServiceMesh.tracing.endpoint": "<tracing server endpoint>",
        }
        ```
        [このセクション](#making-changes-to-osm-configmap)の説明に従って、これらの設定を osm-config にプッシュします。
- [BYO-Prometheus instance](https://github.com/openservicemesh/osm/blob/release-v0.8/docs/content/docs/tasks_usage/metrics.md#byo-bring-your-own) (独自の Prometheus インスタンス)
- [BYO-Grafana dashboard](https://github.com/openservicemesh/osm/blob/release-v0.8/docs/content/docs/tasks_usage/metrics.md#importing-dashboards-on-a-byo-grafana-instance) (独自の Grafana ダッシュボード)


## <a name="monitoring-application-using-azure-monitor-and-applications-insights"></a>Azure Monitor と Applications Insights でアプリケーションを監視する

Azure Monitor と Azure Application Insights の両方では、クラウドおよびオンプレミス環境からテレメトリを収集、分析、対応するための包括的なソリューションを提供することで、アプリケーションやサービスの可用性とパフォーマンスを最大限に高めます。

今後、Arc 対応 Open Service Mesh とこれらの Azure サービスの高度な連携を実装する予定であり、OSM のメトリクスに基づく重要な KPI の確認と対応を、Azure でシームレスに実行できるようになります。 下の手順に従って、Azure Monitor でprometheus エンドポイントのスクレイピングを有効にし、アプリケーションのメトリクスを収集できるようにします。 

1. OSM の ConfigMap で prometheus_scraping が true になっていることを確認します。

2. 監視するアプリケーションの名前空間がメッシュにオンボードされていることを確認します。 [こちら](#onboard-namespaces-to-the-service-mesh)に記載された説明に従います。

3. アプリケーションの名前空間の prometheus エンドポイントをデータ収集対象に設定します。
    ```azurecli-interactive
    osm metrics enable --namespace <namespace1>
    osm metrics enable --namespace <namespace2>
    ```

4. [こちら](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json)の説明に従って Azure Monitor 拡張機能をインストールします。

5. 監視する名前空間を container-azm-ms-osmconfig ConfigMap に追加します。 [こちら](https://github.com/microsoft/Docker-Provider/blob/ci_prod/kubernetes/container-azm-ms-osmconfig.yaml)で ConfigMap をダウンロードします。
    ```azurecli-interactive
    monitor_namespaces = ["namespace1", "namespace2"]
    ```

6. 次の kubectl コマンドを実行します。
    ```azurecli-interactive
    kubectl apply -f container-azm-ms-osmconfig.yaml
    ```

Log Analytics にメトリクスを表示するように最大 15 分程度かかります。 InsightsMetrics テーブルに対するクエリを実行できます。

```azurecli-interactive
InsightsMetrics
| where Name contains "envoy"
| extend t=parse_json(Tags)
| where t.app == "namespace1"
```
Azure Monitor との連携の詳細は[こちら](https://github.com/microsoft/Docker-Provider/blob/ci_dev/Documentation/OSMPrivatePreview/ReadMe.md)をご覧ください。

### <a name="navigating-the-osm-dashboard"></a>OSM ダッシュボード内を移動する

1. この[リンク](https://aka.ms/azmon/osmarcux)から、Arc に接続した Kubernetes クラスターにアクセスします。
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

> [!NOTE]
> OSM のアドオンをアップグレードすると、ユーザーが OSM ConfigMap で構成した値が上書きされる可能性があります。

ConfigMap のこれまでの変更が上書きされないよう、これまでの変更に使用したのと同じ構成設定ファイルを指定してください。

アップグレード中、コントロール プレーンでダウンタイムが発生する場合があります。 データ プレーンは、CRD のアップグレード中のみ影響を受けます。

### <a name="supported-upgrades"></a>サポートされるアップグレード

OSM 拡張機能は、次のマイナー バージョンにアップグレードできます。 ダウングレードとメジャー バージョンのアップグレードは、現在サポートしていません。

### <a name="crd-upgrades"></a>CRD アップグレード

アップグレード先のバージョンで CRD バージョンが更新されている場合は、既存の CRD を先に削除しなければ、OSM 拡張機能をそのバージョンにアップグレードすることはできません。 アップグレード先の OSM で CRD バージョンが更新されているかどうかは、[OSM リリース ノート](https://github.com/openservicemesh/osm/releases)の CRD Updates (CRD の更新) セクションで確認できます。

[OSM CRD Upgrades のドキュメント](https://github.com/openservicemesh/osm/blob/release-v0.8/docs/content/docs/upgrade_guide.md#crd-upgrades)を確認し、クラスターのアップグレードに備えます。 アップグレード後にすぐ再作成できるよう、CRD を削除する前に Custom Resource をバックアップします。 その後で、このガイドの説明に従って、Helm や OSM CLI の代わりに az k8s-extension を使用して、アップグレードを実行してください。

> [!NOTE] 
> CRD をアップグレードすると、削除されてから再び作成されるまでの間 SMI ポリシーが存在しなくなるので、その間データ プレーンが影響を受けます。

### <a name="upgrade-instructions"></a>アップグレード方法の説明

1. 必要に応じて、[古くなった CRD を削除し、新しい CRD をインストールします](https://github.com/openservicemesh/osm/blob/release-v0.8/docs/content/docs/upgrade_guide.md#crd-upgrades)。
    - 新しく作成するときの参照用に、既存の Custom Resources をバックアップします。
    - 新しいバージョンの拡張機能をインストールする前に、更新された CRD と Custom Resources をインストールします。

2. chart の新しいバージョンを環境変数に設定します。
    ```azurecli-interactive
    export VERSION=<chart version>
    ```
    
3. chart の新しいバージョンを使用して az k8s-extension create を実行します。
    ```azurecli-interactive
    az k8s-extension create --cluster-name $CLUSTER_NAME --resource-group $RESOURCE_GROUP --cluster-type connectedClusters --extension-type Microsoft.openservicemesh --scope cluster --release-train pilot --name osm --version $VERSION --configuration-settings-file $SETTINGS_FILE
    ```

4. 必要に応じて、新しい CRD で Custom Resources を再作成します。

## <a name="uninstall-arc-enabled-open-service-mesh"></a>Arc 対応 Open Service Mesh をアンインストールします。

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

[こちら](troubleshooting.md#arc-enabled-open-service-mesh)のトラブルシューティング ガイドを参考にしてください。

## <a name="next-steps"></a>次のステップ

> **試してみたい場合**  
> [Azure Arc Jumpstart](https://aka.ms/arc-jumpstart-osm) の使用パターン (scenario) を参考に、Cluster API を使用して、すぐに作業を開始できます。
