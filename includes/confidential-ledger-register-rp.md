---
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: include
ms.date: 05/05/2021
ms.author: msmbaldwin
ms.openlocfilehash: fa07459629d832516fda5d3141f85b23afe3fbb8
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861446"
---
リソース プロバイダーは、Azure リソースを提供するサービスです。 Azure CLI の [az provider register](/cli/azure/provider#az_provider_register) コマンドまたはAzurePowerShell の [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) コマンドレットを使用して、Azure Confidential Ledger リソース プロバイダー 'microsoft.ConfidentialLedger' を登録します。

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
