


1.  [Google Cloud Console][Google Cloud Console] Web サイトに移動し、Google アカウント資格情報でサインインして、**[CREATE PROJECT]** をクリックします。

    ![][0]

    >[WACOM.NOTE] 既にプロジェクトがある場合は、ログイン後に **[Projects]** ページが表示されます。ダッシュボードで新しいプロジェクトを作成するには、**[API Project]** を展開し、**[Other projects]** の下の **[Create]** をクリックして、プロジェクト名を入力してから **[Create project]** をクリックします。

2.  プロジェクトの名前を入力し、サービスの条件に同意して、**[Create]** をクリックします。要求された SMS の確認を実行し、**[Create]** をもう一度クリックします。

3.  **[Projects]** セクションに表示されたプロジェクト番号をメモしておきます。

    チュートリアルの後の方で、クライアントの PROJECT\_ID 変数に、この値を設定します。

4.  左側の列の **[APIs & auth]** をクリックして、下へスクロールし、トグルをクリックして **[Google Cloud Messaging for Android]** を有効にし、サービスの条件に同意します。

    ![][1]

5.  **[Credentials]**、**[CREATE NEW KEY]** の順にクリックします。

    ![][2]

6.  **[Create a new key]** で、**[Server key]** をクリックします。次のウィンドウで **[Create]** をクリックします。

    ![][3]

7.  **[API key]** の値をメモしておきます。

    ![][4]

    この API キー値を使用して、Mobile Services が GCM で認証し、アプリケーションの代わりにプッシュ通知を送信できるようにします。

  [Google Cloud Console]: http://cloud.google.com/console
  [0]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
  [1]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
  [2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
  [3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
  [4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
