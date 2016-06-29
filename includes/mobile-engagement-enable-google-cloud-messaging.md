
###API キーを使用して Google Cloud Messaging プロジェクトを作成する

>[AZURE.NOTE] この手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。新しい Google アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a> にアクセスしてください。

1. [Google Cloud Console](https://console.developers.google.com/project) に移動し、Google アカウントの資格情報でサインインします。

2. **[All Projects (すべてのプロジェクト)]** に移動し、**[プロジェクトの作成]** をクリックします。

3. **[プロジェクト名]** に入力し、**[作成]** をクリックします。

4. プロジェクトが作成されたら、桁数の多い**プロジェクト番号**を必ずメモしてください。プロジェクト番号は、プロジェクトの **[設定]** の **[IAM と管理] セクション**に表示され、後で必要になります。
 
	![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)

5. 次に、Google Cloud Messaging プラットフォームで使用するキーを作成します。このキーは Android デバイスに通知を送信するために Microsoft のプラットフォームによって使用されます。**[API Manager]** セクションに移動し、**[Mobile API]** 内の **[Google Cloud Messaging]** をクリックします。

	![](./media/mobile-engagement-enable-google-cloud-messaging/gcm.png)

6. 次のページで、**[有効にする]** ボタンをクリックします。ダッシュボードで、認証情報の作成を求められます。**[認証情報に進む]** ボタンをクリックします。

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)

6. 最初のドロップダウン リストから **[Google Cloud Messaging]** を選択し、次のドロップダウン リストから **[ウェブサーバー]** を選択して、**[必要な認証情報]** をクリックします。

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

7. **[プロジェクトへの認証情報の追加]** ページで **[API キーを作成する]** をクリックします。

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)

8. **[API KEY]** の値をメモしておきます。この API キーの値は、後で [ネイティブ プッシュ通知] セクションで構成する際に使用するされます。**[完了]** をクリックします。

<!---HONumber=AcomDC_0622_2016-->