### <a name="noconnection"></a>ローカル ネットワーク ゲートウェイ IP アドレス プレフィックスのないゲートウェイの接続を変更するには

その他のアドレス プレフィックスを追加するには。

```powershell
$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
```

アドレス プレフィックスを削除します。<br>
プレフィックスが不要になったを除外します。 この例では不要になった必要がありますプレフィックス 20.0.0.0/24 (前の例から)、ローカル ネットワーク ゲートウェイを更新してようにを除くそのプレフィックス。

```powershell
$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.0.0.0/24','30.0.0.0/24')
```

### <a name="withconnection"></a>ローカル ネットワーク ゲートウェイ IP アドレス プレフィックスの既存のゲートウェイの接続を変更するには

いる場合、ゲートウェイの接続を追加またはローカル ネットワーク ゲートウェイに含まれている IP アドレスのプレフィックスを削除する、順序で、次の手順を実行する必要があります。 これは、結果、ある程度のダウンタイムを VPN 接続します。 IP アドレス プレフィックスを変更する場合は、VPN ゲートウェイを削除する必要はありません。 のみ、接続を削除する必要があります。


1. 接続を削除します。

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName
  ```
2. ローカル ネットワーク ゲートウェイのアドレス プレフィックスを変更します。
   
  LocalNetworkGateway の変数を設定します。

  ```powershell
  $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName
  ```
   
  プレフィックスを変更します。
   
  ```powershell
  Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
  -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
  ```
3. 接続を作成します。 この例では、IPsec 接続の種類を構成します。 接続を再作成するときに、構成が指定されている接続の種類を使用します。 追加の接続の種類を参照して、 [PowerShell コマンドレット](https://msdn.microsoft.com/library/mt603611.aspx)ページ。
   
  VirtualNetworkGateway の変数を設定します。

  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName
  ```
   
  接続を作成します。 この例では、手順 2. で設定した $local 変数を使用します。

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName -Location 'West US' `
  -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec `
  -RoutingWeight 10 -SharedKey 'abc123'
  ```