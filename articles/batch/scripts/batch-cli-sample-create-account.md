---
title: Azure CLI のサンプル スクリプト - Batch アカウントの作成 - Batch サービス | Microsoft Docs
description: Azure CLI のサンプル スクリプト - Batch サービス モードでの Batch アカウントの作成
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: danlep
ms.openlocfilehash: 86a04393761b79d76339c111d5c5c8b76dc983c2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431210"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>CLI の例: Batch サービス モードでの Batch アカウントの作成

このスクリプトは、Batch サービス モードで Azure Batch アカウントを作成し、アカウントのさまざまなプロパティを照会および更新する方法を示します。 既定の Batch サービス モードで Batch アカウントを作成するときに、Batch サービスによって内部的にコンピューティング ノードが割り当てられます。 割り当てられるコンピューティング ノードは個々の vCPU (コア) クォータの対象になります。また、アカウントは、共有キー資格情報または Azure Active Directory トークンによって認証することができます。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0.20 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Batch アカウントを作成します。 |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | ストレージ アカウントを作成します。 |
| [az batch account set](/cli/azure/batch/account#az-batch-account-set) | Batch アカウントのプロパティを更新します。  |
| [az batch account show](/cli/azure/batch/account#az-batch-account-show) | 指定された Batch アカウントの詳細を取得します。  |
| [az batch account keys list](/cli/azure/batch/account/keys#az-batch-account-keys-list) | 指定された Batch アカウントのアクセス キーを取得します。  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | さらに CLI と対話できるように、指定された Batch アカウントを認証します。  |
| [az group delete](/cli/azure/group#az-group-delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。
