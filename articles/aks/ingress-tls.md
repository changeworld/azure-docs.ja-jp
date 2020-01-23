---
title: Azure Kubernetes Service (AKS) クラスターを使用した HTTPS イングレスの作成
description: Azure Kubernetes Service (AKS) クラスターで、自動的に TLS 証明書を生成する Let's Encrypt を使用する NGINX イングレス コントローラーをインストールおよび構成する方法について説明します。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/15/2020
ms.author: mlearned
ms.openlocfilehash: 5df7a8a639c87f90df0d3d22f84c4e93ac81e3a2
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028312"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で HTTPS イングレス コントローラーを作成する

イングレス コントローラーは、リバース プロキシ、構成可能なトラフィック ルーティング、および Kubernetes サービスの TLS 終端を提供するソフトウェアです。 個別の Kubernetes サービスのイングレス ルールとルートを構成するには、Kubernetes イングレス リソースが使われます。 イングレス コントローラーとイングレス ルールを使用すれば、1 つの IP アドレスで Kubernetes クラスター内の複数のサービスにトラフィックをルーティングできます。

この記事では、Azure Kubernetes Service (AKS) クラスターに [NGINX イングレス コントローラー][nginx-ingress]を展開する方法について説明します。 [Let's Encrypt][lets-encrypt] 証明書を自動的に生成して構成するには、[cert-manager][cert-manager] プロジェクトを使用します。 最後に、それぞれが 1 つの IP アドレスでアクセスできる、2 つのアプリケーションを AKS クラスターで実行します。

次のこともできます。

- [外部のネットワーク接続を使用して基本的なイングレス コントローラーを作成する][aks-ingress-basic]
- [HTTP アプリケーションのルーティング アドオンを有効にする][aks-http-app-routing]
- [内部のプライベート ネットワークと IP アドレスを使用するイングレス コントローラーを作成する][aks-ingress-internal]
- [ご自身の TLS 証明書を使用するイングレス コントローラーを作成する][aks-ingress-own-tls]
- [Let's Encrypt を使用して静的パブリック IP アドレス付きの TLS 証明書を自動的に生成するイングレス コントローラーを作成する][aks-ingress-static-tls]

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

また、この記事では、[カスタム ドメイン][custom-domain]の [DNS ゾーン][dns-zone]が、お使いの AKS クラスターと同じリソース グループにあることも前提としています。

この記事では、Helm を使用し、NGINX イングレス コントローラー、cert-manager およびサンプル Web アプリをインストールします。 最新リリースの Helm を使用していることを確認します。 アップグレード手順については、[Helm のインストール ドキュメント][helm-install]を参照してください。Helm の構成および使用方法の詳細については、「[Azure Kubernetes Service (AKS) での Helm を使用したアプリケーションのインストール][use-helm]」を参照してください。

この記事ではまた、Azure CLI バージョン 2.0.64 以降を実行していることも必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="create-an-ingress-controller"></a>イングレス コントローラーを作成する

イングレス コントローラーを作成するには、`helm` コマンドを使用して *nginx-ingress* をインストールします。 追加された冗長性については、NGINX イングレス コントローラーの 2 つのレプリカが `--set controller.replicaCount` パラメーターでデプロイされています。 イングレス コントローラーのレプリカの実行から十分にメリットを享受するには、AKS クラスターに複数のノードが存在していることを確認します。

イングレス コントローラーも Linux ノード上でスケジュールする必要があります。 Windows Server ノード (現在は AKS でプレビュー段階) では、イングレス コントローラーを実行しないでください。 ノード セレクターは、`--set nodeSelector` パラメーターを使用して指定され、Linux ベース ノード上で NGINX イングレス コントローラーを実行するように Kubernetes スケジューラに指示されます。

> [!TIP]
> 次の例では、*ingress-basic* という名前のイングレス リソースの Kubernetes 名前空間が作成されます。 必要に応じて、ご自身の環境の名前空間を指定できます。

> [!TIP]
> クラスター内のコンテナーへの要求で[クライアント ソース IP の保持][client-source-ip]を有効にする場合は、Helm インストール コマンドに `--set controller.service.externalTrafficPolicy=Local` を追加します。 クライアント ソース IP が要求ヘッダーの *X-Forwarded-For* の下に格納されます。 クライアント ソース IP の保持が有効になっているイングレス コントローラーを使用する場合、SSL パススルーは機能しません。

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repo
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

インストールの間に、Azure パブリック IP アドレスがイングレス コントローラーに対して作成されます。 このパブリック IP アドレスは、イングレス コントローラーが存続している間は静的です。 イングレス コントローラーを削除すると、パブリック IP アドレスの割り当てが失われます。 続いてさらに別のイングレス コントローラーを作成すると、新しいパブリック IP アドレスが割り当てられます。 パブリック IP アドレスを使用し続けることを望む場合は、代わりに[静的パブリック IP アドレス][aks-ingress-static-tls]を使用してイングレス コントローラーを作成できます。

パブリック IP アドレスを取得するには、`kubectl get service` コマンドを使います。 IP アドレスがサービスに割り当てられるまでに、少し時間がかかる場合があります。

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
billowing-kitten-nginx-ingress-controller        LoadBalancer   10.0.182.160   MY_EXTERNAL_IP  80:30920/TCP,443:30426/TCP   20m
billowing-kitten-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

まだイングレス ルールは作成されていません。 パブリック IP アドレスを参照すると、NGINX イングレス コントローラーの既定の 404 ページが表示されます。

## <a name="add-an-a-record-to-your-dns-zone"></a>DNS ゾーンに A レコードを追加する

[az network dns record-set a add-record][az-network-dns-record-set-a-add-record] を使用して、*A* レコードを NGINX サービスの外部 IP アドレスが使用された DNS ゾーンに追加します。

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name * \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> 必要に応じて、イングレス コントローラーの IP アドレスに、カスタム ドメインではなく FQDN を構成することもできます。
> 
> ```azurecli-interactive
> # Public IP address of your ingress controller
> IP="MY_EXTERNAL_IP"
> 
> # Name to associate with public IP address
> DNSNAME="demo-aks-ingress"
> 
> # Get the resource-id of the public ip
> PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
> 
> # Update public ip address with DNS name
> az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
> 
> # Display the FQDN
> az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
> ```

## <a name="install-cert-manager"></a>cert-manager をインストールする

NGINX イングレス コントローラーは、TLS の終端をサポートしています。 HTTPS の証明書を取得および構成するには、いくつかの方法があります。 この記事では、[Lets Encrypt][lets-encrypt] 証明書を自動的に作成および管理する機能を提供する [cert-manager][cert-manager] の使用方法を説明します。

cert-manager コントローラーをインストールするには、次のようにします。

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace ingress-basic

# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic certmanager.k8s.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install cert-manager --namespace ingress-basic --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

cert-manager の構成の詳細については、[cert-manager プロジェクト][cert-manager]についてのページを参照してください。

## <a name="create-a-ca-cluster-issuer"></a>CA クラスター発行者を作成する

証明書を発行する前に、cert-manager には [Issuer][cert-manager-issuer] リソースまたは [ClusterIssuer][cert-manager-cluster-issuer] リソースが必要です。 これらの Kubernetes リソースは機能面では同一ですが、`Issuer` は単一の名前空間で機能し、`ClusterIssuer` はすべての名前空間にわたって機能します。 詳細については、[cert-manager issuer][cert-manager-issuer] についてのページを参照してください。

次のマニフェスト例を使用して、`cluster-issuer.yaml` などのクラスター発行者を作成します。 メール アドレスを実際の組織の有効なアドレスで置き換えてください。

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
        ingress:
          class: nginx
```

発行者を作成するには、`kubectl apply` コマンドを使用します。

```console
kubectl apply -f cluster-issuer.yaml --namespace ingress-basic
```

## <a name="run-demo-applications"></a>デモ アプリケーションを実行する

イングレス コントローラーと証明書管理ソリューションを構成しました。 ここでは、AKS クラスターで 2 つのデモ アプリケーションを実行します。 この例では、Helm を使って、単純な *Hello world* アプリケーションのインスタンスを 2 つデプロイします。

サンプルの Helm チャートをインストールする前に、お使いの Helm 環境に Azure サンプル リポジトリを追加します。

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

*azure-samples/aks-helloworld* Helm チャートを使用して、*aks-helloworld* という名前のデモ アプリケーションを作成します。

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

*aks-helloworld-two* という名前のデモ アプリケーションの 2 番目のインスタンスを作成します。 2 つのアプリケーションを見た目で区別できるように、 *--set* を使用して新しいタイトルと一意のサービス名を指定します。

```console
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>イングレス ルートを作成する

両方のアプリケーションは、Kubernetes クラスターで実行するようになります。 ただし、構成に使用されているサービスの種類は `ClusterIP` であるため、それらのアプリケーションにインターネットからアクセスすることはできません。 公開するには、Kubernetes イングレス リソースを作成します。 イングレス リソースでは、2 つのアプリケーションのいずれかにトラフィックをルーティングするルールを構成します。

次の例では、アドレス *hello-world-ingress.MY_CUSTOM_DOMAIN* へのトラフィックが *aks-helloworld* サービスにルーティングされます。 アドレス *hello-world-ingress.MY_CUSTOM_DOMAIN/hello-world-two* へのトラフィックは、*aks-helloworld-two* サービスにルーティングされます。 *hello-world-ingress.MY_CUSTOM_DOMAIN/static* へのトラフィックは、静的アセット用の *aks-helloworld* というサービスにルーティングされます。

次の YAML の例を使用して、`hello-world-ingress.yaml` という名前のファイルを作成します。 *hosts* と *host* を前の手順で作成した DNS 名に更新します。

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$2
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /static(/|$)(.*)
```

`kubectl apply` コマンドを使用してイングレス リソースを作成します。

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>証明書オブジェクトが作成されたことを確認する

次に、証明書リソースを作成する必要があります。 証明書リソースでは、必要な X.509 証明書を定義します。 詳細については、[cert-manager の証明書][cert-manager-certificates]についてのページを参照してください。 証明書マネージャーによって証明書オブジェクトが ingress-shim を使用して自動的に作成されており、その証明書オブジェクトは v0.2.2 以降の証明書マネージャーで自動的にデプロイされます。 詳しくは、[ingress-shim のドキュメント][ingress-shim]をご覧ください。

証明書が正常に作成されたことを確認するには、`kubectl get certificate --namespace ingress-basic` コマンドを使用して、*READY* が *True* になっていることを確認します。これには数分かかることがあります。

```
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>イングレスの構成をテストする

Web ブラウザーを開いて、お使いの Kubernetes イングレス コントローラーの *hello-world-ingress.MY_CUSTOM_DOMAIN* にアクセスします。 HTTPS を使用するようにリダイレクトされ、証明書が信頼されると、Web ブラウザーにデモ アプリケーションが 表示されます。 */hello-world-two* パスを追加すると、カスタム タイトルの付いた 2 番目のデモ アプリケーションが表示されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事では、Helm を使用して、イングレス コンポーネント、証明書、およびサンプル アプリをインストールしました。 Helm グラフをデプロイすると、多数の Kubernetes リソースが作成されます。 これらのリソースには、ポッド、デプロイ、およびサービスが含まれます。 これらのリソースをクリーンアップするには、サンプルの名前空間全体を削除するか、またはリソースを個々に削除します。

### <a name="delete-the-sample-namespace-and-all-resources"></a>サンプルの名前空間とすべてのリソースを削除する

サンプルの名前空間全体を削除するには、`kubectl delete` コマンドを使用して、名前空間の名前を指定します。 名前空間内のすべてのリソースが削除されます。

```console
kubectl delete namespace ingress-basic
```

次に AKS hello world アプリの Helm レポジトリを削除します。

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>リソースを個々に削除する

作成したリソースを個々に削除するという、きめ細かな方法もあります。 最初に、クラスター発行者リソースを削除します。

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

`helm list` コマンドを使用して、Helm リリースを一覧表示します。 次の出力例に示すように、*nginx-ingress* および *aks-helloworld* という名前のグラフを探します。

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-15 10:24:32.054871 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-15 10:24:37.671667 -0600 CST    deployed        aks-helloworld-0.1.0               
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.12.0    v0.12.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

`helm delete` コマンドでリリースを削除します。 次の例では、NGINX イングレス デプロイおよび 2 つのサンプルの AKS hello world アプリを削除します。

```
$ helm delete aks-helloworld aks-helloworld-two cert-manager nginx --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "cert-manager" uninstalled
release "nginx" uninstalled
```

次に AKS hello world アプリの Helm repo を削除します。

```console
helm repo remove azure-samples
```

サンプル アプリにトラフィックを送信していたイングレス ルートを削除します。

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

最後に、名前空間自体を削除します。 `kubectl delete` コマンドを使用して、名前空間の名前を指定します。

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>次のステップ

この記事には、AKS への外部コンポーネントが含まれています。 これらのコンポーネントの詳細については、次のプロジェクト ページを参照してください。

- [Helm CLI][helm-cli]
- [NGINX イングレス コントローラー][nginx-ingress]
- [cert-manager][cert-manager]

次のこともできます。

- [外部のネットワーク接続を使用して基本的なイングレス コントローラーを作成する][aks-ingress-basic]
- [HTTP アプリケーションのルーティング アドオンを有効にする][aks-http-app-routing]
- [内部のプライベート ネットワークと IP アドレスを使用するイングレス コントローラーを作成する][aks-ingress-internal]
- [ご自身の TLS 証明書を使用するイングレス コントローラーを作成する][aks-ingress-own-tls]
- [Let's Encrypt を使用して静的パブリック IP アドレス付きの TLS 証明書を自動的に生成するイングレス コントローラーを作成する][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
