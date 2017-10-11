<!--author=alkohli last changed: 9/17/15-->

#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>最小の StorSimple デバイスのセットアップを完了するには
1. デバイスを選択し、をクリックして**クイック スタート**です。 をクリックして**デバイスのセットアップを完了**をデバイスの構成ウィザードを開始します。
2. デバイスの構成ウィザードで**基本設定** ダイアログ ボックスで、次の操作します。
   
   1. 指定、**フレンドリ名**デバイス用です。 既定のデバイス名には、デバイスのモデルやシリアル番号などの情報が反映されます。 デバイスを管理するを最大 64 文字のフレンドリ名を割り当てることができます。
   2. 設定、**タイム ゾーン**デバイスが展開されている地理的な場所に基づきます。 デバイスでは、このタイム ゾーンをすべてのスケジュールされた操作を使用します。
   3. **DNS 設定**、アドレスを指定して**セカンダリ DNS サーバー**です。 IPv6 を使用している場合、Windows PowerShell インターフェイスで提供される IPv6 プレフィックスに基づいて、フィールドに表示されます。 
      セカンダリ DNS サーバーが構成されていない場合はことできませんデバイスの構成を保存します。
   4. 有効な iSCSI インターフェイスで iSCSI 用に少なくとも 1 つのネットワークを有効にします。 少なくとも 1 つのネットワーク インターフェイスは、クラウドが有効にする必要があるし、1 つのインターフェイスを iSCSI を有効にする必要があります。 DATA 0 は、自動的にクラウド対応です。
      
      ![StorSimple デバイスの最小セットアップの基本設定](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupBasicSettings1-include.png)
3. 矢印アイコンをクリックします。 ![StorSimple 矢印アイコン](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png)
4. **ネットワーク インターフェイス** ダイアログ ボックスでコント ローラー 0 とコント ローラー 1 の固定の IP アドレスを指定します。 **コント ローラー固定 IP アドレスは、デバイスの IP アドレスでアクセス可能なサブネット内にある空き ip アドレスである必要があります。** かどうか、DATA 0 インターフェイスが IPv4 に対して構成された、固定の IP アドレスはならない IPv4 形式で指定します。 IPv6 構成に対して、プレフィックスを指定した場合は、これらのフィールドに固定の IP アドレスを自動的に入力されますはします。

    ![StorSimple 最小デバイス セットアップ ネットワーク インターフェイス](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

    コント ローラーの固定の IP アドレスは、デバイスに更新プログラムの提供使用され、したがって固定の ip アドレスがありますなルーティングと、インターネットに接続できません。 使用して、コント ローラー固定 Ip がルーティング可能なことを確認することができます、 [Test-hcsmconnection] [ Test]コマンドレット。 次の例は、固定のコント ローラー Ip、インターネットにルーティングされ、Microsoft Update サーバーにアクセスできます。 

     ![Test-hcsmconnection ルーティング可能な ip アドレスを表示](./media/storsimple-complete-minimum-device-setup-u1/Test-HcsmConnectionOutputRegisteredDevice.png)

1. チェック アイコンをクリックして![StorSimple のチェック マーク アイコン](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png)です。
   デバイスが再び表示**クイック スタート**ページ。
   
   > [!NOTE]
   > アクセスして、いつでも他のすべてのデバイス設定を変更することができます、**構成**ページ。
   > 
   > 

<!--Link reference-->
[Test]: https://technet.microsoft.com/library/dn715782(v=wps.630).aspx