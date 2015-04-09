アプリケーションを APNS に登録し、プロジェクトを構成した後で、モバイル アプリを APNS と統合するように構成する必要があります。

1. Keychain Access で、**[鍵]** または **[自分の証明書]** で新しい証明書を右クリックし、**[エクスポート]** をクリックします。ファイルの名前を [QuickstartPusher] にして **[.p12]** 形式を選択し、**[保存]** をクリックします。

   	![](./media/mobile-services-apns-configure-push/mobile-services-ios-push-step18.png)

    エクスポートした証明書のファイル名と場所を書き留めます。

>[AZURE.NOTE] このチュートリアルでは QuickstartPusher.p12 ファイルを作成します。ファイル名と場所は同じである必要はありません。

2. [Azure プレビュー ポータル]にログオンし、**[参照]**、**[モバイル アプリ]** の順にクリックし、アプリをクリックします。[プッシュ通知サービス] をクリックします。

3. Apple Push Notification Service で、証明書を **.p12** ファイルとそれに関連付けたパスワードを使用してアップロードし、目的のモードを選択します。

これで、App Service モバイル アプリが APNS と連携するように構成されました。

<!-- URLs. -->
[Azure プレビュー ポータル]: https://portal.azure.com/

<!--HONumber=49-->