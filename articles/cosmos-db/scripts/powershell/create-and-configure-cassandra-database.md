---
title: Azure PowerShell スクリプト - Azure Cosmos DB の Cassandra API アカウントを作成する
description: Azure PowerShell サンプル スクリプト - Azure Cosmos DB の Cassandra API アカウントを作成する
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: PowerShell
ms.topic: sample
ms.date: 05/29/2019
ms.reviewer: sngun
ms.openlocfilehash: b3abe4e51f75996db88dcebb5e98baf5b5fc08dd
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479301"
---
# <a name="azure-cosmos-db-create-a-cassandra-api-account-using-powershell"></a>Azure Cosmos DB:PowerShell を使用して Cassandra API アカウントを作成する

この PowerShell サンプル スクリプトでは、Azure Cosmos DB Cassandra API アカウントを作成します。 

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/cosmosdb/create-and-configure-cassandra-database/create-and-configure-cassandra-database.ps1?highlight=9,12-15,18,21-23,26-29,32-37 "Create an Azure Cosmos DB account")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | データベースまたはエラスティック プールをホストする論理サーバーを作成します。 |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Cosmos DB のその他の PowerShell サンプル スクリプトについては、[Azure Cosmos DB の PowerShell スクリプト](../../powershell-samples.md)に関する記事をご覧ください。
