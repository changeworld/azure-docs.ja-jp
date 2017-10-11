<!--author=alkohli last changed: 01/18/2017-->


#### <a name="to-configure-and-register-the-device"></a>構成し、デバイスを登録するには

1. StorSimple デバイスのシリアル コンソールに Windows PowerShell インターフェイスにアクセスします。 参照してください[デバイスのシリアル コンソールへの接続に使用する PuTTY](#use-putty-to-connect-to-the-device-serial-console)手順についてはします。 **プロシージャに厳密に従うことを確認してか、コンソールにアクセスすることはできません。**

2. 開いたセッションでキーを押します。 **Enter** 1 回のコマンド プロンプトを取得します。

3. デバイスの設定を希望する言語を選択するように促されます。 言語を指定し、 **Enter**です。

4. シリアル コンソール メニューが表示されますでオプション 1 を選択して**フル アクセスでログイン**です。
     手順 5-12、デバイスの最低限必要なネットワーク設定を構成します。 **これらの構成手順は、デバイスのアクティブなコント ローラー上で実行される必要があります。** シリアル コンソール メニューでは、バナー メッセージにコント ローラーの状態を示します。 アクティブ コント ローラーに接続していない場合は、切断し、アクティブ コント ローラーに接続します。

5. コマンド プロンプトでパスワードを入力します。 既定のデバイス パスワードは**Password1**です。

6. 次のコマンドを入力:`Invoke-HcsSetupWizard`です。

7. セットアップ ウィザードは、デバイスのネットワーク設定を構成する際に表示されます。 指定します、次の情報。
   
   * DATA 0 ネットワーク インターフェイスの IP アドレス
   * サブネット マスク
   * ゲートウェイ
   * プライマリ DNS サーバーの IP アドレス

   サンプル出力は次のとおりです。

    ```
        ---------------------------------------------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: 8100-SHX0991003G44MT
        Software Version: 6.3.9600.17759
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Active
        ---------------------------------------------------------------

        Your device needs to be registered with the Microsoft Azure StorSimple Manager service. Please run 'Invoke-HcsSetupWizard' to set up your device.

        Controller0>Invoke-HcsSetupWizard

        Which IP address family would you like to configure on interface Data0?
        [4] IPv4 [6] IPv6 [B] Both (Default is "4"): 4

        Data0 IPv4 address:10.111.111.00
        Data0 IPv4 subnet: 255.255.252.0
        Data0 IPv4 gateway: 10.111.111.11

        IPv4 primary DNS server [10.222.118.154]:10.222.222.111
    ```

    <br>
    上記のサンプル出力には、システムがプロセスの各手順の後にネットワーク設定を検証することがわかります。

     > [!NOTE]
     > サブネット マスクおよび DNS 設定を適用するのには数分待機する必要があります。 「Data 0 へのネットワーク接続をチェック」エラー メッセージを取得する場合は、アクティブなコント ローラーの DATA 0 ネットワーク インターフェイス上の物理ネットワーク接続を確認します。

8. (省略可能) web プロキシ サーバーを構成します。 Web プロキシ構成は省略可能、 **、web プロキシを使用する場合のみ構成できますがここに注意してください**です。 詳細についてを参照してください[、デバイスの web プロキシの構成](../articles/storsimple/storsimple-8000-configure-web-proxy.md)です。
9. デバイスのプライマリ NTP サーバーを構成します。 NTP サーバーが必要にクラウド サービス プロバイダーで認証できるように、デバイスでの時刻を同期する必要があります。 ネットワークが、データ センターからインターネットに渡す NTP トラフィックを許可することを確認します。 これが可能でない場合は、内部の NTP サーバーを指定します。

    サンプル出力は、以下に示します。

    ```
        Would you like to configure a web proxy?
        [Y] Yes [N] No (Default is "N"):N

        Primary NTP server [time.windows.com]:time.windows.com

    ```

10. セキュリティ上の理由から、デバイス管理者パスワードは、最初のセッション後に有効期限が切れるし、今すぐに変更する必要があります。 メッセージが表示されたらは、デバイス管理者パスワードを入力します。 有効なデバイス管理者パスワードは 8 ~ 15 文字でなければなりません。 パスワードは、次の 3 つを含める必要があります: 小文字、大文字、数字、および特殊文字。

    ```
        The device administrator password must be between 8 and 15 characters. The password must contain a combination of uppercase letters, lowercase letters, numbers and special characters.
        Administrator Password:********
        Confirm Administrator Password:********
    ```
11. セットアップ ウィザードの最後の手順では、StorSimple デバイス マネージャー サービスをデバイスを登録します。 これは、手順 2. で取得したサービス登録キーを必要があります。 登録キーを指定すると後、は、2 ~ 3 分間デバイスを登録する前に待機する必要があります。
    
    > [!NOTE]
    > セットアップ ウィザードを終了するには、いつでも、ctrl キーを押しながら C キーを押すことができます。 すべてのネットワーク設定 (Data 0、サブネット マスク、およびゲートウェイの IP アドレス) を入力した場合、エントリが保持されます。
    
    サンプル出力は、以下に示します。

    ```
        The service registration key is available in the StorSimple Manager service.
        Enter service registration key:**************************************
        Device registration is in progress. Please wait.

    ```

12. デバイスを登録した後、サービス データ暗号化キーが表示されます。 このキーをコピーし、安全な場所に保存します。 **このキーは、StorSimple デバイス マネージャー サービスに追加するデバイスを登録するサービス登録キーと共に必要になります。** 参照してください[StorSimple のセキュリティ](../articles/storsimple/storsimple-security.md)このキーの詳細についてはします。
    
    ![登録デバイスの StorSimple 7](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup1.png)
    
    > [!NOTE]
    > シリアル コンソール ウィンドウからテキストをコピーするには、テキストを選択します。 ことができます、クリップボードまたは任意のテキスト エディターに貼り付けます。 サービス データ暗号化キーをコピーするのには、ctrl キーを押しながら C を使用しないでください。 Ctrl + C を使用して実行するとがセットアップ ウィザードを終了します。 その結果、デバイス管理者パスワードは変更されませんし、デバイスは既定のパスワードに戻ります。
    
13. シリアル コンソールを終了します。
14. Azure ポータルに戻り、次の手順を完了しています。
    
    1. StorSimple デバイス Manager サービスに移動します。
    2. **[デバイス]**をクリックします。
    3. 表形式のリストで、デバイス、デバイスが正常に接続されていることをサービスの状態を参照することを確認します。 デバイスの状態にする必要があります**を設定する準備ができて**です。
       
        ![StorSimple デバイス ページ](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup2.png)
       
        デバイスの状態を変更するまで数分待つ必要があります**を設定する準備ができて**です。
       
        この一覧にデバイスが表示されないかどうかは、」の説明に従って、ファイアウォール ネットワークが構成されていることを確認する必要があります[、StorSimple デバイスのネットワーク要件](../articles/storsimple/storsimple-8000-system-requirements.md)です。 これが StorSimple デバイス マネージャーのサービスとデバイスの通信に service bus で使用されるので、ポート 9354 は発信通信用に開いてであることを確認します。

