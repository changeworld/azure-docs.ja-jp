---
title: Azure Kubernetes Service (AKS) クラスターでイングレスを構成する
description: Azure Kubernetes Service (AKS) クラスターに NGINX イングレス コントローラーをインストールして構成します。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/25/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 9bee80ebbaf0568706428d673d584819b1daa143
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096990"
---
# <a name="https-ingress-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での HTTPS イングレス

イングレス コントローラーは、リバース プロキシ、構成可能なトラフィック ルーティング、および Kubernetes サービスの TLS 終端を提供するソフトウェアです。 個別の Kubernetes サービスのイングレス ルールとルートを構成するには、Kubernetes イングレス リソースが使われます。 イングレス コントローラーとイングレス ルールを使うと、1 つの外部アドレスを使って Kubernetes クラスター内の複数のサービスにトラフィックをルーティングできます。

このドキュメントでは、Azure Kubernetes Service (AKS) クラスターへの [NGINX イングレス コントローラー][nginx-ingress]のサンプル展開の手順を説明します。 さらに、[cert-manager][cert-manager] プロジェクトを使って、[Let's Encrypt][lets-encrypt] 証明書を自動的に生成して構成します。 最後に、単一のアドレスで個々にアクセスできる複数のアプリケーションを AKS クラスターで実行します。

## <a name="install-an-ingress-controller"></a>イングレス コントローラーをインストールする

Helm を使って NGINX イングレス コントローラーをインストールします。 展開の詳細については、NGINX イングレス コントローラーの[ドキュメント][nginx-ingress]をご覧ください。

この例では、`kube-system` 名前空間にコント ローラーをインストールします。このインストール先は、任意の名前空間を変更できます。 ご利用の AKS クラスターが RBAC 対応でない場合は、コマンドに `--set rbac.create=false` を追加します。 詳細については、[nginx-ingress の表][nginx-ingress]を参照してください。

```bash
helm install stable/nginx-ingress --namespace kube-system
```

インストールの間に、Azure パブリック IP アドレスがイングレス コントローラーに対して作成されます。 パブリック IP アドレスを取得するには、kubectl get service コマンドを使います。 IP アドレスがサービスに割り当てられるまで、少し時間がかかる場合があります。

```console
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

イングレス ルールは作成されていないため、パブリック IP アドレスを参照すると、NGINX イングレス コントローラーの既定の 404 ページにルーティングされます。

![既定の NGINX バックエンド](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>DNS 名を構成する

HTTPS 証明書を使うため、イングレス コントローラーの IP アドレスに FQDN 名を構成する必要があります。 この例では、Azure CLI で Azure FQDN を作成します。 イングレス コントローラーの IP アドレスと、FQDN で使う名前で、スクリプトを更新してください。

```bash
#!/bin/bash

# Public IP address
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with dns name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

FQDN 経由でイングレス コントローラーにアクセスできるようになります。

## <a name="install-cert-manager"></a>cert-manager をインストールする

NGINX イングレス コントローラーは、TLS の終端をサポートしています。 HTTPS の証明書を取得して構成する方法はいくつもありますが、このドキュメントでは、[cert-manager][cert-manager] を使う方法を示します。この方法では、[Lets Encrypt][lets-encrypt] 証明書の自動生成と管理機能が提供されます。

cert-manager コントローラーをインストールするには、次の Helm install コマンドを使います。

```bash
helm install stable/cert-manager --set ingressShim.defaultIssuerName=letsencrypt-prod --set ingressShim.defaultIssuerKind=ClusterIssuer
```

ご利用のクラスターが RBAC 対応でない場合は、このコマンドを使用します。

```bash
helm install stable/cert-manager \
  --set ingressShim.defaultIssuerName=letsencrypt-prod \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
```

cert-manager の構成の詳細については、[cert-manager プロジェクト][cert-manager]についてのページを参照してください。

## <a name="create-ca-cluster-issuer"></a>CA クラスター発行者を作成する

証明書を発行する前に、cert-manager には [Issuer][cert-manager-issuer] リソースまたは [ClusterIssuer][cert-manager-cluster-issuer] リソースが必要です。 これらのリソースは機能面では同一ですが、`Issuer` は単一の名前空間で機能し、`ClusterIssuer` はすべての名前空間にわたって機能します。 詳細については、[cert-manager issuer][cert-manager-issuer]についてのページを参照してください。

次のマニフェストを使用してクラスター発行者を作成します。 メール アドレスを実際の組織の有効なアドレスで置き換えてください。

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-prod
    http01: {}
```

## <a name="create-certificate-object"></a>証明書オブジェクトを作成する

次に、証明書リソースを作成する必要があります。 証明書リソースでは、必要な X.509 証明書を定義します。 詳細については、[cert-manager の証明書][cert-manager-certificates]についてのページを参照してください。

次のマニフェストを使用して証明書リソースを作成します。

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
    name: letsencrypt-prod
    kind: ClusterIssuer
```

## <a name="run-application"></a>アプリケーションを実行する

この時点で、イングレス コントローラーと証明書管理ソリューションは構成されています。 AKS クラスターでアプリケーションをいくつか実行してみます。

この例では、Helm を使って、単純な hello world アプリケーションの複数のインスタンスを実行します。

アプリケーションを実行する前に、開発システムに Azure サンプルの Helm リポジトリを追加します。

```bash
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

次のコマンドで AKS hello world グラフを実行します。

```bash
helm install azure-samples/aks-helloworld
```

hello world アプリケーションの 2 番目のインスタンスをインストールします。

2 番目のインスタンスでは、2 つのアプリケーションを見た目で区別できるように、新しいタイトルを指定します。 また、一意サービス名を指定する必要があります。 これらの構成は、次のコマンドで確認できます。

```bash
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>イングレス ルートを作成する

両方のアプリケーションが Kubernetes クラスターで実行されるようになりましたが、構成されているサービスの種類は `ClusterIP` です。 そのため、アプリケーションにはインターネットからアクセスできません。 アクセスできるようにするため、Kubernetes イングレス リソースを作成します。 イングレス リソースでは、2 つのアプリケーションのいずれかにトラフィックをルーティングするルールを構成します。

`hello-world-ingress.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。

アドレス `https://demo-aks-ingress.eastus.cloudapp.azure.com/` へのトラフィックが `aks-helloworld` という名前のサービスにルーティングされることに注意してください。 アドレス `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` へのトラフィックは、`ingress-demo` サービスにルーティングされます。

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-prod
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

`kubectl apply` コマンドでイングレス リソースを作成します。

```console
kubectl apply -f hello-world-ingress.yaml
```

## <a name="test-the-ingress-configuration"></a>イングレスの構成をテストする

Kubernetes イングレス コントローラーの FQDN を参照すると、hello world アプリケーションが表示されるはずです。

![アプリケーションの例 1](media/ingress/app-one.png)

`/hello-world-two` パスでイングレス コントローラーの FQDN を参照すると、カスタム タイトルの hello world アプリケーションが表示されます。

![アプリケーションの例 2](media/ingress/app-two.png)

接続が暗号化されていること、および Let's Encrypt によって発行された証明書が使われていることに注意してください。

![Lets encrypt の証明書](media/ingress/certificate.png)

## <a name="next-steps"></a>次の手順

このドキュメントで示されているソフトウェアについてさらに学習してください。

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
