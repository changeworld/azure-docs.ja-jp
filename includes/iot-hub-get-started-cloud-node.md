## デバイス ID の作成

このセクションでは、IoT Hub の ID レジストリに新しいデバイス ID を作成する Node.js コンソール アプリケーションを作成します。IoT Hub に接続するデバイスは、あらかじめデバイス ID レジストリに登録されている必要があります。詳細については、「[IoT Hub 開発者ガイド][lnk-devguide-identity]」の「**デバイス ID レジストリ**」を参照してください。このコンソール アプリケーションを実行すると、デバイスからクラウドへのメッセージを IoT Hub に送信するときにそのデバイスを識別する一意の ID とキーが生成されます。

1. 「**createdeviceidentity**」という名前の空のフォルダーを新規作成します。**createdeviceidentity** フォルダー内に、コマンド プロンプトで次のコマンドを使用して新しい package.json ファイルを作成します。次の既定値をすべてそのまま使用します。

    ```
    npm init
    ```

2. **createdeviceidentity** フォルダーで、コマンド プロンプトで次のコマンドを実行し、**azure iothub** パッケージをインストールします。

    ```
    npm install azure-iothub --save
    ```

3. テキスト エディターを使用して、新しい **CreateDeviceIdentity.js** ファイルを **createdeviceidentity** フォルダー内に作成します。

4. **CreateDeviceIdentity.js** ファイルの開始時に、次の `require` ステートメントを追加します。

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

8. **createdeviceidentity** アプリケーションを実行するには、createdeviceidentity フォルダーで、コマンド プロンプトで次のコマンドを実行します。

    ```
    node CreateDeviceIdentity.js 
    ```

9. **デバイス ID** と**デバイス キー**をメモします。後でデバイスとして IoT Hub に接続するアプリケーションを作成するときに必要になります。

> [AZURE.NOTE] IoT Hub の ID レジストリには、ハブに対するアクセスの安全性を確保するためのデバイス ID だけが格納されます。セキュリティ資格情報として使用するキーとデバイス ID、そして個々のデバイスについてアクセスを無効にすることのできる有効/無効フラグが格納されます。その他デバイス固有のメタデータをアプリケーションで保存する必要がある場合は、アプリケーション固有のストアを使用する必要があります。詳細については、「[IoT Hub 開発者ガイド][lnk-devguide-identity]」を参照してください。

## デバイスからクラウドへのメッセージの受信

このセクションでは、IoT Hub からのデバイスからクラウドへのメッセージを読み込む Node.js コンソール アプリケーションを作成します。Iot Hub は、デバイスからクラウドへのメッセージを読み取るための、[Event Hubs][lnk-event-hubs-overview] と互換性のあるエンドポイントを公開します。わかりやすくするために、このチュートリアルで作成するリーダーは基本的なものであり、高スループットのデプロイメントには適していません。「[デバイスからクラウドへのメッセージの処理][lnk-processd2c-tutorial]」チュートリアルでは、デバイスからクラウドへの大規模なメッセージを処理する方法を紹介しています。「[Event Hubs の使用][lnk-eventhubs-tutorial]」チュートリアルでは、Event Hubs からのメッセージを処理する方法について詳しく説明しています。これは IoT Hub のイベント ハブと互換性のあるエンドポイントに適用されます。

> [AZURE.NOTE] 常に、Event Hubs と互換性のあるエンドポイントは、デバイスからクラウドへのメッセージを読み取るために AMQPS プロトコルを使用します。

1. **readdevicetocloudmessages** という名前の空のフォルダーを新規作成します。**readdevicetocloudmessages** フォルダー内に、コマンド プロンプトで次のコマンドを使用して、新しい package.json ファイルを作成します。次の既定値をすべてそのまま使用します。

    ```
    npm init
    ```

2. **readdevicetocloudmessages** フォルダーで、コマンド プロンプトで次のコマンドを実行し、**amqp10** と **bluebird** パッケージをインストールします。

    ```
    npm install amqp10 bluebird --save
    ```

3. テキスト エディターを使用して、新しい **ReadDeviceToCloudMessages.js** ファイルを **readdevicetocloudmessages** フォルダー内に作成します。

4. **ReadDeviceToCloudMessages.js** ファイルの開始時に、次の `require` ステートメントを追加します。

    ```
    'use strict';

    var AMQPClient = require('amqp10').Client;
    var Policy = require('amqp10').Policy;
    var translator = require('amqp10').translator;
    var Promise = require('bluebird');
    ```

5. プレース ホルダーを前にメモした値に置き換えて、次の変数宣言を追加します。**{your event hub-compatible namespace}** プレースホルダーの値は、ポータルの **Event Hub-compatible endpoint** フィールドから取得され、**namespace.servicebus.windows.net** (**sb://* プレフィックスなし) の形式になります。

    ```
    var protocol = 'amqps';
    var eventHubHost = '{your event hub-compatible namespace}';
    var sasName = 'iothubowner';
    var sasKey = '{your iot hub key}';
    var eventHubName = '{your event hub-compatible name}';
    var numPartitions = 2;
    ```

    > [AZURE.NOTE] このコードでは、F1 (free) レベルで IoT hub を作成したと想定しています。無料の IoT Hub は、2 つのパーティションがあります。名前は「0」と「1」です。他の価格レベルのいずれかを使用して IoT Hub を作成した場合、コードを調整し、パーティションごとに **MessageReceiver** を作成する必要があります。

6. 次のフィルター定義を追加します。受信側が実行開始後、IoT Hub に送信されたメッセージのみを読み込むように、このアプリケーション メソッドは受信側の構築時にフィルターを使用します。現在の一連のメッセージを確認できるので、テスト環境ではこれは便利ですが、運用環境の場合、コードですべてのメッセージが処理されることを確認する必要があります。詳細については、「[IoT Hub のデバイスからクラウドへのメッセージを処理する方法][lnk-processd2c-tutorial]」チュートリアルを参照してください。

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

<!-- Links -->

[lnk-eventhubs-tutorial]: ../articles/event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../articles/event-hubs/event-hubs-overview.md
[lnk-processd2c-tutorial]: ../articles/iot-hub/iot-hub-csharp-csharp-process-d2c.md

<!---HONumber=AcomDC_0413_2016-->