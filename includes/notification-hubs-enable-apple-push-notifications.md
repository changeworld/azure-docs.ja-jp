

## <a name="generate-the-certificate-signing-request-file"></a>証明書署名要求ファイルを生成します。
Apple Push Notification サービス (APNS) では、証明書を使用して、プッシュ通知を認証します。 通知の送受信に必要なプッシュ証明書を作成する手順に従います。 これらの概念の詳細については、公式を参照してください。 [Apple Push Notification サービス](http://go.microsoft.com/fwlink/p/?LinkId=272584)ドキュメント。

Apple によって署名されたプッシュ証明書を生成するために使用する証明書署名要求 (CSR) ファイルを生成します。

1. Mac でキーチェーン アクセス ツールを実行します。 開くことができます、**ユーティリティ**フォルダーまたは**他**起動パッド上のフォルダーです。
2. をクリックして**キーチェーン アクセス**、展開**証明書アシスタント**、順にクリックして**証明機関から証明書を要求しています.**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. 選択、**ユーザーの電子メール アドレス**と**共通名**、ことを確認して**をディスクに保存された**を選択して、をクリックして**続行**です。 ままにして、 **CA 電子メール アドレス**フィールドを空白のように必須ではありません。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)
4. 証明書署名要求 (CSR) ファイルの名前を入力**名前を付けて保存**、内の場所を選択**場所**、順にクリックして**保存**です。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)
   
      これが選択された場所に CSR ファイルを保存します。既定の場所は、デスクトップでです。 このファイルの選択した場所を覚えておいてください。

次に、Apple にアプリを登録、プッシュ通知を有効にする、およびプッシュ証明書を作成するエクスポートされたこの CSR がアップロードされます。

## <a name="register-your-app-for-push-notifications"></a>プッシュ通知用のアプリを登録します。
IOS アプリにプッシュ通知を送信できるようにするには、Apple にアプリケーションを登録する必要がありますしてもプッシュ通知を登録します。  

1. アプリを登録していない場合に移動、 <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS プロビジョニング ポータル</a>は Apple のデベロッパー センターの Apple ID で、ログに次のようにクリックします。**識別子**、[] をクリック**のアプリ Id**、最後に [] をクリックし、  **+** サインインすると、新しいアプリを登録します。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)
      
2. 新しいアプリの次の 3 つのフィールドを更新し、をクリックして**続行**:
   
   * **名前**: でのアプリのわかりやすい名前を入力、**名前**フィールドで、**アプリ ID の説明**セクションです。
   * **バンドル識別子**: 下にある、**アプリ ID の明示的な**セクションで、入力、**バンドル Id**形式で`<Organization Identifier>.<Product Name>`で説明したように、[アプリの配布のガイド](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8)です。 *組織 Id*と*製品名*必要がありますを使用する、XCode プロジェクトを作成するときに使用する組織の識別子と製品名に一致します。 Screeshot 下で*NotificationHubs*組織 idenitifier として使用されると*初めましょう*製品名として使用されます。 これは XCode プロジェクトで使用する値と一致かどうかを必ず使用すると、XCode で、適切な発行プロファイルを使用できます。 
   * **プッシュ通知**: チェック、 **Push Notifications**オプション、 **App Services**  セクションで、します。
     
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)
     
      これにより、アプリ ID を生成し、情報を確認することを要求します。 をクリックして**登録**新しいアプリ ID を確認するには
     
      クリックすると**登録**が表示されます、**登録完了**画面で、次のようにします。 **[完了]** をクリックします。
      
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)


1. アプリ Id では、デベロッパー センターにだけ作成した、特定の行をクリックしてアプリ ID を探します。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)
   
      アプリ ID をクリックすると、アプリの詳細が表示されます。 クリックして、**編集**下部にあるボタンをクリックします。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)
      
2. 画面の下部までスクロールし、をクリックして、**証明書を作成しています.**セクションの下のボタン**開発プッシュ SSL 証明書**です。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)
   
      これには、「iOS の証明書を追加」アシスタントが表示されます。
   
   > [!NOTE]
   > このチュートリアルでは、開発の証明書を使用します。 運用証明書を登録するときに、同じプロセスが使用されます。 のみ通知を送信する場合は、同じ証明書の種類を使用することを確認します。
   > 
   > 
3. をクリックして**Choose File**最初のタスクで作成した CSR ファイルを保存した場所に移動し、クリックして**生成**です。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)
4. ポータルで証明書を作成したら、**ダウンロード**ボタンをクリックし、をクリックして**完了**です。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)
   
      これにより、証明書をダウンロードし、ダウンロード フォルダー内のコンピューターに保存します。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)
   
   > [!NOTE]
   > 既定では、ダウンロードしたファイルの開発の証明書の名前は**aps_development.cer**です。
   > 
   > 
5. ダウンロードしたプッシュの証明書をダブルクリックして**aps_development.cer**です。
   
      これは、新しい証明書をインストール、キーチェーンに次のように。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)
   
   > [!NOTE]
   > 証明書の名前が異なる可能性がありますが、それを付けます**Apple 開発 iOS プッシュ サービス:**です。
   > 
   > 
6. Keychain Access で作成した新しいプッシュ証明書を右クリックし、**証明書**カテゴリ。 をクリックして**エクスポート**をファイルに名前を選択、 **.p12**書式を設定して、をクリックして**保存**です。
   
    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)
   
    ファイル名をメモし、エクスポートされた .p12 証明書の場所を確認します。 これは、APNS で認証を有効に使用されます。
   
   > [!NOTE]
   > このチュートリアルでは、QuickStart.p12 ファイルを作成します。 ファイル名と場所が異なる可能性があります。
   > 
   > 

## <a name="create-a-provisioning-profile-for-the-app"></a>プロビジョニング プロファイルをアプリを作成します。
1. 戻り、 <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS プロビジョニング ポータル</a>を選択**プロビジョニング プロファイル**を選択**すべて**、をクリックし、  **+** 新しいプロファイルを作成するボタンをクリックします。 これにより、起動、 **iOS Provisiong プロファイルを追加**ウィザード
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)
2. 選択**iOS アプリの開発****開発**provisiong プロファイルの種類、およびクリックとして**続行**です。 
3. 作成したアプリ ID を次に、選択、**アプリ ID**ドロップダウン リスト、およびクリック**続行**
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)
4. **証明書の選択**画面で、コード署名に、使用する一般的な開発証明書をクリックし、をクリックして**続行**です。 これは、作成したプッシュの証明書ではありません。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)
5. 次に、選択、**デバイス**を使用してテストし、をクリックして**続行**
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)
6. プロファイルの名前を最後に、選択**プロファイル名**をクリックして**生成**です。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
7. 新しいプロビジョニング プロファイルの作成時に、ダウンロードし、Xcode 開発用コンピューターにインストールする をクリックします。 をクリックして**完了**です。
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)
