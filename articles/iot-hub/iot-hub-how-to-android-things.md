---
title: Azure IoT SDK を使用した Android Things プラットフォーム向けの開発 | Microsoft Docs
description: 開発者ガイド - Azure IoT Hub SDK を使用して Android Things 上で開発を行う方法について説明します。
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 1/30/2019
ms.author: yizhon
ms.openlocfilehash: b213642b093c3b5f79e5993af91ae51517f09c70
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747916"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Azure IoT SDK を利用したモバイル デバイス向け開発
[Azure IoT Hub SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) では、Windows、Linux、OSX、MBED、および Android や iOS などのモバイル プラットフォームといった人気のある広範なプラットフォーム向けの、最上位のサポートを提供します。  IoT の展開において選択の幅を広げ、柔軟性を高めるための取り組みの一環として、Java SDK では [Android Things](https://developer.android.com/things/) プラットフォームもサポートされています。  開発者は、デバイス側では Android Things オペレーティング システムの利点を活用しながら、数百万台のデバイスの同時接続までスケーリングする中央メッセージ ハブとして [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) を使用できます。 

このチュートリアルでは、Azure IoT Java SDK を使用して Android Things 上のデバイス側アプリケーションをビルドする手順について説明します。

## <a name="prerequisites"></a>前提条件
* Android Things OS が実行されている、Android Things でサポートされるハードウェア。  Android Things をフラッシュする方法については、[Android Things のドキュメント](https://developer.android.com/things/get-started/kits#flash-at)をご覧ください。  Android Things デバイスがインターネットに接続されていて、キーボード、ディスプレイ、マウスなどの必要な周辺機器が取り付けられていることを確認します。  このチュートリアルでは、Raspberry Pi 3 を使用します。
* 最新バージョンの [Visual Studio](https://developer.android.com/studio/)
* 最新バージョンの [Git](https://git-scm.com/)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>デバイスの登録

デバイスを IoT ハブに接続するには、あらかじめ IoT ハブに登録しておく必要があります。 このクイック スタートでは、Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Azure Cloud Shell で次のコマンドを実行して IoT Hub CLI 拡張機能を追加し、デバイス ID を作成します。 

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

   **MyAndroidThingsDevice**:これは、登録済みデバイスに付けられた名前です。 示されているように、MyAndroidThingsDevice を使用します。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用する必要があります。また、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新してください。

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Azure Cloud Shell で次のコマンドを実行して、登録したデバイスの "_デバイス接続文字列_" を取得します。**YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    次のようなデバイス接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    この値は、このクイック スタートの後の方で使います。

## <a name="building-an-android-things-application"></a>Android Things アプリケーションをビルドする
1.  Android Things アプリケーションをビルドする最初のステップは、Android Things デバイスに接続することです。  Android Things デバイスをディスプレイに接続し、インターネットに接続します。  Android Things では、WiFi への接続方法に関する[ドキュメント](https://developer.android.com/things/get-started/kits)が提供されています。  インターネットに接続した後、[ネットワーク] の一覧に表示される IP アドレスを書き留めておきます。
2.  [adb](https://developer.android.com/studio/command-line/adb) ツールを使用し、上で書き留めた IP アドレスで Android Things デバイスに接続します。  ターミナルから次のコマンドを使用して接続を再確認します。  一覧でデバイスに "connected" と表示される必要があります
    ```
    adb devices
    ```
3.  こちらの[リポジトリ](https://github.com/Azure-Samples/azure-iot-samples-java)から、または Git を使用して、Android/Android Things のサンプルをダウンロードます。
    ```
    git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
    ```
4.  Android Studio で、"\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample" にある Android プロジェクトを開きます。
5.  gradle.properties ファイルを開き、"Device_connection_string" を前に書き留めた実際のデバイス接続文字列に置き換えます。
6.  [Run]\(実行\) > [Debug]\(デバッグ\) をクリックして、このコードを Android Things デバイスに展開するためのデバイスを選択します。
7.  アプリケーションが正常に開始されると、Android Things デバイスで実行されているアプリケーションを確認できます。  このサンプル アプリケーションでは、ランダムに生成された温度の測定値が送信されます。

## <a name="read-the-telemetry-from-your-hub"></a>ハブから利用統計情報を読み取る

XCode エミュレーターで実行したサンプル アプリは、デバイスから送信されたメッセージに関するデータを表示します。 データの受信時に IoT ハブを経由するデータを表示することもできます。 IoT Hub CLI 拡張機能は、IoT ハブ上のサービス側 **Events** エンドポイントに接続できます。 この拡張機能は、シミュレートされたデバイスから送信されたデバイスとクラウドの間のメッセージを受信します。 通常、IoT Hub のバックエンド アプリケーションはクラウド内で実行され、デバイスとクラウドの間のメッセージ受信して処理します。

Azure Cloud Shell で、以下のコマンドを実行します。`YourIoTHubName` は実際の IoT ハブの名前に置き換えます。

```
azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

* IoT Hub デバイス SDK を使用して[接続と信頼できるメッセージングを管理する方法](iot-hub-reliability-features-in-sdks.md)に関する記事を参照してください。
* iOS や Android などの[モバイル プラットフォーム向けの開発](iot-hub-how-to-develop-for-mobile-devices.md)方法に関する記事を参照してください。
* [Azure IoT SDK プラットフォームのサポート](iot-hub-device-sdk-platform-support.md)