
1. [Google Cloud Console](https://console.developers.google.com/project) に移動し、Google アカウントの資格情報でサインインします。 
 
2. **[Create Project]** をクリックし、プロジェクト名を入力してから **[Create]** をクリックします。要求された SMS の確認を実行し、**[Create]** をもう一度クリックします。

   	![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)

	 新しい**プロジェクト名**を入力し、**[Create project]** をクリックします。

3. **[Projects]** セクションに表示されたプロジェクト番号をメモしておきます。クライアントの *PROJECT\_ID* 変数として、この値を設定する必要があります。

4. プロジェクトのダッシュボードで、**[Use Google APIs]**、**[Cloud Messaging for Android]** の順にクリックし、次のページで **[Enable API]** をクリックします。

5. API Manager で **[Credentials]**、**[Add Credential]**、**[API Key]** の順にクリックします。

   	![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)

6. **[Create a new key]** で **[Server key]** をクリックし、キーの名前を入力してから **[Create]** をクリックします。

7. **[API KEY]** の値をメモしておきます。

	この API キー値を使用して、Azure が GCM で認証し、アプリの代わりにプッシュ通知を送信できるようにします。

<!---HONumber=AcomDC_1203_2015-->