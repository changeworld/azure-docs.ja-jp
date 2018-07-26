---
title: Azure Kubernetes Service (AKS) クラスターでイングレスを構成する
description: Azure Kubernetes Service (AKS) クラスターで、自動的に SSL 証明書を生成する Let's Encrypt を使用する NGINX イングレス コントローラーをインストールおよび構成する方法について説明します。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/17/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: c65cfec41c2002fd4d4ff27ea74daf0bb4246b5f
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145599"
---
# <a name="deploy-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) に HTTPS イングレス コントローラーを展開する

イングレス コントローラーは、リバース プロキシ、構成可能なトラフィック ルーティング、および Kubernetes サービスの TLS 終端を提供するソフトウェアです。 個別の Kubernetes サービスのイングレス ルールとルートを構成するには、Kubernetes イングレス リソースが使われます。 イングレス コントローラーとイングレス ルールを使うと、1 つの外部アドレスを使って Kubernetes クラスター内の複数のサービスにトラフィックをルーティングできます。

この記事では、Azure Kubernetes Service (AKS) クラスターに [NGINX イングレス コントローラー][nginx-ingress]を展開する方法について説明します。 [Let's Encrypt][lets-encrypt] 証明書を自動的に生成して構成するには、[cert-manager][cert-manager] プロジェクトを使用します。 最後に、単一のアドレスで個々にアクセスできる複数のアプリケーションを AKS クラスターで実行します。

## <a name="before-you-begin"></a>開始する前に

この記事では、Helm を使用し、NGINX イングレス コントローラー、cert-manager およびサンプル Web アプリをインストールします。 Helm は、AKS クラスター内で初期化され、Tiller 用のサービス アカウントが使用されている必要があります。 Helm の構成および使用方法の詳細については、「[Azure Kubernetes Service (AKS) での Helm を使用したアプリケーションのインストール][use-helm]」を参照してください。

この記事では、Azure CLI バージョン 2.0.41 以降も実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="install-an-ingress-controller"></a>イングレス コントローラーをインストールする

Helm を使って NGINX イングレス コントローラーをインストールします。 展開の詳細については、NGINX イングレス コントローラーの[ドキュメント][nginx-ingress]をご覧ください。

次の例では、`kube-system` 名前空間にコントローラーをインストールします。 ご自身の環境には別の名前空間を指定することができます。 ご利用の AKS クラスターが RBAC 対応でない場合は、コマンドに `--set rbac.create=false` を追加します。

```console
helm install stable/nginx-ingress --namespace kube-system
```

インストールの間に、Azure パブリック IP アドレスがイングレス コントローラーに対して作成されます。 パブリック IP アドレスを取得するには、`kubectl get service` コマンドを使います。 IP アドレスがサービスに割り当てられるまでに、少し時間がかかる場合があります。

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

まだイングレス ルールは作成されていません。 パブリック IP アドレスを参照すると、NGINX イングレス コントローラーの既定の 404 ページが次の例のように表示されます。

![既定の NGINX バックエンド](media/ingress/default-back-end.png)

## <a name="configure-a-dns-name"></a>DNS 名を構成する

HTTPS 証明書が正常に動作するには、イングレス コントローラーの IP アドレスに FQDN を構成します。 次のスクリプトを更新して、イングレス コントローラーの IP アドレスと、FQDN として使用する一意の名前に変更します。

```console
#!/bin/bash

# Public IP address of your ingress controller
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

FQDN 経由でイングレス コントローラーにアクセスできるようになります。

## <a name="install-cert-manager"></a>cert-manager をインストールする

NGINX イングレス コントローラーは、TLS の終端をサポートしています。 HTTPS の証明書を取得および構成するには、いくつかの方法があります。 この記事では、[Lets Encrypt][lets-encrypt] 証明書を自動的に作成および管理する機能を提供する [cert-manager][cert-manager] の使用方法を示します。

> [!NOTE]
> この記事では、Let's Encrypt に `staging` 環境を使用します。 運用環境の展開では、リソースの定義と Helm チャートのインストールに `letsencrypt-prod` と `https://acme-v02.api.letsencrypt.org/directory` を使用します。

RBAC が有効になっているクラスターに cert-manager コントローラーをインストールするには、次の `helm install` コマンドを使用します。

```console
helm install stable/cert-manager --set ingressShim.defaultIssuerName=letsencrypt-staging --set ingressShim.defaultIssuerKind=ClusterIssuer
```

クラスターで RBAC が無効な場合、代わりに次のコマンドを使用します。

```console
helm install stable/cert-manager \
  --set ingressShim.defaultIssuerName=letsencrypt-staging \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
```

cert-manager の構成の詳細については、[cert-manager プロジェクト][cert-manager]についてのページを参照してください。

## <a name="create-a-ca-cluster-issuer"></a>CA クラスター発行者を作成する

証明書を発行する前に、cert-manager には [Issuer][cert-manager-issuer] リソースまたは [ClusterIssuer][cert-manager-cluster-issuer] リソースが必要です。 これらの Kubernetes リソースは機能面では同一ですが、`Issuer` は単一の名前空間で機能し、`ClusterIssuer` はすべての名前空間にわたって機能します。 詳細については、[cert-manager issuer][cert-manager-issuer]についてのページを参照してください。

次のマニフェスト例を使用して、`cluster-issuer.yaml` などのクラスター発行者を作成します。 メール アドレスを実際の組織の有効なアドレスで置き換えてください。

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

発行者を作成するには、`kubectl create -f cluster-issuer.yaml` コマンドを使用します。

```
$ kubectl create -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="create-a-certificate-object"></a>証明書オブジェクトを作成する

次に、証明書リソースを作成する必要があります。 証明書リソースでは、必要な X.509 証明書を定義します。 詳細については、[cert-manager の証明書][cert-manager-certificates]についてのページを参照してください。

次のマニフェスト例を使用して、`certificates.yaml` などのクラスター リソースを作成します。 *dnsNames* と *domains* を前の手順で作成した DNS 名に更新します。

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

証明書のリソースを作成するには、`kubectl create -f certificates.yaml` コマンドを使用します。

```
$ kubectl create -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret created
```

## <a name="run-demo-applications"></a>デモ アプリケーションを実行する

イングレス コントローラーと証明書管理ソリューションを構成しました。 ここでは、AKS クラスターで 2 つのデモ アプリケーションを実行します。 この例では、Helm を使って、単純な 'Hello world' アプリケーションのインスタンスを 2 つ実行します。

サンプルの Helm チャートをインストールする前に、次のように Helm 環境に Azure サンプル リポジトリを追加します。

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

次のコマンドを使用して、Helm チャートから最初のデモ アプリケーションを作成します。

```console
helm install azure-samples/aks-helloworld
```

デモ アプリケーションの 2 番目のインスタンスをインストールします。 2 番目のインスタンスでは、2 つのアプリケーションを見た目で区別できるように、新しいタイトルを指定します。 また、一意サービス名を指定する必要があります。

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>イングレス ルートを作成する

両方のアプリケーションが Kubernetes クラスターで実行されるようになりましたが、構成されているサービスの種類は `ClusterIP` です。 そのため、アプリケーションにはインターネットからアクセスできません。 公開するには、Kubernetes イングレス リソースを作成します。 イングレス リソースでは、2 つのアプリケーションのいずれかにトラフィックをルーティングするルールを構成します。

次の例では、アドレス `https://demo-aks-ingress.eastus.cloudapp.azure.com/` へのトラフィックが `aks-helloworld` という名前のサービスにルーティングされることに注意してください。 アドレス `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` へのトラフィックは、`ingress-demo` サービスにルーティングされます。 *hosts* と *host* を前の手順で作成した DNS 名に更新します。

`hello-world-ingress.yaml` という名前のファイルを作成し、次の例の YAML にコピーします。

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

`kubectl create -f hello-world-ingress.yaml` コマンドを使用してイングレス リソースを作成します。

```
$ kubectl create -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>イングレスの構成をテストする

Web ブラウザーで、*https://demo-aks-ingress.eastus.cloudapp.azure.com* などの Kubernetes イングレス コントローラーの FQDN コントローラーを開きます。

これらの例は、`letsencrypt-staging` を使用するので、発行された SSL 証明書はブラウザーでは信頼されていません。 アプリケーションに続けるために警告のプロンプトを受け入れます。 証明書情報には、この *Fake LE Intermediate X1* 証明書が Let's Encrypt によって発行されていると表示されます。 この偽の証明書は、`cert-manager` が要求を正しく処理し、プロバイダーから証明書を受け取ったことを示します。

![Let's Encrypt ステージング証明書](media/ingress/staging-certificate.png)

Let's Encrypt を変更し、`staging` ではなく `prod` が使用されるように変更した場合、Let's Encrypt によって発行された信頼された証明書が次の例のように使用されます。

![Let's Encrypt の証明書](media/ingress/certificate.png)

Web ブラウザーには、アプリケーションのデモが表示されています。

![アプリケーションの例 1](media/ingress/app-one.png)

ここで *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two* などの */hello-world-two* パスを FQDN に追加します。 カスタム タイトルが表示された 2 番目のデモ アプリケーションが表示されています。

![アプリケーションの例 2](media/ingress/app-two.png)

## <a name="next-steps"></a>次の手順

この記事には、AKS への外部コンポーネントが含まれています。 これらのコンポーネントの詳細については、次のプロジェクト ページを参照してください。

- [Helm CLI][helm-cli]
- [NGINX イングレス コントローラー][nginx-ingress]
- [cert-manager][cert-manager]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
