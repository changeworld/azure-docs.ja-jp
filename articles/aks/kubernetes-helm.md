---
title: AKS で Helm を使用して既存のアプリケーションをインストールする
description: Helm パッケージ化ツールを使用して、Azure Kubernetes Service (AKS) クラスターにコンテナーをデプロイする方法について説明します
services: container-service
author: zr-msft
ms.topic: article
ms.date: 12/07/2020
ms.author: zarhoads
ms.openlocfilehash: c1370182d8ca13acb3d94856df784ecea34252ae
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724716"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での Helm を使用した既存のアプリケーションのインストール

[Helm][helm] は、Kubernetes アプリケーションのライフサイクルをインストールおよび管理するのに役立つオープン ソースのパッケージ化ツールです。 *APT* や *Yum* などの Linux パッケージ マネージャーと同様に、Helm は、構成済みの Kubernetes リソースのパッケージである Kubernetes チャートの管理に使用されます。

この記事では、AKS 上の Kubernetes クラスターに Helm を構成して使用する方法を説明します。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

また、この記事では、ACR が統合された既存の AKS クラスターがあることを前提としています。 ACR が統合された AKS クラスターの作成の詳細については、「[Azure Kubernetes サービスから Azure Container Registry の認証を受ける][aks-integrated-acr]」を参照してください。

Helm CLI もインストールする必要があります。これは、開発システムで実行されるクライアントです。 Helm を使用してアプリケーションを起動、停止、管理することができます。 Azure Cloud Shell を使用している場合、Helm CLI は既にインストールされています。 ローカル プラットフォームでのインストール手順については、[Helm のインストール][helm-install]に関するページを参照してください。

> [!IMPORTANT]
> Helm は Linux ノードで実行するものです。 クラスター内に Windows Server ノードがある場合、Helm ポッドが確実に Linux ノードでのみ実行されるようにスケジュールする必要があります。 また、インストールする Helm チャートが確実に正しいノードで実行されるようにスケジュールする必要もあります。 この記事のコマンドでは、[node-selectors][k8s-node-selector] を使用し、ポッドが正しいノードにスケジュールされるが、一部の Helm チャートではノード セレクターが公開されないようにします。 [taints][taints] などの、他のオプションをクラスターで使用することを検討することもできます。

## <a name="verify-your-version-of-helm"></a>Helm のバージョンを確認する

`helm version` コマンドを使用して、Helm 3 がインストールされていることを確認します。

```console
helm version
```

次の例は、Helm バージョン 3.0.0 がインストールされていることを示しています。

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

## <a name="install-an-application-with-helm-v3"></a>Helm v3 を使用してアプリケーションをインストールする

### <a name="add-helm-repositories"></a>Helm リポジトリの追加

[helm repo][helm-repo-add] コマンドを使用して、*ingress-nginx* リポジトリを追加します。

```console
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

### <a name="find-helm-charts"></a>Helm チャートの検索

Helm チャートは、Kubernetes クラスターにアプリケーションをデプロイするために使用されます。 事前に作成されている Helm チャートを検索するには、[helm search][helm-search] コマンドを使用します。

```console
helm search repo ingress-nginx
```

次の出力例の抜粋は、使用できる一部の Helm チャートを示しています。

```console
$ helm search repo ingress-nginx

NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
ingress-nginx/ingress-nginx     2.12.0          0.34.1          Ingress controller for Kubernetes using NGINX a...
```

チャートの一覧を更新するには、[helm repo update][helm-repo-update] コマンドを使用します。

```console
helm repo update
```

リポジトリ更新の成功例を次に示します。

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ingress-nginx" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="import-the-images-used-by-the-helm-chart-into-your-acr"></a>Helm グラフで使用されるイメージを ACR にインポートする

この記事では、3 つのコンテナー イメージに依存する [NGINX イングレス コントローラー Helm グラフ][ingress-nginx-helm-chart]を使用します。 これらのイメージを ACR にインポートするには、`az acr import` を使用します。

```azurecli
REGISTRY_NAME=<REGISTRY_NAME>
CONTROLLER_REGISTRY=k8s.gcr.io
CONTROLLER_IMAGE=ingress-nginx/controller
CONTROLLER_TAG=v0.48.1
PATCH_REGISTRY=docker.io
PATCH_IMAGE=jettech/kube-webhook-certgen
PATCH_TAG=v1.5.1
DEFAULTBACKEND_REGISTRY=k8s.gcr.io
DEFAULTBACKEND_IMAGE=defaultbackend-amd64
DEFAULTBACKEND_TAG=1.5

