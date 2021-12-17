---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: c52dfe33de673f6ad529a3065b9567a685acfad5
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110062554"
---
<!--Create a media services account -->

新しい Media Services アカウントは、以下の Azure CLI コマンドで作成します。 次の値を置き換えることができます。`your-media-services-account-name`、`your-storage-account-name`、および `your-resource-group-name`。 このコマンドは、リソース グループとストレージ アカウントが既に作成済みであることを前提としています。

```azurecli-interactive
az ams account create --name <your-media-services-account-name> -g <your-resource-group-name> --storage-account <your-storage-account-name> -l <your-region>
```

JSON の応答例:

```json
{
  "id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/your-resource-group-name/providers/Microsoft.Media/mediaservices/your-media-services-account-name",
  "location": "your-region",
  "mediaServiceId": "the-media-services-account-id",
  "name": "your-media-services-account-name",
  "resourceGroup": "your-resource-group-name",
  "storageAccounts": [
    {
      "id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/your-resource-group-name/providers/Microsoft.Storage/storageAccounts/your-storage-account-name",
      "resourceGroup": "your-resource-group-name",
      "type": "Primary"
    }
  ],
  "tags": null,
  "type": "Microsoft.Media/mediaservices"
}
```
