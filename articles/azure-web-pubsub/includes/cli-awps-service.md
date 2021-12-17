---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: a34e4d77d77ad34a8a0487a9efcdeb48ed510dda
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131663862"
---
### <a name="publish-messages-and-manage-the-clients"></a>メッセージを発行してクライアントを管理する

Azure CLI には、クライアント接続を管理するための [az webpubsub service](/cli/azure/webpubsub/service) コマンドも用意されています。

**別** の CLI コマンドを開き、すべてのクライアントにメッセージをブロードキャストできます。

- ハブ名: **myHub1**
- リソース グループ名: **myResourceGroup**

```azurecli-interactive
az webpubsub service broadcast --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --hub-name "myHub1" --payload "Hello World"
```

前の CLI コマンドに戻り、クライアントがメッセージを受信したことを確認できます。
```JSON
{"type":"message","from":"server","dataType":"text","data":"Hello World"}
```

`--help` オプションを使用して使用可能なすべてのコマンドを一覧表示し、表示されたコマンドを試してみることもできます。

```azurecli-interactive
az webpubsub service --help
```
