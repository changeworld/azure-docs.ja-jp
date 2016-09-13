<properties
	pageTitle="Node.js で Azure IoT Hub を使用する方法 | Microsoft Azure"
	description="Node.js で Azure IoT Hub を使用する方法についてわかりやすく説明します。Azure IoT Hub と Node.js、Microsoft Azure IoT SDK を使用して IoT ソリューションを実装します。"
	services="iot-hub"
	documentationCenter="nodejs"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/16/2016"
     ms.author="dobett"/>

# Azure IoT Hub for Node.js の使用

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

このチュートリアルの最後には、次の 3 つの Node.js コンソール アプリケーションが完成します。

* **CreateDeviceIdentity.js**。デバイス ID と関連付けられているセキュリティ キーを作成し、シミュレーション対象デバイスを接続します。
* **ReadDeviceToCloudMessages.js**。シミュレーション対象デバイスから送信されたテレメトリを表示します。
* **SimulatedDevice.js**。以前に作成したデバイス ID で IoT Hub に接続し、AMQPS プロトコルを使用して 1 秒ごとにテレメトリ メッセージを送信します。

> [AZURE.NOTE] デバイス上で動作するアプリケーションの作成とソリューションのバックエンドで動作するアプリケーションの作成に利用できる各種 SDK に関する情報は、「[IoT Hub SDK][lnk-hub-sdks]」の記事で取り上げています。

このチュートリアルで行う作業には次のものが必要となります。

+ Node.js バージョン 0.12.x 以降。<br/> 「[Prepare your development environment (開発環境を準備する)][lnk-dev-setup]」では、このチュートリアルのために Node.js を Windows または Linux にインストールする方法が説明されています。

+ アクティブな Azure アカウント。アカウントがない場合は、無料試用版アカウントを数分で作成することができます。詳細については、[Azure の無料試用版][lnk-free-trial]サイトを参照してください。

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

IoT Hub の作成は以上です。以降の作業に必要な IoT Hub ホスト名と IoT Hub 接続文字列が得られました。

## デバイス ID の作成

このセクションでは、IoT Hub の ID レジストリに新しいデバイス ID を作成する Node.js コンソール アプリケーションを作成します。IoT Hub に接続するデバイスは、あらかじめデバイス ID レジストリに登録されている必要があります。詳細については、「[IoT Hub 開発者ガイド][lnk-devguide-identity]」の「**デバイス ID レジストリ**」を参照してください。このコンソール アプリケーションを実行すると、デバイスからクラウドへのメッセージを IoT Hub に送信するときにそのデバイスを識別する一意の ID とキーが生成されます。

1. **createdeviceidentity** という名前の新しい空のフォルダーを作成します。コマンド プロンプトで次のコマンドを使用して、**createdeviceidentity** フォルダー内に新しい package.json ファイルを作成します。次の既定値をすべてそのまま使用します。

    ```
    npm init
    ```

2. コマンド プロンプトで、**createdeviceidentity** フォルダーに移動し、次のコマンドを実行して、**azure-iothub** Service SDK パッケージをインストールします。

    ```
    npm install azure-iothub --save
    ```

3. テキスト エディターを使用して、**createdeviceidentity** フォルダー内に新しい **CreateDeviceIdentity.js** ファイルを作成します。

4. **CreateDeviceIdentity.js** ファイルの先頭に、次の `require` ステートメントを追加します。

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. **CreateDeviceIdentity.js** ファイルに次のコードを追加し、プレースホルダーの値を、前のセクションで作成した IoT Hub の接続文字列に置き換えます。

    ```
    var connectionString = '{iothub connection string}';
    
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. 次のコードを追加して、IoT Hub でデバイス ID のレジストリに新しいデバイスの定義を作成します。このコードは、デバイス ID がレジストリに存在しない場合には新しいデバイスを作成し、存在している場合には既存のデバイスのキーを返します。

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstNodeDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```

7. **CreateDeviceIdentity.js** ファイルを保存して閉じます。

8. **createdeviceidentity** アプリケーションを実行するには、コマンド プロンプトで createdeviceidentity フォルダーに移動し、次のコマンドを実行します。

    ```
    node CreateDeviceIdentity.js 
    ```

9. **デバイス ID** と**デバイス キー**をメモします。後でデバイスとして IoT Hub に接続するアプリケーションを作成するときに必要になります。

> [AZURE.NOTE] IoT Hub の ID レジストリには、ハブに対するアクセスの安全性を確保するためのデバイス ID だけが格納されます。セキュリティ資格情報として使用するキーとデバイス ID、そして個々のデバイスについてアクセスを無効にすることのできる有効/無効フラグが格納されます。その他デバイス固有のメタデータをアプリケーションで保存する必要がある場合は、アプリケーション固有のストアを使用する必要があります。詳細については、「[IoT Hub 開発者ガイド][lnk-devguide-identity]」を参照してください。

## デバイスからクラウドへのメッセージの受信

このセクションでは、IoT Hub からのデバイスからクラウドへのメッセージを読み込む Node.js コンソール アプリケーションを作成します。Iot Hub は、デバイスからクラウドへのメッセージを読み取るための、[Event Hubs][lnk-event-hubs-overview] と互換性のあるエンドポイントを公開します。わかりやすくするために、このチュートリアルで作成するリーダーは基本的なものであり、高スループットのデプロイメントには適していません。[デバイスからクラウドへのメッセージの処理][lnk-process-d2c-tutorial]に関するチュートリアルでは、デバイスからクラウドへのメッセージを大規模に処理する方法を紹介しています。「[Event Hubs の使用][lnk-eventhubs-tutorial]」チュートリアルでは、Event Hubs からのメッセージを処理する方法について詳しく説明しています。また、このチュートリアルは IoT Hub の Event Hubs と互換性のあるエンドポイントに適用されます。

