---
author: craigshoemaker
ms.service: app-service
ms.topic: include
ms.date: 10/25/2021
ms.author: cshoe
ms.openlocfilehash: 67821760435fdac37c51400190c1afe44996d014
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089839"
---
### <a name="get-fully-qualified-domain-name"></a>完全修飾ドメイン名の取得

この `az containerapp show` コマンドは、コンテナーアプリの完全修飾ドメイン名を返します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp show \
  --resource-group <RESOURCE_GROUP_NAME> \
  --name <CONTAINER_APP_NAME> \
  --query configuration.ingress.fqdn
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az containerapp show `
  --resource-group <RESOURCE_GROUP_NAME> `
  --name <CONTAINER_APP_NAME> `
  --query configuration.ingress.fqdn
```

---

この例では、で囲まれたプレースホルダーを `<>` 実際の値に置き換えます。

このコマンドから返される値は、次の例のようなドメイン名に似ています。

```console
myapp.happyhill-70162bb9.canadacentral.azurecontainerapps.io
```
