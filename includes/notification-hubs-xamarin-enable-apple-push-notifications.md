

Apple Push Notification サービス (APNS) を介してアプリ用にプッシュ通知を登録するには、新しいプッシュ証明書、アプリケーション ID、プロジェクトのプロビジョニング プロファイルを Apple の開発者ポータルで作成する必要があります。アプリケーション ID には、プッシュ通知をアプリケーションで送受信できるようにする構成設定が含まれます。これらの設定には、プッシュ通知を送受信するときに必要な Apple Push Notification サービス (APNS) と認証するためのプッシュ通知証明書が含まれます。これらの概念の詳細については、[Apple Push Notification サービス](http://go.microsoft.com/fwlink/p/?LinkId=272584)の公式ドキュメントを参照してください。


####プッシュ証明書用に証明書の署名要求ファイルを生成する

次の手順では、証明書の署名要求の作成手順について説明します。これは、APNS で使用するプッシュ証明書の生成に使用されます。

1. Mac で、キーチェーン アクセス ツールを実行します。これは、Launchpad の **[Utilities]** フォルダーまたは **[Other]** フォルダーから開くことができます。

2. **[Keychain Access]** をクリックし、**[Certificate Assistant]** を展開して、**[Request a Certificate from a Certificate Authority]** をクリックします。

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. **[User Email Address]** と **[Common Name]** を選択し、**[Saved to disk]** が選択されていることを確認して、**[Continue]** をクリックします。必要ではないため、"**CA 電子メール アドレス**" フィールドを空白のままにします。

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. **[Save As]** に証明書署名要求 (CSR) ファイルの名前を入力し、**[Where]** で保存場所を選択して **[Save]** をクリックします。

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)

  	指定選択した場所に CSR ファイルが保存されます。既定の場所は [Desktop] です。このファイル用に選択した場所を忘れないでください。


####アプリケーションをプッシュ通知に登録する

Apple でアプリケーション用の新しいの明示的なアプリケーション ID を作成し、さらにそれをプッシュ通知用に構成します。

1. Apple Developer センターで [iOS Provisioning Portal](http://go.microsoft.com/fwlink/p/?LinkId=272456) に移動し、Apple ID でログインして、**[Identifiers]** をクリックし、**[App IDs]** をクリックします。最後に、**+** 記号をクリックして新しいアプリを登録します。

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. 新しいアプリの次の 3 つのフィールドを更新し、**[Continue]** をクリックします。

	* **[Name]**: **[App ID Description]** セクションの **[Name]** フィールドに、アプリのわかりやすい名前を入力します。

	* **[Bundle Identifier]**: **[Explicit App ID]** セクションに、[アプリ ディストリビューション ガイド](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8)で説明したように `<Organization Identifier>.<Product Name>` の形式で**バンドル ID** を入力します。これは、アプリの XCode、Xamarin、または Cordova プロジェクトで使用されているものとも一致する必要があります。

	* **[Push Notifications]**: **[App Services]** セクションの **[Push Notifications]** オプションをオンにします。

	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

3.	[Confirm your App ID] 画面で設定を確認し、確認後 **[Submit]** をクリックします。

4. 	新しいアプリケーション ID を送信すると、**[Registration complete]** 画面が表示されます。**[Done]** をクリックします。

5. Developer センターで、アプリケーション ID の一覧から作成したアプリケーション ID を見つけ、その行をクリックします。アプリケーション ID 行をクリックすると、アプリケーションの詳細が表示されます。画面の下部にある **[Edit]** をクリックします。

6. 画面の下部までスクロールし、**[Development Push SSL Certificate]** セクションの **[Create Certificate]** ボタンをクリックします。

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

   	これで、"Add iOS Certificate" アシスタントが表示されます。

    > [AZURE.NOTE] このチュートリアルでは開発証明書を使用します。運用証明書の場合も同じ処理を行います。通知の送信と同じ証明書の種類を使用するようにします。

7. **[Choose File]** をクリックして、プッシュ証明書用に CSR を保存した場所に移動します。次いで、**[Generate]** をクリックします。

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

8. ポータルで証明書が作成されたら **[Download]** ボタンをクリックします。

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

   	これによって、署名証明書がダウンロードされ、コンピューターの Downloads フォルダーに保存されます。

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [AZURE.NOTE] 既定では、ダウンロードした開発証明書ファイルの名前は **aps\_development.cer** になっています。

9. ダウンロードしたプッシュ証明書 **aps\_development.cer** をダブルクリックします。下図のように、新しい証明書が Keychain にインストールされます。

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [AZURE.NOTE] 証明書の名前は異なることがありますが、名前の前に **Apple Development iOS Push Services:** が付けられます。

10. キーチェーン アクセスの **[証明書]** カテゴリで、作成したばかりの新しいプッシュ証明書を右クリックします。**[書き出す]** をクリックし、ファイルに名前を付けて、**.p12** 形式を選択します。次に、**[保存]** をクリックします。

	エクスポートした .p12 証明書のファイル名と場所を記録します。これは、Azure クラシック ポータルにアップロードして APNS との認証を有効にするために使用されます。



####アプリケーションのプロビジョニング プロファイルを作成する

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> に戻って **[Provisioning Profiles]** を選択し、**[All]** を選択してから **+** ボタンをクリックして、新しいプロファイルを作成します。これで、**Add iOS Provisiong Profile** ウィザードが起動されます。

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. **[Development]** でプロビジョニング プロファイルの種類として **[iOS App Development]** を選択し、**[Continue]** をクリックします。


3. 次に、**[App ID (アプリ ID)]** ドロップダウン リストから作成したばかりのアプリケーション ID を選択し、**[Continue (続行)]** をクリックします。

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)


4. **[Select certificates (証明書の選択)]** 画面で、コードの署名に使用される開発証明書を選択して、**[Continue (続行)]** をクリックします。これは署名証明書で作成したプッシュの証明書ではありません。

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)


5. 次に、テストに使用する**デバイス**を選択し、**[Continue]** をクリックします。

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)


6. 最後に、**[Profile Name]** でプロファイルの名前を選択し、**[Generate]** をクリックします。

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

<!---HONumber=AcomDC_0720_2016-->