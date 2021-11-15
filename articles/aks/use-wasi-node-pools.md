---
title: Azure Kubernetes Service (AKS) で WebAssembly System Interface (WASI) ノード プールを作成して WebAssembly (WASM) ワークロードを実行する (プレビュー)
description: Azure Kubernetes Service (AKS) で WebAssembly System Interface (WASI) ノード プールを作成し、Kubernetes で WebAssembly (WASM) ワークロードを実行する方法について説明します。
services: container-service
ms.topic: article
ms.date: 10/12/2021
ms.openlocfilehash: 91649a6c7893970eee5685554385eed1493c0094
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265965"
---
# <a name="create-webassembly-system-interface-wasi-node-pools-in-azure-kubernetes-service-aks-to-run-your-webassembly-wasm-workload-preview"></a>Azure Kubernetes Service (AKS) で WebAssembly System Interface (WASI) ノード プールを作成して WebAssembly (WASM) ワークロードを実行する (プレビュー)

[WebAssembly (WASM)][wasm] は、WASM ランタイムでの高速ダウンロードと最大実行速度用に最適化されたバイナリ形式です。 WASM ランタイムは、ターゲット アーキテクチャ上で実行され、ホスト コンピューターから分離されたサンドボックス内で、ほぼネイティブなパフォーマンスで WebAssembly を実行するように設計されています。 既定では、WebAssembly は、明示的に許可されていない限り、サンドボックスの外部にあるホスト上のリソースにアクセスできません。また、ソケットを介した通信で、環境変数や HTTP トラフィックにアクセスすることはできません。 [WebAssembly System Interface (WASI)][wasi] 標準では、WASM ランタイム用の API を定義して、WebAssembly が機能モデルを使用してホストの外部の環境およびリソースにアクセスできるようにします。 [Krustlet][krustlet] は、WASM モジュールを Kubernetes で実行できるようにするオープンソース プロジェクトです。 Krustlet では、WASM/WASI ランタイムを使用してノード上で実行される kubelet が作成されます。 AKS では、ノードで WASM/WASI ランタイムと Krustlet を使用して、WASM アセンブリを実行するノード プールを作成できます。

## <a name="before-you-begin"></a>開始する前に

WASM/WASI ノード プールは現在プレビュー段階です。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

この記事では、[Helm 3][helm] を使用して、サポートされているバージョンの Kubernetes に "*nginx*" グラフをインストールします。 最新リリースの Helm を使用していること、および "*bitnami*" Helm リポジトリにアクセスできることを確認してください。 この記事に記載されている手順は、以前のバージョンの Helm グラフまたは Kubernetes と互換性がない可能性があります。

次のリソースがインストールされている必要もあります。

* 最新バージョンの Azure CLI。
* `aks-preview` 拡張機能バージョン 0.5.34 以降

### <a name="register-the-wasmnodepoolpreview-preview-feature"></a>`WasmNodePoolPreview` プレビュー機能を登録する

この機能を使用するには、サブスクリプションで `WasmNodePoolPreview` 機能フラグも有効にする必要があります。

`WasmNodePoolPreview` 機能フラグは、次の例のとおり、[az feature register][az-feature-register] コマンドを使用して登録します。

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "WasmNodePoolPreview"
```

状態が *[登録済み]* と表示されるまでに数分かかります。 登録の状態は、[az feature list][az-feature-list] コマンドで確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WasmNodePoolPreview')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>`aks-preview` Azure CLI をインストールする

"*aks-preview*" Azure CLI 拡張機能バージョン 0.5.34 以降も必要です。 *aks-preview* Azure CLI 拡張機能は、[az extension add][az-extension-add] コマンドを使用してインストールします。 または、[az extension update][az-extension-update] コマンドを使用すると、使用可能な更新プログラムをインストールできます。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>制限事項

* 同じノード プールで WebAssebmly とコンテナーを実行することはできません。
* Wasmtime プロバイダーを使用して、WebAssembly (WASI) ランタイムのみを使用できます。
* WASM/WASI ノード プールは、システム ノード プールには使用できません。
* WASM/WASI ノード プールの *os-type* は Linux である必要があります。
* 現時点では、Krustlet は Azure CNI では機能しません。 詳細については、[Kruslet GitHub イシューの CNI サポート][krustlet-cni-support]を参照してください。
* Krustlet では、WebAssebmly のネットワーク構成は提供されません。 WebAssebmly マニフェストで、IP アドレスなどのネットワーク構成を提供する必要があります。

## <a name="add-a-wasmwasi-node-pool-to-an-existing-aks-cluster"></a>既存の AKS クラスターに WASM/WASI ノード プールを追加する

WASM/WASI ノード プールを追加するには、[az aks nodepool add][az-aks-nodepool-add] コマンドを使用します。 次の例では、1 つのノードを持つ WASI ノード プールを "*mywasipool*" という名前で作成しています。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mywasipool \
    --node-count 1 \
    --workload-runtime wasmwasi 
```

