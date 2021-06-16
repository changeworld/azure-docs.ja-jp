---
title: Azure Cosmos DB Gremlin API のデータベースとグラフを作成する PowerShell スクリプト
description: Azure PowerShell script - Azure Cosmos DB Gremlin API のデータベースとグラフを作成する
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 823efb6a88e3ef74b90930aa99b71431ca2033e7
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110680255"
---
# <a name="create-a-database-and-graph-for-azure-cosmos-db---gremlin-api"></a>Azure Cosmos DB - Gremlin API のデータベースとグラフを作成する
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

このサンプルには、Azure PowerShell Az 5.4.0 以降が必要です。 `Get-Module -ListAvailable Az` を実行して、インストールされているバージョンを確認します。
インストールする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) を実行して Azure にサインインします。

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-create.ps1 "Create a database and graph for Gremlin API")]

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
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Cosmos DB アカウントを作成します。 |
| [New-AzCosmosDBGremlinDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbgremlindatabase) | Gremlin API データベースを作成します。 |
| [New-AzCosmosDBGremlinConflictResolutionPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbgremlinconflictresolutionpolicy) | Gremlin API 書き込みの競合の解決ポリシーを作成します。 |
| [New-AzCosmosDBGremlinGraph](/powershell/module/az.cosmosdb/new-azcosmosdbgremlingraph) | Gremlin API グラフを作成します。 |
|**Azure リソース グループ**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/)を参照してください。
