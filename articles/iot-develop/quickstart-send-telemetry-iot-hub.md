---
title: Azure IoT Hub にデバイス テレメトリを送信するクイックスタート
description: このクイックスタートでは、デバイス開発者がデバイスを Azure IoT Hub に安全に接続する方法について説明します。 C、C#、Python、Node.js、または Java 用の Azure IoT device SDK を使用し、Windows、Linux、Raspberry Pi OS (Raspian) 用のシミュレートされたデバイス クライアントを構築します。 最後に、デバイスを Azure IoT Hub に接続し、テレメトリを送信します。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: quickstart
ms.date: 08/03/2021
ms.collection: embedded-developer, application-developer
zone_pivot_groups: iot-develop-set1
ms.openlocfilehash: f7b1543b91d604b6640bf97877bf2c134deee866
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094073"
---
# <a name="quickstart-send-telemetry-from-an-iot-plug-and-play-device-to-azure-iot-hub"></a>クイックスタート: IoT プラグ アンド プレイ デバイスから Azure IoT Hub にテレメトリを送信する

**適用対象**: [デバイス アプリケーション開発者](about-iot-develop.md#device-application-development)

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-c](../../includes/iot-develop-send-telemetry-iot-hub-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-csharp](../../includes/iot-develop-send-telemetry-iot-hub-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-java](../../includes/iot-develop-send-telemetry-iot-hub-java.md)]

:::zone-end

:::zone pivot="programming-language-nodejs"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-node](../../includes/iot-develop-send-telemetry-iot-hub-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-python](../../includes/iot-develop-send-telemetry-iot-hub-python.md)]

:::zone-end
    
## <a name="clean-up-resources"></a>リソースをクリーンアップする
このクイックスタートで作成した Azure リソースが不要になった場合は、Azure CLI を使用して削除できます。

> [!IMPORTANT]
> リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。

名前でリソース グループを削除するには、以下の手順を実行します。
1. [az group delete](/cli/azure/group#az_group_delete) コマンドを実行します。 このコマンドにより、作成したリソース グループ、IoT Hub、デバイスの登録が削除されます。

    ```azurecli-interactive
    az group delete --name MyResourceGroup
    ```
1. [az group list](/cli/azure/group#az_group_list) コマンドを実行して、リソース グループが削除されていることを確認します。  

    ```azurecli-interactive
    az group list
    ```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、クラウドに対してデバイスを安全に接続し、device-to-cloud テレメトリを送信するための Azure IoT アプリケーションの基本的なワークフローについて説明しました。 Azure CLI を使用して、Azure IoT ハブとデバイス インスタンスを作成しました。 次に、Azure IoT device SDK を使用して、シミュレートされたデバイスを作成し、ハブに接続して、テレメトリを送信しました。 さらに、Azure portal を使用して、テレメトリを監視しました。

次の手順では、次の記事を参照して、Azure IoT を使用してデバイス ソリューションを構築する方法について確認します。 

> [!div class="nextstepaction"]
> [IoT ハブに接続されたデバイスを制御する](../iot-hub/quickstart-control-device.md)
> [!div class="nextstepaction"]
> [テレメトリを IoT Central に送信する](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [MXCHIP AZ3166 Devkit を IoT Central に接続する](quickstart-devkit-mxchip-az3166.md)
