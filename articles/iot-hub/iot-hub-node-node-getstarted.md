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
     ms.date="03/22/2016"
     ms.author="dobett"/>

# Azure IoT Hub for Node.js の使用

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## はじめに

Azure IoT Hub は、何百万もの IoT (モノのインターネット) デバイスとソリューション バック エンド間で、セキュリティで保護された信頼性のある双方向通信を実現する、完全に管理されたサービスです。IoT プロジェクトが直面する最も大きな課題の 1 つは、ソリューション バックエンドにデバイスを確実かつ安全に接続する方法です。この課題に対応するために、IoT Hub は次の機能を備えています。

- 非常にスケール性が高く信頼性に優れた、デバイスとクラウド間の双方向メッセージングを提供します。
- デバイスごとのセキュリティ資格情報とアクセス制御を使用して、セキュリティで保護された通信を可能します。
- 最も一般的な言語とプラットフォームのデバイスのライブラリが含まれます。

このチュートリアルでは、次の操作方法について説明します。

- Azure ポータルを使用して IoT Hub を作成する。
- IoT Hub におけるデバイス ID を作成する。
- クラウド バックエンドにテレメトリを送信するシミュレーション対象デバイスを作成する。

このチュートリアルの最後には、次の 3 つの Node.js コンソール アプリケーションが作成されています。

* **CreateDeviceIdentity.js**。デバイス ID と関連付けられているセキュリティ キーを作成し、シミュレーション対象デバイスを接続します。
* **ReadDEviceToCloudMessages.js**。シミュレーション対象デバイスから送信されたテレメトリを表示します。
* **SimulatedDevice.js**。以前に作成したデバイス ID で IoT ハブに接続し、AMQPS プロトコルを使用して 1 秒ごとにテレメトリ メッセージを送信します。

> [AZURE.NOTE] デバイス上で動作するアプリケーションの作成とソリューションのバックエンドで動作するアプリケーションの作成に利用できる各種 SDK に関する情報は、「[IoT Hub SDK][lnk-hub-sdks]」の記事で取り上げています。

このチュートリアルで行う作業には次のものが必要となります。

+ Node.js バージョン 0.12.x 以降。<br/> 「[Prepare your development environment (開発環境を準備する)][lnk-dev-setup]」には、このチュートリアルのために Node.js をインストールする方法があります。Windows と Linux の両方が対象となっています。

+ アクティブな Azure アカウント。<br/>アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト][lnk-free-trial]を参照してください。

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

最後の手順として、[IoT Hub] ブレードの **[設定]** をクリックし、**[設定]** ブレードの **[メッセージング]** をクリックします。**[メッセージング]** ブレードで、**[イベント ハブと互換性のある名前]** と **[イベント ハブと互換性のあるエンドポイント]** をメモします。この値は **read-d2c-messages** アプリケーションを作成するときに必要になります。

![][6]

これで IoT Hub が作成され、このチュートリアルの残りを完了するために必要な IoT Hub ホスト名、IoT Hub 接続文字列、Event Hub 対応名、Event Hub 対応エンドポイント値が与えられました。

## デバイス ID の作成

このセクションでは、IoT Hub の ID レジストリに新しいデバイス ID を作成する Node.js コンソール アプリケーションを作成します。IoT Hub に接続するデバイスは、あらかじめデバイス ID レジストリに登録されている必要があります。詳細については、「[IoT Hub 開発者ガイド][lnk-devguide-identity]」の「**デバイス ID レジストリ**」を参照してください。このコンソール アプリケーションを実行すると、デバイスからクラウドへのメッセージを IoT Hub に送信するときにそのデバイスを識別する一意の ID とキーが生成されます。

1. **createdeviceidentity** という名前の新しい空のフォルダーを作成します。コマンド プロンプトで次のコマンドを使用して、**createdeviceidentity** フォルダー内に新しい package.json ファイルを作成します。次の既定値をすべてそのまま使用します。

    ```
    npm init
    ```

2. **createdeviceidentity** フォルダーで、コマンド プロンプトで次のコマンドを実行し、**azure iothub** パッケージをインストールします。

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
    device.deviceId = 'myFirstDevice';
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

