---
title: Azure Arc 対応 Kubernetes の一般的な問題のトラブルシューティング
services: azure-arc
ms.service: azure-arc
ms.date: 05/21/2021
ms.topic: article
description: Azure Arc 対応 Kubernetes クラスターに関する一般的な問題のトラブルシューティング。
keywords: Kubernetes, Arc, Azure, コンテナー
ms.openlocfilehash: acbc8293f5aed737dba6ed3992fc59cac0a4fd55
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059243"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting"></a>Azure Arc 対応 Kubernetes のトラブルシューティング

このドキュメントでは、接続、アクセス許可、エージェントに関する問題のトラブルシューティング ガイドを提供します。

## <a name="general-troubleshooting"></a>一般的なトラブルシューティング

### <a name="azure-cli"></a>Azure CLI

`az connectedk8s` または `az k8s-configuration` CLI コマンドを使用する前に、Azure CLI が適切な Azure サブスクリプションに対して機能するように設定されていることを確認します。

```azurecli
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Azure Arc エージェント

Azure Arc 対応 Kubernetes のすべてのエージェントは、`azure-arc` 名前空間にポッドとしてデプロイされます。 すべてのポッドが実行され、正常性チェックに合格する必要があります。

まず、Azure Arc の Helm リリースを確認します。

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Helm リリースが見つからないか欠落している場合は、もう一度[クラスターを Azure Arc に接続](./quickstart-connect-cluster.md)してみてください。

Helm リリースが存在し、`STATUS: deployed` の場合は、`kubectl` を使用してエージェントの状態を確認します。

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                       READY  UP-TO-DATE  AVAILABLE  AGE
deployment.apps/clusteridentityoperator     1/1       1          1       16h
deployment.apps/config-agent                1/1       1          1       16h
deployment.apps/cluster-metadata-operator   1/1       1          1       16h
deployment.apps/controller-manager          1/1       1          1       16h
deployment.apps/flux-logs-agent             1/1       1          1       16h
deployment.apps/metrics-agent               1/1       1          1       16h
deployment.apps/resource-sync-agent         1/1       1          1       16h

NAME                                            READY   STATUS  RESTART  AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

すべてのポッドの `STATUS` が `Running` と示され、`READY` 列の下に `3/3` または `2/2` のいずれかが表示されます。 ログをフェッチし、`Error` または `CrashLoopBackOff` を返しているポッドを書き留めます。 これらのポッドのいずれかが `Pending` 状態で止まっている場合、クラスター ノードにリソースが十分にないことが考えられます。 [クラスターをスケールアップする](https://kubernetes.io/docs/tasks/administer-cluster/)と、これらのポッドを `Running` 状態に移行させることができます。

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Azure Arc に Kubernetes クラスターを Azure に接続する

クラスターを Azure に接続するには、Azure サブスクリプションへのアクセスと、ターゲット クラスターへの `cluster-admin` アクセスの両方が必要です。 クラスターに接続できない場合、またはアクセス許可が不十分な場合は、クラスターの Azure Arc への接続は失敗します。

### <a name="azure-cli-is-unable-to-download-helm-chart-for-azure-arc-agents"></a>Azure CLI で Azure Arc エージェントの Helm グラフをダウンロードできない

3\.7.0 以上のバージョンの Helm を使用している場合は、`az connectedk8s connect` を実行してクラスターを Azure Arc に接続するときに次のエラーが発生します。

```console
$ az connectedk8s connect -n AzureArcTest -g AzureArcTest

