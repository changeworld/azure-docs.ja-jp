---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: d88d166e001e71cabdabc3a3d344adc4da19aa8c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453690"
---
## <a name="clean-up-resources"></a>リソースをクリーンアップする

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

また、クローンしたサンプル ファイルをお使いの開発用マシンから削除することもできます。