> [!NOTE]
> "*workload-runtime*" パラメーターの既定値は、"*ocicontainer*" です。 コンテナー ワークロードを実行するノード プールを作成するには、"*workload-runtime*" パラメーターを省略するか、値を "*ocicontainer*" に設定します。

`az aks nodepool show` を使用して "*workloadRuntime*" 値を確認します。 次に例を示します。

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mywasipool
```

次の出力例では、"*WasmWasi*" の "*workloadRuntime*" タイプを持つ "*mywasipool*" ノードが示されています。

```output
{
  ...
  "name": "mywasipool",
  ..
  "workloadRuntime": "WasmWasi"
}
```

WASM/WASI ノード プールでは、テイントが `kubernetes.io/arch=wasm32-wagi:NoSchedule` と `kubernetes.io/arch=wasm32-wagi:NoExecute` に設定されていることを確認します。これにより、このノード プールでアプリケーション ポッドがスケジュールされることを防ぐことができます。 また、nodeLabels は `kubernetes.io/arch: wasm32-wasi` である必要があります。これにより、WASM ポッドが通常のコンテナー (OCI) ノード プールでスケジュールされるのを防ぐことができます。

> [!NOTE]
> WASI ノード プールのテイントは、`az aks nodepool list` を使用しても表示されません。 `kubectl` を使用して、WASI ノード プール内のノードにテイントが設定されていることを確認します。

[az aks get-credentials][az-aks-get-credentials] コマンドを使用して、Kubernetes クラスターに接続するように `kubectl` を構成します。 次のコマンドで、以下を行います。  

```azurecli
az aks get-credentials -n myakscluster -g myresourcegroup
```

`kubectl get nodes` を使用して、クラスターのノードを表示します。

```output
$ kubectl get nodes -o wide
NAME                                 STATUS   ROLES   AGE   VERSION         INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION     CONTAINER-RUNTIME
aks-mywasipool-12456878-vmss000000   Ready    agent    9m   1.0.0-alpha.1   WASINODE_IP   <none>        <unknown>            <unknown>          mvp
aks-nodepool1-12456878-vmss000000    Ready    agent   13m   v1.20.9         NODE1_IP      <none>        Ubuntu 18.04.6 LTS   5.4.0-1059-azure   containerd://1.4.9+azure
```

後の手順で使用するために、"*WASINODE_IP*" の値を保存します。

`kubectl describe node` を使用して、WASI ノード プール内のノードのラベルとテイントを表示します。 次の例は、"*aks-mywasipool-12456878-vmss000000*" の詳細を示しています。

```output
$ kubectl describe node aks-mywasipool-12456878-vmss000000

Name:               aks-mywasipool-12456878-vmss000000
Roles:              agent
Labels:             agentpool=mywasipool
...
                    kubernetes.io/arch=wasm32-wagi
...
Taints:             kubernetes.io/arch=wasm32-wagi:NoExecute
                    kubernetes.io/arch=wasm32-wagi:NoSchedule
```


## <a name="running-wasmwasi-workload"></a>WASM/WASI ワークロードの実行

WASM/WASI ノード プールでワークロードを実行するには、ノード セレクターと公差をデプロイに追加します。 次に例を示します。

```yml
...
spec:
  nodeSelector:
    kubernetes.io/arch: "wasm32-wagi"
  tolerations:
    - key: "node.kubernetes.io/network-unavailable"
      operator: "Exists"
      effect: "NoSchedule"
    - key: "kubernetes.io/arch"
      operator: "Equal"
      value: "wasm32-wagi"
      effect: "NoExecute"
    - key: "kubernetes.io/arch"
      operator: "Equal"
      value: "wasm32-wagi"
      effect: "NoSchedule"
...
```

サンプル デプロイを実行するには、次の YAML 定義を使用して `wasi-example.yaml` ファイルを作成します。

```yml
apiVersion: v1
kind: Pod
metadata:
  name: krustlet-wagi-demo
  labels:
    app: krustlet-wagi-demo
  annotations:
    alpha.wagi.krustlet.dev/default-host: "0.0.0.0:3001"
    alpha.wagi.krustlet.dev/modules: |
      {
        "krustlet-wagi-demo-http-example": {"route": "/http-example", "allowed_hosts": ["https://api.brigade.sh"]},
        "krustlet-wagi-demo-hello": {"route": "/hello/..."},
        "krustlet-wagi-demo-error": {"route": "/error"},
        "krustlet-wagi-demo-log": {"route": "/log"},
        "krustlet-wagi-demo-index": {"route": "/"}
      }