> [AZURE.NOTE] 常に、Event Hubs と互換性のあるエンドポイントは、デバイスからクラウドへのメッセージを読み取るために AMQPS プロトコルを使用します。

1. **readdevicetocloudmessages** という名前の新しい空のフォルダーを作成します。コマンド プロンプトで次のコマンドを使用して、**readdevicetocloudmessages** フォルダー内に新しい package.json ファイルを作成します。次の既定値をすべてそのまま使用します。

    ```
    npm init
    ```

2. コマンド プロンプトで、**readdevicetocloudmessages** フォルダーに移動し、次のコマンドを実行して、**azure-event-hubs** パッケージをインストールします。

    ```
    npm install azure-event-hubs --save
    ```

3. テキスト エディターを使用して、**readdevicetocloudmessages** フォルダー内に新しい **ReadDeviceToCloudMessages.js** ファイルを作成します。

4. **ReadDeviceToCloudMessages.js** ファイルの先頭に、次の `require` ステートメントを追加します。

    ```
    'use strict';

    var EventHubClient = require('azure-event-hubs').Client;
    ```

5. 次の変数宣言を追加し、プレースホルダーの値を IoT Hub の接続文字列に置き換えます。

    ```
    var connectionString = '{iothub connection string}';
    ```

6. コンソールに出力する次の 2 つの関数を追加します。

    ```
    var printError = function (err) {
      console.log(err.message);
    };

    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```

7. **EventHubClient** を作成し、IoT Hub への接続を開き、各パーティションのレシーバーを作成する次のコードを追加します。受信側が実行開始後、IoT Hub に送信されたメッセージのみを読み込むように、このアプリケーション メソッドは受信側の構築時にフィルターを使用します。現在の一連のメッセージを確認できるので、テスト環境ではこれは便利ですが、運用環境の場合、コードですべてのメッセージが処理されることを確認する必要があります。詳細については、[IoT Hub のデバイスからクラウドへのメッセージを処理する方法][lnk-process-d2c-tutorial]に関するチュートリアルを参照してください。

    ```
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```

8. **ReadDeviceToCloudMessages.js** ファイルを保存して閉じます。

## シミュレーション対象デバイス アプリの作成

このセクションでは、デバイスからクラウドへのメッセージを IoT Hub に送信するデバイスをシミュレートする Node.js コンソール アプリを作成します。

1. **simulateddevice** という名前の新しい空のフォルダーを作成します。コマンド プロンプトで次のコマンドを使用して、**simulateddevice** フォルダー内に新しい package.json ファイルを作成します。次の既定値をすべてそのまま使用します。

    ```
    npm init
    ```

2. コマンド プロンプトで、**simulateddevice** フォルダーに移動し、次のコマンドを実行して、**azure-iot-device** Device SDK パッケージと **azure-iot-device-amqp** パッケージをインストールします。

    ```
    npm install azure-iot-device azure-iot-device-amqp --save
    ```

3. テキスト エディターを使用して、**simulateddevice** フォルダーに新しい **SimulatedDevice.js** ファイルを作成します。

4. **SimulatedDevice.js** ファイルの先頭に、次の `require` ステートメントを追加します。

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. **connectionString** 変数を追加し、それを使用してデバイス クライアントを作成します。**{youriothostname}** は、「*IoT Hub の作成*」セクションで作成した IoT Hub の名前に置き換えます。**{yourdevicekey}** は、「*デバイス ID の作成*」セクションで生成したデバイス キーの値に置き換えます。

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. 次の関数を追加してアプリケーションからの出力を表示します。

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. コールバックを作成し、**setInterval** 関数を使用して 1 秒ごとに新しいメッセージを IoT Hub に送信します。

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

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

8. IoT Hub への接続を開き、メッセージの送信を開始します。

    ```
    client.open(connectCallback);
    ```

9. **SimulatedDevice.js** ファイルを保存して閉じます。

> [AZURE.NOTE] わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。運用環境のコードでは、[一時的な障害処理][lnk-transient-faults]に関する MSDN の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。


## アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1. コマンド プロンプトで、**readdevicetocloudmessages** フォルダーに移動し、次のコマンドを実行して IoT Hub の監視を開始します。

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![][7]

2. コマンド プロンプトで、**simulateddevice** フォルダーに移動し、次のコマンドを実行して IoT Hub へのテレメトリ データの送信を開始します。

    ```
    node SimulatedDevice.js
    ```

    ![][8]

3. [Azure ポータル][lnk-portal]の **[使用状況]** タイルには、ハブに送信されたメッセージ数が表示されます。

    ![][43]

## 次のステップ

このチュートリアルでは、ポータルで新しい IoT Hub を構成し、ハブの ID レジストリにデバイス ID を作成しました。シミュレーション対象デバイス アプリでデバイスからクラウドへのメッセージをハブに送信できるようにするために、このデバイス ID を使用しました。また、ハブで受け取ったメッセージを表示するアプリを作成しました。

引き続き IoT Hub の使用法を確認すると共に、他の IoT のシナリオについて調べるには、次のページを参照してください。

- [デバイスを接続する][lnk-connect-device]
- [デバイス管理の概要][lnk-device-management]
- [Gateway SDK の概要][lnk-gateway-SDK]

既存の IoT ソリューションを拡張し、大規模な環境でデバイスからクラウドへのメッセージを処理する方法については、[デバイスからクラウドへのメッセージの処理][lnk-process-d2c-tutorial]に関するチュートリアルを参照してください。

<!-- Images. -->
[6]: ./media/iot-hub-node-node-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

<!---HONumber=AcomDC_0907_2016-->