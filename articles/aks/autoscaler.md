---
title: Kubernetes on Azure - クラスター オートスケーラー
description: Azure Kubernetes Service (AKS) でクラスター オートスケーラーを使用して、需要を満たすようにクラスターを自動的にスケーリングする方法について説明します。
services: container-service
author: sakthivetrivel
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/19/18
ms.author: sakthivetrivel
ms.custom: mvc
ms.openlocfilehash: 06b93273ea096f2d24df4411a64ce99d054892cf
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "42141978"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>Azure Kubernetes Service のクラスター オートスケーラー (AKS) - プレビュー

Azure Kubernetes Service (AKS) には、マネージド Kubernetes クラスターを Azure 内に簡単にデプロイするための柔軟なソリューションがあります。 リソースの需要が増えたときに、クラスター オートスケーラーを使用して、設定した制約に基づいてその需要を満たすようにクラスターを拡張できます。 クラスター オートスケーラー (CA) は、保留中のポッドに基づいてエージェント ノードをスケーリングすることでこれを行います。 それは、クラスターを定期的にチェックして、保留中のポッドや空のノードを確認し、可能であればサイズを大きくします。 既定では、CA は、保留中のポッドを 10 秒ごとにスキャンし、ノードが 10 分を越えて不要な場合はそれを削除します。 [ポッドの水平オートスケーラー](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) (HPA) と共に使用した場合、HPA は、需要に応じてポッドのレプリカとリソースを更新します。 このポッドのスケーリング後に十分なノードがない、または不要なポットがない場合は、CA が応答し、新しいノード セットでノードをスケジュールします。

この記事では、エージェント ノードにラスター オートスケーラーをデプロイする方法について説明します。 ただし、クラスター オートスケーラーは kube-system 名前空間にデプロイされるため、そのポッドを実行しているノードのスケールダウンは行いません。

