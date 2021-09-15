---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 1f6356ceaa844433e6da3102b8db0fcbd5a61615
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785566"
---
次の情報を指定した Azure CLI の [az webpubsub client](/cli/azure/webpubsub/client) コマンドを使用して、前の手順で作成したサービスへの WebSocket クライアント接続を開始します。

- ハブ名: 1 から 127 文字の文字列。 これは、英文字 `(a-z, A-Z)` から始まる必要があり、使用できる文字は英数字 `(0-9, a-z, A-Z)` とアンダースコア `(_)` のみです。

**ハブ** は、接続された WebSocket 接続の論理セットです。 この概念の詳細については、[ハブ、グループ、接続の概要](../key-concepts.md)に関する記事を参照してください。

  > [!Important]
  > &lt;your-unique-resource-name&gt; を、前の手順で作成した Web PubSub リソースの名前に置き換えてください。

- ハブ名: **myHub1**
- リソース グループ名: **myResourceGroup**

```azurecli-interactive
az webpubsub client start --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --hub-name myHub1
```

このコマンドによって Web PubSub サービスへの WebSocket 接続が確立されたこと、および現在正常に接続され、一意の `connectionId` が割り当てられていることを示す JSON メッセージが表示されていることが分かります。

```json
{"type":"system","event":"connected","userId":null,"connectionId":"<your_unique_connection_id>"}
```
