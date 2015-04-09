ローカル モバイル アプリに対してクライアント アプリをテストした後、このチュートリアルの最終段階は、モバイル アプリのバックエンドを Azure に発行し、ライブ サービスに対してアプリを実行することです。

> [AZURE.NOTE] この手順は、Visual Studio のツールを使用して、モバイル アプリのバックエンドを発行する方法を示しています。ソース管理を使用して .NET のバックエンドを発行することもできます。

1. ソリューション エクスプローラーで、モバイル アプリのコード プロジェクト ("Service" が追加されたアプリ名) を右クリックし、**[発行]** をクリックします。 

	![アプリのコード プロジェクトの発行の選択](./media/app-service-mobile-dotnet-backend-publish-service-preview/dotnet-project-publish.png)

2. **[Web の発行]** ダイアログ ボックスで、発行先として **[Azure モバイル アプリ]** を選択します。表示されたダイアログ ボックスで、既存のモバイル アプリ （"code" が付いたモバイル アプリの名前） を選択します。

    ![に発行する既存の Ｗeb アプリの選択](./media/app-service-mobile-dotnet-backend-publish-service-preview/mobile-quickstart-publish-select-service.png)

3. **[接続の検証]** をクリックして、発行が正しく構成されていることを確認した後、**[発行]** をクリックします。

	![発行設定ウィザードの最後のページ](./media/app-service-mobile-dotnet-backend-publish-service-preview/dotnet-publish-settings.png)

   発行が成功した後、モバイル アプリのバックエンドが Azure で実行中であることを示す確認ページが表示されます。Visual Studio の出力ウィンドウにも成功が表示されます。

<!--HONumber=49-->