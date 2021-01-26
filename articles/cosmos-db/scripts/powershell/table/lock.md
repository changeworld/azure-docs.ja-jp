---
title: Azure Cosmos Table API テーブルのリソース ロックを作成する PowerShell スクリプト
description: Azure Cosmos Table API テーブルのリソース ロックを作成する
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: a56846e3bfbc9e5392c509ff1f484b8109ce9eac
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098792"
---
# <a name="create-a-resource-lock-for-azure-cosmos-table-api-table-using-azure-powershell"></a>Azure PowerShell を使用して Azure Cosmos Table API テーブルのリソース ロックを作成する
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-PowerShell-install](../../../../../includes/sample-PowerShell-install-no-ssh.md)]

> [!IMPORTANT]
> 先に `disableKeyBasedMetadataWriteAccess` プロパティを有効にして Cosmos DB アカウントをロックしておかないと、ユーザーが Cosmos DB SDK、アカウント キーを介して接続するツール、または Azure portal を使って行った変更に対してリソース ロックが機能しません。 このプロパティを有効にする方法の詳細については、[SDK からの変更の防止](../../../role-based-access-control.md#prevent-sdk-changes)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-PowerShell[main](../../../../../PowerShell_scripts/cosmosdb/table/ps-table-lock.ps1 "Create, list, and remove resource locks")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```PowerShell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
|**Azure リソース**| |
| [New-AzResourceLock](/PowerShell/module/az.resources/new-azresourcelock) | リソース ロックを作成します。 |
| [Get-AzResourceLock](/PowerShell/module/az.resources/get-azresourcelock) | 1 つのリソース ロックを取得するか、リソース ロックを一覧表示します。 |
| [Remove-AzResourceLock](/PowerShell/module/az.resources/remove-azresourcelock) | リソース ロックを削除します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/PowerShell/)をご覧ください。