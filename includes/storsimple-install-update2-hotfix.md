<!--author=alkohli last changed: 01/26/16-->

#### 修正プログラムをダウンロードするには

次の手順を実行して、Microsoft Update カタログからソフトウェア更新プログラムをダウンロードします。

1. Internet Explorer を起動し、[http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx) に移動します。

2. 初めて利用する場合は、Microsoft Update カタログをインストールするよう求められます。**[インストール]** をクリックします。
    
   	![カタログのインストール](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. カタログ検索画面が表示されます。検索ボックスに「**3121901**」と入力し、**[検索]** をクリックします。

    ![カタログの検索](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)

4. **Cumulative Software Bundle Update 2.0 for StorSimple 8000 Series** が表示されます。**[追加]** をクリックします。更新プログラムがバスケットに追加されます。

5. **[バスケットの表示]** をクリックします。
 
6. **[Download]** をクリックします。ダウンロード先となるローカルの場所を指定または**参照**します。更新プログラムは、選択した場所の (更新プログラムと同じ名前の) フォルダーにダウンロードされます。デバイスからアクセスできるネットワーク共有に、このフォルダーをコピーすることもできます。
       
	> [AZURE.NOTE] 
	> 
	> - さらに、**LSI ドライバーの更新プログラム** (SAS Controller Update 2.0 for StorSimple 8000 Series - KB3121900)、**Storport の更新プログラム** (Hotfix for Windows Server 2012 R2 x64 Edition - KB3080728)、**Spaceport の更新プログラム** (Hotfix for Windows Server 2012 R2 x64 Edition - KB3090322)、および **ディスク ファームウェアの更新プログラム** (Cumulative Disk Firmware Update 2.0 for StorSimple 8000 Series - KB3121899) をダウンロードして、同じ共有フォルダーにコピーする必要があります。
	> - 修正プログラムには、ピア コントローラーから潜在的なエラー メッセージをすべて検出するために両方のコントローラーからアクセス可能である必要があります。

#### 通常モードの修正プログラムをインストールして確認するには

通常の修正プログラムをインストールして確認するには、次の手順を実行します。

1. 修正プログラムをインストールするには、StorSimple デバイスのシリアル コンソールで Windows PowerShell インターフェイスにアクセスします。詳細については、[PuTTY を使用してシリアル コンソールに接続する方法](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)に関するセクションを参照してください。コマンド プロンプトで **Enter** キーを押します。

4. **[オプション 1]** を選択して、フル アクセスでデバイスにログオンします。

5. 修正プログラムをインストールするには、コマンド プロンプトで次のように入力します。

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    上記のコマンドの共有パスでは、DNS ではなく IP を使用します。Credential パラメーターは、認証済みの共有にアクセスする場合にのみ使用されます。

	共有にアクセスする場合は、Credential パラメーターを使用することをお勧めします。"すべてのユーザー" に解放されている共有であっても、通常は、非認証ユーザーには解放されません。

    サンプル出力を次に示します。

        ````
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John
      
        Confirm

        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not 
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y

        ````
 
6. 修正プログラムのインストールの確認を求められたら、「**Y**」と入力します。

7. `Get-HcsUpdateStatus` コマンドレットを使用して、更新プログラムを監視します。

    次のサンプル出力は、インストール中の更新プログラムを示しています。更新が進行中の場合、`RunInprogress` は `True` になります。

        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 12/21/2015 10:36:13 PM
        LastUpdateTimestamp : 12/21/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   : 
        ````
 
     次のサンプル出力は、更新が完了したことを示しています。更新が完了した場合、`RunInProgress` は `False` になります。

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 12/21/2015 10:59:13 PM
        LastUpdateTimestamp : 12/21/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````
		

	> [AZURE.NOTE] 場合によっては、更新がまだ進行中であっても、コマンドレットは `False` とレポートします。修正プログラムが完了したことを確認するには、数分待ってから、このコマンドを再実行し、`RunInProgress` が `False` になっていることを確認します。False の場合、修正プログラムは完了しています。
	
8. ソフトウェアの更新が完了した後、`CisMdsAgentUpdateBundle.exe` を使用して SaaS エージェントと MDS エージェントをインストールして監視するために手順 3 ～ 5 を繰り返します。`HcsMdsSoftwareUpdate.exe` が `CisMdsAgentUpdateBundle.exe` の前にインストールされていることを確認します。

9. システム ソフトウェアのバージョンを確認します。次のコマンドを入力します。

    `Get-HcsSystem`

    次のバージョンが表示されます。

    - HcsSoftwareVersion: 6.3.9600.17673
    - CisAgentVersion: 1.0.9150.0
    - MdsAgentVersion: 30.0.4698.13 
    
	更新プログラムの適用後にバージョン番号が変わらない場合は、修正プログラムの適用に失敗したことを示します。そのような場合は、[Microsoft サポート](storsimple-contact-microsoft-support.md)に連絡して、さらに支援を受けてください。
    
9. 残りの通常の修正プログラムをインストールして監視するために手順 3 ～ 5 を繰り返します。

	- `HcsLsiUpdate.exe` パッケージ (KB3121900) を使用する LSI ドライバー。
	- `Storport-KB3080728-x64.msu` パッケージ (KB3080728) を使用する Storport 修正プログラム。
	- `spaceport-KB3090322-x64.msu` パッケージ (KB3090322) を使用する Spaceport 修正プログラム。

#### メンテナンス モードの修正プログラムをインストールして確認するには

`DiskFirmwarePackage.exe` パッケージ (KB3121899) を使用して、ディスク ファームウェアの更新プログラムをインストールします。これらは中断を伴う更新プログラムであり、完了まで約 30 分かかります。デバイスのシリアル コンソールに接続することで、計画されたメンテナンス期間にこれらをインストールするよう選択できます。

ディスク ファームウェアが既に最新の状態になっている場合は、これらの更新プログラムをインストールする必要はありません。デバイスのシリアル コンソールから `Get-HcsUpdateAvailability` コマンドレットを実行します。更新プログラムが利用可能かどうかと、更新プログラムが中断を伴うか (メンテナンス モードの更新プログラム) 伴わないか (通常の更新プログラム) が通知されます。
 
ディスク ファームウェアの更新プログラムをインストールするには、次の指示に従います。

1. デバイスをメンテナンス モードにします。デバイスにメンテナンス モードで接続するときは、Windows PowerShell リモート処理を使用しないでください。このコマンドレットは、デバイス シリアル コンソールを通して接続したときにデバイス コントローラーで実行する必要があります。次のコマンドを入力します。
		
	`Enter-HcsMaintenanceMode`

	サンプル出力を次に示します。

		Controller0>Enter-HcsMaintenanceMode
		Checking device state...

		In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
		[Y] Yes [N] No (Default is "Y"): Y

		-----------------------MAINTENANCE MODE------------------------
		Microsoft Azure StorSimple Appliance Model 8100
		Name: Update2-8100-SHG0997879L76YD
		Software Version: 6.3.9600.17664
		Copyright (C) 2014 Microsoft Corporation. All rights reserved.
		You are connected to Controller0 - Passive
		---------------------------------------------------------------

		Serial Console Menu
		[1] Log in with full access
		[2] Log into peer controller with full access
		[3] Connect with limited access
		[4] Change language
		Please enter your choice>

	両方のコントローラーが再起動されます。再起動が完了すると、両方のコントローラーはメンテナンス モードになります。

3. ディスク ファームウェアの更新プログラムをインストールするには、次のように入力します。

	`Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

	サンプル出力を次に示します。

        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
		Enter Password:
		WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
		Confirm
		This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
		[Y] Yes [N] No (Default is "Y"): Y
		WARNING: Installation is currently in progress. This operation can take several minutes to complete.
	

1.  `Get-HcsUpdateStatus` コマンドを使用して、インストールの進行状況を監視します。更新が完了すると、`RunInProgress` が `False` に変わります。
 
2.  インストールが完了すると、メンテナンス モードの修正プログラムがインストールされたコントローラーが再起動されます。フル アクセスを持つオプション 1 としてログインし、ディスク ファームウェアのバージョンを確認します。次のコマンドを入力します。
	
	`Get-HcsFirmwareVersion`
  
	予想されるディスク ファームウェアのバージョンは次のとおりです。

	`XMGG, XGEG, KZ50, F6C2, VR08`

	サンプル出力を次に示します。


        -----------------------MAINTENANCE MODE------------------------
    	Microsoft Azure StorSimple Appliance Model 8100
    	Name: Update2-8100-SHG0997879L76YD
    	Software Version: 6.3.9600.17664
    	Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    	You are connected to Controller1
    	---------------------------------------------------------------
    	
    	Controller1>Get-HcsFirmwareVersion
    	
    	
    	Controller0 : TalladegaFirmware
    	  ActiveBIOS:0.45.0006
    	  BackupBIOS:0.45.0008
    	  MainCPLD:17.0.0005
    	  ActiveBMCRoot:2.0.000E
    	  BackupBMCRoot:2.0.000E
    	  BMCBoot:2.0.0001
    	  LsiFirmware:19.00.00.00
    	  LsiBios:07.37.00.00
    	  Battery1Firmware:06.29
    	  Battery2Firmware:06.29
    	  DomFirmware:X231600
    	  CanisterFirmware:3.5.0.32
    	  CanisterBootloader:5.03
    	  CanisterConfigCRC:0xD1B030A4
    	  CanisterVPDStructure:0x06
    	  CanisterGEMCPLD:0x17
    	  CanisterVPDCRC:0xEE3504B4
    	  MidplaneVPDStructure:0x0C
    	  MidplaneVPDCRC:0xA6BD4F64
    	  MidplaneCPLD:0x10
    	  PCM1Firmware:1.00|1.05
    	  PCM1VPDStructure:0x05
    	  PCM1VPDCRC:0x41BEF99C
    	  PCM2Firmware:1.00|1.05
    	  PCM2VPDStructure:0x05
    	  PCM2VPDCRC:0x41BEF99C
    	
    	  DisksFirmware
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG

	 2 番目のコントローラーで `Get-HcsFirmwareVersion` コマンドを実行して、ソフトウェアのバージョンが更新されたことを確認します。この後、メンテナンス モードを終了できます。各デバイス コントローラーに対して次のコマンドを入力します。

    `Exit-HcsMaintenanceMode`
     
1. メンテナンス モードを終了すると、コントローラーが再起動します。ディスク ファームウェアの更新プログラムが正常に適用され、デバイスがメンテナンス モードを終了したら、Azure クラシック ポータルに戻ります。メンテナンス モードの更新プログラムは、24 時間が経過するまで、ポータルでは更新されません。






 
 

<!---HONumber=AcomDC_0128_2016-->