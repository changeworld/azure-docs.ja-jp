---
title: Azure HPC Cache と Azure Kubernetes Service を統合する
description: HPC Cache と Azure Kubernetes Service を統合する方法を説明します
services: container-service
author: jbut
ms.author: jebutl
ms.topic: article
ms.date: 09/08/2021
ms.openlocfilehash: 208147894b5fb483bc9ebcc49c01fd93013c3a3d
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491528"
---
# <a name="integrate-azure-hpc-cache-with-azure-kubernetes-service"></a>Azure HPC Cache と Azure Kubernetes Service を統合する

ハイパフォーマンス コンピューティング (HPC) タスク用に、[Azure HPC Cache][hpc-cache] を使用してデータへのアクセスを高速化できます。 Azure HPC Cache で Azure 内のファイルをキャッシュすることで、クラウド コンピューティングのスケーラビリティを既存のワークフローでも実現します。 この記事では、Azure HPC Cache と Azure Kubernetes Service (AKS) を統合する方法を説明します。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

> [!IMPORTANT]
> AKS クラスターは、[Azure HPC Cache がサポートされているリージョン][hpc-cache-regions]に存在する必要があります。

また、Azure CLI バージョン 2.7 以降をインストールして構成する必要もあります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。  HPC Cache での Azure CLI の使用の詳細については、[hpc-cache-cli-prerequisites] を参照してください。

また、hpc-cache Azure CLI 拡張機能もインストールする必要があります。  次の操作を行ってください。

```azurecli
az extension add --upgrade -n hpc-cache
```

## <a name="set-up-azure-hpc-cache"></a>Azure HPC Cache を設定する

このセクションでは、HPC キャッシュを作成して構成する手順について説明します。

### <a name="1-find-the-aks-node-resource-group"></a>1. AKS ノード リソース グループを見つける

最初に、[az aks show][az-aks-show] コマンドを使用してリソース グループ名を取得し、`--query nodeResourceGroup` クエリ パラメーターを追加します｡ 同じリソース グループに HPC キャッシュを作成します。

次の例では、リソース グループ名 *myResourceGroup* にある *myAKSCluster* という名前の AKS クラスターのノード リソース グループ名を取得しています。

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

### <a name="2-create-the-cache-subnet"></a>2. キャッシュ サブネットを作成する

HPC キャッシュを実行する前に満たしておく必要がある[前提条件][hpc-cache-prereqs]がいくつかあります。  最も重要なこととして、キャッシュには、少なくとも 64 個の IP アドレスを使用できる "*専用*" のサブネットが必要です。 このサブネットで他の VM またはコンテナーをホストすることはできません。  このサブネットには、AKS ノードからアクセスできる必要があります。

専用の HPC Cache サブネットを作成します。

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyHpcCacheSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --address-prefixes 10.0.0.0/26
```

*Microsoft.StorageCache* リソース プロバイダーを登録します。

```azurecli
az provider register --namespace Microsoft.StorageCache --wait
```

> [!NOTE]
> リソース プロバイダーの登録は、完了するまでに時間がかかることがあります。

### <a name="3-create-the-hpc-cache"></a>3. HPC キャッシュを作成する

AKS クラスターと同じリージョンの、ステップ 1 で取得したノード リソース グループに、HPC キャッシュを作成します。 [az hpc-cache create][az-hpc-cache-create] を使用します。

> [!NOTE]
> HPC キャッシュの作成には約 20 分かかります。

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyHpcCacheSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
az hpc-cache create \
  --resource-group $RESOURCE_GROUP \
  --cache-size-gb "3072" \
  --location eastus \
  --subnet $SUBNET_ID \
  --sku-name "Standard_2G" \
  --name MyHpcCache
```

### <a name="4-create-a-storage-account-and-new-container"></a>4. ストレージ アカウントと新しいコンテナーを作成する

Blob Storage コンテナー用の Azure ストレージ アカウントを作成します。  HPC Cache によって、この Blob Storage コンテナーに格納されるコンテンツがキャッシュされます。

> [!IMPORTANT]
> 一意のストレージ アカウント名を選択する必要があります。  "uniquestorageaccount" は一意のものに置き換えます。

選択したストレージ アカウント名が使用可能であることを確認します。

