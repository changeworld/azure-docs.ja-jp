---
title: Open Service Mesh のトラブルシューティング
description: Open Service Mesh をトラブルシューティングする方法
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 397a1ff24152bf0496842971e545b3eb65e779a3
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534005"
---
# <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Open Service Mesh (OSM) AKS アドオンのトラブルシューティング ガイド

OSM AKS アドオンをデプロイするときに、サービス メッシュの構成に関連する問題が発生する場合があります。 次のガイドでは、エラーのトラブルシューティングを行い、一般的な問題を解決する方法について説明します。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="verifying-and-troubleshooting-osm-components"></a>OSM コンポーネントの検証とトラブルシューティング

### <a name="check-osm-controller-deployment"></a>OSM コントローラーのデプロイを確認する

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

正常な OSM コントローラーは次のようになります。

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

### <a name="check-the-osm-controller-pod"></a>OSM コントローラー ポッドを確認する

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

### <a name="check-osm-controller-service"></a>OSM コントローラー サービスの確認

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

### <a name="check-osm-controller-endpoints"></a>OSM コントローラー エンドポイントを確認する

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

正常な OSM コントローラー エンドポイントは次のようになります。

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

### <a name="check-osm-injector-deployment"></a>OSM インジェクターのデプロイを確認する

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

正常な OSM インジェクターのデプロイは次のようになります。

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

### <a name="check-osm-injector-pod"></a>OSM インジェクター ポッドを確認する

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

正常な OSM インジェクター ポッドは次のようになります。

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

### <a name="check-osm-injector-service"></a>OSM インジェクター サービスを確認する

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

正常な OSM インジェクター サービスは次のようになります。

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

### <a name="check-osm-endpoints"></a>OSM エンドポイントを確認する

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

正常な OSM エンドポイントは次のようになります。

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

### <a name="check-validating-and-mutating-webhooks"></a>Validating および Mutating Webhook を確認する

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

### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>Validating Webhook のサービスと CA バンドルを確認する

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

### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>Mutating Webhook のサービスと CA バンドルを確認する

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

### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>OSM コントローラーによって Validating (または Mutating) Webhook に CA バンドルが提供されているかどうかを確認する

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

### <a name="check-the-osm-mesh-config-resource"></a>`osm-mesh-config` リソースを確認する

存在するかどうかを確認します。

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system
```

OSM MeshConfig の内容を確認する

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n osm-system -o yaml
```

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

`osm-mesh-config` リソースの値は次のとおりです。

| キー | Type | 既定値 | Kubectl Patch コマンドの例 |
|-----|------|---------------|--------------------------------|
| spec.traffic.enableEgress | [bool] | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"enableEgress":true}}}'  --type=merge` |
| spec.traffic.enablePermissiveTrafficPolicyMode | [bool] | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}'  --type=merge` |
| spec.traffic.useHTTPSIngress | [bool] | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"useHTTPSIngress":true}}}'  --type=merge` |
| spec.traffic.outboundPortExclusionList | array | `[]` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"outboundPortExclusionList":6379,8080}}}'  --type=merge` |
| spec.traffic.outboundIPRangeExclusionList | array | `[]` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"traffic":{"outboundIPRangeExclusionList":"10.0.0.0/32,1.1.1.1/24"}}}'  --type=merge` |
| spec.certificate.serviceCertValidityDuration | string | `"24h"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"certificate":{"serviceCertValidityDuration":"24h"}}}'  --type=merge` |
| spec.observability.enableDebugServer | [bool] | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"serviceCertValidityDuration":true}}}'  --type=merge` |
| spec.observability.tracing.enable | [bool] | `"jaeger.osm-system.svc.cluster.local"`| `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"address": "jaeger.osm-system.svc.cluster.local"}}}}'  --type=merge` |
| spec.observability.tracing.address | string | `"/api/v2/spans"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"endpoint":"/api/v2/spans"}}}}'  --type=merge' --type=merge` |
| spec.observability.tracing.endpoint | string | `false`| `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"enable":true}}}}'  --type=merge` |
| spec.observability.tracing.port | INT | `9411`| `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"observability":{"tracing":{"port":9411}}}}'  --type=merge` |
| spec.sidecar.enablePrivilegedInitContainer | [bool] | `false` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"enablePrivilegedInitContainer":true}}}'  --type=merge` |
| spec.sidecar.logLevel | string | `"error"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"logLevel":"error"}}}'  --type=merge` |
| spec.sidecar.maxDataPlaneConnections | INT | `0` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"maxDataPlaneConnections":"error"}}}'  --type=merge` |
| spec.sidecar.envoyImage | string | `"envoyproxy/envoy-alpine:v1.17.2"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"envoyImage":"envoyproxy/envoy-alpine:v1.17.2"}}}'  --type=merge` |
| spec.sidecar.initContainerImage | string | `"openservicemesh/init:v0.9.2"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"initContainerImage":"openservicemesh/init:v0.9.2"}}}' --type=merge` |
| spec.sidecar.configResyncInterval | string | `"0s"` | `kubectl patch meshconfig osm-mesh-config -n osm-system -p '{"spec":{"sidecar":{"configResyncInterval":"30s"}}}'  --type=merge` |

### <a name="check-namespaces"></a>名前空間を確認する

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

> [!NOTE]
> `osm namespace add` を呼び出すと、**新しい** ポッドのみに Envoy サイドカーが挿入されます。 `kubectl rollout restart deployment ...` を使用して既存のポッドを再起動する必要があります

### <a name="verify-the-smi-crds"></a>SMI CRD を確認します。

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

### <a name="certificate-management"></a>証明書の管理

アプリケーション ポッドで実行されている Envoy プロキシに対する OSM による証明書の発行と管理方法については、[OpenServiceMesh docs サイト](https://docs.openservicemesh.io/docs/guides/certificates/)を参照してください。

### <a name="upgrading-envoy"></a>Envoy のアップグレード

アドオンによって監視されている名前空間に新しいポッドが作成されると、OSM により、そのポッドに [Envoy プロキシ サイドカー](https://docs.openservicemesh.io/docs/guides/app_onboarding/sidecar_injection/)が挿入されます。 Envoy のバージョンを更新する方法に関する情報は、OpenServiceMesh docs サイトの[更新ガイド](https://docs.openservicemesh.io/docs/getting_started/upgrade/#envoy)に記載されています。