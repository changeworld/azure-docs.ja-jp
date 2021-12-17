---
title: クイックスタート - デバイスを接続し、テレメトリを Azure IoT Central に送信する
description: このクイックスタートでは、デバイス開発者がデバイスを Azure IoT Central に安全に接続する方法について説明します。 C、C#、Python、Node.js、または Java 用の Azure IoT device SDK を使用して、デバイスでクライアント アプリを実行し、IoT Central に接続してテレメトリを送信します。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: quickstart
ms.date: 04/27/2021
ms.collection: embedded-developer, application-developer
zone_pivot_groups: iot-develop-set1
ms.openlocfilehash: f6955c10aab9b21be43cc3b346e564cb9114979d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131032742"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central"></a>クイックスタート: デバイスから Azure IoT Central にテレメトリを送信する

**適用対象**: [デバイス アプリケーション開発者](about-iot-develop.md#device-application-development)

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-develop-send-telemetry-central-c](../../includes/iot-develop-send-telemetry-central-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-develop-send-telemetry-central-csharp](../../includes/iot-develop-send-telemetry-central-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-develop-send-telemetry-central-java](../../includes/iot-develop-send-telemetry-central-java.md)]

:::zone-end

:::zone pivot="programming-language-nodejs"

[!INCLUDE [iot-develop-send-telemetry-central-nodejs](../../includes/iot-develop-send-telemetry-central-nodejs.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-develop-send-telemetry-central-python](../../includes/iot-develop-send-telemetry-central-python.md)]

:::zone-end

## <a name="view-telemetry"></a>利用統計情報データを表示する
デバイスは、IoT Central に接続した後、テレメトリの送信を開始します。 IoT Central で接続されているデバイスに関するテレメトリとその他の詳細を表示できます。 

IoT Central で、 **[デバイス]** を選択し、デバイス名をクリックしてから、 **[概要]** タブを選択します。2 つのサーモスタット デバイスからの温度のグラフがこのビューに表示されます。

:::image type="content" source="media/quickstart-send-telemetry-central/iot-central-telemetry-output-overview.png" alt-text="IoT Central デバイスのテレメトリの概要":::

**[生データ]** タブを選択します。サーモスタットの読み取り値が送信されるたびにテレメトリがこのビューに表示されます。

:::image type="content" source="media/quickstart-send-telemetry-central/iot-central-telemetry-output-raw.png" alt-text="IoT Central デバイス テレメトリの生の出力":::

デバイスが安全に接続され、Azure IoT にテレメトリを送信するようになりました。
    
## <a name="clean-up-resources"></a>リソースをクリーンアップする
このクイックスタートで作成した IoT Central リソースが不要になった場合は、それらを削除してください。 引き続きこのガイドのドキュメントに従う場合は、必要に応じて、作成したアプリケーションを保持して他のサンプルに再利用することもできます。

Azure IoT Central のサンプル アプリケーションとそのデバイスおよびリソースをすべて削除するには:
1. **[管理]**  >  **<自分のアプリケーション>** を選択します。
1. **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、クラウドに対してデバイスを安全に接続し、device-to-cloud テレメトリを送信するための Azure IoT アプリケーションの基本的なワークフローについて説明しました。 Azure IoT Central を使用して、アプリケーションとデバイス インスタンスを作成しました。 次に、Azure IoT device SDK を使用して、温度コントローラーを作成し、IoT Central に接続して、テレメトリを送信しました。 また、IoT Central を使用してテレメトリを監視しました。

次の手順では、次の記事を参照して、Azure IoT を使用してデバイス ソリューションを構築する方法について確認します。 

> [!div class="nextstepaction"]
> [テレメトリを Azure IoT Hub に送信する](./quickstart-send-telemetry-iot-hub.md)
> [!div class="nextstepaction"]
> [IoT Central アプリケーションを作成する](../iot-central/core/quick-deploy-iot-central.md)