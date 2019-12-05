---
author: lucygoldbergmicrosoft
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: lugoldbe
ms.openlocfilehash: 9598724db630db7fc48545b58f2b0c1cad464aa4
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667843"
---
## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。 

### <a name="clean-up-resources-using-the-azure-portal"></a>Azure portal を使用してリソースをクリーンアップする

「[リソースのクリーンアップ](../articles/data-explorer/create-cluster-database-portal.md#clean-up-resources)」の手順に従って、Azure portal でリソースを削除します。

### <a name="clean-up-resources-using-powershell"></a>PowerShell を使用してリソースをクリーンアップする

Cloud Shell がまだ開いている場合は、最初の行 (Read-Host) をコピー/実行する必要はありません。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```