---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 633121c21bf09c608c0c40d6b87bf68edfbaf1f8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478008"
---
## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。 

### <a name="clean-up-resources-using-the-azure-portal"></a>Azure portal を使用してリソースをクリーンアップする

「[リソースのクリーンアップ](../ingest-data/data-explorer-ingest-event-hub-portal.md#clean-up-resources)」の手順に従って、Azure portal でリソースを削除します。

### <a name="clean-up-resources-using-powershell"></a>PowerShell を使用してリソースをクリーンアップする

Cloud Shell がまだ開いている場合は、最初の行 (Read-Host) をコピー/実行する必要はありません。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```
