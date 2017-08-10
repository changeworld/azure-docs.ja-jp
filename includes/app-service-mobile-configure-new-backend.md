
1. **[App Services]** をクリックしてモバイル アプリ バックエンドを選択し、**[クイック スタート]** をクリックして目的のクライアント プラットフォーム (iOS、Android、Xamarin、Cordova) をクリックします。

![Mobile Apps のクイックスタートが強調表示された Azure ポータル][quickstart]

2. データベースとの接続が構成されていない場合は、データ接続を 1 つ作成する必要があります。

![Azure Portal (Mobile Apps をデータベースに接続)][connect]

  * 新しい SQL Database とサーバーを作成します。

  ![Azure Portal (Mobile Apps の新しいデータベースとサーバーを作成)][server]

  * データ接続が正常に作成されるまで待ちます。

  ![Azure Portal (Mobile Apps のデータ接続が作成されて通知が表示されたところ)][notification]

  * データ接続の作成に成功しました。

  ![Azure Portal (Mobile Apps のデータ接続が作成されて通知が表示されたところ)][already-connection]

3. **[2. テーブル API の作成]** で、**[バックエンド言語]** として Node.js を選択します。 確認要求をそのまま受け入れ、 **[TodoItem テーブルを作成する]**をクリックします。 新しい *TodoItem* テーブルがデータベースに作成されます。 既存のバックエンドを Node.js に切り替えると、すべてのコンテンツが上書きされるので注意してください。 代わりに .NET バックエンドを作成する場合は、[こちらの手順に従ってください][instructions]。

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
