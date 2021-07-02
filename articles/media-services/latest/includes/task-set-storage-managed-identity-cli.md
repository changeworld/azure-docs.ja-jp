---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 8b4fd9356810b6ef44ea3401ac87c12c4f58b3d8
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103302"
---
<!-- ### Give a Media Services Managed identity access to a storage account. -->

次のコマンドでは、ストレージ アカウントへのアクセス権が Media Services マネージド ID に付与されます。

下のコマンドの `your-resource-group-name` をリソース グループ名に変更し、`your-media-services-account-name` を使用する Media Services アカウント名に変更します。

```azurecli-interactive
az ams account storage set-authentication --storage-auth ManagedIdentity --resource-group <your-resource-group_name> --account-name <your-media-services-account-name>

```

JSON の応答例:

```json
{
  "encryption": {
    "keyVaultProperties": null,
    "type": "SystemKey"
  },
  "id": "/subscriptions/00000000-0000-0000-00000000/resourceGroups/your-resource-group-name/providers/Microsoft.Media/mediaservices/your-storage-account-name",
  "identity": null,
  "location": "West US 2",
  "mediaServiceId": "00000000-0000-0000-00000000",
  "name": "your-media-services-account",
  "resourceGroup": "your-resource-group-name",
  "storageAccounts": [
    {
      "id": "/subscriptions/2b461b25-f7b4-4a22-90cc-d640a14b5471/resourceGroups/your-resource-group-name/providers/Microsoft.Storage/storageAccounts/your-storage-account-name",
      "resourceGroup": "your-resource-group-name",
      "type": "Primary"
    }
  ],
  "storageAuthentication": "ManagedIdentity",
  "systemData": {
    "createdAt": "2021-05-17T19:15:00.8850297Z",
    "createdBy": "you@example.com",
    "createdByType": "User",
    "lastModifiedAt": "2021-05-17T21:23:11.3863627Z",
    "lastModifiedBy": "you@example.com",
    "lastModifiedByType": "User"
  },
  "tags": null,
  "type": "Microsoft.Media/mediaservices"
}

```
