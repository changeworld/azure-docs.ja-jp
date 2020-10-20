---
title: Azure Cosmos DB Table API に対するスループット (RU/秒) 操作の PowerShell スクリプト
description: Azure Cosmos DB Table API に対するスループット (RU/秒) 操作の PowerShell スクリプト
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 62e9e00b94da99e169d0002086e87b9942ca0ca4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843520"
---
# <a name="throughput-rus-operations-with-powershell-for-a-table-for-azure-cosmos-db---table-api"></a>Azure Cosmos DB - Table API のテーブルに対する PowerShell でのスループット (RU/秒) 操作

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="get-throughput"></a>スループットの取得

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-ru-get.ps1 "Get throughput on a table for Table API")]

## <a name="update-throughput"></a>スループットを更新する

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-ru-update.ps1 "Update throughput on a table for Table API")]

## <a name="migrate-throughput"></a>スループットを移行する

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-ru-migrate.ps1 "Migrate between standard and autoscale throughput on a table for Table API")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | Notes |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBTableThroughput](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbtablethroughput) | 指定された Table API テーブルのスループット値を取得します。 |
| [Update-AzCosmosDBMongoDBCollectionThroughput](https://docs.microsoft.com/powershell/module/az.cosmosdb/update-azcosmosdbsqldatabasethroughput) | Table API テーブルのスループット値を更新します。 |
| [Invoke-AzCosmosDBTableThroughputMigration](https://docs.microsoft.com/powershell/module/az.cosmosdb/invoke-azcosmosdbtablethroughputmigration) | Table API テーブルのスループットを移行します。 |
|**Azure リソース グループ**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Cosmos DB のその他の PowerShell サンプル スクリプトについては、[Azure Cosmos DB の PowerShell スクリプト](../../../powershell-samples.md)に関する記事をご覧ください。
