---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 05/13/2021
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 4611ac467072301c7f32d55e30111c20c717c413
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103269"
---
<!--Show Media Services Managed Identity CLI-->

このコマンドは、Media Services アカウントのすべてのプロパティを表示します。

```azurecli-interactive
az ams account show --name <your-media-services-account-name> --resource-group <your-resource-group>
```

> [!NOTE]
> Media Services アカウントにアクセス ロールを割り当てた場合、この行は `"storageAuthentication": "ManagedIdentity"` を返します。


JSON の応答例:

```json
{
  "encryption": {
    "keyVaultProperties": null,
    "type": "SystemKey"
  },
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/your-resource-group-name/providers/Microsoft.Media/mediaservices/your-media-services-account",
  "identity": {
    "principalId": "00000000-0000-0000-0000-000000000000",
    "tenantId": "00000000-0000-0000-0000-000000000000",
    "type": "SystemAssigned"  //Type will show "Managed Identity" if you have assigned a role to the Media Services account.
  },
  "location": "your-region",
  "mediaServiceId": "00000000-0000-0000-0000-000000000000",
  "name": "your-media-services-account",
  "resourceGroup": "your-resource-group-name",
  "storageAccounts": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/your-resource-group-name/providers/Microsoft.Storage/storageAccounts/your-storage-account-name",
      "resourceGroup": "your-resource-group-name",
      "type": "Primary"
    }
  ],
  "storageAuthentication": "System", //If you have assigned access roles to the account, this line will return storageAuthentication": "ManagedIdentity"
  "systemData": {
    "createdAt": "2021-05-14T21:25:12.3492071Z",
    "createdBy": "you@example.com",
    "createdByType": "User",
    "lastModifiedAt": "2021-05-14T21:25:12.3492071Z",
    "lastModifiedBy": "you@example.com",
    "lastModifiedByType": "User"
  },
  "tags": null,
  "type": "Microsoft.Media/mediaservices"
}
```
