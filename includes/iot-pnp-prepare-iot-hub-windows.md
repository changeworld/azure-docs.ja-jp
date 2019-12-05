---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: 6b6c481a7443953a70fe3db45121f5f9982d8710
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152040"
---
## <a name="prepare-an-iot-hub"></a>IoT ハブを準備する

また、このクイックスタートを完了するには、ご利用の Azure サブスクリプション内に Azure IoT ハブが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 IoT ハブがない場合は、[こちらの手順に従って作成します](../articles/iot-hub/iot-hub-create-using-cli.md)。

> [!IMPORTANT]
> パブリック プレビュー中、IoT プラグ アンド プレイ機能は、**米国中部**、**北ヨーロッパ**、および**東日本**の各リージョンで作成された IoT ハブでのみご利用いただけます。

次のコマンドを実行して、Microsoft Azure IoT Extension for Azure CLI を Cloud Shell インスタンスに追加します。

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

次のコマンドを実行して、ご利用の IoT ハブに新しいデバイス ID を作成します。 **YourIoTHubName** と **YourDeviceID** のプレースホルダーを、任意の _IoT Hub 名_と_デバイス ID_ に置き換えます。

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

次のコマンドを実行して、登録したデバイスの_デバイス接続文字列_を取得します (後で使用するためにメモします)。

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDevice> --output table
```

次のコマンドを実行して、ご使用のハブに対する _IoT ハブ接続文字列_を取得します (後で使用するためにメモします)。

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```