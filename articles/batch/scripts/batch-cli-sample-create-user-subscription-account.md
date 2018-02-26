---
title: "Azure CLI のサンプル スクリプト - Batch アカウントの作成 - ユーザー サブスクリプション | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - ユーザー サブスクリプション モードでの Batch アカウントの作成"
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: danlep
ms.openlocfilehash: 6f00a522f1cbf8ebecd7883dd3d462e94d2cb9b4
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>CLI の例: ユーザー サブスクリプション モードでの Batch アカウントの作成

このスクリプトでは、ユーザー サブスクリプション モードで Azure Batch アカウントを作成します。 サブスクリプションにコンピューティング ノードを割り当てるアカウントは、Azure Active Directory トークンを使用して認証される必要があります。 割り当てられたコンピューティング ノードは、サブスクリプションの vCPU (コア) クォータに対してカウントされます。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0.20 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az role assignment create](/cli/azure/role#az_role_assignment_create) | ユーザー、グループ、またはサービス プリンシパルに対して、新しいロール割り当てを作成します。 |
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_create) | Key Vault を作成します。 |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_set_policy) | 指定した Key Vault のセキュリティ ポリシーを更新します。 |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Batch アカウントを作成します。  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | さらに CLI と対話できるように、指定された Batch アカウントを認証します。  |
| [az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure/overview)のページをご覧ください。
