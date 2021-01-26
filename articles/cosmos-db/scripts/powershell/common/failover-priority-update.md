---
title: 単一の書き込みリージョンで構成された Azure Cosmos アカウントのフェールオーバー優先度を変更する PowerShell スクリプト
description: Azure PowerShell スクリプト サンプル - 単一の書き込みリージョンで構成された Azure Cosmos アカウントのフェールオーバー優先度を変更する、またはフェールオーバーをトリガーする
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: caea364ae1ef1475bc5963837a7bb83c0bdc8271
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099567"
---
# <a name="change-failover-priority-or-trigger-failover-for-an-azure-cosmos-account-with-single-write-region-by-using-powershell"></a>PowerShell を使用して、単一の書き込みリージョンで構成された Azure Cosmos アカウントのフェールオーバー優先度を変更する、またはフェールオーバーをトリガーする
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>サンプル スクリプト

> [!NOTE]
> 手動フェールオーバーは、`failoverPriority=0` のリージョンを変更した場合にトリガーされ、また、手動フェールオーバー用に構成されたアカウントに対してのみ実行できます。 それ以外は、どのリージョンを変更しても、Cosmos アカウントのフェールオーバー優先度が変更されるだけです。
> [!NOTE]
> このサンプルでは、SQL (Core) API アカウントの使用方法を紹介しています。 このサンプルを他の API で使用する場合は、関連するプロパティをコピーし、お使いの API 固有のスクリプトに適用してください。

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-failover-priority-update.ps1 "Update failover priority for an Azure Cosmos account or trigger a manual failover")]

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
| [Get-AzCosmosDBAccount](/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Cosmos DB アカウントを一覧表示します。または、指定された Cosmos DB アカウントを取得します。 |
| [Update-AzCosmosDBAccountFailoverPriority](/powershell/module/az.cosmosdb/update-azcosmosdbaccountfailoverpriority) | Cosmos DB アカウントのリージョンに対するフェールオーバーの優先順位を更新します。 |
|**Azure リソース グループ**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/)を参照してください。