---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 2b12b25b973d9e56de0241c1e088e2409137b086
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121788253"
---
### <a name="publish-messages-and-manage-the-clients"></a>メッセージを発行してクライアントを管理する

Azure CLI には、クライアント接続を管理するための [az webpubsub service](/cli/azure/webpubsub/service) コマンドも用意されています。

**別** の CLI コマンドを開き、すべてのクライアントにメッセージをブロードキャストできます。

- ハブ名: **myHub1**
- リソース グループ名: **myResourceGroup**

```azurecli-interactive
az webpubsub service broadcast --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --hub-name myHub1 --payload "Hello World"
```

前の CLI コマンドに戻り、クライアントがメッセージを受信したことを確認できます。
```JSON
{"type":"message","from":"server","dataType":"text","data":"Hello World"}
```

`--help` オプションを使用して使用可能なすべてのコマンドを一覧表示し、表示されたコマンドを試してみることもできます。

```azurecli-interactive
az webpubsub service --help
```
