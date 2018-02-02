---
title: "AKS で Azure Files を使用する"
description: "AKS での Azure ディスクの使用"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/17/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: b6267dd2bc1b29229b2e8016e2429ed88b7bf676
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2018
---
# <a name="using-azure-files-with-kubernetes"></a>Kubernetes での Azure Files の使用

コンテナーベースのアプリケーションは、データへのアクセスとデータの永続化の手段として、外部データ ボリュームが必要になることが少なくありません。 この外部データ ストアとして Azure Files を使用することができます。 この記事では、Azure Container Service の Kubernetes ボリュームとして Azure Files を使用する方法について詳しく説明します。

Kubernetes ボリュームの詳細については、[Kubernetes ボリューム][kubernetes-volumes]に関するページを参照してください。

## <a name="create-an-azure-file-share"></a>Azure ファイル共有を作成する

Azure ファイル共有を Kubernetes ボリュームとして使用するには、あらかじめ Azure Storage アカウントとファイル共有を作成しておく必要があります。 この作業は以下のスクリプトを使って実行できます。 パラメーターの値は書き留めておくか、更新してください。いくつかの値は、Kubernetes ボリュームを作成する際に必要となります。

```azurecli-interactive
# Change these four parameters
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create the Resource Group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create the storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)
```

## <a name="create-kubernetes-secret"></a>Kubernetes シークレットの作成

Kubernetes には、ファイル共有にアクセスするための資格情報が必要です。 これらの資格情報は [Kubernetes シークレット][kubernetes-secret]に格納され、Kubernetes ポッドを作成するときにそのシークレットが参照されます。

Kubernetes シークレットを作成するとき、シークレットの値は base64 でエンコードされている必要があります。

まず、ストレージ アカウントの名前をエンコードします。 `$AKS_PERS_STORAGE_ACCOUNT_NAME` は、必要に応じて実際の Azure ストレージ アカウントの名前に置き換えてください。

```azurecli-interactive
echo -n $AKS_PERS_STORAGE_ACCOUNT_NAME | base64
```

次に、ストレージ アカウント キーをエンコードします。 `$STORAGE_KEY` は、必要に応じて実際の Azure ストレージ アカウント キーの名前に置き換えてください。

```azurecli-interactive
echo -n $STORAGE_KEY | base64
```

`azure-secret.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。 `azurestorageaccountname` と `azurestorageaccountkey` の値は、直前の手順で取得した、base64 でエンコードされた値に置き換えてください。

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: azure-secret
type: Opaque
data:
  azurestorageaccountname: <base64_encoded_storage_account_name>
  azurestorageaccountkey: <base64_encoded_storage_account_key>
```

[kubectl create][kubectl-create] コマンドを使用してシークレットを作成します。

```azurecli-interactive
kubectl create -f azure-secret.yaml
```

## <a name="mount-file-share-as-volume"></a>ファイル共有をボリュームとしてマウントする

ポッドには、その仕様の中で必要なボリュームを構成することで、Azure Files 共有をマウントすることができます。次の内容で、`azure-files-pod.yaml` という名前の新しいファイルを作成します。 `aksshare` は、Azure Files 共有に割り当てられた名前に置き換えてください。

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: kubernetes/pause
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

kubectl を使用してポッドを作成します。

```azurecli-interactive
kubectl apply -f azure-files-pod.yaml
```

これで Azure ファイル共有が `/mnt/azure` ディレクトリにマウントされ、コンテナーが稼働状態となりました。 このボリュームのマウントは、`kubectl describe pod azure-files-pod` でポッドを調べることにより確認できます。

## <a name="next-steps"></a>次の手順

Azure Files を使用した Kubernetes ボリュームについて、さらに詳しい情報を確認します。

> [!div class="nextstepaction"]
> [Azure Files 対応の Kubernetes プラグイン][kubernetes-files]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
