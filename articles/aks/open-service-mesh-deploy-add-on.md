---
title: Open Service Mesh (プレビュー) をデプロイする
description: Azure Kubernetes Service (AKS) に Open Service Mesh をデプロイする
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 8476d6c01c0c388eefad42eb89d65011c57bfa65
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440193"
---
# <a name="deploy-the-open-service-mesh-aks-add-on-preview"></a>Open Service Mesh AKS アドオン (プレビュー) をデプロイする
この記事では、OSM アドオンを AKS にデプロイする方法について説明します。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>前提条件

- Azure CLI バージョン 2.20.0 以降
- `aks-preview` 拡張機能バージョン 0.5.5 以降
- OSM バージョン v0.9.1 以降
- JSON プロセッサ "jq" バージョン 1.6 以上

## <a name="install-the-aks-preview-extension"></a>aks-preview 拡張機能をインストールする

*aks-preview* Azure CLI 拡張機能バージョン 0.5.24 以降が必要です。 *aks-preview* Azure CLI 拡張機能は、[az extension add][az-extension-add] コマンドを使用してインストールします。 または、[az extension update][az-extension-update] コマンドを使用すると、使用可能な更新プログラムをインストールできます。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="register-the-aks-openservicemesh-preview-feature"></a>`AKS-OpenServiceMesh` プレビュー機能を登録する

Open Service Mesh アドオンを使用できる AKS クラスターを作成するには、サブスクリプションで `AKS-OpenServiceMesh` 機能フラグを有効にする必要があります。

`AKS-OpenServiceMesh` 機能フラグは、次の例のとおり、[az feature register][az-feature-register] コマンドを使用して登録します。

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

状態が _[登録済み]_ と表示されるまでに数分かかります。 登録の状態は、[az feature list][az-feature-list] コマンドで確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、_Microsoft.ContainerService_ リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>新しい AKS クラスター用に Open Service Mesh (OSM) Azure Kubernetes Service (AKS) アドオンをインストールする

新しい AKS クラスターのデプロイ シナリオでは、クラスターの作成操作で OSM アドオンを有効にして AKS クラスターの新しいデプロイを開始します。

### <a name="create-a-resource-group"></a>リソース グループを作成する

Azure で、関連するリソースをリソース グループに割り当てます。 [az group create](/cli/azure/group#az_group_create) を使用してリソース グループを作成します。 次の例では、_myOsmAksGroup_ という名前のリソース グループを _eastus2_ の場所 (リージョン) に作成します。

```azurecli-interactive
az group create --name <my-osm-aks-cluster-rg> --location <azure-region>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>OSM アドオンが有効になっている AKS クラスターをデプロイする

次に、OSM アドオンが有効になっている新しい AKS クラスターをデプロイします。

> [!NOTE]
> 次の AKS デプロイ コマンドでは、AKS のデプロイの例として OS のエフェメラル ディスクを使用していることに注意してください。 [AKS のエフェメラル OS ディスク](./cluster-configuration.md#ephemeral-os)の詳細については、こちらを参照してください

```azurecli-interactive
az aks create -n <my-osm-aks-cluster-name> -g <my-osm-aks-cluster-rg> --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>AKS クラスター アクセス資格情報を取得する

新しいマネージド Kubernetes クラスターのアクセス資格情報を取得します。

```azurecli-interactive
az aks get-credentials -n <my-osm-aks-cluster-name> -g <my-osm-aks-cluster-rg>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>既存の AKS クラスター用に Open Service Mesh (OSM) Azure Kubernetes Service (AKS) アドオンを有効にする

既存の AKS クラスターのシナリオでは、既にデプロイされている既存の AKS クラスターに対して OSM アドオンを有効にします。

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>既存の AKS クラスターに対して OSM アドオンを有効にする

AKS OSM アドオンを有効にするには、パラメーター `open-service-mesh` を渡して `az aks enable-addons --addons` コマンドを実行する必要があります

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <my-osm-aks-cluster-rg> -n <my-osm-aks-cluster-name>
```

AKS OSM アドオンがインストールされていることを確認するため、下に示す出力のような出力が表示されます。

```json
{- Finished ..
  "aadProfile": null,
  "addonProfiles": {
    "KubeDashboard": {
      "config": null,
      "enabled": false,
      "identity": null
    },
    "openServiceMesh": {
      "config": {},
      "enabled": true,
      "identity": {
...
```

## <a name="validate-the-aks-osm-add-on-installation"></a>AKS OSM アドオンのインストールを検証する

AKS OSM アドオンのすべてのコンポーネントが有効で実行されていることを確認するためには、実行するコマンドがいくつかあります。

まず、クラスターのアドオン プロファイルに対してクエリを実行し、インストールされているアドオンの有効状態を確認します。 次のコマンドは、"true" を返します。

```azurecli-interactive
az aks list -g <my-osm-aks-cluster-rg> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

次の `kubectl` コマンドを実行すると、osm-controller の状態がレポートされます。

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on-configuration"></a>AKS OSM アドオンの構成にアクセスする

現在、OSM コントローラーには、OSM MeshConfig リソースからアクセスしてその構成を行うことができます。 OSM コントローラーの構成設定を CLI で確認するには、次のように **kubectl** get コマンドを使用します。

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

MeshConfig の出力は次のようになります。

```
apiVersion: config.openservicemesh.io/v1alpha1
kind: MeshConfig
metadata:
  creationTimestamp: "0000-00-00A00:00:00A"
  generation: 1
  name: osm-mesh-config
  namespace: kube-system
  resourceVersion: "2494"
  uid: 6c4d67f3-c241-4aeb-bf4f-b029b08faa31
spec:
  certificate:
    serviceCertValidityDuration: 24h
  featureFlags:
    enableEgressPolicy: true
    enableMulticlusterMode: false
    enableWASMStats: true
  observability:
    enableDebugServer: true
    osmLogLevel: info
    tracing:
      address: jaeger.osm-system.svc.cluster.local
      enable: false
      endpoint: /api/v2/spans
      port: 9411
  sidecar:
    configResyncInterval: 0s
    enablePrivilegedInitContainer: false
    envoyImage: mcr.microsoft.com/oss/envoyproxy/envoy:v1.18.3
    initContainerImage: mcr.microsoft.com/oss/openservicemesh/init:v0.9.1
    logLevel: error
    maxDataPlaneConnections: 0
    resources: {}
  traffic:
    enableEgress: true
    enablePermissiveTrafficPolicyMode: true
    inboundExternalAuthorization:
      enable: false
      failureModeAllow: false
      statPrefix: inboundExtAuthz
      timeout: 1s
    useHTTPSIngress: false
```

**enablePermissiveTrafficPolicyMode** が **true** に構成されていることに注意してください。 OSM の制限のないトラフィック ポリシー モードは、[SMI](https://smi-spec.io/) トラフィック ポリシーの適用がバイパスされるモードです。 このモードの OSM では、サービス メッシュの一部であるサービスが自動的に検出され、これらのサービスと通信できるように各エンボイ プロキシ サイドカーのトラフィック ポリシー規則がプログラムされます。 制限の少ないトラフィック モードの詳細については、[制限の少ないトラフィック ポリシー モード](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/)に関する記事を参照してください。 

> [!WARNING]
> 続行する前に、制限のないトラフィック ポリシー モードが true に設定されていることを確認してください。そうでない場合は、下のコマンドを使用して **true** に変更してください

```OSM Permissive Mode to True
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}' --type=merge
```