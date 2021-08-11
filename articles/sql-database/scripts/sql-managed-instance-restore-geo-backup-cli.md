---
title: geo バックアップを復元する CLI の例 - Azure SQL Database
description: geo 冗長バックアップから Azure SQL Managed Instance データベースを復元する Azure CLI サンプル スクリプト。
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: rothja
ms.author: jroth
ms.reviewer: mathoma
ms.date: 07/03/2019
ms.openlocfilehash: 014ce4fdc45ba219cc7f7c68aa6b4aa16d192a2f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114470039"
---
# <a name="use-cli-to-restore-a-managed-instance-database-to-another-geo-region"></a>CLI を使用して Managed Instance データベースを別の geo リージョンに復元する

この Azure CLI サンプル スクリプトでは、リモート geo リージョンから Azure SQL Managed Instance データベースを復元します (geo リストア)。  

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

### <a name="prerequisites"></a>前提条件

マネージド インスタンスのペアがあらかじめ存在すること。[Azure CLI を使用して Azure SQL Managed Instance を作成する方法](sql-database-create-configure-managed-instance-cli.md)に関するページを参照してください。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>スクリプトを実行する

```azurepowershell-interactive
#!/bin/bash
$instance = "<instanceId>" # add instance here
$targetInstance = "<targetInstanceId>" # add target instance here
$resource = "<resourceId>" # add resource here

$randomIdentifier = $(Get-Random)
$managedDatabase = "managedDatabase-$randomIdentifier"

echo "Creating $($managedDatabase) on $($instance)..."
az sql midb create -g $resource --mi $instance -n $managedDatabase

echo "Restoring $($managedDatabase) to $($targetInstance)..."
az sql midb restore -g $resource --mi $instance -n $managedDatabase --dest-name $targetInstance --time "2018-05-20T05:34:22"
```

## <a name="sample-reference"></a>サンプル リファレンス

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| スクリプト | 説明 |
|---|---|
| [az sql midb](/cli/azure/sql/midb) | Managed Instance のデータベース コマンド。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の SQL Database 用の CLI サンプル スクリプトは、[Azure SQL Database のドキュメント](../../azure-sql/database/az-cli-script-samples-content-guide.md)のページにあります。
