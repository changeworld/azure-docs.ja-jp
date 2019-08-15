---
title: CLI の例 - フェールオーバー グループへの単一データベースの追加 - Azure SQL Database
description: Azure SQL Database の単一データベースを作成し、それをフェールオーバー グループに追加して、フェールオーバーをテストする Azure CLI サンプル スクリプト。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 12e433a146d8dd8a6fb7e1d7c40acc72e023047b
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736210"
---
# <a name="use-cli-to-add-an-azure-sql-database-single-database-into-a-failover-group"></a>CLI を使用して Azure SQL Database の単一データベースをフェールオーバー グループに追加する

この PowerShell スクリプト例では、単一のデータベースを作成し、フェールオーバー グループを作成してそれにデータベースを追加し、フェールオーバーをテストします。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to failover group")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを使用して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | サブスクリプションを現在のアクティブなサブスクリプションとして設定します。 | 
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | 単一データベースとエラスティック プールをホストする SQL Database サーバーを作成します。 |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | サーバーのファイアウォール規則を作成します。 | 
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | フェールオーバー グループを更新します。 | 
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | サーバーにフェールオーバー グループが表示されます。 |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | 現在のプライマリ サーバーからすべてのデータベースをフェールオーバーして、フェールオーバー グループのプライマリを設定します。 | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の SQL Database 用の CLI サンプル スクリプトは、[Azure SQL Database のドキュメント](../sql-database-cli-samples.md)のページにあります。
