
#### <a name="to-configure-remote-management-on-cloud-appliance"></a>クラウド アプライアンスのリモート管理を構成するには

1. クリックして、StorSimple デバイス マネージャー サービスで**デバイス**です。 サービスに接続されているデバイスの一覧から、クラウド アプライアンスをクリックします。
    ![StorSimple クラウド アプライアンス](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage1.png)

2. 移動して**設定 > セキュリティ**を開くには、**セキュリティ設定**ブレードです。

     ![StorSimple のセキュリティ設定](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage2.png)

3. 移動して、**リモート管理**セクションです。 をクリックして**リモート管理**ボックス。
     ![StorSimple リモート manangement](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage3.png)

4. **リモート管理**ブレード。

    1. 確認**リモート管理を有効にする**を有効にします。
    2. 既定では、HTTPS 経由で接続します。 HTTP を使用して接続を選択できます。 HTTP 経由の接続は、信頼されたネットワークでのみ許容はします。 HTTP が有効になっていることを確認します。
    3. ブレードの上部にあるコマンド バーでをクリック**しています.詳細** をクリックし、**証明書のダウンロード**リモート管理証明書をダウンロードします。 このファイルを保存する場所を指定することができます。 クラウド アプライアンスへの接続に使用するクライアントまたはホストのコンピューターには、この証明書をインストールする必要があります。

        ![リモート manangement ブレード](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage4.png)
5. をクリックして**保存**メッセージが表示されたら、変更を確認したりします。