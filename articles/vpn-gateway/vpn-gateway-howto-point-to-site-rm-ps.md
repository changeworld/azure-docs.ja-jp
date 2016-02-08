<properties 
   pageTitle="Azure Virtual Network へのポイント対サイト VPN Gateway 接続の構成 | Microsoft Azure"
   description="ポイント対サイト VPN 接続を作成することで、Azure Virtual Network に安全に接続します。この構成は、VPN デバイスを使用せずにリモートの場所からのクロスプレミス接続が必要な場合に便利であり、ハイブリッド ネットワーク構成で使用できます。この記事には、リソース マネージャー デプロイメント モデルで作成された VNet の PowerShell 指示が含まれています。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/19/2016"
   ms.author="cherylmc" />

# PowerShell を利用し、仮想ネットワークへのポイント対サイト接続を構成する

> [AZURE.SELECTOR]
- [PowerShell - Resource Manager](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Portal - Classic](vpn-gateway-point-to-site-create.md)

ポイント対サイト構成では、クライアント コンピューターから仮想ネットワークへのセキュリティで保護された接続を個別に作成することができます。VPN 接続を確立するには、クライアント コンピューターからの接続を開始します。ポイント対サイトは、自宅や会議室など、リモートの場所から VNet に接続する場合、または仮想ネットワークに接続する必要があるクライアントの数が少ない場合などに適しています。ポイント対サイト接続を機能させるために、VPN デバイスや公開 IP アドレスは必要ありません。ポイント対サイト接続の詳細については、「[VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md#point-to-site-connections)」と[クロスプレミス接続](vpn-gateway-cross-premises-options.md)に関するページを参照してください。

この記事は、**Azure リソース マネージャー** デプロイメント モデルを使用して作成された VNet と VPN ゲートウェイを対象としています。サービス管理 (クラシック デプロイ モデルとも呼ばれます) を使用して作成された VNet のポイント対サイト接続を構成する場合は、「[VNet へのポイント対サイト VPN 接続の構成](vpn-gateway-point-to-site-create.md)」をご覧ください。

[AZURE.INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## この構成について

このシナリオでは、ポイント対サイト接続で仮想ネットワークを作成します。ポイント対サイト接続は、ゲートウェイのある VNet、アップロードしたルート証明書 .cer ファイル、クライアント証明書、クライアント側の VPN 構成で構成されます。

この演習では、次の値を使用します。

- 名前: **TestVNet**。アドレス空間として **192.168.0.0/16** と **10.254.0.0/16** を使用します。1 つの VNet に複数のアドレス空間を使用できます。
- サブネット名: **FrontEnd**。**192.168.1.0/24** を使用します。
- サブネット名: **BackEnd**。**10.254.1.0/24** を使用します。
- サブネット名: **GatewaySubnet**。**192.168.200.0/24** を使用します。ゲートウェイが機能するには、サブネット名 *GatewaySubnet* が必須となります。 
- VPN クライアント アドレス プール: **172.16.201.0/24**。このポイント対サイト接続を利用して VNet に接続する VPN クライアントはこのプールから IP アドレスを受け取ります。
- サブスクリプション: サブスクリプションが複数ある場合は、適切なサブスクリプションを使用していることを確認します。
- リソース グループ: **TestRG**。
- 場所: **米国東部**。
- DNS サーバー: 名前解決に利用する DNS サーバーの **IP アドレス**。
- GW 名: **GW**。
- パブリック IP 名: **GWIP**。
- VpnType: **RouteBased**。


## 作業を開始する前に

Azure サブスクリプションがあり、この構成に必要な Azure PowerShell コマンドレットがインストールされていることを確認します (1.0.2 以降)。Azure サブスクリプションを持っていない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。
	
**PowerShell コマンドレット モジュールのインストールについて**

	[AZURE.INCLUDE [vpn-gateway-ps-rm-howto](../../includes/vpn-gateway-ps-rm-howto-include.md)]

## Azure のポイント対サイト接続を構成する

1. PowerShell コンソールで、Azure アカウントにログインします。このコマンドレットは、Azure アカウントのログイン資格情報をユーザーに求めます。ログイン後にアカウント設定がダウンロードされ、Azure PowerShell で使用できるようになります。

		Login-AzureRmAccount 

2. Azure サブスクリプションの一覧を取得します。

		Get-AzureRmSubscription

3. 使用するサブスクリプションを指定します。

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. この構成では、使用する値で次の PowerShell 変数を宣言します。サンプル スクリプトでは、宣言済みの値が使用されます。使用する値を宣言します。次のサンプルを使用し、必要に応じて独自の値で置き換えます。

		$VNetName  = "TestVNet"
		$FESubName = "FrontEnd"
		$BESubName = "Backend"
		$GWSubName = "GatewaySubnet"
		$VNetPrefix1 = "192.168.0.0/16"
		$VNetPrefix2 = "10.254.0.0/16"
		$FESubPrefix = "192.168.1.0/24"
		$BESubPrefix = "10.254.1.0/24"
		$GWSubPrefix = "192.168.200.0/26"
		$VPNClientAddressPool = "172.16.201.0/24"
		$RG = "TestRG"
		$Location = "East US"
		$DNS = "8.8.8.8"
		$GWName = "GW"
		$GWIPName = "GWIP"
		$GWIPconfName = "gwipconf"
    	$P2SRootCertName = "ARMP2SRootCert.cer"

5. 新しいリソース グループを作成します。

		New-AzureRmResourceGroup -Name $RG -Location $Location

6. 仮想ネットワークのサブネット構成を作成し、「*FrontEnd*」、「*BackEnd*」、「*GatewaySubnet*」という名前を付けます。これらのプレフィックスは、上で宣言された VNet アドレス空間に含まれる必要があります。

		$fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
		$besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
		$gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

7. 仮想ネットワークを作成する。指定された DNS サーバーは、接続するリソースの名前を解決できる DNS サーバーにする必要があります。この例ではパブリック IP アドレスを使用しましたが、独自の値をここに指定できます。

		New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

8. 作成した仮想ネットワークの変数を指定します。

		$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

9. 動的に割り当てられたパブリック IP アドレスを要求します。この IP アドレスは、ゲートウェイが適切に機能するために必要です。後でゲートウェイをゲートウェイ IP 構成に接続します。
		
		$pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
		
10. Azure にルート証明書 .cer ファイルをアップロードします。会社の証明書環境のルート証明書を使用するか、自己署名のルート証明書を使用できます。最大 20 個のルート証明書をアップロードすることができます。*makecert* を使用して自己署名ルート証明書を作成する手順については、「[ポイント対サイト構成の自己署名ルート証明書の操作](vpn-gateway-certificates-point-to-site.md)」をご覧ください。.cer ファイルにはルート証明書の秘密キーを含めないでください。
	
	以下はサンプルです。公的な証明書データをアップロードする際の問題は、文字列全体をスペースなしでコピーし、貼り付けなければならないということです。そのようにコピーしないと、アップロードは機能しません。この手順では、独自の証明書 .cer ファイルを使用する必要があります。下のサンプルからはコピーしないでください。

		$MyP2SRootCertPubKeyBase64 = "MIIDUzCCAj+gAwIBAgIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAMDQxEjAQBgNVBAoTCU1pY3Jvc29mdDEeMBwGA1UEAxMVQnJrIExpdGUgVGVzdCBSb290IENBMB4XDTEzMDExOTAwMjQxOFoXDTIxMDExOTAwMjQxN1owNDESMBAGA1UEChMJTWljcm9zb2Z0MR4wHAYDVQQDExVCcmsgTGl0ZSBUZXN0IFJvb3QgQ0EwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQC7SmE+iPULK0Rs7mQBO/6a6B6/G9BaMxHgDGzAmSG0Qsyt5e08aqgFnPdkMl3zRJw3lPKGha/JCvHRNrO8UpeAfc4IXWaqxx2iBipHjwmHPHh7+VB8lU0EJcUe7WBAI2n/sgfCwc+xKtuyRVlOhT6qw/nAi8e5don/iHPU6q7GCcnqoqtceQ/pJ8m66cvAnxwJlBFOTninhb2VjtvOfMQ07zPP+ZuYDPxvX5v3nd6yDa98yW4dZPuiGO2s6zJAfOPT2BrtyvLekItnSgAw3U5C0bOb+8XVKaDZQXbGEtOw6NZvD4L2yLd47nGkN2QXloiPLGyetrj3Z2pZYcrZBo8hAgMBAAGjaTBnMGUGA1UdAQReMFyAEOncRAPNcvJDoe4WP/gH2U+hNjA0MRIwEAYDVQQKEwlNaWNyb3NvZnQxHjAcBgNVBAMTFUJyayBMaXRlIFRlc3QgUm9vdCBDQYIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAA4IBAQCGyHhMdygS0g2tEUtRT4KFM+qqUY5HBpbIXNAav1a1dmXpHQCziuuxxzu3iq4XwnWUF1OabdDE2cpxNDOWxSsIxfEBf9ifaoz/O1ToJ0K757q2Rm2NWqQ7bNN8ArhvkNWa95S9gk9ZHZLUcjqanf0F8taJCYgzcbUSp+VBe9DcN89sJpYvfiBiAsMVqGPc/fHJgTScK+8QYrTRMubtFmXHbzBSO/KTAP5rBTxse88EGjK5F8wcedvge2Ksk6XjL3sZ19+Oj8KTQ72wihN900p1WQldHrrnbixSpmHBXbHr9U0NQigrJp5NphfuU5j81C8ixvfUdwyLmTv7rNA7GTAD"
		$p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $MyP2SRootCertPubKeyBase64

11. VNet の仮想ネットワーク ゲートウェイを作成します。GatewayType は Vpn に、VpnType は RouteBased にする必要があります。

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Standard -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## クライアントの構成

各クライアントがポイント対サイトを利用して Azure に接続するには、VPN クライアントを接続するように構成し、クライアントにクライアント証明書をインストールする必要があります。Windows クライアントの場合、VPN クライアント構成パッケージが利用できます。詳細については、「[VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md#point-to-site-connections)」をご覧ください。

1. VPN クライアント構成パッケージをダウンロードします。この手順では、次のサンプルを利用し、クライアント構成パッケージをダウンロードします。

		Get-AzureRmVpnClientPackage -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

	PowerShell コマンドレットは URL リンクを返します。Web ブラウザーに返されたリンクをコピーして貼り付け、コンピューターにパッケージをダウンロードします。下は返された URL の例です。

    	"https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
	
2. クライアント コンピューターのルート証明書からクライアント証明書 (*.pfx) を生成しインストールします。自分に適している方法でインストールしてください。自己署名ルート証明書を使用する場合、その方法については、「[ポイント対サイト構成の自己署名ルート証明書の操作](vpn-gateway-certificates-point-to-site.md)」をご覧ください。

3. VNet に接続するには、クライアント コンピューターで [VPN 接続] に移動し、作成した VPN 接続を見つけます。仮想ネットワークと同じ名前が付いています。**[接続]** をクリックします。証明書を使用することを示すポップアップ メッセージが表示される場合があります。その場合、**[続行]** をクリックして、昇格された特権を使用します。

4. **接続**の状態ページで、**[接続]** をクリックして接続を開始します。**[証明書の選択]** 画面が表示された場合は、表示されているクライアント証明書が接続に使用する証明書であることを確認します。そうでない場合は、ドロップダウン矢印を使用して適切な証明書を選択し、**[OK]** をクリックします。

5. これで接続が確立されたはずです。以下の手順で接続を確認できます。

## 接続を確認する

1. VPN 接続がアクティブであることを確認するには、管理者特権でのコマンド プロンプトを開いて、*ipconfig/all* を実行します。

2. 結果を表示します。受信した IP アドレスが、構成に指定したポイント対サイト VPN クライアント アドレス プール内のアドレスのいずれかであることに注意してください。結果は、次のようになります。
    
		PPP adapter VNetEast:
			Connection-specific DNS Suffix .:
			Description.....................: VNetEast
			Physical Address................:
			DHCP Enabled....................: No
			Autoconfiguration Enabled.......: Yes
			IPv4 Address....................: 172.16.201.3(Preferred)
			Subnet Mask.....................: 255.255.255.255
			Default Gateway.................:
			NetBIOS over Tcpip..............: Enabled

## ルート証明書を追加または削除するには

証明書は、ポイント対サイト VPN の VPN クライアントの認証に使用されます。以下の手順に従って、ルート証明書を追加および削除できます。

### ルート証明書の追加

最大 20 個のルート証明書を Azure に追加できます。ルート証明書を追加するには、次の手順に従います。

1. 新しいルート証明書を作成し、アップロードの準備をします。

		$P2SRootCertName2 = "ARMP2SRootCert2.cer"
		$MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. 新しいルート証明書をアップロードします。一度に追加できるルート証明書は 1 つに限られます。

		Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2

3. 次のコマンドレットを使用して、新しい証明書が正しく追加されたことを確認できます。

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName

### ルート証明書の削除

ルート証明書を Azure から削除できます。ルート証明書を削除すると、そのルート証明書から生成されたクライアント証明書は、Azure で有効なルート証明書から生成されたクライアント証明書をインストールするまで、ポイント対サイトを使用して Azure に接続できなくなります。

1. ルート証明書を削除します。

		Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

 
2. 次のコマンドレットを使用して、証明書が正常に削除されたことを確認します。

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName

## 失効したクライアント証明書のリストの管理

クライアント証明書は失効させることができます。証明書失効リストを使用すると、個々のクライアント証明書に基づくポイント対サイト接続を選択して拒否することができます。Azure からルート証明書を削除すると、失効したルート証明書によって生成/署名されたすべてのクライアント証明書のアクセスが取り消されますが、クライアント証明書を失効させると、特定の証明書のアクセスを削除できます。一般的な方法としては、ルート証明書を使用してチームまたは組織レベルでアクセスを管理し、失効したクライアント証明書を使用して、個々のユーザーの細かいアクセス制御を構成します。

### クライアント証明書の失効

1. 失効させるクライアント証明書の拇印を取得します。

		$RevokedClientCert1 = "ClientCert1"
		$RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. 失効した拇印のリストに拇印を追加します。

		Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. 証明書失効リストに拇印が追加されたことを確認します。拇印は 1 つずつ追加する必要があります。

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### クライアント証明書の回復

失効したクライアント証明書のリストから拇印を削除することで、クライアント証明書を回復できます。

1.  失効したクライアント証明書の拇印のリストから拇印を削除します。

		Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. 失効リストから拇印が削除されているかどうかを確認します。

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## 次のステップ

仮想ネットワークに仮想マシンを追加できます。手順については、[仮想マシンの作成](../virtual-machines/virtual-machines-windows-tutorial.md)に関するページを参照してください。

<!---HONumber=AcomDC_0128_2016-->