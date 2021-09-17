---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 156633c04cabd3e97bca4ae6d861b007be8fe84c
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122447072"
---
Azure CLI の [az webpubsub event-handler hub](/cli/azure/webpubsub/event-handler/hub) コマンドを使用して、イベント ハンドラーの設定を更新します。

  > [!Important]
  > &lt;your-unique-resource-name&gt; を、前の手順で作成した Web PubSub リソースの名前に置き換えます。
  > &lt;domain-name&lt; を、ngrok が出力した名前に置き換えます。

```azurecli-interactive
az webpubsub event-handler hub update -n "<your-unique-resource-name>" -g "myResourceGroup" --hub-name myHub1 --template url-template="https://<domain-name>.ngrok.io/eventHandler" user-event-pattern="*" system-event-pattern="connected"
```
