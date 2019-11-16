---
title: Azure Kubernetes Service (AKS) で Istio をインストールする
description: Istio をインストールして使用し、Azure Kubernetes Service (AKS) クラスターでサービス メッシュを作成する方法について説明します
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 245ac3b1fd88b8d2430e9ddefef3562efd16e6d1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885376"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で Istio をインストールして使用する

[Istio][istio-github] はオープンソースのサービス メッシュであり、Kubernetes クラスターのマイクロサービスに対して重要な機能のセットが提供されます。 これらの機能には、トラフィック管理、サービスの ID とセキュリティ、ポリシー適用、可観測性などがあります。 Istio について詳しくは、公式ドキュメント「[Istio とは][istio-docs-concepts]」をご覧ください。

この記事では、Istio をインストールする方法について説明します。 Istio `istioctl` クライアント バイナリはクライアント コンピューターにインストールされます。それから、Istio コンポーネントが AKS の Kubernetes クラスターにインストールされます。

> [!NOTE]
> これらの手順は Istio バージョン `1.3.2` を参考にし、最低でも Helm バージョン `2.14.2` を使用しています。
>
> Istio `1.3.x` リリースは、Istio チームによって Kubernetes バージョン `1.13`、`1.14`、`1.15` に対してテストされています。 Istio のその他のバージョンは [GitHub の Istio リリース][istio-github-releases]に関するページにあります。各リリースに関する情報は [Istio のニュース][istio-release-notes]を参照してください。サポートされている Kubernetes のバージョンについては、[Istio の全般的な FAQ][istio-faq] のページを参照してください。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Istio istioctl クライアント バイナリをダウンロードしてインストールする
> * AKS に Istio をインストールする
> * Istio インストールを検証する
> * アドオンにアクセスする
> * AKS から Istio をアンインストールする

## <a name="before-you-begin"></a>開始する前に

この記事で詳しく説明する手順では、AKS クラスターを作成済みで (Kubernetes `1.13`以上、RBAC を有効にする)、そのクラスターとの `kubectl` 接続が確立されていることを前提としています。 いずれかの項目でヘルプが必要な場合、[AKS クイック スタート][aks-quickstart]を参照してください。

これらの手順に従い Istio をインストールするには [Helm][helm] が必要です。 クラスターに最新の安定バージョンを正しくインストールし、構成しておくことをお勧めします。 Helm のインストールでヘルプが必要な場合は、[AKS Helm インストール ガイド][helm-install]をご覧ください。 Linux ノード上で実行するようにすべての Istio ポッドをスケジュールすることも必要です。

[Istio のパフォーマンスとスケーラビリティ](https://istio.io/docs/concepts/performance-and-scalability/)に関するドキュメントを読み、AKS クラスターで Istio を実行するための追加リソース要件を理解しておいてください。 コアとメモリの要件は、特定のワークロードに基づいて変わります。 セットアップに適したノード数と VM サイズを選択します。

この記事では、Istio のインストール ガイドを個別のステップに分割しています。 最終的な結果は、公式の Istio インストール [ガイド][istio-install-helm]と構造的に同じになります。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="add-the-istio-helm-chart-repository"></a>Istio Helm チャート リポジトリを追加する

Istio のリリースに対応する Istio Helm チャート リポジトリを追加します。 `helm repo update` を実行して、チャート リポジトリのローカル情報を更新するようにしてください。

```azurecli
helm repo add istio.io https://storage.googleapis.com/istio-release/releases/$ISTIO_VERSION/charts/
helm repo update
```

## <a name="install-the-istio-crds-on-aks"></a>Istio CRD を AKS にインストールする

Istio は [Custom Resource Definitions (CRD)][kubernetes-crd] を使用してランタイム構成を管理します。 Istio コンポーネントには Istio CRD に対する依存関係があるため、最初にインストールする必要があります。 Helm と `istio-init` チャートを使用して Istio CRD を AKS クラスターの `istio-system` 名前空間にインストールします。

```azurecli
helm install istio.io/istio-init --name istio-init --namespace istio-system
```

[ジョブ][kubernetes-jobs]が、CRD をインストールする `istio-init` Helm Chart の一部としてデプロイされます。 これらのジョブは、クラスター環境によって異なりますが、20 分以内に完了します。 ジョブが正常に完了したことは次のように確認できます。

```azurecli
kubectl get jobs -n istio-system
```

次の出力例では、正常に完了したジョブが示されています。

```console
NAME                      COMPLETIONS   DURATION   AGE
istio-init-crd-10-1.3.2   1/1           14s        14s
istio-init-crd-11-1.3.2   1/1           12s        14s
istio-init-crd-12-1.3.2   1/1           14s        14s
```

ジョブの正常な完了を確認したところで、正しい数の Istio CRD がインストールされたことを確認します。 次のコマンドを実行すると、全部で 23 の Istio CRD がインストールされたことを確認できます。 コマンドから数 `23` が返されます。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - check CRD count](includes/servicemesh/istio/install-check-crd-count-powershell.md)]

