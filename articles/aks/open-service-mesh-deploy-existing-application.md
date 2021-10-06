---
title: Open Service Mesh を使用して既存のアプリケーションを管理する
description: Open Service Mesh を使用して既存のアプリケーションを管理する方法
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 092e42a5f9c1779fc5968b9fc733d260d405a90a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625907"
---
# <a name="manage-an-existing-application-with-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Open Service Mesh (OSM) Azure Kubernetes Service (AKS) アドオンを使用して既存のアプリケーションを管理する

## <a name="before-you-begin"></a>開始する前に

このチュートリアルで詳しく説明する手順では、AKS クラスターの OSM AKS アドオンを以前に有効にしたことを前提としています。 そうしていない場合、続行する前に、「[OSM AKS アドオンをデプロイする](./open-service-mesh-deploy-addon-az-cli.md)」の記事を確認してから、次に進みます。 また、AKS クラスターは Kubernetes バージョン `1.19+` 以降であること、Kubernetes RBAC が有効になっていること、クラスターとの `kubectl` 接続が確立されていること (これらの項目のいずれかについてのヘルプが必要な場合は、[AKS クイック スタート](./kubernetes-walkthrough.md)に関するページを参照してください)、および AKS OSM アドオンがインストールされていることが必要です。

次のリソースがインストールされている必要があります。

- Azure CLI バージョン 2.20.0 以降
- `aks-preview` 拡張機能バージョン 0.5.5 以降
- OSM バージョン v0.8.0 以降
- JSON プロセッサ "jq" バージョン 1.6 以上

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Open Service Mesh (OSM) の制限のないトラフィック モード ポリシーを確認する

