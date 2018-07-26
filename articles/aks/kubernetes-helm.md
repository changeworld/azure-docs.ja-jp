---
title: Helm を使用して Azure の Kubernetes にコンテナーをデプロイする
description: Helm パッケージ化ツールを使用して、Azure Kubernetes Service (AKS) クラスターにコンテナーをデプロイする
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: dd2deba25615373765dd3492d03c1ba547c8ba8c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39055136"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での Helm を使用したアプリケーションのインストール

[Helm][helm] は、Kubernetes アプリケーションのインストールとライフサイクルの管理に役立つオープン ソースのパッケージ化ツールです。 *APT* や *Yum* などの Linux パッケージ マネージャーと同様に、Helm は、構成済みの Kubernetes リソースのパッケージである Kubernetes チャートの管理に使用されます。

この記事では、AKS 上の Kubernetes クラスターに Helm を構成して使用する方法を説明します。

## <a name="before-you-begin"></a>開始する前に

このドキュメントで詳しく説明する手順では、AKS クラスターを作成済みで、そのクラスターとの `kubectl` 接続が確立されていることを想定しています。 これらの項目が必要な場合は、[AKS のクイック スタート][aks-quickstart]を参照してください。

## <a name="install-helm-cli"></a>Helm CLI のインストール

Helm CLI は、開発システムで実行されるクライアントで、Helm を使用してアプリケーションを起動、停止、管理することができます。

Azure Cloud Shell を使用している場合、Helm CLI は既にインストールされています。 Helm CLI を Mac にインストールするには、`brew` を使用します。 その他のインストール オプションについては、「[Installing Helm (Helm のインストール)][helm-install-options]」をご覧ください。

```console
brew install kubernetes-helm
```

出力:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.9.1.high_sierra.bottle.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.9.1.high_sierra.bottle.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.9.1: 50 files, 66.2MB
```

## <a name="create-a-service-account"></a>サービス アカウントの作成

RBAC が有効になったクラスターに Helm をデプロイする前に、Tiller サービスのサービス アカウントとロール バインディングが必要になります。 RBAC 対応のクラスターの Helm / Tiller に関する詳細については、「[Tiller, Namespaces, and RBAC][tiller-rbac]」(Tiller、名前空間、および RBAC) を参照してください。 ご利用のクラスターが RBAC に対応していない場合は、この手順をスキップしてください。

`helm-rbac.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。

```yaml
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

```console
kubectl create -f helm-rbac.yaml
```

## <a name="secure-tiller-and-helm"></a>Tiller と Helm をセキュリティで保護する

Helm クライアントと Tiller サービスは TLS/SSL を使用して互いに認証および通信を行います。 この認証方法では、Kubernetes クラスターをセキュリティで保護して、どのサービスをデプロイできるかを確認できます。 セキュリティを強化するために、独自の署名証明書を生成できます。 各 Helm ユーザーは独自のクライアント証明書を受信し、Tiller は証明書が適用された Kubernetes クラスターで初期化されていました。 詳しくは、「[Using TLS/SSL between Helm and Tiller][helm-ssl]」(Helm と Tiller 間での TLS/SSL の使用) を参照してください。

RBAC 対応の Kubernetes クラスターを使用している場合、Tiller のクラスターに対するアクセスのレベルを制御できます。 Tiller のデプロイに使用される Kubernetes 名前空間を定義し、Tiller によるリソースのデプロイに使用できる名前空間を制限できます。 この方法により、さまざまな名前空間の Tiller インスタンスを作成し、デプロイ境界を制限したり、Helm クライアントのユーザーの範囲を特定の名前空間に制限したりできます。 詳しくは、「[Helm role-based access controls][helm-rbac]」(Helm でのロール ベースのアクセス制御) を参照してください。

## <a name="configure-helm"></a>Helm の構成

基本的な Tiller を AKS にデプロイするには、[helm init][helm-init] コマンドを使用します。 ご利用のクラスターが RBAC に対応していない場合は、`--service-account` 引数と値を削除します。 Tiller と Helm 用に TLS または SSL を構成した場合、この基本的な初期化手順はスキップし、次の例に示されているように必要な `--tiller-tls-` を指定します。

```console
helm init --service-account tiller
```

Helm と Tiller 間に TLS または SSL を構成した場合、次の例に示されているように、`--tiller-tls-` パラメーターと独自の証明書の名前を指定します。

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --service-account tiller
```

