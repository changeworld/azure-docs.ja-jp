<properties
 pageTitle="ダイレクト メソッドの使用 | Microsoft Azure"
 description="このチュートリアルでは、ダイレクト メソッドの使用方法について説明します。"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="nberdy"/>


# <a name="tutorial:-use-direct-methods"></a>チュートリアル: ダイレクト メソッドの使用

## <a name="introduction"></a>はじめに

Azure IoT Hub は、何百万もの IoT デバイスとアプリケーション バックエンドの間に信頼性のある保護された双方向通信を確立できる、完全に管理されたサービスです。 前のチュートリアル ([IoT Hub の使用]と [IoT Hub を使用したクラウドからデバイスへのメッセージの送信]に関するチュートリアル) では、IoT Hub のデバイスからクラウドおよびクラウドからデバイスへののメッセージングについて、基本的な機能の使用方法を説明しています。 さらに、IoT Hub には、クラウドからデバイスに持続性のないメソッドを呼び出す機能も備わっています。 メソッドは、デバイスとの要求/応答を表しています。これは、成功または失敗の直後 (ユーザーが指定したタイムアウト後) に、呼び出しのステータスをユーザーに伝える HTTP 呼び出しと同様のものです。 [デバイスでのダイレクト メソッドの呼び出し][lnk-devguide-methods]では、メソッドの詳細を説明し、メソッドやクラウドからデバイスへのメッセージを使用するタイミングに関するガイダンスを提供します。

このチュートリアルでは、次の操作方法について説明します。

- Azure Portal を使用して IoT Hub を作成し、IoT Hub でデバイス ID を作成します。
- クラウドから呼び出すことができるダイレクト メソッドを持つ、シミュレートされたデバイスを作成します。
- IoT Hub 経由で、シミュレートされたデバイス上にダイレクト メソッドを呼び出すコンソール アプリケーションを作成します。

> [AZURE.NOTE] 現在のところ、ダイレクト メソッドには MQTT プロトコルを使用して IoT Hub に接続するデバイスからのみアクセスできます。 既存のデバイス アプリを変換して MQTT を使用する方法については、[MQTT サポート][lnk-devguide-mqtt]の記事をご覧ください。

このチュートリアルの最後には、次の 2 つの Node.js コンソール アプリケーションが完成します。

* **CallMethodOnDevice.js**。シミュレートされたデバイスでメソッドを呼び出し、応答を表示します。
* **SimulatedDevice.js**。前もって作成してあるデバイス ID を使用して IoT Hub に接続し、クラウドによって呼び出されたメソッドに応答をします。

> [AZURE.NOTE] デバイス上で動作するアプリケーションの作成とソリューションのバックエンドで動作するアプリケーションの作成に利用できる各種 SDK に関する情報は、「[IoT Hub SDK][lnk-hub-sdks]」の記事で取り上げています。

このチュートリアルを完了するには、以下が必要です。

+ Node.js バージョン 0.10.x 以降。

+ アクティブな Azure アカウント。 アカウントがない場合は、無料試用版アカウントを数分で作成することができます。 詳細については、[Azure の無料試用版][lnk-free-trial]のサイトをご覧ください。

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>シミュレーション対象デバイス アプリの作成

このセクションでは、クラウドによって呼び出されたメソッドに応答する Node.js コンソール アプリを作成します。

1. **simulateddevice**という名前の新しい空のフォルダーを作成します。 コマンド プロンプトで次のコマンドを使用して、 **simulateddevice** フォルダー内に新しい package.json ファイルを作成します。 次の既定値をすべてそのまま使用します。

    ```
    npm init
    ```

2. コマンド プロンプトで、**simulateddevice** フォルダーに移動し、次のコマンドを実行して、**azure-iot-device** Device SDK パッケージと **azure-iot-device-mqtt** パッケージをインストールします。

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. テキスト エディターを使用して、**simulateddevice** フォルダーに新しい **SimulatedDevice.js** ファイルを作成します。

4. **SimulatedDevice.js** ファイルの先頭に、次の `require` ステートメントを追加します。

    ```
    'use strict';

    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```