```azurecli
STORAGE_ACCOUNT_NAME=uniquestorageaccount
az storage account check-name --name $STORAGE_ACCOUNT_NAME
```

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
STORAGE_ACCOUNT_NAME=uniquestorageaccount
az storage account create \
  -n $STORAGE_ACCOUNT_NAME \
  -g $RESOURCE_GROUP \
  -l eastus \
  --sku Standard_LRS
```

ストレージ アカウント内に BLOB コンテナーを作成します。

```azurecli
STORAGE_ACCOUNT_NAME=uniquestorageaccount
STORAGE_ACCOUNT_ID=$(az storage account show --name $STORAGE_ACCOUNT_NAME --query "id" -o tsv)
AD_USER=$(az ad signed-in-user show --query objectId -o tsv)
CONTAINER_NAME=mystoragecontainer
az role assignment create --role "Storage Blob Data Contributor" --assignee $AD_USER --scope $STORAGE_ACCOUNT_ID
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --auth-mode login
```

ストレージ アカウントと BLOB コンテナーにアクセスするためのアクセス許可を、Azure HPC Cache サービス アカウントに付与します。

```azurecli
HPC_CACHE_USER="StorageCache Resource Provider"
STORAGE_ACCOUNT_NAME=uniquestorageaccount
STORAGE_ACCOUNT_ID=$(az storage account show --name $STORAGE_ACCOUNT_NAME --query "id" -o tsv)
$HPC_CACHE_ID=$(az ad sp list --display-name "${HPC_CACHE_USER}" --query "[].objectId" -o tsv)
az role assignment create --role "Storage Account Contributor" --assignee $HPC_CACHE_ID --scope $STORAGE_ACCOUNT_ID
az role assignment create --role "Storage Blob Data Contributor" --assignee $HPC_CACHE_ID --scope $STORAGE_ACCOUNT_ID
```

### <a name="5-configure-the-storage-target"></a>5. ストレージ ターゲットを構成する

ストレージ ターゲットとして HPC キャッシュに BLOB コンテナーを追加します。

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
STORAGE_ACCOUNT_NAME=uniquestorageaccount
STORAGE_ACCOUNT_ID=$(az storage account show --name $STORAGE_ACCOUNT_NAME --query "id" -o tsv)
CONTAINER_NAME=mystoragecontainer
az hpc-cache blob-storage-target add \
  --resource-group $RESOURCE_GROUP \
  --cache-name MyHpcCache \
  --name MyStorageTarget \
  --storage-account $STORAGE_ACCOUNT_ID \
  --container-name $CONTAINER_NAME \
  --virtual-namespace-path "/myfilepath"
```

### <a name="6-set-up-client-load-balancing"></a>6. クライアントの負荷分散を設定する

クライアント接続用 IP アドレスのための Azure プライベート DNS ゾーンを作成します。

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
PRIVATE_DNS_ZONE="myhpccache.local"
az network private-dns zone create \
  -g $RESOURCE_GROUP \
  -n $PRIVATE_DNS_ZONE
az network private-dns link vnet create \
  -g $RESOURCE_GROUP \
  -n MyDNSLink \
  -z $PRIVATE_DNS_ZONE \
  -v $VNET_NAME \
  -e true
```

ラウンドロビン DNS 名を作成します。

```azurecli
DNS_NAME="server"
PRIVATE_DNS_ZONE="myhpccache.local"
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
HPC_MOUNTS0=$(az hpc-cache show --name "MyHpcCache" --resource-group $RESOURCE_GROUP --query "mountAddresses[0]" -o tsv | tr --delete '\r')
HPC_MOUNTS1=$(az hpc-cache show --name "MyHpcCache" --resource-group $RESOURCE_GROUP --query "mountAddresses[1]" -o tsv | tr --delete '\r')
HPC_MOUNTS2=$(az hpc-cache show --name "MyHpcCache" --resource-group $RESOURCE_GROUP --query "mountAddresses[2]" -o tsv | tr --delete '\r')
az network private-dns record-set a add-record -g $RESOURCE_GROUP -z $PRIVATE_DNS_ZONE -n $DNS_NAME -a $HPC_MOUNTS0
az network private-dns record-set a add-record -g $RESOURCE_GROUP -z $PRIVATE_DNS_ZONE -n $DNS_NAME -a $HPC_MOUNTS1
az network private-dns record-set a add-record -g $RESOURCE_GROUP -z $PRIVATE_DNS_ZONE -n $DNS_NAME -a $HPC_MOUNTS2
```

## <a name="create-the-aks-persistent-volume"></a>AKS 永続ボリュームを作成する

[永続ボリューム][persistent-volume]を定義するための `pv-nfs.yaml` ファイルを作成します。

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 10000Gi
  accessModes:
    - ReadWriteMany
  mountOptions:
    - vers=3
  nfs:
    server: server.myhpccache.local
    path: /
```

