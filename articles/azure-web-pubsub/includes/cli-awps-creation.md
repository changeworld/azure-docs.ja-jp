---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 785114c797fba5c84e97f5a0d0cc1a34dc14d540
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785581"
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

> [!Note]
> Web PubSub サービスはまだプレビュー段階であるため、CLI コンソールに次のような警告が表示される場合があります。 "The command requires the extension webpubsub. (このコマンドには、拡張機能 webpubsub が必要です。) Do you want to install it now? (今すぐインストールしますか?) The command will continue to run after the extension is installed. (この拡張機能をインストールした後も、コマンドは継続して実行されます。) (Y/n)" Y を押して、この拡張機能をインストールしてください。

このコマンドの出力では、新しく作成したリソースのプロパティが表示されます。 次の 2 つのプロパティをメモしておきます。

- **Resource Name**: 上記の `--name` パラメーターに指定した名前です。
- **hostName**: この例では、ホスト名は `<your-unique-resource-name>.webpubsub.azure.com/` です。

この時点で、お使いの Azure アカウントのみが、この新しいリソースで任意の操作を実行することを許可されています。
