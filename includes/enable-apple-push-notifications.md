
Apple Push Notification Service (APNS) では、証明書を使用してモバイル サービスを認証します。次の手順に従って、必要な証明書を作成し、モバイル サービスにアップロードしてください。公式な APNS 機能のドキュメントについては、「[Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584)」を参照してください。

## <a id="certificates"></a>証明書署名要求ファイルを生成する

まず、Apple が署名証明書を生成するために使用する、証明書署名要求 (CSR: Certificate Signing Request) ファイルを生成する必要があります。

1. Utilities フォルダーから [Keychain Access] ツールを実行します。

2. **[Keychain Access]** をクリックし、**[Certificate Assistant]** を展開して、**[Request a Certificate from a Certificate Authority]** をクリックします。

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step5.png)

3. **[User Email Address]** と **[Common Name]** を選択し、**[Saved to disk]** が選択されていることを確認して、**[Continue]** をクリックします。必要ではないため、"**CA 電子メール アドレス**" フィールドを空白のままにします。

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step6.png)

4. **[Save As]** に証明書署名要求 (CSR) ファイルの名前を入力し、**[Where]** で保存場所を選択して **[Save]** をクリックします。

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step7.png)

  	指定選択した場所に CSR ファイルが保存されます。既定の場所は [Desktop] です。このファイル用に選択した場所を忘れないでください。

次に、アプリケーションを Apple に登録し、プッシュ通知を有効にし、このエクスポートした CSR をアップロードしてプッシュ通知を作成します。

## <a id="register"></a>アプリをプッシュ通知に登録する

モバイル サービスから iOS アプリケーションにプッシュ通知を送信できるようにするには、アプリケーションを Apple に登録し、プッシュ通知にも登録する必要があります。

1. アプリをまだ登録していない場合は、Apple Developer センターで <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> に移動し、Apple ID でログインして、**[Identifiers]** をクリックし、**[App IDs]** をクリックします。最後に、**+** 記号をクリックして新しいアプリを登録します。

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-02.png)



> [AZURE.NOTE]<strong>[Bundle Identifier]</strong> に <i>MobileServices.Quickstart</i> 以外の値を指定する場合は、Xcode プロジェクトでバンドル ID の値を更新する必要があります。クイック スタート プロジェクトで既に使用した正確なバンドル id の値を使用することをお勧めします。

2. **[Description]** にアプリケーションの名前を入力し、_[Bundle Identifier]_ に値として「**MobileServices.Quickstart**」と入力します。[App Services] セクションの [Push Notifications] オプションをオンにして、**[Continue]** をクリックします。この例では ID として **MobileServices.Quickstart** を使用していますが、アプリケーション ID はすべてのユーザー間で一意である必要があるので、この同じ ID を再利用することはできません。そのため、自分のフル ネームやイニシャルをアプリケーション名の後に付加することをお勧めします。


    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-03.png)

   	これで、アプリケーション ID が生成され、情報の**サブミット**が求められます。**[Submit]** をクリックします。


    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-04.png)


   	**[Submit]** をクリックすると、以下のような **[Registration complete]** 画面が表示されます。**[Done]** をクリックします。


    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-05.png)


3. 作成したアプリケーション ID を見つけ、その行をクリックします。

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-06.png)

   	アプリケーション ID をクリックすると、アプリケーションとアプリケーション ID の詳細が表示されます。**[Settings]** をクリックします。

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-07.png)

4. 画面の下部までスクロールし、**[Development Push SSL Certificate]** セクションの **[Create Certificate]** ボタンをクリックします。

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-08.png)

   	これで、[Add iOS Certificate] アシスタントが表示されます。

    > [AZURE.NOTE]このチュートリアルでは開発証明書を使用します。運用証明書の場合も同じ処理を行います。証明書をモバイル サービスにアップロードするときと同じ証明書タイプを設定してください。

5. **[Choose File]** をクリックして、最初の作業で CSR ファイルを保存した場所に移動し、**[Generate]** をクリックします。

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-10.png)

6. ポータルで証明書が作成されたら **[Download]** ボタンをクリックし、**[Done]** をクリックします。

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-11.png)

   	これによって、署名証明書がダウンロードされ、コンピューターの Downloads フォルダーに保存されます。

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step9.png)

    > [AZURE.NOTE]既定では、ダウンロードした開発証明書ファイルの名前は **aps_development.cer** になっています。

7. ダウンロードしたプッシュ証明書 **aps_development.cer** をダブルクリックします。

   	下図のように、新しい証明書が Keychain にインストールされます。

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step10.png)

    > [AZURE.NOTE]証明書の名前は異なることがありますが、名前の前に **Apple Development iOS Push Services:** が付けられます。

後で、この証明書を使用して .p12 ファイルを生成し、それをモバイル サービスにアップロードして APNS による認証を有効にします。

## <a id="profile"></a>アプリケーションのプロビジョニング プロファイルを作成する

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> に戻って **[Provisioning Profiles]** を選択し、**[All]** を選択してから **+** ボタンをクリックして、新しいプロファイルを作成します。これで、**Add iOS Provisiong Profile** ウィザードが起動されます。

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-12.png)

2. **[Development]** でプロビジョニング プロファイルの種類として **[iOS App Development]** を選択し、**[Continue]** をクリックします。

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-13.png)

3. 次に、**[App ID]** の一覧でモバイル サービスのクイック スタート アプリケーションのアプリケーション ID を選択し、**[Continue]** をクリックします。

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-14.png)

4. **[Select certificates]** 画面で、前に作成した証明書を選択し、**[Continue]** をクリックします。

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-15.png)

5. 次に、テストに使用する**デバイス**を選択し、**[Continue]** をクリックします。

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-16.png)

6. 最後に、**[Profile Name]** でプロファイルの名前を選択し、**[Generate]** をクリックしてから、**[Done]** をクリックします。

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-17.png)

  	新しいプロビジョニング プロファイルが作成されます。

7. Xcode で、Organizer を開いて [Devices] ビューを選択し、左ウィンドウの **[Library]** セクションにある **[Provisioning Profiles]** を選択して、中央ウィンドウの一番下にある **[Refresh]** をクリックします。

8. または、Xcode メニューで **[Preferences]**、**[Accounts]** の順にクリックします。左側のパネルで、Apple の開発者の ID を選択します。右側の **[View Details]** をクリックします。ポップオーバー ウィンドウで、角の丸い **[Refresh]** をクリックします。これにより、プロビジョニング プロファイルの一覧が更新されます。このプロセスには数分かかることがあります。新しいプロビジョニング プロファイルが表示されるまで、**[Refresh]** を 2 ～ 3 回クリックすることをお勧めします。また、この Xcode プロジェクトのバンドル識別子が、アプリケーション ID およびこれまでに作成したプロビジョニング プロファイルに関連付けられているバンドル識別子と同じであることを確認してください。

    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-01.png)

9. **[Targets]** の **[Quickstart]** をクリックし、**[Code Signing Identity]** を展開して、**[Debug]** で新しいプロファイルを選択します。これで、Xcode プロジェクトはコード署名のために新しいプロファイルを使用するようになります。次に、証明書を Azure にアップロードする必要があります。

   	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step17.png)

<!--HONumber=54-->