まず、Kubernetes クラスター用の資格情報があることを確認します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

*server* と *path* を前の手順で作成した NFS (ネットワーク ファイル システム) ボリュームの値に更新します。 [kubectl apply][kubectl-apply] コマンドを使用して永続ボリュームを作成します。

```console
kubectl apply -f pv-nfs.yaml
```

[kubectl describe][kubectl-describe] コマンドを使用して、永続ボリュームの状態が **使用可能** であることを確認します。

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistent-volume-claim"></a>永続ボリューム要求を作成する

[永続ボリューム要求][persistent-volume-claim]を定義している `pvc-nfs.yaml` を作成します。 次に例を示します。

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
      storage: 100Gi
```

永続ボリューム要求を作成するには、[kubectl apply][kubectl-apply] コマンドを使用します。

```console
kubectl apply -f pvc-nfs.yaml
```

[kubectl describe][kubectl-describe] コマンドを使用して、永続ボリューム要求の状態が **バインド済み** であることを確認します。

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-the-hpc-cache-with-a-pod"></a>ポッドを使用して HPC キャッシュをマウントする

永続ボリューム要求を使用するポッドを定義するための `nginx-nfs.yaml` ファイルを作成します。 次に例を示します。

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
    - while true; do echo $(date) >> /mnt/azure/myfilepath/outfile; sleep 1; done
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

[kubectl describe][kubectl-describe] コマンドを使用して、ポッドが実行中であることを確認します。

```console
kubectl describe pod nginx-nfs
```

ボリュームがポッドにマウントされていることを [kubectl exec][kubectl-exec] を使用して確認してポッドに接続してから、`df -h` を使用してボリュームがマウントされてるかどうかを確認します。

```console
kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
server.myhpccache.local:/myfilepath 8.0E         0      8.0E   0% /mnt/azure/myfilepath
...
```

## <a name="frequently-asked-questions-faq"></a>よく寄せられる質問 (FAQ)

### <a name="running-applications-as-non-root"></a>非ルートとしてのアプリケーションの実行

ルート以外のユーザーとしてアプリケーションを実行する必要がある場合は、ルート スカッシュを無効にして、ディレクトリの所有者を別のユーザーに変更することが必要になる場合があります。  ルート以外のユーザーが、ファイル システムにアクセスするディレクトリを所有している必要があります。  ユーザーがディレクトリを所有するためには、ルート ユーザーがディレクトリの所有者をそのユーザーに変更する必要がありますが、HPC キャッシュでルートのスカッシュが行われている場合、ルート ユーザー (UID 0) が匿名ユーザーにマップされているため、この操作は拒否されます。  ルート スカッシュとクライアント アクセス ポリシーの詳細については、[こちら][hpc-cache-access-policies]を参照してください。

### <a name="sending-feedback"></a>フィードバックの送信

お気軽にお問い合わせください。  フィードバックまたは質問は <aks-hpccache-feed@microsoft.com> までお送りください。

## <a name="next-steps"></a>次のステップ

* Azure HPC Cache の詳細については、[HPC Cache の概要][hpc-cache]に関する記事を参照してください。
* NFS と AKS の使用について詳しくは、「[NFS (Network File System) Linux Server ボリュームを手動で作成し、Azure Kubernetes Service (AKS) と共に使用する][aks-nfs]」を参照してください。

[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[hpc-cache]: ../hpc-cache/hpc-cache-overview.md
[hpc-cache-access-policies]: ../hpc-cache/access-policies.md
[hpc-cache-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=hpc-cache&regions=all
[hpc-cache-cli-prerequisites]: ../hpc-cache/az-cli-prerequisites.md
[hpc-cache-prereqs]: ../hpc-cache/hpc-cache-prerequisites.md
[az-hpc-cache-create]: /cli/azure/hpc-cache#az_hpc_cache_create
[az-aks-show]: /cli/azure/aks#az_aks_show
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[persistent-volume]: concepts-storage.md#persistent-volumes
[persistent-volume-claim]: concepts-storage.md#persistent-volume-claims
