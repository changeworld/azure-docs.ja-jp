---
title: Azure IoT ソリューションに接続されている IoT プラグ アンド プレイ プレビュー デバイスとやりとりする | Microsoft Docs
description: Java を使用して、ご利用の Azure IoT ソリューションに接続されている IoT プラグ アンド プレイ プレビュー デバイスと接続してやり取りします。
author: baanders
ms.author: baanders
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 217dfe125dcacae5d50645275b20421a23169cb9
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550878"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-java"></a>クイック スタート:ご利用のソリューションに接続されている IoT プラグ アンド プレイ プレビュー デバイスとやり取りする (Java)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT プラグ アンド プレイ プレビューを使用すると、基礎となるデバイスの実装に関する知識がなくてもデバイスの機能とやりとりできるので、IoT が簡略化されます。 このクイックスタートでは、Java を使用して、ご利用のソリューションに接続されている IoT プラグ アンド プレイ デバイスに接続して制御する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイックスタートを完了するには、お使いの開発用マシン上に Java SE 8 が必要です。 Maven 3 もインストールする必要があります。

これらの設定方法の詳細については、Java 用 Microsoft Azure IoT device SDK の「[開発環境を準備する](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md)」を参照してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

次のコマンドを実行して、ご使用のハブに対する _IoT ハブ接続文字列_を取得します (後で使用するためにメモします)。

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>サンプル デバイスを実行する

このクイックスタートでは、Java で IoT プラグ アンド プレイ デバイスとして記述されたサンプル環境センサーを使用します。 次の手順では、デバイスをインストールして実行する方法を示します。

1. 任意のディレクトリでターミナル ウィンドウを開きます。 次のコマンドを実行して、[Java 用 Azure IoT サンプル](https://github.com/Azure-Samples/azure-iot-samples-java)の GitHub リポジトリをこの場所にクローンします。

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-java
    ```

1. これで、このターミナル ウィンドウは "_デバイス_" ターミナルとして使用されるようになります。 クローンしたリポジトリのフォルダーに移動し、さらに **/azure-iot-samples-java/digital-twin/Samples/device/JdkSample** フォルダーに移動します。 次のコマンドを実行して、必要なライブラリをインストールし、シミュレートされたデバイス アプリケーションをビルドします。

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. _デバイス接続文字列_を構成します。

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. 次のコマンドを実行して、デバイス フォルダーからサンプルを実行します。

    ```cmd/sh
    java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
    ```

1. デバイスが接続されたこと、さまざまな設定手順が実行されていること、およびサービスの更新を待っていることを示すメッセージが表示され、その後にテレメトリ ログが表示されます。 これは、デバイスがコマンドとプロパティの更新情報を受信する準備が整い、ハブへの利用統計情報の送信を開始したことを示します。 次の手順を完了するまで、サンプルを実行したままにしておきます。 このターミナルは閉じないでください。後でサービスのサンプルも動作していることを確認するために必要になります。

## <a name="run-the-sample-solution"></a>サンプル ソリューションを実行する

このクイックスタートでは、Java で記述されたサンプル IoT ソリューションを使用してサンプル デバイスとやり取りします。

1. 別のターミナル ウィンドウを開きます (これは "_サービス_" ターミナルになります)。 クローンしたリポジトリのフォルダーに移動し、さらに **/azure-iot-samples-java\digital-twin\Samples\service\JdkSample** フォルダーに移動します。

1. 次のコマンドを実行して、必要なライブラリをインストールし、サービス サンプルをビルドします。

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. "_IoT ハブ接続文字列_" と "_デバイス ID_" を構成して、サービスがこれらの両方に接続できるようにします。

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>プロパティを読み取る

1. そのターミナルで "_デバイス_" に接続したときの出力メッセージの 1 つは、オンライン状態を示す次のメッセージでした。 デバイスがオンラインであるかどうかを示すために使用される `state` プロパティは _true_ です。

    ```cmd/sh
    State of environmental sensor was set to true
    ```

1. "_サービス_" ターミナルにアクセスし、次のコマンドを使用して、デバイス情報を読み取るためのサービス サンプルを実行します。

    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```

1. "_サービス_" ターミナル出力内の `environmentalSensor` コンポーネントまでスクロールします。 `state` プロパティが _true_ として報告されていることがわかります。
    ```JSON
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>書き込み可能なプロパティを更新する

1. "_サービス_" ターミナルにアクセスし、次の変数を設定して、更新するプロパティを定義します。
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. 次のコマンドを使用して、プロパティを更新するためのサービス サンプルを実行します。

    ```cmd/sh
    java -jar update-digital-twin/target/update-digital-twin-with-deps.jar
    ```

1. "_サービス_" ターミナル出力に、更新されたデバイス情報が表示されます。 `environmentalSensor` コンポーネントまでスクロールして、brightness の新しい値 42 を確認します。

    ```json
    "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
            "brightness": {
                "reported": null,
                "desired": {
                    "value": "42"
                }
            },
    ```

1. _デバイス_ ターミナルにアクセスして、デバイスが更新プログラムを受信済みであることを確認します。

    ```cmd/sh
    OnPropertyUpdate called: propertyName=brightness, reportedValue=null, desiredVersion=2, desiredValue={"value":"42"}
    Report property: propertyName=brightness, reportedValue={"value":"42"}, desiredVersion=2 was DIGITALTWIN_CLIENT_OK
    ```
2. "_サービス_" ターミナルに戻り、次のコマンドを実行してデバイス情報を再取得し、プロパティが更新されたことを確認します。
    
    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```
3. "_サービス_" ターミナル出力の `environmentalSensor` コンポーネントの下に、brightness の更新された値が報告されていることがわかります。 注: デバイスが更新を完了するまで、しばらく時間がかかる場合があります。 デバイスによってプロパティの更新が実際に処理されるまで、この手順は繰り返すことができます。
    
    ```json
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "brightness" : {
          "reported" : {
            "value" : {
              "value" : "42"
            },
            "desiredState" : {
              "code" : 200,
              "version" : 2,
              "description" : "OK"
            }
          },
          "desired" : {
            "value" : "42"
          }
        },
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
        }
      }
    },       
    ```

### <a name="invoke-a-command"></a>コマンドを呼び出す

1. "_サービス_" ターミナルにアクセスし、次の変数を設定して、呼び出すコマンドを定義します。
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    set PAYLOAD=10
    ```

1. 次のコマンドを使用して、コマンドを呼び出すためのサービス サンプルを実行します。

    ```cmd/sh
    java -jar invoke-digital-twin-command/target/invoke-digital-twin-command-with-deps.jar
    ```

1. "_サービス_" ターミナルの出力に、次の確認が表示されます。

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned PAYLOAD was
    ```

1. _デバイス_ ターミナルにアクセスすると、コマンドが確認済みであることがわかります。

    ```cmd/sh
    OnCommandReceived called: commandName=blink, requestId=<some ID value>, commandPayload="10"
    EnvironmentalSensor is blinking every 10 seconds.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT プラグ アンド プレイ デバイスを IoT ソリューションに接続する方法を学習しました。 ご利用の IoT プラグ アンド プレイ デバイスとやりとりするソリューションを構築する方法の詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [方法: デバイスに接続してやりとりする](howto-develop-solution.md)