::: zone-end

この時点まで進んだということは、Istio CRD のインストールが正常に行われたことを意味します。 次は [Istio コンポーネントを AKS にインストールする](#install-the-istio-components-on-aks)セクションに進みます。

## <a name="install-the-istio-components-on-aks"></a>Istio コンポーネントを AKS にインストールする

[Grafana][grafana] と [Kiali][kiali] を Istio インストールの一部としてインストールします。 Grafana では分析と監視のダッシュボードが提供され、Kiali ではサービス メッシュ監視ダッシュボードが提供されます。 ご使用のセットアップでは、これらのコンポーネントそれぞれに資格情報が必要であり、[シークレット][kubernetes-secrets]として提供する必要があります。

Istio コンポーネントをインストールするには、前もって Grafana と Kiali 両方のシークレットを作成する必要があります。 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - create secrets for Grafana and Kiali](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash check for CRDs](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell check for CRDs](includes/servicemesh/istio/install-create-secrets-powershell.md)]

::: zone-end

### <a name="install-istio-components"></a>Istio コンポーネントをインストールする

AKS クラスターに Grafana および Kiali シークレットを正常に作成したところで、Istio コンポーネントをインストールします。 Helm と `istio` チャートを使用して Istio コンポーネントを AKS クラスターの `istio-system` 名前空間にインストールします。 

> [!NOTE]
> **インストール オプション**
> 
> このインストールにおいては次のオプションを使用しています。
> - `global.controlPlaneSecurityEnabled=true` - コントロール プレーンの相互 TLS を有効化します
> - `global.mtls.enabled=true` - すべてのサービス間通信に mtls を使用することを要求します
> - `grafana.enabled=true` - 分析よび監視ダッシュボードのための Grafana のデプロイを有効化します
> - `grafana.security.enabled=true` - Grafana の認証を有効化します
> - `tracing.enabled=true` - トレースのための Jaeger のデプロイを有効化します
> - `kiali.enabled=true` - サービス メッシュ監視ダッシュボードのための Kiali のデプロイを有効化します
>
> **ノードのセレクター**
>
> 現時点で Istio は Linux ノード上で実行するようにスケジュールする必要があります。 クラスター内に Windows Server ノードがある場合、Istio ポッドが確実に Linux ノードでのみ実行されるようにスケジュールする必要があります。 [ノードのセレクター][kubernetes-node-selectors]を使用して、ポッドが適切なノードにスケジュールされていることを確認します。

> [!CAUTION]
> [SDS (シークレット検出サービス)][istio-feature-sds] と [Istio CNI][istio-feature-cni] の機能は現在[アルファ版][istio-feature-stages]であるため、これらを有効にする場合は事前に十分な考慮をする必要があります。 また、現在の AKS バージョンでは、[サービス アカウント トークン ボリューム プロジェクション][kubernetes-feature-sa-projected-volume] Kubernetes 機能 (SDS の要件) は有効になっていません。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/istio/install-components-powershell.md)]

::: zone-end

`istio`Helm Chart によって、たくさんのオブジェクトがデプロイされます。 上記の `helm install` コマンドの出力で一覧を確認できます。 Istio コンポーネントのデプロイには、クラスター環境によって異なりますが、2 分以内に完了します。

