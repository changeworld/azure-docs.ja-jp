---
title: Azure Application Gateway イングレス コントローラーの使用
description: Azure Application Gateway イングレスを Open Service Mesh と一緒に使用する方法
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 765eb53098f757d29072d736d50086f31bb11dc3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649758"
---
# <a name="deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>Azure Application Gateway イングレス AKS アドオンを使用して、Open Service Mesh (OSM) によって管理されるアプリケーションをデプロイする

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
>
> - 現在の OSM クラスター構成を表示する
> - 名前空間でデプロイされたアプリケーションを管理するための OSM の名前空間を作成する
> - OSM によって管理される名前空間をオンボードする
> - サンプル アプリケーションをデプロイする
> - AKS クラスター内で実行されているアプリケーションを確認する
> - アプリケーションのイングレス コントローラーとして使用する Azure Application Gateway を作成する
> - Azure Application Gateway イングレス経由でサービスをインターネットに公開する

## <a name="before-you-begin"></a>開始する前に

このチュートリアルで詳しく説明する手順では、AKS クラスターの OSM AKS アドオンを以前に有効にしたことを前提としています。 そうしていない場合、続行する前に、「[OSM AKS アドオンをデプロイする](./open-service-mesh-deploy-addon-az-cli.md)」の記事を確認してから、次に進みます。 また、AKS クラスターは Kubernetes バージョン `1.19+` 以降であること、Kubernetes RBAC が有効になっていること、クラスターとの `kubectl` 接続が確立されていること (これらの項目のいずれかについてのヘルプが必要な場合は、[AKS クイック スタート](./kubernetes-walkthrough.md)に関するページを参照してください)、および AKS OSM アドオンがインストールされていることが必要です。

次のリソースがインストールされている必要があります。

- Azure CLI バージョン 2.20.0 以降
- `aks-preview` 拡張機能バージョン 0.5.5 以降
- OSM バージョン v0.8.0 以降
- JSON プロセッサ "jq" バージョン 1.6 以上

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="view-and-verify-the-current-osm-cluster-configuration"></a>現在の OSM クラスター構成を表示して確認する

AKS の OSM アドオンが AKS クラスターで有効になったら、osm-mesh-config リソースで現在の構成パラメーターを表示できます。 次のコマンドを実行して、プロパティを表示します。

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

出力には、クラスターの現在の OSM MeshConfig が表示されます。

```
apiVersion: config.openservicemesh.io/v1alpha1
kind: MeshConfig
metadata:
  creationTimestamp: "0000-00-00A00:00:00A"
  generation: 1
  name: osm-mesh-config
  namespace: kube-system
  resourceVersion: "2494"
  uid: 6c4d67f3-c241-4aeb-bf4f-b029b08faa31
spec:
  certificate:
    serviceCertValidityDuration: 24h
  featureFlags:
    enableEgressPolicy: true
    enableMulticlusterMode: false
    enableWASMStats: true
  observability:
    enableDebugServer: true
    osmLogLevel: info
    tracing:
      address: jaeger.osm-system.svc.cluster.local
      enable: false
      endpoint: /api/v2/spans
      port: 9411
  sidecar:
    configResyncInterval: 0s
    enablePrivilegedInitContainer: false
    envoyImage: mcr.microsoft.com/oss/envoyproxy/envoy:v1.18.3
    initContainerImage: mcr.microsoft.com/oss/openservicemesh/init:v0.9.1
    logLevel: error
    maxDataPlaneConnections: 0
    resources: {}
  traffic:
    enableEgress: true
    enablePermissiveTrafficPolicyMode: true
    inboundExternalAuthorization:
      enable: false
      failureModeAllow: false
      statPrefix: inboundExtAuthz
      timeout: 1s
    useHTTPSIngress: false
```

