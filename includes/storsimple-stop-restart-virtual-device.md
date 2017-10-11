#### <a name="to-stop-and-start-a-virtual-device"></a>停止し、仮想デバイスの起動
停止する仮想デバイスの名前をクリックし、をクリックして**シャット ダウン**です。 その状態は、仮想デバイスをシャット ダウン**停止**です。 仮想デバイスを停止すると、その状態は**Stopped**です。

次のコマンドレットを使用して停止し、仮想デバイスを起動します。

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-virtual-device"></a>仮想デバイスを再起動するには
仮想デバイスが実行されていると、それを再開する、その名前をクリックし、**再起動**です。 仮想デバイスを再起動すると、その状態は**再起動**です。 その状態は、仮想デバイスを使用する準備ができたら、**を実行している**です。

仮想デバイスを再起動するのにには、次のコマンドレットを使用します。

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