> [!IMPORTANT]
> Azure Kubernetes Service (AKS) のクラスター オートスケーラーの統合は、現在は**プレビュー段階**です。 プレビュー版は、[追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。
>

## <a name="prerequisites"></a>前提条件

このドキュメントでは、RBAC が有効な AKS クラスターがあることを前提としています。 AKS クラスターが必要な場合は、[Azure Kubernetes Service (AKS) のクイック スタート][aks-quick-start]に関するページを参照してください。

 クラスター オートスケーラーを使用するには、クラスターが Kubernetes v1.10.X 以降であり、RBAC が有効になっている必要があります。 クラスターをアップグレードするには、[AKS クラスターのアップグレード][aks-upgrade]に関する記事を参照してください。

## <a name="gather-information"></a>情報を収集する

クラスター オートスケーラーをクラスターで実行するためのアクセス許可を生成するには、次の bash スクリプトを実行します。

```sh
#! /bin/bash
ID=`az account show --query id -o json`
SUBSCRIPTION_ID=`echo $ID | tr -d '"' `

TENANT=`az account show --query tenantId -o json`
TENANT_ID=`echo $TENANT | tr -d '"' | base64`

read -p "What's your cluster name? " cluster_name
read -p "Resource group name? " resource_group

CLUSTER_NAME=`echo $cluster_name | base64`
RESOURCE_GROUP=`echo $resource_group | base64`

PERMISSIONS=`az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/$SUBSCRIPTION_ID" -o json`
CLIENT_ID=`echo $PERMISSIONS | sed -e 's/^.*"appId"[ ]*:[ ]*"//' -e 's/".*//' | base64`
CLIENT_SECRET=`echo $PERMISSIONS | sed -e 's/^.*"password"[ ]*:[ ]*"//' -e 's/".*//' | base64`

SUBSCRIPTION_ID=`echo $ID | tr -d '"' | base64 `

NODE_RESOURCE_GROUP=`az aks show --name $cluster_name  --resource-group $resource_group -o tsv --query 'nodeResourceGroup' | base64`

echo "---
apiVersion: v1
kind: Secret
metadata:
    name: cluster-autoscaler-azure
    namespace: kube-system
data:
    ClientID: $CLIENT_ID
    ClientSecret: $CLIENT_SECRET
    ResourceGroup: $RESOURCE_GROUP
    SubscriptionID: $SUBSCRIPTION_ID
    TenantID: $TENANT_ID
    VMType: QUtTCg==
    ClusterName: $CLUSTER_NAME
    NodeResourceGroup: $NODE_RESOURCE_GROUP
---"
```

スクリプトの手順を実行した後、スクリプトは、次のような詳細をシークレット形式で出力します。

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>$
  ResourceGroup: <base64-encoded-resource-group>  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

次に、次のコマンドを実行して、ノード プールの名前を取得します。 

```console
$ kubectl get nodes --show-labels
```

出力:

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

次に、ラベル **agentpool** の値を抽出します。 クラスターのノード プールの既定の名前は "nodepool1" です。

これで、シークレットとノード プールを使用して、デプロイ チャートを作成できます。

## <a name="create-a-deployment-chart"></a>デプロイ チャートの作成

`aks-cluster-autoscaler.yaml` という名前のファイルを作成し、そこに次の YAML コードをコピーします。

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["events","endpoints"]
  verbs: ["create", "patch"]
- apiGroups: [""]
  resources: ["pods/eviction"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["pods/status"]
  verbs: ["update"]
- apiGroups: [""]
  resources: ["endpoints"]
  resourceNames: ["cluster-autoscaler"]
  verbs: ["get","update"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["watch","list","get","update"]
- apiGroups: [""]
  resources: ["pods","services","replicationcontrollers","persistentvolumeclaims","persistentvolumes"]
  verbs: ["watch","list","get"]
- apiGroups: ["extensions"]
  resources: ["replicasets","daemonsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["policy"]
  resources: ["poddisruptionbudgets"]
  verbs: ["watch","list"]
- apiGroups: ["apps"]
  resources: ["statefulsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["storage.k8s.io"]
  resources: ["storageclasses"]
  verbs: ["get", "list", "watch"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: Role
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["configmaps"]
  resourceNames: ["cluster-autoscaler-status"]
  verbs: ["delete","get","update"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: RoleBinding
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  labels:
    app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cluster-autoscaler
  template:
    metadata:
      labels:
        app: cluster-autoscaler
    spec:
      serviceAccountName: cluster-autoscaler
      containers:
      - image: k8s.gcr.io/cluster-autoscaler:{{ ca_version }}
        imagePullPolicy: Always
        name: cluster-autoscaler
        resources:
          limits:
            cpu: 100m
            memory: 300Mi
          requests:
            cpu: 100m
            memory: 300Mi
        command:
        - ./cluster-autoscaler
        - --v=3
        - --logtostderr=true
        - --cloud-provider=azure
        - --skip-nodes-with-local-storage=false
        - --nodes=1:10:nodepool1
        env:
        - name: ARM_SUBSCRIPTION_ID
          valueFrom:
            secretKeyRef:
              key: SubscriptionID
              name: cluster-autoscaler-azure
        - name: ARM_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: ResourceGroup
              name: cluster-autoscaler-azure
        - name: ARM_TENANT_ID
          valueFrom:
            secretKeyRef:
              key: TenantID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_ID
          valueFrom:
            secretKeyRef:
              key: ClientID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_SECRET
          valueFrom:
            secretKeyRef:
              key: ClientSecret
              name: cluster-autoscaler-azure
        - name: ARM_VM_TYPE
          valueFrom:
            secretKeyRef:
              key: VMType
              name: cluster-autoscaler-azure
        - name: AZURE_CLUSTER_NAME
          valueFrom:
            secretKeyRef:
              key: ClusterName
              name: cluster-autoscaler-azure
        - name: AZURE_NODE_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: NodeResourceGroup
              name: cluster-autoscaler-azure
      restartPolicy: Always
```

前の手順で作成したシークレットをコピーして貼り付け、それをファイルの先頭に挿入します。

次に、ノードの範囲を設定するために、`command` の `--nodes` 引数を、MIN:MAX:NODE_POOL_NAME 形式で入力します。 例: `--nodes=3:10:nodepool1` は、ノードの最小数を 3 に、ノードの最大数を 10 に、ノード プール名を nodepool1 に設定します。

次に、**containers** の下のイメージ フィールドに、使用するクラスター オートスケーラーのバージョンを入力します。 AKS では、v1.2.2 以降が必要です。 この例では、v1.2.2 を使用しています。

## <a name="deployment"></a>Deployment

以下を実行して、クラスター オートスケーラーをデプロイします。

```console
kubectl create -f aks-cluster-autoscaler.yaml
```

クラスター オートスケーラーが実行されているかどうかを確認するには、次のコマンドを使用してポッドの一覧を調べます。 頭に "cluster-autoscaler" が付いたポッドが実行されているはずです。 この表示を確認できれば、クラスター オートスケーラーはデプロイされています。

```console
kubectl -n kube-system get pods
```

クラスター オートスケーラーの状態を表示するには、以下を実行します。

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

## <a name="interpreting-the-cluster-autoscaler-status"></a>クラスター オートスケーラーの状態の解釈

```console
$ kubectl -n kube-system describe configmap cluster-autoscaler-status
Name:         cluster-autoscaler-status
Namespace:    kube-system
Labels:       <none>
Annotations:  cluster-autoscaler.kubernetes.io/last-updated=2018-07-25 22:59:22.661669494 +0000 UTC

Data
====
status:
----
Cluster-autoscaler status at 2018-07-25 22:59:22.661669494 +0000 UTC:
Cluster-wide:
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 registered=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC

NodeGroups:
  Name:        nodepool1
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0 cloudProviderTarget=1 (minSize=1, maxSize=5))
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 cloudProviderTarget=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC


Events:  <none>
```

クラスター オートスケーラーの状態では、クラスター全体と各ノード グループという 2 つの異なるレベルでクラスター オートスケーラーの状態を確認できます。 現時点では、AKS は 1 つのノード プールのみをサポートするため、これらのメトリックは同じです。

* Health は、ノードの全体的な正常性を示します。 クラスター オートスケーラーがクラスター内でのノードの作成または削除をスムーズに進めることができない場合、状態は、"Unhealthy" に変わります。 他のノードの状態の内訳も表示されます。
    * "Ready" は、ノードがその上にポッドをスケジュールする準備ができていることを意味します。
    * "Unready" は、ノードが開始後に停止したことを意味します。
    * "NotStarted" は、ノードがまだ完全に開始されていないことを意味します。
    * "LongNotStarted" は、ノードが妥当な制限内で開始できなかったことを意味します。
    * "Registered" は、ノードがグループに登録されていることを意味します。
    * "Unregistered" は、ノードがクラスターのプロバイダー側に存在するが、Kubernetes の登録に失敗したことを意味します。
  
* ScaleUp では、クラスター内でスケールアップを発生させる必要があることを、クラスターがいつ判断したかをチェックできます。
    * 遷移は、クラスター内のノードの数が変更されたとき、またはノードの状態が変更されたときを示します。
    * 準備中のノードの数は、利用可能であり、準備ができているクラスター内のノードの数です。 
    * cloudProviderTarget は、クラスターがそのワークロードを処理する必要があるとクラスター オートスケーラーが判断したノードの数です。

* ScaleDown では、スケールダウンの候補があるかどうかをチェックできます。 
    * スケールダウンの候補は、ワークロードを処理するクラスターの能力に影響を与えずに削除できるとクラスター オートスケーラーが判断したノードです。 
    * 提供される時間は、クラスターがスケールダウンの候補とその最後の遷移時間をチェックした最後の時間を示します。

最後に、Events には、クラスター オートスケーラーが実行したすべてのスケールアップまたはスケールダウン イベント、失敗または成功、および時刻が示されます。

## <a name="next-steps"></a>次の手順

ポッドの水平自動スケーラーでクラスター オートスケーラーを使用するには、[Kubernetes アプリケーションとインフラストラクチャのスケーリング][aks-tutorial-scale]に関する記事を参照してください。

AKS のデプロイと管理の詳細を AKS チュートリアルで確認してください。

> [!div class="nextstepaction"]
> [AKS チュートリアル][aks-tutorial-prepare-app]

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md

<!-- LINKS - external -->
[cluster-autoscale]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md
