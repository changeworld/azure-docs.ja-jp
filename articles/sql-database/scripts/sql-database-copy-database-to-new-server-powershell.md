---
title: "Azure PowerShell スクリプト - 新しいサーバーに SQL データベースをコピーする | Microsoft Docs"
description: "Azure PowerShell のサンプル スクリプト - PowerShell を使用して新しいサーバーに SQL データベースをコピーする"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: PowerShell
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/07/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 0f750cb3021f04ade9d639e2de14e1b119cde36e
ms.lasthandoff: 03/10/2017

---

# <a name="copy-a-sql-database-to-a-new-server-using-powershell"></a>PowerShell を使用して新しいサーバーに SQL データベースをコピーする

この PowerShell のサンプル スクリプトでは、新しいサーバーに既存のデータベースのコピーを作成します。 

このスクリプトを実行する前に、`Add-AzureRmAccount` コマンドレットを使用して Azure との接続が作成されていることを確認してください。

## <a name="copy-a-database-to-a-new-server"></a>新しいサーバーにデータベースをコピーする

[!code-powershell[main](../../../powershell_scripts/sql-database/copy-database-to-new-server/copy-database-to-new-server.ps1 "新しいサーバーにデータベースをコピーする")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) | データベースまたはエラスティック プールをホストする論理サーバーを作成します。 |
| [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) | 単一のデータベースまたはプールされているデータベースとして論理サーバーにデータベースを作成します。 |
| [New-AzureRmSqlDatabaseCopy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabasecopy) | 現在の時刻でのスナップショットを使用するデータベースのコピーを作成します。 |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

その他の SQL Database 用の PowerShell サンプル スクリプトは、[Azure SQL Database 用の PowerShell スクリプト](../sql-database-powershell-samples.md)のページにあります。

