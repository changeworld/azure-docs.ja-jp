---
author: baanders
description: Azure Digital Twins のインクルード ファイル - Cloud Shell と IoT 拡張機能の設定
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 6f472865c131b873f1ae0a21fa9ec55865fb2b29
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277844"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Cloud Shell セッションの設定

Cloud Shell ウィンドウを開いた後は、まずログインし、このセッションのサブスクリプションにシェル コンテキストを設定します。 Cloud Shell でこれらのコマンドを実行します。

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```
> [!TIP]
> サブスクリプション名を使用してサブスクリプションを設定することもできます。 次のコマンドを実行します。 
> ```azurecli
> az account set --subscription "your-Azure-subscription-name"
> 
このサブスクリプションを Azure Digital Twins で初めて使用する場合は、次のコマンドを実行して Azure Digital Twins の名前空間に登録します (それが不明な場合は、以前に実行していたとしても、もう一度実行して問題ありません)。

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

次に、[**Azure CLI 用の Microsoft Azure IoT 拡張機能**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest)を Cloud Shell に追加し、Azure Digital Twins およびその他の IoT サービスと対話するためのコマンドを有効にします。 

まず、次のコマンドを実行して、既にインストールされているすべての拡張機能を一覧表示します。

```azurecli-interactive
az extension list
```

出力された一覧のエントリごとに `"name"` フィールドを探し、拡張機能の名前を確認します。

出力結果を見て、拡張機能のセットアップで実行すべきコマンドを次の中から特定します (実行すべきコマンドが複数ある場合もあります)。
* 一覧に `azure-iot` が含まれている場合: 拡張機能は既に存在しています。 次のコマンドを実行して、最新の更新プログラムが適用されていることを確認します。

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* 一覧に `azure-iot` が含まれ**ない**場合: 拡張機能をインストールする必要があります。 次のコマンドを実行します。

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* 一覧に `azure-iot-cli-ext` が含まれている場合: これは従来のバージョンの拡張機能です。 同時にインストールする拡張機能のバージョンは 1 つのみとしてください。従来の拡張機能はアンインストールする必要があります。 次のコマンドを実行します。

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```

これで、Cloud Shell で Azure Digital Twins を使用する準備ができました。

このことは、`az dt -h` を実行し、使用できる最上位の Azure Digital Twins コマンドの一覧を表示することで、いつでも確認できます。