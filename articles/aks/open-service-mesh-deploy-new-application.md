---
title: Open Service Mesh で新しいアプリケーションを管理する
description: Open Service Mesh で新しいアプリケーションを管理する方法
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 1dc58fa0709cdf1e09482a3f3ac3ee05788cd1eb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066864"
---
# <a name="manage-a-new-application-with-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Open Service Mesh (OSM) Azure Kubernetes Service (AKS) アドオンを使用して新しいアプリケーションを管理する

## <a name="before-you-begin"></a>開始する前に

このチュートリアルで概要を説明する手順では、AKS クラスター (Kubernetes RBAC が有効になっている Kubernetes `1.19+` 以降) を作成し、クラスターとの `kubectl` 接続を確立し (これらの項目のいずれかについてのヘルプが必要な場合は、[AKS クイックスタート](./kubernetes-walkthrough.md)に関するページを参照してください)、AKS OSM アドオンをインストールしたことを前提としています。

次のリソースがインストールされている必要があります。

- Azure CLI バージョン 2.20.0 以降
- OSMバージョンバージョン v 0.11.1 以降
- JSON プロセッサ "jq" バージョン 1.6 以上

## <a name="create-namespaces-for-the-application"></a>アプリケーションの名前空間を作成する

このチュートリアルでは、次の Kubernetes サービスを含む OSM bookstore アプリケーションを使用します。

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

OSM メッシュに名前空間を追加すると、このアクションによって、エンボイ サイドカー プロキシ コンテナーを OSM コントローラーで自動的にアプリケーションに挿入できるようになります。 次のコマンドを実行して、OSM bookstore アプリケーションの名前空間をオンボードします。

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
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.11/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.11/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.11/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.11/docs/example/manifests/apps/bookwarehouse.yaml
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

## <a name="checkpoint-what-got-installed"></a>チェックポイント: インストールされたもの

Bookstore アプリケーションは、サービス メッシュ機能のテストに適した多層アプリケーションの例です。 このアプリケーションは、`bookbuyer`、`bookthief`、`bookstore`、`bookwarehouse` の 4 つのサービスから成ります。 `bookbuyer` サービスと `bookthief` サービスは、どちらも `bookstore` サービスと通信を行うことで `bookstore` サービスから書籍を取得します。 `bookstore` サービスは、`bookbuyer` と `bookthief` に供給する書籍を `bookwarehouse` サービスから取得します。 これは、アプリケーション サービス間の通信を保護および認可するためにサービス メッシュをどのように使用できるかを示すのに適した、単純な多層アプリケーションです。 このチュートリアルの続きとして、Service Mesh Interface (SMI) ポリシーを有効にしたり無効にしたりして、サービスによる OSM 経由での通信の許可および禁止の両方を行うことができるようにします。 下に、bookstore アプリケーション用にインストールされたもののアーキテクチャ図を示します。

![OSM bookbuyer アプリのアーキテクチャ](./media/aks-osm-addon/osm-bookstore-app-arch.png)

## <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>AKS クラスター内で実行されている bookstore アプリケーションを確認する

`bookstore` 複数コンテナー アプリケーションをデプロイしましたが、現時点でアクセスできるのは AKS クラスター内からだけです。 後のチュートリアルは、イングレス コントローラー経由でクラスターの外部にアプリケーションを公開するのに役立ちます。 ここでは、ポート転送を利用し、AKS クラスター内の `bookbuyer` アプリケーションにアクセスして、`bookstore` サービスから書籍を購入していることを検証します。

アプリケーションがクラスター内で実行されていることを確認するには、ポート転送を使用して、`bookbuyer` コンポーネントと `bookthief` コンポーネントの UI を表示します。

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

> [!NOTE]
> どのポート転送コマンドでも、トンネルを切断せずにこのチュートリアルの作業を続行できるように、追加のターミナルを使用することをお勧めします。 また、Azure Cloud Shell の外部でポート転送トンネルを確立することをお勧めします。

```Bash
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

次のような出力が表示されます。

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

ポート転送セッションが実行されている間、ブラウザーの `http://localhost:8080` から次の URL に移動します。 下のイメージのように、ブラウザーで `bookbuyer` アプリケーション UI を表示できるようになります。

