---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 96336dc58f4e6059827343beeed32bd9923c377e
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842698"
---
### <a name="device-registration"></a>デバイス登録

デバイスを登録する手順には、**Azure portal**、**Visual Studio Code**、または **Azure CLI** を使用できます。 ユーティリティごとに個別の前提条件があります。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure サブスクリプション内の無料または標準の [IoT ハブ](../articles/iot-hub/iot-hub-create-through-portal.md)。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Azure サブスクリプション内の無料または標準の [IoT ハブ](../articles/iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* Visual Studio Code 用 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Azure サブスクリプション内の無料または標準の [IoT ハブ](../articles/iot-hub/iot-hub-create-using-cli.md)
* ご使用の環境内の [Azure CLI](/cli/azure/install-azure-cli)

  Azure CLI のバージョンは、少なくとも 2.0.70 以降である必要があります。 検証するには、`az --version` を使用します。 このバージョンでは、az 拡張機能のコマンドがサポートされ、Knack コマンド フレームワークが導入されています。

---
