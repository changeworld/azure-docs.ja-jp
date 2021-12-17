---
title: Microsoft Azure Confidential Ledger に Ledger サービス プリンシパルを登録する
description: Microsoft Azure Confidential Ledger に Ledger サービス プリンシパルを登録する
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 843c2489052ed1df78c738b680740d2a7206e4a5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131454506"
---
# <a name="register-a-confidential-ledger-service-principal"></a>Confidential Ledger サービス プリンシパルを登録する

ストレージ アカウントを Confidential Ledger に関連付けるには、まず、Confidential Ledger サービス プリンシパルを登録する必要があります。

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

サービス プリンシパルを作成するには、Azure CLI [az ad sp create](/cli/azure/ad/sp#az_ad_sp_create) コマンド、または Azure PowerShell [Connect-AzureAD](/powershell/module/azuread/connect-azuread) コマンドレットと [New-AzureADServicePrincipal](/powershell/module/azuread/new-azureadserviceprincipal) コマンドレットを実行します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az ad sp create --id 4353526e-1c33-4fcf-9e82-9683edf52848
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell-interactive
Connect-AzureAD -TenantId "<tenant-id-of-customer>"
New-AzureADServicePrincipal -AppId 4353526e-1c33-4fcf-9e82-9683edf52848 -DisplayName ConfidentialLedger
```
---

## <a name="assign-roles"></a>ロールを割り当てる

ストレージ アカウントの Confidential Ledger サービス プリンシパルに IAM "[ストレージ BLOB データ共同作成者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)" を設定します。 これを行うには、Azure CLI [az role assignment create](/cli/azure/role/assignment) コマンド、または Azure PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) コマンドレットを使用します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az role assignment create --role "Storage Blob Data Contributor" --assignee "4353526e-1c33-4fcf-9e82-9683edf52848" --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell-interactive
New-AzRoleAssignment -ApplicationId 4353526e-1c33-4fcf-9e82-9683edf52848 -RoleDefinitionName "Storage Blob Data Contributor" -Scope "/subscriptions/<subscription-id>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```
---

## <a name="next-steps"></a>次のステップ

- [Microsoft Azure Confidential Ledger の概要](overview.md)
