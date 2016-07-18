<properties
	pageTitle="IoT Hub を使用したクラウドからデバイスへのメッセージの送信 | Microsoft Azure"
	description="このチュートリアルに従って、Java を使用して Azure IoT Hub でクラウドからデバイスへのメッセージを送信する方法を学習します。"
	services="iot-hub"
	documentationCenter="nodejs"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="07/04/2016"
     ms.author="dobett"/>

# チュートリアル: IoT Hub と Node.js でクラウドからデバイスへのメッセージを送信する方法

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## はじめに

Azure IoT Hub は、何百万もの IoT デバイスとアプリケーション バックエンドの間に信頼性のある保護された双方向通信を確立するのに役立つ、完全に管理されたサービスです。「[IoT Hub の概要]」チュートリアルには、IoT Hub の作成方法、IoT Hub でデバイス ID をプロビジョニングする方法、およびデバイスからクラウドへのメッセージを送信するシミュレーション済みデバイスをコード化する方法が示されています。

このチュートリアルは、[IoT Hub の概要]に関するページのチュートリアルに基づいて作成されており、次の方法について説明します。

- アプリケーションのクラウド バックエンドから IoT Hub を介して単一のデバイスにクラウドからデバイスへのメッセージを送信する。
- クラウドからデバイスへのメッセージをデバイスで受信する。
- IoT Hub からデバイスに送信されたメッセージの配信確認 (*フィードバック*) を、アプリケーションのクラウド バックエンドから要求する。

クラウドからデバイスへのメッセージの詳細については、「[IoT Hub 開発者ガイド][IoT Hub Developer Guide - C2D]」をご覧ください。

このチュートリアルの最後には、次の 2 つの Node.js コンソール アプリケーションを実行します。

* **SimulatedDevice**。「[IoT Hub の概要]」で作成されたアプリケーションの修正バージョン。これは、IoT Hub に接続し、クラウドからデバイスへのメッセージを受け取ります。
* **SendCloudToDeviceMessage**。クラウドからデバイスへのメッセージを IoT Hub を介してシミュレートされたデバイスに送信し、その配信確認を受け取ります。

> [AZURE.NOTE] IoT Hub には、Azure IoT device SDK を介した多数のデバイス プラットフォームや言語 (C、Java、Javascript など) に対する SDK サポートがあります。このチュートリアルのコード (一般的には Azure IoT Hub) にデバイスを接続するための詳しい手順については、[Azure IoT デベロッパー センター]のページを参照してください。

このチュートリアルで行う作業には次のものが必要となります。

+ Node.js バージョン 0.12.x 以降。<br/> 「[Prepare your development environment (開発環境を準備する)][lnk-dev-setup]」では、このチュートリアルのために Node.js を Windows または Linux にインストールする方法が説明されています。

+ アクティブな Azure アカウント。アカウントがない場合は、無料試用版アカウントを数分で作成することができます。詳細については、[Azure の無料試用版][lnk-free-trial]サイトを参照してください。

## シミュレーション済みデバイスでのメッセージの受信

このセクションでは、[IoT Hub の概要]に関するページで作成したシミュレートされたデバイス アプリケーションを、クラウドからデバイスへのメッセージを IoT Hub から受信するように変更します。

1. テキスト エディターを使用して SimulatedDevice.js ファイルを開きます。

