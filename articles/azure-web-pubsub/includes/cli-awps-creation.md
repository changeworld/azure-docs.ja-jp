---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 3671a5d221afa89c776dca1bc3a17b965b621c5b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423996"
---
次の情報を指定した Azure CLI の [az webpubsub create](/cli/azure/webpubsub#az_webpubsub_create) コマンドを使用して、前の手順のリソース グループに Web PubSub を作成します。

- リソース名: 数字 (0-9)、文字 (a-z、A-Z)、ハイフン (-) のみを含んだ 3 から 24 文字の文字列

  > [!Important]
  > Web PubSub リソースには、それぞれ一意の名前を付ける必要があります。 次の例では、&lt;your-unique-resource-name&gt; をお使いの Web PubSub の名前に置き換えてください。

- リソース グループ名: **myResourceGroup**
- 場所: **EastUS**。
- SKU: **Free_F1**

```azurecli-interactive
az webpubsub create --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --location "EastUS" --sku Free_F1
```

このコマンドの出力では、新しく作成したリソースのプロパティが表示されます。 次の 2 つのプロパティをメモしておきます。

- **Resource Name**: 上記の `--name` パラメーターに指定した名前です。
- **hostName**: この例では、ホスト名は `<your-unique-resource-name>.webpubsub.azure.com/` です。

この時点で、お使いの Azure アカウントのみが、この新しいリソースで任意の操作を実行することを許可されています。
