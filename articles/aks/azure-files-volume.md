---
title: Azure Kubernetes Service (AKS) の複数のポッド用の静的ボリュームを作成する
description: Azure Kubernetes Service (AKS) 上で複数の同時実行ポッドで使用するための Azure Files を含むボリュームを手動で作成する方法について説明します
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: iainfou
ms.openlocfilehash: 1a8609dbf5fa1c1e7d5f4e35b081ecaa09994eb6
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2018
ms.locfileid: "49068079"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 上で Azure ファイル共有を含むボリュームを手動で作成して使用する

コンテナーベースのアプリケーションは、データへのアクセスとデータの永続化の手段として、外部データ ボリュームが必要になることが少なくありません。 複数のポッドが同じストレージ ボリュームに同時アクセスする必要がある場合は、Azure Files を使用し、[サーバー メッセージ ブロック (SMB) プロトコル][smb-overview]を使用して接続します。 この記事では、Azure ファイル共有を手動で作成し、AKS のポッドにアタッチする方法を示します。

Kubernetes ボリュームの詳細については、[Kubernetes ボリューム][kubernetes-volumes]に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用して][ aks-quickstart-cli]または[Azure portal を使用して][aks-quickstart-portal] AKS のクイック スタートを参照してください。

また、Azure CLI バージョン 2.0.46 以降がインストール、構成されていること必要もあります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

## <a name="create-an-azure-file-share"></a>Azure ファイル共有を作成する

Azure Files を Kubernetes ボリュームとして使用するには、あらかじめ Azure Storage アカウントとファイル共有を作成しておく必要があります。 次のスクリプトでは、*myAKSShare* という名前のリソース グループ、ストレージ アカウント、*aksshare* という名前のファイル共有を作成します。

```azurecli
#!/bin/bash

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

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

ストレージ アカウント名と、スクリプトの出力の末尾に表示されるキーをメモしておきます。 これらの値は、次の手順のいずれかで Kubernetes ボリュームを作成するときに必要になります。

## <a name="create-a-kubernetes-secret"></a>Kubernetes シークレットを作成する

Kubernetes には、前の手順で作成されたファイル共有にアクセスするための資格情報が必要です。 これらの資格情報は [Kubernetes シークレット][kubernetes-secret]に格納され、Kubernetes ポッドを作成するときにそのシークレットが参照されます。

`kubectl create secret` コマンドを使用して、シークレットを作成します。 次の例では、*azure-secret* という名前の共有を作成し、前の手順の *azurestorageaccountname* と *azurestorageaccountkey* を設定します。 既存の Azure ストレージ アカウントを使用するには、アカウント名とキーを指定します。

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>ファイル共有をボリュームとしてマウントする

Azure ファイル共有をポッドにマウントするには、コンテナーの指定でボリュームを構成します。次の内容で、`azure-files-pod.yaml` という名前の新しいファイルを作成します。 ファイル共有の名前またはシークレット名を変更した場合は、*shareName* と *secretName* を更新します。 必要な場合は、`mountPath` を更新します。これはファイル共有がポッドにマウントされているパスです。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.5
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
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

`kubectl` コマンドを使用して、ポッドを作成します。

```azurecli-interactive
kubectl apply -f azure-files-pod.yaml
```

これで Azure ファイル共有が */mnt/azure* にマウントされ、ポッドが稼働状態となりました。 `kubectl describe pod mypod` を使用すると、共有が正常にマウントされていることを確認できます。 次に示したのは、その出力例の抜粋です。コンテナーにマウントされたボリュームが表示されています。

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Mon, 08 Oct 2018 19:28:34 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
[...]
```

## <a name="next-steps"></a>次の手順

AKS クラスターと Azure Files の操作について詳しくは、[Azure Files 対応の Kubernetes プラグイン][kubernetes-files]に関するページをご覧ください。

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
