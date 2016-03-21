## シミュレーション済みデバイスからの対話型メッセージの送信

このセクションでは、デバイスからクラウドへの対話式メッセージが IoT Hub に送信されるように、「[IoT Hub の概要]」チュートリアルで作成したシミュレーション済みデバイス アプリケーションを変更します。

1. Visual Studio の **SimulatedDevice** プロジェクトで、次のメソッドを **Program** クラスに追加します。

    ```
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
    ```

    このメソッドは **SimulatedDevice** プロジェクトの **SendDeviceToCloudMessagesAsync** メソッドに非常に似ています。唯一の違いは、**MessageId** システム プロパティと「**messageType**」という名前のユーザー プロパティを設定することです。このコードにより、グローバルに一意の識別子 (guid) が **MessageId** プロパティに割り当てられます。Service Bus でこれが利用され、受信したメッセージを重複除去できます。このサンプルでは **messageType** プロパティを利用し、データ ポイント メッセージから対話式メッセージを識別します。この情報は、メッセージ本文ではなく、メッセージ プロパティで渡されるため、イベント プロセッサでメッセージをルーティングするためにメッセージを逆シリアル化する必要はありません。

    > [AZURE.NOTE] 対話型メッセージの重複除去に使用する **MessageId** はデバイス コードで作成することが重要です。ネットワーク通信が途切れると、あるいはその他の障害に起因し、そのデバイスから同じメッセージが繰り返し再送信されることがあります。guid の代わりに、関連メッセージ データ フィールドのハッシュなど、セマンティック メッセージ id を利用することもできます。

2. `Console.ReadLine()` 行の直前の **Main** メソッドに次のメソッドを追加します。

    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````

    > [AZURE.NOTE] わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。運用環境のコードでは、MSDN の記事「[Transient Fault Handling (一時的な障害の処理)]」で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。

<!-- Links -->
[Transient Fault Handling (一時的な障害の処理)]: https://msdn.microsoft.com/library/hh675232.aspx
[IoT Hub の概要]: iot-hub-csharp-csharp-getstarted.md

<!---HONumber=AcomDC_0309_2016-->