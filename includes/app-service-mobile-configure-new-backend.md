
1. をクリックして、 **App Services**ボタン、モバイル アプリ バックエンドを選択し、選択**クイック スタート**、クライアント プラットフォーム (iOS、Android、Xamarin、Cordova) を選択します。

    ![強調表示されているモバイル アプリのクイック スタートを使用して azure ポータル][quickstart]

2. データベース接続が構成されていない場合、次の手順で作成します。

    ![データベースにモバイル アプリの接続での azure ポータル][connect]

    a. 新しい SQL データベースとサーバーを作成します。

    ![モバイル アプリを使用して azure ポータルの新しいデータベースとサーバーを作成します。][server]

    b. データ接続が正常に作成されるまで待ちます。

    ![データ接続が正常に作成の azure ポータルの通知][notification]

    c. データ接続の作成に成功しました。

    ![Azure ポータルの通知、「接続が既にある、データ」][already-connection]

3.  **2 です。API のテーブルを作成**、選択の Node.js**バックエンド言語**します。 
 
4. クリックして、受信確認を受け入れる**作成 TodoItem テーブル**です。  
    この操作では、データベースに新しい作業項目テーブルが作成されます。 

    >[!IMPORTANT]
    > Node.js に既存のバックエンドを切り替えるには、すべてのコンテンツが上書きされます。 代わりに、.NET バックエンドを作成するを参照してください。[モバイル アプリの .NET バックエンド サーバー SDK と連携][instructions]です。

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
