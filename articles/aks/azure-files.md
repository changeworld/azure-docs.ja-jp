---
title: "AKS で Azure Files を使用する | Microsoft Docs"
description: "AKS での Azure ディスクの使用"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6e88c590e11aa8d2f4ae17e8b5e164483f0a6820
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2017
---
# <a name="using-azure-files-with-kubernetes"></a>Kubernetes での Azure Files の使用

コンテナー ベースのアプリケーションは、データへのアクセスとデータの永続化の手段として、外部データ ボリュームが必要になることが少なくありません。 この外部データ ストアとして Azure Files を使用することができます。 この記事では、Azure Container Service の Kubernetes ボリュームとして Azure Files を使用する方法について詳しく説明します。

Kubernetes ボリュームの詳細については、[Kubernetes ボリューム][kubernetes-volumes]に関するページを参照してください。

## <a name="creating-a-file-share"></a>ファイル共有の作成

Azure Container Service には、既存の Azure ファイル共有を使用することができます。 新たに作成する必要がある場合は、以下に示した一連のコマンドを使用してください。

[az group create][az-group-create] コマンドを使用して、Azure ファイル共有のリソース グループを作成します。 ストレージ アカウントのリソース グループと Kubernetes クラスターは、同じリージョンに存在する必要があります。

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

[az storage account create][az-storage-create] コマンドを使用して Azure Storage アカウントを作成します。 ストレージ アカウントには、一意の名前を使用します。 `--name` 引数の値を一意の値に置き換えてください。

```azurecli-interactive
az storage account create --name mystorageaccount --resource-group myResourceGroup --sku Standard_LRS
```

[az storage account keys list ][az-storage-key-list] コマンドを使用して、ストレージ キーを取得します。 `--account-name` 引数の値は、一意のストレージ アカウント名に置き換えてください。

いずれかのキーの値を書き留めておいてください。この後の手順で使用します。

```azurecli-interactive
az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup --output table
```

[az storage share create][az-storage-share-create] コマンドを使用して Azure ファイル共有を作成します。 `--account-key` の値は、直前の手順で収集した値に置き換えてください。

```azurecli-interactive
az storage share create --name myfileshare --account-name mystorageaccount --account-key <key>
```

## <a name="create-kubernetes-secret"></a>Kubernetes シークレットの作成

Kubernetes には、ファイル共有にアクセスするための資格情報が必要です。 Azure Storage アカウントの名前とキーは、ポッドごとに保存されるのではなく、[Kubernetes シークレット][kubernetes-secret]に一度だけ保存されて、各 Azure Files ボリュームにより参照されます。 

Kubernetes シークレット マニフェスト内の値は、base64 でエンコードされている必要があります。 以下のコマンドを使用して、エンコードした値を取得してください。

まず、ストレージ アカウントの名前をエンコードします。 `storage-account` は、実際の Azure Storage アカウントの名前に置き換えてください。

```azurecli-interactive
echo -n <storage-account> | base64
```

次に、ストレージ アカウントのアクセス キーが必要です。 次のコマンドを実行して、エンコード済みのキーを取得します。 `storage-key` は、前の手順で収集したキーに置き換えてください。

```azurecli-interactive
echo -n <storage-key> | base64
```

`azure-secret.yml` という名前のファイルを作成し、そこに以下の YAML をコピーします。 `azurestorageaccountname` と `azurestorageaccountkey` の値は、直前の手順で取得した、base64 でエンコードされた値に置き換えてください。

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

[kubectl apply][kubectl-apply] コマンドを使用してシークレットを作成します。

```azurecli-interactive
kubectl apply -f azure-secret.yml
```

## <a name="mount-file-share-as-volume"></a>ファイル共有をボリュームとしてマウントする

ポッドには、その仕様の中で必要なボリュームを構成することで、Azure Files 共有をマウントすることができます。次の内容で、`azure-files-pod.yml` という名前の新しいファイルを作成します。 `share-name` は、Azure Files 共有に割り当てられた名前に置き換えてください。

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
      shareName: <share-name>
      readOnly: false
```

kubectl を使用してポッドを作成します。

```azurecli-interactive
kubectl apply -f azure-files-pod.yml
```

これで Azure ファイル共有が `/mnt/azure` ディレクトリにマウントされ、コンテナーが稼働状態となりました。 このボリュームのマウントは、`kubectl describe pod azure-files-pod` でポッドを調べることにより確認できます。

## <a name="next-steps"></a>次のステップ

Azure Files を使用した Kubernetes ボリュームについて、さらに詳しい情報を確認します。

> [!div class="nextstepaction"]
> [Azure Files 用 Kubernetes プラグイン](https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md)

<!-- LINKS -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[kubectl-apply]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#apply
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[az-group-create]: /cli/azure/group#az_group_create