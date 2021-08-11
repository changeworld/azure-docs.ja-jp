---
author: dominicbetts
ms.author: dominicbetts
ms.service: iot-develop
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: c4e4d6858ce03c0a7015f6754018c509c738f8da
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2021
ms.locfileid: "114400088"
---
引き続きその他の IoT プラグ アンド プレイの記事に進む場合は、この記事で使用したリソースを保持し、再使用できます。 それ以外の場合、追加料金が発生するのを防ぐために、この記事で作成したリソースは削除してください。

次の Azure CLI コマンドを使用してリソース グループ全体を削除することで、ハブと登録済みデバイスの両方を一度に削除できます。 ただし、これらのリソースと保持したい他のリソースがリソース グループを共有している場合、このコマンドは使用しないでください。

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
