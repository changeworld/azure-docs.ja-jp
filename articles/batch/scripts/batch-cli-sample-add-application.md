---
title: Azure CLI のサンプル スクリプト - Batch へのアプリケーションの追加
description: このサンプルでは、Azure Batch プールまたはタスクで使用するアプリケーションを追加する方法について説明します。
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 06afb59a76e763c25e943c3be1531372a6bd2aa1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765269"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>CLI の例: Azure Batch アカウントへのアプリケーションの追加

このスクリプトでは、Azure Batch プールまたはタスクで使用するアプリケーションを追加する方法について説明します。 Batch アカウントに追加するようにアプリケーションをセットアップするには、実行ファイルをすべての依存関係と共に zip ファイルにパッケージ化します。 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - このチュートリアルには、Azure CLI のバージョン 2.0.20 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。
表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | ストレージ アカウントを作成します。 |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Batch アカウントを作成します。 |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | さらに CLI と対話できるように、指定された Batch アカウントを認証します。  |
| [az batch application create](/cli/azure/batch/application#az_batch-application-create) | アプリケーションを作成します。  |
| [az batch application package create](/cli/azure/batch/application/package#az_batch-application-package-create) | アプリケーション パッケージを指定されたアプリケーションに追加します。  |
| [az batch application set](/cli/azure/batch/application#az_batch-application-set) | アプリケーションのプロパティを更新します。  |
| [az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。
