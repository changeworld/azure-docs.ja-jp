---
title: Open Service Mesh をデプロイする
description: Azure CLI を使用して Azure Kubernetes Service (AKS) に Open Service Mesh をデプロイする
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 93ff4f0d8565f439bc16e887b0dd31e8f14249e9
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132025750"
---
# <a name="deploy-the-open-service-mesh-aks-add-on-using-azure-cli"></a>Azure CLI を使用して Open Service Mesh AKS アドオンをデプロイする

この記事では、OSM アドオンを AKS にデプロイする方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure CLI バージョン 2.20.0 以降
- OSMバージョンバージョン v 0.11.1 以降

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>新しい AKS クラスター用に Open Service Mesh (OSM) Azure Kubernetes Service (AKS) アドオンをインストールする

新しい AKS クラスターのデプロイ シナリオでは、クラスターの作成操作で OSM アドオンを有効にして AKS クラスターの新しいデプロイを開始します。

### <a name="create-a-resource-group"></a>リソース グループを作成する

Azure で、関連するリソースをリソース グループに割り当てます。 [az group create](/cli/azure/group#az_group_create) を使用してリソース グループを作成します。 次の例は、指定した場所 (リージョン) にリソース グループを作成するために使用されます。

```azurecli-interactive
az group create --name <my-osm-aks-cluster-rg> --location <azure-region>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>OSM アドオンが有効になっている AKS クラスターをデプロイする

次に、OSM アドオンが有効になっている新しい AKS クラスターをデプロイします。

> [!NOTE]
> 次の AKS デプロイ コマンドでは OS エフェメラル ディスクを使用することに注意してください。 [AKS のエフェメラル OS ディスク](./cluster-configuration.md#ephemeral-os)の詳細については、こちらを参照してください

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

> [!NOTE]
> OSM アドオンのデプロイを成功するには、クラスターに 1 つの OSM メッシュ インスタンスのみをデプロイする必要があります。 クラスターに他の OSM メッシュインスタンスがある場合は、コマンドを実行する前にアンインストールしてください `enable-addons` 。

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

### <a name="check-osm-add-on-version"></a>OSM アドオンのバージョンを確認する

インストールした OSM アドオンのバージョンは v0.11.1 以上である必要があります。 これを確認するには、次のコマンドを実行し、osm-controller のイメージのバージョンを確かめます。この情報はイメージのタグにエンコードされています。 

```azurecli-interactive
kubectl get deployment -n kube-system osm-controller -o=jsonpath='{$.spec.template.spec.containers[:1].image}'
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

**enablePermissiveTrafficPolicyMode** が **true** に構成されていることに注意してください。 OSM の制限のないトラフィック ポリシー モードは、[SMI](https://smi-spec.io/) トラフィック ポリシーの適用がバイパスされるモードです。 このモードの OSM では、サービス メッシュの一部であるサービスが自動的に検出され、これらのサービスと通信できるように各エンボイ プロキシ サイドカーのトラフィック ポリシー規則がプログラムされます。

> [!WARNING]
> 続行する前に、制限のないトラフィック ポリシー モードが true に設定されていることを確認してください。そうでない場合は、下のコマンドを使用して **true** に変更してください

```OSM Permissive Mode to True
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}' --type=merge
```

## <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>AKS クラスターの Open Service Mesh (OSM) アドオンを無効にする

OSM アドオンを無効にするには、次のコマンドを実行します。

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```
OSM アドオンを無効にすると、次のリソースがクラスターに残ります。
1. OSM MeshConfig カスタム リソース
2. OSM コントロール プレーン シークレット
3. Webhook の構成を変更する OSM
4. Webhook の構成を検証する OSM
5. OSM CRD

> [!IMPORTANT]
> OSM アドオンを無効にした後、これらの追加リソースを削除する必要があります。 これらのリソースをクラスターに残すと、今後 OSM アドオンを再度有効にした場合に問題が発生する可能性があります。

これらの残ったリソースを削除するには、次のようにします。

1. MeshConfig 構成リソースを削除します
```azurecli-interactive
kubectl delete --ignore-not-found meshconfig -n kube-system osm-mesh-config
```

2. OSM コントロール プレーン シークレットを削除します
```azurecli-interactive
kubectl delete --ignore-not-found secret -n kube-system osm-ca-bundle mutating-webhook-cert-secret validating-webhook-cert-secret crd-converter-cert-secret
```

3. Webhook の構成を変更する OSM を削除します
```azurecli-interactive
kubectl delete mutatingwebhookconfiguration -l app.kubernetes.io/name=openservicemesh.io,app.kubernetes.io/instance=osm,app=osm-injector --ignore-not-found
```

4. Webhook の構成を検証する OSM を削除します
```azurecli-interactive
kubectl delete validatingwebhookconfiguration -l app.kubernetes.io/name=openservicemesh.io,app.kubernetes.io/instance=osm,app=osm-controller --ignore-not-found
```

5. OSM CRD を削除します。OSM の CRD に関するガイダンスとそれらを削除する方法については、[このドキュメント](https://release-v0-11.docs.openservicemesh.io/docs/getting_started/uninstall/#removal-of-osm-cluster-wide-resources)を参照してください。

<!-- Links -->
<!-- Internal -->

[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
