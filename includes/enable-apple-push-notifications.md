Apple Push Notification Service (APNS) では、証明書を使用してモバイル サービスを認証します。次の手順に従って、必要な証明書を作成し、モバイル サービスにアップロードしてください。公式な APNS 機能のドキュメントについては、「[Apple Push Notification Service][]」を参照してください。

## 証明書の署名要求ファイルを生成する

まず、Apple が署名証明書を生成するために使用する、証明書署名要求 (CSR: Certificate Signing Request) ファイルを生成する必要があります。

1.  Utilities フォルダーから [Keychain Access] ツールを実行します。

2.  **[Keychain Access]** をクリックし、**[Certificate Assistant]** を展開して、**[Request a Certificate from a Certificate Authority]** をクリックします。

    ![][]

3.  **[User Email Address]** と **[Common Name]** を選択し、**[Saved to disk]** が選択されていることを確認して、**[Continue]** をクリックします。必要ではないため、"**CA 電子メール アドレス**" フィールドを空白のままにします。

    ![][1]

4.  **[Save As]** に証明書署名要求 (CSR) ファイルの名前を入力し、**[Where]** で保存場所を選択して **[Save]** をクリックします。

    ![][2]

    指定選択した場所に CSR ファイルが保存されます。既定の場所は [Desktop] です。このファイル用に選択した場所を忘れないでください。

次に、アプリケーションを Apple に登録し、プッシュ通知を有効にし、このエクスポートした CSR をアップロードしてプッシュ通知を作成します。

## アプリケーションをプッシュ通知に登録する

モバイル サービスから iOS アプリケーションにプッシュ通知を送信できるようにするには、アプリケーションを Apple に登録し、プッシュ通知にも登録する必要があります。

1.  アプリケーションをまだ登録していない場合は、Apple デベロッパー センターで [iOS プロビジョニング ポータル][]に移動し、Apple ID でログインして、**[Identifiers]** をクリックし、**[App IDs]** をクリックします。最後に、**+** 記号をクリックして新しいアプリケーションを登録します。

    ![][3]

2.  **[Description]** にアプリケーションの名前を入力し、**[Bundle Identifier]** に値として「*MobileServices.Quickstart*」と入力します。[App Services] セクションの [Push Notifications] オプションをオンにして、**[Continue]** をクリックします。この例では ID として **MobileServices.Quickstart** を使用していますが、アプリケーション ID はすべてのユーザー間で一意である必要があるので、この同じ ID を再利用することはできません。そのため、自分のフル ネームやイニシャルをアプリケーション名の後に付加することをお勧めします。

    ![][4]

    これで、アプリケーション ID が生成され、情報の**サブミット**が求められます。**[Submit]** をクリックします。

    ![][5]

    **[Submit]** をクリックすると、以下のような **[Registration complete]** 画面が表示されます。**[Done]** をクリックします。

    ![][6]

    > [WACOM.NOTE] **[Bundle Identifier]** に *MobileServices.Quickstart* 以外の値を指定する場合は、Xcode プロジェクトでバンドル ID の値も更新する必要があります。

3.  作成したアプリケーション ID を見つけ、その行をクリックします。

    ![][7]

    アプリケーション ID をクリックすると、アプリケーションとアプリケーション ID の詳細が表示されます。**[Settings]** をクリックします。

    ![][8]

4.  画面の下部までスクロールし、**[Development Push SSL Certificate]** セクションの **[Create Certificate]** ボタンをクリックします。

    ![][9]

    これで、[Add iOS Certificate] アシスタントが表示されます。

    > [WACOM.NOTE] このチュートリアルでは開発証明書を使用します。運用証明書の場合も同じ処理を行います。証明書をモバイル サービスにアップロードするときと同じ証明書タイプを設定してください。

5.  **[Choose File]** をクリックして、最初の作業で CSR ファイルを保存した場所に移動し、**[Generate]** をクリックします。

    ![][10]

6.  ポータルで証明書が作成されたら **[Download]** ボタンをクリックし、**[Done]** をクリックします。

    ![][11]

    これによって、署名証明書がダウンロードされ、コンピューターの Downloads フォルダーに保存されます。

    ![][12]

    > [WACOM.NOTE] 既定では、ダウンロードした開発証明書ファイルの名前は **aps\_development.cer** になっています。

