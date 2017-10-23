"Get-AzureRmVirtualNetworkGatewayConnection" コマンドレットを使用して、接続が成功したことを確認できます。"-Debug" は指定しても指定しなくてもかまいません。 

1. 次のコマンドレットを使用します。値は実際の値に置き換えてください。 プロンプトが表示されたら、"A" を選択して "すべて" (All) を実行します。 この例では、テストする接続の名前が "-Name" で示されています。

  ```powershell
  Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG
  ```
2. コマンドレットの実行後、値を確認します。 以下の例では、接続状態は "Connected" と表示され、受信バイトと送信バイトを確認できます。
   
  ```
  "connectionStatus": "Connected",
  "ingressBytesTransferred": 33509044,
  "egressBytesTransferred": 4142431
  ```