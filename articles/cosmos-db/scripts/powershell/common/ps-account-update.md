---
title: Azure Cosmos アカウントを更新する PowerShell スクリプト
description: Azure PowerShell サンプル スクリプト - Azure Cosmos アカウントを更新したりリージョンを変更したりする
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 09/20/2019
ms.author: mjbrown
ms.openlocfilehash: 075c33b0818aa3ec8b16158f538ae302446ff5f7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "75445035"
---
# <a name="update-an-azure-cosmos-account-or-modify-regions-using-powershell"></a>PowerShell を使用して Azure Cosmos アカウントを更新したりリージョンを変更したりする

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>サンプル スクリプト

> [!NOTE]
> リージョンの変更とその他の Cosmos アカウントのプロパティに対する変更を同じ操作で実行することはできません。 これらは 2 つの独立した操作として行う必要があります。
> [!NOTE]
> このサンプルでは、SQL (Core) API アカウントの使用方法を紹介しています。 このサンプルを他の API で使用する場合は、関連するプロパティをコピーし、お使いの API 固有のスクリプトに適用してください。

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-update.ps1 "Add a region to an Azure Cosmos account")]

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
| [Set-AzResource](https://docs.microsoft.com/powershell/module/az.resources/set-azresource) | リソースを更新します。 |
|**Azure リソース グループ**| |
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Cosmos DB のその他の PowerShell サンプル スクリプトについては、[Azure Cosmos DB の PowerShell スクリプト](../../../powershell-samples.md)に関する記事をご覧ください。
