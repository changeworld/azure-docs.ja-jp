---
title: インクルード ファイル
description: インクルード ファイル
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: e80033d696de1b83da43fc27e5be9eca3b3f8757
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44168963"
---
## <a name="create-a-device-identity"></a>デバイス ID の作成

このセクションでは、Azure CLI を使用して、このチュートリアル用のデバイス ID を作成します。 Azure CLI は、[Azure Cloud Shell](https://docs.microsoft.com/zure/cloud-shell/overview) にプレインストールされています。また、[ローカルにインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)することもできます。 デバイス ID には大文字と小文字の区別があります。

1. Azure CLI を使用して IoT 拡張機能をインストールするには、コマンド ライン環境で、次のコマンドを実行します。

    ```cmd/sh
    az extension add --name azure-cli-iot-ext
    ```

1. CAzure CLI をローカルで実行している場合、次のコマンドを使用して Azure アカウントにサインインします (Cloud Shell を使用している場合、自動でサインインされるため、このコマンドを実行する必要はありません)。

    ```cmd/sh
    az login
    ```

1. 最後に、次のコマンドを実行して、`myDeviceId` という新しいデバイス ID を作成し、デバイス接続文字列を取得します。

    ```cmd/sh
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

結果として得られたデバイスの接続文字列をメモしておきます。 このデバイス接続文字列は、デバイス アプリからデバイスとして IoT Hub に接続する際に使用します。

<!-- images and links -->
