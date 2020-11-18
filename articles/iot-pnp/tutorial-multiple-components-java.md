---
title: チュートリアル - IoT プラグ アンド プレイのサンプル Java コンポーネント デバイス コードを Azure IoT Hub に接続する | Microsoft Docs
description: チュートリアル - 複数のコンポーネントを使用し、IoT ハブに接続する、IoT プラグ アンド プレイのサンプル Java デバイス コードをビルドして実行します。 Azure IoT Explorer ツールを使用して、デバイスからハブに送信された情報を表示します。
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 92732eee94a17aa59257ce2894775b2f881632d4
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421424"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-multiple-component-device-application-to-iot-hub-java"></a>チュートリアル:IoT プラグ アンド プレイの複数コンポーネント デバイス アプリケーションのサンプルを IoT Hub に接続する (Java)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

このチュートリアルでは、複数コンポーネントの IoT プラグ アンド プレイ デバイス アプリケーションのサンプルをビルドし、それをご利用の IoT ハブに接続し、送信されるテレメトリを Azure CLI を使用して表示する方法を示します。 このサンプル アプリケーションは Java で記述されており、Java 用 Azure IoT device SDK に含まれています。 ソリューション ビルダーは Azure CLI を使用して、デバイス コードを表示しなくても IoT プラグ アンド プレイ デバイスの機能を理解することができます。

このチュートリアルでは、複数のコンポーネントを使用する IoT プラグ アンド プレイのサンプル デバイス アプリケーションをビルドし、それを IoT ハブに接続して、ハブに送信される情報を Azure IoT エクスプローラー ツールを使用して表示する方法について説明します。 このサンプル アプリケーションは Java で記述されており、Java 用 Azure IoT device SDK に含まれています。 ソリューション ビルダーは Azure IoT エクスプローラー ツールを使用して、デバイス コードを表示しなくても IoT プラグ アンド プレイ デバイスの機能を理解することができます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Windows でこのチュートリアルを完了するには、ご利用のローカル Windows 環境に次のソフトウェアをインストールします。

* Java SE Development Kit 8。 「[Azure および Azure Stack の Java 長期サポート](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)」の「**長期サポート**」で「**Java 8**」を選択します。
* [Apache Maven 3](https://maven.apache.org/download.cgi)。

## <a name="download-the-code"></a>コードのダウンロード

「[クイックスタート: Windows 上で実行されている IoT プラグ アンド プレイのサンプル デバイス アプリケーションを IoT Hub に接続する (Java)](quickstart-connect-device-java.md)」を完了している場合は、リポジトリを既にクローンしています。

任意のディレクトリでコマンド プロンプトを開きます。 次のコマンドを実行して、[Azure IoT Java SDK およびライブラリ](https://github.com/Azure/azure-iot-sdk-java)の GitHub リポジトリをこの場所にクローンします。

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

この操作が完了するまで数分かかることが予想されます。

## <a name="build-the-code"></a>コードのビルド

Windows で、クローンした Java SDK リポジトリのルート フォルダーに移動します。 次のコマンドを実行して、依存関係をビルドします。

```cmd/sh
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>デバイス サンプルを実行する

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

サンプル アプリケーションを実行するには、 *\device\iot-device-samples\pnp-device-sample\temperature-controller-device-sample* フォルダーに移動し、次のコマンドを実行します。

```cmd/sh
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
```

これで、デバイスはコマンドとプロパティの更新情報を受信する準備ができ、ハブへのテレメトリ データの送信が開始されました。 次の手順を完了するまで、サンプルを実行したままにしておきます。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT エクスプローラーを使用してコードを確認する

デバイス クライアントのサンプルが開始された後、Azure IoT エクスプローラー ツールを使用して、それが動作していることを確認します。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>コードの確認

このサンプルにより、IoT プラグ アンド プレイの温度コントローラー デバイスが実装されます。 このサンプルによって実装されるモデルにより、[複数のコンポーネント](concepts-components.md)が使用されます。 [温度デバイスの Digital Twins Definition Language (DTDL) モデル ファイル](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)により、デバイスによって実装されるテレメトリ、プロパティ、およびコマンドが定義されます。

デバイス コードによって、IoT ハブに接続するために標準の `DeviceClient` クラスが使用されます。 デバイスにより、接続要求で実装される DTDL モデルのモデル ID が送信されます。 モデル ID を送信するデバイスは、IoT プラグ アンド プレイ デバイスです。

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

モデル ID は、次のスニペットに示すように、コードに格納されます。

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

デバイスが IoT ハブに接続された後、コードによってコマンド ハンドラーが登録されます。

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);
```

2 つのサーモスタット コンポーネントには、必要なプロパティの更新用の個別のハンドラーがあります。

```java
deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_1, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_1)),
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_2, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_2))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

このサンプル コードによって、各サーモスタット コンポーネントからテレメトリが送信されます。

```java
sendTemperatureReading(THERMOSTAT_1);
sendTemperatureReading(THERMOSTAT_2);
```

`sendTemperatureReading` メソッドによって、各コンポーネントのメッセージを作成するために `PnpHhelper` クラスが使用されます。

```java
Message message = PnpHelper.createIotHubMessageUtf8(telemetryName, currentTemperature, componentName);
```

`PnpHelper` クラスには、複数コンポーネントのモデルで使用できるその他のサンプル メソッドが含まれています。

2 つのサーモスタット コンポーネントからのテレメトリとプロパティを表示するには、Azure IoT エクスプローラー ツールを使用します。

:::image type="content" source="media/tutorial-multiple-components-java/multiple-component.png" alt-text="Azure IoT エクスプローラーの複数コンポーネントのデバイス":::

また、Azure IoT エクスプローラー ツールを使用して、2 つのサーモスタット コンポーネントのいずれか、または既定のコンポーネントでコマンドを呼び出すこともできます。

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、コンポーネントを使用する IoT プラグ アンド プレイ デバイスを IoT ハブに接続する方法を学習しました。 IoT プラグ アンド プレイ デバイス モデルの詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [IoT プラグ アンド プレイ モデリング開発者ガイド](concepts-developer-guide-device-csharp.md)