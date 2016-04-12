### Azure ポータルを使用して接続を確認するには

Azure ポータルで VPN 接続を確認するには、**[仮想ネットワーク ゲートウェイ]** に移動し、***ゲートウェイ名***、**[設定]**、**[接続]** の順にクリックします。接続の名前を選択すると、接続の詳細情報が表示されます。次の例では、接続が未接続の状態のため、データの流れはありません。


![Verify connection](./media/vpn-gateway-verify-connection-rm-include/connectionverify450.png)


### PowerShell を使用して接続を確認するには

`Get-AzureRmVirtualNetworkGatewayConnection –Debug` を使用して、接続が成功したことを確認することもできます。次のコマンドレット サンプルを使用して、実際に使用する値と同じ値を構成できます。プロンプトが表示されたら、*A* を選択してすべてを実行します。

	Get-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 コマンドレットが完了したら、スクロールして値を表示します。以下の例では、接続状態は "*Connected*" と表示され、受信バイトと送信バイトを確認できます。

	Body:
	{
	  "name": "localtovon",
	  "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
	ltovon",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
	    "virtualNetworkGateway1": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
	teways/vnetgw1"
	    },
	    "localNetworkGateway2": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
	ways/LocalSite"
	    },
	    "connectionType": "IPsec",
	    "routingWeight": 10,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 33509044,
	    "egressBytesTransferred": 4142431
	  }

<!---HONumber=AcomDC_0406_2016-->