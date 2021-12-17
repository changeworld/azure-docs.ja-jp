---
title: Bicep を使用して Open Service Mesh の AKS アドオンをデプロイする
description: Bicep を使用して、Azure Kubernetes Service (AKS) に Open Service Mesh をデプロイします
services: container-service
ms.topic: article
ms.date: 9/20/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: b0d2e22f0d23f558221b88d7c02539dd29dd8cae
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132708728"
---
# <a name="deploy-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-using-bicep"></a>Bicep を使用して Open Service Mesh (OSM) の Azure Kubernetes Service (AKS) アドオンをデプロイする

この記事では、[Bicep](../azure-resource-manager/bicep/index.yml) テンプレートを使用して OSM アドオンを AKS にデプロイする方法について説明します。

[Bicep](../azure-resource-manager/bicep/overview.md) は、宣言型の構文を使用して Azure リソースをデプロイするドメイン固有言語 (DSL) です。 コードとしてのインフラストラクチャ Azure リソースをデプロイするために、Azure [ARM](../azure-resource-manager/templates/overview.md) テンプレートを作成する代わりに Bicep を使用できます。

## <a name="prerequisites"></a>前提条件

- Azure CLI バージョン 2.20.0 以降
- OSMバージョンバージョン v 0.11.1 以降
- AKS のデプロイに使用される SSH 公開キー
- Bash ターミナルを利用する [Visual Studio Code](https://code.visualstudio.com/)
- Visual Studio Code の [Bicep 拡張機能](../azure-resource-manager/bicep/install.md)

## <a name="install-the-aks-preview-extension"></a>aks-preview 拡張機能をインストールする

_aks-preview_ Azure CLI 拡張機能バージョン 0.5.24 以降が必要です。 _aks-preview_ Azure CLI 拡張機能は、[az extension add][az-extension-add] コマンドを使用してインストールします。 または、[az extension update][az-extension-update] コマンドを使用すると、使用可能な更新プログラムをインストールできます。

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

## <a name="install-the-osm-aks-add-on-for-a-new-aks-cluster-using-bicep"></a>Bicep を使用して新しい AKS クラスターに OSM AKS アドオンをインストールする

新しい AKS クラスターのデプロイ シナリオでは、クラスターの作成操作で OSM アドオンを有効にして AKS クラスターの新しいデプロイを開始します。 次の手順では、エフェメラル ディスクを使用し、[`kubenet`](./configure-kubenet.md) CNI を使用し、AKS OSM アドオンを有効にすることによって AKS クラスターをデプロイする、汎用 Bicep テンプレートを使用します。 さらに高度なデプロイのシナリオについては、[Bicep](../azure-resource-manager/bicep/overview.md) のドキュメントを参照してください。

### <a name="create-a-resource-group"></a>リソース グループを作成する

Azure では、リソース グループを使用して、関連するリソースを関連付けることができます。 [az group create](/cli/azure/group#az_group_create) を使用してリソース グループを作成します。 次の例は、指定した Azure の場所 (リージョン) に指定した名前のリソース グループを作成するために使用されます。

```azurecli-interactive
az group create --name <my-osm-bicep-aks-cluster-rg> --location <azure-region>
```

### <a name="create-the-main-and-parameters-bicep-files"></a>メインとパラメーターの Bicep ファイルを作成する

Visual Studio Code を使用して bash ターミナルを開き、必要な Bicep デプロイ ファイルを格納するディレクトリを作成します。 次の例では、`bicep-osm-aks-addon` という名前のディレクトリを作成して、そのディレクトリに移動します

```azurecli-interactive
mkdir bicep-osm-aks-addon
cd bicep-osm-aks-addon
```

次に、次の例に示すように、メインとパラメーターの両方のファイルを作成します。

```azurecli-interactive
touch osm.aks.bicep && touch osm.aks.parameters.json
```

`osm.aks.bicep` ファイルを開き、次の内容の例をコピーして、ファイルを保存します。

```azurecli-interactive
// https://docs.microsoft.com/azure/aks/troubleshooting#what-naming-restrictions-are-enforced-for-aks-resources-and-parameters
@minLength(3)
@maxLength(63)
@description('Provide a name for the AKS cluster. The only allowed characters are letters, numbers, dashes, and underscore. The first and last character must be a letter or a number.')
param clusterName string
@minLength(3)
@maxLength(54)
@description('Provide a name for the AKS dnsPrefix. Valid characters include alphanumeric values and hyphens (-). The dnsPrefix can\'t include special characters such as a period (.)')
param clusterDNSPrefix string
param k8Version string
param sshPubKey string


resource aksCluster 'Microsoft.ContainerService/managedClusters@2021-03-01' = {
  name: clusterName
  location: resourceGroup().location
  identity: {
    type: 'SystemAssigned'
  }
  properties: {
    kubernetesVersion: k8Version
    dnsPrefix: clusterDNSPrefix
    enableRBAC: true
    agentPoolProfiles: [
      {
        name: 'agentpool'
        count: 3
        vmSize: 'Standard_DS2_v2'
        osDiskSizeGB: 30
        osDiskType: 'Ephemeral'
        osType: 'Linux'
        mode: 'System'
      }
    ]
    linuxProfile: {
      adminUsername: 'adminUserName'
      ssh: {
        publicKeys: [
          {
            keyData: sshPubKey
          }
        ]
      }
    }
    addonProfiles: {
        openServiceMesh: {
            enabled: true
            config: {}
      }
    }
  }
}
```

`osm.aks.parameters.json` ファイルを開き、次の内容の例をコピーします。 デプロイ固有のパラメーターを追加してから、ファイルを保存します。

> [!NOTE]
> `osm.aks.parameters.json` は、Bicep のデプロイに必要なテンプレート パラメーター ファイルの例です。 指定されているパラメーターを、デプロイ環境に合わせて更新する必要があります。 この例で使用されている次のパラメーターの特定のパラメーター値を更新する必要があります。 _clusterName_、_clusterDNSPrefix_、_k8Version_、_sshPubKey_ です。 リージョンでサポートされている Kubernetes のバージョンの一覧を確認するには、`az aks get-versions --location <region>` コマンドを使用してください。

```azurecli-interactive
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "<YOUR CLUSTER NAME HERE>"
    },
    "clusterDNSPrefix": {
      "value": "<YOUR CLUSTER DNS PREFIX HERE>"
    },
    "k8Version": {
      "value": "<YOUR SUPPORTED KUBERNETES VERSION HERE>"
    },
    "sshPubKey": {
      "value": "<YOUR SSH KEY HERE>"
    }
  }
}
```

### <a name="deploy-the-bicep-file"></a>Bicep ファイルをデプロイする

以前に作成した Bicep ファイルをデプロイするには、ターミナルを開き、`az login` コマンドを使用して、Azure CLI 用の Azure アカウントで認証を行います。 Azure サブスクリプションに対する認証が完了したら、デプロイ用に次のコマンドを実行します。

```azurecli-interactive
az group create --name osm-bicep-test --location eastus2

az deployment group create \
  --name OSMBicepDeployment \
  --resource-group osm-bicep-test \
  --template-file osm.aks.bicep \
  --parameters @osm.aks.parameters.json
```

デプロイが完了すると、デプロイが成功したことを示すメッセージが表示されます。

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

現在は、OSM MeshConfig リソースを使用して OSM コントローラーの構成にアクセスして構成できます。また、次に示すように、CLI の **kubectl get** コマンドを使用して、OSM コントローラーの構成設定を表示できます。

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

MeshConfig の出力を次に示します。

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

**enablePermissiveTrafficPolicyMode** が **true** に構成されていることに注意してください。 OSM の制限のないトラフィック ポリシー モードは、[SMI](https://smi-spec.io/) トラフィック ポリシーの適用がバイパスされるモードです。 このモードでは、OSM によってサービス メッシュの一部であるサービスが自動的に検出されます。 検出されたサービスには、これらのサービス間で通信できるように各 Envoy プロキシ サイドカーでプログラムされたトラフィック ポリシー規則があります。

> [!WARNING]
> 続行する前に、制限のないトラフィック ポリシー モードが true に設定されていることを確認してください。そうでない場合は、下のコマンドを使用して **true** に変更してください

```OSM Permissive Mode to True
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}' --type=merge
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure リソースが不要になったら、Azure CLI を使用してデプロイ テスト リソース グループを削除します。

```
az group delete --name osm-bicep-test
```

<!-- Links -->
<!-- Internal -->

[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update