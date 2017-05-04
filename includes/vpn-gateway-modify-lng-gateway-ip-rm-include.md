ゲートウェイ IP アドレスを変更するには、"New-AzureRmVirtualNetworkGatewayConnection" コマンドレットを使用します。 現時点では、"Set" コマンドレットでゲートウェイ IP アドレスを変更することはできません。

### <a name="gwipnoconnection"></a>ゲートウェイ IP アドレスの変更 (ゲートウェイ接続がない場合)
まだ接続のないローカル ネットワーク ゲートウェイのゲートウェイ IP アドレスを変更する場合は、以下の例を参考にしてください。 同時にアドレス プレフィックスを変更することもできます。 必ず、ローカル ネットワーク ゲートウェイの既存の名前を使用して、現在の設定を上書きしてください。 そうしないと、既存のゲートウェイが上書きされずに、新しいローカル ネットワーク ゲートウェイが作成されます。

次の例の値を実際の値に置き換えて使用してください。

```powershell
New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
-Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName
```

### <a name="gwipwithconnection"></a>ゲートウェイ IP アドレスの変更 (ゲートウェイ接続が既にある場合)
既にゲートウェイ接続が存在する場合はまず、その接続を削除する必要があります。 接続を削除したら、ゲートウェイ IP アドレスを変更し、新しい接続を作成し直します。 同時にアドレス プレフィックスを変更することもできます。 これに伴い、VPN 接続のためにある程度のダウンタイムが発生します。

> [!IMPORTANT]
> VPN ゲートウェイは削除しないでください。 削除した場合は、前の手順に戻って作成し直す必要があります。 さらに、新しい VPN ゲートウェイの IP アドレスでオンプレミス VPN デバイスを更新する必要があります。
> 
> 

1. 接続を削除します。 "Get-AzureRmVirtualNetworkGatewayConnection" コマンドレットを使用して、接続の名前を調べることができます。

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName
  ```
2. "GatewayIpAddress" の値を変更します。 同時にアドレス プレフィックスを変更することもできます。 必ず、ローカル ネットワーク ゲートウェイの既存の名前を使用して、現在の設定を上書きしてください。 そうしないと、既存のゲートウェイが上書きされずに、新しいローカル ネットワーク ゲートウェイが作成されます。

  ```powershell
  New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
  -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
  -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
  ```
3. 接続を作成します。 この例では、IPsec という接続の種類を構成します。 接続を作成し直すときは、実際の構成で指定した接続の種類を使用してください。 その他の種類の接続については、 [PowerShell コマンドレット](https://msdn.microsoft.com/library/mt603611.aspx) のページを参照してください。  VirtualNetworkGateway の名前は、"Get-AzureRmVirtualNetworkGateway" コマンドレットを実行して取得できます。
   
    変数を設定します。

  ```powershell
  $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
  $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName
  ```
   
    接続を作成します。

  ```powershell 
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
  -Location "West US" `
  -VirtualNetworkGateway1 $vnetgw `
  -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```