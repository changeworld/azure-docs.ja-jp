
1. [Azure の管理ポータル]にログインし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

	![](./media/mobile-services-create-new-push-table/mobile-services-selection.png)

2. **[データ]** タブをクリックし、**[作成]** をクリックします。

	![](./media/mobile-services-create-new-push-table/mobile-create-table.png)

	**[新しいテーブルの作成]** ダイアログ ボックスが表示されます。

3. すべてのアクセス許可について既定の **[アプリケーション キーを持つユーザー]** 設定をそのままにし、**[テーブル名]** に「Registrations」と入力してチェック ボタンをクリックします。

	![](./media/mobile-services-create-new-push-table/mobile-create-registrations-table.png)

  **Registrations** テーブルが作成されます。このテーブルには、プッシュ通知を送信するために使用されるチャネル URI が格納されます。

次に、プッシュ通知を有効にするようにアプリケーションを変更します。

<!-- URLs -->
[Azure の管理ポータル]: https://manage.windowsazure.com/

