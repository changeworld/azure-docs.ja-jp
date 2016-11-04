ゲートウェイ IP アドレスを変更するには、`New-AzureRmVirtualNetworkGatewayConnection` コマンドレットを使用します。ローカル ネットワーク ゲートウェイの名前を変更せずに既存の名前をそのまま使用すれば、設定は上書きされます。現時点では、"Set" コマンドレットでゲートウェイ IP アドレスを変更することはできません。

### <a name="gwipnoconnection"></a>ゲートウェイ IP アドレスを変更する方法 (ゲートウェイ接続がない場合)
まだ接続のないローカル ネットワーク ゲートウェイのゲートウェイ IP アドレスを更新する場合は、以下の例を参考にしてください。同時にアドレス プレフィックスを更新することもできます。既存の設定は、指定した設定で上書きされます。必ず、ローカル ネットワーク ゲートウェイの既存の名前を使用してください。そうしないと、新しいローカル ネットワーク ゲートウェイが作成され、既存のゲートウェイが上書きされません。

次の例を参考にしてください。値は実際の値に置き換えてください。

    New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
    -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
    -GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName


### <a name="gwipwithconnection"></a>ゲートウェイ IP アドレスを変更する方法 (ゲートウェイ接続が既にある場合)
既にゲートウェイ接続が存在する場合はまず、その接続を削除する必要があります。そのうえでゲートウェイの IP アドレスを変更し、新しい接続を作成し直します。これに伴い、VPN 接続のためにある程度のダウンタイムが発生します。

> [!IMPORTANT]
> VPN ゲートウェイは削除しないでください。削除した場合は、前の手順に戻って VPN ゲートウェイをもう一度作成し、新しく作成したゲートウェイに割り当てる IP アドレスでオンプレミスのルーターを構成し直す必要があります。
> 
> 

1. 接続を削除します。対象となる接続の名前は、`Get-AzureRmVirtualNetworkGatewayConnection` コマンドレットで探すことができます。
   
        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName
2. GatewayIpAddress の値を変更します。このとき、必要に応じてアドレス プレフィックスを変更することもできます。これで既存のローカル ネットワーク ゲートウェイの設定が上書きされます。変更を加える際は、設定が上書きされるように、ローカル ネットワーク ゲートウェイの既存の名前を使用してください。そうしないと、新しいローカル ネットワーク ゲートウェイが作成され、既存のゲートウェイが変更されません。
   
        New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
        -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
        -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
3. 接続を作成します。この例では、IPsec という接続の種類を構成しています。接続を作成し直すときは、実際の構成で指定した接続の種類を使用してください。その他の種類の接続については、[PowerShell コマンドレット](https://msdn.microsoft.com/library/mt603611.aspx)のページを参照してください。VirtualNetworkGateway の名前は、`Get-AzureRmVirtualNetworkGateway` コマンドレットを実行して取得できます。
   
    変数を設定します。
   
        $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName
   
    接続を作成します。
   
        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
        -Location "West US" `
        -VirtualNetworkGateway1 $vnetgw `
        -LocalNetworkGateway2 $local `
        -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0810_2016-->