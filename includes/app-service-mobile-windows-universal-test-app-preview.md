
Visual Studio に戻り、共有コード クライアント アプリ プロジェクトを選択します ("<アプリ名>.Shared" のような名前になります）。

1. **App.xaml** ファイルを展開し、**App.xaml.cs** ファイルを開きます。localhost URL で初期化される  `MobileService` メンバー宣言を探します。この宣言を （ `CTRL+K,CTRL+C` を押して) コメントアウトし、ホステッド サービスに接続する宣言を (Ctrl + K、Ctrl + U を押して) コメント解除します。

        // This MobileServiceClient has been configured to communicate with your local
        // test project for debugging purposes.
        //public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "http://localhost:58454"
        //);

        // This MobileServiceClient has been configured to communicate with the Azure Mobile Service and
        // Azure Gateway using the application key. You're all set to start working with your Mobile Service!
        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://mymobileapp-code.azurewebsites.net",
            "https://myresourcegroupgateway.azurewebsites.net/Microsoft.Azure.AppService.ApiApps.Gateway",
            "XXXX-APPLICATION-KEY-XXXXX"
        );

2. F5 キーを押してプロジェクトをリビルドし、Windows ストア アプリを開始します。これが既定のスタートアップ プロジェクトになります。

2. アプリで、**[ToDo 項目を挿入する]** テキストボックスに意味のあるテキスト (たとえば、「*Complete the tutorial*」) を入力し、**[保存]** をクリックします。

	![](./media/app-service-mobile-windows-universal-test-app-preview/mobile-quickstart-startup.png)

	これで、Azure でホストされる新しいモバイル アプリのバックエンドに POST 要求が送信されます。

3. デバッグを停止し、Windows Phone ストア アプリにユニバーサル Windows ソリューション内の既定のスタートアップ プロジェクトを Windows Phone ストア アプリに変更し (`<アプリ名>.WindowsPhone` プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします)、もう一度 F5 キーを押します。

	![](./media/app-service-mobile-windows-universal-test-app-preview/mobile-quickstart-completed-wp8.png)

	Windows アプリの開始後に、前の手順で保存したデータがモバイル アプリから読み込まれることに注目してください。


<!--HONumber=49-->