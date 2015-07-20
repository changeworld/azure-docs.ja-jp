
###テスト用の Android エミュレーターの設定
このアプリケーションをエミュレーターで実行する場合は、Google API をサポートしている Android Virtual Device (AVD) を使用してください。

> [AZURE.IMPORTANT]プッシュ通知を受信するには、Android Virtual Device で Google アカウントを設定する必要があります (エミュレーターで、**[Settings]** に移動して **[Add Account]** をクリックします)。さらに、エミュレーターがインターネットに接続されていることを確認します。

1. **[ツール]** で **[Android エミュレーター マネージャーを開く]** をクリックし、デバイスを選択してから **[編集]** をクリックします。

   	![Android Virtual Device Manager](./media/mobile-services-android-push-notifications-test/notification-hub-create-android-app7.png)

2. **[Target]** で **[Google APIs]** を選択し、**[OK]** をクリックします。

   	![Android Virtual Device の編集](./media/mobile-services-android-push-notifications-test/notification-hub-create-android-app8.png)

3. 上部のツール バーで、**[実行]** をクリックし、アプリを選択します。これによりエミュレーターが起動し、アプリケーションが実行されます。

  アプリにより、GCM から *registrationId* が取得され、通知ハブに登録されます。

###新しい項目を挿入すると、通知が生成されます。

1. アプリケーションで、意味のあるテキスト (たとえば、「_新しいモバイル サービス タスク_」) を入力し、**[Add]** をクリックします。

2. 画面の上端から下へスワイプし、通知を表示するためのデバイスの通知センターを開きます。

	![通知センターでの通知の表示](./media/mobile-services-android-push-notifications-test/notification-area-received.png)

<!---HONumber=July15_HO2-->