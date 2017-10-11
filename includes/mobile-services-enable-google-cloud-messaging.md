
1. 移動し、 [Google Cloud コンソール](https://console.developers.google.com/project)、Google アカウントの資格情報でサインインします。 
2. をクリックして**プロジェクトの作成**プロジェクトの名前を入力しをクリックして、**作成**です。 SMS 検証を実行しをクリックして、要求された場合**作成**もう一度です。
   
    ![新規プロジェクトの作成](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
   
     新しい型**プロジェクト名** をクリック**プロジェクトの作成**です。
3. クリックして、**ユーティリティと複数**ボタンをクリックし、をクリックして**プロジェクト情報**です。 メモ、**プロジェクト番号**です。 この値として設定する必要があります、`SenderId`クライアント アプリの変数です。
   
    ![ユーティリティと詳細](./media/mobile-services-enable-google-cloud-messaging/notification-hubs-utilities-and-more.png)
4. プロジェクト ダッシュ ボードで、[ **Mobile Api**、] をクリックして**Google Cloud Messaging**、ページで [次へ] をクリックして**を有効にする API**サービスの使用に同意します。 
   
    ![GCM を有効にします。](./media/mobile-services-enable-google-cloud-messaging/enable-GCM.png)
   
    ![GCM を有効にします。](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-2.png) 
5. プロジェクト ダッシュ ボードで、をクリックして**資格情報** > **Create Credential** > **API キー**です。 
   
    ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)
6. **新しいキーを作成**をクリックして**サーバー キー**、キーの名前を入力しをクリックして、**作成**です。
7. メモ、 **API キー**値。
   
    この API キーの値を使用して、GCM と認証し、アプリの代わりにプッシュ通知を送信する Azure を有効にするされます。

