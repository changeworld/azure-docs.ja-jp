---
title: "Helm を使用して Azure の Kubernetes にコンテナーをデプロイする | Microsoft Docs"
description: "Helm パッケージ化ツールを使用して AKS の Kubernetes クラスターにコンテナーをデプロイします"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: de52c2aad0f74b59970234872dfa3e4136929915
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2017
---
# <a name="use-helm-with-azure-container-service-aks"></a>Azure Container Service (AKS) での Helm の使用

[Helm](https://github.com/kubernetes/helm/) は、Kubernetes アプリケーションのライフサイクルをインストールおよび管理するのに役立つオープン ソースのパッケージ化ツールです。 *APT* や *Yum* などの Linux パッケージ マネージャーと同様に、Helm は、構成済みの Kubernetes リソースのパッケージである Kubernetes チャートの管理に使用されます。

このドキュメントでは、AKS の Kubernetes クラスターで Helm を構成して使用する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

このドキュメントで詳しく説明する手順では、AKS クラスターを作成済みで、そのクラスターとの kubectl 接続が確立されていることを想定しています。 これらの項目が必要な場合は、[AKS のクイック スタート](./kubernetes-walkthrough.md)を参照してください。

## <a name="install-helm-cli"></a>Helm CLI のインストール

Helm CLI は、開発システムで実行されるクライアントで、Helm チャートを使用してアプリケーションを起動、停止、管理することができます。

Azure CloudShell を使用している場合、Helm CLI は既にインストールされています。 Helm CLI を Mac にインストールするには、`brew` を使用します。 その他のインストール オプションについては、「[Installing Helm (Helm のインストール)](https://github.com/kubernetes/helm/blob/master/docs/install.md)」を参照してください。

```console
brew install kubernetes-helm
```

出力:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="configure-helm"></a>Helm の構成

Helm コンポーネントを Kubernetes クラスターにインストールし、クライアント側の構成を行うには、[helm init](https://docs.helm.sh/helm/#helm-init) コマンドを使用します。 Helm は AKS クラスターにプレインストールされているため、必要なのはクライアント側の構成のみです。 Helm クライアントを構成するには、次のコマンドを実行します。

```azurecli-interactive
helm init --client-only
```

出力:

```
$HELM_HOME has been configured at /Users/neilpeterson/.helm.
Not installing Tiller due to 'client-only' flag having been set
Happy Helming!
```

## <a name="find-helm-charts"></a>Helm チャートの検索

Helm チャートは、Kubernetes クラスターにアプリケーションをデプロイするために使用されます。 事前に作成されている Helm チャートを検索するには、[helm search](https://docs.helm.sh/helm/#helm-search) コマンドを使用します。

```azurecli-interactive
helm search
```

出力は次のようになりますが、さらに多くのチャートが含まれます。

```
NAME                            VERSION DESCRIPTION
stable/acs-engine-autoscaler    2.0.0   Scales worker nodes within agent pools
stable/artifactory              6.1.0   Universal Repository Manager supporting all maj...
stable/aws-cluster-autoscaler   0.3.1   Scales worker nodes within autoscaling groups.
stable/buildkite                0.2.0   Agent for Buildkite
stable/centrifugo               2.0.0   Centrifugo is a real-time messaging server.
stable/chaoskube                0.5.0   Chaoskube periodically kills random pods in you...
stable/chronograf               0.3.0   Open-source web application written in Go and R...
stable/cluster-autoscaler       0.2.0   Scales worker nodes within autoscaling groups.
stable/cockroachdb              0.5.0   CockroachDB is a scalable, survivable, strongly...
stable/concourse                0.7.0   Concourse is a simple and scalable CI system.
stable/consul                   0.4.1   Highly available and distributed service discov...
stable/coredns                  0.5.0   CoreDNS is a DNS server that chains middleware ...
stable/coscale                  0.2.0   CoScale Agent
stable/dask-distributed         2.0.0   Distributed computation in Python
stable/datadog                  0.8.0   DataDog Agent
...
```

チャートの一覧を更新するには、[helm repo update](https://docs.helm.sh/helm/#helm-repo-update) コマンドを使用します。

```azurecli-interactive
helm repo update
```

出力:

```
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Helm チャートの実行

NGINX イングレス コントローラーをデプロイするには、[helm install](https://docs.helm.sh/helm/#helm-install) コマンドを使用します。

```azurecli-interactive
helm install stable/nginx-ingress
```

出力は次のようになりますが、Kubernetes のデプロイの使用方法など、追加情報が含まれます。

```
NAME:   tufted-ocelot
LAST DEPLOYED: Thu Oct  5 00:48:04 2017
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                    DATA  AGE
tufted-ocelot-nginx-ingress-controller  1     5s

==> v1/Service
NAME                                         CLUSTER-IP   EXTERNAL-IP  PORT(S)                     AGE
tufted-ocelot-nginx-ingress-controller       10.0.140.10  <pending>    80:30486/TCP,443:31358/TCP  5s
tufted-ocelot-nginx-ingress-default-backend  10.0.34.132  <none>       80/TCP                      5s

==> v1beta1/Deployment
NAME                                         DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
tufted-ocelot-nginx-ingress-controller       1        1        1           0          5s
tufted-ocelot-nginx-ingress-default-backend  1        1        1           1          5s
...
```

Kubernetes での NGINX イングレス コントローラーの使用方法の詳細については、[NGINX イングレス コントローラー](https://github.com/kubernetes/ingress/tree/master/controllers/nginx)に関するページを参照してください。

## <a name="list-helm-charts"></a>Helm チャートの一覧表示

クラスターにインストールされているチャートの一覧を表示するには、[helm list](https://docs.helm.sh/helm/#helm-list) コマンドを使用します。

```azurecli-interactive
helm list
```

出力:

```
NAME            REVISION    UPDATED                     STATUS      CHART               NAMESPACE
bilging-ant     1           Thu Oct  5 00:11:11 2017    DEPLOYED    nginx-ingress-0.8.7 default
```

## <a name="next-steps"></a>次のステップ

Kubernetes チャートの管理の詳細については、Helm のドキュメントを参照してください。

> [!div class="nextstepaction"]
> [Helm のドキュメント](https://github.com/kubernetes/helm/blob/master/docs/index.md)