この時点で、Istio が AKS クラスターにデプロイされています。 Istio が正常にデプロイされたことを確認するには、次の [Istio インストールを検証する](#validate-the-istio-installation)セクションに進みます。

## <a name="validate-the-istio-installation"></a>Istio インストールを検証する

最初に、サービスが期待どおりに作成されていることを確認します。 実行中のサービスを表示するには、[kubectl get svc][kubectl-get] コマンドを使用します。 `istio-system` 名前空間に照会します。この名前空間は `istio` Helm Chart によって Istio とアドオン コンポーネントがインストールされた場所です。

```console
kubectl get svc --namespace istio-system --output wide
```

次の出力例では、現在実行中のサービスを確認できます。

- `istio-*` サービス
- `jaeger-*`、`tracing`、および `zipkin` アドオン トレーシング サービス
- `prometheus` アドオン メトリック サービス
- `grafana` アドオン分析と監視ダッシュボード サービス
- `kiali`アドオン サービス メッシュ ダッシュボード サービス

`istio-ingressgateway` に `<pending>` の外部 IP が表示された場合、Azure ネットワークによって IP アドレスが割り当てられるまで数分待ってください。

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.164.244   <none>           3000/TCP                                                                                                                                     53s   app=grafana
istio-citadel            ClusterIP      10.0.49.16     <none>           8060/TCP,15014/TCP                                                                                                                           53s   istio=citadel
istio-galley             ClusterIP      10.0.175.173   <none>           443/TCP,15014/TCP,9901/TCP                                                                                                                   53s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.226.151   20.188.221.111   15020:31128/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:30817/TCP,15030:30436/TCP,15031:32485/TCP,15032:30980/TCP,15443:30124/TCP   53s   app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.102.158   <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       53s   istio=pilot
istio-policy             ClusterIP      10.0.234.53    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                                 53s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.216.8     <none>           443/TCP,15014/TCP                                                                                                                            53s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.154.215   <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       53s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                                   52s   app=jaeger
jaeger-collector         ClusterIP      10.0.26.109    <none>           14267/TCP,14268/TCP                                                                                                                          52s   app=jaeger
jaeger-query             ClusterIP      10.0.70.55     <none>           16686/TCP                                                                                                                                    52s   app=jaeger
kiali                    ClusterIP      10.0.36.206    <none>           20001/TCP                                                                                                                                    53s   app=kiali
prometheus               ClusterIP      10.0.236.99    <none>           9090/TCP                                                                                                                                     53s   app=prometheus
tracing                  ClusterIP      10.0.83.152    <none>           80/TCP                                                                                                                                       52s   app=jaeger
zipkin                   ClusterIP      10.0.25.86     <none>           9411/TCP                                                                                                                                     52s   app=jaeger
```

次に、必要なポッドが作成されていることを確認します。 [kubectl get pods][kubectl-get] コマンドを使用し、再度、`istio-system` 名前空間に照会します。

```console
kubectl get pods --namespace istio-system
```

次の出力例では、実行中のポッドを確認できます。

- `istio-*` ポッド
- `prometheus-*` アドオン メトリック ポッド
- `grafana-*` アドオン分析と監視ダッシュボード ポッド
- `kiali` アドオン サービス メッシュ ダッシュボード ポッド

```console
NAME                                     READY   STATUS      RESTARTS   AGE
grafana-7c48555456-msl7b                 1/1     Running     0          88s
istio-citadel-566fc66db7-m8wgl           1/1     Running     0          87s
istio-galley-5746db8d56-pl5gg            1/1     Running     0          88s
istio-ingressgateway-6c94f7c9bf-f5lt5    1/1     Running     0          88s
istio-init-crd-10-1.3.2-xw9g2            0/1     Completed   0          92m
istio-init-crd-11-1.3.2-54rz8            0/1     Completed   0          92m
istio-init-crd-12-1.3.2-789qj            0/1     Completed   0          92m
istio-pilot-6748968b6d-rvdfx             2/2     Running     0          87s
istio-policy-7576bbbcf7-2stft            2/2     Running     0          87s
istio-sidecar-injector-76d79d494-7jk9n   1/1     Running     0          87s
istio-telemetry-74b7bf676d-tfrcl         2/2     Running     0          88s
istio-tracing-655d9588bc-d2htg           1/1     Running     0          86s
kiali-65d55bcfb8-tqrfk                   1/1     Running     0          88s
prometheus-846f9849bd-br8kp              1/1     Running     0          87s
```

`Completed` ステータスの 3 つの `istio-init-crd-*` ポッドがあります。 これらのポッドは、前の手順で CRD を作成したジョブの実行に使用されました。 他のすべてのポッドのステータスは `Running` と表示される必要があります。 ポッドのステータスが実行中ではない場合、実行中にナルまで数分待ってください。 ポッドから問題が報告された場合、[kubectl describe pod][kubectl-describe] コマンドを使用し、出力と状態を確認してください。

## <a name="accessing-the-add-ons"></a>アドオンにアクセスする

上記の例では、追加機能を提供するたくさんのアドオンが Istio によってインストールされています。 アドオンの Web アプリが外部 IP アドレス経由で公開されることは**ありません**。 

アドオン ユーザー インターフェイスにアクセスするには、`istioctl dashboard` コマンドを使用します。 このコマンドでは、[kubectl port-forward][kubectl-port-forward] とランダム ポートを活用して、クライアント コンピューターと AKS クラスターの関連ポッドの間に安全な接続を作成します。 これにより、自動的に既定のブラウザーでアドオン Web アプリが開きます。

この記事の前半で Grafana と Kiali の資格情報を指定してさらにセキュリティのレベルを高めています。

### <a name="grafana"></a>Grafana

Istio の分析と監視ダッシュボードが [Grafana][grafana] によって提供されます。 プロンプトが表示されたら、前に Grafana シークレットを使用して作成した資格情報を使用することを忘れないでください。 Grafana ダッシュボードを安全に開くには、次のコマンドを実行します。

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Istio のメトリックは [Prometheus][prometheus] によって提供されます。 Prometheus ダッシュボードを安全に開くには、次のコマンドを実行します。

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Istio でのトレースは [Jaeger][jaeger] によって提供されます。 Jaeger ダッシュボードを安全に開くには、次のコマンドを実行します。

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

サービス メッシュ可観測性ダッシュボードは [Kiali][kiali] によって提供されます。 プロンプトが表示されたら、前に Kiali シークレットを使用して作成した資格情報を使用することを忘れないでください。 Kiali ダッシュボードを安全に開くには、次のコマンドを実行します。

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

[Envoy][envoy] プロキシへのシンプルなインターフェイスが提供されています。 これは、指定されたポッドで実行されている Envoy プロキシの構成情報とメトリックを提供するものです。 Envoy インターフェイスを安全に開くには、次のコマンドを実行します。

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>AKS から Istio をアンインストールする

> [!WARNING]
> 実行中のシステムから Istio を削除すると、サービス間のトラフィックに関連する問題が発生することがあります。 進める前に、Istio なしでもシステムが正しく動作するようにプロビジョニングを作成することをお勧めします。

### <a name="remove-istio-components-and-namespace"></a>Istio コンポーネントおよび名前空間を削除する

Istio を AKS クラスターから削除するには、次のコマンドを使用します。 `helm delete` コマンドによって `istio` および `istio-init` チャートが削除され、`kubectl delete namespace` コマンドによって `istio-system` 名前空間が削除されます。

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete namespace istio-system
```

### <a name="remove-istio-crds-and-secrets"></a>Istio CRD とシークレットを削除する

上記のコマンドによってすべての Istio コンポーネントと名前空間が削除されますが、まだ Istio CRD とシークレットが残っています。 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>次の手順

次のドキュメントでは、カナリア リリースを展開するために Istio を使用してインテリジェント ルーティングを提供する方法を説明しています。

> [!div class="nextstepaction"]
> [AKS Istio インテリジェント経路指定シナリオ][istio-scenario-routing]

Istio のインストール オプションと構成オプションを他にも見るには、次の公式 Istio 記事をご覧ください。

- [Istio - Helm インストール ガイド][istio-install-helm]
- [Istio - Helm インストール オプション][istio-install-helm-options]

以下を使用した追加のシナリオに沿って進めることもできます。

- [Istio Bookinfo Application の例][istio-bookinfo-example]

Application Insights と Istio を使用して AKS アプリケーションを監視する方法については、次の Azure Monitor のドキュメントを参照してください。

- [Kubernetes でホストされるアプリケーションに対するゼロ インストルメンテーション アプリケーション監視][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/security/auth-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./servicemesh-istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
