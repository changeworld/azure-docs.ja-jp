---
title: Azure PowerShell スクリプト - Azure Cosmos アカウントのデータベースを削除する
description: Azure PowerShell サンプル スクリプト - Azure Cosmos アカウントのデータベースを削除する
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 05/08/2019
ms.author: mjbrown
ms.openlocfilehash: a8ecdb7456b13b21f814a3c2eda924aa9b3f48fa
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080479"
---
# <a name="delete-a-database-in-an-azure-cosmos-account-in-an-azure-cosmos-account-using-powershell"></a>PowerShell を使用して Azure Cosmos アカウントのデータベースを削除する

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-database-delete.ps1 "Delete a database in an Azure Cosmos account")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
|**Azure リソース**| |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | リソースを作成します。 |
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | リソースを取得します。 |
| [Set-AzResource](https://docs.microsoft.com/powershell/module/az.resources/set-azresource) | リソースを更新します。 |
| [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction) | リソースに対するアクションを呼び出します。 |
| [Remove-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource) | リソースを削除します。 |
|**Azure リソース グループ**| |
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Cosmos DB のその他の PowerShell サンプル スクリプトについては、[Azure Cosmos DB の PowerShell スクリプト](../../../powershell-samples.md)に関する記事をご覧ください。