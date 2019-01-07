---
title: Azure Kubernetes Service (AKS) でポータルを使用して仮想ノードを作成する
description: Azure portal を使用して、仮想ノードを使用する Azure Kubernetes Service (AKS) クラスターを作成してポッドを実行する方法を説明します。
services: container-service
author: iainfoulds
ms.service: container-service
ms.date: 12/03/2018
ms.author: iainfou
ms.openlocfilehash: 3b99afe82f77b6bd89b5afa458179abee4c98e4f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999128"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Azure portal で仮想ノードを使用する Azure Kubernetes Service (AKS) クラスターを作成して構成する

Azure Kubernetes Service (AKS) クラスターでワークロードをすばやくデプロイするには、仮想ノードを使用します。 仮想ノードを使用すると、ポッドを短時間でプロビジョニングできるため、ポッドの実行時間に対して秒単位の支払いだけで済みます。 スケーリング シナリオでは、Kubernetes クラスターのオートスケーラーが VM コンピューティング ノードをデプロイして追加のポッドを実行するのを待つ必要はありません。 この記事では、仮想ノードを有効にして、仮想ネットワーク リソースと AKS クラスターを作成して構成する方法を示します。

> [!IMPORTANT]
> AKS の仮想ノードは現在、**プレビュー**段階です。 プレビュー版は、[追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-an-aks-cluster"></a>AKS クラスターの作成

Azure portal の左上隅で、**[リソースの作成]** > **[Kubernetes Service]** を選択します。

**[基本]** ページで、次のオプションを構成します。

- *プロジェクトの詳細*:サブスクリプションを選択し、Azure リソース グループ (たとえば、*myResourceGroup*) を選択または作成します。 **Kubernetes クラスター名** (たとえば、*myAKSCluster*) を入力します。
- *クラスターの詳細*:AKS クラスターのリージョン、Kubernetes バージョン、および DNS 名プレフィックスを選択します。
- *規模の設定*:AKS ノードの VM サイズを選択します。 AKS クラスターがデプロイされた後に、VM サイズを変更することは**できません**。
    - クラスターにデプロイするノードの数を選択します。 この記事では、**[ノード数]** を *1* に設定します。 ノード数は、クラスターをデプロイした後に調整**できます**。
    - **[仮想ノード]** で *[有効]* を選択します。

![AKS クラスターを作成し、仮想ノードを有効にする](media/virtual-nodes-portal/enable-virtual-nodes.png)

既定では、Azure Active Directory サービス プリンシパルが作成されます。 このサービス プリンシパルは、クラスター通信と他の Azure サービスとの統合に使用されます。

このクラスターは高度なネットワークに対しても構成されます。 仮想ノードは、独自の Azure 仮想ネットワーク サブネットを使用するように構成されます。 このサブネットには、Azure リソースと AKS クラスター間を接続するための委任されたアクセス許可があります。 委任されたサブネットがまだない場合、Azure portal によって仮想ノードで使用するための Azure 仮想ネットワークとサブネットが作成および構成されます。

**[Review + create]\(レビュー + 作成\)** を選択します。 検証が完了したら、**[作成]** を選択します。

AKS クラスターを作成して、使用準備が完了するのには数分かかります。

## <a name="connect-to-the-cluster"></a>クラスターへの接続

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 Kubernetes クラスターを管理するには、Kubernetes のコマンドライン クライアントである [kubectl][kubectl] を使います。 `kubectl` クライアントは Azure Cloud Shell に事前にインストールされています。

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

Kubernetes クラスターに接続するように `kubectl` を構成するには、[az aks get-credentials][az-aks-get-credentials] コマンドを実行します。 次の例では、*myResourceGroup* という名前のリソース グループの *myAKSCluster* という名前のクラスターの資格情報を取得します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

クラスターへの接続を確認するには、[kubectl get][kubectl-get] コマンドを使って、クラスター ノードの一覧を取得します。

```azurecli-interactive
kubectl get nodes
```

次の出力例は、単一の VM ノードが作成されてから、Linux 用の仮想ノード、*virtual-node-aci-linux*が作成されることを示しています。

```
$ kubectl get nodes

NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>サンプル アプリのデプロイ

Azure Cloud Shell で、`virtual-node.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。 ノード上のコンテナーをスケジュールするため、[nodeSelector][node-selector] と [toleration][toleration] が定義されます。 これらの設定により、仮想ノードでポッドがスケジュール設定され、機能が正常に有効化されていることを確認できます。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
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
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

[kubectl apply][kubectl-apply] コマンドを使用してアプリケーションを実行します。

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

`-o wide` 引数を指定して [kubectl get pods][kubectl-get] コマンドを使用し、ポッドの一覧とスケジュールされたノードを出力します。 `virtual-node-helloworld` ポッドは `virtual-node-linux` ノードでスケジュールされています。

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

ポッドには、仮想ノードで使用するために委任された Azure 仮想ネットワーク サブネットからの内部 IP アドレスが割り当てられます。

## <a name="test-the-virtual-node-pod"></a>仮想ノード ポッドのテスト

仮想ノードで実行されているポッドをテストするには、Web クライアントでデモ アプリケーションを参照します。 ポッドには内部 IP アドレスが割り当てられているため、AKS クラスターの別のポッドからこの接続をすばやくテストできます。 テスト ポッドを作成し、それにターミナル セッションをアタッチします。

```azurecli-interactive
kubectl run -it --rm virtual-node-test --image=debian
```

`apt-get` を使用して、`curl` をポッドにインストールします。

```azurecli-interactive
apt-get update && apt-get install -y curl
```

次に、`curl` (*http://10.241.0.4* など) を使用して、ポッドのアドレスにアクセスします。 前の `kubectl get pods` コマンドで示された独自の内部 IP アドレスを入力します。

```azurecli-interactive
curl -L http://10.241.0.4
```

次の簡約された出力例に示されているように、デモ アプリケーションが表示されます。

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

`exit` を使用してテスト ポッドへのターミナル セッションを閉じます。 セッションが終了すると、ポッドが削除されます。

## <a name="next-steps"></a>次の手順

この記事では、ポッドを仮想ノードでスケジュールし、プライベートの内部 IP アドレスを割り当てました。 代わりに、サービスのデプロイを作成して、ロード バランサーまたはイングレス コントローラーを使用してトラフィックを自分のポッドにルーティングすることもできます。 詳細は、[AKS での基本的なイングレス コント ローラーの作成][aks-basic-ingress]を参照してください｡

仮想ノードは、AKS のスケーリング ソリューションの 1 つのコンポーネントです。 スケーリング ソリューションの詳細については、次の記事を参照してください。

- [Kubernetes のポッドの水平オートスケーラーを使用する][aks-hpa]
- [Kubernetes クラスター オートスケーラーを使用する][aks-cluster-autoscaler]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: autoscaler.md
[aks-basic-ingress]: ingress-basic.md
