---
title: "Windows 用 Azure Kubernetes クラスター | Microsoft Docs"
description: "Azure Container Service で Windows コンテナー用 Kubernetes クラスターをデプロイして使ってみます"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 4e730b65a98af05ea00c5f8ebd9914e3367b66a7
ms.contentlocale: ja-jp
ms.lasthandoff: 05/09/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>Container Service で Kubernetes と Windows コンテナーを使用する


この記事では、Windows ノードを含む Azure Container Service で Kubernetes クラスターを作成し、Windows コンテナーを実行する方法について説明します。 最初に Azure CLI 2.0 コマンドの `az acs` を使って Azure Container Service に Kubernetes クラスターを作成します。 その後、Kubernetes の `kubectl` コマンド ライン ツールで、Docker イメージから構築された Windows コンテナーを操作します。 

> [!NOTE]
> Azure Container Service における Kubernetes での Windows コンテナーのサポートはプレビュー段階です。 
>



次の図は、1 つの Linux マスター ノードと 2 つの Windows エージェント ノードを含む、Azure Container Service の Kubernetes クラスターのアーキテクチャを示しています。 

![Azure 上の Kubernetes クラスターの図](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

* Linux マスターは Kubernetes REST API を提供し、ポート 22 の SSH またはポート 443 の `kubectl` でアクセスできます。 
* Windows エージェント ノードは、Azure 可用性セットにグループ化され、コンテナーを実行します。 Windows ノードには、マスター ノードを介して RDP SSH トンネル経由でアクセスできます。 Azure Load Balancer ルールは、公開されているサービスに応じてクラスターに動的に追加されます。



すべての VM は同一のプライベート仮想ネットワーク内にあり、完全な相互アクセスが可能です。 すべての VM で、kubelet、Docker、およびプロキシが実行されます。

詳しい背景については、[Azure Container Service の概要](container-service-intro.md)に関するページと [Kubernetes のドキュメント](https://kubernetes.io/docs/home/)を参照してください。

## <a name="prerequisites"></a>前提条件
Azure CLI 2.0 を使用して Azure Container Service クラスターを作成するには、以下のものが必要です。
* Azure アカウント ([無料試用版はこちら](https://azure.microsoft.com/pricing/free-trial/))
* [Azure CLI 2.0](/cli/azure/install-az-cli2) のインストールとログイン

また、Kubernetes クラスターには以下の情報が必要となります。 これらは事前に準備しておくか、クラスターのデプロイ時に `az acs create` コマンド オプションを使って自動的に生成してください。 

* **SSH RSA 公開キー**: Secure Shell (SSH) RSA キーを作成する場合は、[macOS と Linux](../virtual-machines/linux/mac-create-ssh-keys.md) のガイダンスまたは [Windows](../virtual-machines/linux/ssh-from-windows.md) のガイダンスを参照してください。 

* **サービス プリンシパル クライアント ID とシークレット**: Azure Active Directory サービス プリンシパルの作成手順とさらに詳しい情報については、[Kubernetes クラスターのサービス プリンシパル](container-service-kubernetes-service-principal.md)に関するページを参照してください。

この記事のコマンド例では、SSH キーとサービス プリンシパルを自動的に生成します。
  
## <a name="create-your-kubernetes-cluster"></a>Kubernetes クラスターの作成

クラスターを作成するための Azure CLI 2.0 コマンドは以下のとおりです。 

### <a name="create-a-resource-group"></a>リソース グループの作成
Azure Container Service が[利用できる場所](https://azure.microsoft.com/regions/services/)にリソース グループを作成します。 次のコマンドは、*westus* に *myKubernetesResourceGroup* という名前のリソース グループを作成します。

```azurecli
az group create --name=myKubernetesResourceGroup --location=westus
```

### <a name="create-a-kubernetes-cluster-with-windows-agent-nodes"></a>Windows エージェント ノードから成る Kubernetes クラスターの作成

`az acs create` コマンドに `--orchestrator-type=kubernetes` と `--windows` エージェント オプションを指定して、Kubernetes クラスターをリソース グループに作成します。 コマンド構文については、`az acs create` の[ヘルプ](/cli/azure/acs#create)を参照してください。

以下のコマンドでは、*myKubernetesClusterName* という名前の Container Service クラスターを作成します。管理ノードには DNS プレフィックスとして *myPrefix* を使用し、各 Windows ノードにアクセスするための資格情報を指定しています。 このバージョンのコマンドでは、Kubernetes クラスターのサービス プリンシパルと SSH RSA キーが自動的に生成されます。


```azurecli
az acs create --orchestrator-type=kubernetes \
    --resource-group myKubernetesResourceGroup \
    --name=myKubernetesClusterName \
    --dns-prefix=myPrefix \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username myWindowsAdminName \
    --admin-password myWindowsAdminPassword
```

数分後、コマンドが完了すると、Kubernetes クラスターが稼働状態となります。

> [!IMPORTANT]
> ご利用のアカウントに Azure AD サービス プリンシパルを作成するためのアクセス許可がない場合、このコマンドを実行すると、"`Insufficient privileges to complete the operation.`" というエラーが発生します。詳細については、[Kubernetes クラスターのサービス プリンシパル](container-service-kubernetes-service-principal.md)に関するページを参照してください。 
> 

## <a name="connect-to-the-cluster-with-kubectl"></a>kubectl によるクラスターへの接続

クライアント コンピューターから Kubernetes クラスターに接続するには、Kubernetes コマンドライン クライアント ([`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/)) を使用します。 

`kubectl` をまだローカルにインストールしていない場合は、`az acs kubernetes install-cli` でインストールできます  ([Kubernetes サイト](https://kubernetes.io/docs/tasks/kubectl/install/)からダウンロードすることもできます)。

**Linux または macOS**

```azurecli
sudo az acs kubernetes install-cli
```

**Windows**
```azurecli
az acs kubernetes install-cli
```

> [!TIP]
> 既定では、`kubectl` バイナリが `/usr/local/bin/kubectl` (Linux または macOS システム) または `C:\Program Files (x86)\kubectl.exe` (Windows) にインストールされます。 別のインストール パスを指定する場合は、`--install-location` パラメーターを使用してください。
>
> `kubectl` のインストール後、そのディレクトリのパスを通して (ディレクトリをシステム パスに追加して) ください。 


そのうえで、次のコマンドを実行して、マスターの Kubernetes クラスター構成をローカル `~/.kube/config` ファイルにダウンロードします。

```azurecli
az acs kubernetes get-credentials --resource-group=myKubernetesResourceGroup --name=myKubernetesClusterName
```

この時点で、自分のコンピューターからクラスターにアクセスできます。 次を実行してみてください。

```bash
kubectl get nodes
```

クラスター内にコンピューターの一覧が表示されることを確認します。

![Kubernetes クラスターで実行されているノード](media/container-service-kubernetes-windows-walkthrough/kubectl-get-nodes.png)

## <a name="create-your-first-kubernetes-service"></a>最初の Kubernetes サービスの作成

クラスターを作成し、`kubectl` で接続した後、Docker コンテナーから Windows アプリを起動し、インターネットに公開してみましょう。 この基本的な例では、JSON ファイルを使って Microsoft Internet Information Server (IIS) コンテナーを指定し、`kubctl apply` を使って作成します。 

1. `iis.json` という名前のローカル ファイルを作成し、以下の内容をコピーします。 このファイルによって、[Docker Hub](https://hub.docker.com/r/microsoft/iis/) からのパブリック イメージを使用し、Windows Server 2016 Server Core で IIS を実行するよう Kubernetes に伝えられます。 このコンテナーにはポート 80 が使用されていますが、初期状態ではクラスター ネットワーク内からしかアクセスできません。

  ```JSON
  {
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
      "name": "iis",
      "labels": {
        "name": "iis"
      }
    },
    "spec": {
      "containers": [
        {
          "name": "iis",
          "image": "microsoft/iis",
          "ports": [
            {
            "containerPort": 80
            }
          ]
        }
      ],
      "nodeSelector": {
        "beta.kubernetes.io/os": "windows"
      }
    }
  }
  ```
2. アプリケーションを起動するには、次のように入力します。  
  
  ```bash
  kubectl apply -f iis.json
  ```  
3. コンテナーのデプロイを追跡するには、次のように入力します。  
  ```bash
  kubectl get pods
  ```
  コンテナーのデプロイ中は、状態が `ContainerCreating` になります。 

  ![ContainerCreating 状態の IIS コンテナー](media/container-service-kubernetes-windows-walkthrough/iis-pod-creating.png)   

  IIS イメージのサイズ上、コンテナーが `Running` 状態になるまでに数分かかる場合があります。

  ![Running 状態の IIS コンテナー](media/container-service-kubernetes-windows-walkthrough/iis-pod-running.png)

4. このコンテナーを外部に公開するには、次のコマンドを入力します。

  ```bash
  kubectl expose pods iis --port=80 --type=LoadBalancer
  ```

  このコマンドにより、パブリック IP アドレスを持つ Azure Load Balancer ルールが、Kubernetes によって作成されます。 変更がロード バランサーに反映されるまでに数分かかります。 詳細については、「[Azure Container Service の Kubernetes クラスターのコンテナーで負荷を分散する](container-service-kubernetes-load-balancing.md)」を参照してください。

5. 次のコマンドを実行して、サービスの状態を確認します。

  ```bash
  kubectl get svc
  ```

  初期状態では IP アドレスが `pending` として表示されます。

  ![保留状態の外部 IP アドレス](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose.png)

  数分後、IP アドレスが次のように設定されます。
  
  ![IIS の外部 IP アドレス](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose-public.png)


6. 外部 IP アドレスが利用できる状態になったら、そのアドレスにブラウザーでアクセスすることができます。

  ![IIS にブラウザーでアクセスしたところ](media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  

7. IIS ポッドを削除するには、次のように入力します。

  ```bash
  kubectl delete pods iis
  ```

## <a name="next-steps"></a>次のステップ

* Kubernetes UI を使用するには、`kubectl proxy` コマンドを実行します。 その後、ブラウザーで http://localhost:8001/ui にアクセスします。

* カスタム IIS Web サイトを構築して Windows コンテナーで実行する手順については、[Docker Hub](https://hub.docker.com/r/microsoft/iis/) のガイダンスを参照してください。

* PuTTy を使用し、マスター ノードへの RDP SSH トンネルを介して Windows ノードにアクセスする方法については、[ACS-Engine のドキュメント](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#create-port-80-tunnel-to-the-master)を参照してください。 

