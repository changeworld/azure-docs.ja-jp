<!--author=alkohli last changed: 12/01/15-->


#### <a name="to-configure-and-register-the-device"></a>構成し、デバイスを登録するには
1. StorSimple デバイスのシリアル コンソールに Windows PowerShell インターフェイスにアクセスします。 参照してください[デバイスのシリアル コンソールへの接続に使用する PuTTY](#use-putty-to-connect-to-the-device-serial-console)手順についてはします。 **プロシージャに厳密に従うことを確認してか、コンソールにアクセスすることはできません。**
2. 開いたセッションで Enter キーを押します 1 回のコマンド プロンプトを取得します。 
3. デバイスの設定を希望する言語を選択するように促されます。 言語を指定し、Enter キーを押します。 
   
    ![StorSimple を構成するデバイスと登録 1](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice1-include.png)
4. シリアル コンソール メニューが表示されますのフル アクセスでログオンするオプション 1 を選択します。 
   
    ![StorSimple デバイス 2 を登録](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice2-include.png)
   
     手順 5-12、デバイスの最低限必要なネットワーク設定を構成します。 **これらの構成手順は、デバイスのアクティブなコント ローラー上で実行される必要があります。** シリアル コンソール メニューでは、バナー メッセージにコント ローラーの状態を示します。 アクティブ コント ローラーに接続していない場合は、切断し、アクティブ コント ローラーに接続します。
5. コマンド プロンプトでパスワードを入力します。 既定のデバイス パスワードは**Password1**です。
6. 次のコマンドを入力します。
   
     `Invoke-HcsSetupWizard` 
7. セットアップ ウィザードは、デバイスのネットワーク設定を構成する際に表示されます。 指定します、次の情報。 
   
   * DATA 0 ネットワーク インターフェイスの IP アドレス
   * サブネット マスク
   * ゲートウェイ
   * プライマリ DNS サーバーの IP アドレス
   * プライマリ NTP サーバーの IP アドレス
     
     > [!NOTE]
     > サブネット マスクおよび DNS 設定を適用するのには数分待機する必要があります。 場合は、「デバイスができていません」を取得します。 エラー メッセージで、アクティブなコント ローラーの DATA 0 ネットワーク インターフェイスの物理的なネットワーク接続を確認します。
     > 
     > 
8. (省略可能) web プロキシ サーバーを構成します。 Web プロキシ構成は省略可能、 **、web プロキシを使用する場合のみ構成できますがここに注意してください**です。 詳細についてを参照してください[、デバイスの web プロキシの構成](../articles/storsimple/storsimple-configure-web-proxy.md)です。 この手順で問題に実行する場合のトラブルシューティングのための指針を参照してください。 [web プロキシ構成中にエラー](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-the-optional-web-proxy-settings)です。

     > [!NOTE]
     > セットアップ ウィザードを終了するには、いつでも、ctrl キーを押しながら C キーを押すことができます。 このコマンドを発行する前に適用するすべての設定は保持されます。

1. セキュリティ上の理由から、デバイス管理者パスワードは、最初のセッション後に有効期限が切れるし、以降のセッションを変更する必要があります。 メッセージが表示されたらは、デバイス管理者パスワードを入力します。 有効なデバイス管理者パスワードは 8 ~ 15 文字でなければなりません。 パスワードは、小文字、大文字、数字、および特殊文字の組み合わせを含める必要があります。
2. ここでは、StorSimple Snapshot Manager のパスワードも設定されます。 StorSimple Snapshot Manager を実行している Windows ホストでデバイスを認証する場合は、このパスワードを使用します。 メッセージが表示されたらは、14 ~ 15 文字のパスワードを入力します。 パスワードは、次の 3 つの組み合わせを含める必要があります: 小文字、大文字、数字、および特殊文字。 
   
   ![StorSimple デバイス 4 を登録](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice4-include.png)
   
   StorSimple Manager サービスのインターフェイスから StorSimple Snapshot Manager パスワードをリセットすることができます。 詳細な手順についてを参照してください[StorSimple Manager サービスを使用して StorSimple パスワードを変更して](../articles/storsimple/storsimple-change-passwords.md)です。
   
   この手順で問題のトラブルシューティング、トラブルシューティングのガイダンスを参照してください[パスワードに関連するエラー](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords)です。
3. セットアップ ウィザードの最後の手順では、StorSimple Manager サービスにデバイスを登録します。 これは、手順 2. で取得したサービス登録キーを必要があります。 登録キーを指定すると後、は、2 ~ 3 分間デバイスを登録する前に待機する必要があります。
   
   潜在的なデバイスの登録エラーをトラブルシューティングするを参照してください[デバイスの登録中にエラー](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-device-registration)です。 トラブルシューティングについては、参照することできますも[ステップ バイ ステップのトラブルシューティングの例](../articles/storsimple/storsimple-troubleshoot-deployment.md#step-by-step-storsimple-troubleshooting-example)です。
4. デバイスを登録した後、サービス データ暗号化キーが表示されます。 このキーをコピーし、安全な場所に保存します。
   
   > [!WARNING]
   > このキーは、StorSimple Manager サービスに追加のデバイスを登録するサービス登録キーと共に必要になります。 参照してください[StorSimple のセキュリティ](../articles/storsimple/storsimple-security.md)このキーの詳細についてはします。
   > 
   > 
   
    ![StorSimple デバイス 6 を登録](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice6-include.png)
   
    シリアル コンソール ウィンドウからテキストをコピーするには、テキストを選択します。 ことができます、クリップボードまたは任意のテキスト エディターに貼り付けます。 サービス データ暗号化キーをコピーするのには、ctrl キーを押しながら C を使用しないでください。 Ctrl + C を使用して実行するとがセットアップ ウィザードを終了します。 その結果、デバイス管理者パスワードと StorSimple Snapshot Manager のパスワードは変更されませんし、デバイスは既定のパスワードに戻ります。
5. シリアル コンソールを終了します。
6. Azure クラシック ポータルに戻り、次の手順を完了しています。
   
   1. StorSimple Manager サービスにアクセスする をダブルクリック、**クイック スタート**ページ。
   2. をクリックして**ビューには、デバイスが接続されている**です。
   3. **デバイス** ページで、デバイスが正常に接続されていることをサービスの状態を参照することを確認します。 デバイスの状態にする必要があります**オンライン**です。 デバイスの状態がある場合**オフライン**、オンラインになるデバイスの数分待ちます。
   
   ![StorSimple デバイス ページ](./media/storsimple-configure-and-register-device/HCS_DevicesPageM-include.png) 
   
   > [!IMPORTANT]
   > デバイスがオンライン後、この手順の最初に切断されていたネットワーク ケーブルを接続します。
   > 
   > 

デバイスが正常に登録して、オンラインになるが、後に行うことができます、`Test-HcsmConnection -Verbose`ネットワーク接続が異常な状態であることを確認します。 このコマンドレットの詳細な利用状況を参照してください[Test-hcsmconnection コマンドレット リファレンス](https://technet.microsoft.com/library/dn715782.aspx)です。

![使用可能なビデオ](./media/storsimple-configure-and-register-device/Video_icon.png)**ビデオ**

構成して、StorSimple 用 Windows PowerShell を使用して、デバイスを登録する方法を示すビデオを見るには、をクリックして[ここ](https://azure.microsoft.com/documentation/videos/initialize-the-storsimple-appliance/)です。

