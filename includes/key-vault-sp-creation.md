---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: c6a9f17d46ef8feb571c0ecc7a0a93a169f74725
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285554"
---
クラウドベースの Python アプリケーションを認証するための最も簡単な方法は、マネージド ID を使用することです。詳細については、[App Service マネージド ID を使用した Azure Key Vault へのアクセス](/azure/key-vault/general/managed-identity)に関するページを参照してください。 

ただし簡潔にするために、このクイックスタートではデスクトップ アプリケーションを作成します。デスクトップ アプリケーションでは、サービス プリンシパルとアクセス制御ポリシーを使用する必要があります。 サービス プリンシパルは、"http://&lt;my-unique-service-principal-name&gt;" の形式で一意の名前にする必要があります。

Azure CLI の [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) コマンドを使用してサービス プリンシパルを作成します。

```azurecli
az ad sp create-for-rbac -n "http://<my-unique-service-principal-name>" --sdk-auth
```

この操作では、一連のキーと値のペアが返されます。

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```
