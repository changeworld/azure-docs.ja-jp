### <a name="noconnection"></a>ローカル ネットワーク ゲートウェイ IP アドレスのプレフィックスを変更するには (ゲートウェイに接続していない場合)

アドレス プレフィックスを追加するには:

```powershell
$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
```

アドレス プレフィックスを削除するには:<br>
不要になったプレフィックスは削除します。 この例では、プレフィックス 20.0.0.0/24 (前の例に含まれる) が不要になったため、ローカル ネットワーク ゲートウェイを更新してそのプレフィックスを削除します。

```powershell
$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.0.0.0/24','30.0.0.0/24')
```

### <a name="withconnection"></a>ローカル ネットワーク ゲートウェイ IP アドレスのプレフィックスを変更するには (ゲートウェイに接続している場合)

ゲートウェイ接続がある場合に、ローカル ネットワーク ゲートウェイに含まれている IP アドレスのプレフィックスを追加または削除するには、次の手順を順番に実行する必要があります。 これに伴い、VPN 接続のためにある程度のダウンタイムが発生します。 IP アドレスのプレフィックスを変更するときに、VPN ゲートウェイを削除する必要はありません。 削除が必要になるのは、接続のみです。


1. 接続を削除します。

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName
  ```
2. ローカル ネットワーク ゲートウェイ用のアドレス プレフィックスを変更します。
   
  LocalNetworkGateway の変数を設定します。

  ```powershell
  $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName
  ```
   
  プレフィックスを変更します。
   
  ```powershell
  Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
  -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
  ```
3. 接続を作成します。 この例では、IPsec という接続の種類を構成します。 接続を作成し直すときは、実際の構成で指定した接続の種類を使用してください。 その他の種類の接続については、 [PowerShell コマンドレット](https://msdn.microsoft.com/library/mt603611.aspx) のページを参照してください。
   
  VirtualNetworkGateway の変数を設定します。

  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName
  ```
   
  接続を作成します。 この例では、手順 2. で設定した $local という変数を使用しています。

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName -Location 'West US' `
  -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec `
  -RoutingWeight 10 -SharedKey 'abc123'
  ```