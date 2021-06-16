---
author: baanders
description: Azure Digital Twins のインクルード ファイル - Cloud Shell と IoT 拡張機能の設定
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 5f11c78e6619c2581d04ebc8e97c683c43cf2e73
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "110793005"
---
開いている [Azure Cloud Shell](https://shell.azure.com) ウィンドウで Azure Digital Twins の使用を開始するには、最初にログインし、このセッションのサブスクリプションにシェル コンテキストを設定します。 Cloud Shell でこれらのコマンドを実行します。

```azurecli-interactive
az login
az account set --subscription "<your-Azure-subscription-ID>"
```

> [!TIP]
> 上記のコマンドでは、ID の代わりにサブスクリプション名も使用できます。 

このサブスクリプションを Azure Digital Twins で初めて使用する場合は、次のコマンドを実行して Azure Digital Twins の名前空間に登録します (それが不明な場合は、以前に実行していたとしても、もう一度実行して問題ありません)。

```azurecli-interactive
az provider register --namespace 'Microsoft.DigitalTwins'
```

次に、[Azure CLI 用の Microsoft Azure IoT 拡張機能](/cli/azure/service-page/azure%20iot?view=azure-cli-latest&preserve-view=true)を Cloud Shell に追加し、Azure Digital Twins およびその他の IoT サービスと対話するためのコマンドを有効にします。 このコマンドを実行して、最新バージョンの拡張機能がインストールされていることを確認します。

```azurecli-interactive
az extension add --upgrade --name azure-iot
```

これで、Cloud Shell で Azure Digital Twins を使用する準備ができました。

このことは、`az dt --help` を実行し、使用できる最上位の Azure Digital Twins コマンドの一覧を表示することで、いつでも確認できます。