---
title: PowerShell:SQL Database のデータベースの自動バックアップを復元する
description: Azure PowerShell のサンプル スクリプトを使用して、SQL Database のデータベースを自動バックアップから以前の時点に復元します。
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: devx-track-azurepowershell
ms.devlang: PowerShell
ms.topic: sample
author: shkale-msft
ms.author: shkale
ms.reviewer: mathoma
ms.date: 03/27/2019
ms.openlocfilehash: bbb61031c960b1fd096a1875caeee235cdf50212
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110708063"
---
# <a name="use-powershell-to-restore-a-database-to-an-earlier-point-in-time"></a>PowerShell を使用して以前の時点にデータベースを復元する

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

この PowerShell サンプル スクリプトは、SQL Database のデータベースを特定の時点に復元します。  

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Az PowerShell 1.4.0 以降が必要になります。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Create SQL Database")]

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
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | サーバーにデータベースを作成します。 |
| [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) | スタンドアロンまたはプールされたデータベースの geo 冗長バックアップを取得します。 |
| [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) | データベースを復元します。 |
| [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) | データベースを削除します。 |
| [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | 復元する削除済みデータベースを取得します。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

その他の SQL Database 用の PowerShell サンプル スクリプトは、[Azure SQL Database 用の PowerShell スクリプト](../powershell-script-content-guide.md)のページにあります。
