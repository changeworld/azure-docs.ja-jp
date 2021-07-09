---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 05/14/2021
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 93eea067cf26a5962d16ae6b174263484520d1a8
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103261"
---
<!-- ### Set the the Key Vault policy -->

このコマンドを使用するには、Key Vault とキーが既に作成されている必要があります。

```azurecli-interactive
az ams account encryption set --account-name <your-media-services-account-name> --resource-group <your-resource-group> --key-type CustomerKey --key-identifier https://<your-keyvault-name>.vault.azure.net/keys/<your-key-name>
```

JSON の応答例:

```json
{
  "id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/your-resource-group-name/providers/Microsoft.KeyVault/vaults/your-keyvault-name",
  "location": "your-region",
  "name": "your-keyvault-name",
  "properties": {
    "accessPolicies": [
      {
        "applicationId": null,
        "objectId": "00000000-0000-0000-000000000000",
        "permissions": {
          "certificates": [
            "get",
            "list",
            "delete",
            "create",
            "import",
            "update",
            "managecontacts",
            "getissuers",
            "listissuers",
            "setissuers",
            "deleteissuers",
            "manageissuers",
            "recover"
          ],
          "keys": [
            "get",
            "create",
            "delete",
            "list",
            "update",
            "import",
            "backup",
            "restore",
            "recover"
          ],
          "secrets": [
            "get",
            "list",
            "set",
            "delete",
            "backup",
            "restore",
            "recover"
          ],
          "storage": [
            "get",
            "list",
            "delete",
            "set",
            "update",
            "regeneratekey",
            "setsas",
            "listsas",
            "getsas",
            "deletesas"
          ]
        },
        "tenantId": "the-tenant-id"
      },
      {
        "applicationId": null,
        "objectId": "the-media-services-account-id",
        "permissions": {
          "certificates": null,
          "keys": [
            "encrypt",
            "get",
            "list",
            "wrapKey",
            "decrypt",
            "unwrapKey"
          ],
          "secrets": null,
          "storage": null
        },
        "tenantId": "the-tenant-id"
      }
    ],
    "createMode": null,
    "enablePurgeProtection": true,
    "enableRbacAuthorization": null,
    "enableSoftDelete": true,
    "enabledForDeployment": false,
    "enabledForDiskEncryption": null,
    "enabledForTemplateDeployment": null,
    "networkAcls": null,
    "privateEndpointConnections": null,
    "provisioningState": "Succeeded",
    "sku": {
      "name": "standard"
    },
    "softDeleteRetentionInDays": 90,
    "tenantId": "the-tenant-id",
    "vaultUri": "https://your-keyvault-name.vault.azure.net/"
  },
  "resourceGroup": "your-resource-group-name",
  "tags": {},
  "type": "Microsoft.KeyVault/vaults"
}
```
