
#### StorSimple 用 Windows PowerShell を使用して Update 1 をインストールするには

1. 次の手順を実行して、ソフトウェア更新プログラムをダウンロードします。

    1. Internet Explorer を起動し、[http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx) に移動します。
    2. 初めて利用する場合は、Microsoft Update カタログをインストールするよう求められます。**[インストール]** をクリックします。
    
        ![Install catalog](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)

    3. カタログ検索画面が表示されます。検索ボックスに「**3063418**」と入力し、**[検索]** をクリックします。

        ![Search catalog](./media/storsimple-install-update-option-1/HCS_SearchCatalog-include.png)

    4. **StorSimple Update 1.0 Appliance Update** bundle が表示されます。**[追加]** をクリックします。更新プログラムがバスケットに追加されます。

        ![Update bundle](./media/storsimple-install-update-option-1/HCS_UpdateBundle-include.png)

    5. **[バスケットの表示]** をクリックします。
 
        ![View basket](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)

    6. **[ダウンロード]** をクリックします。ダウンロード先となるローカルの場所を指定または参照します。更新プログラム (all-hcsmdssoftwareupdate_288da2cc8cd2e3c3958b603a79346cb586fb8fe3.exe) が、選択した場所の StorSimple Update 1.0 Appliance Update bundle (KB3063418) フォルダーにダウンロードされます。デバイスからアクセスできるネットワーク共有に、このフォルダーをコピーすることもできます。
        
2. ソフトウェア更新プログラムをインストールするには、StorSimple デバイスのシリアル コンソールで Windows PowerShell インターフェイスにアクセスします。詳細については、[PuTTY を使用してシリアル コンソールに接続する方法](#use-putty-to-connect-to-the-serial-console)に関するセクションを参照してください。

3. コマンド プロンプトで Enter キーを押します。

4. **[オプション 1]** を選択して、フル アクセスでデバイスにログオンします。

5. 更新プログラム パッケージをインストールするには、コマンド プロンプトで次のように入力します。

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    Credential パラメーターは、認証済みの共有にアクセスする場合にのみ使用されます。

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

7. Get-HcsUpdateStatus コマンドレットを使用して、更新プログラムを監視します。

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

        ````
 
8. ソフトウェアの更新が完了したら、管理ポータルの [メンテナンス] ページに移動します。利用可能な更新プログラムをスキャンします。さらに複数のソフトウェア更新プログラムを利用できることがわかります。

9. **[更新プログラムのインストール]** をクリックして、ポータルから利用可能なソフトウェア更新プログラムをすべて適用します。

10. ソフトウェアの更新が完了したら、システムのソフトウェア、ドライバー、ファームウェアのバージョンを確認します。次のコマンドを入力します。

    `Get-HcsSystem`

    次のバージョンが表示されます。

    - HcsSoftwareVersion: 6.3.9600.17491
    - CisAgentVersion: 1.0.9037.0
    - MdsAgentVersion: 26.0.4696.1433 
 
11. ファームウェアが正しく更新されたことを確認するには、次のように入力します。

    `Start-HcsFirmwareCheck`

    ファームウェアの状態が **UpToDate** になっています。

<!---HONumber=July15_HO2-->