---
title: Azure NetApp Files と Azure Kubernetes Service を統合する | Microsoft Docs
description: Azure NetApp Files と Azure Kubernetes Service をプロビジョニングする方法について説明します。
services: container-service
ms.topic: article
ms.date: 10/18/2021
ms.openlocfilehash: a88f2ac33d22852f1b14be65434eb2e354c45155
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130162303"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Azure NetApp Files と Azure Kubernetes Service を統合する

永続ボリュームとは、Kubernetes ポッドで使用するためにプロビジョニングされているストレージの一部です。 永続ボリュームは 1 つまたは複数のポッドで使用でき、動的または静的にプロビジョニングできます。 この記事では、Azure Kubernetes Service (AKS) クラスター内のポッドで使用される [Azure NetApp Files][anf] ボリュームを作成する方法について説明します。

[Azure NetApp Files][anf] は、エンタープライズクラスでハイパフォーマンスの Azure 上で実行される従量制課金ファイル ストレージ サービスです。 Azure NetApp Files ボリュームを Kubernetes のワークロードに使用する場合、Kubernetes ユーザーには以下の 2 つの選択肢があります。

* Azure NetApp Files ボリュームを **静的** に作成する: このシナリオでは、ボリュームの作成は AKS の外部で行われます。ボリュームは `az`/Azure UI を使用して作成され、`PersistentVolume` の作成によって Kubernetes に公開されます。 静的に作成された Azure NetApp Files ボリュームには多くの制限 (たとえば、拡張できない、過剰なプロビジョニングが必要など) があるため、ほとんどのユース ケースには推奨されません。
* **オンデマンド** で Azure NetApp Files ボリュームを作成し、Kubernetes でオーケストレーションを行う: この方法は、Kubernetes で複数のボリュームを直接作成する場合に **推奨される** 操作モードで、[Astra Trident](https://docs.netapp.com/us-en/trident/index.html) を使用して実施します。 Astra Trident は、CSI に準拠した動的ストレージ オーケストレーターであり、Kubernetes を通じてボリュームをネイティブにプロビジョニングするのに役立ちます。

CSI ドライバーを使用して AKS ワークロードから Azure NetApp Files ボリュームを直接使用することは、ほとんどのユース ケースで **強く推奨されます**。 この要件は、Kubernetes 用のオープンソースの動的ストレージ オーケストレーターである Astra Trident を使用することで対応可能です。 Astra Trident は、Kubernetes 専用のエンタープライズ レベルのストレージ オーケストレーターであり、NetApp で完全にサポートされています。 ストレージ プロビジョニングを自動化することで、Kubernetes クラスターからストレージへのアクセスを簡略化します。 Astra Trident の Azure NetApp Files 用 Container Storage Interface (CSI) ドライバーを利用することで、基になる詳細情報を抽象化し、オンデマンドでボリュームの作成、拡張、スナップショットすることができます。 また、Astra Trident を使用すると、Astra Trident の上に構築された [Astra Control Service](https://cloud.netapp.com/astra-control) を使用して、Azure リージョン内および Azure リージョン全体のクラスター間で AKS ワークロードのアプリケーション データ ライフサイクルをバックアップ、復旧、移動、管理し、ビジネスおよびサービス継続性のニーズを満たすことができます。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

> [!IMPORTANT]
> また、AKS クラスターは、[Azure NetApp Files をサポートするリージョン][anf-regions]にある必要があります。

また、Azure CLI バージョン 2.0.59 以降がインストールされ、構成されている必要もあります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

### <a name="prerequisites"></a>前提条件

Azure NetApp Files を使用する場合、次の考慮事項が適用されます。

* Azure NetApp Files は、[選択された Azure リージョン][anf-regions]でのみ利用できます。
* AKS クラスターの初期デプロイ後、Azure NetApp Files ボリュームのプロビジョニングを静的または動的に行うかを選択できます。
* Azure NetApp Files で動的プロビジョニングを使用するには [Astra Trident](https://docs.netapp.com/us-en/trident/index.html) バージョン 19.07 以降をインストールして構成します。

## <a name="configure-azure-netapp-files"></a>Azure NetApp Files の構成

*Microsoft.NetApp* リソース プロバイダーを登録します。

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> この処理には、完了までに時間がかかる場合があります。

AKS で使用するための Azure NetApp アカウントを作成する場合は、**ノード** リソース グループ内にアカウントを作成する必要があります。 最初に、[az aks show][az-aks-show] コマンドを使用してリソース グループ名を取得し、`--query nodeResourceGroup` クエリ パラメーターを追加します｡ 次の例では、リソース グループ名 *myResourceGroup* にある *myAKSCluster* という名前の AKS クラスターのノード リソース グループを取得しています。

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

[az netappfiles account create][az-netappfiles-account-create] を使用して、その **ノード** リソース グループ (AKS クラスターと同じリージョン) に Azure NetApp Files アカウントを作成します。 次の例では、*MC_myResourceGroup_myAKSCluster_eastus* リソース グループと *eastus* リージョンに *myaccount1* という名前のアカウントを作成します。

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

[az netappfiles pool create][az-netappfiles-pool-create] を使用して新しい容量プールを作成します。 次の例では、*Premium* サービス レベルの 4 TB の *mypool1* という名前の新しい容量プールを作成します。

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

[az network vnet subnet create][az-network-vnet-subnet-create] を使用して、[Azure NetApp Files に委任][anf-delegate-subnet]するサブネットを作成します。 *このサブネットは、AKS クラスターと同じ仮想ネットワーク内に存在する必要があります。*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

ボリュームのプロビジョニングは、静的または動的に行うことができます。 両方のオプションの詳細は、以下に記載されています。

## <a name="provision-azure-netapp-files-volumes-statically"></a>Azure NetApp Files ボリュームを静的にプロビジョニングする

[az netappfiles volume create][az-netappfiles-volume-create] を使用して、ボリュームを作成します。

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Note that file path needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --file-path $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

### <a name="create-the-persistentvolume"></a>PersistentVolume の作成

[az netappfiles volume show][az-netappfiles-volume-show] を使用して、ボリュームの詳細を一覧表示します。

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

PersistentVolume を定義する `pv-nfs.yaml` を作成します。 `path` を前のコマンドからの *creationToken* に、`server` を *ipAddress* に置き換えます。 次に例を示します。

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  mountOptions:
    - vers=3
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

*server* と *path* を前の手順で作成した NFS (ネットワーク ファイル システム) ボリュームの値に更新します。 [kubectl apply][kubectl-apply] コマンドを使用して PersistentVolume を作成します。

```console
kubectl apply -f pv-nfs.yaml
```

[kubectl describe][kubectl-describe] コマンドを使用して、PersistentVolume の "*状態*" が "*使用可能*" であることを確認します。

```console
kubectl describe pv pv-nfs
```

### <a name="create-the-persistentvolumeclaim"></a>PersistentVolumeClaim の作成

PersistentVolume を定義する `pvc-nfs.yaml` を作成します。 次に例を示します。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

[kubectl apply][kubectl-apply] コマンドを使用して PersistentVolumeClaim を作成します。

```console
kubectl apply -f pvc-nfs.yaml
```

[kubectl describe][kubectl-describe] コマンドを使用して、PersistentVolumeClaim の "*状態*" が "*使用可能*" であることを確認します。

```console
kubectl describe pvc pvc-nfs
```

### <a name="mount-with-a-pod"></a>ポッドを使ったマウント

PersistentVolumeClaim を使用するポッドを定義する `nginx-nfs.yaml` を作成します。 次に例を示します。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

[kubectl apply][kubectl-apply] コマンドを使用してポッドを作成します。

```console
kubectl apply -f nginx-nfs.yaml
```

[kubectl describe][kubectl-describe] コマンドを使用して、ポッドが "*実行中*" であることを確認します。

```console
kubectl describe pod nginx-nfs
```

ボリュームがポッドにマウントされていることを [kubectl exec][kubectl-exec] を使用して確認してポッドに接続してから、`df -h` を使用してボリュームがマウントされてるかどうかを確認します。

```console
$ kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="provision-azure-netapp-files-volumes-dynamically"></a>Azure NetApp Files ボリュームを動的にプロビジョニングする

### <a name="install-and-configure-astra-trident"></a>Astra Trident をインストールして構成する

ボリュームを動的にプロビジョニングするには、Astra Trident をインストールする必要があります。 Astra Trident は、Kubernetes 専用に開発された NetApp の動的ストレージ プロビジョナーです。 Astra Trident の業界標準である [Container Storage Interface (CSI)](https://kubernetes-csi.github.io/docs/) ドライバーを使用して、Kubernetes アプリケーションのストレージの消費を簡素化します。 Astra Trident は、Kubernetes クラスターにポッドとしてデプロイされ、Kubernetes ワークロードに動的ストレージ オーケストレーション サービスを提供します。

詳細については、[ドキュメント]https://docs.netapp.com/us-en/trident/index.html) を参照してください。

次の手順に進む前に、次の手順を実行する必要があります。

1. **Astra Trident をインストールします**。 Trident は、オペレーター、Helm チャート、または `tridentctl` を使用してインストールすることができます。 以下の手順では、オペレーターを使用して Astra Trident をインストールする方法について説明します。 他のインストール方法については、[インストール ガイド](https://docs.netapp.com/us-en/trident/trident-get-started/kubernetes-deploy.html)を参照してください。

2. **バックエンドを作成します**。 Astra Trident に Azure NetApp Files サブスクリプションとボリュームを作成する場所を指示するために、バックエンドを作成します。 この手順では、前の手順で作成したアカウントの詳細が必要です。

#### <a name="install-astra-trident-using-the-operator"></a>オペレーターを使用して Astra Trident をインストールする

このセクションでは、オペレーターを使用して Astra Trident をインストールする方法について説明します。 また、残りの他の方法でインストールすることもできます。

* [Helm チャート](https://docs.netapp.com/us-en/trident/trident-get-started/kubernetes-deploy-operator.html)。
* [`tridentctl`](https://docs.netapp.com/us-en/trident/trident-get-started/kubernetes-deploy-tridentctl.html).

各オプションの仕組みを理解し、ご自身に最適なオプションを選択するには、[Trident のデプロイ](https://docs.netapp.com/us-en/trident/trident-get-started/kubernetes-deploy.html)に関するページを参照してください。

[GitHub リポジトリ](https://github.com/NetApp/trident/releases)から Astra Trident をダウンロードします。 目的のバージョンを選択し、インストーラー バンドルをダウンロードします。

```console
#Download Astra Trident

$  wget https://github.com/NetApp/trident/releases/download/v21.07.1/trident-installer-21.07.1.tar.gz
$  tar xzvf trident-installer-21.07.1.tar.gz
```
`deploy/bundle.yaml` を使用してオペレーターを展開します。

```console
$  kubectl create ns trident

namespace/trident created

$  kubectl apply -f trident-installer/deploy/bundle.yaml -n trident

serviceaccount/trident-operator created
clusterrole.rbac.authorization.k8s.io/trident-operator created
clusterrolebinding.rbac.authorization.k8s.io/trident-operator created
deployment.apps/trident-operator created
podsecuritypolicy.policy/tridentoperatorpods created
```

`TridentOrchestrator` を作成して Astra Trident をインストールします。

```console
$ kubectl apply -f trident-installer/deploy/crds/tridentorchestrator_cr.yaml

tridentorchestrator.trident.netapp.io/trident created 
```

オペレーターによって、`TridentOrchestrator` の仕様に記載されているパラメータを使用してインストールが行われます。構成パラメーターやバックエンドの例については、広範な[インストール](https://docs.netapp.com/us-en/trident/trident-get-started/kubernetes-deploy.html)および[バックエンド ガイド](https://docs.netapp.com/us-en/trident/trident-use/backends.html)を参照してください。

Astra Trident がインストールされていることを確認します。 

```console
$  kubectl describe torc trident
Name:         trident
Namespace:
Labels:       <none>
Annotations:  <none>
API Version:  trident.netapp.io/v1
Kind:         TridentOrchestrator
...
Spec:
  Debug:      true
  Namespace:  trident
Status:
  Current Installation Params:
    IPv6:                       false
    Autosupport Hostname:
    Autosupport Image:          netapp/trident-autosupport:21.01
    Autosupport Proxy:
    Autosupport Serial Number:
    Debug:                      true
    Enable Node Prep:           false
    Image Pull Secrets:
    Image Registry:
    k8sTimeout:           30
    Kubelet Dir:          /var/lib/kubelet
    Log Format:           text
    Silence Autosupport:  false
    Trident Image:        netapp/trident:21.07.1
  Message:                Trident installed
  Namespace:              trident
  Status:                 Installed
  Version:                v21.07.1
Events:
  Type    Reason      Age   From                        Message
  ----    ------      ----  ----                        -------
  Normal  Installing  74s   trident-operator.netapp.io  Installing Trident
  Normal  Installed   67s   trident-operator.netapp.io  Trident installed
```

### <a name="create-a-backend"></a>バックエンドを作成する

Astra Trident がインストールされたら、自分の Azure NetApp Files サブスクリプションを指すバックエンドを作成します。

```console
$  kubectl apply -f trident-installer/sample-input/backends-samples/azure-netapp-files/backend-anf.yaml -n trident

secret/backend-tbc-anf-secret created
tridentbackendconfig.trident.netapp.io/backend-tbc-anf created
```

コマンドを実行する前に、`backend-anf.yaml` を更新して、Azure NetApp Files サブスクリプションの次のような詳細を含める必要があります。

* Azure NetApp Files が有効な Azure サブスクリプションの `subscriptionID`。 次に、 
* Azure NetApp Files サービスに対する十分なアクセス許可を持つ Azure Active Directory (AD) の[アプリの登録](../active-directory/develop/howto-create-service-principal-portal.md)の `tenantID`、`clientID`、および `clientSecret`。 アプリの登録には、Azure によって事前定義されている `Owner` または `Contributor` ロールが付与されている必要があります。
* 少なくとも 1 つの委任されたサブネットを含む Azure の場所。

さらに、別のサービス レベルを指定することもできます。 Azure NetApp Files には、3 つの[サービス レベル](../azure-netapp-files/azure-netapp-files-service-levels.md) (Standard、Premium、Ultra) が用意されています。

### <a name="create-a-storageclass"></a>StorageClass を作成する

ストレージ クラスは、保存の単位を永続ボリュームを使用して動的に作成する方法を定義します。 Azure NetApp Files ボリュームを使用するには、ストレージ クラスを作成する必要があります。 `anf-storageclass.yaml` という名前のファイルを作成し、そこに次のマニフェストをコピーします。

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azure-netapp-files
provisioner: csi.trident.netapp.io
parameters:
  backendType: "azure-netapp-files"
  fsType: "nfs"
```

[kubectl apply][kubectl-apply] コマンドを使用してストレージ クラスを作成します。

```console
$  kubectl apply -f anf-storageclass.yaml

storageclass/azure-netapp-files created

$  kubectl get sc
NAME                 PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
azure-netapp-files   csi.trident.netapp.io   Delete          Immediate           false                  3s
```

### <a name="create-a-persistentvolumeclaim"></a>PersistentVolumeClaim を作成する

PersistentVolumeClaim (PVC) は、ユーザーによるストレージの要求です。 PersistentVolumeClaim を作成すると、Astra Trident によって Azure NetApp Files ボリュームが自動的に作成され、Kubernetes ワークロードで使用できるようになります。

`anf-pvc.yaml` というファイルを作成し、次のマニフェストを指定します。 この例では、*ReadWriteMany* である 1 TiB のボリュームが作成されています。

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: anf-pvc
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Ti
  storageClassName: azure-netapp-files
```

[kubectl apply][kubectl-apply] コマンドを使用して永続ボリューム要求を作成します。

```console
$  kubectl apply -f anf-pvc.yaml

persistentvolumeclaim/anf-pvc created

$  kubectl get pvc
kubectl get pvc -n trident
NAME      STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS         AGE
anf-pvc   Bound    pvc-bffa315d-3f44-4770-86eb-c922f567a075   1Ti        RWO            azure-netapp-files   62s
```

### <a name="use-the-persistent-volume"></a>永続ボリュームの使用

PVC が作成されたら、Azure NetApp Files ボリュームにアクセスするためにポッドを起動することができます。 次のマニフェストを使用して、前の手順で作成した Azure NetApp Files ボリュームをマウントする NGINX ポッドを定義することができます。 この例では、ボリュームは `/mnt/data` にマウントされます。

次のマニフェストを含む、`anf-nginx-pod.yaml` という名前のファイルを作成します。

```yml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: mcr.microsoft.com/oss/nginx/nginx:latest1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/data"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: anf-pvc
```

[kubectl apply][kubectl-apply] コマンドを使用してポッドを作成します。

```console
$  kubectl apply -f anf-nginx-pod.yaml

pod/nginx-pod created
```

Kubernetes によって、`/mnt/data` にある `nginx` コンテナー内に、ボリュームがマウントされた、アクセス可能なポッドが作成されました。 `kubectl describe` を使用してポッドのイベント ログを確認し、これが行われたことを確認します。

```console
$  kubectl describe pod nginx-pod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  anf-pvc
    ReadOnly:   false
  default-token-k7952:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-k7952
    Optional:    false
[...]
Events:
  Type    Reason                  Age   From                     Message
  ----    ------                  ----  ----                     -------
  Normal  Scheduled               15s   default-scheduler        Successfully assigned trident/nginx-pod to brameshb-non-root-test
  Normal  SuccessfulAttachVolume  15s   attachdetach-controller  AttachVolume.Attach succeeded for volume "pvc-bffa315d-3f44-4770-86eb-c922f567a075"
  Normal  Pulled                  12s   kubelet                  Container image "mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine" already present on machine
  Normal  Created                 11s   kubelet                  Created container nginx
  Normal  Started                 10s   kubelet                  Started container nginx
```

Astra Trident では、次のような Azure NetApp Files の多くの機能がサポートされています。

* [ボリュームの拡張](https://docs.netapp.com/us-en/trident/trident-use/vol-expansion.html)
* [オンデマンドのボリューム スナップショット](https://docs.netapp.com/us-en/trident/trident-use/vol-snapshots.html)
* [ボリュームのインポート](https://docs.netapp.com/us-en/trident/trident-use/vol-import.html)

## <a name="next-steps"></a>次のステップ

* Azure NetApp Files の詳細については、「[Azure NetApp Files とは][anf]」を参照してください。

[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account#az_netappfiles_account_create
[az-netapp-files-dynamic]: azure-netapp-files-dynamic.md
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool#az_netappfiles_pool_create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume#az_netappfiles_volume_create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume#az_netappfiles_volume_show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
