---
title: NGINX イングレスの使用
description: Open Service Mesh で NGINX イングレスを使用する方法
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: ee313000b5b2fd21e7f629c57f45bbe0d3557835
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066826"
---
# <a name="deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>NGINX イングレスを使用して Open Service Mesh (OSM) によって管理されるアプリケーションをデプロイする

Open Service Mesh (OSM) は、軽量で拡張可能なクラウド ネイティブ サービス メッシュであり、ユーザーは、高度に動的なマイクロサービス環境のために、すぐに使用できる監視機能を一様に管理し、セキュリティで保護することができます。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
>
> - 現在の OSM クラスター構成を表示する
> - 名前空間でデプロイされたアプリケーションを管理するための OSM の名前空間を作成する
> - OSM によって管理される名前空間をオンボードする
> - サンプル アプリケーションをデプロイする
> - AKS クラスター内で実行されているアプリケーションを確認する
> - アプリケーションに使用される NGINX イングレス コントローラーを作成する
> - Azure Application Gateway イングレス経由でサービスをインターネットに公開する

## <a name="before-you-begin"></a>開始する前に

この記事で詳細を説明する手順では、AKS クラスター (Kubernetes RBAC が有効になっている Kubernetes `1.19+` 以降) を作成し、クラスターとの `kubectl` 接続を確立し (これらの項目のいずれかについてのヘルプが必要な場合は、[AKS クイックスタート](./kubernetes-walkthrough.md)に関するページを参照してください)、AKS OSM アドオンをインストールしたことを前提としています。

次のリソースがインストールされている必要があります。

- Azure CLI バージョン 2.20.0 以降
- OSMバージョンバージョン v 0.11.1 以降
- JSON プロセッサ "jq" バージョン 1.6 以上

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>現在の OSM クラスター構成を表示して確認する

AKS の OSM アドオンが AKS クラスターで有効になったら、osm-mesh-config リソースで現在の構成パラメーターを表示できます。 次のコマンドを実行して、プロパティを表示します。

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n osm-system -o yaml
```

出力には、クラスターの現在の OSM 構成が表示されます。

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

**enablePermissiveTrafficPolicyMode** が **true** に構成されていることに注意してください。 OSM の制限のないトラフィック ポリシー モードは、[SMI](https://smi-spec.io/) トラフィック ポリシーの適用がバイパスされるモードです。 このモードの OSM では、サービス メッシュの一部であるサービスが自動的に検出され、これらのサービスと通信できるように各エンボイ プロキシ サイドカーのトラフィック ポリシー規則がプログラムされます。 制限の少ないトラフィック モードの詳細については、[制限の少ないトラフィック ポリシー モード](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/)に関する記事を参照してください。

## <a name="create-namespaces-for-the-application"></a>アプリケーションの名前空間を作成する

このチュートリアルでは、次のアプリケーション コンポーネントがある OSM `bookstore` アプリケーションを使用します。

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

OSM メッシュに名前空間を追加すると、OSM コントローラーでは、アプリケーションにエンボイ サイドカー プロキシ コンテナーを自動的に挿入できるようになります。 次のコマンドを実行して、OSM `bookstore` アプリケーションの名前空間をオンボードします。

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

## <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>bookstore アプリケーションを AKS クラスターにデプロイする

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
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

## <a name="update-the-bookbuyer-service"></a>Bookbuyer サービスを更新する

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

## <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>AKS クラスター内で実行されている bookstore アプリケーションを確認する

現在のところ、`bookstore` 複数コンテナー アプリケーションをデプロイしましたが、アクセスできるのは AKS クラスター内からだけです。 後で、AKS クラスター外にアプリケーションを公開するために、Azure Application Gateway のイングレス コントローラーを追加します。 アプリケーションがクラスター内で実行されていることを確認するには、ポート転送を使用して、`bookbuyer` コンポーネント UI を表示します。

まず、`bookbuyer` ポッドの名前を取得しましょう

```azurecli-interactive
kubectl get pod -n bookbuyer
```

次のような出力が表示されます。 `bookbuyer` ポッドには、一意の名前が追加されます。

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

ポッドの名前を取得したら、port-forward コマンドを使用して、ローカル システムから AKS クラスター内のアプリケーションにトンネルを設定できるようになります。 次のコマンドを実行して、ローカル システム ポート 8080 のポート転送を設定します。 ここでも、指定した bookbuyer ポッド名を使用します。

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

次のような出力が表示されます。

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

ポート転送セッションが実行されている間、ブラウザーの `http://localhost:8080` から次の URL に移動します。 下のイメージのように、ブラウザーで `bookbuyer` アプリケーション UI を表示できるようになります。

