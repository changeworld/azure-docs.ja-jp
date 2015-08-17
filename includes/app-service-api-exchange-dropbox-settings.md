4. 別のブラウザー ウィンドウまたはタブで、[Azure プレビュー ポータル](https://portal.azure.com)に移動します。

3. Dropbox コネクタの **[API アプリ]** ブレードに移動します (まだ **[リソース グループ]** ブレードが表示されている場合は、図の Dropbox コネクタをクリックするだけです)。

4. **[設定]** をクリックし、**[設定]** ブレードの **[認証]** をクリックします。

	![[設定] のクリック](./media/app-service-api-exchange-dropbox-settings/clicksettings.png)

	![[認証] のクリック](./media/app-service-api-exchange-dropbox-settings/clickauth.png)

5. [認証] ブレードで、Dropbox サイトのクライアント ID とクライアント シークレットを入力し、**[保存]** をクリックします。

	![設定の入力と [保存] のクリック](./media/app-service-api-exchange-dropbox-settings/authblade.png)

3. **リダイレクト URI** (クライアント ID とクライアント シークレットの上にある灰色のボックス) の値をコピーして、前の手順で開いたままにしていたページにこの値を追加します。

	リダイレクト URI のパターンは次のとおりです。

		[gatewayurl]/api/consent/redirect/[connectorname]

	次に例を示します。

		https://dropboxrgaeb4ae60b7.azurewebsites.net/api/consent/redirect/DropboxConnector

	![リダイレクト URI の取得](./media/app-service-api-exchange-dropbox-settings/redirecturi.png)

	![Dropbox アプリの作成](./media/app-service-api-exchange-dropbox-settings/dbappsettings2.png)

<!---HONumber=August15_HO6-->