2. IoT Hub から送信されたメッセージを処理するように **connectCallback** 関数を変更します。この例では、メッセージが処理されたことを IoT Hub に通知するために、デバイスは必ず **complete** 関数を呼び出します。変更後の **connectCallback** 関数は次のようになります。

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```

    > [AZURE.NOTE] トランスポートとして AMQP の代わりに HTTP/1 を使用した場合、**DeviceClient** インスタンスが IoT Hub からのメッセージをチェックする頻度は低くなります (25 分に 1 回未満)。AMQP と HTTP/1 のサポートの相違点と、IoT Hub スロットルの詳細については、「[Azure IoT Hub 開発者ガイド][IoT Hub Developer Guide - C2D]」を参照してください。

## クラウドからデバイスへのメッセージをアプリケーションのバックエンドから送信する

このセクションでは、クラウドからデバイスへのメッセージを、シミュレートされたデバイス アプリに送信する Node.js コンソール アプリを作成します。[IoT Hub の概要]に関するページのチュートリアルで追加したデバイスのデバイス ID と、[Azure ポータル]で確認できる IoT Hub の接続文字列が必要です。

1. 新しい空のフォルダーを **sendcloudtodevicemessage** という名前で作成します。コマンド プロンプトで次のコマンドを使用して、**sendcloudtodevicemessage** フォルダー内に新しい package.json ファイルを作成します。次の既定値をすべてそのまま使用します。

    ```
    npm init
    ```

2. コマンド プロンプトで、**sendcloudtodevicemessage** フォルダーに移動し、次のコマンドを実行して、**azure-iothub** パッケージをインストールします。

    ```
    npm install azure-iothub --save
    ```

3. テキスト エディターを使用して、**sendcloudtodevicemessage** フォルダーに新しい **SendCloudToDeviceMessage.js** ファイルを作成します。

4. **SendCloudToDeviceMessage.js** ファイルの先頭に、次の `require` ステートメントを追加します。

    ```
    'use strict';
    
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. **SendCloudToDeviceMessage.js** ファイルに次のコードを追加します。接続文字列のプレースホルダーの値は、[IoT Hub の概要]に関するページのチュートリアルで作成した IoT Hub の接続文字列に置き換えてください。また、ターゲット デバイスのプレースホルダーには、[IoT Hub の概要]に関するページのチュートリアルで作成したデバイスのデバイス ID を指定します。

    ```
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. 操作結果をコンソールに出力するための次の関数を追加します。

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. 配信フィードバック メッセージをコンソールに出力するための次の関数を追加します。

    ```
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. デバイスにメッセージを送信し、クラウドからデバイスへのメッセージが配信されたことの確認応答がデバイスからあったときにフィードバック メッセージを処理するための次のコードを追加します。

    ```
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

7. **SendCloudToDeviceMessage.js** ファイルを保存して閉じます。

## アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1. **simulateddevice** フォルダーからコマンド プロンプトで次のコマンドを実行し、IoT Hub にテレメトリを送信して、クラウドからデバイスへのメッセージを待機します。

    ```
    node SimulatedDevice.js 
    ```

    ![][img-simulated-device]

2. **sendcloudtodevicemessage** フォルダーからコマンド プロンプトで次のコマンドを実行し、クラウドからデバイスへのメッセージを送信して、配信確認のフィードバックを待機します。

    ```
    node SendCloudToDeviceMessage.js 
    ```

    ![][img-send-command]

    > [AZURE.NOTE] わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。運用環境のコードでは、MSDN の記事「[Transient Fault Handling (一時的な障害の処理)]」で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。

## 次のステップ

このチュートリアルでは、クラウドからデバイスへのメッセージを送受信する方法を学習しました。次のチュートリアルで IoT Hub の機能やシナリオをさらに詳しく調べることができます。

- 「[デバイスからクラウドへのメッセージの処理]」には、デバイスから送信されるテレメトリおよび対話型メッセージを確実に処理する方法が示されています。
- 「[デバイスからのファイルのアップロード]」では、デバイスからのファイル アップロードを容易にするためにクラウドからデバイスへのメッセージを活用したパターンについて説明しています。

IoT Hub に関するその他の情報:

* [IoT Hub の概要]
* [IoT Hub 開発者ガイド]
* [IoT Hub のガイダンス]
* [サポートされているデバイスのプラットフォームおよび言語]
* [Azure IoT デベロッパー センター]

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]: media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[IoT Hub の概要]: iot-hub-node-node-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[デバイスからクラウドへのメッセージの処理]: iot-hub-csharp-csharp-process-d2c.md
[デバイスからのファイルのアップロード]: iot-hub-csharp-csharp-file-upload.md
[IoT Hub の概要]: iot-hub-what-is-iot-hub.md
[IoT Hub のガイダンス]: iot-hub-guidance.md
[IoT Hub 開発者ガイド]: iot-hub-devguide.md
[サポートされているデバイスのプラットフォームおよび言語]: iot-hub-supported-devices.md
[Azure IoT デベロッパー センター]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[Transient Fault Handling (一時的な障害の処理)]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure ポータル]: https://portal.azure.com

<!---HONumber=AcomDC_0706_2016-->