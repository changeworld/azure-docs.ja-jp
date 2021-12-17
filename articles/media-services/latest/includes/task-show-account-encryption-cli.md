---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 05/13/2021
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 443803807aae3b65c349b61bde7410325472ccf1
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103277"
---
<!--Show Media Services Managed Identity CLI-->

```azurecli-interactive
az ams account encryption show --account-name <your-media-services-account-name> --resource-group <your-resource-group-name>
```

JSON の応答例:

```json

{
  "keyVaultProperties": {
    "currentKeyIdentifier": "https://your-key-vault-name.vault.azure.net/keys/your-key-name/523c33b607b644fc88e1640ac408caf9",
    "keyIdentifier": "https://your-key-vault-name.vault.azure.net/keys/your-key-name"
  },
  "type": "CustomerKey"
}
```
