### VPN ゲートウェイ接続をまだ作成していない場合にプレフィックスを追加または削除する

- まだ VPN ゲートウェイ接続がない状態で、作成したローカル ネットワーク ゲートウェイにさらにアドレスのプレフィックスを**追加**する場合は、以下の例を使用してください。

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- VPN 接続がないローカル ネットワーク ゲートウェイからアドレスのプレフィックスを**削除**する場合は、以下の例を使用してください。不要になったプレフィックスは削除します。この例では、プレフィックス 20.0.0.0/24 (前の例に含まれる) が不要になったため、ローカル ネットワーク ゲートウェイを更新してそのプレフィックスを削除します。

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### VPN ゲートウェイ接続を既に作成していた場合にプレフィックスを追加または削除する

VPN 接続を作成していた場合に、ローカル ネットワーク ゲートウェイに含まれている IP アドレスのプレフィックスを追加または削除するには、次の手順を順番に実行する必要があります。これにより、ゲートウェイを削除して再作成する必要があるため、VPN 接続のためにある程度のダウンタイムが発生します。ただし、その接続用の IP アドレスは要求済みであるため、以前に使用した値を変更する場合を除き、オンプレミスの VPN ルーターを再構成する必要はありません。
 
1. ゲートウェイの接続を削除します。 
2. ローカル ネットワーク ゲートウェイのプレフィックスを変更します。 
3. 新しいゲートウェイ接続を作成します。 

次のサンプルをガイドラインとして使用できます。

	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

	Remove-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg

	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
	Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	
	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0323_2016-->