<!--author=alkohli last changed: 12/01/15-->


#### <a name="to-configure-and-register-the-device"></a>デバイスを構成して登録するには
1. StorSimple デバイスのシリアル コンソールで Windows PowerShell インターフェイス にアクセスします。 方法については、「 [PuTTY を使用してデバイスのシリアル コンソールに接続する](#use-putty-to-connect-to-the-device-serial-console) 」を参照してください。 **必ず手順を正確に実行してください。そうしないと、コンソールにアクセスできません。**
2. 開いたセッションで、Enter キーを 1 回押して、コマンド プロンプトを開きます。 
3. デバイスに設定する言語を選択するように求められます。 言語を指定し、Enter キーを押します。 
   
    ![StorSimple によるデバイスの構成および登録 1](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice1-include.png)
4. 表示されるシリアル コンソール メニューで、オプション 1 を選択してフル アクセスでログインします。 
   
    ![StorSimple によるデバイスの登録 2](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice2-include.png)
   
     手順 5. ～ 12. に従って、デバイスで必要な最小のネットワーク設定を構成します。 **これらの構成手順は、デバイスのアクティブ コントローラーで実行する必要があります。** シリアル コンソール メニューでは、バナー メッセージにコントローラーの状態が示されます。 アクティブ コントローラーに接続されていない場合は、切断してアクティブ コントローラーに接続します。
5. コマンド プロンプトにパスワードを入力します。 デバイスの既定のパスワードは **Password1**です。
6. 次のコマンドを入力します。
   
     `Invoke-HcsSetupWizard` 
7. デバイスのネットワーク設定の構成に役立つセットアップ ウィザードが表示されます。 次の情報を指定します。 
   
   * DATA 0 ネットワーク インターフェイスの IP アドレス
   * サブネット マスク
   * ゲートウェイ
   * プライマリ DNS サーバーの IP アドレス
   * プライマリ NTP サーバーの IP アドレス
     
     > [!NOTE]
     > サブネット マスクおよび DNS 設定が適用されるまでに数分かかる場合があります。 "デバイスの準備ができていません" という エラー メッセージが表示された場合は、アクティブ コントローラーの DATA 0 ネットワーク インターフェイスの物理ネットワーク接続を確認します。
     > 
     > 
8. (省略可能) Web プロキシ サーバーを構成します。 Web プロキシの構成は省略可能ですが、 **Web プロキシを使用する場合は、ここでのみ構成できることに注意してください**。 詳細については、「 [デバイスの Web プロキシの構成](../articles/storsimple/storsimple-configure-web-proxy.md)」を参照してください。 この手順で問題が発生した場合、トラブルシューティング ガイダンスの「 [Web プロキシ構成中のエラー](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-the-optional-web-proxy-settings)」を参照してください。

     > [!NOTE]
     > Ctrl キーを押しながら C キーを押すことで、いつでもセットアップ ウィザードを終了できます。 このコマンドを発行する前に適用されたすべての設定は保持されます。

1. セキュリティ上の理由で、デバイス管理者のパスワードは最初のセッション後に期限が切れるため、以降のセッションでは変更する必要があります。 画面の指示に従って、デバイスの管理者パスワードを入力します。 デバイス管理者の有効なパスワードの長さは、8 ～ 15 文字です。 大文字、小文字、数字、および特殊文字を組み合わせたパスワードを使用してください。
2. StorSimple Snapshot Manager のパスワードもここで設定します。 StorSimple Snapshot Manager が実行されている Windows ホストに対してデバイスを認証する場合は、このパスワードを使用します。 入力を求められたら、14 ～ 15 文字のパスワードを入力します。 パスワードには、小文字、大文字、数字、および特殊文字のうち 3 種類の文字を組み合わせる必要があります。 
   
   ![StorSimple によるデバイスの登録 4](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice4-include.png)
   
   StorSimple Snapshot Manager のパスワードは StorSimple Manager サービス インターフェイスからリセットできます。 詳しい手順については、「 [StorSimple Manager サービスを利用して StorSimple パスワードを変更する](../articles/storsimple/storsimple-change-passwords.md)」を参照してください。
   
   この手順で発生した問題を解消する場合、トラブルシューティング ガイダンスの「 [パスワード関連のエラー](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords)」を参照してください。
3. セットアップ ウィザードの最後の手順では、お使いのデバイスを StorSimple Manager サービスに登録します。 そのためには、手順 2. で取得したサービス登録キーが必要です。 登録キーを指定したら、デバイスが登録されるまでに 2 ～ 3 分かかる場合があります。
   
   デバイス登録関連のエラーを解消するには、「 [デバイス登録中のエラー](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-device-registration)」を参照してください。 トラブルシューティングの詳細については、「 [ステップ バイ ステップ トラブルシューティング例](../articles/storsimple/storsimple-troubleshoot-deployment.md#step-by-step-storsimple-troubleshooting-example)」も参照してください。
4. デバイスが登録されると、サービス データ暗号化キーが表示されます。 このキーをコピーし、安全な場所に保存しておきます。
   
   > [!WARNING]
   > このキーは、StorSimple Manager サービスに追加のデバイスを登録するために、サービス登録キーと共に必要になります。 このキーの詳細については、「 [StorSimple のセキュリティ](../articles/storsimple/storsimple-security.md) 」を参照してください。
   > 
   > 
   
    ![StorSimple によるデバイスの登録 6](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice6-include.png)
   
    シリアル コンソール ウィンドウからテキストをコピーするには、単にテキストを選択します。 その状態で、クリップボードまたは任意のテキスト エディターに貼り付けることができます。 サービス データ暗号化キーをコピーするときには、Ctrl キーを押しながら C キーを押さないでください。 Ctrl キーを押しながら C キーを押すと、セットアップ ウィザードが終了します。 その場合、デバイスの管理者パスワードおよび StorSimple Snapshot Manager のパスワードは変更されず、デバイスは既定のパスワードに戻ります。
5. シリアル コンソールを終了します。
6. Azure クラシック ポータルに戻り、次の手順を実行します。
   
   1. StorSimple Manager サービスをダブルクリックして **[クイック スタート]** ページにアクセスします。
   2. **[接続されたデバイスの表示]**をクリックします。
   3. **[デバイス]** ページで、状態を参照して、デバイスが正常にサービスに接続されていることを確認します。 デバイスの状態は **"オンライン"**と表示されます。 デバイスの状態が **"オフライン"**の場合は、デバイスがオンラインになるまで数分待ちます。
   
   ![StorSimple デバイス ページ](./media/storsimple-configure-and-register-device/HCS_DevicesPageM-include.png) 
   
   > [!IMPORTANT]
   > デバイスがオンラインになったら、この手順の最初で取り外したネットワーク ケーブルを取り付けます。
   > 
   > 

デバイスを登録したが、オンラインにならない場合、 `Test-HcsmConnection -Verbose` を実行し、ネットワーク接続の正常な状態を確認できます。 このコマンドレットの詳しい利用については、「 [Test-HcsmConnection のコマンドレット リファレンス](https://technet.microsoft.com/library/dn715782.aspx)」を参照してください。

![ビデオ](./media/storsimple-configure-and-register-device/Video_icon.png) **ビデオ**

StorSimple 用 Windows PowerShell でデバイスを構成および登録する方法を説明したビデオについては、 [こちら](https://azure.microsoft.com/documentation/videos/initialize-the-storsimple-appliance/)を参照してください。