![NGINX UI イメージ用 OSM bookbuyer アプリ](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

## <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で NGINX イングレス コントローラーを作成する

イングレス コントローラーは、リバース プロキシ、構成可能なトラフィック ルーティング、および Kubernetes サービスの TLS 終端を提供するソフトウェアです。 個別の Kubernetes サービスのイングレス ルールとルートを構成するには、Kubernetes イングレス リソースが使われます。 イングレス コントローラーとイングレス ルールを使用すれば、1 つの IP アドレスで Kubernetes クラスター内の複数のサービスにトラフィックをルーティングできます。

イングレス コントローラーを使用して、OSM によって管理されるアプリケーションをインターネットに公開します。 イングレス コントローラーを作成するには、Helm を使用して nginx-ingress をインストールします。 追加された冗長性については、NGINX イングレス コントローラーの 2 つのレプリカが `--set controller.replicaCount` パラメーターでデプロイされています。 イングレス コントローラーのレプリカの実行から十分にメリットを享受するには、AKS クラスターに複数のノードが存在していることを確認します。

イングレス コントローラーは Linux ノード上でスケジュールします。 Windows Server ノードでは、イングレス コントローラーを実行しないでください。 ノード セレクターは、`--set nodeSelector` パラメーターを使用して指定され、Linux ベース ノード上で NGINX イングレス コントローラーを実行するように Kubernetes スケジューラに指示されます。

> [!TIP]
> 次の例では、_ingress-basic_ という名前のイングレス リソースの Kubernetes 名前空間が作成されます。 必要に応じて、ご自身の環境の名前空間を指定できます。

```azurecli-interactive
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Update the helm repo(s)
helm repo update

# Use Helm to deploy an NGINX ingress controller in the ingress-basic namespace
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=1 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

NGINX イングレス コントローラー用の Kubernetes ロード バランサー サービスが作成されます。 次の出力例に示すように、動的なパブリック IP アドレスが割り当てられます。

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

イングレス ルールは作成されていません。 現在、内部 IP アドレスを参照すると、NGINX イングレス コントローラーの既定の 404 ページが表示されます。 イングレス ルールは、後続の手順で構成します。

## <a name="expose-the-bookbuyer-service-to-the-internet"></a>bookbuyer サービスをインターネットに公開する

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: nginx

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

次の出力が表示されます。

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

## <a name="view-the-nginx-logs"></a>NGINX ログを表示する

```azurecli-interactive
POD=$(kubectl get pods -n ingress-basic | grep 'nginx-ingress' | awk '{print $1}')

kubectl logs $POD -n ingress-basic -f
```

出力には、イングレス規則が正常に適用されたときの NGINX イングレス コントローラーの状態が示されます。

```Output
I0321 <date>       6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"bookbuyer", Name:"bookbuyer-ingress", UID:"e1018efc-8116-493c-9999-294b4566819e", APIVersion:"networking.k8s.io/v1beta1", ResourceVersion:"5460", FieldPath:""}): type: 'Normal' reason: 'Sync' Scheduled for sync
I0321 <date>        6 controller.go:146] "Configuration changes detected, backend reload required"
I0321 <date>        6 controller.go:163] "Backend successfully reloaded"
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
```

## <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>NGINX サービスと bookbuyer サービスを外部で表示する

```azurecli-interactive
kubectl get services -n ingress-basic
```

```Output
NAME                                               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-ingress-nginx-controller             LoadBalancer   10.0.100.23   20.193.1.74   80:31742/TCP,443:32683/TCP   4m15s
nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.0.163.98   <none>        443/TCP                      4m15s
```

イングレス マニフェストのホスト名はテストに使用される擬似名であるため、DNS 名はインターネット上では使用できません。 代わりに、curl プログラムを使用して、hostname ヘッダーを NGINX パブリック IP アドレスに渡し、bookbuyer サービスに正常に接続して 200 コードを受信することもできます。

```azurecli-interactive
curl -H 'Host: bookbuyer.contoso.com' http://EXTERNAL-IP/
```

次の出力が表示されます。

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
        <li>Total books bought: <strong>1833</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>1556</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 15:02:53 UTC</strong>
  </body>
</html>
```
