
Apple Push Notification Service (APNS) では、証明書を使用してプッシュ通知を認証します。次の手順に従って、通知を送受信するために必要なプッシュ証明書を作成します。公式な APNS 機能のドキュメントについては、[Apple プッシュ通知サービス](http://go.microsoft.com/fwlink/p/?LinkId=272584)に関するページを参照してください。

##証明書の署名要求ファイルを生成する

まず、Apple が署名済みのプッシュ証明書を生成するために使用する、証明書署名要求 (CSR: Certificate Signing Request) ファイルを生成する必要があります。

1. **[Utilities]** フォルダーまたは **[Other]** フォルダーから Keychain Access ツールを実行します。

2. **[Keychain Access]** をクリックし、**[Certificate Assistant]** を展開して、**[Request a Certificate from a Certificate Authority]** をクリックします。

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. **[User Email Address]** と **[Common Name]** を選択し、**[Saved to disk]** が選択されていることを確認して、**[Continue]** をクリックします。必要ではないため、"**CA 電子メール アドレス**" フィールドを空白のままにします。

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. **[Save As]** に証明書署名要求 (CSR) ファイルの名前を入力し、**[Where]** で保存場所を選択して **[Save]** をクリックします。

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

  	指定選択した場所に CSR ファイルが保存されます。既定の場所は [Desktop] です。このファイル用に選択した場所を忘れないでください。

次に、アプリケーションを Apple に登録し、プッシュ通知を有効にし、このエクスポートした CSR をアップロードしてプッシュ通知を作成します。

##アプリケーションをプッシュ通知に登録する

iOS アプリケーションにプッシュ通知を送信できるようにするには、アプリケーションを Apple に登録し、プッシュ通知にも登録する必要があります。

1. アプリをまだ登録していない場合は、Apple Developer センターで <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> に移動し、Apple ID でログインして、**[Identifiers]** をクリックし、**[App IDs]** をクリックします。最後に、**+** 記号をクリックして新しいアプリを登録します。

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)


2. **[App ID Description (アプリ ID の説明)]** 名にアプリのわかりやすい名前を入力します。

	**[Explicit App ID (明示的なアプリ ID)]** に、「[App Distribution Guide (アプリ ディストリビューション ガイド)](http://go.microsoft.com/fwlink/?LinkId=613485)」で説明したように `<Organization Identifier>.<Product Name>` の形式で **Bundle Identifier** を入力します。使用する *Organization Identifier* と *Product Name* は XCode プロジェクトを作成する際に使用する組織 ID と製品名に一致させる必要があります。次のスクリーンショットでは、組織 ID として*NotificationHubs*、製品名として *GetStarted* を使用しています。XCode プロジェクトで使用する値と一致していることを確認することで、XCode で正しい発行プロファイルが使用できるようになります。
	
	App Services セクションの **[Push Notifications (プッシュ通知)]** オプションを選択し、**[Continue (続行)]** をクリックします。

	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

   	これで、アプリケーション ID が生成され、情報の確認が求められます。**[Submit]** をクリックします。


    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-confirm-new-appid.png)


   	**[Submit]** をクリックすると、以下のような **[Registration complete]** 画面が表示されます。**[Done]** をクリックします。


    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)


3. 作成したアプリケーション ID を見つけ、その行をクリックします。

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

   	アプリケーション ID をクリックすると、アプリケーションの詳細が表示されます。**[編集]** をクリックします。

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

4. 画面の下部までスクロールし、**[Development Push SSL Certificate]** セクションの **[Create Certificate]** ボタンをクリックします。

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

   	これで、[Add iOS Certificate] アシスタントが表示されます。

    > [AZURE.NOTE]このチュートリアルでは開発証明書を使用します。運用証明書の場合も同じ処理を行います。通知の送信と同じ証明書の種類を使用するようにします。

5. **[Choose File]** をクリックして、最初の作業で CSR ファイルを保存した場所に移動し、**[Generate]** をクリックします。

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

6. ポータルで証明書が作成されたら **[Download]** ボタンをクリックし、**[Done]** をクリックします。

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

   	これによって、署名証明書がダウンロードされ、コンピューターの Downloads フォルダーに保存されます。

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [AZURE.NOTE]既定では、ダウンロードした開発証明書ファイルの名前は **aps_development.cer** になっています。

7. ダウンロードしたプッシュ証明書 **aps_development.cer** をダブルクリックします。

   	下図のように、新しい証明書が Keychain にインストールされます。

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [AZURE.NOTE]証明書の名前は異なることがありますが、名前の前に **Apple Development iOS Push Services:** が付けられます。

後で、この証明書を使用して .p12 ファイルを生成し、APNS による認証を有効にします。

##アプリケーションのプロビジョニング プロファイルを作成する

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> に戻って **[Provisioning Profiles]** を選択し、**[All]** を選択してから **+** ボタンをクリックして、新しいプロファイルを作成します。これで、**Add iOS Provisiong Profile** ウィザードが起動されます。

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. **[Development]** でプロビジョニング プロファイルの種類として **[iOS App Development]** を選択し、**[Continue]** をクリックします。


3. 次に、**[App ID (アプリ ID)]** ドロップダウン リストから作成したばかりのアプリケーション ID を選択し、**[Continue (続行)]** をクリックします。

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)


4. **[Select certificates (証明書の選択)]** 画面で、コードの署名に使用される通常の開発証明書を選択して、**[Continue (続行)]** をクリックします。これは作成したプッシュの証明書ではありません。

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)


5. 次に、テストに使用する**デバイス**を選択し、**[Continue]** をクリックします。

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)


6. 最後に、**[Profile Name]** でプロファイルの名前を選択し、**[Generate]** をクリックしてから、**[Done]** をクリックします。

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)


  	新しいプロビジョニング プロファイルが作成されます。

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)

<!---HONumber=July15_HO2-->