### <a name="noconnection"></a>VPN ゲートウェイ接続を使用しないプレフィックスを追加または削除する方法

- まだ VPN ゲートウェイ接続がない状態で、作成したローカル ネットワーク ゲートウェイにさらにアドレスのプレフィックスを**追加**する場合は、以下の例を使用してください。

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- VPN 接続がないローカル ネットワーク ゲートウェイからアドレスのプレフィックスを**削除**する場合は、以下の例を使用してください。不要になったプレフィックスは削除します。この例では、プレフィックス 20.0.0.0/24 (前の例に含まれる) が不要になったため、ローカル ネットワーク ゲートウェイを更新してそのプレフィックスを削除します。

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### <a name="withconnection"></a>VPN ゲートウェイ接続を使用したプレフィックスを追加または削除する方法

VPN 接続を作成していた場合に、ローカル ネットワーク ゲートウェイに含まれている IP アドレスのプレフィックスを追加または削除するには、次の手順を順番に実行する必要があります。これに伴い、VPN 接続のためにある程度のダウンタイムが発生します。プレフィックスを更新するときはまず、接続を削除し、プレフィックスを変更した後で新しい接続を作成します。

>[AZURE.IMPORTANT] VPN ゲートウェイは削除しないでください。削除した場合は、手順を戻って、VPN ゲートウェイをもう一度作成し、新しい設定でオンプレミスのルーターを構成し直す必要があります。
 
1. 変数を指定します。

		$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

2. 接続を削除します。

		Remove-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg

3. プレフィックスを変更します。

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
		-AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

4. 接続を作成します。この例では、IPsec という接続の種類を構成しています。その他の種類の接続については、[PowerShell コマンドレット](https://msdn.microsoft.com/library/mt603611.aspx)のページを参照してください。
	
		New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
		-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec `
		-RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0803_2016-->