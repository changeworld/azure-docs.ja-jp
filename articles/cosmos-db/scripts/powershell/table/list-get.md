---
title: Azure Cosmos DB Table API の操作を一覧表示および取得する PowerShell スクリプト
description: Azure PowerShell スクリプト - Table API 向けの Azure Cosmos DB の list 操作と get 操作
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 07/31/2020
ms.author: mjbrown
ms.openlocfilehash: ab4313f02d233b3eac33b7da202fca559bffa147
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92281929"
---
# <a name="list-and-get-tables-for-azure-cosmos-db---table-api"></a>Azure Cosmos DB のテーブルのリストと取得 - Table API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-list-get.ps1 "List or get tables for Table API")]

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
| [Get-AzCosmosDBTable](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbtable) | アカウントに存在する Table API テーブルを一覧表示します。または、指定された Table API テーブルをアカウントから取得します。 |
|**Azure リソース グループ**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。
