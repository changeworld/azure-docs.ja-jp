---
title: Open Service Mesh (プレビュー)
description: Azure Kubernetes Service (AKS) の Open Service Mesh (OSM)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: bbc07a7ee3f996c778cfc1b9d1764f10a613c50b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782947"
---
# <a name="open-service-mesh-aks-add-on-preview"></a>Open Service Mesh AKS アドオン (プレビュー)

## <a name="overview"></a>概要

[Open Service Mesh (OSM)](https://docs.openservicemesh.io/) は、軽量で拡張可能なクラウド ネイティブ サービス メッシュであり、ユーザーは、高度に動的なマイクロサービス環境のために、すぐに使用できる監視機能を統一して管理し、セキュリティで保護することができます。

OSM では、Kubernetes 上でエンボイベースのコントロール プレーンを実行します。また OSM は [SMI](https://smi-spec.io/) API を使用して構成でき、アプリケーションの各インスタンスの横に、エンボイ プロキシをサイドカー コンテナーとして挿入することで機能します。 エンボイ プロキシでは、アクセス制御ポリシーに関する実行規則が含まれており、これらの規則を実行します。また、ルーティング構成を実装し、メトリックをキャプチャします。 コントロール プレーンでは、ポリシーとルーティング規則が最新であることを確認し、プロキシが正常であることを保証するために、プロキシを継続的に構成します。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="capabilities-and-features"></a>機能と特徴

OSM には、Azure Kubernetes Service (AKS) クラスターにクラウド ネイティブ サービス メッシュを提供するために、次の一連の機能と特徴が備えられています。

- mTLS を有効にして、サービス間の通信をセキュリティで保護する

- エンボイ プロキシの自動サイドカー挿入を有効にして、アプリケーションをメッシュに簡単にオンボードする

- デプロイ時のトラフィック移行の簡単で透過的な構成

- サービスに対してきめ細かなアクセス制御ポリシーを定義して実行する機能

- デバッグおよび監視サービスのためのアプリケーション メトリックの監視と洞察

- プラグ可能なインターフェイスを使用した外部証明書管理サービスやソリューションとの統合

## <a name="scenarios"></a>シナリオ

OSM では、次のシナリオで AKS のデプロイを支援できます。

- クラスターにデプロイされたサービス エンドポイント間の暗号化された通信を提供する

- メッシュ内の HTTP/HTTPS と TCP トラフィックの両方に対するトラフィックの認可

- A/B またはカナリア デプロイ用の複数のサービス間の重み付けトラフィック制御の構成

- アプリケーション トラフィックからの KPI の収集と表示

## <a name="osm-service-quotas-and-limits-preview"></a>OSM サービスのクォータと制限 (プレビュー)

サービスのクォータと制限に関する OSM プレビューの制限については、AKS の[「クォータと地域の制限」ページ](https://docs.microsoft.com/azure/aks/quotas-skus-regions)を参照してください。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/osm/install-osm-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/osm/install-osm-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/osm/install-osm-binary-windows.md)]

::: zone-end

> [!WARNING]
> `osm install` を使用してバイナリから OSM のインストールを試行しないでください。 こうすると、AKS のアドオンとして統合されていない OSM がインストールされます。

### <a name="register-the-aks-openservicemesh-preview-feature"></a>`AKS-OpenServiceMesh` プレビュー機能を登録する

Open Service Mesh アドオンを使用できる AKS クラスターを作成するには、サブスクリプションで `AKS-OpenServiceMesh` 機能フラグを有効にする必要があります。

`AKS-OpenServiceMesh` 機能フラグは、次の例のとおり、[az feature register][az-feature-register] コマンドを使用して登録します。

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

状態が _[登録済み]_ と表示されるまでに数分かかります。 登録の状態は、[az feature list][az-feature-list] コマンドで確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、_Microsoft.ContainerService_ リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>新しい AKS クラスター用に Open Service Mesh (OSM) Azure Kubernetes Service (AKS) アドオンをインストールする

新しい AKS クラスターのデプロイ シナリオでは、クラスターの作成操作で OSM アドオンを有効にして AKS クラスターの新しいデプロイを開始します。

### <a name="create-a-resource-group"></a>リソース グループを作成する

Azure で、関連するリソースをリソース グループに割り当てます。 [az group create](/cli/azure/group#az_group_create) を使用してリソース グループを作成します。 次の例では、_myOsmAksGroup_ という名前のリソース グループを _eastus2_ の場所 (リージョン) に作成します。

```azurecli-interactive
az group create --name <myosmaksgroup> --location <eastus2>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>OSM アドオンが有効になっている AKS クラスターをデプロイする

次に、OSM アドオンが有効になっている新しい AKS クラスターをデプロイします。

> [!NOTE]
> 次の AKS デプロイ コマンドでは OS エフェメラル ディスクを使用することに注意してください。 [AKS のエフェメラル OS ディスク](https://docs.microsoft.com/azure/aks/cluster-configuration#ephemeral-os)の詳細については、こちらを参照してください

```azurecli-interactive
az aks create -n osm-addon-cluster -g <myosmaksgroup> --kubernetes-version 1.19.6 --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>AKS クラスター アクセス資格情報を取得する

新しいマネージド Kubernetes クラスターのアクセス資格情報を取得します。

```azurecli-interactive
az aks get-credentials -n <myosmakscluster> -g <myosmaksgroup>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>既存の AKS クラスター用に Open Service Mesh (OSM) Azure Kubernetes Service (AKS) アドオンを有効にする

既存の AKS クラスターのシナリオでは、既にデプロイされている既存の AKS クラスターに対して OSM アドオンを有効にします。

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>既存の AKS クラスターに対して OSM アドオンを有効にする

AKS OSM アドオンを有効にするには、パラメーター `open-service-mesh` を渡して `az aks enable-addons --addons` コマンドを実行する必要があります

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <resource group name> -n <AKS cluster name>
```

AKS OSM アドオンがインストールされていることを確認するため、下に示す出力のような出力が表示されます。

```json
{- Finished ..
  "aadProfile": null,
  "addonProfiles": {
    "KubeDashboard": {
      "config": null,
      "enabled": false,
      "identity": null
    },
    "openServiceMesh": {
      "config": {},
      "enabled": true,
      "identity": {
...
```

## <a name="validate-the-aks-osm-add-on-installation"></a>AKS OSM アドオンのインストールを検証する

AKS OSM アドオンのすべてのコンポーネントが有効で実行されていることを確認するためには、実行するコマンドがいくつかあります。

まず、クラスターのアドオン プロファイルに対してクエリを実行し、インストールされているアドオンの有効状態を確認します。 次のコマンドは、"true" を返します。

```azurecli-interactive
az aks list -g <resource group name> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

次の `kubectl` コマンドを実行すると、osm-controller の状態がレポートされます。

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on"></a>AKS OSM アドオンにアクセスする

現時点では、configmap を使用して OSM コントローラー構成にアクセスして構成できます。 OSM コントローラーの構成設定を表示するには、`kubectl` を使用して osm-config configmap に対してクエリを実行し、その構成設定を表示します。

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

OSM configmap の出力は次のようになります。

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254/32,168.63.129.16/32,<YOUR_API_SERVER_PUBLIC_IP>/32",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

**permissive_traffic_policy_mode** が **true** に構成されていることに注目してください。 OSM の制限のないトラフィック ポリシー モードは、[SMI](https://smi-spec.io/) トラフィック ポリシーの適用がバイパスされるモードです。 このモードの OSM では、サービス メッシュの一部であるサービスが自動的に検出され、これらのサービスと通信できるように各エンボイ プロキシ サイドカーのトラフィック ポリシー規則がプログラムされます。

> [!WARNING]
> 続行する前に、制限のないトラフィック ポリシー モードが true に設定されていることを確認してください。そうでない場合は、下のコマンドを使用して **true** に変更してください

```OSM Permissive Mode to True
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"true"}}'
```

## <a name="deploy-a-new-application-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Open Service Mesh (OSM) Azure Kubernetes Service (AKS) アドオンによって管理される新しいアプリケーションをデプロイする

### <a name="before-you-begin"></a>開始する前に

このチュートリアルで概要を説明する手順では、AKS クラスター (Kubernetes RBAC が有効になっている Kubernetes `1.19+` 以降) を作成し、クラスターとの `kubectl` 接続を確立し (これらの項目のいずれかについてのヘルプが必要な場合は、[AKS クイックスタート](./kubernetes-walkthrough.md)に関するページを参照してください)、AKS OSM アドオンをインストールしたことを前提としています。

次のリソースがインストールされている必要があります。

- Azure CLI バージョン 2.20.0 以降
- `aks-preview` 拡張機能バージョン 0.5.5 以降
- OSM バージョン v0.8.0 以降
- apt-get install jq

### <a name="create-namespaces-for-the-application"></a>アプリケーションの名前空間を作成する

このチュートリアルでは、次の Kubernetes サービスを含む OSM bookstore アプリケーションを使用します。

- bookbuyer
- bookthief
- bookstore
- bookwarehouse

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

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>OSM によって管理される名前空間をオンボードする

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

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>bookstore アプリケーションを AKS クラスターにデプロイする

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

### <a name="checkpoint-what-got-installed"></a>チェックポイント: インストールされたもの

例の bookstore アプリケーションは、bookbuyer、bookthief、bookstore、bookwarehouse の 4 つのサービスで構成される多層アプリです。 bookbuyer と bookthief の両方のサービスで bookstore サービスと通信し、bookstore サービスから書籍を取得します。 bookstore サービスでは、bookwarehouse サービスから書籍を取得して、bookbuyer と bookthief を指定します。 これは、アプリケーション サービス間の通信を保護および認可するためにサービス メッシュをどのように使用できるかを示すのに適した、単純な多層アプリケーションです。 このチュートリアルの続きとして、Service Mesh Interface (SMI) ポリシーを有効にしたり無効にしたりして、サービスによる OSM 経由での通信の許可および禁止の両方を行うことができるようにします。 下に、bookstore アプリケーション用にインストールされたもののアーキテクチャ図を示します。

![OSM bookbuyer アプリのアーキテクチャ](./media/aks-osm-addon/osm-bookstore-app-arch.png)

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>AKS クラスター内で実行されている bookstore アプリケーションを確認する

現在のところ、bookstore 複数コンテナー アプリケーションをデプロイしましたが、アクセスできるのは AKS クラスター内からだけです。 後のチュートリアルは、イングレス コントローラー経由でクラスターの外部にアプリケーションを公開するのに役立ちます。 ここでは、ポート転送を利用し、AKS クラスター内の bookbuyer アプリケーションにアクセスして、bookstore サービスから書籍を購入していることを検証します。

アプリケーションがクラスター内で実行されていることを確認するには、ポート転送を使用して、bookbuyer と bookbuyer の両方のコンポーネント UI を表示します。

まず、bookbuyer ポッドの名前を取得しましょう

```azurecli-interactive
kubectl get pod -n bookbuyer
```

次のような出力が表示されます。 bookbuyer ポッドには、一意の名前が追加されます。

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

ポート転送セッションが実行されている間、ブラウザーの `http://localhost:8080` から次の URL に移動します。 下のイメージのように、ブラウザーで bookbuyer アプリケーション UI を表示できるようになります。

![OSM bookbuyer アプリの UI イメージ](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

また、書籍の購入数合計が、bookstore v1 サービスで増分し続けていることもわかります。 bookstore v2 サービスはまだデプロイされていません。 SMI トラフィック分割ポリシーをデモンストレーションするときに、bookstore v2 サービスをデプロイします。

また、bookthief サービスについても同じことを確認できます。

```azurecli-interactive
kubectl get pod -n bookthief
```

次のような出力が表示されます。 bookthief ポッドには、一意の名前が追加されます。

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

bookthief ポッドにポート転送します。

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

ブラウザー `http://localhost:8080` から次の URL に移動します。 bookthief が bookstore サービスから現在書籍を盗んでいることがわかります。 後で、bookthief を止めるためのトラフィック ポリシーを実装します。

![OSM bookthief アプリの UI イメージ](./media/aks-osm-addon/osm-bookthief-service-ui.png)

### <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>メッシュの OSM の制限のないトラフィック モードを無効にする

OSM クラスター構成を表示するときに前述したように、OSM 構成では既定で、制限のないトラフィック モード ポリシーが有効になります。 このモードでは、トラフィック ポリシーの適用はバイパスされます。また、OSM では、サービス メッシュの一部であるサービスが自動的に検出され、これらのサービスと通信できるように各エンボイ プロキシ サイドカーのトラフィック ポリシー規則がプログラムされます。

ここでは、制限のないトラフィック モード ポリシーを無効にします。OSM では、各サービスからメッシュ内での通信を許可するために、クラスターにデプロイされた明示的な [SMI](https://smi-spec.io/) ポリシーが必要です。 制限のないトラフィック モードを無効にするには、次のコマンドを実行して、configmap プロパティを更新して値を `true` から `false` に変更します。

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"false"}}'
```

次のような出力が表示されます。 bookthief ポッドには、一意の名前が追加されます。

```Output
configmap/osm-config patched
```

制限のないトラフィック モードが無効になっていることを確認するには、bookbuyer または bookbuyer のいずれかのポッドにポート転送して戻り、ブラウザーで UI を表示し、購入された書籍または盗まれた書籍がもう増分していないかどうかを確認します。 ブラウザーを必ず最新の状態に更新してください。 増分が停止している場合は、ポリシーが正しく適用されています。 bookthief による書籍の盗難をうまくやめさせましたが、bookbuyer が bookstore から購入することも、bookstore が bookwarehouse から書籍を取得することもできません。 次に、通信する必要があるメッシュ内のサービスのみに通信を許可する [SMI](https://smi-spec.io/) ポリシーを実装します。

### <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>Service Mesh Interface (SMI) トラフィック アクセス ポリシーを適用する

メッシュ内のすべての通信を無効にしたので、書籍を購入するために bookbuyer サービスで bookstore サービスと通信できるようにし、販売する書籍を取得するために bookstore サービスで bookwarehouse サービスと通信できるようにしましょう。

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

これで、bookbuyer または bookstore のいずれかのポッドでポート転送セッションを設定できるようになりました。また、購入した書籍と販売した書籍の両方のメトリックが再度増分していることを確認できます。 また、bookthief ポッドがもう書籍を盗むことができなくなっていることを確認するために、このポッドに対しても同じ操作を行うことができます。

### <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>Service Mesh Interface (SMI) トラフィック分割ポリシーを適用する

最後のデモでは、[SMI](https://smi-spec.io/) トラフィック分割ポリシーを作成して、1 つのサービスから複数のサービスへの通信の重みをバックエンドとして構成します。 トラフィック分割機能を使用すると、0 から 100 のスケールでトラフィックを重み付けして、あるサービスへの接続を別のサービスに段階的に移動できます。

下の図は、デプロイされる [SMI](https://smi-spec.io/) トラフィック分割ポリシーの図です。 追加の bookstore バージョン 2 をデプロイした後、bookbuyer からの着信トラフィックを分割し、トラフィックの重みの 25% を bookstore v1 サービスに、75% を bookstore v2 サービスに適用します。

![OSM bookbuyer のトラフィック分割の図](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

bookstore v2 サービスをデプロイします。

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

次に、トラフィック分割ポリシーをデプロイして、bookstore v1 と v2 の 2 つの サービスの間で bookbuyer のトラフィックを分割します。

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

bookbuyer ポッドへのポート転送トンネルを設定します。bookstore v2 サービスから購入した書籍が表示されるようになります。 引き続き購入の増分を確認すると、bookstore v2 サービスを通じて行われる購入の増分が早くなっていることに気づきます。

![OSM bookbuyer の購入された書籍 UI](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)

## <a name="manage-existing-deployed-applications-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Open Service Mesh (OSM) Azure Kubernetes Service (AKS) アドオンによって管理される既存のデプロイ済みアプリケーションを管理する

### <a name="before-you-begin"></a>開始する前に

このチュートリアルで詳しく説明する手順では、AKS クラスターの OSM AKS アドオンを以前に有効にしたことを前提としています。 それ以外の場合は、「[既存の AKS クラスター用に Open Service Mesh (OSM) Azure Kubernetes Service (AKS) アドオンを有効にする](#enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster)」セクションを確認してから続行してください。 また、AKS クラスターは Kubernetes バージョン `1.19+` 以降であること、Kubernetes RBAC が有効になっていること、クラスターとの `kubectl` 接続が確立されていること (これらの項目のいずれかについてのヘルプが必要な場合は、[AKS クイック スタート](./kubernetes-walkthrough.md)に関するページを参照してください)、および AKS OSM アドオンがインストールされていることが必要です。

次のリソースがインストールされている必要があります。

- Azure CLI バージョン 2.20.0 以降
- `aks-preview` 拡張機能バージョン 0.5.5 以降
- OSM バージョン v0.8.0 以降
- apt-get install jq

### <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Open Service Mesh (OSM) の制限のないトラフィック モード ポリシーを確認する

OSM の制限のないトラフィック ポリシー モードは、[SMI](https://smi-spec.io/) トラフィック ポリシーの適用がバイパスされるモードです。 このモードの OSM では、サービス メッシュの一部であるサービスが自動的に検出され、これらのサービスと通信できるように各エンボイ プロキシ サイドカーのトラフィック ポリシー規則がプログラムされます。

クラスターの OSM の現在の制限のないトラフィック モードを確認するには、次のコマンドを実行します。

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

OSM configmap の出力は次のようになります。

```Output
{
  "egress": "true",
  "enable_debug_server": "true",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

**permissive_traffic_policy_mode** が **true** に構成されている場合は、サービス間の通信を中断することなく、名前空間を安全にオンボードできます。 **permissive_traffic_policy_mode** が **false** に構成されている場合は、正しい [SMI](https://smi-spec.io/) トラフィック アクセス ポリシー マニフェストがデプロイされていること、および名前空間にデプロイされている各サービスを表すサービス アカウントを持っていることを確認する必要があります。 「[Open Service Mesh (OSM) の制限のないトラフィック ポリシーを False に構成して既存のデプロイ済みアプリケーションをオンボードする](#onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false)」のガイダンスに従ってください

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Open Service Mesh (OSM) の制限のないトラフィック ポリシーを True に構成して既存のデプロイ済みアプリケーションをオンボードする

最初に、デプロイされたアプリケーションの名前空間を OSM に追加して管理します。

```azurecli-interactive
osm namespace add bookstore
```

次の出力が表示されます。

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

次に、名前空間での現在のポッドのデプロイについて見ていきます。 次のコマンドを実行して、指定された名前空間のポッドを表示します。

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

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Open Service Mesh (OSM) の制限のないトラフィック ポリシーを False に構成して既存のデプロイ済みアプリケーションをオンボードする

制限のないトラフィック ポリシーの OSM 構成が `false` に設定されている場合、OSM では、クラスター内でサービス間の通信を行うために、明示的な [SMI](https://smi-spec.io/) トラフィック アクセス ポリシーがデプロイされている必要があります。 現在、OSM では、サービス間の通信の認可の一環として Kubernetes サービス アカウントも使用されています。 OSM メッシュによる管理時に既存のデプロイ済みのアプリケーションでの通信を保証するには、使用するサービス アカウントが存在することを確認し、サービス アカウント情報を使用してアプリケーションのデプロイを更新して、[SMI](https://smi-spec.io/) トラフィック アクセス ポリシーを適用する必要があります。

#### <a name="verify-kubernetes-service-accounts"></a>Kubernetes サービス アカウントを確認する

アプリケーションのデプロイ先の名前空間に kubernetes サービス アカウントがあるかどうかを確認します。

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

次では、bookbuyer 名前空間に `bookbuyer` という名前のサービス アカウントがあります。

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

#### <a name="view-your-applications-current-deployment-specification"></a>アプリケーションの現在のデプロイ仕様を表示する

前のセクションでサービス アカウントを作成する必要があった場合、アプリケーションのデプロイがデプロイ仕様の特定の `serviceAccountName` で構成されていない可能性があります。次のコマンドを使用して、アプリケーションのデプロイ仕様を表示できます。

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

デプロイの一覧が出力に表示されます。

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

ここでは、[Pod Template] セクションにサービス アカウントが一覧表示されているかどうかを確認するために、デプロイについて説明します。

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

この特定のデプロイでは、[Pod Template] セクションに一覧表示されているデプロイに関連付けられているサービス アカウントがあることがわかります。 このデプロイでは、サービス アカウント bookbuyer が使用されています。 **[Serivce Account:]** プロパティが表示されない場合は、サービス アカウントを使用するようにデプロイが構成されていません。

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

#### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>必要なサービス メッシュ インターフェイス (SMI) ポリシーをデプロイする

認可されたトラフィックがメッシュ内で流れることができるようにするための最後の手順は、アプリケーションに必要な [SMI](https://smi-spec.io/) トラフィック アクセス ポリシーをデプロイすることです。 [SMI](https://smi-spec.io/) トラフィック アクセス ポリシーを使用して実現できる構成の量については、このチュートリアルの範囲には含まれません。ただし、仕様のいくつかの共通コンポーネントについて詳しく説明し、アプリケーションのサービス間の通信を可能にするために単純な TrafficTarget と HTTPRouteGroup の両方のポリシーを構成する方法を示します。

[SMI](https://smi-spec.io/) [**トラフィック アクセス制御**](https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-access/v1alpha3/traffic-access.md#traffic-access-control)仕様では、ユーザーはアプリケーションのアクセス制御ポリシーを定義できます。 ここでは、**TrafficTarget** および **HTTPRoutGroup** API リソースに焦点を合わせます。

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

上記の TrafficTarget 仕様では、`destination` は、宛先ソース サービス用に構成されているサービス アカウントを表します。 前にデプロイに追加されたサービス アカウントは、アタッチ先のデプロイへのアクセスを認可するために使用されます。 この特定の例の `rules` セクションでは、接続を介して許可される HTTP トラフィックの種類を定義します。 HTTP 経由で許可されるトラフィックに固有のものになるように、HTTP ヘッダーの詳細な正規表現パターンを構成することができます。 `sources` セクションは、通信の発信元のサービスです。 この仕様は、bookbuyer が bookstore と通信する必要があることを示しています。

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

### <a name="manage-the-applications-namespace-with-osm"></a>OSM を使用してアプリケーションの名前空間を管理する

次に、名前空間を管理するように OSM を構成し、デプロイを再起動して、エンボイ サイドカー プロキシをアプリケーションに挿入します。

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

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>チュートリアル: Open Service Mesh (OSM) によって管理されるアプリケーションを NGINX イングレスでデプロイする

Open Service Mesh (OSM) は、軽量で拡張可能なクラウド ネイティブ サービス メッシュであり、ユーザーは、高度に動的なマイクロサービス環境のために、すぐに使用できる監視機能を統一して管理し、セキュリティで保護することができます。

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

### <a name="before-you-begin"></a>開始する前に

この記事で詳細を説明する手順では、AKS クラスター (Kubernetes RBAC が有効になっている Kubernetes `1.19+` 以降) を作成し、クラスターとの `kubectl` 接続を確立し (これらの項目のいずれかについてのヘルプが必要な場合は、[AKS クイックスタート](./kubernetes-walkthrough.md)に関するページを参照してください)、AKS OSM アドオンをインストールしたことを前提としています。

次のリソースがインストールされている必要があります。

- Azure CLI バージョン 2.20.0 以降
- `aks-preview` 拡張機能バージョン 0.5.5 以降
- OSM バージョン v0.8.0 以降
- apt-get install jq

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>現在の OSM クラスター構成を表示して確認する

AKS の OSM アドオンが AKS クラスターで有効になったら、osm-config Kubernetes ConfigMap で現在の構成パラメーターを表示できます。 次のコマンドを実行して、ConfigMap プロパティを表示します。

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

出力には、クラスターの現在の OSM 構成が表示されます。

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

**permissive_traffic_policy_mode** が **true** に構成されていることに注目してください。 OSM の制限のないトラフィック ポリシー モードは、[SMI](https://smi-spec.io/) トラフィック ポリシーの適用がバイパスされるモードです。 このモードの OSM では、サービス メッシュの一部であるサービスが自動的に検出され、これらのサービスと通信できるように各エンボイ プロキシ サイドカーのトラフィック ポリシー規則がプログラムされます。

### <a name="create-namespaces-for-the-application"></a>アプリケーションの名前空間を作成する

このチュートリアルでは、次のアプリケーション コンポーネントを持つ OSM bookstore アプリケーションを使用します。

- bookbuyer
- bookthief
- bookstore
- bookwarehouse

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

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>OSM によって管理される名前空間をオンボードする

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

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>bookstore アプリケーションを AKS クラスターにデプロイする

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

### <a name="update-the-bookbuyer-service"></a>bookbuyer サービスを更新する

次のサービス マニフェストを使用して、bookbuyer サービスを正しい受信ポート構成に更新します。

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

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>AKS クラスター内で実行されている bookstore アプリケーションを確認する

現在のところ、bookstore 複数コンテナー アプリケーションをデプロイしましたが、アクセスできるのは AKS クラスター内からだけです。 後で、AKS クラスター外にアプリケーションを公開するために、Azure Application Gateway のイングレス コントローラーを追加します。 アプリケーションがクラスター内で実行されていることを確認するには、ポート転送を使用して、bookbuyer コンポーネント UI を表示します。

まず、bookbuyer ポッドの名前を取得しましょう

```azurecli-interactive
kubectl get pod -n bookbuyer
```

次のような出力が表示されます。 bookbuyer ポッドには、一意の名前が追加されます。

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

ポート転送セッションが実行されている間、ブラウザーの `http://localhost:8080` から次の URL に移動します。 下のイメージのように、ブラウザーで bookbuyer アプリケーション UI を表示できるようになります。

![NGINX UI イメージ用 OSM bookbuyer アプリ](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で NGINX イングレス コントローラーを作成する

イングレス コントローラーは、リバース プロキシ、構成可能なトラフィック ルーティング、および Kubernetes サービスの TLS 終端を提供するソフトウェアです。 個別の Kubernetes サービスのイングレス ルールとルートを構成するには、Kubernetes イングレス リソースが使われます。 イングレス コントローラーとイングレス ルールを使用すれば、1 つの IP アドレスで Kubernetes クラスター内の複数のサービスにトラフィックをルーティングできます。

イングレス コントローラーを使用して、OSM によって管理されるアプリケーションをインターネットに公開します。 イングレス コントローラーを作成するには、Helm を使用して nginx-ingress をインストールします。 追加された冗長性については、NGINX イングレス コントローラーの 2 つのレプリカが `--set controller.replicaCount` パラメーターでデプロイされています。 イングレス コントローラーのレプリカの実行から十分にメリットを享受するには、AKS クラスターに複数のノードが存在していることを確認します。

イングレス コントローラーも Linux ノード上でスケジュールする必要があります。 Windows Server ノードでは、イングレス コントローラーを実行しないでください。 ノード セレクターは、`--set nodeSelector` パラメーターを使用して指定され、Linux ベース ノード上で NGINX イングレス コントローラーを実行するように Kubernetes スケジューラに指示されます。

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

次の出力例に示すように、NGINX イングレス コントローラーに対して Kubernetes ロード バランサー サービスが作成されると、動的パブリック IP アドレスが割り当てられます。

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

イングレス ルールはまだ作成されていないため、内部 IP アドレスを参照すると、NGINX イングレス コントローラーの既定の 404 ページが表示されます。 イングレス ルールは、後続の手順で構成します。

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>bookbuyer サービスをインターネットに公開する

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

### <a name="view-the-nginx-logs"></a>NGINX ログを表示する

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

### <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>NGINX サービスと bookbuyer サービスを外部で表示する

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

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>チュートリアル: Azure Application Gateway イングレス AKS アドオンを使用して、Open Service Mesh (OSM) によって管理されるアプリケーションをデプロイする

Open Service Mesh (OSM) は、軽量で拡張可能なクラウド ネイティブ サービス メッシュであり、ユーザーは、高度に動的なマイクロサービス環境のために、すぐに使用できる監視機能を統一して管理し、セキュリティで保護することができます。

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

### <a name="before-you-begin"></a>開始する前に

この記事で詳細を説明する手順では、AKS クラスター (Kubernetes RBAC が有効になっている Kubernetes `1.19+` 以降) を作成し、クラスターとの `kubectl` 接続を確立し (これらの項目のいずれかについてのヘルプが必要な場合は、[AKS クイックスタート](./kubernetes-walkthrough.md)に関するページを参照してください)、AKS OSM アドオンをインストールしたことを前提としています。また、イングレスの新しい Azure Application Gateway を作成します。

次のリソースがインストールされている必要があります。

- Azure CLI バージョン 2.20.0 以降
- `aks-preview` 拡張機能バージョン 0.5.5 以降
- (Azure Vnet にアタッチされた) Azure CNI ネットワークを使用する AKS クラスター バージョン 1.19+
- OSM バージョン v0.8.0 以降
- apt-get install jq

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>現在の OSM クラスター構成を表示して確認する

AKS の OSM アドオンが AKS クラスターで有効になったら、osm-config Kubernetes ConfigMap で現在の構成パラメーターを表示できます。 次のコマンドを実行して、ConfigMap プロパティを表示します。

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

出力には、クラスターの現在の OSM 構成が表示されます。

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

**permissive_traffic_policy_mode** が **true** に構成されていることに注目してください。 OSM の制限のないトラフィック ポリシー モードは、[SMI](https://smi-spec.io/) トラフィック ポリシーの適用がバイパスされるモードです。 このモードの OSM では、サービス メッシュの一部であるサービスが自動的に検出され、これらのサービスと通信できるように各エンボイ プロキシ サイドカーのトラフィック ポリシー規則がプログラムされます。

### <a name="create-namespaces-for-the-application"></a>アプリケーションの名前空間を作成する

このチュートリアルでは、次のアプリケーション コンポーネントを持つ OSM bookstore アプリケーションを使用します。

- bookbuyer
- bookthief
- bookstore
- bookwarehouse

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

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>OSM によって管理される名前空間をオンボードする

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

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>bookstore アプリケーションを AKS クラスターにデプロイする

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

### <a name="update-the-bookbuyer-service"></a>bookbuyer サービスを更新する

次のサービス マニフェストを使用して、bookbuyer サービスを正しい受信ポート構成に更新します。

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

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>AKS クラスター内で実行されている bookstore アプリケーションを確認する

現在のところ、bookstore 複数コンテナー アプリケーションをデプロイしましたが、アクセスできるのは AKS クラスター内からだけです。 後で、AKS クラスター外にアプリケーションを公開するために、Azure Application Gateway のイングレス コントローラーを追加します。 アプリケーションがクラスター内で実行されていることを確認するには、ポート転送を使用して、bookbuyer コンポーネント UI を表示します。

まず、bookbuyer ポッドの名前を取得しましょう

```azurecli-interactive
kubectl get pod -n bookbuyer
```

次のような出力が表示されます。 bookbuyer ポッドには、一意の名前が追加されます。

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

ポッドの名前を取得したら、port-forward コマンドを使用して、ローカル システムから AKS クラスター内のアプリケーションにトンネルを設定できるようになります。 次のコマンドを実行して、ローカル システム ポート 8080 のポート転送を設定します。 ここでも、特定の bookbuyer ポッド名を使用します。

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

次のような出力が表示されます。

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

ポート転送セッションが実行されている間、ブラウザーの `http://localhost:8080` から次の URL に移動します。 下のイメージのように、ブラウザーで bookbuyer アプリケーション UI を表示できるようになります。

![App Gateway の UI イメージ用 OSM bookbuyer アプリ](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application-outside-the-aks-cluster"></a>AKS クラスター外で bookbuyer アプリケーションを公開するために Azure Application Gateway を作成する

> [!NOTE]
> 次の手順では、イングレスに使用する Azure Application Gateway の新しいインスタンスを作成します。 使用したい既存の Azure Application Gateway がある場合は、Application Gateway イングレス コントローラー アドオンを有効にするためのセクションに進んでください。

#### <a name="deploy-a-new-application-gateway"></a>新しい Application Gateway のデプロイ

> [!NOTE]
> 既存の AKS クラスターに対して Application Gateway イングレス コントローラー アドオンを有効にするための既存のドキュメントを参照しています。 OSM の資料に合うようにいくつかの変更が行われました。 この件に関する詳細なドキュメントについては、[こちら](https://docs.microsoft.com/azure/application-gateway/tutorial-ingress-controller-add-on-existing)を参照してください。

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

#### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>Azure CLI を使用して既存の AKS クラスターの AGIC アドオンを有効にする

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

#### <a name="peer-the-two-virtual-networks-together"></a>2 つの仮想ネットワークを一緒にピアリングする

AKS クラスターを独自の仮想ネットワークにデプロイし、別の仮想ネットワークに Application Gateway をデプロイしたため、Application Gateway からクラスター内のポッドにトラフィックを流すには、2 つの仮想ネットワークを一緒にピアリングする必要があります。 2 つの仮想ネットワークをピアリングするには、接続が双方向になるよう、Azure CLI コマンドを 2 回個別に実行する必要があります。 最初のコマンドは、Application Gateway 仮想ネットワークから AKS 仮想ネットワークへのピアリング接続を作成します。2 番目のコマンドは、その逆方向にピアリング接続を作成します。

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>bookbuyer サービスをインターネットに公開する

AKS クラスターに次のイングレス マニフェストを適用して、Azure Application Gateway 経由で bookbuyer サービスをインターネットに公開します。

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
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

イングレス マニフェストのホスト名はテストに使用される擬似名であるため、DNS 名はインターネット上では使用できません。 代わりに、curl プログラムを使用して、hostname ヘッダーを Azure Application Gateway パブリック IP アドレスに渡し、bookbuyer サービスに正常に接続して 200 コードを受信することもできます。

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

### <a name="troubleshooting"></a>トラブルシューティング

- [AGIC トラブルシューティングのドキュメント](https://docs.microsoft.com/azure/application-gateway/ingress-controller-troubleshoot)
- [その他のトラブルシューティング ツールは、AGIC の GitHub リポジトリで入手可能](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)

## <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Azure Monitor と Application Insights を使用した Open Service Mesh (OSM) の監視

Azure Monitor と Azure Application Insights の両方では、クラウドおよびオンプレミス環境からテレメトリを収集、分析、対応するための包括的なソリューションを提供することで、アプリケーションやサービスの可用性とパフォーマンスを最大限に高めます。

OSM AKS アドオンは、これらの Azure サービスの両方に緊密に統合され、OSM メトリックによって提供される重要な KPI を表示して対応するためにシームレスな Azure のエクスペリエンスが提供されます。 OSM AKS アドオン用にこれらのサービスを有効にして構成する方法の詳細については、[OSM の Azure Monitor](https://aka.ms/azmon/osmpreview) に関するページを参照してください。

## <a name="tutorial-manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>チュートリアル: Prometheus、Grafana、Jaeger を手動でデプロイして、監視のために Open Service Mesh (OSM) メトリックを表示する

> [!WARNING]
> Prometheus、Grafana、Jaeger のインストールは、これらのツールを使用して OSM メトリック データを表示する方法を示すための一般的なガイダンスとして提供されています。 インストール ガイドは、運用環境でのセットアップには使用されません。 インストールをどのようにしてニーズに合わせるのかが最適かについては、各ツールのドキュメントを参照してください。 最も注目すべき点は、永続的なストレージがないことです。つまり、Prometheus、Grafana、Jaeger のポッドが終了すると、すべてのデータが失われます。

Open Service Mesh (OSM) では、メッシュ内のすべてのトラフィックに関連する詳細なメトリックが生成されます。 これらのメトリックによって、ユーザーがアプリケーションのトラブルシューティング、保守、分析を行うのに役立つメッシュ内のアプリケーションの動作に関する洞察が提供されます。

現時点では、OSM ではサイドカー プロキシ (エンボイ) からメトリックを直接収集します。 OSM によって、メッシュ内のすべてのサービスの受信および送信トラフィックについての豊富なメトリックが提供されます。 ユーザーはこれらのメトリックを使用して、トラフィックの全体的な量、トラフィック内のエラー、要求の応答時間に関する情報を取得できます。

OSM では Prometheus を使用して、メッシュ内で実行されているすべてのアプリケーションについて、一貫性のあるトラフィック メトリックと統計情報を収集して保存します。 Prometheus はオープンソースの監視およびアラート ツールキットであり、Kubernetes およびサービス メッシュ環境で一般的に使用されます (ただし、これらには限定されません)。

メッシュの一部である各アプリケーションは、Prometheus 形式でメトリック (プロキシ メトリック) を公開するエンボイ サイドカーを含むポッド内で実行されます。 さらに、メッシュの一部であるすべてのポッドに Prometheus の注釈があるため、Prometheus サーバーでアプリケーションを動的にスクレイピングすることができます。 このメカニズムにより、新しい名前空間/ポッド/サービスがメッシュに追加されるたびに、メトリックのスクレイピングが自動的に有効になります。

オープンソースの視覚化および分析ソフトウェアである Grafana を使用して OSM メトリックを表示できます。 メトリックのクエリ、視覚化、操作、またメトリックに基づいたアラートの作成を行うために使用できます。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
>
> - Prometheus インスタンスを作成してデプロイする
> - Prometheus スクレイピングを許可するように OSM を構成する
> - Prometheus の Configmap を更新する
> - Grafana インスタンスを作成してデプロイする
> - Prometheus データ ソースで Grafana を構成する
> - Grafana の OSM ダッシュボードをインポートする
> - Jaeger インスタンスを作成してデプロイする
> - OSM の Jaeger トレースを構成する

### <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>OSM の Prometheus インスタンスをデプロイして構成する

Helm を使用して Prometheus インスタンスをデプロイします。 次のコマンドを実行して、Helm を介して Prometheus をインストールします。

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

インストールが正常に完了した場合は、下のような出力が表示されます。 Prometheus サーバー ポートとクラスター DNS 名をメモしておきます。 この情報は、後で Grafana のデータ ソースとして Prometheus を構成するために使用されます。

```Output
NAME: stable
LAST DEPLOYED: Fri Mar 26 13:34:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Prometheus server can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-server.default.svc.cluster.local


Get the Prometheus server URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9090


The Prometheus alertmanager can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-alertmanager.default.svc.cluster.local


Get the Alertmanager URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=alertmanager" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9093
#################################################################################
######   WARNING: Pod Security Policy has been moved to a global property.  #####
######            use .Values.podSecurityPolicy.enabled with pod-based      #####
######            annotations                                               #####
######            (e.g. .Values.nodeExporter.podSecurityPolicy.annotations) #####
#################################################################################


The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
stable-prometheus-pushgateway.default.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9091

For more information on running Prometheus, visit:
https://prometheus.io/
```

#### <a name="configure-osm-to-allow-prometheus-scraping"></a>Prometheus スクレイピングを許可するように OSM を構成する

OSM コンポーネントが Prometheus スクレイピング用に構成されていることを確認するには、osm-config 構成ファイルにある **prometheus_scraping** 構成を確認します。 次のコマンドを使用して構成を表示します。

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data.prometheus_scraping'
```

OSM が Prometheus スクレイピング用に構成されている場合、前のコマンドの出力では `true` が返されます。 戻り値が `false` の場合は、構成を `true` に更新する必要があります。 次のコマンドを実行して、OSM Prometheus スクレイピングを **オン** にします。

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"prometheus_scraping":"true"}}'
```

次の出力が表示されます。

```Output
configmap/osm-config patched
```

#### <a name="update-the-prometheus-configmap"></a>Prometheus の Configmap を更新する

Prometheus の既定のインストールには、2 つの Kubernetes configmap が含まれています。 次のコマンドを使用して、Prometheus configmap の一覧を表示できます。

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

**stable-prometheus-server** configmap にある prometheus.yml 構成を次の OSM 構成に置き換える必要があります。 このタスクを実行するには、いくつかのファイル編集手法があります。 簡単かつ安全な方法は、configmap をエクスポートし、そのコピーをバックアップ用に作成した後、Visual Studio Code などのエディターを使用してそれを編集することです。

> [!NOTE]
> Visual Studio Code インストールされていない場合は、[こちら](https://code.visualstudio.com/Download)からダウンロードしてインストールすることができます。

まず、**stable-prometheus-server** configmap をエクスポートしてから、バックアップ用のコピーを作成してみましょう。

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

次に、Visual Studio Code を使用してファイルを開いて編集します。

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

Visual Studio Code エディターで configmap を開いたら、prometheus.yml ファイルを下の OSM 構成に置き換えて、ファイルを保存します。

> [!WARNING]
> yaml ファイルのインデント構造を保持することが非常に重要です。 yaml ファイル構造を変更すると、configmap を再適用できなくなる可能性があります。

```OSM Prometheus Configmap Configuration
prometheus.yml: |
    global:
      scrape_interval: 10s
      scrape_timeout: 10s
      evaluation_interval: 1m

    scrape_configs:
      - job_name: 'kubernetes-apiservers'
        kubernetes_sd_configs:
        - role: endpoints
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
          # TODO need to remove this when the CA and SAN match
          insecure_skip_verify: true
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(apiserver_watch_events_total|apiserver_admission_webhook_rejection_count)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
          action: keep
          regex: default;kubernetes;https

      - job_name: 'kubernetes-nodes'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics

      - job_name: 'kubernetes-pods'
        kubernetes_sd_configs:
        - role: pod
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(envoy_server_live|envoy_cluster_upstream_rq_xx|envoy_cluster_upstream_cx_active|envoy_cluster_upstream_cx_tx_bytes_total|envoy_cluster_upstream_cx_rx_bytes_total|envoy_cluster_upstream_cx_destroy_remote_with_active_rq|envoy_cluster_upstream_cx_connect_timeout|envoy_cluster_upstream_cx_destroy_local_with_active_rq|envoy_cluster_upstream_rq_pending_failure_eject|envoy_cluster_upstream_rq_pending_overflow|envoy_cluster_upstream_rq_timeout|envoy_cluster_upstream_rq_rx_reset|^osm.*)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        - source_labels: [__meta_kubernetes_namespace]
          action: replace
          target_label: source_namespace
        - source_labels: [__meta_kubernetes_pod_name]
          action: replace
          target_label: source_pod_name
        - regex: '(__meta_kubernetes_pod_label_app)'
          action: labelmap
          replacement: source_service
        - regex: '(__meta_kubernetes_pod_label_osm_envoy_uid|__meta_kubernetes_pod_label_pod_template_hash|__meta_kubernetes_pod_label_version)'
          action: drop
        # for non-ReplicaSets (DaemonSet, StatefulSet)
        # __meta_kubernetes_pod_controller_kind=DaemonSet
        # __meta_kubernetes_pod_controller_name=foo
        # =>
        # workload_kind=DaemonSet
        # workload_name=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          target_label: source_workload_kind
        - source_labels: [__meta_kubernetes_pod_controller_name]
          action: replace
          target_label: source_workload_name
        # for ReplicaSets
        # __meta_kubernetes_pod_controller_kind=ReplicaSet
        # __meta_kubernetes_pod_controller_name=foo-bar-123
        # =>
        # workload_kind=Deployment
        # workload_name=foo-bar
        # deplyment=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          regex: ^ReplicaSet$
          target_label: source_workload_kind
          replacement: Deployment
        - source_labels:
          - __meta_kubernetes_pod_controller_kind
          - __meta_kubernetes_pod_controller_name
          action: replace
          regex: ^ReplicaSet;(.*)-[^-]+$
          target_label: source_workload_name

      - job_name: 'smi-metrics'
        kubernetes_sd_configs:
        - role: pod
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: 'envoy_.*osm_request_(total|duration_ms_(bucket|count|sum))'
          action: keep
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_(\d{3})_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: response_code
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_total
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_total
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_total)
          target_label: __name__

        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_duration_ms_(bucket|sum|count))
          target_label: __name__

      - job_name: 'kubernetes-cadvisor'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(container_cpu_usage_seconds_total|container_memory_rss)'
          action: keep
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
```

次のコマンドを使用して、更新された configmap yaml ファイルを適用します。

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> 必要な kubernetes 注釈が見つからないことに関するメッセージが表示される場合があります。 これは、ここでは無視できます。

#### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>Prometheus が OSM メッシュおよび API エンドポイントをスクレイピングするように構成されていることを確認します

Prometheus が OSM メッシュと API エンドポイントをスクレイピングするように正しく構成されていることを確認するために、Prometheus ポッドにポート転送し、ターゲット構成を表示します。 次のコマンドを実行します。

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

ブラウザーで `http://localhost:9090/targets` を開きます

下にスクロールすると、下の図のように定義されている他の OSM メトリックに加えて、すべての SMI メトリック エンドポイントの状態が **[アップ]** と表示されます。

![OSM Prometheus ターゲット メトリックの UI イメージ](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

### <a name="deploy-and-configure-a-grafana-instance-for-osm&quot;></a>OSM の Grafana インスタンスをデプロイして構成する

Helm を使用して Grafana インスタンスをデプロイします。 次のコマンドを実行して、Helm を介して Grafana をインストールします。

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

次に、Grafana サイトにログインするための既定の Grafana パスワードを取得します。

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath=&quot;{.data.admin-password}&quot; | base64 --decode ; echo
```

Grafana のパスワードを書き留めます。

次に、Grafana ポッドを取得して、Grafana ダッシュボードをポート転送してログインします。

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l &quot;app.kubernetes.io/name=grafana&quot; -o jsonpath=&quot;{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

ブラウザーで `http://localhost:3000` を開きます

下の図に示すログイン画面で、ユーザー名として「**admin**」と入力し、前にキャプチャした Grafana パスワードを使用します。

![OSM Grafana ログイン ページの UI イメージ](./media/aks-osm-addon/osm-grafana-ui-login.png)

#### <a name="configure-the-grafana-prometheus-data-source"></a>Grafana Prometheus データ ソースを構成する

Grafana に正常にログインしたら、次の手順では、Grafana のデータ ソースとして Prometheus を追加します。 これを行うには、左側のメニューの [構成] アイコンに移動し、下に示すように [データ ソース] を選択します。

![OSM Grafana [データ ソース] ページの UI イメージ](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

**[データ ソースの追加]** ボタンをクリックし、[時系列データベース] で [Prometheus] を選択します。

![OSM Grafana データ ソースの選択ページの UI イメージ](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

**[下の Prometheus データ ソースの構成]** ページで、[HTTP URL] 設定の Prometheus サービスについて Kubernetes クラスター FQDN を入力します。 既定の FQDN は `stable-prometheus-server.default.svc.cluster.local` である必要があります。 Prometheus サービス エンドポイントを入力したら、ページの下部までスクロールして、 **[保存 & テスト]** を選択します。 データ ソースが機能していることを示す緑色のチェック ボックスが表示されます。

#### <a name="importing-osm-dashboards"></a>OSM ダッシュボードのインポート

OSM ダッシュボードは、次のどちらを使用しても利用できます。

- [リポジトリ](https://github.com/grafana/grafana)。Web 管理ポータルを使用して json blob としてインポートできます
- または [Grafana.com からオンラインで](https://grafana.com/grafana/dashboards/14145)

ダッシュボードをインポートするには、左側のメニューの `+` 記号を探し、[`import`] を選択します。
ダッシュボードは、`Grafana.com` の ID で直接インポートできます。 たとえば、`OSM Mesh Details` ダッシュボードで ID `14145` が使用されている場合、フォームで ID を直接使用して、[`import`] を選択します。

![OSM Grafana ダッシュボードの [インポート] ページの UI イメージ](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

[インポート] を選択するとすぐに、インポートしたダッシュボードが自動的に表示されます。

![OSM Grafana ダッシュボードの [メッシュの詳細] ページの UI イメージ](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

### <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>OSM の Kubernetes に Jaeger Operator をデプロイして構成する

[Jaeger](https://www.jaegertracing.io/) は、分散システムの監視とトラブルシューティングに使用されるオープンソースのトレース システムです。 OSM を新しいインスタンスとして使用してこれをデプロイすることも、独自のインスタンスを使用することもできます。 次の手順では、Jaeger の新しいインスタンスを AKS クラスターの `jaeger` 名前空間にデプロイします。

#### <a name="deploy-jaeger-to-the-aks-cluster"></a>Jaeger を AKS クラスターにデプロイする

Jaeger をインストールするには、次のマニフェストを適用します。

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
        - name: jaeger
          image: jaegertracing/all-in-one
          args:
          - --collector.zipkin.host-port=9411
          imagePullPolicy: IfNotPresent
          ports:
          - containerPort: 9411
          resources:
            limits:
              cpu: 500m
              memory: 512M
            requests:
              cpu: 100m
              memory: 256M
---
kind: Service
apiVersion: v1
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  selector:
    app: jaeger
  ports:
  - protocol: TCP
    # Service port and target port are the same
    port: 9411
  type: ClusterIP
EOF
```

```Output
deployment.apps/jaeger created
service/jaeger created
```

#### <a name="enable-tracing-for-the-osm-add-on"></a>OSM アドオンのトレースを有効にする

次に、OSM アドオンのトレースを有効にする必要があります。

> [!NOTE]
> 現時点では、トレース プロパティは現在 osm-config configmap では表示されません。 これは、OSM AKS アドオンの新しいリリースで表示されるようになります。

OSM アドオンのトレースを有効にするには、次のコマンドを使用します。

```azurecli-interactive
kubectl patch configmap osm-config -n kube-system -p '{"data":{"tracing_enable":"true", "tracing_address":"jaeger.jaeger.svc.cluster.local", "tracing_port":"9411", "tracing_endpoint":"/api/v2/spans"}}' --type=merge
```

```Output
configmap/osm-config patched
```

#### <a name="view-the-jaeger-ui-with-port-forwarding"></a>ポート フォワーディングを使用して Jaeger UI を表示する

Jaeger の UI はポート 16686 で実行されています。 Web UI を表示するには、kubectl port-forward を使用できます。

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

ブラウザーにサービスのドロップダウンが表示され、bookstore のデモによってデプロイされたさまざまなアプリケーションから選択できます。 サービスを選択して、それからのすべての範囲を表示します。 たとえば、1 時間のルックバックを使用して bookbuyer を選択すると、bookstore-v1 および bookstore-v2 との相互作用を時間で並べ替えて表示できます。

![OSM Jaeger トレース ページの UI イメージ](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

項目を選択すると、それが詳細に表示されます。 複数の項目を選択して、トレースを比較します。 たとえば、特定の時点での bookstore および bookstore-v2 と bookbuyer との相互作用を比較できます。

また、[システム アーキテクチャ] タブを選択して、さまざまなアプリケーションがどのように相互作用および通信しているかをグラフに表示することもできます。 これにより、アプリケーション間でのトラフィックの流れを把握できます。

![OSM Jaeger [システム アーキテクチャ] の UI イメージ](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)

## <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Open Service Mesh (OSM) AKS アドオンのトラブルシューティング ガイド

OSM AKS アドオンをデプロイすると、問題が発生することがあります。 次のガイドでは、エラーのトラブルシューティングを行い、一般的な問題を解決する方法について説明します。

### <a name="verifying-and-troubleshooting-osm-components"></a>OSM コンポーネントの検証とトラブルシューティング

#### <a name="check-osm-controller-deployment"></a>OSM コントローラーのデプロイを確認する

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

正常な OSM コントローラーは次のようになります。

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

#### <a name="check-the-osm-controller-pod"></a>OSM コントローラー ポッドを確認する

```azurecli-interactive
kubectl get pods -n kube-system --selector app=osm-controller
```

正常な OSM ポッドは次のようになります。

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

ある時点で 1 つのコントローラーが無効にされましたが、[READY] が [1/1]、[Running]、さらに再起動が 0 回の別のものがあります。 [READY] 列が [1/1] 以外の場合、サービス メッシュは破損した状態です。
[0/1] の [READY] 列は、コントロール プレーン コンテナーがクラッシュしていることを示します。ログを取得する必要があります。 下の Azure サポート センターからの OSM コントローラー ログの取得に関するセクションを参照してください。 / の後に 1 より大きい数が示された [READY] 列は、サイドカーがインストールされていることを示します。 OSM コントローラーは、多くの場合、アタッチされているサイドカーでは動作しません。

> [!NOTE]
> バージョン v 0.8.2 の時点では、OSM コントローラーは HA モードではなく、レプリカ数 1 (単一ポッド) でデプロイされて実行されます。 ポッドには正常性プローブがあり、必要に応じて kubelet によって再起動されます。

#### <a name="check-osm-controller-service"></a>OSM コントローラー サービスの確認

```azurecli-interactive
kubectl get service -n kube-system osm-controller
```

正常な OSM コントローラー サービスは次のようになります。

```Output
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> [!NOTE]
> CLUSTER-IP は異なります。 サービス の NAME と PORT(S) は、上記の例と同じである必要があります。

#### <a name="check-osm-controller-endpoints"></a>OSM コントローラー エンドポイントを確認する

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

正常な OSM コントローラー エンドポイントは次のようになります。

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

#### <a name="check-osm-injector-deployment"></a>OSM インジェクターのデプロイを確認する

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

正常な OSM インジェクターのデプロイは次のようになります。

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-pod"></a>OSM インジェクター ポッドを確認する

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

正常な OSM インジェクター ポッドは次のようになります。

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-service"></a>OSM インジェクター サービスを確認する

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

正常な OSM インジェクター サービスは次のようになります。

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

#### <a name="check-osm-endpoints"></a>OSM エンドポイントを確認する

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

正常な OSM エンドポイントは次のようになります。

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

#### <a name="check-validating-and-mutating-webhooks"></a>Validating および Mutating Webhook を確認する

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

正常な OSM Validating Webhook は次のようになります。

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      81m
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration --selector app=osm-injector
```

正常な OSM Mutating Webhook は次のようになります。

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>Validating Webhook のサービスと CA バンドルを確認する

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

適切に構成された Validating Webhook 構成は次とまったく同じになります。

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>Mutating Webhook のサービスと CA バンドルを確認する

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

適切に構成された Mutating Webhook 構成は次とまったく同じになります。

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

#### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>OSM コントローラーによって Validating (または Mutating) Webhook に CA バンドルが提供されているかどうかを確認する

> [!NOTE]
> V 0.8.2 の時点では、Validating Webhook が AKS RP によってインストールされ、これが存在することを AKS 競合管理モジュールで確認するが、CA バンドルを提供するのは OSM コントローラーであるということを認識することが重要です。

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```Example Output
1845
```

この数値は、CA バンドルのバイト数またはサイズを示しています。 この値が空である 0、または 1000 未満の数値である場合は、CA バンドルが正しくプロビジョニングされていないことを示します。 正しい CA バンドルがない場合、Validating Webhook によってエラーが発生し、ユーザーは、kube-system 名前空間で osm-config ConfigMap に変更を加えることが禁止されます。

CA バンドルが正しくない場合のエラーの例を示します。

- osm-config ConfigMap を変更しようとします。

```azurecli-interactive
kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
```

- エラー:

```
Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.kube-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
```

**Validating** Webhook 構成に無効な証明書がある場合の回避策:

- オプション 1 - OSM コントローラーを再起動します。これで、OSM コントローラーが再起動されます。 起動時に、Mutating と Validating の両方の Webhook の CA バンドルが上書きされます。

```azurecli-interactive
kubectl rollout restart deployment -n kube-system osm-controller
```

- オプション 2 - オプション 2。 Validating Webhook の削除 - Validating Webhook を削除すると、`osm-config` ConfigMap の変更が検証されなくなります。 パッチはすべて適用されます。 AKS の競合回避モジュールによって、ある時点で Validating Webhook が存在することが確認され、再作成されます。 CA バンドルを迅速に書き換えるには、OSM コントローラーを再起動する必要がある場合があります。

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm
```

- オプション 3 - 削除とパッチ: 次のコマンドを実行すると、Validating Webhook が削除され、値を追加できるようになり、パッチの適用が直ちに試行されます。 ほとんどの場合、AKS の競合回避モジュールでは、Validating Webhook を調整して復元するための十分な時間がないため、最後の手段として変更を適用する機会が得られます。

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm; kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
```

#### <a name="check-the-osm-config-configmap"></a>`osm-config` **ConfigMap** を確認する

> [!NOTE]
> OSM コントローラーでは、kube-system 名前空間に `osm-config` ConfigMap が存在している必要はありません。 コントローラーには構成に対して妥当な既定値があり、これがなくても動作できます。

存在するかどうかを確認します。

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config
```

osm-config ConfigMap の内容を確認する

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config -o json | jq '.data'
```

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.20.233",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

`osm-config` ConfigMap の値:

| キー                              | 種類   | 使用できる値                                          | 既定値                          | 機能                                                                                                                                                                                                                                |
| -------------------------------- | ------ | ------------------------------------------------------- | -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| egress                           | [bool]   | true、false                                             | `"false"`                              | メッシュでのエグレスを有効にします。                                                                                                                                                                                                             |
| enable_debug_server              | [bool]   | true、false                                             | `"true"`                               | osm-controller ポッドでデバッグ エンドポイントを有効にして、プロキシ接続、証明書、SMI ポリシーなどのメッシュに関する情報を一覧表示します。                                                                                    |
| enable_privileged_init_container | [bool]   | true、false                                             | `"false"`                              | メッシュ内のポッドの特権のある init コンテナーを有効にします。 false の場合、init コンテナーには NET_ADMIN のみが含まれます。                                                                                                                                   |
| envoy_log_level                  | string | trace、debug、info、warning、warn、error、critical、off | `"error"`                              | エンボイ プロキシ サイドカーのログの詳細を設定します。メッシュに参加している新たに作成されたポッドにのみ適用されます。 既存のポッドのログ レベルを更新するには、`kubectl rollout restart` でデプロイを再起動します。                            |
| outbound_ip_range_exclusion_list | string | a.b.c.d/x 形式の IP 範囲のコンマ区切り一覧 | `-`                                    | サイドカー プロキシによる送信トラフィックの傍受から除外する IP アドレス範囲のグローバル一覧。                                                                                                                                    |
| permissive_traffic_policy_mode   | [bool]   | true、false                                             | `"false"`                              | `true` に設定する と、メッシュで allow-all モードが有効になります。つまり、メッシュにトラフィック ポリシーは適用されません。 `false` に設定すると、メッシュで deny-all トラフィック ポリシーが有効になります。つまり、サービスで通信するためには `SMI Traffic Target` が必要です。 |
| prometheus_scraping              | [bool]   | true、false                                             | `"true"`                               | サイドカー プロキシでの Prometheus メトリックのスクレイピングを有効にします。                                                                                                                                                                                 |
| service_cert_validity_duration   | string | 24h、1h30m (任意の期間)                          | `"24h"`                                | サービス証明書の有効期間を設定します。これは、それぞれがオプションの分数と単位のサフィックスを持つ 10 進数のシーケンスとして表されます。                                                                                             |
| tracing_enable                   | [bool]   | true、false                                             | `"false"`                              | メッシュの Jaeger トレースを有効にします。                                                                                                                                                                                                    |
| tracing_address                  | string | jaeger.mesh-namespace.svc.cluster.local                 | `jaeger.kube-system.svc.cluster.local` | トレースが有効な場合の Jaeger デプロイのアドレス。                                                                                                                                                                                |
| tracing_endpoint                 | string | /api/v2/spans                                           | /api/v2/spans                          | トレースが有効な場合のトレース データのエンドポイント。                                                                                                                                                                                          |
| tracing_port                     | INT    | 0 以外の整数値                              | `"9411"`                               | トレースが有効になっているポート。                                                                                                                                                                                                       |
| use_https_ingress                | [bool]   | true、false                                             | `"false"`                              | メッシュでの HTTPS イングレスを有効にします。                                                                                                                                                                                                      |
| config_resync_interval           | string | 1 分未満でこれは無効になります                            | 0 (無効)                           | 1m (60s) を超える値を指定した場合、OSM コントローラーでは、指定した間隔で、接続されている各エンボイに対して使用可能なすべての構成が送信されます                                                                                                    |

#### <a name="check-namespaces"></a>名前空間を確認する

> [!NOTE]
> kube-system 名前空間は、サービス メッシュには参加しません。また、下のキーと値を使用してラベル付けしたり注釈を付けたりすることはありません。

`osm namespace add` コマンドを使用して、特定のサービス メッシュに名前空間を参加させます。
K8s 名前空間がメッシュの一部である場合 (またはメッシュの一部にする場合) は、次に該当する必要があります。

次を使用して注釈を表示する

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.annotations'
```

次の注釈が存在する必要があります。

```Output
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```

次を使用してラベルを表示する

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.labels'
```

次のラベルが存在する必要があります。

```Output
{
  "openservicemesh.io/monitored-by": "osm"
}
```

名前空間に `"openservicemesh.io/sidecar-injection": "enabled"` の注釈が付けられていない場合、または `"openservicemesh.io/monitored-by": "osm"` でラベル付けされていない場合、OSM インジェクターではエンボイ サイドカーは追加されません。

> 注: `osm namespace add` を呼び出すと、**新しい** ポッドのみにエンボイ サイドカーが挿入されます。 `kubectl rollout restart deployment ...` を使用して既存のポッドを再起動する必要があります

#### <a name="verify-the-smi-crds"></a>SMI CRD を確認します。

クラスターに必要な CRD があるかどうかを確認します。

```azurecli-interactive
kubectl get crds
```

クラスターに次のものがインストールされている必要があります。

- httproutegroups.specs.smi-spec.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io
- udproutes.specs.smi-spec.io

次のコマンドを使用して、インストールされている CRD のバージョンを取得します。

```azurecli-interactive
for x in $(kubectl get crds --no-headers | awk '{print $1}' | grep 'smi-spec.io'); do
    kubectl get crd $x -o json | jq -r '(.metadata.name, "----" , .spec.versions[].name, "\n")'
done
```

予想される出力:

```Output
httproutegroups.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


tcproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


trafficsplits.split.smi-spec.io
----
v1alpha2


traffictargets.access.smi-spec.io
----
v1alpha3
v1alpha2
v1alpha1


udproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1
```

OSM コントローラー v 0.8.2 には、次のバージョンが必要です。

- traffictargets.access.smi-spec.io - [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io - サポートされません
- trafficsplits.split.smi-spec.io - [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*.metrics.smi-spec.io - [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)

CRD がない場合は、次のコマンドを使用してクラスターにこれらをインストールします。

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/access.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/specs.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/split.yaml
```

## <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>AKS クラスターの Open Service Mesh (OSM) アドオンを無効にする

OSM アドオンを無効にするには、次のコマンドを実行します。

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register
