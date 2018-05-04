---
title: Azure Container Service (AKS) クラスターでイングレスを構成する
description: Azure Container Service (AKS) クラスターに NGINX イングレス コントローラーをインストールして構成します。
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/03/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: dbb37c6fc2b5db8b2799eaacbfb4864c4e04fee7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
# <a name="https-ingress-on-azure-container-service-aks"></a>Azure Container Service (AKS) での HTTPS イングレス

イングレス コントローラーは、リバース プロキシ、構成可能なトラフィック ルーティング、および Kubernetes サービスの TLS 終端を提供するソフトウェアです。 個別の Kubernetes サービスのイングレス ルールとルートを構成するには、Kubernetes イングレス リソースが使われます。 イングレス コントローラーとイングレス ルールを使うと、1 つの外部アドレスを使って Kubernetes クラスター内の複数のサービスにトラフィックをルーティングできます。

このドキュメントでは、Azure Container Service (AKS) クラスターへの [NGINX イングレス コントローラー][nginx-ingress]のサンプル展開の手順を説明します。 さらに、[KUBE-LEGO][kube-lego] プロジェクトを使って、[Let's Encrypt][lets-encrypt] 証明書を自動的に生成して構成します。 最後に、単一のアドレスで個々にアクセスできる複数のアプリケーションを AKS クラスターで実行します。

## <a name="prerequisite"></a>前提条件

Helm CLI のインストール - インストール手順については、Helm CLI の[ドキュメント][helm-cli]を参照してください。

## <a name="install-an-ingress-controller"></a>イングレス コントローラーをインストールする

Helm を使って NGINX イングレス コントローラーをインストールします。 展開の詳細については、NGINX イングレス コントローラーの[ドキュメント][nginx-ingress]をご覧ください。

グラフのリポジトリを更新します。

```console
helm repo update
```

NGINX イングレス コントローラーをインストールします。 この例では、`kube-system` 名前空間にコント ローラーをインストールします。このインストール先は、任意の名前空間を変更できます。

```
helm install stable/nginx-ingress --namespace kube-system
```

インストールの間に、Azure パブリック IP アドレスがイングレス コントローラーに対して作成されます。 パブリック IP アドレスを取得するには、kubectl get service コマンドを使います。 IP アドレスがサービスに割り当てられるまで、少し時間がかかる場合があります。

```console
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   13.82.238.45   80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>         80/TCP                       20m
```

イングレス ルールは作成されていないため、パブリック IP アドレスを参照すると、NGINX イングレス コントローラーの既定の 404 ページにルーティングされます。

![既定の NGINX バックエンド](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>DNS 名を構成する

HTTPS 証明書を使うため、イングレス コントローラーの IP アドレスに FQDN 名を構成する必要があります。 この例では、Azure CLI で Azure FQDN を作成します。 イングレス コントローラーの IP アドレスと、FQDN で使う名前で、スクリプトを更新してください。

```
#!/bin/bash

# Public IP address
IP="52.224.125.195"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get resource group and public ip name
RESOURCEGROUP=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[resourceGroup]" --output tsv)
PIPNAME=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[name]" --output tsv)

# Update public ip address with dns name
az network public-ip update --resource-group $RESOURCEGROUP --name  $PIPNAME --dns-name $DNSNAME
```

FQDN 経由でイングレス コントローラーにアクセスできるようになります。

## <a name="install-kube-lego"></a>KUBE-LEGO をインストールする

NGINX イングレス コントローラーは、TLS の終端をサポートしています。 HTTPS の証明書を取得して構成する方法はいくつもありますが、このドキュメントでは、[KUBE-LEGO][kube-lego] を使う方法を示します。この方法では、[Lets Encrypt][lets-encrypt] 証明書の自動生成と管理機能が提供されます。

KUBE-LEGO コントローラーをインストールするには、次の helm install コマンドを使います。 メール アドレスを実際の組織のものに置き換えてください。

```
helm install stable/kube-lego \
  --set config.LEGO_EMAIL=user@contoso.com \
  --set config.LEGO_URL=https://acme-v01.api.letsencrypt.org/directory
```

KUBE-LEGO の構成について詳しくは、[KUBE-LEGO のドキュメント][kube-lego]をご覧ください。

## <a name="run-application"></a>アプリケーションを実行する

この時点で、イングレス コントローラーと証明書管理ソリューションは構成されています。 AKS クラスターでアプリケーションをいくつか実行してみます。

この例では、Helm を使って、単純な hello world アプリケーションの複数のインスタンスを実行します。

アプリケーションを実行する前に、開発システムに Azure サンプルの Helm リポジトリを追加します。

```
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

 次のコマンドで AKS hello world グラフを実行します。

```
helm install azure-samples/aks-helloworld
```

hello world アプリケーションの 2 番目のインスタンスをインストールします。

2 番目のインスタンスでは、2 つのアプリケーションを見た目で区別できるように、新しいタイトルを指定します。 また、一意サービス名を指定する必要があります。 これらの構成は、次のコマンドで確認できます。

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>イングレス ルートを作成する

両方のアプリケーションが Kubernetes クラスターで実行されるようになりましたが、構成されているサービスの種類は `ClusterIP` です。 そのため、アプリケーションにはインターネットからアクセスできません。 アクセスできるようにするため、Kubernetes イングレス リソースを作成します。 イングレス リソースでは、2 つのアプリケーションのいずれかにトラフィックをルーティングするルールを構成します。

`hello-world-ingress.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。

アドレス `https://demo-aks-ingress.eastus.cloudapp.azure.com/` へのトラフィックが `aks-helloworld` という名前のサービスにルーティングされることに注意してください。 アドレス `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` へのトラフィックは、`ingress-demo` サービスにルーティングされます。

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/tls-acme: "true"
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
- [KUBE-LEGO][kube-lego]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[kube-lego]: https://github.com/jetstack/kube-lego
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
