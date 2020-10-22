---
title: Azure Cosmos DB SQL API リソースを一覧表示および取得する PowerShell スクリプト
description: Azure PowerShell スクリプト - Azure Cosmos DB SQL API の操作を一覧表示および取得する
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 03/17/2020
ms.author: mjbrown
ms.openlocfilehash: a1de4143d07bc2e5f73e7d216013e3d27c90c778
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92282609"
---
# <a name="list-and-get-databases-and-containers-for-azure-cosmos-db---sql-core-api"></a>Azure Cosmos DB - SQL (Core) API のデータベースとコンテナーを一覧表示および取得する

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-list-get.ps1 "List and get databases and containers for SQL API")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Cosmos DB アカウントを一覧表示します。または、指定された Cosmos DB アカウントを取得します。 |
| [Get-AzCosmosDBSqlDatabase](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbsqldatabase) | アカウントに存在する Cosmos DB データベースを一覧表示します。または、指定された Cosmos DB データベースをアカウントから取得します。 |
| [Get-AzCosmosDBSqlContainer](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainer) | データベースに存在する Cosmos DB コンテナーを一覧表示します。または、指定された Cosmos DB コンテナーをデータベースから取得します。 |
|**Azure リソース グループ**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。
