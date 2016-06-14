
1. [Google Cloud Console](https://console.developers.google.com/project) に移動し、Google アカウントの資格情報でサインインします。 
 
2. **[Create Project]** をクリックし、プロジェクト名を入力してから **[Create]** をクリックします。要求された SMS の確認を実行し、**[Create]** をもう一度クリックします。

   	![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)

	 新しい**プロジェクト名**を入力し、**[Create project]** をクリックします。

3. **[Utilities and More]** ボタンをクリックし、**[Project Information]** をクリックします。**プロジェクト番号**をメモしておきます。クライアント アプリの `SenderId` 変数として、この値を設定する必要があります。

   	![](./media/mobile-services-enable-google-cloud-messaging/notification-hubs-utilities-and-more.png)


4. プロジェクトのダッシュボードで、**[Mobile APIs (Mobile API)]** の **[Google Cloud Messaging (Google Cloud Messaging)]** をクリックし、次のページで **[Enable API (API を有効にする)]** をクリックし、サービス使用条件に同意します。

	![Enabling GCM](./media/mobile-services-enable-google-cloud-messaging/enable-GCM.png)

	![Enabling GCM](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-2.png)

5. プロジェクト ダッシュボードで、**[Credentials (認証情報)]**、**[Create Credential (認証情報の作成)]**、**[API Key (API キー)]** の順にクリックします。

   	![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)

6. **[Create a new key]** で **[Server key]** をクリックし、キーの名前を入力してから **[Create]** をクリックします。

7. **[API KEY]** の値をメモしておきます。

	この API キー値を使用して、Azure が GCM で認証し、アプリの代わりにプッシュ通知を送信できるようにします。

<!---HONumber=AcomDC_0608_2016-->