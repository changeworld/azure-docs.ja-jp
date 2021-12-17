---
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: include
ms.date: 05/05/2021
ms.author: msmbaldwin
ms.openlocfilehash: bebab5bbab271b83b6b7e039bdf68d6254bd5fe2
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110387672"
---
リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 Azure CLI の [az group create](/cli/azure/group#az_group_create) コマンド、または Azure PowerShell の [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドレットを使用して、*myResourceGroup* という名前のリソース グループを *eastus* の場所に作成します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location eastus
```
---
