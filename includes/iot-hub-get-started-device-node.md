## シミュレーション対象デバイス アプリの作成
このセクションでは、デバイスからクラウドへのメッセージを IoT Hub に送信するデバイスをシミュレートする Node.js コンソール アプリを作成します。

1. 「 **simulateddevice**」という名前の空のフォルダーを新規作成します。**simulateddevice** フォルダー内に、コマンド プロンプトで次のコマンドを使用して新しい package.json ファイルを作成します。次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. **simulateddevice** フォルダーで、コマンド プロンプトで次のコマンドを実行し、**azure-iot-device-amqp** パッケージをインストールします。
   
    ```
    npm install azure-iot-device-amqp --save
    ```
3. テキスト エディターを使用して、**simulateddevice** ディレクトリに **SimulatedDevice.js** という名前の新しいファイルを作成します。
4. **SimulatedDevice.js** ファイルの開始時に、次の `require` ステートメントを追加します。
   
    ```
    'use strict';
   
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```
5. **connectionString** 変数を追加し、それを使用してデバイス クライアントを作成します。**{youriothubname}** を IoT Hub 名に、**{yourdeviceid}** と **{yourdevicekey}** を「*デバイス ID の作成*」セクションで生成されたデバイス値に置換します。
   
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
7. コールバックを作成し、**setInterval** 関数を使用して、1 秒ごとに新しいメッセージを IoT Hub に送信します。
   
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

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。運用環境のコードでは、[一時的な障害処理][lnk-transient-faults]に関する MSDN の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
> 
> 

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

<!---HONumber=AcomDC_0309_2016-->