このセクションでは、IoT Hub からのデバイスからクラウドへのメッセージを読み込む Node.js コンソール アプリケーションを作成します。Iot Hub は、デバイスからクラウドへのメッセージを読み取るための、[Event Hubs][lnk-event-hubs-overview] と互換性のあるエンドポイントを公開します。わかりやすくするために、このチュートリアルで作成するリーダーは基本的なものであり、高スループットのデプロイメントには適していません。[デバイスからクラウドへのメッセージの処理][lnk-process-d2c-tutorial]に関するチュートリアルでは、デバイスからクラウドへのメッセージを規模を拡大して処理する方法を紹介しています。「[Event Hubs の使用][lnk-eventhubs-tutorial]」チュートリアルでは、Event Hubs からのメッセージを処理する方法について詳しく説明しています。また、このチュートリアルは IoT Hub のイベント ハブと互換性のあるエンドポイントに当てはまります。

> [AZURE.NOTE] 常に、Event Hubs と互換性のあるエンドポイントは、デバイスからクラウドへのメッセージを読み取るために AMQPS プロトコルを使用します。

1. **readdevicetocloudmessages** という名前の新しい空のフォルダーを作成します。コマンド プロンプトで次のコマンドを使用して、**readdevicetocloudmessages** フォルダー内に新しい package.json ファイルを作成します。次の既定値をすべてそのまま使用します。

    ```
    npm init
    ```

2. コマンド プロンプトで、**readdevicetocloudmessages** フォルダーに移動し、次のコマンドを実行して、**amqp10** と **bluebird** パッケージをインストールします。

    ```
    npm install amqp10 bluebird --save
    ```

3. テキスト エディターを使用して、**readdevicetocloudmessages** フォルダー内に新しい **ReadDeviceToCloudMessages.js** ファイルを作成します。

4. **ReadDeviceToCloudMessages.js** ファイルの先頭に、次の `require` ステートメントを追加します。

    ```
    'use strict';

    var AMQPClient = require('amqp10').Client;
    var Policy = require('amqp10').Policy;
    var translator = require('amqp10').translator;
    var Promise = require('bluebird');
    ```

