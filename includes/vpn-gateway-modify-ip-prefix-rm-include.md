### <a name="a-namenoconnectionahow-to-add-or-remove-prefixes-no-gateway-connection"></a><a name="noconnection"></a>プレフィックスを追加または削除する方法 (ゲートウェイ接続がない場合)
* **追加** する場合は、以下の例を使用してください。 必ず独自の値に変更してください。
  
        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
* **削除** する場合は、以下の例を使用してください。 不要になったプレフィックスは削除します。 この例では、プレフィックス 20.0.0.0/24 (前の例に含まれる) が不要になったため、ローカル ネットワーク ゲートウェイを更新してそのプレフィックスを削除します。
  
        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### <a name="a-namewithconnectionahow-to-add-or-remove-prefixes-existing-gateway-connection"></a><a name="withconnection"></a>プレフィックスを追加または削除する方法 (ゲートウェイ接続がある場合)
既にゲートウェイ接続が作成済みである場合に、ローカル ネットワーク ゲートウェイに含まれている IP アドレスのプレフィックスを追加または削除するには、次の手順を順番に実行する必要があります。 これに伴い、VPN 接続のためにある程度のダウンタイムが発生します。 プレフィックスを更新するときはまず、接続を削除し、プレフィックスを変更した後で新しい接続を作成します。 以下の例で使用されている値は、必ず独自の値に変更してください。

> [!IMPORTANT]
> VPN ゲートウェイは削除しないでください。 削除した場合は、手順を戻って、VPN ゲートウェイをもう一度作成し、新しい設定でオンプレミスのルーターを構成し直す必要があります。
> 
> 

1. 接続を削除します。
   
        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName
2. ローカル ネットワーク ゲートウェイ用のアドレス プレフィックスを変更します。
   
    LocalNetworkGateway の変数を設定します。
   
        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName
   
    プレフィックスを変更します。
   
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
3. 接続を作成します。 この例では、IPsec という接続の種類を構成しています。 接続を作成し直すときは、実際の構成で指定した接続の種類を使用してください。 その他の種類の接続については、 [PowerShell コマンドレット](https://msdn.microsoft.com/library/mt603611.aspx) のページを参照してください。
   
     VirtualNetworkGateway の変数を設定します。
   
        $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName
   
    接続を作成します。 このサンプルでは、前の手順で設定した $local という変数を使用しています。

        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName -Location 'West US' `
        -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
        -ConnectionType IPsec `
        -RoutingWeight 10 -SharedKey 'abc123'


<!--HONumber=Nov16_HO2-->


