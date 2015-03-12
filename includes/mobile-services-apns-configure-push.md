アプリケーションを APNS に登録し、プロジェクトを構成した後で、モバイル サービスを APNS と統合するように構成する必要があります。

1. Keychain Access で、**[鍵]** または **[自分の証明書]** で新しい証明書を右クリックし、**[エクスポート]** をクリックします。ファイルの名前を [QuickstartPusher] にして **[.p12]** 形式を選択し、**[保存]** をクリックします。

   	![](./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png)

  エクスポートした証明書のファイル名と場所を書き留めます。

>[AZURE.NOTE] このチュートリアルでは QuickstartPusher.p12 ファイルを作成します。ファイル名と場所は同じである必要はありません。

2. [Azure 管理ポータル] にログオンし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

   	![](./media/mobile-services-apns-configure-push/mobile-services-selection.png)

3. **[プッシュ]** タブをクリックし、**[アップロード]** をクリックします。

   	![](./media/mobile-services-apns-configure-push/mobile-push-tab-ios.png)

	[証明書のアップロード] ダイアログ ボックスが表示されます。

4. **[ファイル]** をクリックし、エクスポートした証明書ファイル QuickstartPusher.p12 を選択します。**[パスワード]** にパスワードを入力し、正しい **[モード]** (Dev/Sandbox または Prod/Production) が選択されていることを確認して **[保存]** をクリックします。

   	![](./media/mobile-services-apns-configure-push/mobile-push-tab-ios-upload.png)

    > [AZURE.NOTE] このチュートリアルでは開発証明書を使用します。

APNS と連携するようにモバイル サービスが構成されました。

<!-- URLs. -->
[Azure 管理ポータル]: https://manage.windowsazure.com/
<!--HONumber=42-->
