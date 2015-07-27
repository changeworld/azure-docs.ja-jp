
1. Visual Studio のソリューション エクスプローラーでサービス プロジェクトを右クリックし、**[デバッグ]** コンテキスト メニューの **[新しいインスタンスの開始]** をクリックします。

    Visual Studio にサービスの既定の Web ページが表示されます。既定では、Visual Studio は、モバイル アプリ バックエンドを IIS Express でローカルにホストします。

2. Windows タスクバーにある IIS Express のトレイ アイコンを右クリックして、モバイル アプリ バックエンドが開始していることを確認します。

	 ![タスクバーにあるモバイル サービスを確認する](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/iis-express-tray.png)

3. .NET バックエンドの開始ページで、**[実際に使ってみる]** をクリックします。

    API ドキュメントのページが表示されます。このページを使用して Mobile App をテストします。

	>[AZURE.NOTE]ローカルで実行する場合、このページにアクセスするための認証は不要です。Azure で実行する場合、このページにアクセスするには、(ユーザー名なしで) パスワードとしてアプリケーション キーを指定する必要があります。

4. **[テーブル/TodoItem の取得]** のリンクをクリックします。

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-api-documentation-page.png)
   	
	これにより、API の GET 応答ページが表示されます。

5. **[実際に使ってみる]**、**[送信]** の順にクリックします。
 
	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-todoitems.png)

	TodoItem テーブルのすべての行を返す GET 要求がローカル モバイル アプリ バックエンドに送信されます。テーブルが初期化子によってシードされているため、応答メッセージの本体に 2 つの TodoItem オブジェクトが返されます。初期化子についての詳細は、「[.NET バックエンド モバイル サービスに対してデータ モデルを変更する方法](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)」を参照してください。

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-response.png)

<!---HONumber=July15_HO3-->