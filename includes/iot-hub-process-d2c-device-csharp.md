## シミュレーション済みデバイスからの対話型メッセージの送信

このセクションでは、デバイスからクラウドへの対話式メッセージが IoT Hub に送信されるように、シミュレーション済みデバイス アプリケーションを変更します。

1. Visual Studio の **SimulatedDevice** プロジェクトで、次のメソッドを **Program** クラスに追加します。
   
        private static async void SendDeviceToCloudInteractiveMessagesAsync()
        {
            while (true)
            {
                var interactiveMessageString = "Alert message!";
                var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
                interactiveMessage.Properties["messageType"] = "interactive";
                interactiveMessage.MessageId = Guid.NewGuid().ToString();

                await deviceClient.SendEventAsync(interactiveMessage);
                Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

                Thread.Sleep(10000);
            }
        }

    このメソッドは、「[IoT Hub の概要]」で作成された `SendDeviceToCloudMessagesAsync()` メソッドと非常に類似していますが、`MessageId` システム プロパティと `messageType` と呼ばれるユーザー プロパティがこのメソッドでは設定されていることだけが異なります。`MessageId` プロパティはグローバルに一意な ID (guid) に設定されており、これは受信するメッセージの重複除去に使用されます。`messageType` プロパティは、データ ポイント メッセージから対話式メッセージを識別するために使用されます。この情報は、メッセージ本文ではなく、メッセージ プロパティで渡されるため、バックエンドのイベント プロセッサでルーティングをするためだけにメッセージ全体を逆シリアル化する必要はありません。

> [AZURE.NOTE]対話型メッセージの重複除去に使用される `MessageId` が断続的なネットワーク通信 (またはその他の障害) 中にデバイスで作成されると、デバイスからの同じメッセージによって再送信が複数回発生する可能性があります。グリッドとは対照的に、セマンティック メッセージ ID (関連するメッセージ データ フィールドのハッシュ) を使用することもできます。

2. `Console.ReadLine()` 行の直前の **Main** メソッドに次のメソッドを追加します。

        SendDeviceToCloudInteractiveMessagesAsync();

> [AZURE.NOTE]わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。実稼働のコードでは、MSDN 記事 (一時的な障害処理) で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。

<!-- Links -->
[IoT Hub の概要]: iot-hub-csharp-csharp-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-getstarted-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-getstarted-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-getstarted-cloud-csharp/create-storage3.png

<!---HONumber=Oct15_HO3-->