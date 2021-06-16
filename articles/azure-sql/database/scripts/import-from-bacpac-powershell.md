---
title: PowerShell:BACPAC ファイルを Azure SQL Database の新しいデータベースにインポートする
description: BACPAC ファイルを SQL Database のデータベースにインポートするための Azure PowerShell サンプル スクリプト
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: load & move data, sqldbrb=1, devx-track-azurepowershell
ms.devlang: PowerShell
ms.topic: sample
author: shkale-msft
ms.author: shkale
ms.reviewer: mathoma
ms.date: 05/24/2019
ms.openlocfilehash: c8f584f836390fdec90c787ddc15ac09fd3695e2
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110689917"
---
# <a name="use-powershell-to-import-a-bacpac-file-into-a-database-in-sql-database"></a>PowerShell を使用して BACPAC ファイルを SQL Database のデータベースにインポートする
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

この Azure PowerShell サンプル スクリプトは、SQL Database の新しいデータベースに BACPAC ファイルからデータベースをインポートします。  

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Az PowerShell 1.4.0 以降が必要になります。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/import-from-bacpac/import-from-bacpac.ps1?highlight=20-21 "Create SQL Database")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを使用して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | データベースとエラスティック プールをホストするサーバーを作成します。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | サーバーレベルのファイアウォール規則をサーバーに作成します。 |
| [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) | BACPAC ファイルをインポートし、新しいデータベースをサーバー上に作成します。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

その他の SQL Database 用の PowerShell サンプル スクリプトは、[Azure SQL Database 用の PowerShell スクリプト](../powershell-script-content-guide.md)のページにあります。