7.  ダウンロードしたプッシュ証明書 **aps\_development.cer** をダブルクリックします。

    下図のように、新しい証明書が Keychain にインストールされます。

    ![][13]

    > [WACOM.NOTE] 証明書の名前は異なることがありますが、名前の前に **Apple Development iOS Push Notification Services:** が付けられます。

後で、この証明書を使用して .p12 ファイルを生成し、それをモバイル サービスにアップロードして APNS による認証を有効にします。

## アプリケーションのプロビジョニング プロファイルを作成する

1.  [iOS プロビジョニング ポータル][]に戻って **[Provisioning Profiles]** を選択し、**[All]** を選択してから **+** ボタンをクリックして、新しいプロファイルを作成します。これで、**Add iOS Provisiong Profile** ウィザードが起動されます。

    ![][14]

2.  **[Development]** でプロビジョニング プロファイルの種類として **[iOS App Development]** を選択し、**[Continue]** をクリックします。

    ![][15]

3.  次に、**[App ID]** の一覧でモバイル サービスのクイック スタート アプリケーションのアプリケーション ID を選択し、**[Continue]** をクリックします。

    ![][16]

4.  **[Select certificates]** 画面で、前に作成した証明書を選択し、**[Continue]** をクリックします。

    ![][17]

5.  次に、テストに使用する**デバイス**を選択し、**[Continue]** をクリックします。

    ![][18]

6.  最後に、**[Profile Name]** でプロファイルの名前を選択し、**[Generate]** をクリックしてから、**[Done]** をクリックします。

    ![][19]

    新しいプロビジョニング プロファイルが作成されます。

7.  Xcode で、Organizer を開いて [Devices] ビューを選択し、左ウィンドウの **[Library]** セクションにある **[Provisioning Profiles]** を選択して、中央ウィンドウの一番下にある **[Refresh]** をクリックします。

    ![][20]

8.  **[Targets]** の **[Quickstart]** をクリックし、**[Code Signing Identity]** を展開して、**[Debug]** で新しいプロファイルを選択します。

    ![][21]

これで、Xcode プロジェクトはコード署名のために新しいプロファイルを使用するようになります。次に、証明書を Azure にアップロードする必要があります。

  [Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
  []: ./media/enable-apple-push-notifications/mobile-services-ios-push-step5.png
  [1]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step6.png
  [2]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step7.png
  [iOS プロビジョニング ポータル]: http://go.microsoft.com/fwlink/p/?LinkId=272456
  [3]: ./media/enable-apple-push-notifications/mobile-services-ios-push-02.png
  [4]: ./media/enable-apple-push-notifications/mobile-services-ios-push-03.png
  [5]: ./media/enable-apple-push-notifications/mobile-services-ios-push-04.png
  [6]: ./media/enable-apple-push-notifications/mobile-services-ios-push-05.png
  [7]: ./media/enable-apple-push-notifications/mobile-services-ios-push-06.png
  [8]: ./media/enable-apple-push-notifications/mobile-services-ios-push-07.png
  [9]: ./media/enable-apple-push-notifications/mobile-services-ios-push-08.png
  [10]: ./media/enable-apple-push-notifications/mobile-services-ios-push-10.png
  [11]: ./media/enable-apple-push-notifications/mobile-services-ios-push-11.png
  [12]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step9.png
  [13]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step10.png
  [14]: ./media/enable-apple-push-notifications/mobile-services-ios-push-12.png
  [15]: ./media/enable-apple-push-notifications/mobile-services-ios-push-13.png
  [16]: ./media/enable-apple-push-notifications/mobile-services-ios-push-14.png
  [17]: ./media/enable-apple-push-notifications/mobile-services-ios-push-15.png
  [18]: ./media/enable-apple-push-notifications/mobile-services-ios-push-16.png
  [19]: ./media/enable-apple-push-notifications/mobile-services-ios-push-17.png
  [20]: ./media/enable-apple-push-notifications/mobile-services-ios-push-01.png
  [21]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step17.png