OSM の制限のないトラフィック ポリシー モードは、[SMI](https://smi-spec.io/) トラフィック ポリシーの適用がバイパスされるモードです。 このモードの OSM では、サービス メッシュの一部であるサービスが自動的に検出され、これらのサービスと通信できるように各エンボイ プロキシ サイドカーのトラフィック ポリシー規則がプログラムされます。

クラスターの OSM の現在の制限のないトラフィック モードを確認するには、次のコマンドを実行します。

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o yaml
```

OSM MeshConfig の出力は次のようになります。

```Output
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

**enablePermissiveTrafficPolicyMode** が **true** に構成されている場合は、サービス間の通信を中断することなく、名前空間を安全にオンボードできます。 **enablePermissiveTrafficPolicyMode** が **false** に構成されている場合は、正しい [SMI](https://smi-spec.io/) トラフィック アクセス ポリシー マニフェストがデプロイされていることを確認する必要があります。 また、名前空間にデプロイされている各サービスを表すサービス アカウントを持っていることを確認する必要もあります。 制限の少ないトラフィック モードの詳細については、[制限の少ないトラフィック ポリシー モード](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/)に関する記事を参照してください。

## <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Open Service Mesh (OSM) の制限のないトラフィック ポリシーを True に構成して既存のデプロイ済みアプリケーションをオンボードする

最初に、デプロイされたアプリケーションの名前空間を OSM に追加して管理します。 次の例では、**bookstore** 名前空間を OSM にオンボードします。

```azurecli-interactive
osm namespace add bookstore
```

次の出力が表示されます。

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

次に、名前空間での現在のポッドのデプロイについて見ていきます。 次のコマンドを実行して、`bookbuyer` 名前空間のポッドを表示します。

```azurecli-interactive
kubectl get pod -n bookbuyer
```

次のような出力が表示されます。

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-78666dcff8-wh6wl   1/1     Running   0          43s
```

**[READY]** 列に **[1/1]** と表示されます。つまり、アプリケーション ポッドに含まれるコンテナーは 1 つだけであることに注意してください。 次に、OSM でアプリケーション ポッドにエンボイ サイドカー プロキシ コンテナーを挿入できるように、アプリケーションのデプロイを再起動する必要があります。 名前空間のデプロイの一覧を取得してみましょう。

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

次の出力が表示されます。

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           23h
```

次に、アプリケーション ポッドにエンボイ サイドカー プロキシ コンテナーを挿入するために、デプロイを再起動します。 次のコマンドを実行します。

```azurecli-interactive
kubectl rollout restart deployment bookbuyer -n bookbuyer
```

次の出力が表示されます。

```Output
deployment.apps/bookbuyer restarted
```

名前空間のポッドをもう一度確認すると、次のようになります。

```azurecli-interactive
kubectl get pod -n bookbuyer
```

ポッドのコンテナーの準備ができているという **[2/2]** が現在 **[Ready]** 列に表示されていることがわかります。 2 番目のコンテナーは、エンボイ サイドカー プロキシです。

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-84446dd5bd-j4tlr   2/2     Running   0          3m30s
```

describe コマンドを実行して構成を表示すると、さらにポッドを調べてエンボイ プロキシを表示できます。

```azurecli-interactive
kubectl describe pod bookbuyer-84446dd5bd-j4tlr -n bookbuyer
```

```Output
Containers:
  bookbuyer:
    Container ID:  containerd://b7503b866f915711002292ea53970bd994e788e33fb718f1c4f8f12cd4a88198
    Image:         openservicemesh/bookbuyer:v0.8.0
    Image ID:      docker.io/openservicemesh/bookbuyer@sha256:813874bd2dc9c5a259b9657995348cf0822b905e29c4e86f21fdefa0ef21dcee
    Port:          <none>
    Host Port:     <none>
    Command:
      /bookbuyer
    State:          Running
      Started:      Tue, 23 Mar 2021 10:52:53 -0400
    Ready:          True
    Restart Count:  0
    Environment:
      BOOKSTORE_NAMESPACE:  bookstore
      BOOKSTORE_SVC:        bookstore
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from bookbuyer-token-zft2r (ro)
  envoy:
    Container ID:  containerd://f5f1cb5db8d5304e23cc984eb08146ea162a3e82d4262c4472c28d5579c25e10
    Image:         envoyproxy/envoy-alpine:v1.17.1
    Image ID:      docker.io/envoyproxy/envoy-alpine@sha256:511e76b9b73fccd98af2fbfb75c34833343d1999469229fdfb191abd2bbe3dfb
    Ports:         15000/TCP, 15003/TCP, 15010/TCP
    Host Ports:    0/TCP, 0/TCP, 0/TCP
```

エンボイ サイドカー プロキシ注入の後もアプリケーションが機能していることを確認します。

## <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Open Service Mesh (OSM) の制限のないトラフィック ポリシーを False に構成して既存のデプロイ済みアプリケーションをオンボードする

制限のないトラフィック ポリシーの OSM 構成が `false` に設定されている場合、OSM では、クラスター内でサービス間の通信を行うために、明示的な [SMI](https://smi-spec.io/) トラフィック アクセス ポリシーがデプロイされている必要があります。 現在、OSM では、サービス間の通信の認可の一環として Kubernetes サービス アカウントも使用されています。 OSM メッシュによる管理時に既存のデプロイ済みのアプリケーションでの通信を保証するには、次に、使用するサービス アカウントが存在することを確認し、サービス アカウント情報を使用してアプリケーションのデプロイを更新して、[SMI](https://smi-spec.io/) トラフィック アクセス ポリシーを適用します。

### <a name="verify-kubernetes-service-accounts"></a>Kubernetes サービス アカウントを確認する

アプリケーションのデプロイ先の名前空間に kubernetes サービス アカウントがあるかどうかを確認します。

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

次では、`bookbuyer` 名前空間に `bookbuyer` という名前のサービス アカウントがあります。

```Output
NAME        SECRETS   AGE
bookbuyer   1         25h
default     1         25h
```

既定のアカウント以外のサービス アカウントが一覧表示されていない場合は、アプリケーション用に作成する必要があります。 アプリケーションのデプロイ済み名前空間にサービス アカウントを作成するには、次のコマンドを例として使用します。

```azurecli-interactive
kubectl create serviceaccount myserviceaccount -n bookbuyer
```

```Output
serviceaccount/myserviceaccount created
```

### <a name="view-your-applications-current-deployment-specification"></a>アプリケーションの現在のデプロイ仕様を表示する

前のセクションでサービス アカウントを作成する必要があった場合、アプリケーションのデプロイがデプロイ仕様の特定の `serviceAccountName` で構成されていない可能性があります。次のコマンドを使用して、アプリケーションのデプロイ仕様を表示できます。

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

デプロイの一覧が出力に表示されます。

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

[ポッド テンプレート] セクションにサービス アカウントが一覧表示されているかどうかを確認するために、デプロイについて説明します。

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

この特定のデプロイでは、[ポッド テンプレート] セクションに一覧表示されているデプロイに関連付けられているサービス アカウントがあることがわかります。 このデプロイでは、サービス アカウント `bookbuyer` が使用されています。 **[サービス アカウント:]** プロパティが表示されない場合は、サービス アカウントを使用するようにデプロイが構成されていません。

```Output
Pod Template:
  Labels:           app=bookbuyer
                    version=v1
  Annotations:      kubectl.kubernetes.io/restartedAt: 2021-03-23T10:52:49-04:00
  Service Account:  bookbuyer
  Containers:
   bookbuyer:
    Image:      openservicemesh/bookbuyer:v0.8.0

```

デプロイを更新して kubernetes サービス アカウントを追加するには、いくつかの方法があります。 インラインでの[デプロイの更新](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment)に関する Kubernetes のドキュメントを確認するか、[ポッドのサービス アカウントを構成](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/)します。 サービス アカウントでデプロイ仕様を更新したら、デプロイをクラスターに再デプロイします (kubectl apply -f your-deployment.yaml)。

### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>必要なサービス メッシュ インターフェイス (SMI) ポリシーをデプロイする

認可されたトラフィックがメッシュ内で流れることができるようにするための最後の手順は、アプリケーションに必要な [SMI](https://smi-spec.io/) トラフィック アクセス ポリシーをデプロイすることです。 [SMI](https://smi-spec.io/) トラフィック アクセス ポリシーを使用して実現できる構成の量については、このチュートリアルの範囲には含まれません。ただし、仕様のいくつかの共通コンポーネントについて詳しく説明し、アプリケーションのサービス間の通信を可能にするために単純な TrafficTarget と HTTPRouteGroup の両方のポリシーを構成する方法を示します。

[SMI](https://smi-spec.io/) [**トラフィック アクセス制御**](https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-access/v1alpha3/traffic-access.md#traffic-access-control)仕様では、ユーザーはアプリケーションのアクセス制御ポリシーを定義できます。 **TrafficTarget** および **HTTPRoutGroup** API リソースに焦点を合わせます。

TrafficTarget リソースは、3 つの主要な構成設定である destination、rules、sources で構成されています。 TrafficTarget の例を下に示します。

```TrafficTarget Example spec
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
```

上記の TrafficTarget 仕様では、`destination` は、宛先ソース サービス用に構成されているサービス アカウントを表します。 前にデプロイに追加されたサービス アカウントは、アタッチ先のデプロイへのアクセスを認可するために使用されます。 この特定の例の `rules` セクションでは、接続を介して許可される HTTP トラフィックの種類を定義します。 HTTP 経由で許可されるトラフィックに固有のものになるように、HTTP ヘッダーの詳細な正規表現パターンを構成することができます。 `sources` セクションは、通信の発信元のサービスです。 この仕様は、`bookbuyer` が bookstore と通信する必要があることを示しています。

HTTPRouteGroup リソースは、HTTP ヘッダー情報の 1 つまたは複数の一致の配列で構成され、TrafficTarget 仕様の要件です。下の例では、HTTPRouteGroup が 3 つの HTTP アクション (2 つの GET と 1 つの POST) を認可していることがわかります。

```HTTPRouteGroup Example Spec
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
```

フロントエンド アプリケーションがアプリケーションの他の層に対して行う HTTP トラフィックの種類に慣れていない場合、TrafficTarget 仕様には規則が必要であるため、HTTPRouteGroup に下の仕様を使用して、すべて許可規則に相当するものを作成できます。

```HTTPRouteGroup Allow All Example
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: allow-all
  namespace: yournamespace
spec:
  matches:
  - name: allow-all
    pathRegex: '.*'
    methods: ["GET","PUT","POST","DELETE","PATCH"]
```

TrafficTarget と HTTPRouteGroup の仕様を構成したら、それらを 1 つの YAML にまとめて、デプロイすることができます。 bookstore の構成例を下に示します。

```Bookstore Example TrafficTarget and HTTPRouteGroup configuration
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
EOF
```

仕様の詳細については、[SMI](https://smi-spec.io/) のサイトを参照してください。

## <a name="manage-the-applications-namespace-with-osm"></a>OSM を使用してアプリケーションの名前空間を管理する

次に、OSM で名前空間を管理するようにし、デプロイを再起動して、エンボイ サイドカー プロキシをアプリケーションに挿入します。

次のコマンドを実行して、OSM で管理するように `azure-vote` 名前空間を構成します。

```azurecli-interactive
osm namespace add azure-vote
```

```Output
Namespace [azure-vote] successfully added to mesh [osm]
```

次に、次のコマンドを使用して `azure-vote-front` および `azure-vote-back` の両方のデプロイを再起動します。

```azurecli-interactive
kubectl rollout restart deployment azure-vote-front -n azure-vote
kubectl rollout restart deployment azure-vote-back -n azure-vote
```

```Output
deployment.apps/azure-vote-front restarted
deployment.apps/azure-vote-back restarted
```

`azure-vote` 名前空間のポッドを表示すると、`azure-vote-front` と `azure-vote-back` の両方の **[READY]** ステージが [2/2] として表示されます。つまり、エンボイ サイドカー プロキシがアプリケーションに挿入されています。
