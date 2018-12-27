---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: c5cb1c5a005265950f8dcd02c8e7675918751dab
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165356"
---
## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#az_group_create) コマンドで Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

[az storage account create](/cli/azure/storage/account#create) コマンドで汎用のストレージ アカウントを作成します。 汎用のストレージ アカウントは、BLOB、ファイル、テーブル、およびキューという 4 つのサービスすべてに使用できます。 

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>ストレージ アカウントの資格情報を指定する

Azure CLI では、このチュートリアルのほとんどのコマンドにストレージ アカウントの資格情報が必要です。 機能の実行時にいくつかのオプションもありますが、最も簡単な指定方法は、`AZURE_STORAGE_ACCOUNT` および `AZURE_STORAGE_ACCESS_KEY` 環境変数を設定することです。

まず、[az storage account keys list](/cli/azure/storage/account/keys#list) コマンドを使用してストレージ アカウント キーを表示します。

```azurecli-interactive
az storage account keys list \
    --account-name mystorageaccount \
    --resource-group myResourceGroup \
    --output table
```

次に `AZURE_STORAGE_ACCOUNT` および `AZURE_STORAGE_ACCESS_KEY` 環境変数を設定します。 この設定には、Bash シェルで `export` コマンドを使用します。

```bash
export AZURE_STORAGE_ACCOUNT="mystorageaccountname"
export AZURE_STORAGE_ACCESS_KEY="myStorageAccountKey"
```