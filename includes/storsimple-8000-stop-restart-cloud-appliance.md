#### <a name="to-stop-and-start-a-cloud-appliance"></a>クラウド アプライアンスの開始し、停止に

1. クラウド アプライアンスを停止するには、クラウド アプライアンスの VM に移動します。
    ![クラウドの StorSimple アプライアンスの仮想マシン](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart1.png)

2. コマンド バーからをクリックして**停止**です。

    ![クラウドの StorSimple アプライアンスの仮想マシン](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart2.png)

3. 確認のメッセージが表示されたら、クリックして**はい**です。

    ![クラウドの StorSimple アプライアンスの仮想マシン](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart3.png)

4. VM を停止すると、解放を取得します。 クラウド アプライアンスを停止すると、その状態は**Deallocating**です。 クラウド アプライアンスが停止したら、その状態は、**停止 (割り当て解除)**です。

    ![クラウドの StorSimple アプライアンスの仮想マシン](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart4.png)

5. VM が停止した後にをクリックして**開始**(ボタンをクリックできるようになります) を VM を開始します。 その状態は、クラウド アプライアンスの起動後**Started**です。

    ![クラウドの StorSimple アプライアンスの仮想マシン](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart5.png)

クラウド アプライアンスの開始し、停止には、次のコマンドレットを使用します。

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-cloud-appliance"></a>クラウド アプライアンスを再起動するには

クラウド アプライアンスを再起動するには、クラウド アプライアンス用に VM に移動します。 コマンド バーからをクリックして**再起動**です。 メッセージが表示されたら、再起動を確認します。 クラウド アプライアンスを使用する準備ができたら、その状態は、**を実行している**です。

![クラウドの StorSimple アプライアンスの仮想マシン](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart6.png)

クラウド アプライアンスを再起動するのにには、次のコマンドレットを使用します。

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

