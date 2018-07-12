---
title: Azure Resource Manager の個人データ | Microsoft Docs
description: Azure Resource Manager の操作に関連する個人データを管理する方法について説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2018
ms.author: tomfitz
ms.openlocfilehash: 71928be07080ed14fdcb93f33ea64d2572955b53
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38466026"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Azure Resource Manager に関連する個人データを管理する

機密情報の漏洩を回避するために、展開、リソース グループ、またはタグに含まれている個人情報を削除します。 Azure Resource Manager では、展開、リソース グループ、またはタグに含まれている個人データを管理する操作が提供されています。

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-personal-data-in-deployment-history"></a>展開履歴内の個人データを削除する

展開の場合、Azure Resource Manager は、パラメーター値とステータス メッセージを展開履歴内に保持しています。 これらの値は、履歴から展開を削除するまで保持されます。 これらの値の中に個人データが含まれているかどうかを確認するには、展開を一覧表示します。 個人データが見つかった場合は、履歴から展開を削除します。

履歴内の**展開**を一覧表示するには、以下のコマンドを使用します。

* [List By Resource Group](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/Get-AzureRmResourceGroupDeployment)
* [az group deployment list](/cli/azure/group/deployment#az-group-deployment-list)

履歴から**展開**を削除するには、以下のコマンドを使用します。

* [削除](/rest/api/resources/deployments/delete)
* [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/Remove-AzureRmResourceGroupDeployment)
* [az group deployment delete](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>リソース グループ名内の個人データを削除する

リソース グループ名は、リソース グループを削除するまで保持されます。 リソース グループ名の中に個人データが含まれているかどうかを確認するには、リソース グループを一覧表示します。 個人データを見つけた場合は、[リソースを新しいリソース グループに移動](resource-group-move-resources.md)し、名前の中に個人データが含まれているリソース グループを削除します。

**リソース グループ**を一覧表示するには、以下のコマンドを使用します。

* [リスト](/rest/api/resources/resourcegroups/list)
* [Get-AzureRmResourceGroup](/powershell/module/azurerm.resources/Get-AzureRmResourceGroup)
* [az group list](/cli/azure/group#az-group-list)

**リソース グループ**を削除するには、以下のコマンドを使用します。

* [削除](/rest/api/resources/resourcegroups/delete)
* [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/Remove-AzureRmResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>タグ内の個人データを削除する

タグの名前と値は、タグを削除するか、変更するまで保持されます。 タグの中に個人データが含まれているかどうかを確認するには、タグを一覧表示します。 個人データが見つかった場合は、タグを削除します。

**タグ**を一覧表示するには、以下のコマンドを使用します。

* [リスト](/rest/api/resources/tags/list)
* [Get-AzureRmTag](/powershell/module/azurerm.tags/get-azurermtag)
* [az tag list](/cli/azure/tag#az-tag-list)

**タグ**を削除するには、以下のコマンドを使用します。

* [削除](/rest/api/resources/tags/delete)
* [Remove-AzureRmTag](/powershell/module/azurerm.tags/remove-azurermtag)
* [az tag delete](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>次の手順
* Azure Resource Manager の概要については、「[Azure Resource Manager の概要](resource-group-overview.md)」を参照してください。