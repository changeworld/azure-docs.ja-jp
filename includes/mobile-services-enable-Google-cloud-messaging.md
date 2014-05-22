

<p></p>

<div class="dev-callout"><b>注:</b>
<p>このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。新しい Google アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a> にアクセスしてください。</p>
</div> 

1. <a href="http://cloud.google.com/console" target="_blank">Google Cloud Console</a> Web サイトに移動し、Google アカウント資格情報でサインインして、**[CREATE PROJECT]** をクリックします。

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

	<div class="dev-callout"><b>注:</b>
	<p>既にプロジェクトがある場合は、ログイン後に <strong>[Projects]</strong> ページが表示されます。ダッシュボードで新しいプロジェクトを作成するには、<strong>[API Project]</strong> を展開し、<strong>[Other projects]</strong> の下の <strong>[Create...]</strong> をクリックして、プロジェクト名を入力してから <strong>[Create project]</strong> をクリックします。</p>
    </div>

2. プロジェクトの名前を入力し、サービスの条件に同意して、**[Create]** をクリックします。要求された SMS の確認を実行し、**[Create]** をもう一度クリックします。

3. **[Projects]** セクションに表示されたプロジェクト番号をメモしておきます。

	チュートリアルの後半で、クライアントの PROJECT_ID 変数に、この値を設定します。

4. 左側の列の **[APIs & auth]** をクリックして、下にスクロールし、トグルをクリックして **[Google Cloud Messaging for Android]** を有効にし、サービスの条件に同意します。

	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. **[Credentials]**、**[CREATE NEW KEY]** の順にクリックします。

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. **[Create a new key]** で、**[Server key]** をクリックします。次のウィンドウで **[Create]** をクリックします。

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7. **[API key]** の値をメモしておきます。

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 


