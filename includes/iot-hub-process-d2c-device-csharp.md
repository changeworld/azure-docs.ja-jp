## シミュレーション済みデバイスからの対話型メッセージの送信
このセクションでは、[IoT Hub の使用方法]のチュートリアルで作成したシミュレーション済みデバイス アプリケーションを変更して、デバイスからクラウドへの対話型メッセージが IoT Hub に送信されるようにします。

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
   
        Task.Delay(10000).Wait();
      }
    }
    ```
   
    このメソッドは **SimulatedDevice** プロジェクトの **SendDeviceToCloudMessagesAsync** メソッドに非常に似ています。唯一の違いは、**MessageId** システム プロパティと「**messageType**」という名前のユーザー プロパティを設定することです。コードにより、グローバル一意識別子 (GUID) が **MessageId** プロパティに割り当てられます。Service Bus はこれを使用して、受信したメッセージを重複除去できます。このサンプルでは **messageType** プロパティを利用し、データ ポイント メッセージから対話式メッセージを識別します。この情報は、メッセージ本文ではなく、メッセージ プロパティで渡されるため、イベント プロセッサでメッセージをルーティングするためにメッセージを逆シリアル化する必要はありません。
   
   > [!NOTE]
   > 対話型メッセージの重複除去に使用する **MessageId** はデバイス コードで作成することが重要です。ネットワーク通信の切断や、その他の障害により、そのデバイスから同じメッセージが繰り返し再送信されることがあります。GUID の代わりに、関連メッセージ データ フィールドのハッシュなど、セマンティック メッセージ ID を利用することもできます。
   > 
   > 
2. **Main** メソッドの `Console.ReadLine()` 行の直前に次のメソッドを追加します。
   
    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````
   
   > [!NOTE]
   > わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。運用環境のコードでは、MSDN の記事「[Transient Fault Handling (一時的な障害の処理)]」で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
   > 
   > 

<!-- Links -->
[Transient Fault Handling (一時的な障害の処理)]: https://msdn.microsoft.com/library/hh675232.aspx
[IoT Hub の使用方法]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md

<!---HONumber=AcomDC_0608_2016-->