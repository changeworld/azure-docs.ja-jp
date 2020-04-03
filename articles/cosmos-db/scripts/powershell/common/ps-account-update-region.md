---
title: Azure Cosmos アカウントのリージョンを更新する PowerShell スクリプト
description: Azure PowerShell サンプル スクリプト - Azure Cosmos アカウントのリージョンを更新します
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/21/2020
ms.author: mjbrown
ms.openlocfilehash: 4a8e24b4445e2bf29b35e87600de260c523d4376
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80367175"
---
# <a name="update-an-azure-cosmos-accounts-regions-using-powershell"></a>PowerShell を使用して Azure Cosmos アカウントのリージョンを更新する

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>サンプル スクリプト

> [!NOTE]
> リージョンの変更とその他の Cosmos アカウントのプロパティに対する変更を同じ操作で実行することはできません。 これらは 2 つの独立した操作として行う必要があります。
> [!NOTE]
> このサンプルでは、SQL (Core) API アカウントの使用方法を紹介しています。 このサンプルを他の API で使用する場合は、関連するプロパティをコピーし、お使いの API 固有のスクリプトに適用してください。

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-update-region.ps1 "Update Azure Cosmos account regions")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | Notes |
|---|---|
|**Azure リソース**| |
| [Set-AzResource](https://docs.microsoft.com/powershell/module/az.resources/set-azresource) | リソースを更新します。 |
|**Azure リソース グループ**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Cosmos DB のその他の PowerShell サンプル スクリプトについては、[Azure Cosmos DB の PowerShell スクリプト](../../../powershell-samples.md)に関する記事をご覧ください。