5. **connectionString** 変数を追加し、それを使用してデバイス クライアントを作成します。 **{device connection string}** を「*デバイス ID の作成*」セクションで生成した接続文字列に置き換えます。

    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```

6. デバイスにメソッドを実装する次の関数を追加します。

    ```
    function onWriteLine(request, response) {
        console.log(request.payload);

        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```

7. IoT Hub への接続を開き、メソッド リスナーの初期化を開始します。

    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```

8. **SimulatedDevice.js** ファイルを保存して閉じます。

> [AZURE.NOTE] わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、MSDN の記事「[Transient Fault Handling (一時的な障害処理)][lnk-transient-faults]」で推奨されているように、再試行ポリシー (接続の再試行など) を実装することをお勧めします。

## <a name="call-a-method-on-a-device"></a>デバイスでメソッドを呼び出します。

このセクションでは、シミュレートされたデバイスでメソッドを呼び出し、応答を表示する Node.js コンソール アプリを作成します。

1. 「**callmethodondevice**」という名前の新しい空のフォルダーを作成します。 コマンド プロンプトで次のコマンドを使用して、**callmethodondevice** フォルダー内に新しい package.json ファイルを作成します。 次の既定値をすべてそのまま使用します。

    ```
    npm init
    ```

2. コマンド プロンプトで、**callmethodondevice** フォルダーに移動し、次のコマンドを実行して、**azure-iothub** パッケージをインストールします。

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. テキスト エディターを使用して、**callmethodondevice** フォルダーに **CallMethodOnDevice.js** ファイルを作成します。

4. **CallMethodOnDevice.js** ファイルの先頭に、次の `require` ステートメントを追加します。

    ```
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. 次の変数宣言を追加し、プレースホルダーの値を IoT Hub の接続文字列に置き換えます。

    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```

6. IoT Hub への接続を開くクライアントを作成します。

    ```
    var client = Client.fromConnectionString(connectionString);
    ```
    
7. デバイスのメソッドを呼び出し、コンソールにデバイスの応答を出力する、次の関数を追加します。

    ```
    var methodParams = {
        methodName: methodName,
        payload: 'a line to be written',
        timeoutInSeconds: 30
    };

    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```

7. **CallMethodOnDevice.js** ファイルを保存して閉じます。

## <a name="run-the-applications"></a>アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1. コマンド プロンプトで、**simulateddevice** フォルダーに移動し、次のコマンドを実行して、IoT Hub からのメソッドの呼び出しのリッスンを開始します。

    ```
    node SimulatedDevice.js
    ```

    ![][7]
    
2. **callmethodondevice** フォルダーで、コマンド プロンプトで次のコマンドを実行し、IoT Hub の監視を開始します。

    ```
    node CallMethodOnDevice.js 
    ```

    ![][8]
    
3. メッセージと、デバイスからの応答を表示するメソッドを呼び出したアプリケーションを出力することによって、デバイスの対応を確認できます。

    ![][9]
    
## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ポータルで新しい IoT Hub を構成し、ハブの ID レジストリにデバイス ID を作成しました。 シミュレーション対象デバイス アプリでクラウドから呼び出したメソッドに対応できるようにするために、このデバイス ID を使用しました。 また、デバイスでメソッドを呼び出し、デバイスからの応答を表示するアプリも作成しました。 

引き続き IoT Hub の使用方法を確認すると共に、他の IoT のシナリオについて調べるには、次のページを参照してください。

- [IoT Hub の概要]
- [複数デバイスでのジョブのスケジュール][lnk-devguide-jobs]

IoT ソリューションの拡張と複数のデバイスでのメソッドの呼び出しをスケジュールする方法については、「[Schedule and broadcast jobs (ジョブのスケジュールとブロードキャスト)][lnk-tutorial-jobs]」チュートリアルをご覧ください。

<!-- Images. -->
[7]: ./media/iot-hub-c2d-methods/run-simulated-device.png
[8]: ./media/iot-hub-c2d-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-c2d-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[IoT Hub を使用したクラウドからデバイスへのメッセージの送信]: iot-hub-csharp-csharp-c2d.md
[デバイスからクラウドへのメッセージの処理]: iot-hub-csharp-csharp-process-d2c.md
[IoT Hub の概要]: iot-hub-node-node-getstarted.md



<!--HONumber=Oct16_HO2-->


