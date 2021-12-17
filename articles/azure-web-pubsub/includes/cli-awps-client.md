---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 1c9875f6c3c6b69098c915d4257ed05e3ca0abc4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121788248"
---
### <a name="connect-to-the-service"></a>サービスに接続する

[!INCLUDE [az webpubsub client](cli-awps-client-connect.md)]

試しに、`joingroup <group-name>` を使用してグループに参加し、`sendtogroup <group-name>` を使用してグループにメッセージを送信してみてください。

```azurecli
joingroup group1
```

```azurecli
sendtogroup group1 hello
```

[!INCLUDE [publish messages](cli-awps-service.md)]
