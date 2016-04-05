
###API キーを使用して Google Cloud Messaging プロジェクトを作成する

>[AZURE.NOTE] この手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。新しい Google アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a> にアクセスしてください。

1. [Google Cloud Console](https://console.developers.google.com/project) に移動し、Google アカウント資格情報でサインインして、**[CREATE PROJECT]** をクリックします。

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project.png)

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project-2.png)

2. プロジェクトの名前を入力し、サービスの条件に同意して、**[Create]** をクリックします。要求された SMS の確認を実行し、**[Create]** をもう一度クリックします。

3. **[Projects]** セクションに表示されたプロジェクト番号をメモしておきます。この番号は、チュートリアルの後半で Android マニフェスト ファイルを設定する際に必要になります。

   	![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)

4. 左側の列の **[APIs & auth]** を展開して **[APIs]** をクリックしてから下へスクロールし、**[Cloud Messaging for Android]** をクリックします。次のページで、**[Enable API]** をクリックし、サービス条件に同意します。

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-gcm-2.png)

5. **[Credentials]**、**[Add Credential]**、**[API Key]** の順にクリックします。

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

6. **[Create a new key]** で、**[Server key]** をクリックします。次のウィンドウで **[Create]** をクリックします。

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)


   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key6.png)

7. **[API KEY]** の値をメモしておきます。この API キーの値は、後で [ネイティブ プッシュ通知] セクションで構成する際に使用するされます。

<!---HONumber=AcomDC_0330_2016-->