1. [Google Cloud Console](https://console.developers.google.com/project) に移動して、Google アカウント資格情報でサインインします。次に、**[Select a project]**、**[Create a project]** の順にクリックします。

2. プロジェクトの名前を入力し、サービスの条件に同意して、**[Create]** をクリックします。要求された SMS の確認を実行し、**[Create]** をもう一度クリックします。

3. **[Projects]** セクションに表示されたプロジェクト番号をメモしておきます。

	チュートリアルの後の方で、クライアントの PROJECT\_ID 変数に、この値を設定します。

4. **[User Google APIs]** で **[Enable and manage APIs]** をクリックし、**[Cloud Messaging for Android]** をクリックします。次のページで、**[Enable API]** をクリックします。

5. **[Credentials]**、**[Add Credential]**、**[API Key]** の順にクリックします。

6. **[Create a new key]** で、**[Server key]** をクリックします。次のウィンドウで **[Create]** をクリックします。

7. **[API KEY]** の値をメモしておきます。

	この API キー値を使用して、Azure が GCM で認証し、アプリの代わりにプッシュ通知を送信できるようにします。

<!---HONumber=AcomDC_1203_2015-->