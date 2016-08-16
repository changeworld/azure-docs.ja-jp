### PowerShell を使用して接続を確認するには

接続に成功したことを確認するには、`Get-AzureRmVirtualNetworkGatewayConnection` コマンドレットを使用します。`-Debug` は指定しても指定しなくてもかまいません。

1. 次のコマンドレットを使用します。値は実際の値に置き換えてください。プロンプトが表示されたら、"A" を選択して "すべて" (All) を実行します。この例の `-Name` は、テストする作成済みの接続の名前を示します。

		Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG

2. コマンドレットの実行後、値を確認します。以下の例では、接続状態は "Connected" と表示され、受信バイトと送信バイトを確認できます。

		Body:
		{
		  "name": "MyGWConnection",
		  "id":
		"/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/connections/MyGWConnection",
		  "properties": {
		    "provisioningState": "Succeeded",
		    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
		    "virtualNetworkGateway1": {
		      "id":
		"/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/virtualNetworkGa
		teways/vnetgw1"
		    },
		    "localNetworkGateway2": {
		      "id":
		"/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/localNetworkGate
		ways/LocalSite"
		    },
		    "connectionType": "IPsec",
		    "routingWeight": 10,
		    "sharedKey": "abc123",
		    "connectionStatus": "Connected",
		    "ingressBytesTransferred": 33509044,
		    "egressBytesTransferred": 4142431
		  }

### Azure ポータルを使用して接続を確認するには

接続の状態は、Azure ポータルで目的の接続に移動して確認できます。これには複数の方法があります。以下に示したのは、目的の接続に移動する方法の一例です。

1. [Azure ポータル](http://portal.azure.com)で **[仮想ネットワーク ゲートウェイ]** に移動します。ゲートウェイの名前をクリックします。
2. 対応するウィンドウで **[設定]** の **[接続]** をクリックします。各接続の状態が確認できます。
3. 接続についての詳しい情報を表示するには、その接続の名前をクリックします。接続の [要点] ページで **[接続の状態]** に注目してください。接続に成功していれば、この状態が "成功" と "接続済み" になります。データの流れは、**[受信データ]** と **[送信データ]** を見てチェックできます。

	以下の例では、**[接続の状態]** が "接続されていません" になっています。

	![Verify connection](./media/vpn-gateway-verify-connection-rm-include/connectionverify450.png)

<!---HONumber=AcomDC_0810_2016-->