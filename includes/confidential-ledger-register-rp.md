---
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: include
ms.date: 05/05/2021
ms.author: msmbaldwin
ms.openlocfilehash: 5a94cdae7cc332b8e0df5b2ebdc6ae57b67c8a68
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110387648"
---
リソース プロバイダーは、Azure リソースを提供するサービスです。 Azure CLI の [az provider register](/cli/azure/provider#az_provider_register) コマンドまたはAzurePowerShell の [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) コマンドレットを使用して、Confidential Ledger リソース プロバイダー 'microsoft.ConfidentialLedger' を登録します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az provider register --namespace "microsoft.ConfidentialLedger"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Register-AzureRmResourceProvider -ProviderNamespace "microsoft.ConfidentialLedger"
```
---

登録が完了したことを確認するには、Azure CLI の [az provider register](/cli/azure/provider#az_provider_show) コマンドまたは Azure PowerShell の [Get-AzureRmResourceProvider](/powershell/module/azurerm.resources/get-azurermresourceprovider) コマンドレットを使用します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az provider show --namespace "microsoft.ConfidentialLedger"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzureRmResourceProvider -ProviderNamespace "microsoft.ConfidentialLedger"
```
---
