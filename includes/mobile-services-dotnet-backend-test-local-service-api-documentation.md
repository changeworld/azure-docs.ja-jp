
1. Visual Studio のソリューション エクスプローラーで、サービス プロジェクトを右クリックして、**[デバッグ]** コンテンツ メニューの **[新しいインスタンスの開始]** をクリックします。

    ![モバイル サービス プロジェクトをローカルに開始する](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/vs-start-debug-service-project.png)

    Visual Studio では、サービスの既定の Web ページが表示されます。既定では、Visual Studio によって、モバイル サービスは IIS Express にローカルでホストされます。

2. Windows タスクバーにある IIS Express のトレイ アイコンを右クリックして、モバイル サービスが開始していることを確認します。

	 ![タスクバーにあるモバイル サービスを確認する](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/iis-express-tray.png)

3. .NET バックエンドの開始ページで、**[実際に使ってみる]** をクリックします。

    ![[モバイル サービスの開始] ページ](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-welcome-page.png)

    API ドキュメントのページが表示されます。このページを使用してモバイル サービスをテストします。

	>[AZURE.NOTE]ローカルで実行する場合、このページにアクセスするための認証は不要です。Azure で実行する場合、このページにアクセスするには、(ユーザー名なしで) パスワードとしてアプリケーション キーを指定する必要があります。

4. **[テーブル/TodoItem の取得]** のリンクをクリックします。

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-api-documentation-page.png)
   	
	これにより、API の GET 応答ページが表示されます。

5. **[実際に使ってみる]**、**[送信]** の順にクリックします。
 
	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-todoitems.png)

	TodoItem テーブルのすべての行を返すローカル モバイル サービスに対して GET 要求を送信します。テーブルが初期化子によってシードされているため、応答メッセージの本体に 2 つの TodoItem オブジェクトが返されます。初期化子についての詳細は、「[.NET バックエンド モバイル サービスに対してデータ モデルを変更する方法](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)」を参照してください。

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-response.png)

<!---HONumber=Oct15_HO3-->