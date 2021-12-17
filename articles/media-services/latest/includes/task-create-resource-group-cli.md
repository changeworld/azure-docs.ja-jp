---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: f1b77e4cee809d7ddb7bbbf7de6c5ac483b150e3
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110060120"
---
<!-- Create a resource group -->

次のコマンドを使用して、リソース グループを作成します。 `your-resource-group-name` を、リソース グループに付けたい名前に変更します。

`your-region` を、Media Services アカウントのメディアとメタデータのレコードを保存する地理的リージョンに変更します。 このリージョンでメディアの処理とストリーミングが行われます。

```azurecli-interactive
az group create --name <your-resource-group-name> --location <your-region>
```

JSON の応答例:

```json
{
  "id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/your-resource-group-name",
  "location": "your-region",
  "managedBy": null,
  "name": "your-resource-group-name",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": "Microsoft.Resources/resourceGroups"
}
```