spec:
  hostNetwork: true
  nodeSelector:
    kubernetes.io/arch: wasm32-wagi
  containers:
    - image: webassembly.azurecr.io/krustlet-wagi-demo-http-example:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-http-example
    - image: webassembly.azurecr.io/krustlet-wagi-demo-hello:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-hello
    - image: webassembly.azurecr.io/krustlet-wagi-demo-index:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-index
    - image: webassembly.azurecr.io/krustlet-wagi-demo-error:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-error
    - image: webassembly.azurecr.io/krustlet-wagi-demo-log:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-log
  tolerations:
    - key: "node.kubernetes.io/network-unavailable"
      operator: "Exists"
      effect: "NoSchedule"
    - key: "kubernetes.io/arch"
      operator: "Equal"
      value: "wasm32-wagi"
      effect: "NoExecute"
    - key: "kubernetes.io/arch"
      operator: "Equal"
      value: "wasm32-wagi"
      effect: "NoSchedule"
```

`kubectl` を使用して、デプロイの例を実行します。

```azurecli-interactive
kubectl apply -f wasi-example.yaml
```

> [!NOTE]
> サンプル デプロイのポッドの状態が "*登録済み*" のままになることがあります。 これは想定されていることなので、次の手順に進んでください。

次の例の yaml を使用し、"*WASINODE_IP*" を前の手順の値に置き換えて `values.yaml` を作成します。

```yml
serverBlock: |-
  server {
    listen 0.0.0.0:8080;
    location / {
            proxy_pass http://WASINODE_IP:3001;
    }
  }
```

[Helm][helm] を使用して *bitnami* リポジトリを追加し、前のステップで作成した `values.yaml` ファイルで "*nginx*" グラフをインストールします。 上記の `values.yaml` で NGINX をインストールすると、サンプル デプロイへのリバース プロキシが作成され、外部 IP アドレスを使用してアクセスできるようになります。

>[!NOTE]
> 次の例では、パブリック コンテナー イメージを Docker Hub からプルします。 匿名の pull request を行うのではなく、Docker Hub アカウントを使用して認証するようにプル シークレットを設定することをお勧めします。 パブリック コンテンツを操作するときの信頼性を向上させるために、プライベートの Azure Container Registry にイメージをインポートして管理します。 [パブリック イメージの操作に関する詳細を参照してください][dockerhub-callout]。

```console
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm install hello-wasi bitnami/nginx -f values.yaml
```

`kubectl get service` を使用して、"*hello-wasi-ngnix*" サービスの外部 IP アドレスを表示します。

```output
$ kubectl get service
NAME               TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
hello-wasi-nginx   LoadBalancer   10.0.58.239   EXTERNAL_IP      80:32379/TCP   112s
kubernetes         ClusterIP      10.0.0.1      <none>           443/TCP        145m
```

サンプル デプロイが、"*EXTERNAL_IP*" の `/hello` パスに対して `curl` コマンドによって実行されることを確認します。

```azurecli-interactive
curl EXTERNAL_IP/hello
```

次の出力例で、サンプル デプロイが実行されていることを確認できます。

```output
$ curl EXTERNAL_IP/hello
hello world
```

> [!NOTE] 
> 独自のドメインでサービスを発行するには、[Azure DNS][azure-dns-zone] と [外部 DNS][external-dns] プロジェクトをご参照ください。

## <a name="clean-up"></a>クリーンアップ

NGINX を削除するには、`helm delete` を使用します。

```console
helm delete hello-wasi
```

サンプル デプロイを削除するには、`kubectl delete` を使用します。

```azurecli-interactive
kubectl delete -f wasi-example.yaml
```

WASM/WASI ノード プールを削除するには、`az aks nodepool delete` を使用します。

```azurecli-interactive
az aks nodepool delete --name mywasipool -g myresourcegroup --cluster-name myakscluster
```


<!-- EXTERNAL LINKS -->
[helm]: https://helm.sh/
[krustlet]: https://krustlet.dev/
[krustlet-cni-support]: https://github.com/krustlet/krustlet/issues/533
[wasm]: https://webassembly.org/
[wasi]: https://wasi.dev/
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

<!-- INTERNAL LINKS -->

[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool#az_aks_nodepool_list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool#az_aks_nodepool_update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool#az_aks_nodepool_upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az_aks_nodepool_scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool#az_aks_nodepool_delete
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[dockerhub-callout]: ../container-registry/buffer-gate-public-content.md
[install-azure-cli]: /cli/azure/install-azure-cli
[use-multiple-node-pools]: use-multiple-node-pools.md
[use-system-pool]: use-system-pools.md
