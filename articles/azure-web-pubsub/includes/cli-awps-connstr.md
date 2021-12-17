---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 4bfab858862bfb08cfaa2e9dc685b8ade7c0325e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785574"
---
> [!Important]
> 接続文字列には、アプリケーションが Azure Web PubSub サービスにアクセスするために必要な認可情報が含まれています。 接続文字列内のアクセス キーは、サービスのルート パスワードに似ています。 アクセス キーは常に慎重に保護してください。 キーを安全に管理およびローテーションするには、Azure Key Vault を使用します。 アクセス キーを他のユーザーに配布したり、ハードコーディングしたり、他のユーザーがアクセスできるプレーンテキストで保存したりしないでください。 キーが侵害された可能性があると思われる場合は、キーをローテーションしてください。

Azure CLI の [az webpubsub key](/cli/azure/webpubsub#az_webpubsub_key) コマンドを使用して、サービスの **ConnectionString** を取得します。

```azurecli-interactive
az webpubsub key show --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --query primaryConnectionString
```
