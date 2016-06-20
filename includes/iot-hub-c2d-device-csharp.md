## シミュレーション済みデバイスでのメッセージの受信

このセクションでは、「IoT Hub の概要」で作成したシミュレーション済みデバイス アプリケーションを変更して、クラウドからデバイスへのメッセージを IoT Hub から受信します。

1. Visual Studio の **SimulatedDevice** プロジェクトで、次のメソッドを **Program** クラスに追加します。

        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();

                await deviceClient.CompleteAsync(receivedMessage);
            }
        }

    `ReceiveAsync` メソッドは、受信したメッセージを、そのメッセージがデバイスによって受信されたとき非同期で返します。指定可能なタイムアウト期間が経過したら、*null* が返されます (ここでは、既定の 1 分が使用されます)。これが生じると、新しいメッセージを待機し続けるためのコードが必要になります。このため、`if (receivedMessage == null) continue` 行が必要になります。

    `CompleteAsync()` への呼び出しにより、メッセージが正常に処理されたことが IoT Hub に伝えられます。メッセージはデバイスのキューから安全に削除することができます。デバイスのアプリケーションがメッセージの処理を完了できなくなる何らかの事態が生じると、IoT Hub はそれをもう一度送信します。デバイス アプリケーション内のメッセージ処理ロジックが*べき等*であることが重要です。つまり、複数回受信しても、同じメッセージであれば同じ結果が生成されます。アプリケーションはメッセージを一時的に破棄することもできます。この場合、IoT Hub は将来の使用に備えてメッセージをキュー内に保持します。または、メッセージを拒否することもできます。この場合、メッセージはキューから永久に削除されます。クラウドからデバイスへのメッセージのライフサイクルの詳細については、「[Azure IoT Hub 開発者ガイド][IoT Hub Developer Guide - C2D]」をご覧ください。

> [AZURE.NOTE] トランスポートとして AMQP ではなく HTTP/1 を使用している場合、`ReceiveAsync` メソッドは即時に返されます。HTTP/1 を使用するクラウドからデバイスへのメッセージに関してサポートされているパターンは、メッセージを低頻度 (25 分未満の間隔) でチェックするデバイスに断続的に接続されます。HTTP/1 受信の発行が多くなれば、IoT Hub で要求がスロットルされます。AMQP と HTTP/1 サポートの相違点と、IoT Hub スロットルの詳細については、「[Azure IoT Hub開発者ガイド][IoT Hub Developer Guide - C2D]」をご覧ください。

2. `Console.ReadLine()` 行の直前の **Main** メソッドに次のメソッドを追加します。

        ReceiveC2dAsync();

> [AZURE.NOTE] わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。運用環境のコードでは、MSDN の記事「Transient Fault Handling (一時的な障害の処理)」で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。

<!-- Links -->
[IoT Hub Developer Guide - C2D]: ../articles/iot-hub/iot-hub-devguide.md#c2d

<!-- Images -->

<!---HONumber=AcomDC_0608_2016-->