az acr import --name $REGISTRY_NAME --source $CONTROLLER_REGISTRY/$CONTROLLER_IMAGE:$CONTROLLER_TAG --image $CONTROLLER_IMAGE:$CONTROLLER_TAG
az acr import --name $REGISTRY_NAME --source $PATCH_REGISTRY/$PATCH_IMAGE:$PATCH_TAG --image $PATCH_IMAGE:$PATCH_TAG
az acr import --name $REGISTRY_NAME --source $DEFAULTBACKEND_REGISTRY/$DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG --image $DEFAULTBACKEND_IMAGE:$DEFAULTBACKEND_TAG
```

> [!NOTE]
> コンテナー イメージを ACR にインポートするだけでなく、Helm グラフを ACR にインポートすることもできます。 詳細については、「[Azure コンテナー レジストリに対する Helm グラフのプッシュおよびプル][acr-helm]」を参照してください。

### <a name="run-helm-charts"></a>Helm チャートの実行

Helm を使用してチャートをインストールするには、[helm install][helm-install-command] コマンドを使用し、リリース名とインストールするチャートの名前を指定します。 Helm チャートのインストールを実際に確かめるために、Helm チャートを使用して基本的な nginx デプロイをインストールしてみましょう。

> [!TIP]
> 次の例では、*ingress-basic* という名前のイングレス リソースの Kubernetes 名前空間が作成され、その名前空間内で動作することを想定しています。 必要に応じて、ご自身の環境の名前空間を指定できます。

```console
ACR_URL=<REGISTRY_URL>

# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.image.registry=$ACR_URL \
    --set controller.image.image=$CONTROLLER_IMAGE \
    --set controller.image.tag=$CONTROLLER_TAG \
     --set controller.image.digest="" \
    --set controller.admissionWebhooks.patch.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.image.registry=$ACR_URL \
    --set controller.admissionWebhooks.patch.image.image=$PATCH_IMAGE \
    --set controller.admissionWebhooks.patch.image.tag=$PATCH_TAG \
    --set defaultBackend.nodeSelector."kubernetes\.io/os"=linux \
    --set defaultBackend.image.registry=$ACR_URL \
    --set defaultBackend.image.image=$DEFAULTBACKEND_IMAGE \
    --set defaultBackend.image.tag=$DEFAULTBACKEND_TAG
```

次の出力例の抜粋は、Helm チャートによって作成された Kubernetes リソースのデプロイ状態を示します。

```console
NAME: nginx-ingress
LAST DEPLOYED: Wed Jul 28 11:35:29 2021
NAMESPACE: ingress-basic
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The ingress-nginx controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller'
...
```

サービスの *EXTERNAL-IP* を取得するには、`kubectl get services` コマンドを使用します。

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

たとえば、次のコマンドは *my-nginx-ingress-ingress-nginx-controller* サービスの *EXTERNAL-IP* を示しています。

```console
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.254.93   <EXTERNAL_IP>   80:30004/TCP,443:30348/TCP   61s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

### <a name="list-releases"></a>リリースを一覧表示する

クラスターにインストールされているリリースの一覧を表示するには、`helm list` コマンドを使用します。

```console
helm list --namespace ingress-basic
```

次の例は、前の手順でデプロイされた *my-nginx-ingress* リリースを示しています。

```console
$ helm list --namespace ingress-basic
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress   ingress-basic   1               2021-07-28 11:35:29.9623734 -0500 CDT   deployed        ingress-nginx-3.34.0    0.47.0
```

### <a name="clean-up-resources"></a>リソースをクリーンアップする

Helm グラフをデプロイすると、多数の Kubernetes リソースが作成されます。 これらのリソースには、ポッド、デプロイ、およびサービスが含まれます。 これらのリソースをクリーンアップするには、[helm uninstall][helm-cleanup] コマンドを使用し、前の `helm list` コマンドで見つかったリリース名を指定します。

```console
helm uninstall --namespace ingress-basic nginx-ingress
```

次の例は、*my-nginx-ingress* という名前のリリースがアンインストールされたことを示しています。

```console
$ helm uninstall --namespace ingress-basic nginx-ingress

release "nginx-ingress" uninstalled
```

サンプルの名前空間全体を削除するには、`kubectl delete` コマンドを使用して、名前空間の名前を指定します。 名前空間内のすべてのリソースが削除されます。

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>次のステップ

Kubernetes アプリケーションのデプロイの管理について詳しくは、Helm のドキュメントを参照してください。

> [!div class="nextstepaction"]
> [Helm のドキュメント][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[ingress-nginx-helm-chart]: https://github.com/kubernetes/ingress-nginx/tree/main/charts/ingress-nginx
            
<!-- LINKS - internal -->
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-integrated-acr]: cluster-container-registry-integration.md?tabs=azure-cli#create-a-new-aks-cluster-with-acr-integration
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[taints]: operator-best-practices-advanced-scheduler.md