Unable to pull helm chart from the registry 'mcr.microsoft.com/azurearck8s/batch1/stable/azure-arc-k8sagents:1.4.0': Error: unknown command "chart" for "helm"
Run 'helm --help' for usage.
```

この場合は、バージョン &lt; 3.7.0 である、以前のバージョンの [Helm 3](https://helm.sh/docs/intro/install/) をインストールする必要があります。 その後、`az connectedk8s connect` コマンドをもう一度実行して、クラスターを Azure Arc に接続します。

### <a name="insufficient-cluster-permissions"></a>クラスターのアクセス許可が不十分

指定された kubeconfig ファイルに、Azure Arc エージェントをインストールするための十分なアクセス許可が含まれていない場合、Azure CLI コマンドはエラーを返します。

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

クラスターを Azure Arc に接続するユーザーには、そのクラスターで `cluster-admin` ロールが割り当てられている必要があります。


### <a name="unable-to-connect-openshift-cluster-to-azure-arc"></a>OpenShift クラスターを Azure Arc に接続できない

OpenShift クラスターを Azure Arc に接続するときに `az connectedk8s connect` がタイムアウトして失敗する場合、次を確認してください。

1. OpenShift クラスターはバージョンの前提条件 (4.5.41+ または 4.6.35+ または 4.7.18+) を満たす必要があります。

1. `az connectedk8s connnect` を実行する前に、クラスター上で次のコマンドを実行する必要があります。

    ```console
    oc adm policy add-scc-to-user privileged system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa
    ```

### <a name="installation-timeouts"></a>インストールのタイムアウト

Kubernetes クラスターを Azure Arc 対応 Kubernetes に接続するには、クラスターに Azure Arc エージェントをインストールする必要があります。 クラスターが低速のインターネット接続を介して実行されている場合、エージェントのコンテナー イメージのプルに、Azure CLI のタイムアウトよりも時間がかかることがあります。

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Helm の問題

Helm バージョン `v3.3.0-rc.1` には、helm install または helm upgrade を実行すると (`connectedk8s` CLI 拡張機能で使用) すべてのフックが実行され、以下のエラーが発生するという[問題](https://github.com/helm/helm/pull/8527)があります。

```console
$ az connectedk8s connect -n AzureArcTest -g AzureArcTest
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

この問題を解決するには、次の手順に従います。

