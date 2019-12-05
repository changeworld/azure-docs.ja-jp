---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: e6ca830a7224326c17003924b76192b95ba6bcb2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152029"
---
## <a name="clean-up-resources"></a>リソースのクリーンアップ

引き続きその他の IoT プラグ アンド プレイの記事に進む場合は、このクイックスタートで使用したリソースを保持し、再使用できます。 それ以外の場合、追加料金が発生するのを防ぐために、このクイックスタートで作成したリソースは削除できます。

Azure CLI で次のコマンドを使用してリソース グループ全体を削除することで、ハブと登録済みデバイスの両方を一度に削除できます。 (ただし、これらのリソースが、別の目的で使用している他のリソースとリソース グループを共有している場合、この方法は使用しないでください。)

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```
IoT ハブのみを削除するには、Azure CLI を使用して次のコマンドを実行します。

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

IoT ハブに登録したデバイス ID のみを削除するには、Azure CLI を使用して次のコマンドを実行します。

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

また、クローンした SDK ファイルを開発用コンピューターから削除することもできます。