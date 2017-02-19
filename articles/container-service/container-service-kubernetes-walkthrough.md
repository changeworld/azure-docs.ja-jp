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
ms.date: 11/15/2016
ms.author: anhowe
translationtype: Human Translation
ms.sourcegitcommit: 7ed8fb75f057d5a7cfde5436e72e8fec52d07156
ms.openlocfilehash: f3b2fc301bf7083f192c0ec872c4e032472eef97


---

# <a name="microsoft-azure-container-service-engine---kubernetes-walkthrough"></a>Microsoft Azure Container Service エンジン - Kubernetes チュートリアル

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、['azure-cli' コマンド ライン ツール](https://github.com/azure/azure-cli#installation)をインストール済みで、`~/.ssh/id_rsa.pub` に [SSH 公開キー](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md)を作成済みであることを前提としています。

## <a name="overview"></a>概要

以下の手順では、1 つのマスターと&2; つのワーカー ノードを含む Kubernetes クラスターを作成します。
マスターは Kubernetes REST API を提供します。  ワーカー ノードは、Azure 可用性セットにグループ化され、コンテナーを実行します。 すべての VM は同一のプライベート VNET 内にあり、完全な相互アクセスが可能です。

> [!NOTE]
> Azure Container Service での Kubernetes のサポートは、現在はプレビューの段階です。
>

次の図は、1 つのマスターと&2; つのエージェントを含むコンテナー サービス クラスターのアーキテクチャを示します。

![Azure 上の Kubernetes クラスターの図](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="creating-your-kubernetes-cluster"></a>Kubernetes クラスターの作成

### <a name="create-a-resource-group"></a>リソース グループの作成
クラスターを作成するには、まず特定の場所にリソース グループを作成する必要があるため、次のコマンドを実行します。
```console
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>クラスターの作成
リソース グループが用意できたら、次のコマンドを実行して、そのグループ内にクラスターを作成できます。
```console
DNS_PREFIX=some-unique-value
SERVICE_NAME=any-acs-service-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$SERVICE_NAME --dns-prefix=$DNS_PREFIX
```

> [!NOTE]
> azure-cli は `~/.ssh/id_rsa.pub` を Linux VM にアップロードします。
>

このコマンドが完了すると、動作している Kubernetes クラスターが作成されます。

### <a name="configure-kubectl"></a>kubectl を構成する
`kubectl` は、Kubernetes のコマンド ライン クライアントです。  まだインストールしていない場合は、次のコマンドでインストールできます。

```console
az acs kubernetes install-cli
```

`kubectl` がインストールされたら、次のコマンドを実行して、マスターの Kubernetes クラスター構成を ~/.kube/config ファイルにダウンロードします。
```console
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$SERVICE_NAME
```

この時点で、自分のコンピューターからクラスターにアクセスできるようになっているので、次のコマンドを実行してみてください。
```console
kubectl get nodes
```

クラスター内にマシンが表示されることを確認します。

## <a name="create-your-first-kubernetes-service"></a>初めての Kubernetes サービスの作成

このチュートリアルを完了すると、次の方法がわかります。
 * Docker アプリケーションをデプロイして公開する。
 * `kubectl exec` を使用してコンテナーでコマンドを実行する。 
 * Kubernetes ダッシュボードにアクセスする。

### <a name="start-a-simple-container"></a>単純なコンテナーを起動する
次のコマンドを使用して、単純なコンテナー (この場合、`nginx` Web サーバー) を実行できます。

```console
kubectl run nginx --image nginx
```

このコマンドにより、いずれかのノードにあるポッドで nginx Docker コンテナーが開始されます。

次のコマンドを実行すると、
```console
kubectl get pods
```

実行中のコンテナーを参照できます。

### <a name="expose-the-service-to-the-world"></a>サービスを公開する
サービスを公開するには、  タイプが `LoadBalancer` の Kubernetes `Service` を作成します。

```console
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

これにより、Kubernetes がパブリック IP を持つ Azure ロード バランサーを作成します。 変更がロード バランサーに反映されるまでに 2 ～ 3 分かかります。

サービスが "保留中" から外部 IP に変更されたことを確認するには、次のように入力します。
```console
watch 'kubectl get svc'
```

  ![保留中から外部 IP への切り替え確認の画像](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

外部 IP を確認したら、ブラウザーでそれを参照できます。

  ![nginx の参照の画像](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Kubernetes UI を参照する
Kubernetes Web インターフェイスを表示するには、次のコマンドを使用できます。

```console
kubectl proxy
```
このコマンドは、ローカルホスト上で簡単な認証済みのプロキシを実行します。これを使用して、[kubernetes ui](http://localhost:8001/ui) を表示することができます。

![Kubernetes ダッシュボードの画像](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>コンテナー内のリモート セッション
Kubernetes では、クラスターで実行されているリモートの Docker コンテナー内でコマンドを実行することができます。

```console
# Get the name of your nginx pod
kubectl get pods
```

ポッドの名前を使用すると、ポッドに対してリモート コマンドを実行できます。  次に例を示します。
```console
kubectl exec nginx-701339712-retbj date
```

`-it` フラグを使用して、完全な対話型セッションを取得することもできます。

```console
kubectl exec nginx-701339712-retbj -it bash
```

![ポッド IP に対する curl の実行の画像](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)


## <a name="details"></a>詳細
### <a name="installing-the-kubectl-configuration-file"></a>kubectl 構成ファイルのインストール
`az acs kubernetes get-credentials` を実行したとき、ホーム ディレクトリ ~/.kube/config にリモート アクセス用の kube 構成ファイルが保存されました。

直接ダウンロードする必要が生じた場合は、Linux または OS X では `ssh` を、Windows では `Putty` を使用することができます。

#### <a name="windows"></a>Windows
[putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) の pscp を使用するには、  証明書を [pageant](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#key-management-and-agent-forwarding-with-windows-pageant) を通じて公開していることを確認します。
  ```
  # MASTERFQDN is obtained in step1
  pscp azureuser@MASTERFQDN:.kube/config .
  SET KUBECONFIG=%CD%\config
  kubectl get nodes
  ```

#### <a name="os-x-or-linux"></a>OS X または Linux の場合。
  ```
  # MASTERFQDN is obtained in step1
  scp azureuser@MASTERFQDN:.kube/config .
  export KUBECONFIG=`pwd`/config
  kubectl get nodes
  ```
## <a name="learning-more"></a>詳細情報

### <a name="azure-container-service"></a>Azure Container Service

1. [Azure Container Service のドキュメント](https://azure.microsoft.com/en-us/documentation/services/container-service/)
2. [Azure Container Service のオープン ソース エンジン](https://github.com/azure/acs-engine)

### <a name="kubernetes-community-documentation"></a>Kubernetes コミュニティのドキュメント

1. [Kubernetes ブートキャンプ](https://katacoda.com/embed/kubernetes-bootcamp/1/) - コンテナー化されたアプリケーションをデプロイ、スケール、更新、デバッグする方法について説明されています。
2. [Kubernetes ユーザー ガイド](http://kubernetes.io/docs/user-guide/) - 既存の Kubernetes クラスターでのプログラム実行に関する情報が記載されています。
3. [Kubernetes の例](https://github.com/kubernetes/kubernetes/tree/master/examples) - Kubernetes を使って実際のアプリケーションを実行する方法の例が多数記載されています。



<!--HONumber=Jan17_HO4-->


