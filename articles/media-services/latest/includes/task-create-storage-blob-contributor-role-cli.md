---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 4dae6dc7702781231965f7dc66423fde941f4185
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103317"
---
<!-- ### Create a Storage Blob Contributor role -->

次のコマンドでは、Storage Blob の共同作成者ロールが作成されます。 

`assignee` を `principalId` に変更します。 このコマンドは、リソース グループとストレージ アカウントが既に作成済みであることを前提としています。  次のコマンドに示すように、`scope` 値の一部として `your-resource-group-name` および `your-storage-account-name` を使用します。

```azurecli-interactive
az role assignment create --assignee 00000000-0000-0000-000000000000 --role "Storage Blob Data Contributor" --scope "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/<your-resource-group-name>/providers/Microsoft.Storage/storageAccounts/<your-storage-account-name>"
```

JSON の応答例:

```json
{
  "canDelegate": null,
  "condition": null,
  "conditionVersion": null,
  "description": null,
  "id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/your-resource-group-name/providers/Microsoft.Storage/storageAccounts/your-storage-account-name/providers/Microsoft.Authorization/roleAssignments/00000000-0000-0000-000000000000",
  "name": "00000000-0000-0000-000000000000",
  "principalId": "00000000-0000-0000-000000000000",
  "principalType": "ServicePrincipal",
  "resourceGroup": "your-resource-group-name",
  "roleDefinitionId": "/subscriptions/00000000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-000000000000",
  "scope": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/your-resource-group-name/providers/Microsoft.Storage/storageAccounts/your-storage-account-name",
  "type": "Microsoft.Authorization/roleAssignments"
}

```
