---
author: baanders
description: Azure Digital Twins のインクルード ファイル - Cloud Shell と IoT 拡張機能の設定
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 4aa016294f0ef3bd26f7f3ef6fa374e9367b672d
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2020
ms.locfileid: "85296969"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Cloud Shell セッションの設定

Cloud Shell ウィンドウを開いた後は、まずログインし、このセッションのサブスクリプションにシェル コンテキストを設定します。 Cloud Shell でこれらのコマンドを実行します。

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

このサブスクリプションを Azure Digital Twins で初めて使用する場合は、次のコマンドを実行して Azure Digital Twins の名前空間に登録します (それが不明な場合は、以前に実行していたとしても、もう一度実行して問題ありません)。

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

次に、[**Azure CLI 用の Microsoft Azure IoT 拡張機能**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest)を Cloud Shell に追加し、Azure Digital Twins およびその他の IoT サービスと対話するためのコマンドを有効にします。 次のコマンドを使用して拡張機能を追加します。

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

拡張機能を以前にインストールしたことがある場合、出力に "拡張機能 'azure-iot' は既にインストールされています" と表示されることがあります。 このようになる場合は、次を実行して、最新の更新プログラムがインストールされていることを確認します。 

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

これで、Cloud Shell で Azure Digital Twins を使用する準備ができました。

このことは、`az dt -h` を実行し、使用できる最上位の Azure Digital Twins コマンドの一覧を表示することで、いつでも確認できます。