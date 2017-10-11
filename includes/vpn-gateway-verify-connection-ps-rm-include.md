有無にかかわらず、' Get AzureRmVirtualNetworkGatewayConnection' コマンドレットを使用して、接続が成功したことを確認することができます '-デバッグ' です。 

1. 独自に一致するように値を設定する、次のコマンドレット例を使用します。 メッセージが表示されたら、'A' を 'All' を実行するために選択します。 例では、'-Name' はテストを実行する接続の名前を参照します。

  ```powershell
  Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG
  ```
2. コマンドレットが完了した後は、値を表示します。 次の例では、'接続' と接続の状態を示していて、受信と送信バイト数を確認できます。
   
  ```
  "connectionStatus": "Connected",
  "ingressBytesTransferred": 33509044,
  "egressBytesTransferred": 4142431
  ```