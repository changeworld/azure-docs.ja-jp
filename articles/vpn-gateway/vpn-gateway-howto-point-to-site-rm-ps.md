<properties 
   pageTitle="Resource Manager デプロイ モデルを使用した仮想ネットワークへのポイント対サイト VPN 接続の構成 | Microsoft Azure"
   description="ポイント対サイト VPN 接続を作成することで、Azure Virtual Network に安全に接続します。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc" />

# PowerShell を使用した VNet へのポイント対サイト接続の構成

> [AZURE.SELECTOR]
- [PowerShell - Resource Manager](vpn-gateway-howto-point-to-site-rm-ps.md)
- [ポータル - クラシック](vpn-gateway-point-to-site-create.md)

ポイント対サイト (P2S) 構成では、個々のクライアント コンピューターから仮想ネットワークへのセキュリティで保護された接続を作成することができます。P2S 接続は、自宅や会議室など、リモートの場所から VNet に接続する場合や、仮想ネットワークに接続する必要があるクライアントの数が少ない場合に便利です。

この記事では、**Resource Manager デプロイメント モデル**でポイント対サイト接続を使用した VNet の作成について説明します。この手順には PowerShell が必要です。現時点では、Azure Portal のみでこのソリューションを作成することはできません。

ポイント対サイト接続を機能させるために、VPN デバイスや公開 IP アドレスは必要ありません。VPN 接続を確立するには、クライアント コンピューターからの接続を開始します。ポイント対サイト接続の詳細については、「[VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md#point-to-site-connections)」と[計画と設計](vpn-gateway-plan-design.md)に関するページを参照してください。



**Azure のデプロイ モデルについて**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**ポイント対サイト接続のデプロイ モデルとツール**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

![ポイント対サイトのダイアログ](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-to-site")


## この構成について

このシナリオでは、ポイント対サイト接続で仮想ネットワークを作成します。この手順は、この構成で必要となる証明書の生成にも役立ちます。P2S 接続は、VPN ゲートウェイのある VNet、ルート証明書 .cer ファイル (公開キー)、クライアント証明書、クライアントの VPN 構成で構成されます。

この構成では次の値を使用します。この記事のパート [1](#declare) で変数を設定します。手順をチュートリアルとして利用して値を変更せずに使用することも、実際の環境に合わせて値を変更することもできます。

- 名前: **VNet1**。アドレス空間として **192.168.0.0/16** と **10.254.0.0/16** を使用します。1 つの VNet に複数のアドレス空間を使用できます。
- サブネット名: **FrontEnd**。**192.168.1.0/24** を使用します。
- サブネット名: **BackEnd**。**10.254.1.0/24** を使用します。
- サブネット名: **GatewaySubnet**。**192.168.200.0/24** を使用します。ゲートウェイが機能するには、サブネット名 *GatewaySubnet* が必須となります。
- VPN クライアント アドレス プール: **172.16.201.0/24**。このポイント対サイト接続を利用して VNet に接続する VPN クライアントはこのプールから IP アドレスを受け取ります。
- サブスクリプション: サブスクリプションが複数ある場合は、適切なサブスクリプションを使用していることを確認します。
- リソース グループ: **TestRG**
- 場所: **米国東部**
- DNS サーバー: 名前解決に利用する DNS サーバーの **IP アドレス**。
- GW 名: **GW**
- パブリック IP 名: **GWIP**。
- VPN の種類: **RouteBased**。


## 作業を開始する前に

- Azure サブスクリプションを持っていることを確認します。Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。
	
- Azure Resource Manager PowerShell コマンドレット (1.0.2 以降) をインストールします。PowerShell コマンドレットのインストールの詳細については、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」を参照してください。

## <a name="declare"></a>パート 1 - ログインと変数の設定

このセクションでは、ログインのほか、この構成で使用される値の宣言を行います。サンプル スクリプトでは、宣言済みの値が使用されます。実際の環境に合わせて値を変更してください。宣言済みの値を使用し、以下の手順を練習として使用することもできます。

1. PowerShell コンソールで、Azure アカウントにログインします。このコマンドレットは、Azure アカウントのログイン資格情報をユーザーに求めます。ログイン後にアカウント設定がダウンロードされ、Azure PowerShell で使用できるようになります。

		Login-AzureRmAccount 

2. Azure サブスクリプションの一覧を取得します。

		Get-AzureRmSubscription

3. 使用するサブスクリプションを指定します。

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. 使用する変数を宣言します。次のサンプルを使用し、必要に応じて独自の値で置き換えます。

		$VNetName  = "VNet1"
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


## パート 2 - VNet の構成 


1. リソース グループを作成します。

		New-AzureRmResourceGroup -Name $RG -Location $Location

2. 仮想ネットワークのサブネット構成を作成し、*FrontEnd*、*BackEnd*、*GatewaySubnet*という名前を付けます。これらのプレフィックスは、上で宣言された VNet アドレス空間に含まれる必要があります。

		$fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
		$besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
		$gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

3. 仮想ネットワークを作成する。指定された DNS サーバーは、接続するリソースの名前を解決できる DNS サーバーである必要があります。この例では、パブリック IP アドレスを使用しましたが、実際には独自の値を使用してください。
	
		New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

4. 作成した仮想ネットワークの変数を指定します。

		$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

5. 動的に割り当てられたパブリック IP アドレスを要求します。この IP アドレスは、ゲートウェイが適切に機能するために必要です。後でゲートウェイをゲートウェイ IP 構成に接続します。
		
		$pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

## パート 3 - 信頼された証明書の追加

証明書の使用により、P2S を介して接続するクライアントが Azure によって認証されます。ルート証明書の .cer ファイル (公開キー) を Azure にインポートします。Base64 でエンコードされた X.509 (.cer) ファイルを Azure に追加すると、このファイルが表すルート証明書を信頼するよう Azure に伝えることになります。

エンタープライズ ソリューションを使用している場合、既存の証明書チェーンを使うことができます。エンタープライズ CA ソリューションを使用していない場合、自己署名ルート証明書を作成できます。自己署名証明書を作成する方法の 1 つに、makecert があります。*makecert* を使用して自己署名ルート証明書を作成する手順については、「[ポイント対サイト構成の自己署名ルート証明書の操作](vpn-gateway-certificates-point-to-site.md)」をご覧ください。

どの方法で証明書を取得する場合でも、証明書の .cer ファイルを Azure にアップロードします。また、クライアント証明書を生成し、接続するクライアントにインストールします。自己署名証明書はクライアントに直接インストールしません。クライアント証明書は後で、この記事の[クライアントの構成](#cc)に関するセクションで生成できます。
		
最大 20 個の信頼された証明書を Azure に追加できます。公開キーを取得するには、Base64 でエンコードされた X.509 (.cer) ファイルとして証明書をエクスポートします。.cer をエクスポートする方法の 1 つは、**certmger.msc** を開き、[個人] の [証明書] にある証明書を見つけることです。この証明書を右クリックし、秘密キーなしで "Base-64 encoded X.509(.CER)" としてエクスポートします。.cer ファイルのエクスポート先のファイル パスをメモします。次に示すのは、証明書の Base64 文字列表現を取得するコードのサンプルです。

信頼された証明書を Azure に追加します。この手順では、独自の .cer ファイル パスを使用する必要があります。この記事のパート 1 で "ARMP2SRootCert.cer" に設定した $P2SRootCertName 変数に特に気を付けてください。証明書の名前が異なる場合、それに応じて変数を調整します。
    
		$filePathForCert = "pasteYourCerFilePathHere"
		$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
		$CertBase64 = [system.convert]::ToBase64String($cert.RawData)
		$p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## パート 4 - VPN ゲートウェイの作成

VNet の仮想ネットワーク ゲートウェイを構成、作成します。*-GatewayType* は **Vpn** に、*-VpnType* は **RouteBased** にする必要があります。完了するまで最大 45 分かかることがあります。

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
		-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
		-VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
		-VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## パート 5 - VPN クライアント構成パッケージのダウンロード

P2S を使用して Azure に接続するクライアントには、クライアント証明書と VPN クライアント構成パッケージの両方がインストールされている必要があります。Windows クライアントの場合、VPN クライアント構成パッケージが利用できます。VPN クライアント パッケージには、Windows に組み込まれた、接続先の VPN に固有の VPN クライアント ソフトウェアを構成するための情報が含まれています。このパッケージでは、追加のソフトウェアはインストールされません。詳細については、「[VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md#point-to-site-connections)」を参照してください。

1. ゲートウェイの作成が完了した後、次のサンプルを利用し、クライアント構成パッケージをダウンロードします。

		Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
		-VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

2. PowerShell コマンドレットは URL リンクを返します。次に示すのは、返された URL の例です。

    	"https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"

3. Web ブラウザーに返されたリンクをコピーして貼り付け、パッケージをダウンロードします。その後、パッケージをクライアント コンピューターにインストールします。

4. クライアント コンピューターで **[ネットワークの設定]** に移動し、**[VPN]** をクリックします。接続が一覧で表示されます。接続先となる仮想ネットワークの名前が、次のように表示されます。

	![VPN client](./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png "VPN client")

## <a name="cc"></a>パート 6 - クライアント証明書のインストール
	
クライアント コンピューターのルート証明書からクライアント証明書 (*.pfx) を生成しインストールします。自分に適している方法でインストールしてください。

自己署名ルート証明書を使用しており、クライアント証明書の生成方法がわからない場合は、[こちらの記事](vpn-gateway-certificates-point-to-site.md)を参照してください。エンタープライズ ソリューションを使用している場合は、"NetBIOS domain name\\username" 形式ではなく、共通名の値の形式 "name@yourdomain.com" を使用してクライアント証明書を発行してください。

.pfx ファイルをダブルクリックすることで、クライアント証明書をコンピューターに直接インストールできます。

## パート 7 - Azure への接続

1. VNet に接続するには、クライアント コンピューターで [VPN 接続] に移動し、作成した VPN 接続を見つけます。仮想ネットワークと同じ名前が付いています。**[接続]** をクリックします。証明書を使用することを示すポップアップ メッセージが表示される場合があります。その場合、**[続行]** をクリックして、昇格された特権を使用します。

2. **接続**の状態ページで、**[接続]** をクリックして接続を開始します。**[証明書の選択]** 画面が表示された場合は、表示されているクライアント証明書が接続に使用する証明書であることを確認します。そうでない場合は、ドロップダウン矢印を使用して適切な証明書を選択し、**[OK]** をクリックします。

	![VPN client 2](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "VPN client connection")

3. これで接続が確立されたはずです。

	![VPN client 3](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "VPN client connection 2")

## パート 8 - 接続の確認

1. VPN 接続がアクティブであることを確認するには、管理者特権でのコマンド プロンプトを開いて、*ipconfig/all* を実行します。

2. 結果を表示します。受信した IP アドレスが、構成に指定したポイント対サイト VPN クライアント アドレス プール内のアドレスのいずれかであることに注意してください。結果は、次のようになります。
    
		PPP adapter VNet1:
			Connection-specific DNS Suffix .:
			Description.....................: VNet1
			Physical Address................:
			DHCP Enabled....................: No
			Autoconfiguration Enabled.......: Yes
			IPv4 Address....................: 172.16.201.3(Preferred)
			Subnet Mask.....................: 255.255.255.255
			Default Gateway.................:
			NetBIOS over Tcpip..............: Enabled

## 信頼されたルート証明書を追加または削除するには

証明書は、ポイント対サイト VPN の VPN クライアントの認証に使用されます。以下の手順に従って、ルート証明書を追加および削除できます。Base64 でエンコードされた X.509 (.cer) ファイルを Azure に追加すると、このファイルが表すルート証明書を信頼するよう Azure に伝えることになります。

PowerShell または Azure Portal を使用して、信頼されたルート証明書を追加したり削除したりできます。Azure Portal を使用してこれを行う場合、**[仮想ネットワーク ゲートウェイ]、[設定]、[ポイント対サイトの構成]、[ルート証明書]** の順に移動します。以下の手順では、PowerShell を使用してこれらのタスクについて説明します。

### 信頼されたルート証明書を追加する

信頼されたルート証明書 .cer ファイルを最大 20 個まで Azure に追加できます。ルート証明書を追加するには、次の手順に従います。

1. Azure に追加する新しいルート証明書を作成して準備します。Base64 でエンコードされた X.509 (.cer) として公開キーをエクスポートし、テキスト エディターで開きます。次に示す部分のみをコピーします。
 
	次の例で示すように値をコピーします。

	![証明書](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png "証明書")
	
2. 次の例では、証明書名とキーの情報を変数として指定しています。情報は独自のものに置き換えてください。

		$P2SRootCertName2 = "ARMP2SRootCert2.cer"
		$MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"


3. 新しいルート証明書を追加します。一度に追加できる証明書は 1 つだけです。

		Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2


4. 次のコマンドレットを使用して、新しい証明書が正しく追加されたことを確認できます。

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
		-VirtualNetworkGatewayName "GW"

### 信頼されたルート証明書を削除するには

信頼されたルート証明書を Azure から削除できます。信頼された証明書を削除すると、そのルート証明書から生成されたクライアント証明書は、ポイント対サイトを介して Azure に接続することができなくなります。クライアントが接続できるようにするには、Azure で信頼されている証明書から生成された新しいクライアント証明書をインストールする必要があります。

1. 信頼されたルート証明書を削除するには、次のサンプルを変更します。

	変数を宣言します。

		$P2SRootCertName2 = "ARMP2SRootCert2.cer"
		$MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. 証明書を削除します。

		Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
 
3. 次のコマンドレットを使用して、証明書が正常に削除されたことを確認します。

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
		-VirtualNetworkGatewayName "GW"

## 失効したクライアント証明書のリストを管理するには

クライアント証明書は失効させることができます。証明書失効リストを使用すると、個々のクライアント証明書に基づくポイント対サイト接続を選択して拒否することができます。ルート証明書 .cer を Azure から削除すると、失効したルート証明書によって生成または署名されたすべてのクライアント証明書のアクセス権は取り消されます。ルート証明書ではなく、特定のクライアント証明書を失効させることもできます。この場合、ルート証明書から生成された他の証明書は有効なままです。一般的な方法としては、ルート証明書を使用してチームまたは組織レベルでアクセスを管理し、失効したクライアント証明書を使用して、個々のユーザーの細かいアクセス制御を構成します。

### クライアント証明書の失効

1. 失効させるクライアント証明書の拇印を取得します。

		$RevokedClientCert1 = "ClientCert1"
		$RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. 失効した拇印のリストに拇印を追加します。

		Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
		-VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. 証明書失効リストに拇印が追加されたことを確認します。拇印は 1 つずつ追加する必要があります。

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### クライアント証明書の回復

失効したクライアント証明書のリストから拇印を削除することで、クライアント証明書を回復できます。

1.  失効したクライアント証明書の拇印のリストから拇印を削除します。

		Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
		-VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. 失効リストから拇印が削除されているかどうかを確認します。

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## 次のステップ

仮想ネットワークに仮想マシンを追加できます。手順については、[仮想マシンの作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md)に関するページを参照してください。

<!---HONumber=AcomDC_0831_2016-->