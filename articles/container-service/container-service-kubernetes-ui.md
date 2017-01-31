---
title: "Azure Container Service の Kubernetes Web UI を使用する |Microsoft Docs"
description: "Azure Container Service の Kubernetes Web UI を使用する"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: what-goes-here?
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
translationtype: Human Translation
ms.sourcegitcommit: ba0848c14344f137a7032a0098d2c1e07e387960
ms.openlocfilehash: 6ede8e11361c9df2a83c71d97cf47e2306d25371


---

# <a name="microsoft-azure-container-service-engine---using-the-kubernetes-web-ui"></a>Microsoft Azure Container Service エンジン - Kubernetes Web UI を使用する

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、[Azure Container Service を使用して Kubernetes クラスターを作成](container-service-kubernetes-walkthrough.md)したことを想定します。

また、azure cli と kubectl ツールをインストールしていることも想定します。

`az` ツールがインストールされていることを確認するには、次を実行します。

```console
$ az --version
```

`az` ツールをインストールしていないない場合、[ここ](https://github.com/azure/azure-cli#installation)に手順が記載されています。

`kubectl` ツールがインストールされていることを確認するには、次を実行します。

```console
$ kubectl version
```

`kubectl` をインストールしていない場合、次を実行できます。

```console
$ az acs kubernetes install-cli
```

## <a name="overview"></a>概要

### <a name="connect-to-the-web-ui"></a>Web UI に接続する
Kubernetes Web UI を起動するには、次を実行できます。

```console
$ az acs kubernetes browse
```

これにより、お使いのローカル コンピューターと Kubernetes Web UI を接続する Secure プロキシと対話するように構成された Web ブラウザーが開きます。

### <a name="create-and-expose-a-service"></a>サービスを作成および公開する
Kubernetes Web UI では、右上のウィンドウで [作成] ボタンが表示されます。

![Kubernetes [作成] UI](media/k8s/create.png)

これにより、ダイアログ ボックスが開き、アプリケーションの作成を開始できます。
`hello-nginx` と名付けます。 [Docker からの `nginx` コンテナー](https://hub.docker.com/_/nginx/)を使用して、この Web サービスの 3 つのレプリカをデプロイします。

![Kubernetes ポッドの [作成] ダイアログ](media/k8s/nginx.png)

続いて、"外部" Kubernetes サービスを作成して、3 つのレプリカにトラフィックを負荷分散します。  [外部] を選択し、ポート 80 を入力します。

![Kubernetes サービスの [作成] ダイアログ](media/k8s/service.png)

最後に、"デプロイ" ボタンを押して、これらのコンテナーおよびサービスをデプロイします。

![Kubernetes デプロイ](media/k8s/deploy.png)

### <a name="view-your-containers"></a>コンテナーを表示する
"デプロイ" を押した後、UI は、デプロイされているサービスのビューを表示します。

![Kubernetes の状態](media/k8s/status.png)

UI の左側にある円の中で、各 Kubernetes オブジェクトの状態を確認できます。 円が部分的である場合、オブジェクトはデプロイ中であることを示します。 オブジェクトが完全にデプロイされると、緑色のチェック マークが表示されます。

![デプロイされた Kubernetes](media/k8s/deployed.png)

すべてが正常に実行している場合、Pod のいずれかをクリックして、実行中の Web サービスに関する詳細を表示できます

![Kubernetes ポッド](media/k8s/pods.png)

Pod 固有のビューでは、Pod でコンテナーについての情報を確認できます。また、これらのコンテナーで使用される CPU およびメモリ リソースも表示できます。

![Kubernetes リソース](media/k8s/resources.png)

リソースが表示されない場合は、監視データが反映されるまで数分間待つことが必要になる場合があります。

[ログ] リンクをクリックして、コンテナーのログを参照することもできます。

![Kubernetes のログ](media/k8s/logs.png)

### <a name="viewing-your-service"></a>サービスを表示する
コンテナーを実行するほかにも、Kubernetes UI は外部 `Service` を作成しました。これにより、クラスターにあるコンテナーにトラフィックを運ぶロード バランサーがプロビジョニングされます。

左側のナビゲーション ウィンドウで [Services] をクリックして、すべてのサービスを表示できます (通常は 1 つのみ表示されます)

![Kubernetes サービス](media/k8s/service-deployed.png)

そのビューでは、サービスに割り当てられている外部 IP アドレスが表示されます。
その IP アドレスをクリックすると、ロード バランサーの背後で実行している nginx コンテナーが表示されます。

![nginx ビュー](media/k8s/nginx-page.png)

### <a name="resizing-your-service"></a>サービスのサイズを変更する
UI でオブジェクトを表示するだけではなく、Kubernetes API オブジェクトを編集および更新することもできます。

まず、左側のナビゲーション ウィンドウで [デプロイメント] をクリックして、サービスの `Deployment` に移動します。

そのビューが表示されたら、ReplicaSet をクリックし、上部のナビゲーション バーで [編集] ボタンをクリックします。

![Kubernetes 編集](media/k8s/edit.png)

`spec.replicas` フィールドを "2" に編集して [更新] を押します。

これで、Pod のいずれかを削除することにより、レプリカの数を 2 つに下げます。

 




<!--HONumber=Dec16_HO3-->


