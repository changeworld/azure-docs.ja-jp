アプリケーションを APNS に登録し、プロジェクトを構成した後で、モバイル サービスを APNS と統合するように構成する必要があります。

1.  Keychain Access で、新しい証明書を右クリックし、**[書き出す]** をクリックします。ファイルの名前を「QuickstartPusher」にして **[.p12]** 形式を選択し、**[保存]** をクリックします。

    ![][]

エクスポートした証明書のファイル名と場所を書き留めます。

> [WACOM.NOTE] このチュートリアルでは QuickstartPusher.p12 ファイルを作成します。ファイル名と場所は同じである必要はありません。

1.  [Azure の管理ポータル][]にログオンし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

    ![][1]

2.  **[プッシュ]** タブをクリックし、**[アップロード]** をクリックします。

    ![][2]

    [証明書のアップロード] ダイアログ ボックスが表示されます。

3.  **[ファイル]** をクリックし、エクスポートした証明書ファイル [QuickstartPusher.p12] を選択します。**[パスワード]** を入力し、正しい **[モード]** が選択されていることを確認して **[保存]** をクリックします。

    ![][3]

    > [WACOM.NOTE] このチュートリアルでは開発証明書を使用します。

APNS と連携するようにモバイル サービスが構成されました。

<!-- URLs. -->

  []: ./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-apns-configure-push/mobile-services-selection.png
  [2]: ./media/mobile-services-apns-configure-push/mobile-push-tab-ios.png
  [3]: ./media/mobile-services-apns-configure-push/mobile-push-tab-ios-upload.png
