---
title: Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの構成を表示する
titleSuffix: Azure Arc-enabled data services
description: Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの構成を表示する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 0789e9bb28868ce4d75aa48beb956fcb4dd74031
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345805"
---
# <a name="show-the-configuration-of-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの構成を表示する

この記事では、サーバー グループの構成を表示する方法について説明します。 そのために、あなたが自問する可能性がある質問をいくつか予測し、それらに答えます。 場合によっては、複数の有効な答えがあることがあります。 この記事では、最も一般的または有益なものを紹介します。 それらの質問をテーマ別にグループ化します。

- Kubernetes の観点から
- Azure Arc 対応データ サービスの観点から

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-a-kubernetes-point-of-view"></a>Kubernetes の観点から

### <a name="what-are-the-postgres-server-groups-deployed-and-how-many-pods-are-they-using"></a>Postgres サーバー グループはどのようにデプロイされていますか? また、使用しているポッドの数はどのくらいですか?

Postgres の種類の Kubernetes リソースを一覧表示します。 次のコマンドを実行します。

```console
kubectl get postgresqls -n <namespace>
```

このコマンドの出力では、作成されたサーバー グループの一覧が表示されます。 それぞれにポッド数が示されます。 次に例を示します。

```output
NAME         STATE   READY-PODS   PRIMARY-ENDPOINT     AGE
postgres01   Ready   5/5          20.101.12.221:5432   12d
```

この例は、1つのサーバー グループが作成されていることを示しています。 5つのポッドで実行されます。1 つがコーディネーターで 4 つがワーカーです。

### <a name="what-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループで使用されているポットは何ですか?

次を実行します。

```console
kubectl get pods -n <namespace>
```

コマンドによって、ポッドの一覧が返されます。 サーバー グループによって使用されているポッドが、それらのサーバー グループに付けた名前に基づいて表示されます。 次に例を示します。

```console
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-4jrtl   1/1     Running   0          12d
control-kz8gh        2/2     Running   0          12d
controldb-0          2/2     Running   0          12d
logsdb-0             3/3     Running   0          12d
logsui-qjkgz         3/3     Running   0          12d
metricsdb-0          2/2     Running   0          12d
metricsdc-4jslw      2/2     Running   0          12d
metricsdc-4tl2g      2/2     Running   0          12d
metricsdc-fkxv2      2/2     Running   0          12d
metricsdc-hs4h5      2/2     Running   0          12d
metricsdc-tvz22      2/2     Running   0          12d
metricsui-7pcch      2/2     Running   0          12d
postgres01c0-0       3/3     Running   0          2d19h
postgres01w0-0       3/3     Running   0          2d19h
postgres01w0-1       3/3     Running   0          2d19h
postgres01w0-2       3/3     Running   0          2d19h
postgres01w0-3       3/3     Running   0          2d19h
```

### <a name="what-pod-is-used-for-what-role-in-the-server-group"></a>サーバー グループ内のどのロールに対してどのようなポッドが使用されますか?

`c` でサフィックスが付けられたポッド名はすべて、コーディネーター ノードを表します。 `w` でサフィックスが付けられたノード名はすべてワーカー ノードで、たとえばサーバー グループをホストする 5 つのポッドなどがあります。

- `postgres01c0-0`: コーディネーター ノード
- `postgres01w0-0`: ワーカー ノード
- `postgres01w0-1`: ワーカー ノード
- `postgres01w0-2`: ワーカー ノード
- `postgres01w0-3`: ワーカー ノード

ここでは、`c` と `w` の後に表示されている文字 `0` は無視できます (ServerGroupName`c0`-x または ServerGroupName`w0`-x)。 これは、製品が高可用性エクスペリエンスを提供するときに使用される表記です。

### <a name="what-is-the-status-of-the-pods"></a>ポッドの状態は何ですか?

`kubectl get pods -n <namespace>` を実行して、`STATUS` 列を確認します

### <a name="what-persistent-volume-claims-pvcs-are-being-used"></a>どのような永続ボリューム要求 (PVC) が使用されていますか?

使用される PVC と、どれがデータ、ログ、およびバックアップに使用されるかを理解するには、次を実行します。

```console
kubectl get pvc -n <namespace>
```

既定で、PVC の名前のプレフィックスはその使用法を示しています。

- `backups-`: バックアップに使用される PVC です
- `data-`: データ ファイルに使用される PVC です
- `logs-`: トランザクション ログ/WAL ファイルに使用される PVC です

次に例を示します。

