### <a name="gwipnoconnection"></a>ローカル ネットワーク ゲートウェイ 'GatewayIpAddress' のゲートウェイの接続を変更するには

接続する VPN デバイスが、パブリック IP アドレスを変更した場合は、その変更を反映するように、ローカル ネットワーク ゲートウェイを変更する必要があります。 この例を使用すると、ゲートウェイの接続を持たないローカル ネットワーク ゲートウェイを変更できます。

この値を変更する場合は、同時にアドレス プレフィックスを変更することもできます。 必ず、現在の設定を上書きするために、ローカル ネットワーク ゲートウェイの既存の名前を使用してください。 別の名前を使用する場合は、既存の 1 つを上書きする代わりに、新しいローカル ネットワーク ゲートウェイを作成します。

```powershell
New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
-Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName
```

### <a name="gwipwithconnection"></a>ローカル ネットワーク ゲートウェイ 'GatewayIpAddress' - 既存のゲートウェイの接続を変更するには

接続する VPN デバイスが、パブリック IP アドレスを変更した場合は、その変更を反映するように、ローカル ネットワーク ゲートウェイを変更する必要があります。 ゲートウェイの接続は既に存在する場合、まず、接続を削除する必要があります。 接続が削除されると、ゲートウェイの IP アドレスを変更し、新しい接続を再作成できます。 同時に、アドレス プレフィックスを変更することもできます。 これは、結果、ある程度のダウンタイムを VPN 接続します。 ゲートウェイの IP アドレスを変更するときは VPN ゲートウェイを削除する必要はありません。 のみ、接続を削除する必要があります。
 

1. 接続を削除します。 ' Get AzureRmVirtualNetworkGatewayConnection' コマンドレットを使用して、接続の名前を見つけることができます。

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName
  ```
2. 'GatewayIpAddress' 値を変更します。 同時に、アドレス プレフィックスを変更することもできます。 現在の設定を上書きする既存のローカル ネットワーク ゲートウェイの名前を使用することを確認します。 ない場合は、既存の 1 つを上書きする代わりに、新しいローカル ネットワーク ゲートウェイを作成します。

  ```powershell
  New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
  -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
  -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
  ```
3. 接続を作成します。 この例では、IPsec 接続の種類を構成します。 接続を再作成するときに、構成が指定されている接続の種類を使用します。 追加の接続の種類を参照して、 [PowerShell コマンドレット](https://msdn.microsoft.com/library/mt603611.aspx)ページ。  VirtualNetworkGateway 名を取得するには、' Get AzureRmVirtualNetworkGateway' コマンドレットを実行できます。
   
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