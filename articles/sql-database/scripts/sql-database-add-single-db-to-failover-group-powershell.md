---
title: PowerShell の例 - フェールオーバー グループ - Azure SQL Database の単一データベース | Microsoft Docs
description: Azure SQL Database の単一データベースを作成し、それをフェールオーバー グループに追加して、フェールオーバーをテストする Azure PowerShell サンプル スクリプト。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 1f975eb11e546a0ece862ac239ac4321ed8e4110
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570087"
---
# <a name="use-powershell-to-add-an-azure-sql-database-single-database-to-a-failover-group"></a>PowerShell を使用して Azure SQL Database の単一データベースをフェールオーバー グループに追加します 

この PowerShell スクリプト例では、単一のデータベースを作成し、フェールオーバー グループを作成してそれにデータベースを追加し、フェールオーバーをテストします。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは AZ PowerShell 1.4.0 以降が必要になります。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="sample-scripts"></a>サンプルのスクリプト

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを使用して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 単一データベースとエラスティック プールをホストする SQL Database サーバーを作成します。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 論理サーバー用のファイアウォール規則を作成します。 | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Azure SQL Database の新しい単一データベースを作成します。 | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | 新しいフェールオーバー グループを作成します。 |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | 1 つまたは複数の SQL データベースを取得します。 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | 1 つまたは複数の Azure SQL データベースをフェールオーバー グループに追加します。 |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Azure SQL Database のフェールオーバー グループを取得または一覧表示します。 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Azure SQL Databese のフェールオーバー グループのフェールオーバーを実行します。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループを削除します | 

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の SQL Database 用の PowerShell サンプル スクリプトは、[Azure SQL Database 用の PowerShell スクリプト](../sql-database-powershell-samples.md)のページにあります。
