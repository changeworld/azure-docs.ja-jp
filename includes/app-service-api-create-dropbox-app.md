次の手順は、Dropbox.com サイトを使用して Dropbox アプリを作成するプロセスを示しています。Dropbox.com サイトは通知なしで変更される可能性があるため、実際の UI と異なる場合があります。

1. [Dropbox の開発者ポータル](https://www.dropbox.com/developers/apps)に移動し、**[App Console]**、**[Create App]** の順にクリックします。

	![Dropbox アプリの作成](./media/app-service-api-create-dropbox-app/dbappcreate.png)

2. **[Dropbox API app]** を選択し、その他の設定を構成します。
 
	アカウント内にファイルがある場合は、次のスクリーン ショットに示すファイル アクセスのオプションによって、単純な HTTP Get 要求で Dropbox アカウントへのアクセスをテストすることができます。

	Dropbox API アプリの名前は、Dropbox サイトで許可されている内容にします。

3. **[Create app]** をクリックします。

	![Dropbox アプリの作成](./media/app-service-api-create-dropbox-app/dbapiapp.png)

	次のページには、アプリ キーとアプリ シークレット設定 (Azure で名前を付けたクライアント ID とクライアント シークレット) が表示されます。これらは Azure Dropbox コネクタを構成するために使用します。

	このページにはリダイレクト URI を入力するフィールドもあります。その値は次のセクションで取得します。

	![Dropbox アプリの作成](./media/app-service-api-create-dropbox-app/dbappsettings.png)

<!---HONumber=August15_HO6-->