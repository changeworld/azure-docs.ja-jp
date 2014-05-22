

ローカルのモバイル サービスと組み合わせて Windows ストア アプリをテストした後、このチュートリアルの最終段階でモバイル サービスを Azure に発行し、ライブ サービスと組み合わせてアプリを実行します。

1. ソリューション エクスプローラーで、モバイル サービス プロジェクトを右クリックし、**[発行]** をクリックします。

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish.png)

	[Web の発行] ダイアログ ボックスが表示されます。

2. **[インポート]**、**[参照]** の順にクリックし、以前に発行プロファイル ファイルを保存した場所に移動して、発行プロファイル ファイルを選択し、**[OK]** をクリックします。

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-import-profile.png)

	この結果、モバイル サービスを Azure に発行するために Visual Studio が必要とする情報が読み込まれます。
	
	<div class="dev-callout"><strong>セキュリティ上の注意</strong><p>ダウンロードしたファイルに含まれている情報を使用して他のユーザーがお客様のサービスにアクセスすることも可能であるため、発行プロファイルをインポートした後、ダウンロードしたファイルを削除することを検討してください。</p></div>

3. **[接続の検証]** をクリックして、発行が正しく構成されていることを確認した後、**[発行]** をクリックします。

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-2.png)

	発行に成功した後、確認ページが再び表示されますが、今度はモバイル サービスが Azure 内で実行されていることが示されます。


