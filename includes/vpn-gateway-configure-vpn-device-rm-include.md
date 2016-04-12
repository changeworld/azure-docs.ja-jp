
VPN デバイスを構成するには、オンプレミス VPN デバイスを構成するための、仮想ネットワーク ゲートウェイのパブリック IP アドレスが必要です。構成に関する具体的な情報についてはお使いのデバイスの製造元の情報を参照し、デバイスの構成を行ってください。Azure で正しく動作する VPN デバイスの詳細については、「[VPN デバイス](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md)」を参照してください。

PowerShell を使用して仮想ネットワーク ゲートウェイのパブリック IP アドレスを検索するには、次のサンプルを使用します。

	Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG

仮想ネットワーク ゲートウェイのパブリック IP アドレスは、Azure ポータルを使用して表示することもできます。**仮想ネットワーク ゲートウェイ**に移動し、ゲートウェイの名前をクリックします。

<!---HONumber=AcomDC_0406_2016-->