ローカルのモバイル サービスと組み合わせてクライアント アプリケーションをテストした後、このチュートリアルの最終段階でモバイル サービスを Azure に発行し、ライブ サービスと組み合わせてアプリケーションを実行します。

1.  ソリューション エクスプローラーで、モバイル サービス プロジェクトを右クリックし、**[発行]** をクリックします。その後、**[Web の発行]** ダイアログ ボックスで **[Azure Mobile Services]** をクリックします。

    ![][]

2.  Azure アカウント資格情報でサインインし、**[既存の Mobile Services]** から目的のサービスを選択して、**[OK]** をクリックします。

    ![][1]

    Visual Studio によって、Azure から直接、発行設定がダウンロードされます。

    > [WACOM.NOTE] Visual Studio は、明示的にサインアウトするまで Azure 資格情報を格納します。

3.  **[接続の検証]** をクリックして、発行が正しく構成されていることを確認した後、**[発行]** をクリックします。

    ![][2]

    発行に成功した後、確認ページが再び表示されますが、今度はモバイル サービスが Azure 内で実行されていることが示されます。

  []: ./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish.png
  [1]: ./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-select-service.png
  [2]: ./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-2.png
