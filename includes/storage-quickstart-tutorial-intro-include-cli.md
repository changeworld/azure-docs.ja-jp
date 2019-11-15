---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 4ad977dc8cbaa85360092dbfd391a3c3b88f67bb
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747921"
---
## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group) コマンドで Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli-interactive
az group create \
    --name <resource-group-name> \
    --location <location>
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

[az storage account create](/cli/azure/storage/account) コマンドで汎用のストレージ アカウントを作成します。 汎用のストレージ アカウントは、BLOB、ファイル、テーブル、およびキューという 4 つのサービスすべてに使用できます。

山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group <resource-group-name> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>ストレージ アカウントの資格情報を指定する

Azure CLI では、このチュートリアルのほとんどのコマンドにストレージ アカウントの資格情報が必要です。 機能の実行時にいくつかのオプションもありますが、最も簡単な指定方法は、`AZURE_STORAGE_ACCOUNT` および `AZURE_STORAGE_KEY` 環境変数を設定することです。

> [!NOTE]
> この記事では、Bash を使用して環境変数を設定する方法について説明します。 他の環境では、構文の変更が必要になる場合があります。

まず、[az storage account keys list](/cli/azure/storage/account/keys) コマンドを使用してストレージ アカウント キーを表示します。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli-interactive
az storage account keys list \
    --account-name <account-name> \
    --resource-group <resource-group-name> \
    --output table
```

次に `AZURE_STORAGE_ACCOUNT` および `AZURE_STORAGE_KEY` 環境変数を設定します。 これを行うには、Bash シェルで `export` コマンドを使用します。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```bash
export AZURE_STORAGE_ACCOUNT="<account-name>"
export AZURE_STORAGE_KEY="<account-key>"
```

Azure portal を使用してアカウント アクセス キーを取得する方法の詳細については、「[Azure portal でストレージ アカウント設定を管理する](../articles/storage/common/storage-account-manage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#access-keys)」の「 **[アクセス キー]** 」を参照してください。