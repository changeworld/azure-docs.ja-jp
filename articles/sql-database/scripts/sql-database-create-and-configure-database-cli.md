---
title: CLI サンプル - Azure SQL データベースの作成 | Microsoft Docs
description: この Azure CLI サンプル スクリプトを使用して、SQL データベースを作成します。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: CarlRabeler
manager: craigg
ms.author: carlrab
ms.reviewer: ''
ms.date: 09/20/2018
ms.openlocfilehash: d239236e9e6f7133aa57eb617c3cc9e8626fc178
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47055477"
---
# <a name="use-cli-to-create-a-single-azure-sql-database-and-configure-a-firewall-rule"></a>CLI を使用して Azure SQL データベースを 1 つ作成し、ファイアウォール規則を構成する

この Azure CLI のサンプル スクリプトでは、Azure SQL データベースを作成し、サーバー レベルのファイアウォール規則を構成します。 スクリプトが正常に実行されると、すべての Azure サービスおよび構成済み IP アドレスから SQL Database へアクセスできるようになります。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh?highlight=9-10 "Create SQL Database")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | SQL Database をホストする論理サーバーを作成します。 |
| [az sql server firewall create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create) | サーバー上のすべての SQL データベースに対する入力した IP アドレス範囲からのアクセスを許可するファイアウォール規則を作成します。 |
| [az sql db create](/cli/azure/sql/db#az-sql-db-create) | 論理サーバー内に SQL Database を作成します。 |
| [az group delete](/cli/azure/resource#az-resource-delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の SQL Database 用の CLI サンプル スクリプトは、[Azure SQL Database のドキュメント](../sql-database-cli-samples.md)のページにあります。

