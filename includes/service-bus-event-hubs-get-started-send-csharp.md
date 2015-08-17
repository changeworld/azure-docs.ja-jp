## Event Hub へのメッセージ送信

このセクションでは、Windows コンソール アプリを記述して、Event Hub にイベントを送信します。

1. Visual Studio で、**コンソール アプリケーション** プロジェクト テンプレートを使用して、新しい Visual C# のデスクトップ アプリ プロジェクトを作成します。プロジェクトの名前として「**Sender**」と入力します。

   	![][7]

2. ソリューション エクスプローラーでソリューションを右クリックし、**[ソリューション用 NuGet パッケージの管理]** をクリックします。

	[NuGet パッケージの管理] ダイアログ ボックスが表示されます。

3. `Microsoft Azure Service Bus` を検索し、**[インストール]** をクリックして、使用条件に同意します。

	![][8]

	これによりパッケージのダウンロードとインストールが実行され、<a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus/">Azure Service Bus ライブラリ NuGet パッケージ</a>への参照が追加されます。

4. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

		using System.Threading;
		using Microsoft.ServiceBus.Messaging;

5. **Program** クラスに次のフィールドを追加し、**send** 権限を使用して、前のセクションで作成した Event Hub の名前と接続文字列を値に代入します。

		static string eventHubName = "{event hub name}";
        static string connectionString = "{send connection string}";

6. **Program** クラスに次のメソッドを追加します。

		static void SendingRandomMessages()
        {
            var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
            while (true)
            {
                try
                {
                    var message = Guid.NewGuid().ToString();
                    Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                    eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
                }
                catch (Exception exception)
                {
                    Console.ForegroundColor = ConsoleColor.Red;
                    Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                    Console.ResetColor();
                }

                Thread.Sleep(200);
            }
        }

	このメソッドは、200 ミリ秒の遅延時間で Event Hub にイベントを継続的に送信します。

7. 最後に、**Main** メソッドに次の行を追加します。

		Console.WriteLine("Press Ctrl-C to stop the sender process");
        Console.WriteLine("Press Enter to start now");
        Console.ReadLine();
        SendingRandomMessages();


<!-- Images -->
[7]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp2.png

<!---HONumber=August15_HO6-->