![OSM bookbuyer アプリの UI イメージ](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

また、書籍の購入数合計が、`bookstore` v1 サービスで増分し続けていることもわかります。 `bookstore` v2 サービスはまだデプロイされていません。 SMI トラフィック分割ポリシーをデモンストレーションするときに、`bookstore` v2 サービスをデプロイします。

また、`bookthief` サービスについても同じことを確認できます。

```azurecli-interactive
kubectl get pod -n bookthief
```

次のような出力が表示されます。 `bookthief` ポッドには、一意の名前が追加されます。

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

`bookthief` ポッドにポート転送します。

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

ブラウザー `http://localhost:8080` から次の URL に移動します。 `bookthief` が `bookstore` サービスから現在書籍を盗んでいることがわかります。 後で、`bookthief` を止めるためのトラフィック ポリシーを実装します。

![OSM bookthief アプリの UI イメージ](./media/aks-osm-addon/osm-bookthief-service-ui.png)

## <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>メッシュの OSM の制限のないトラフィック モードを無効にする

ここでは、制限のないトラフィック モード ポリシーを無効にします。OSM では、各サービスからメッシュ内での通信を許可するために、クラスターにデプロイされた明示的な [SMI](https://smi-spec.io/) ポリシーが必要です。 制限のないトラフィック モードを無効にするには、次のコマンドを実行して、OSM MeshConfig リソース プロパティを更新して値を `true` から `false` に変更します。

```azurecli-interactive
kubectl patch meshconfig osm-mesh-config -n kube-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":false}}}' --type=merge
```

次のような出力が表示されます。

```Output
meshconfig.config.openservicemesh.io/osm-mesh-config patched
```

制限のないトラフィック モードが無効になっていることを確認するには、`bookbuyer` または `bookthief` のいずれかのポッドにポート転送して戻り、ブラウザーで UI を表示し、購入された書籍または盗まれた書籍がもう増分していないかどうかを確認します。 ブラウザーを必ず最新の状態に更新してください。 増分が停止している場合は、ポリシーが正しく適用されています。 `bookthief` による書籍の盗難をうまくやめさせましたが、`bookbuyer` が `bookstore` から購入することも、`bookstore` が `bookwarehouse` から書籍を取得することもできません。 次に、通信する必要があるメッシュ内のサービスのみに通信を許可する [SMI](https://smi-spec.io/) ポリシーを実装します。 制限の少ないトラフィック モードの詳細については、[制限の少ないトラフィック ポリシー モード](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/)に関する記事を参照してください。

## <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>Service Mesh Interface (SMI) トラフィック アクセス ポリシーを適用する

メッシュ内のすべての通信を無効にしたので、書籍を購入するために `bookbuyer` サービスで `bookstore` サービスと通信できるようにし、販売する書籍を取得するために `bookstore` サービスで `bookwarehouse` サービスと通信できるようにしましょう。

次の [SMI](https://smi-spec.io/) ポリシーをデプロイします。

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore
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
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookstore-access-bookwarehouse
  namespace: bookwarehouse
spec:
  destination:
    kind: ServiceAccount
    name: bookwarehouse
    namespace: bookwarehouse
  rules:
  - kind: HTTPRouteGroup
    name: bookwarehouse-service-routes
    matches:
    - restock-books
  sources:
  - kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  - kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookwarehouse-service-routes
  namespace: bookwarehouse
spec:
  matches:
    - name: restock-books
      methods:
      - POST
      headers:
      - host: bookwarehouse.bookwarehouse
EOF
```

次のような出力が表示されます。

```Output
traffictarget.access.smi-spec.io/bookbuyer-access-bookstore-v1 created
httproutegroup.specs.smi-spec.io/bookstore-service-routes created
traffictarget.access.smi-spec.io/bookstore-access-bookwarehouse created
httproutegroup.specs.smi-spec.io/bookwarehouse-service-routes created
```

これで、`bookbuyer` または `bookstore` のいずれかのポッドでポート転送セッションを設定できるようになりました。また、購入した書籍と販売した書籍の両方のメトリックが再度増分していることを確認できます。 また、`bookthief` ポッドがもう書籍を盗むことができなくなっていることを確認するために、このポッドに対しても同じ操作を行うことができます。

## <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>Service Mesh Interface (SMI) トラフィック分割ポリシーを適用する

最後のデモでは、[SMI](https://smi-spec.io/) トラフィック分割ポリシーを作成して、1 つのサービスから複数のサービスへの通信の重みをバックエンドとして構成します。 トラフィック分割機能を使用すると、0 から 100 のスケールでトラフィックを重み付けして、あるサービスへの接続を別のサービスに段階的に移動できます。

下の図は、デプロイされる [SMI](https://smi-spec.io/) トラフィック分割ポリシーの図です。 別の `Bookstore` バージョン 2 をデプロイした後、`bookbuyer` からの着信トラフィックを分割し、トラフィックの重みの 25% を `bookstore` v1 サービスに、75% を `bookstore` v2 サービスに適用します。

![OSM bookbuyer のトラフィック分割の図](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

`bookstore` v2 サービスをデプロイします。

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: v1
kind: Service
metadata:
  name: bookstore-v2
  namespace: bookstore
  labels:
    app: bookstore-v2
spec:
  ports:
  - port: 14001
    name: bookstore-port
  selector:
    app: bookstore-v2
---
# Deploy bookstore-v2 Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: bookstore-v2
  namespace: bookstore
---
# Deploy bookstore-v2 Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bookstore-v2
  namespace: bookstore
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bookstore-v2
  template:
    metadata:
      labels:
        app: bookstore-v2
    spec:
      serviceAccountName: bookstore-v2
      containers:
        - name: bookstore
          image: openservicemesh/bookstore:v0.8.0
          imagePullPolicy: Always
          ports:
            - containerPort: 14001
              name: web
          command: ["/bookstore"]
          args: ["--path", "./", "--port", "14001"]
          env:
            - name: BOOKWAREHOUSE_NAMESPACE
              value: bookwarehouse
            - name: IDENTITY
              value: bookstore-v2
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookbuyer-access-bookstore-v2
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore-v2
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
EOF
```

次の出力が表示されます。

```Output
service/bookstore-v2 configured
serviceaccount/bookstore-v2 created
deployment.apps/bookstore-v2 created
traffictarget.access.smi-spec.io/bookstore-v2 created
```

次に、トラフィック分割ポリシーをデプロイして、v1 と v2 の 2 つの `bookstore` サービスの間で `bookbuyer` のトラフィックを分割します。

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: split.smi-spec.io/v1alpha2
kind: TrafficSplit
metadata:
  name: bookstore-split
  namespace: bookstore
spec:
  service: bookstore.bookstore
  backends:
  - service: bookstore
    weight: 25
  - service: bookstore-v2
    weight: 75
EOF
```

次の出力が表示されます。

```Output
trafficsplit.split.smi-spec.io/bookstore-split created
```

`bookbuyer` ポッドへのポート転送トンネルを設定します。`bookstore` v2 サービスから購入した書籍が表示されるようになります。 引き続き購入の増分を確認すると、`bookstore` v2 サービスを通じて行われる購入の増分が早くなっていることに気付きます。

![OSM bookbuyer の購入済み書籍の UI](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)
