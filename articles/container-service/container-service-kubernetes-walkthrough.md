---
title: "Azure の Kubernetes クラスターのクイック スタート | Microsoft Docs"
description: "Azure Container Service で Kubernetes クラスターをデプロイして使ってみます"
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 5c529ae41b42d276d37e6103305e33ed04694e18
ms.lasthandoff: 04/07/2017

---

# <a name="get-started-with-a-kubernetes-cluster-in-container-service"></a>Container Service で Kubernetes クラスターを使用する


このチュートリアルでは、Azure CLI 2.0 コマンドを使って Azure Container Service に Kubernetes クラスターを作成する方法について説明します。 その後、`kubectl` コマンドライン ツールを使用して、クラスター内のコンテナーの操作を開始します。

次の図は、1 つのマスターと 2 つのエージェントを含むコンテナー サービス クラスターのアーキテクチャを示します。 マスターは Kubernetes REST API を提供します。 エージェント ノードは、Azure 可用性セットにグループ化され、コンテナーを実行します。 すべての VM は同一のプライベート仮想ネットワーク内にあり、完全な相互アクセスが可能です。

![Azure 上の Kubernetes クラスターの図](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="prerequisites"></a>前提条件
このチュートリアルは、[Azure CLI 2.0](/cli/azure/install-az-cli2) のインストールとセットアップが完了していることを前提としています。 

コマンド例は、Linux と macOS でよく利用されている bash シェルから Azure CLI を実行することを想定しています。 Windows クライアントから Azure CLI を実行する場合、コマンド シェルによって、スクリプトとファイルの構文が一部異なる可能性があります。 

## <a name="create-your-kubernetes-cluster"></a>Kubernetes クラスターの作成

Azure CLI 2.0 を使用してクラスターを作成する簡単なシェル コマンドを次に示します。 

### <a name="create-a-resource-group"></a>リソース グループの作成
クラスターを作成するには、まず特定の場所にリソース グループを作成する必要があります。 次のようなコマンドを実行します。

```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>クラスターの作成
リソース グループが用意できたら、そのグループ内にクラスターを作成できます。 次の例では、`--generate-ssh-keys` オプションを使用しています。デプロイに必要な SSH の公開キー ファイルと秘密キー ファイルがまだ既定の `~/.ssh/` ディレクトリに存在しない場合、このオプションにより、それらのファイルが生成されます。 

また、Azure 内の Kubernetes クラスターによって使用される [Azure Active Directory サービス プリンシパル](container-service-kubernetes-service-principal.md)が、このコマンドによって自動的に生成されます。

```azurecli
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$CLUSTER_NAME --dns-prefix=$DNS_PREFIX --generate-ssh-keys
```


数分後、コマンドが完了すると、Kubernetes クラスターが稼働状態となります。

### <a name="connect-to-the-cluster"></a>クラスターへの接続

クライアント コンピューターから Kubernetes クラスターに接続するには、Kubernetes コマンドライン クライアント ([`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/)) を使用します。 

`kubectl` をまだインストールしていない場合は、次のコマンドでインストールできます。

```azurecli
sudo az acs kubernetes install-cli
```
> [!TIP]
> 既定では、`kubectl` バイナリが `/usr/local/bin/kubectl` (Linux または macOS システム) または `C:\Program Files (x86)\kubectl.exe` (Windows) にインストールされます。 別のインストール パスを指定する場合は、`--install-location` パラメーターを使用してください。
>

`kubectl` のインストール後、そのディレクトリのパスを通して (ディレクトリをシステム パスに追加して) ください。 


そのうえで、次のコマンドを実行して、マスターの Kubernetes クラスター構成を `~/.kube/config` ファイルにダウンロードします。

```azurecli
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

`kubectl` をインストールして構成する方法については、[Azure Container Service クラスターへの接続](container-service-connect.md)に関するトピックを参照してください。

この時点で、自分のコンピューターからクラスターにアクセスできます。 次を実行してみてください。

```bash
kubectl get nodes
```

クラスター内にコンピューターの一覧が表示されることを確認します。

## <a name="create-your-first-kubernetes-service"></a>最初の Kubernetes サービスの作成

このチュートリアルを完了すると、次の方法がわかります。
* Docker アプリケーションをデプロイして公開する
* `kubectl exec` を使用してコンテナーでコマンドを実行する 
* Kubernetes ダッシュボードにアクセスする

### <a name="start-a-simple-container"></a>単純なコンテナーを起動する
次のコマンドを使用して、単純なコンテナー (この場合、Nginx Web サーバー) を実行できます。

```bash
kubectl run nginx --image nginx
```

このコマンドにより、いずれかのノードにあるポッドで Nginx Docker コンテナーが開始されます。

実行中のコンテナーを表示するには、次を実行します。

```bash
kubectl get pods
```

### <a name="expose-the-service-to-the-world"></a>サービスを公開する
サービスを公開するには、タイプが `LoadBalancer` の Kubernetes `Service` を作成します。

```bash
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

このコマンドにより、パブリック IP アドレスを持つ Azure Load Balancer ルールが、Kubernetes によって作成されます。 変更がロード バランサーに反映されるまでに数分かかります。 詳細については、「[Azure Container Service の Kubernetes クラスターのコンテナーで負荷を分散する](container-service-kubernetes-load-balancing.md)」を参照してください。

次のコマンドを実行して、サービスが `pending` から変更され、外部 IP アドレスを表示されることを確認します。

```bash
watch 'kubectl get svc'
```

  ![保留中から外部 IP アドレスへの切り替え確認の画像](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

外部 IP アドレスを確認したら、ブラウザーでそれを参照できます。

  ![Nginx の参照の画像](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Kubernetes UI を参照する
Kubernetes Web インターフェイスを表示するには、次のコマンドを使用できます。

```bash
kubectl proxy
```
このコマンドは、localhost 上で簡単な認証済みのプロキシを実行します。これを使用して、[http://localhost:8001/ui](http://localhost:8001/ui) で実行されている Kubernetes Web UI を表示することができます。 詳細については、「[Azure Container Service で Kubernetes Web UI を使用する](container-service-kubernetes-ui.md)」を参照してください。

![Kubernetes ダッシュボードの画像](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>コンテナー内のリモート セッション
Kubernetes では、クラスターで実行されているリモートの Docker コンテナー内でコマンドを実行することができます。

```bash
# Get the name of your nginx pods
kubectl get pods
```

ポッドの名前を使用すると、ポッドに対してリモート コマンドを実行できます。  次に例を示します。

```bash
kubectl exec <pod name> date
```

`-it` フラグを使用して、完全な対話型セッションを取得することもできます。

```bash
kubectl exec <pod name> -it bash
```

![コンテナー内のリモート セッション](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)



## <a name="next-steps"></a>次のステップ

Kubernetes クラスターで他の操作を行うには、次のリソースを参照してください。

* [Kubernetes ブートキャンプ](https://katacoda.com/embed/kubernetes-bootcamp/1/) - コンテナー化されたアプリケーションをデプロイ、スケール、更新、デバッグする方法について説明されています。
* [Kubernetes ユーザー ガイド](http://kubernetes.io/docs/user-guide/) - 既存の Kubernetes クラスターでのプログラム実行に関する情報が記載されています。
* [Kubernetes の例](https://github.com/kubernetes/kubernetes/tree/master/examples) - Kubernetes を使って実際のアプリケーションを実行する方法の例が記載されています。

