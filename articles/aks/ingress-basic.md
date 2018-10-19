---
title: Azure Kubernetes Service (AKS) での基本的なイングレス コントローラーの作成
description: Azure Kubernetes Service (AKS) クラスターで基本的な NGINX イングレス コントローラーをインストールして構成する方法について説明します。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/30/2018
ms.author: iainfou
ms.openlocfilehash: 3ae7a3193e0a4bacc64524f477b6c179ead20b6b
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2018
ms.locfileid: "44355997"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でイングレス コントローラーを作成する

イングレス コントローラーは、リバース プロキシ、構成可能なトラフィック ルーティング、および Kubernetes サービスの TLS 終端を提供するソフトウェアです。 個別の Kubernetes サービスのイングレス ルールとルートを構成するには、Kubernetes イングレス リソースが使われます。 イングレス コントローラーとイングレス ルールを使用すれば、1 つの IP アドレスで Kubernetes クラスター内の複数のサービスにトラフィックをルーティングできます。

この記事では、Azure Kubernetes Service (AKS) クラスターに [NGINX イングレス コントローラー][nginx-ingress]を展開する方法について説明します。 続いて、それぞれが 1 つの IP アドレスでアクセスできる、2 つのアプリケーションを AKS クラスターで実行します。

さらに、以下を実行できます。

- [HTTP アプリケーションのルーティング アドオンを有効にする][aks-http-app-routing]
- [内部のプライベート ネットワークと IP アドレスを使用するイングレス コントローラーを作成する][aks-ingress-internal]
- [動的パブリック IP アドレスを使用してイングレス コントローラーを作成し、Let's Encrypt を構成して TLS 証明書を自動的に生成する][aks-ingress-tls]
- [静的パブリック IP アドレスを使用してイングレス コントローラーを作成し、Let's Encrypt を構成して TLS 証明書を自動的に生成する][aks-ingress-static-tls]

## <a name="before-you-begin"></a>開始する前に

この記事では、Helm を使用し、NGINX イングレス コントローラー、cert-manager およびサンプル Web アプリをインストールします。 Helm は、AKS クラスター内で初期化され、Tiller 用のサービス アカウントが使用されている必要があります。 Helm の構成および使用方法の詳細については、「[Azure Kubernetes Service (AKS) での Helm を使用したアプリケーションのインストール][use-helm]」を参照してください。

この記事では、Azure CLI バージョン 2.0.41 以降も実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="create-an-ingress-controller"></a>イングレス コントローラーを作成する

イングレス コントローラーを作成するには、`Helm` を使用して *nginx-ingress* をインストールします。

> [!TIP]
> 次の例では、`kube-system` の名前空間にイングレス コントローラーをインストールします。 必要に応じて、ご利用の環境に別の名前空間を指定できます。 AKS クラスターが RBAC 対応でない場合は、コマンドに `--set rbac.create=false` を追加します。

```console
helm install stable/nginx-ingress --namespace kube-system
```

次の出力例に示すように、NGINX イングレス コントローラーに対して Kubernetes ロード バランサー サービスが作成されると、動的パブリック IP アドレスが割り当てられます。

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                         TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
masked-otter-nginx-ingress-controller        LoadBalancer   10.0.92.99    40.117.74.8   80:31077/TCP,443:32592/TCP   7m
masked-otter-nginx-ingress-default-backend   ClusterIP      10.0.46.106   <none>        80/TCP                       7m
```

イングレス ルールはまだ作成されていないため、内部 IP アドレスを参照すると、NGINX イングレス コントローラーの既定の 404 ページが表示されます。 イングレス ルールは、後続の手順で構成します。

## <a name="run-demo-applications"></a>デモ アプリケーションを実行する

稼働中のイングレス コントローラーを確認するには、AKS クラスターで 2 つのデモ アプリケーションを実行してください。 この例では、Helm を使って、単純な 'Hello world' アプリケーションのインスタンスを 2 つ実行します。

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

両方のアプリケーションは、Kubernetes クラスターで実行するようになります。 各アプリケーションにトラフィックをルーティングするには、Kubernetes イングレス リソースを作成します。 イングレス リソースでは、2 つのアプリケーションのいずれかにトラフィックをルーティングするルールを構成します。

次の例では、アドレス `http://40.117.74.8/` へのトラフィックが `aks-helloworld` という名前のサービスにルーティングされることに注意してください。 アドレス `http://40.117.74.8/hello-world-two` へのトラフィックは、`ingress-demo` サービスにルーティングされます。

`hello-world-ingress.yaml` という名前のファイルを作成し、次の例の YAML 内にコピーします。

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
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

`kubectl apply -f hello-world-ingress.yaml` コマンドを使用してイングレス リソースを作成します。

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>イングレス コントローラーをテストする

イングレス コント ローラーに対してルートをテストするには、2 つのアプリケーションを参照します。 Web ブラウザーを開き、*http://40.117.74.8* などの NGINX イングレス コントローラーの IP アドレスに移動します。 最初のデモ アプリケーションは、次の例で示すように、Web ブラウザーに表示されます。

![イングレス コント ローラーの背後で実行中の最初のアプリ](media/ingress-basic/app-one.png)

ここで *http://40.117.74.8/hello-world-two* などの */hello-world-two* パスを IP アドレスに追加します。 カスタム タイトル付きの 2 番目のデモ アプリケーションが表示されます。

![イングレス コント ローラーの背後で実行中の 2 番目のアプリ](media/ingress-basic/app-two.png)

## <a name="next-steps"></a>次の手順

この記事には、AKS への外部コンポーネントが含まれています。 これらのコンポーネントの詳細については、次のプロジェクト ページを参照してください。

- [Helm CLI][helm-cli]
- [NGINX イングレス コントローラー][nginx-ingress]

さらに、以下を実行できます。

- [HTTP アプリケーションのルーティング アドオンを有効にする][aks-http-app-routing]
- [内部のプライベート ネットワークと IP アドレスを使用するイングレス コントローラーを作成する][aks-ingress-internal]
- [動的パブリック IP アドレスを使用してイングレス コントローラーを作成し、Let's Encrypt を構成して TLS 証明書を自動的に生成する][aks-ingress-tls]
- [静的パブリック IP アドレスを使用してイングレス コントローラーを作成し、Let's Encrypt を構成して TLS 証明書を自動的に生成する][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
