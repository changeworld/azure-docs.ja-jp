<!--author=SharS last changed: 02/22/16-->

### <a name="to-configure-and-register-the-device"></a>デバイスを構成して登録するには
1. StorSimple デバイスのシリアル コンソールで Windows PowerShell インターフェイス にアクセスします。 方法については、「 [PuTTY を使用してデバイスのシリアル コンソールに接続する](#use-putty-to-connect-to-the-device-serial-console) 」を参照してください。 **必ず手順を正確に実行してください。そうしないと、コンソールにアクセスできません。**
2. 開いたセッションで、Enter キーを 1 回押して、コマンド プロンプトを開きます。 
3. デバイスに設定する言語を選択するように求められます。 言語を指定し、Enter キーを押します。 
   
    ![StorSimple によるデバイスの構成および登録 1](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice1-gov-include.png)
4. 表示されるシリアル コンソール メニューで、オプション 1 を選択してフル アクセスでログインします。 
   
    ![StorSimple によるデバイスの登録 2](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice2-gov-include.png)
5. 次の手順を実行して、デバイスで必要な最小のネットワーク設定を構成します。
   
   > [!IMPORTANT]
   > これらの構成手順は、デバイスのアクティブ コントローラーで実行する必要があります。 シリアル コンソール メニューでは、バナー メッセージにコントローラーの状態が示されます。 アクティブ コントローラーに接続されていない場合は、切断してアクティブ コントローラーに接続します。
   > 
   > 
   
   1. コマンド プロンプトにパスワードを入力します。 デバイスの既定のパスワードは **Password1**です。
   2. 次のコマンドを入力します。
      
        `Invoke-HcsSetupWizard`
   3. デバイスのネットワーク設定の構成に役立つセットアップ ウィザードが表示されます。 次の情報を指定します。 
      
      * DATA 0 ネットワーク インターフェイスの IP アドレス
      * サブネット マスク
      * ゲートウェイ
      * プライマリ DNS サーバーの IP アドレス
      * プライマリ NTP サーバーの IP アドレス
      
      > [!NOTE]
      > サブネット マスクおよび DNS 設定が適用されるまでに数分かかる場合があります。 
      > 
      > 
   4. 必要に応じて、Web プロキシ サーバーを構成します。
      
      > [!IMPORTANT]
      > Web プロキシの構成は省略可能ですが、Web プロキシを使用する場合は、ここでのみ構成できることに注意してください。 詳細については、「 [デバイスの Web プロキシの構成](../articles/storsimple/storsimple-configure-web-proxy.md)」を参照してください。 
      > 
      > 
6. Ctrl キーを押しながら C キーを押してセットアップ ウィザードを終了します。
7. 次のように、更新プログラムをインストールします。
   
   1. 次のコマンドレットを使用して、両方のコントローラーに IP を設定します。
      
      `Set-HcsNetInterface -InterfaceAlias Data0 -Controller0IPv4Address <Controller0 IP> -Controller1IPv4Address <Controller1 IP>`
   2. コマンド プロンプトで、 `Get-HcsUpdateAvailability`を実行します。 更新プログラムを使用できることが通知されます。
   3. `Start-HcsUpdate` を実行します。 このコマンドは、任意のノードで実行できます。 更新プログラムが最初のコントローラーに適用され、コントローラーがフェールオーバーします。次に、もう 1 つのコントローラーに更新が適用されます。
      
      `Get-HcsUpdateStatus` を実行すると、更新プログラムの進行状況を監視できます。    
      
      次のサンプル出力は、インストール中の更新プログラムを示しています。
      
      ````
      Controller0>Get-HcsUpdateStatus
      RunInprogress       : True
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   : 
      ````
      
      次のサンプル出力は、更新が完了したことを示しています。
      
      ````
      Controller1>Get-HcsUpdateStatus
      
      RunInprogress       : False
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   :
      
      It may take up to 11 hours to apply all the updates, including the Windows Updates.
8. After all the updates are successfully installed, run the following cmdlet to confirm that the software updates were applied correctly:
   
     `Get-HcsSystem`
   
    You should see the following versions:
   
   * HcsSoftwareVersion: 6.3.9600.17491
   * CisAgentVersion: 1.0.9037.0
   * MdsAgentVersion: 26.0.4696.1433
9. Run the following cmdlet to confirm that the firmware update was applied correctly:
   
    `Start-HcsFirmwareCheck`.
   
     The firmware status should be **UpToDate**.
10. Run the following cmdlet to point the device to the Microsoft Azure Government portal (because it points to the public Azure classic portal by default). This will restart both controllers. We recommend that you use two PuTTY sessions to simultaneously connect to both controllers so that you can see when each controller is restarted.
    
     `Set-CloudPlatform -AzureGovt_US`
    
    You will see a confirmation message. Accept the default (**Y**).
11. Run the following cmdlet to resume setup:
    
     `Invoke-HcsSetupWizard`
    
     ![Resume setup wizard](./media/storsimple-configure-and-register-device-gov/HCS_ResumeSetup-gov-include.png)
    
    When you resume setup, the wizard will be the Update 1 version (which corresponds to version 17469). 
12. Accept the network settings. You will see a validation message after you accept each setting.
13. For security reasons, the device administrator password expires after the first session, and you will need to change it now. When prompted, provide a device administrator password. A valid device administrator password must be between 8 and 15 characters. The password must contain three of the following: lowercase, uppercase, numeric, and special characters.
    
    <br/>![StorSimple register device 5](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice5_gov-include.png)
14. The final step in the setup wizard registers your device with the StorSimple Manager service. For this, you will need the service registration key that you obtained in [Step 2: Get the service registration key](#step-2-get-the-service-registration-key). After you supply the registration key, you may need to wait for 2-3 minutes before the device is registered.
    
    > [!NOTE]
    > You can press Ctrl + C at any time to exit the setup wizard. If you have entered all the network settings (IP address for Data 0, Subnet mask, and Gateway), your entries will be retained.
    > 
    > 
    
    ![StorSimple registration progress](./media/storsimple-configure-and-register-device-gov/HCS_RegistrationProgress-gov-include.png)
15. After the device is registered, a Service Data Encryption key will appear. Copy this key and save it in a safe location. **This key will be required with the service registration key to register additional devices with the StorSimple Manager service.** Refer to [StorSimple security](../articles/storsimple/storsimple-security.md) for more information about this key.
    
    ![StorSimple register device 7](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice7_gov-include.png)    
    
    > [!IMPORTANT]
    > To copy the text from the serial console window, simply select the text. You should then be able to paste it in the clipboard or any text editor. 
    > 
    > DO NOT use Ctrl + C to copy the service data encryption key. Using Ctrl + C will cause you to exit the setup wizard. As a result, the device administrator password will not be changed and the device will revert to the default password.
    > 
    > 
16. Exit the serial console.
17. Return to the Azure Government Portal, and complete the following steps:
    
    1. Double-click your StorSimple Manager service to access the **Quick Start** page.
    2. Click **View connected devices**.
    3. On the **Devices** page, verify that the device has successfully connected to the service by looking up the status. The device status should be **Online**.
       
        ![StorSimple Devices page](./media/storsimple-configure-and-register-device-gov/HCS_DeviceOnline-gov-include.png) 
       
        If the device status is **Offline**, wait for a couple of minutes for the device to come online. 
       
        If the device is still offline after a few minutes, then you need to make sure that your firewall network was configured as described in [networking requirements for your StorSimple device](../articles/storsimple/storsimple-system-requirements.md). 
       
        Verify that port 9354 is open for outbound communication as this is used by the service bus for StorSimple Manager service-to-device communication.



<!--HONumber=Nov16_HO3-->