## <a name="find-helm-charts"></a>Helm チャートの検索

Helm チャートは、Kubernetes クラスターにアプリケーションをデプロイするために使用されます。 事前に作成されている Helm チャートを検索するには、[helm search][helm-search] コマンドを使用します。

```console
helm search
```

次の出力例の抜粋は、使用できる一部の Helm チャートを示しています。

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/acs-engine-autoscaler   2.2.0            2.1.1        Scales worker nodes within agent pools
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

チャートの一覧を更新するには、[helm repo update][helm-repo-update] コマンドを使用します。 リポジトリ更新の成功例を次に示します。

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Helm チャートの実行

Helm を使用してチャートをインストールするには、[helm install][helm-install] コマンドを使用して、インストールするチャートの名前を指定します。 実際にその動作を確かめるために、Helm チャートを使用して基本的な Wordpress デプロイをインストールしてみましょう。 TLS/SSL を構成した場合は、`--tls` パラメーターを追加して Helm クライアント証明書を使用します。

```console
helm install stable/wordpress
```

次の出力例の抜粋は、Helm チャートによって作成された Kubernetes リソースのデプロイ状態を示します。

```
$ helm install stable/wordpress

NAME:   wishful-mastiff
LAST DEPLOYED: Thu Jul 12 15:53:56 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1beta1/Deployment
NAME                       DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
wishful-mastiff-wordpress  1        1        1           0          1s

==> v1beta1/StatefulSet
NAME                     DESIRED  CURRENT  AGE
wishful-mastiff-mariadb  1        1        1s

==> v1/Pod(related)
NAME                                        READY  STATUS   RESTARTS  AGE
wishful-mastiff-wordpress-6f96f8fdf9-q84sz  0/1    Pending  0         1s
wishful-mastiff-mariadb-0                   0/1    Pending  0         1s

==> v1/Secret
NAME                       TYPE    DATA  AGE
wishful-mastiff-mariadb    Opaque  2     2s
wishful-mastiff-wordpress  Opaque  2     2s

==> v1/ConfigMap
NAME                           DATA  AGE
wishful-mastiff-mariadb        1     2s
wishful-mastiff-mariadb-tests  1     2s

==> v1/PersistentVolumeClaim
NAME                       STATUS   VOLUME   CAPACITY  ACCESS MODES  STORAGECLASS  AGE
wishful-mastiff-wordpress  Pending  default  2s

==> v1/Service
NAME                       TYPE          CLUSTER-IP   EXTERNAL-IP  PORT(S)                     AGE
wishful-mastiff-mariadb    ClusterIP     10.1.116.54  <none>       3306/TCP                    2s
wishful-mastiff-wordpress  LoadBalancer  10.1.217.64  <pending>    80:31751/TCP,443:31264/TCP  2s
...
```

Wordpress サービスの *EXTERNAL-IP* アドレスが設定され、Web ブラウザーでアクセスできるようになるまでに 1 分から 2 分かかります。

## <a name="list-helm-releases"></a>Helm リリースの一覧表示

クラスターにインストールされているリリースの一覧を表示するには、[helm list][helm-list] コマンドを使用します。 次の例は、前の手順でデプロイされた Wordpress リリースを示しています。 TLS/SSL を構成した場合は、`--tls` パラメーターを追加して Helm クライアント証明書を使用します。

```console
$ helm list

NAME             REVISION    UPDATED                     STATUS      CHART              NAMESPACE
wishful-mastiff  1           Thu Jul 12 15:53:56 2018    DEPLOYED    wordpress-2.1.3  default
```

## <a name="next-steps"></a>次の手順

Kubernetes アプリケーションのデプロイの管理について詳しくは、Helm のドキュメントを参照してください。

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
[helm-ssl]: https://docs.helm.sh/using_helm/#using-ssl-between-helm-and-tiller

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
