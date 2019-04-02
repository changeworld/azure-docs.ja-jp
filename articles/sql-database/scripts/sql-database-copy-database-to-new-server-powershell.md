---
title: PowerShell サンプル - コピー - Azure SQL データベース - 新しいサーバー | Microsoft Docs
description: 新しいサーバーに SQL データベースをコピーするための Azure PowerShell サンプル スクリプト
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: c7c016629141b978e472ba2339b9ba4ef95a876b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57892355"
---
# <a name="use-powershell-to-copy-a-sql-database-to-a-new-server"></a>PowerShell を使用して新しいサーバーに SQL データベースをコピーする

この PowerShell のサンプル スクリプトでは、新しいサーバーに既存のデータベースのコピーを作成します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは AZ PowerShell 1.4.0 以降が必要になります。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="copy-a-database-to-a-new-server"></a>新しいサーバーにデータベースをコピーする

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/copy-database-to-new-server/copy-database-to-new-server.ps1?highlight=20-23 "Copy database to new server")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```powershell
Remove-AzResourceGroup -ResourceGroupName $sourceresourcegroupname
Remove-AzResourceGroup -ResourceGroupName $targetresourcegroupname
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 単一データベースまたはエラスティック プールをホストする SQL Database サーバーを作成します。 |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | 単一のデータベースまたはエラスティック プールを作成します。 |
| [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) | 現在の時刻でのスナップショットを使用するデータベースのコピーを作成します。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の SQL Database 用の PowerShell サンプル スクリプトは、[Azure SQL Database 用の PowerShell スクリプト](../sql-database-powershell-samples.md)のページにあります。
