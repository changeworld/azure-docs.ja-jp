---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: 7a0ea2330e682e1e60eca6540151b0f5f620571c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453701"
---
## <a name="prepare-an-iot-hub"></a>IoT ハブを準備する

また、このクイックスタートを完了するには、ご利用の Azure サブスクリプション内に Azure IoT ハブが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 IoT ハブがない場合は、[こちらの手順に従って作成します](../articles/iot-hub/iot-hub-create-using-cli.md)。

> [!IMPORTANT]
> パブリック プレビュー中、IoT プラグ アンド プレイ機能は、**米国中部**、**北ヨーロッパ**、および**東日本**の各リージョンで作成された IoT ハブでのみご利用いただけます。

Azure CLI をローカルで使用している場合は、最初に `az login` を使用して Azure サブスクリプションにサインインします。 Azure Cloud Shell でこれらのコマンドを実行している場合は、自動的にサインインされます。

Azure CLI をローカルで使用している場合、`az` のバージョンは **2.0.73** 以降である必要があります。Azure Cloud Shell では最新バージョンが使用されています。 マシンにインストールされているバージョンを確認するには、`az --version` コマンドを使用します。

次のコマンドを実行して、Microsoft Azure IoT Extension for Azure CLI をお使いのインスタンスに追加します。

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

次のコマンドを実行して、ご利用の IoT ハブに新しいデバイス ID を作成します。 **YourIoTHubName** と **YourDeviceID** のプレースホルダーを、任意の _IoT Hub 名_と_デバイス ID_ に置き換えます。

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

次のコマンドを実行して、登録したデバイスの_デバイス接続文字列_を取得します (後で使用するためにメモします)。

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```