1. Azure portal で、Azure Arc 対応 Kubernetes リソースを削除します。
2. お使いのマシンで次のコマンドを実行します。
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. リリース候補バージョンではなく、[安定したバージョン](https://helm.sh/docs/intro/install/) (Helm 3) をマシンにインストールします。
4. 適切な値を指定して `az connectedk8s connect` コマンドを実行し、クラスターを Azure Arc に接続します。

## <a name="configuration-management"></a>構成管理

### <a name="general"></a>全般
リソースの構成に関する問題のトラブルシューティングに役立てるため、`--debug` パラメーターを指定して az コマンドを実行します。

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8s-configuration create <parameters> --debug
```

### <a name="create-configurations"></a>構成の作成

クラスターで構成を作成するには、Azure Arc 対応 Kubernetes リソース (`Microsoft.Kubernetes/connectedClusters/Write`) に対する書き込みアクセス許可があれば十分です。

### <a name="configuration-remains-pending"></a>構成が `Pending` のままになる

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```

## <a name="monitoring"></a>監視

コンテナー用の Azure Monitor では、その DaemonSet を特権モードで実行する必要があります。 監視のために Canonical Charmed Kubernetes クラスターを正常に設定するには、次のコマンドを実行します。

```console
juju config kubernetes-worker allow-privileged=true
```

## <a name="enable-custom-locations-using-service-principal"></a>サービス プリンシパルを使用してカスタムの場所を有効にする

クラスターを Azure Arc に接続するとき、または既存のクラスターで "カスタムの場所" 機能を有効にするときに、次の警告が表示される場合があります。

```console
Unable to fetch oid of 'custom-locations' app. Proceeding without enabling the feature. Insufficient privileges to complete the operation.
```

この警告は、サービス プリンシパルを使用して Azure にログインしており、Azure Arc サービスで使用されるアプリケーションの情報を取得するためのアクセス許可がこのサービス プリンシパルにない場合に発生します。 このエラーを回避するには、次の手順を実行します。

1. Azure Arc サービスで使用される Azure AD アプリケーションのオブジェクト ID をフェッチします。

    ```console
    az ad sp show --id 'bc313c14-388c-4e7d-a58e-70017303ee3b' --query objectId -o tsv
    ```

1. 上記の手順の `<objectId>` 値を使用して、クラスターでカスタムの場所機能を有効にします。
    - クラスターを Arc に接続する一環としてカスタムの場所機能を有効にする場合は、次のコマンドを実行します。

        ```console
        az connectedk8s connect -n <cluster-name> -g <resource-group-name> --custom-locations-oid <objectId>   
        ```

    - 既存の Azure Arc 対応 Kubernetes クラスターでカスタムの場所機能を有効にする場合は、次のコマンドを実行します。

        ```console
        az connectedk8s enable-features -n <cluster-name> -g <resource-group-name> --custom-locations-oid <objectId> --features cluster-connect custom-locations
        ```

このアクセス許可が付与されると、クラスターで ["カスタムの場所" 機能の有効化](custom-locations.md#enable-custom-locations-on-cluster)に進めるようになります。

## <a name="azure-arc-enabled-open-service-mesh"></a>Azure Arc 対応 Open Service Mesh

次のトラブルシューティングの手順では、クラスター上のすべての Open Service Mesh 拡張コンポーネントのデプロイを検証する方法について説明します。

### <a name="1-check-osm-controller-deployment"></a>1. OSM コントローラーの **デプロイ** を確認する
```bash
kubectl get deployment -n arc-osm-system --selector app=osm-controller
```

OSM コントローラーが正常な場合は、次のような出力が表示されます。
```
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

### <a name="2-check-the-osm-controller-pod"></a>2. OSM コントローラー **ポッド** を確認する
```bash
kubectl get pods -n arc-osm-system --selector app=osm-controller
```

OSM コントローラーが正常な場合は、次のような出力が表示されます。
```
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

ある時点で 1 つのコントローラーは "_無効_" にされましたが、もう 1 つは `READY 1/1`、`Running`、再起動 `0` になっています。
`READY` 列が `1/1` 以外の場合、サービス メッシュは破損した状態です。
`0/1` の `READY` 列は、コントロール プレーン コンテナーがクラッシュしていることを示します。ログを取得する必要があります。 下の `Get OSM Controller Logs from Azure Support Center` セクションを参照してください。
`/` の後に 1 より大きい数が示された `READY` 列は、サイドカーがインストールされていることを示します。 OSM コントローラーは、多くの場合、アタッチされているサイドカーでは動作しません。

### <a name="3-check-osm-controller-service"></a>3. OSM コントローラー **サービス** を確認する
```bash
kubectl get service -n arc-osm-system osm-controller
```

OSM コントローラーが正常な場合、次の出力が表示されます。
```
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> 注: `CLUSTER-IP` は異なります。 サービスの `NAME` と `PORT(S)` は、出力に表示されているものと同じである必要があります。

### <a name="4-check-osm-controller-endpoints"></a>4. OSM コントローラー **エンドポイント** を確認する
```bash
kubectl get endpoints -n arc-osm-system osm-controller
```

OSM コントローラーが正常な場合は、次のような出力が表示されます。
```
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

ユーザーのクラスターに `osm-controller` 用の `ENDPOINTS` がない場合は、コントロール プレーンが異常であることを示しています。 これは、OSM コントローラー ポッドがクラッシュしたか、正しくデプロイされていないことが原因である可能性があります。

### <a name="5-check-osm-injector-deployment"></a>5. OSM インジェクターの **デプロイ** を確認する
```bash
kubectl get deployments -n arc-osm-system osm-injector
```

OSM インジェクターが正常な場合は、次のような出力が表示されます。
```
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
osm-injector   1/1     1            1           73m
```

### <a name="6-check-osm-injector-pod"></a>6. OSM インジェクター **ポッド** を確認する
```bash
kubectl get pod -n arc-osm-system --selector app=osm-injector
```

OSM インジェクターが正常な場合は、次のような出力が表示されます。
```
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

`READY` 列は `1/1` である必要があります。 それ以外の値は、異常な osm-injector ポッドを示しています。

### <a name="7-check-osm-injector-service"></a>7. OSM インジェクター **サービス** を確認する
```bash
kubectl get service -n arc-osm-system osm-injector
```

OSM インジェクターが正常な場合は、次のような出力が表示されます。
```
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

`osm-injector` サービス用にリストされている IP アドレスが `9090` であることを確認します。 `EXTERNAL-IP` はないはずです。

### <a name="8-check-osm-injector-endpoints"></a>8. OSM インジェクター **エンドポイント** を確認する
```bash
kubectl get endpoints -n arc-osm-system osm-injector
```

OSM インジェクターが正常な場合は、次のような出力が表示されます。
```
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

OSM を機能させるには、`osm-injector` のエンドポイントが少なくとも 1 つ必要です。 OSM インジェクター エンドポイントの IP アドレスは異なります。 ポート `9090` は同じである必要があります。


### <a name="9-check-validating-and-mutating-webhooks"></a>9. **Validating** および **Mutating** の Webhook を確認する
```bash
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

Validating Webhook が正常な場合は、次のような出力が表示されます。
```
NAME              WEBHOOKS   AGE
arc-osm-webhook-osm   1      81m
```

```bash
kubectl get MutatingWebhookConfiguration --selector app=osm-controller
```


Mutating Webhook が正常な場合は、次のような出力が表示されます。
```
NAME              WEBHOOKS   AGE
arc-osm-webhook-osm   1      102m
```

**Validating** Webhook のサービスと CA バンドルを確認します。
```
kubectl get ValidatingWebhookConfiguration arc-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

適切に構成された Validating Webhook 構成の出力は、次のようになります。
```json
{
  "name": "osm-config-validator",
  "namespace": "arc-osm-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

**Mutating** Webhook のサービスと CA バンドルを確認します。
```bash
kubectl get MutatingWebhookConfiguration arc-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

適切に構成された Mutating Webhook 構成の出力は、次のようになります。
```
{
  "name": "osm-injector",
  "namespace": "arc-osm-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```


次のコマンドを使用して、OSM コントローラーによって Validating (または Mutating) Webhook に CA バンドルが提供されているかどうかを確認します。

```bash
kubectl get ValidatingWebhookConfiguration arc-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```bash
kubectl get MutatingWebhookConfiguration arc-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

出力例:
```bash
1845
```
出力のこの数値は、CA バンドルのバイト数またはサイズを示しています。 この値が空、0、または 1000 未満の数値である場合は、CA バンドルが正しくプロビジョニングされていないことを示しています。 正しい CA バンドルがないと、Validating Webhook によってエラーがスローされ、`arc-osm-system` 名前空間の `osm-config` ConfigMap に変更を加えることができなくなります。

CA バンドルが正しくない場合のエラーの例を見てみましょう。
- `osm-config` Configmap を変更しようとすると:
  ```bash
  kubectl patch ConfigMap osm-config -n arc-osm-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
  ```
- 次のエラーが出力されます。
  ```bash
  Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.arc-osm-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
  ```

**Validating** Webhook 構成に無効な証明書がある場合は、次のいずれかの回避策を使用します。
- 方法 1. OSM コントローラーを再起動します。これで、OSM コントローラーが再起動されます。 起動時に、Mutating と Validating の両方の Webhook の CA バンドルが上書きされます。
  ```bash
  kubectl rollout restart deployment -n arc-osm-system osm-controller
  ```

- 方法 2. Validating Webhook を削除します。Validating Webhook を削除すると、`osm-config` ConfigMap の変更が検証されなくなります。 パッチはすべて適用されます。 CA バンドルを迅速に書き換えるには、OSM コントローラーを再起動する必要がある場合があります。
   ```bash
   kubectl delete ValidatingWebhookConfiguration arc-osm-webhook-osm
   ```

- 方法 3. 削除してパッチを適用します。次のコマンドを実行すると、Validating Webhook が削除され、任意の値を追加できるようになり、パッチの適用が直ちに試行されます。
  ```bash
  kubectl delete ValidatingWebhookConfiguration arc-osm-webhook-osm; kubectl patch ConfigMap osm-config -n arc-osm-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
  ```


### <a name="10-check-the-osm-config-configmap"></a>10. `osm-config` **ConfigMap** を確認する

>[!Note]
>OSM コントローラーでは、`arc-osm-system` 名前空間に `osm-config` ConfigMap が存在している必要はありません。 コントローラーには構成に対して妥当な既定値があり、これがなくても動作できます。

存在するかどうかを確認します。
```bash
kubectl get ConfigMap -n arc-osm-system osm-config
```

`osm-config` ConfigMap の内容を確認します。
```bash
kubectl get ConfigMap -n arc-osm-system osm-config -o json | jq '.data'     
```
次の出力が表示されます。
```json
{
  "egress": "false",
  "enable_debug_server": "false",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "true",
  "service_cert_validity_duration": "24h",
  "tracing_enable": "false",
  "use_https_ingress": "false",
}
```

`osm-config` Configmap の値を理解するには、[OSM ConfigMap のドキュメント](https://release-v0-8.docs.openservicemesh.io/docs/osm_config_map/)を参照してください。

### <a name="11-check-namespaces"></a>11. 名前空間を確認する

>[!Note]
>arc-osm-system 名前空間は、サービス メッシュに参加することはありません。また、下のキーと値を使用してラベル付けされたり注釈を付けられたりすることもありません。

`osm namespace add` コマンドを使用して、特定のサービス メッシュに名前空間を参加させます。
kubernetes 名前空間がメッシュの一部である場合、次を満たす必要があります。

名前空間 `bookbuyer` の注釈を表示します。
```bash
kc get namespace bookbuyer -o json | jq '.metadata.annotations'
```

次の注釈が存在する必要があります。
```
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```


名前空間 `bookbuyer` のラベルを表示します。
```bash
kc get namespace bookbuyer -o json | jq '.metadata.labels'
```

次のラベルが存在する必要があります。
```
{
  "openservicemesh.io/monitored-by": "osm"
}
```
`osm` CLI を使用していない場合は、これらの注釈を名前空間に手動で追加することもできます。 名前空間に `"openservicemesh.io/sidecar-injection": "enabled"` の注釈が付けられていない場合、または `"openservicemesh.io/monitored-by": "osm"` でラベル付けされていない場合、OSM インジェクターではエンボイ サイドカーは追加されません。

>[!Note]
>`osm namespace add` を呼び出すと、**新しい** ポッドのみに Envoy サイドカーが挿入されます。 `kubectl rollout restard deployment` コマンドを使用して既存のポッドを再起動する必要があります


### <a name="12-verify-the-smi-crds"></a>12. SMI CRD を確認する
クラスターに必要な CRD があるかどうかを確認します。
```bash
kubectl get crds
```

CRD が同じ OSM アップストリーム バージョンに対応していることを確認します。 例: v 0.8.4 を使用している場合は、CRD が [OSM OSS プロジェクト](https://docs.openservicemesh.io/)のリリース ブランチ v0.8.4 で利用可能なものと一致していることを確認します。 [OSM のリリース ノート](https://github.com/openservicemesh/osm/releases)を参照してください。

次のコマンドを使用して、インストールされている CRD のバージョンを取得します。
```bash
for x in $(kubectl get crds --no-headers | awk '{print $1}' | grep 'smi-spec.io'); do
    kubectl get crd $x -o json | jq -r '(.metadata.name, "----" , .spec.versions[].name, "\n")'
done
```

CRD がない場合は、次のコマンドを使用してクラスターにこれらをインストールします。 コマンド内のバージョンは必ず置き換えてください。
```bash
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8.2/charts/osm/crds/access.yaml

kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8.2/charts/osm/crds/specs.yaml

kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8.2/charts/osm/crds/split.yaml
```
