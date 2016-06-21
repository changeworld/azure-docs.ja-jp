
###API キーを使用して Google Cloud Messaging プロジェクトを作成する

>[AZURE.NOTE] この手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。新しい Google アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a> にアクセスしてください。

1. [Google Cloud Console](https://console.developers.google.com/project) に移動し、Google アカウントの資格情報でサインインします。

2. **[プロジェクトに移動]** をクリックし、**[プロジェクトの作成]** をクリックします。
<!--
   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project.png)

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project-2.png)   
-->
3. プロジェクト名を入力します。

4. **[プロジェクト名]** テキスト ボックスの下に表示されるプロジェクト番号を書き留めます。この番号は、チュートリアルの後半で Android マニフェスト ファイルを設定する際に必要になります。![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)
5. **[作成]** をクリックします。

6. 左側の列で **[概要]** が選択されていることを確認し、[Mobile API] の下にある **[Google Cloud Messaging]** をクリックします。次のページで、**[有効にする]** をクリックします。

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)
<!--
	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-gcm-2.png)
-->
7. 次のページで **[認証情報に進む]** をクリックします。その次のページの最初のドロップダウン リストから **[Google Cloud Messaging]** を選択し、次のドロップダウン リストから **[ウェブサーバー]** を選択して、**[必要な認証情報]** をクリックします。

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

8. **[プロジェクトへの認証情報の追加]** ページで **[API キーを作成する]** をクリックします。

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)
<!--
   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key6.png)
-->
9. **[API KEY]** の値をメモしておきます。この API キーの値は、後で [ネイティブ プッシュ通知] セクションで構成する際に使用するされます。**[完了]** をクリックします。

<!---HONumber=AcomDC_0608_2016-->