**enablePermissiveTrafficPolicyMode** が **true** に構成されていることに注意してください。 OSM の制限のないトラフィック ポリシー モードは、[SMI](https://smi-spec.io/) トラフィック ポリシーの適用がバイパスされるモードです。 このモードの OSM では、サービス メッシュの一部であるサービスが自動的に検出され、これらのサービスと通信できるように各エンボイ プロキシ サイドカーのトラフィック ポリシー規則がプログラムされます。

## <a name="create-namespaces-for-the-application"></a>アプリケーションの名前空間を作成する

このチュートリアルでは、次のアプリケーション コンポーネントを持つ OSM bookstore アプリケーションを使用します。

- `bookbuyer`
- `bookthief`
- `bookstore`
- `bookwarehouse`

これらのアプリケーション コンポーネントごとに名前空間を作成します。

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

次の出力が表示されます。

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

## <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>OSM によって管理される名前空間をオンボードする

OSM メッシュに名前空間を追加すると、OSM コントローラーでは、アプリケーションにエンボイ サイドカー プロキシ コンテナーを自動的に挿入できるようになります。 次のコマンドを実行して、OSM bookstore アプリケーションの名前空間をオンボードします。

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

次の出力が表示されます。

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

## <a name="deploy-the-bookstore-application"></a>Bookstore アプリケーションをデプロイする

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.9/docs/example/manifests/apps/bookwarehouse.yaml
```

下に、すべてのデプロイ出力の要約を示します。

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

## <a name="update-the-bookbuyer-service"></a>`Bookbuyer` Service を更新する

次のサービス マニフェストを使用して、`bookbuyer` サービスを正しい受信ポート構成に更新します。

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

## <a name="verify-the-bookstore-application"></a>Bookstore アプリケーションを確認する

現在のところ、bookstore 複数コンテナー アプリケーションをデプロイしましたが、アクセスできるのは AKS クラスター内からだけです。 後で、AKS クラスター外にアプリケーションを公開するために、Azure Application Gateway のイングレス コントローラーを追加します。 アプリケーションがクラスター内で実行されていることを確認するには、ポート転送を使用して、`bookbuyer` コンポーネント UI を表示します。

まず、`bookbuyer` ポッドの名前を取得しましょう

```azurecli-interactive
kubectl get pod -n bookbuyer
```

次のような出力が表示されます。 `bookbuyer` ポッドには、一意の名前が追加されます。

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

ポッドの名前を取得したら、port-forward コマンドを使用して、ローカル システムから AKS クラスター内のアプリケーションにトンネルを設定できるようになります。 次のコマンドを実行して、ローカル システム ポート 8080 のポート転送を設定します。 もう一度、特定の `bookbuyer` ポッドの名前を使用します。

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

次のような出力が表示されます。

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

ポート転送セッションが実行されている間、ブラウザーの `http://localhost:8080` から次の URL に移動します。 下のイメージのように、ブラウザーで `bookbuyer` アプリケーション UI を表示できるようになります。

![App Gateway の UI イメージ用 OSM bookbuyer アプリ](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

## <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application"></a>Azure Application Gateway を作成して `bookbuyer` アプリケーションにアクセスできるようにする

> [!NOTE]
> 次の手順では、イングレスに使用する Azure Application Gateway の新しいインスタンスを作成します。 使用したい既存の Azure Application Gateway がある場合は、Application Gateway イングレス コントローラー アドオンを有効にするためのセクションに進んでください。

### <a name="deploy-a-new-application-gateway"></a>新しい Application Gateway のデプロイ

> [!NOTE]
> 既存の AKS クラスターに対して Application Gateway イングレス コントローラー アドオンを有効にするための既存のドキュメントを参照しています。 OSM の資料に合うようにいくつかの変更が行われました。 この件に関する詳細なドキュメントについては、[こちら](../application-gateway/tutorial-ingress-controller-add-on-existing.md)を参照してください。

ここでは、_myCluster_ という AKS クラスターへのトラフィックの負荷分散に使用する既存の Application Gateway があることをシミュレートするため、新しい Application Gateway をデプロイします。 Application Gateway の名前は _myApplicationGateway_ になりますが、最初に、_myPublicIp_ という名前のパブリック IP リソースと、アドレス空間が 11.0.0.0/8 の _myVnet_ という名前の新しい仮想ネットワークと、アドレス空間が 11.1.0.0/16 の _mySubnet_ というサブネットを作成し、_myPublicIp_ を使用して Application Gateway を _mySubnet_ にデプロイする必要があります。

AKS クラスターと Application Gateway を別の仮想ネットワークで使用する場合、2 つの仮想ネットワークのアドレス空間が重複しないようにする必要があります。 AKS クラスターがデプロイする既定のアドレス空間は 10.0.0.0/8 であるため、Application Gateway 仮想ネットワークのアドレス プレフィックスを 11.0.0.0/8 に設定します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus2
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16
az network application-gateway create -n myApplicationGateway -l eastus2 -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Application Gateway イングレス コントローラー (AGIC) アドオンは、Application Gateway v2 SKU (Standard および WAF) **のみ** をサポートし、Application Gateway v1 SKU はサポート **しません**。

### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>Azure CLI を使用して既存の AKS クラスターの AGIC アドオンを有効にする

Azure CLI を引き続き使用する場合は、作成した AKS クラスター _myCluster_ で AGIC アドオンを有効にし、作成した既存の Application Gateway _myApplicationGateway_ を使用する AGIC アドオンを指定します。

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

次のコマンドを使用して、Azure Application Gateway AKS アドオンが有効になっていることを確認できます。

```azurecli-interactive
az aks list -g osm-aks-rg -o json | jq -r .[].addonProfiles.ingressApplicationGateway.enabled
```

このコマンドでは、出力には `true` と示されます。

### <a name="peer-the-two-virtual-networks-together"></a>2 つの仮想ネットワークを一緒にピアリングする

AKS クラスターを独自の仮想ネットワークにデプロイし、別の仮想ネットワークに Application Gateway をデプロイしたため、Application Gateway からクラスター内のポッドにトラフィックを流すには、2 つの仮想ネットワークを一緒にピアリングする必要があります。 2 つの仮想ネットワークをピアリングするには、接続が双方向になるよう、Azure CLI コマンドを 2 回個別に実行する必要があります。 最初のコマンドは、Application Gateway 仮想ネットワークから AKS 仮想ネットワークへのピアリング接続を作成します。2 番目のコマンドは、その逆方向にピアリング接続を作成します。

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

## <a name="expose-the-bookbuyer-service-to-the-internet"></a>インターネットで `bookbuyer` サービスにアクセスできるようにする

次のイングレス マニフェストを AKS クラスターに適用し、Azure Application Gateway を通じてインターネットで `bookbuyer` サービスにアクセスできるようにします。

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

次のような出力が表示されるはずです

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

イングレス マニフェストのホスト名はテストに使用される擬似名であるため、DNS 名はインターネット上では使用できません。 その代わりに、curl プログラムを使用して、ホストネーム ヘッダーを Azure Application Gateway パブリック IP に渡し、200 コードを受け取ることで、`bookbuyer` サービスに接続できます。

```azurecli-interactive
appGWPIP=$(az network public-ip show -g MyResourceGroup -n myPublicIp -o tsv --query "ipAddress")
curl -H 'Host: bookbuyer.contoso.com' http://$appGWPIP/
```

次のような出力が表示されるはずです

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>5969</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>5692</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 16:34:30 UTC</strong>
  </body>
</html>
```

## <a name="troubleshooting"></a>トラブルシューティング

- [AGIC トラブルシューティングのドキュメント](../application-gateway/ingress-controller-troubleshoot.md)
- [その他のトラブルシューティング ツールは、AGIC の GitHub リポジトリで入手可能](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)
