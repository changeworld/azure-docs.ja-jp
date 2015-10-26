
1. [ソリューション] ビューで、Xamarin.Android アプリケーションの **Components** フォルダーを展開し、Azure Mobile Services パッケージがインストールされていることを確認します。 

2. **Components** フォルダーを右クリックし、[**その他のコンポーネントを取得...**] をクリックし、[**Google Cloud Messaging Client**] コンポーネントを検索し、それをプロジェクトに追加します。

1. ToDoActivity.cs プロジェクト ファイルを開き、次の using ステートメントをクラスに追加します。

		using Gcm.Client;

2. **ToDoActivity** クラスに、次の新しいメソッドを追加します。

        // Create a new instance field for this activity.
        static ToDoActivity instance = new ToDoActivity();

        // Return the current activity instance.
        public static ToDoActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }
        // Return the Mobile Services client.
        public MobileServiceClient CurrentClient
        {
            get
            {
                return client;
            }
        }

	これにより、サービス プロセスから Mobile Services クライアント インスタンスにアクセスできるようになります。

3. 既存の Mobile Services クライアント宣言を次のように public に変更します。

		public MobileServiceClient client { get; private set; }

4.	**MobileServiceClient** が作成された後で、次のコードを **OnCreate** メソッドに追加します。

        // Set the current instance of TodoActivity.
        instance = this;

        // Make sure the GCM client is set up correctly.
        GcmClient.CheckDevice(this);
        GcmClient.CheckManifest(this);

        // Register the app for push notifications.
        GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

これで、**ToDoActivity** は、プッシュ通知を追加するための準備が整いました。

<!---HONumber=Oct15_HO3-->