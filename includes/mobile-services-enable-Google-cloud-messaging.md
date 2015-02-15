


1. <a href="http://cloud.google.com/console" target="_blank">Google Cloud Console Web</a> サイトに移動し、Google アカウント資格情報でサインインして、**[Create Project]** をクリックします。

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

	>[AZURE.NOTE]既にプロジェクトがある場合は、ログイン後に <strong>[Projects]</strong> ページが表示されます。ダッシュボードで新しいプロジェクトを作成するには、<strong>[API Project]</strong> を展開し、<strong>[Other projects]</strong> の下の <strong>[Create]</strong> をクリックして、プロジェクト名を入力してから <strong>[Create project]</strong> をクリックします。

2. プロジェクトの名前を入力し、サービスの条件に同意して、**[Create]** をクリックします。要求された SMS の確認を実行し、**[Create]** をもう一度クリックします。

3. **[Projects]** セクションに表示されたプロジェクト番号をメモしておきます。 

	チュートリアルの後の方で、クライアントの PROJECT_ID 変数に、この値を設定します。

4. 左側の列の **[APIs & auth]** をクリックして、下にスクロールし、トグルをクリックして **[Google Cloud Messaging for Android]** を有効にし、サービスの条件に同意します。 

	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. **[Credentials]**、**[Create new Key]** の順にクリックします。 

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. **[Create a new key]** で、**[Server key]** をクリックします。次のウィンドウで **[Create]** をクリックします。

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7. **[API KEY]** の値をメモしておきます。

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 

	この API キー値を使用して、Mobile Services が GCM で認証し、アプリケーションの代わりにプッシュ通知を送信できるようにします。


<!--HONumber=42-->
