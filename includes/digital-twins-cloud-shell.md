---
author: baanders
description: Azure Digital Twins のインクルード ファイル - Cloud Shell と IoT 拡張機能の設定
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 76480959d94b9e2a2e04857c85b7359105e73e4f
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2020
ms.locfileid: "84611475"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Cloud Shell セッションの設定

Cloud Shell ウィンドウを開いた後は、まずログインし、このセッションのサブスクリプションにシェル コンテキストを設定します。 

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

このサブスクリプションを Azure Digital Twins で初めて使用する場合は、次のコマンドを実行して Azure Digital Twins の名前空間に登録します (それが不明な場合は、以前に実行していたとしても、もう一度実行して問題ありません)。

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

その後、Cloud Shell インスタンスで次のコマンドを実行して、Microsoft Azure IoT Extension for Azure CLI を追加します。

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

> [!NOTE]
> この記事では、`azure-iot` と呼ばれる、Azure IoT 拡張機能の最新バージョンを使用します。 従来のバージョンは `azure-iot-cli-ext` と呼ばれます。一度にインストールできるバージョンは 1 つだけです。 コマンド `az extension list` を使用すると、現在インストールされている拡張機能を確認できます。
> 拡張機能の従来のバージョンを削除するには、`az extension remove --name azure-cli-iot-ext` を使用します。
> 拡張機能の新しいバージョンを追加するには、`az extension add --name azure-iot` を使用します。 インストール済みの拡張機能を表示するには、`az extension list` を使用してください。

> [!TIP]
> `az dt -h` を実行して、最上位レベルの Azure Digital Twins コマンドを表示できます。