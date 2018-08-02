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
ms.openlocfilehash: 4f8df8e7004ca3cee832b6230dc153b21e2a6c18
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186715"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>Azure Kubernetes Service のクラスター オートスケーラー (AKS) - プレビュー

Azure Kubernetes Service (AKS) には、マネージド Kubernetes クラスターを Azure 内に簡単にデプロイするための柔軟なソリューションがあります。 リソースの需要が増えたときに、クラスター オートスケーラーを使用して、設定した制約に基づいてその需要を満たすようにクラスターを拡張できます。 クラスター オートスケーラー (CA) は、保留中のポッドに基づいてエージェント ノードをスケーリングすることでこれを行います。 それは、クラスターを定期的にチェックして、保留中のポッドや空のノードを確認し、可能であればサイズを大きくします。 既定では、CA は、保留中のポッドを 10 秒ごとにスキャンし、ノードが 10 分を越えて不要な場合はそれを削除します。 ポッドの水平オートスケーラー (HPA) と共に使用した場合、HPA は、需要に応じてポッドのレプリカとリソースを更新します。 このポッドのスケーリング後に十分なノードがない、または不要なポットがない場合は、CA が応答し、新しいノード セットでノードをスケジュールします。

> [!IMPORTANT]
> Azure Kubernetes Service (AKS) のクラスター オートスケーラーの統合は、現在は**プレビュー段階**です。 プレビュー版は、[追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。
>

## <a name="prerequisites"></a>前提条件

このドキュメントでは、RBAC が有効な AKS クラスターがあることを前提としています。 AKS クラスターが必要な場合は、[Azure Kubernetes Service (AKS) のクイック スタート][aks-quick-start]に関するページを参照してください。

 クラスター オートスケーラーを使用するには、クラスターが Kubernetes v1.10.X 以降であり、RBAC が有効になっている必要があります。 クラスターをアップグレードするには、[AKS クラスターのアップグレード][aks-upgrade]に関する記事を参照してください。

## <a name="gather-information"></a>情報を収集する

次の一覧は、オートスケーラー定義に指定する必要があるすべての情報を示しています。

- "*サブスクリプション ID*": このクラスターで使用されるサブスクリプションに対応する ID
- "*リソース グループ名*": クラスターが属するリソース グループの名前 
- "*クラスター名*": クラスターの名前
- "*クライアント ID*": アクセス許可生成手順によって付与されるアプリ ID
- "*クライアント シークレット*": アクセス許可生成手順によって付与されるアプリ シークレット
- "*テナント ID*": テナントの ID (アカウント所有者)
- "*ノード リソース グループ*": クラスター内のエージェント ノードを含むリソース グループの名前
- "*ノード プール名*": スケーリングするノード プールの名前
- "*最小ノード数*": クラスター内に存在するノードの最小数
- "*最大ノード数*": クラスター内に存在するノードの最大数
- "*VM の種類*": Kubernetes クラスターを生成するために使用するサービス

次のコマンドを実行して、サブスクリプション ID を取得します。 

``` azurecli
az account show --query id
```

次のコマンドを実行して、Azure の資格情報セットを生成します。

```console
$ az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription-id>" --output json

"appId": <app-id>,
"displayName": <display-name>,
"name": <name>,
"password": <app-password>,
"tenant": <tenant-id>
```

アプリ ID、パスワード、およびテナント ID は、次の手順で使用する clientID、clientSecret、および tenantID になります。

次のコマンドを実行して、ノード プールの名前を取得します。 

```console
$ kubectl get nodes --show-labels
```

出力:

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

次に、ラベル **agentpool** の値を抽出します。 クラスターのノード プールの既定の名前は "nodepool1" です。

ノード リソース グループの名前を取得するには、ラベル**kubernetes.azure.com<span></span>/cluster** の値を抽出します。 ノード リソース グループ名は、通常は MC_[resource-group]\_[cluster-name]_[location] の形式になっています。

VmType パラメーターは使用されるサービスを指します。ここでは AKS です。

これで、以下の情報が手元に用意されます。

- SubscriptionID
- ResourceGroup
- ClusterName
- ClientID
- ClientSecret
- TenantId
- NodeResourceGroup
- VMType

次に、これらの値のすべてを base64 でエンコードします。 たとえば、VMType 値を base64 でエンコードするには:

```console
$ echo AKS | base64
QUtTCg==
```

## <a name="create-secret"></a>シークレットの作成
このデータを使用して、前の手順で見つかった値を次の形式で使用して、デプロイ用のシークレットを作成します。

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>
  ResourceGroup: <base64-encoded-resource-group>
  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

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

## <a name="deployment"></a>デプロイ

以下を実行して、クラスター オートスケーラーをデプロイします。

```console
kubectl create -f cluster-autoscaler-containerservice.yaml
```

クラスター オートスケーラーが実行されているかどうかを確認するには、次のコマンドを使用してポッドの一覧を調べます。 "cluster-autoscaler" が前に付いているポッドがあれば、クラスター オートスケーラーはデプロイされています。

```console
kubectl -n kube-system get pods
```

クラスター オートスケーラーの状態を表示するには、以下を実行します。

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

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