```output
NAME                                            STATUS   VOLUME              CAPACITY   ACCESS MODES   STORAGECLASS    AGE
backups-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    local-pv-485e37db   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    local-pv-9d3d4a15   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    local-pv-7b8dd819   1938Gi     RWO            local-storage   6d6h
...
data-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-3c1a8cc5   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-8303ab19   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-55572fe6   1938Gi     RWO            local-storage   6d6h
...
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-5e852b76   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-55d309a7   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-5ccd02e6   1938Gi     RWO            local-storage   6d6h
...
```

### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-server-group"></a>使用されているメモリと仮想コアの量はどれくらいですか、およびサーバー グループに対してどのような拡張機能が作成されましたか?

Kubectl を使用して Postgres リソースを記述します。 これを行うには、その種類 (Azure Arc の Postgres の Kubernetes リソース (CRD) の名前) とサーバーグループの名前が必要です。

このコマンドの一般的な形式は次のとおりです。

```console
kubectl describe <CRD name>/<server group name> -n <namespace>
```

次に例を示します。

```console
kubectl describe postgresql/postgres01 -n arc
```

このコマンドによって、サーバー グループの構成が示されます。

```output
Name:         postgres01
Namespace:    arc
Labels:       <none>
Annotations:  <none>
API Version:  arcdata.microsoft.com/v1beta2
Kind:         PostgreSql
Metadata:
  Creation Timestamp:  2021-10-13T01:09:25Z
  Generation:          29
  Managed Fields:
    API Version:  arcdata.microsoft.com/v1beta2
    Fields Type:  FieldsV1
    fieldsV1:
      f:spec:
        .:
        f:dev:
        f:engine:
          .:
          f:extensions:
          f:version:
        f:scale:
          .:
          f:replicas:
          f:workers:
        f:scheduling:
          .:
          f:default:
            .:
            f:resources:
              .:
              f:requests:
                .:
                f:memory:
          f:roles:
            .:
            f:coordinator:
              .:
              f:resources:
                .:
                f:limits:
                  .:
                  f:cpu:
                  f:memory:
                f:requests:
                  .:
                  f:cpu:
                  f:memory:
            f:worker:
              .:
              f:resources:
                .:
                f:limits:
                  .:
                  f:cpu:
                  f:memory:
                f:requests:
                  .:
                  f:cpu:
                  f:memory:
        f:services:
          .:
          f:primary:
            .:
            f:port:
            f:type:
        f:storage:
          .:
          f:backups:
            .:
            f:volumes:
          f:data:
            .:
            f:volumes:
          f:logs:
            .:
            f:volumes:
    Manager:      OpenAPI-Generator
    Operation:    Update
    Time:         2021-10-22T22:37:51Z
    API Version:  arcdata.microsoft.com/v1beta2
    Fields Type:  FieldsV1
    fieldsV1:
      f:IsValid:
      f:spec:
        f:scale:
          f:syncReplicas:
      f:status:
        .:
        f:logSearchDashboard:
        f:metricsDashboard:
        f:observedGeneration:
        f:podsStatus:
        f:primaryEndpoint:
        f:readyPods:
        f:state:
    Manager:         unknown
    Operation:       Update
    Time:            2021-10-22T22:37:53Z
  Resource Version:  1541521
  UID:               23565e53-2e7a-4cd6-8f80-3a79397e1d7a
Spec:
  Dev:  false
  Engine:
    Extensions:
      Name:   citus
    Version:  12
  Scale:
    Replicas:       1
    Sync Replicas:  0
    Workers:        4
  Scheduling:
    Default:
      Resources:
        Requests:
          Memory:  256Mi
    Roles:
      Coordinator:
        Resources:
          Limits:
            Cpu:     2
            Memory:  1Gi
          Requests:
            Cpu:     1
            Memory:  256Mi
      Worker:
        Resources:
          Limits:
            Cpu:     2
            Memory:  1Gi
          Requests:
            Cpu:     1
            Memory:  256Mi
  Services:
    Primary:
      Port:  5432
      Type:  LoadBalancer
  Storage:
    Backups:
      Volumes:
        Size:  5Gi
    Data:
      Volumes:
        Class Name:  managed-premium
        Size:        5Gi
    Logs:
      Volumes:
        Class Name:  managed-premium
        Size:        5Gi
Status:
  Log Search Dashboard:  https://12.235.78.99:5601/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))
  Metrics Dashboard:     https://12.346.578.99:3000/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01
  Observed Generation:   29
  Pods Status:
    Conditions:
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:40:55.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:40:55.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01w0-1
    Role:                    worker
    Conditions:
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:42:41.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:42:41.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01c0-0
    Role:                    coordinator
    Conditions:
      Last Transition Time:  2021-10-22T22:37:54.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:40:52.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:40:52.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:54.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01w0-3
    Role:                    worker
    Conditions:
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:38:35.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:38:35.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01w0-0
    Role:                    worker
    Conditions:
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:42:40.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:42:40.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01w0-2
    Role:                    worker
  Primary Endpoint:          20.101.12.221:5432
  Ready Pods:                5/5
  Running Version:           v1.1.0_2021-10-12_patching_0bcb7bcaf
  State:                     Ready
Events:                      <none>
```