5. プレース ホルダーを前にメモした値に置き換えて、次の変数宣言を追加します。**{your event hub-compatible namespace}** プレースホルダーの値は、ポータルの **[Event Hub-compatible endpoint]** (イベント ハブと互換性のあるエンドポイント) フィールドから取得され、**namespace.servicebus.windows.net** (**sb://* プレフィックスなし) の形式になります。

    ```
    var protocol = 'amqps';
    var eventHubHost = '{your event hub-compatible namespace}';
    var sasName = 'iothubowner';
    var sasKey = '{your iot hub key}';
    var eventHubName = '{your event hub-compatible name}';
    var numPartitions = 2;
    ```

    > [AZURE.NOTE] このコードでは、F1 (free) レベルで IoT hub を作成したと想定しています。無料の IoT Hub は、2 つのパーティションがあります。名前は「0」と「1」です。他の価格レベルのいずれかを使用して IoT Hub を作成した場合は、パーティションごとに **MessageReceiver** を作成するようにコードを調整する必要があります。

6. 次のフィルター定義を追加します。受信側が実行開始後、IoT Hub に送信されたメッセージのみを読み込むように、このアプリケーション メソッドは受信側の構築時にフィルターを使用します。現在の一連のメッセージを確認できるので、テスト環境ではこれは便利ですが、運用環境の場合、コードですべてのメッセージが処理されることを確認する必要があります。詳細については、[IoT Hub のデバイスからクラウドへのメッセージを処理する方法][lnk-process-d2c-tutorial]に関するチュートリアルを参照してください。

    ```
    var filterOffset = new Date().getTime();
    var filterOption;
    if (filterOffset) {
      filterOption = {
      attach: { source: { filter: {
      'apache.org:selector-filter:string': translator(
        ['described', ['symbol', 'apache.org:selector-filter:string'], ['string', "amqp.annotation.x-opt-enqueuedtimeutc > " + filterOffset + ""]])
        } } }
      };
    }
    ```

7. 次のコードを追加して、受信アドレスと AMQP クライアントを作成します。

    ```
    var uri = protocol + '://' + encodeURIComponent(sasName) + ':' + encodeURIComponent(sasKey) + '@' + eventHubHost;
    var recvAddr = eventHubName + '/ConsumerGroups/$default/Partitions/';
    
    var client = new AMQPClient(Policy.EventHub);
    ```

8. コンソールに出力する次の 2 つの関数を追加します。

    ```
    var messageHandler = function (partitionId, message) {
      console.log('Received(' + partitionId + '): ', message.body);
    };
    
    var errorHandler = function(partitionId, err) {
      console.warn('** Receive error: ', err);
    };
    ```

9. フィルターを使用して、特定のパーティションの受信側として機能する次の関数を追加します。

    ```
    var createPartitionReceiver = function(partitionId, receiveAddress, filterOption) {
      return client.createReceiver(receiveAddress, filterOption)
        .then(function (receiver) {
          console.log('Listening on partition: ' + partitionId);
          receiver.on('message', messageHandler.bind(null, partitionId));
          receiver.on('errorReceived', errorHandler.bind(null, partitionId));
        });
    };
    ```

10. 次のコードを追加して、イベント ハブと互換性のあるエンドポイントに接続し、受信側を起動します。

    ```
    client.connect(uri)
      .then(function () {
        var partitions = [];
        for (var i = 0; i < numPartitions; ++i) {
          partitions.push(createPartitionReceiver(i, recvAddr + i, filterOption));
        }
        return Promise.all(partitions);
    })
    .error(function (e) {
        console.warn('Connection error: ', e);
    });
    ```

11. **ReadDeviceToCloudMessages.js** ファイルを保存して閉じます。

## シミュレーション対象デバイス アプリの作成

このセクションでは、デバイスからクラウドへのメッセージを IoT Hub に送信するデバイスをシミュレートする Node.js コンソール アプリを作成します。

1. **simulateddevice** という名前の新しい空のフォルダーを作成します。コマンド プロンプトで次のコマンドを使用して、**simulateddevice** フォルダー内に新しい package.json ファイルを作成します。次の既定値をすべてそのまま使用します。

    ```
    npm init
    ```

2. コマンド プロンプトで、**simulateddevice** フォルダーに移動し、次のコマンドを実行して、**azure-iot-device-amqp** パッケージをインストールします。

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

5. **connectionString** 変数を追加し、それを使用してデバイス クライアントを作成します。**{youriothubname}** を IoT Hub 名に、**{yourdeviceid}** と **{yourdevicekey}** を「*デバイス ID の作成*」セクションで生成したデバイス値に置き換えます。

    ```
    var connectionString = 'HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    
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
            var data = JSON.stringify({ deviceId: 'mydevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 2000);
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

2. コマンド プロンプトで、**simulateddevice** フォルダー内で、次のコマンドを実行して IoT Hub へのテレメトリ データの送信を開始します。

    ```
    node SimulatedDevice.js
    ```

    ![][8]

3. [Azure ポータル][lnk-portal]の **[使用状況]** タイルには、Hub に送信されたメッセージ数が表示されます。

    ![][43]

## 次のステップ

このチュートリアルでは、ポータルで新しい IoT Hub を構成し、ハブの ID レジストリにデバイス ID を作成しました。シミュレーション対象デバイス アプリでデバイスからクラウドへのメッセージをハブに送信できるようにするために、このデバイス ID を使用しました。また、ハブで受け取ったメッセージを表示するアプリを作成しました。IoT Hub の機能や、他の IoT のシナリオを次のチュートリアルでさらに詳しく説明しています。

- 「[IoT Hub を使用したクラウドからデバイスへのメッセージの送信][lnk-c2d-tutorial]」には、デバイスにメッセージを送信し、IoT Hub によって生成される配信フィードバックを処理する方法が示されています。
- 「[デバイスからクラウドへのメッセージの処理][lnk-process-d2c-tutorial]」には、デバイスから送信されるテレメトリおよび対話型メッセージを確実に処理する方法が示されています。
- 「[デバイスからのファイルのアップロード][lnk-upload-tutorial]」では、デバイスからのファイル アップロードを容易にするためにクラウドからデバイスへのメッセージを活用したパターンについて説明しています。

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

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/doc/devbox_setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0601_2016-->