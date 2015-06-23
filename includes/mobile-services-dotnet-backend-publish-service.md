

ローカルのモバイル サービスと組み合わせてクライアント アプリケーションをテストした後、このチュートリアルの最終段階でモバイル サービスを Azure に発行し、ライブ サービスと組み合わせてアプリケーションを実行します。

>[AZURE.NOTE]この手順は、Visual Studio ツールを使用して、モバイル サービスを発行する方法を示しています。また、ソース管理を使用して、.NET バックエンド モバイル サービスを発行することもできます。詳細については、「[ソース管理へのプロジェクト コードの保存](../articles/mobile-services/mobile-services-dotnet-backend-store-code-source-control.md)」をご覧ください

1. ソリューション エクスプローラーで、モバイル サービス プロジェクトを右クリックし、**[発行]** をクリックします。その後、**[Web の発行]** ダイアログ ボックスで **[Azure Mobile Services]** をクリックします。

2. Azure アカウント資格情報でサインインし、**[既存の Mobile Services]** から目的のサービスを選択して、**[OK]** をクリックします。Visual Studio によって、Azure から直接、発行設定がダウンロードされます。

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-select-service.png)

3. **[接続の検証]** をクリックして、発行が正しく構成されていることを確認した後、**[発行]** をクリックします。

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-2.png)

	発行に成功した後、確認ページが再び表示されますが、今度はモバイル サービスが Azure 内で実行されていることが示されます。

<!--HONumber=54-->