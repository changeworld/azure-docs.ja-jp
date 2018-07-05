---
title: Azure Kubernetes Service (AKS) クラスターで Virtual Kubelet を実行する
description: Azure Container Instances で Kubernetes コンテナーを実行するには、Virtual Kubelet を使用します。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/12/2018
ms.author: iainfou
ms.openlocfilehash: 04fdb1620dc6e7147ed10ae6eeeaeb3eeae14b62
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097361"
---
# <a name="virtual-kubelet-with-aks"></a>AKS での Virtual Kubelet

Azure Container Instances (ACI) には Azure 内でコンテナーを実行するためのホスト環境が用意されています。 ACI を使用する場合、Azure がユーザーに代わって管理を担当するので、基礎となるコンピューティング インフラストラクチャを管理する必要はありません。 ACI でコンテナーを実行すると、実行中の各コンテナーについて秒単位で課金されます。

Azure Container Instances に Virtual Kubelet プロバイダーを使用する場合、標準の Kubernetes ノードであるかのように、コンテナー インスタンス上で Linux と Windows 両方のコンテナーをスケジュールすることができます。 この構成では、Kubernetes の機能と、コンテナー インスタンスの管理値とコスト上の利点の両方を活用できます。

> [!NOTE]
> Virtual Kubelet は実験的なオープン ソース プロジェクトなので、その点を考慮して使用する必要があります。 Virtual Kubelet に関する投稿、問題の提起、詳細情報については、[Virtual Kubelet GitHub プロジェクト][vk-github]に関するページを参照してください。

このドキュメントでは、AKS 上でコンテナー インスタンス用に Virtual Kubelet を構成する方法について詳しく説明します。

## <a name="prerequisite"></a>前提条件

このドキュメントでは、AKS クラスターがあることを前提としています。 AKS クラスターが必要な場合は、[Azure Kubernetes Service (AKS) のクイック スタート][aks-quick-start]に関するページを参照してください。

また、Azure CLI バージョン **2.0.33** 以降がインストールされている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

Virtual Kubelet をインストールするには、[Helm](https://docs.helm.sh/using_helm/#installing-helm) も必要です。

## <a name="installation"></a>インストール

Virtual Kubelet をインストールするには、[az aks install-connector][aks-install-connector] コマンドを使用します。 次の例では、Linux と Windows 両方のコネクタを展開します。

```azurecli-interactive
az aks install-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet --os-type Both
```

これらの引数は `aks install-connector` コマンドに使用できます。

| 引数: | 説明 | 必須 |
|---|---|:---:|
| `--connector-name` | ACI コネクタの名前。| [はい] |
| `--name` `-n` | マネージド クラスターの名前。 | [はい] |
| `--resource-group` `-g` | リソース グループの名前。 | [はい] |
| `--os-type` | コンテナー インスタンスのオペレーティング システムのタイプ。 使用できる値: Both、Linux、Windows。 既定値: Linux。 | いいえ  |
| `--aci-resource-group` | ACI コンテナー グループを作成するリソース グループ。 | いいえ  |
| `--location` `-l` | ACI コンテナー グループを作成する場所。 | いいえ  |
| `--service-principal` | Azure API に対する認証に使用されるサービス プリンシパル。 | いいえ  |
| `--client-secret` | サービス プリンシパルに関連付けられているシークレット。 | いいえ  |
| `--chart-url` | ACI Connector をインストールする Helm チャートの URL。 | いいえ  |
| `--image-tag` | Virtual Kubelet コンテナー イメージのイメージ タグ。 | いいえ  |

## <a name="validate-virtual-kubelet"></a>Virtual Kubelet を検証する

Virtual Kubelet がインストールされていることを検証するには、[kubectl get nodes][kubectl-get] コマンドを使用して Kubernetes ノードの一覧を返します。

```console
$ kubectl get nodes

NAME                                    STATUS    ROLES     AGE       VERSION
aks-nodepool1-23443254-0                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-1                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-2                Ready     agent     16d       v1.9.6
virtual-kubelet-virtual-kubelet-linux   Ready     agent     4m        v1.8.3
virtual-kubelet-virtual-kubelet-win     Ready     agent     4m        v1.8.3
```

## <a name="run-linux-container"></a>Linux コンテナーを実行する

`virtual-kubelet-linux.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。 `kubernetes.io/hostname` の値を Linux Virtual Kubelet ノードの名前に置き換えます。 ノード上のコンテナーをスケジュールするために [nodeSelector][node-selector] と [toleration][toleration] が使用されている点に気を付けてください。

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-linux
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

[kubectl create][kubectl-create] コマンドを使用してアプリケーションを実行します。

```azurecli-interactive
kubectl create -f virtual-kubelet-linux.yaml
```

`-o wide` 引数を指定して [kubectl get pods][kubectl-get] コマンドを使用し、スケジュールされたノードがあるポッドの一覧を出力します。 `aci-helloworld` ポッドは `virtual-kubelet-virtual-kubelet-linux` ノードでスケジュールされています。

```console
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
aci-helloworld-2559879000-8vmjw     1/1       Running   0          39s       52.179.3.180   virtual-kubelet-virtual-kubelet-linux
```

## <a name="run-windows-container"></a>Windows コンテナーを実行する

`virtual-kubelet-windows.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。 `kubernetes.io/hostname` の値を Windows Virtual Kubelet ノードの名前に置き換えます。 ノード上のコンテナーをスケジュールするために [nodeSelector][node-selector] と [toleration][toleration] が使用されている点に気を付けてください。

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: nanoserver/iis
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-win
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

[kubectl create][kubectl-create] コマンドを使用してアプリケーションを実行します。

```azurecli-interactive
kubectl create -f virtual-kubelet-windows.yaml
```

`-o wide` 引数を指定して [kubectl get pods][kubectl-get] コマンドを使用し、スケジュールされたノードがあるポッドの一覧を出力します。 `nanoserver-iis` ポッドは `virtual-kubelet-virtual-kubelet-win` ノードでスケジュールされています。

```console
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
nanoserver-iis-868bc8d489-tq4st     1/1       Running   8         21m       138.91.121.91   virtual-kubelet-virtual-kubelet-win
```

## <a name="remove-virtual-kubelet"></a>Virtual Kubelet を削除する

Virtual Kubelet を削除するには、[az aks remove-connector][aks-remove-connector] コマンドを使用します。 引数の値を、コネクタ、AKS クラスター、および AKS クラスター リソース グループの名前に置き換えます。

```azurecli-interactive
az aks remove-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet
```

## <a name="next-steps"></a>次の手順

[Virtual Kubelet Github プロジェクト][vk-github]のページで Virtual Kubelet の詳細について参照してください。

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az_aks_list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