#### <a name="interpret-the-configuration-information"></a>構成情報を解釈する

先述の `servergroup` の説明のいくつかの特定の重要ポイントに注目してみましょう。 それにより、このサーバー グループについて何がわかりますか?

- これは Postgres のバージョン 12 のものであり、Citus 拡張機能を実行します。

   ```output
   Spec:
     Dev:  false
     Engine:
       Extensions:
         Name:   citus
       Version:  12
   ```

- 2021 年 10 月 13 日に作成されました。

   ```output
     Metadata:
     Creation Timestamp:  2021-10-13T01:09:25Z
   ```

- 4 つのワーカー ノードを使用します。

   ```output
        Scale:
          Replicas:       1
          Sync Replicas:  0
          Workers:        4
   ```

- リソース構成: この例では、コーディネーターとワーカーで 256 Mi のメモリが保証されています。 コーディネーターとワーカー ノードで、1Gi を超えるメモリを使用することはできません。 コーディネーターとワーカーは両方とも 1 つの仮想コアが保証されており、3 つ以上の仮想コアを使用することはできません。

   ```console
        Scheduling:
       Default:
         Resources:
           Requests:
             Memory:  256Mi
       Roles:
         Coordinator:
           Resources:
             Limits:
               Cpu:     2
               Memory:  1Gi
             Requests:
               Cpu:     1
               Memory:  256Mi
         Worker:
           Resources:
             Limits:
               Cpu:     2
               Memory:  1Gi
             Requests:
               Cpu:     1
               Memory:  256Mi
   ```

- サーバー グループはどのような状態ですか? 自分のアプリケーションで使用できますか?

   はい、すべてのポッド (コーディネーター ノードと 4 つすべてのワーカーノード) の準備ができています

   ```console
   Ready Pods:                5/5
   ```

## <a name="from-an-azure-arc-enabled-data-services-point-of-view"></a>Azure Arc 対応データ サービスの観点から

Az CLI コマンドを使用します。

### <a name="what-are-the-postgres-server-groups-deployed-and-how-many-workers-are-they-using"></a>Postgres サーバー グループはどのようにデプロイされていますか? また、使用しているワーカーの数はどのくらいですか?

次のコマンドを実行します。

   ```azurecli
   az postgres arc-server list --k8s-namespace <namespace> --use-k8s
   ```

デプロイされているサーバー グループが一覧表示されます。

   ```output
   [
     {
       "name": "postgres01",
       "replicas": 1,
       "state": "Ready",
       "workers": 4
     }
   ]
   ```

また、サーバー グループが使用するワーカー ノードの数も示されます。 各ワーカー ノードは 1 つのポッドにデプロイされます。また、コーディネーター ノードをホストするために使用されるポッドを1つ追加する必要があります。

### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-group"></a>使用されているメモリと仮想コアの量はどれくらいですか、およびグループに対してどのような拡張機能が作成されましたか?

次のコマンドのいずれかを実行します

```azurecli
az postgres arc-server show -n <server group name>  --k8s-namespace <namespace> --use-k8s
```

次に例を示します。

```azurecli
az postgres arc-server show -n postgres01 --k8s-namespace arc --use-k8s
```

kubectl によって返されるものとよく似た形式と内容で、以下の情報が返されます。 任意のツールを使用して、システムと対話します。

## <a name="next-steps"></a>次のステップ

- [Azure Arc 対応 PostgreSQL Hyperscale の概念を確認する](concepts-distributed-postgres-hyperscale.md)
- [サーバー グループをスケールアウト (ワーカーノードを追加) する方法について確認する](scale-out-in-postgresql-hyperscale-server-group.md)
- [サーバー グループのスケールアップ/スケールダウン (メモリや仮想コアの増減) の方法について確認する](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- [ストレージ構成について確認する](storage-configuration.md)
- [データベース インスタンスを監視する方法を確認する](monitor-grafana-kibana.md)
- [Azure Arc 対応の PostgreSQL Hyperscale サーバー グループで PostgreSQL 拡張機能を使用する](using-extensions-in-postgresql-hyperscale-server-group.md)
- [Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのセキュリティを構成する](configure-security-postgres-hyperscale.md)
