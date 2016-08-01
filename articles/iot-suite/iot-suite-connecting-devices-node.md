<properties
   pageTitle="Node.js を使用してデバイスを接続する |Microsoft Azure"
   description="Node.js で記述されたアプリケーションを使用して、デバイスを Azure IoT Suite 構成済みリモート監視ソリューションに接続する方法について説明します。"
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/14/2016"
   ms.author="dobett"/>


# デバイスをリモート監視構成済みソリューションに接続する (Node.js)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## node.js サンプル ソリューションのビルドと実行

1. *Microsoft Azure IoT SDK* GitHub リポジトリのクローンを作成し、*Microsoft Azure IoT device SDK for Node.js* をデスクトップ環境にインストールするには、「[開発環境を準備する][lnk-github-prepare]」の指示に従います。

2. [azure iot-sdk][lnk-github-repo] リポジトリの node/device/samples フォルダーのローカル コピーから、次の 2 つのファイルをコピーします。

  - packages.json
  - remote\_monitoring.js

3. remote\_monitoring.js ファイルを開き、次の変数を探します。

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

4. **[IoT Hub device connection string]** を、デバイスの接続文字列に置き換えます。IoT Hub のホスト名、デバイス ID およびデバイス キーの値は、リモート監視ソリューション ダッシュボードにあります。デバイスの接続文字列は、次の形式にする必要があります。

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    IoT Hub のホスト名が **contoso** でデバイス ID が **mydevice** の場合、接続文字列は以下のようになります。

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

5. ファイルを保存します。コマンド プロンプトで、これらのファイルが含まれているフォルダーで次のコマンドを実行して必要なパッケージをインストールし、サンプル アプリケーションを実行します。

    ```
    npm install
    node remote_monitoring.js
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdks
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

<!---HONumber=AcomDC_0720_2016-->