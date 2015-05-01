

新しい Mobile Services を作成するには、次のステップに従います。

1.	[管理ポータル]にログインします。ナビゲーション ウィンドウの下部にある **[新規]** をクリックします。**[コンピューティング]**、**[Mobile Services]** の順に展開し、**[作成]** をクリックします。
	![](./media/mobile-services-dotnet-backend-create-new-service/mobile-create.png)

	**[Mobile Services の作成]** ダイアログ ボックスが表示されます。

2.	**[Mobile Services の作成]** ページで、**[無料の 20 MB SQL データベースの作成]** を選択し、**[.NET]** ランタイムを選択してから、**[URL]** テキストボックスに新しい Mobile Services のサブドメイン名を入力します。右矢印ボタンをクリックして、次のページに進みます。
	![](./media/mobile-services-dotnet-backend-create-new-service/mobile-create-page1.png)

**[データベースの設定の指定]** ページが表示されます。

> [AZURE.NOTE] このチュートリアルの一環として、新しい SQL Database インスタンスと SQL Database サーバーを作成します。この新しいデータベースは、再利用したり、その他の SQL データベース インスタンスと同様に管理したりすることができます。新しい Mobile Services と同じリージョンにデータベースを既に所有している場合は、代わりに **[既存のデータベースを使用する]** を選択してそのデータベースを選択できます。別のリージョンにあるデータベースを使用することは、帯域幅コストと待機時間が増加するため、お勧めしません。

3.	**[名前]** ボックスに新しいデータベースの名前を入力し、**[ログイン名]** ボックスに、新しい SQL データベース サーバーの管理者ログイン名を入力します。パスワードを入力および確認入力し、チェック ボタンをクリックして処理を完了します。
	![](./media/mobile-services-dotnet-backend-create-new-service/mobile-create-page2.png)

これで、モバイル アプリケーションで使用できる新しい Mobile Services が作成されました。

<!-- URLs. -->
[管理ポータル]: https://manage.windowsazure.com/

<!--HONumber=52--> 
