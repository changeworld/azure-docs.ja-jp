次の手順では、Windows ストアでアプリケーションを登録し、プッシュ通知を有効にするモバイル サービスを構成し、デバイス チャネルを通知ハブに登録するコードをアプリケーションに追加します。設定した資格情報を使用して、Visual Studio 2013 が Azure と Windows ストアに接続されます。

1. Visual Studio 2013 で、ソリューション エクスプローラーを開き、Windows ストアのアプリケーション プロジェクトを右クリックして、**[追加]**、**[プッシュ通知]** の順にクリックします。 

	![Visual Studio 2013 でのプッシュ通知の追加](./media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013.png)

	これにより、プッシュ通知の追加ウィザードが開始されます。

2. **[次へ]** をクリックし、Windows ストア アカウントにサインインします。次に、**[新しいアプリケーション名の予約]** に名前を指定し、**[予約]** をクリックします。

	これで、新しいアプリケーションの登録が作成されます。

3. **[アプリケーション名]** の一覧で新しい登録をクリックし、**[次へ]** をクリックします。

4. **[サービスの選択]** ページでモバイル サービス名をクリックし、**[次へ]**、**[完了]** の順にクリックします。

	モバイル サービスによって使用される通知ハブが、Windows Notification Services (WNS) の登録を使用して更新されます。これでモバイル サービスが、Azure の通知ハブを使用し、WNS と連携してアプリケーションに通知を送信できるようになりました。

	>[AZURE.NOTE]このチュートリアルでは、モバイル サービスのバックエンドから通知を送信する方法について説明しています。しかし同じ通知ハブの登録を使用して、任意のバックエンド サービスから通知を送信できます。詳細については、「[通知ハブの概要](http://msdn.microsoft.com/library/azure/jj927170.aspx)」を参照してください。

5. ウィザードを完了すると、Visual Studio で新しく **[プッシュ設定はほぼ完了です]** ページが開きます。このページでは、モバイル サービス プロジェクトで通知の送信を構成するための別の方法として、このチュートリアルとは異なる方法が詳しく説明されています。

	プッシュ通知の追加ウィザードによってユニバーサル Windows アプリケーション ソリューションに追加されたコードは、プラットフォーム固有です。このチュートリアルでは、後ほどモバイル サービスのクライアント コードを共有して、この重複を解消します。これにより、ユニバーサル アプリケーションのメンテナンスが容易になります。

<!-- URLs. -->
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet/
[Import your publishsettings file in Visual Studio 2013]: ../articles/mobile-services/mobile-services-windows-how-to-import-publishsettings.md

<!---HONumber=August15_HO6-->