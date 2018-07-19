---
title: Helm を使用して Azure の Kubernetes にコンテナーをデプロイする
description: Helm パッケージ化ツールを使用して AKS の Kubernetes クラスターにコンテナーをデプロイします
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 531e6d9368b2bf91c48fd41b1e9330879b0df49a
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101839"
---
# <a name="use-helm-with-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での Helm の使用

[Helm][helm] は、Kubernetes アプリケーションのインストールとライフサイクルの管理に役立つオープン ソースのパッケージ化ツールです。 *APT* や *Yum* などの Linux パッケージ マネージャーと同様に、Helm は、構成済みの Kubernetes リソースのパッケージである Kubernetes チャートの管理に使用されます。

このドキュメントでは、AKS の Kubernetes クラスターで Helm を構成して使用する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

このドキュメントで詳しく説明する手順では、AKS クラスターを作成済みで、そのクラスターとの kubectl 接続が確立されていることを想定しています。 これらの項目が必要な場合は、[AKS のクイック スタート][aks-quickstart]を参照してください。

## <a name="install-helm-cli"></a>Helm CLI のインストール

Helm CLI は、開発システムで実行されるクライアントで、Helm を使用してアプリケーションを起動、停止、管理することができます。

Azure CloudShell を使用している場合、Helm CLI は既にインストールされています。 Helm CLI を Mac にインストールするには、`brew` を使用します。 その他のインストール オプションについては、「[Installing Helm (Helm のインストール)][helm-install-options]」をご覧ください。

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

## <a name="create-service-account"></a>サービス アカウントの作成

RBAC が有効になったクラスターに Helm を構成する前に、Tiller サービスのサービス アカウントとロール バインディングが必要になります。 RBAC 対応のクラスターの Helm / Tiller に関する詳細については、「[Tiller, Namespaces, and RBAC][tiller-rbac]」(Tiller、名前空間、および RBAC) を参照してください。 ご利用のクラスターが RBAC に対応していない場合は、この手順をスキップすることに注意してください。

`helm-rbac.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

`kubectl create` コマンドを使って、サービス アカウントとロール バインディングを作成します。

```
kubectl create -f helm-rbac.yaml
```

RBAC 対応のクラスターを使用している場合、Tiller がクラスターに対して保持している複数のオプションが、アクセスのレベルにあります。 「[Helm: ロールベースのアクセス制御][helm-rbac]」を参照してください。

## <a name="configure-helm"></a>Helm の構成

[helm init][helm-init] コマンドを使用して、tiller をインストールします。 ご利用のクラスターが RBAC に対応していない場合は、`--service-account` 引数と値を削除します。

```
helm init --service-account tiller
```

## <a name="find-helm-charts"></a>Helm チャートの検索

Helm チャートは、Kubernetes クラスターにアプリケーションをデプロイするために使用されます。 事前に作成されている Helm チャートを検索するには、[helm search][helm-search] コマンドを使用します。

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

チャートの一覧を更新するには、[helm repo update][helm-repo-update] コマンドを使用します。

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

Helm チャートを使って WordPress をデプロイするには、[helm install][helm-install] コマンドを使用します。

```azurecli-interactive
helm install stable/wordpress
```

出力は次のようになりますが、Kubernetes のデプロイの使用方法など、追加情報が含まれます。

```
NAME:   bilging-ibex
LAST DEPLOYED: Tue Jun  5 14:31:49 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                     READY  STATUS   RESTARTS  AGE
bilging-ibex-mariadb-7557b5474-dmdxn     0/1    Pending  0         1s
bilging-ibex-wordpress-7494c545fb-tskhz  0/1    Pending  0         1s

==> v1/Secret
NAME                    TYPE    DATA  AGE
bilging-ibex-mariadb    Opaque  2     1s
bilging-ibex-wordpress  Opaque  2     1s

==> v1/ConfigMap
NAME                        DATA  AGE
bilging-ibex-mariadb        1     1s
bilging-ibex-mariadb-tests  1     1s

==> v1/PersistentVolumeClaim
NAME                    STATUS   VOLUME   CAPACITY  ACCESS MODES  STORAGECLASS  AGE
bilging-ibex-mariadb    Pending  default  1s
bilging-ibex-wordpress  Pending  default  1s

==> v1/Service
NAME                    TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)                     AGE
bilging-ibex-mariadb    ClusterIP     10.0.76.164   <none>       3306/TCP                    1s
bilging-ibex-wordpress  LoadBalancer  10.0.215.250  <pending>    80:30934/TCP,443:31134/TCP  1s

==> v1beta1/Deployment
NAME                    DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
bilging-ibex-mariadb    1        1        1           0          1s
bilging-ibex-wordpress  1        1        1           0          1s
...
```

## <a name="list-helm-releases"></a>Helm リリースの一覧表示

クラスターにインストールされているリリースの一覧を表示するには、[helm list][helm-list] コマンドを使用します。

```azurecli-interactive
helm list
```

出力:

```
NAME            REVISION    UPDATED                     STATUS      CHART           NAMESPACE
bilging-ibex    1           Tue Jun  5 14:31:49 2018    DEPLOYED    wordpress-1.0.9 default
```

## <a name="next-steps"></a>次の手順

Kubernetes チャートの管理の詳細については、Helm のドキュメントを参照してください。

> [!div class="nextstepaction"]
> [Helm のドキュメント][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/helm/#helm-install
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
