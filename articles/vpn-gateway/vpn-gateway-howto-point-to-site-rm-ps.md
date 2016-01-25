<properties 
   pageTitle="Azure Virtual Network へのポイント対サイト VPN Gateway 接続の構成 | Microsoft Azure"
   description="ポイント対サイト VPN 接続を作成することで、Azure Virtual Network に安全に接続します。この構成は、VPN デバイスを使用せず、離れた場所からプレミス間接続するときに便利であり、ハイブリッド ネットワーク構成で利用できます。この記事には、リソース マネージャー デプロイメント モデルで作成された VNet の PowerShell 指示が含まれています。"
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
   ms.date="01/11/2016"
   ms.author="cherylmc" />

# PowerShell を利用し、仮想ネットワークへのポイント対サイト接続を構成する

> [AZURE.SELECTOR]
- [PowerShell - Resource Manager](vpn-gateway-howto-point-to-site-rm-ps.md)
- [PowerShell - Classic](vpn-gateway-point-to-site-create.md)

ポイント対サイト構成では、クライアント コンピューターから仮想ネットワークへのセキュリティで保護された接続を個別に作成することができます。VPN 接続を確立するには、クライアント コンピューターからの接続を開始します。ポイント対サイトは、自宅や会議室など、リモートの場所から VNet に接続する場合、または仮想ネットワークに接続する必要があるクライアントの数が少ない場合などに適しています。ポイント対サイト接続を機能させるために、VPN デバイスや公開 IP アドレスは必要ありません。ポイント対サイト接続の詳細については、「[VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md#point-to-site-connections)」と[クロスプレミス接続](vpn-gateway-cross-premises-options.md)に関するページを参照してください。

この記事は、**Azure リソース マネージャー** デプロイメント モデルを使用して作成された VNet と VPN ゲートウェイを対象としています。サービス管理 (別名、クラシック デプロイメント モデル) を使用して作成された VNet 用にポイント対サイト接続を構成する場合は、[この記事](vpn-gateway-point-to-site-create.md)を参照してください。

[AZURE.INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## この構成について

このシナリオでは、ポイント対サイト接続で仮想ネットワークを作成します。ポイント対サイト接続は、ゲートウェイのある VNet、アップロードしたルート証明書 .cer ファイル、クライアント証明書、クライアント側の VPN 構成で構成されます。

この演習では、次の値を使用します。

- 名前 = **TestVNet**、アドレス空間として 192.168.0.0/16 と 10.254.0.0/16 を使用します。1 つの VNet に複数のアドレス空間を使用できます。
- サブネット名 = **FrontEnd**、192.168.1.0/24 を使用します。
- サブネット名 = **BackEnd**、10.254.1.0/24 を使用します。
- サブネット名 = **GatewaySubnet**、192.168.200.0/24 を使用します。ゲートウェイが機能するには、サブネット名 *GatewaySubnet* が必須となります。 
- VPN クライアント アドレス プール: 172.16.201.0/24。このポイント対サイト接続を利用して VNet に接続する VPN クライアントはこのプールから IP アドレスを受け取ります。
- サブスクリプション = サブスクリプションが複数ある場合、サブスクリプションが正しいことを確認してください。
- リソース グループ = **TestRG**
- 場所 = **East US**
- DNS サーバー = 名前の解決に利用する DNS サーバーの IP アドレス。
- GW 名 = **GW**
- パブリック IP 名 = **GWIP**
- VpnType = **RouteBased**


## 作業を開始する前に

Azure サブスクリプションがあり、この構成に必要な Azure PowerShell コマンドレットがインストールされていることを確認します (1.0.2 以降)。Azure サブスクリプションを持っていない場合は、[MSDN サブスクライバーの特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料試用版](http://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。
	
**PowerShell コマンドレット モジュールのインストールについて**

	[AZURE.INCLUDE [vpn-gateway-ps-rm-howto](../../includes/vpn-gateway-ps-rm-howto-include.md)]

## Azure のポイント対サイト接続を構成する

1. PowerShell コンソールで、Azure アカウントにログインします。このコマンドレットは、Azure アカウントのログイン資格情報をユーザーに求めます。ログイン後にアカウント設定がダウンロードされるため、Azure PowerShell で使用できるようになります。

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

		$vnet   = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

9. 動的に割り当てられたパブリック IP アドレスを要求します。この IP アドレスは、ゲートウェイが適切に機能するために必要です。後でゲートウェイをゲートウェイ IP 構成に接続します。
		
		$pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
		
10. Azure にルート証明書 .cer ファイルをアップロードします。会社の証明書環境のルート証明書を使用するか、自己署名のルート証明書を使用できます。最大 20 個のルート証明書をアップロードすることができます。*makecert* で自己署名ルート証明書を作成する方法については、この記事の終わりのセクションを参照してください。.cer ファイルにはルート証明書の秘密キーを含めないでください。
	
	以下はサンプルです。公的な証明書データをアップロードする際の問題は、文字列全体をスペースなしでコピーし、貼り付けなければならないということです。そのようにコピーしないと、アップロードは機能しません。この手順では、独自の証明書 .cer ファイルを使用する必要があります。下のサンプルからはコピーしないでください。

		$MyP2SRootCertPubKeyBase64 = "MIIDUzCCAj+gAwIBAgIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAMDQxEjAQBgNVBAoTCU1pY3Jvc29mdDEeMBwGA1UEAxMVQnJrIExpdGUgVGVzdCBSb290IENBMB4XDTEzMDExOTAwMjQxOFoXDTIxMDExOTAwMjQxN1owNDESMBAGA1UEChMJTWljcm9zb2Z0MR4wHAYDVQQDExVCcmsgTGl0ZSBUZXN0IFJvb3QgQ0EwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQC7SmE+iPULK0Rs7mQBO/6a6B6/G9BaMxHgDGzAmSG0Qsyt5e08aqgFnPdkMl3zRJw3lPKGha/JCvHRNrO8UpeAfc4IXWaqxx2iBipHjwmHPHh7+VB8lU0EJcUe7WBAI2n/sgfCwc+xKtuyRVlOhT6qw/nAi8e5don/iHPU6q7GCcnqoqtceQ/pJ8m66cvAnxwJlBFOTninhb2VjtvOfMQ07zPP+ZuYDPxvX5v3nd6yDa98yW4dZPuiGO2s6zJAfOPT2BrtyvLekItnSgAw3U5C0bOb+8XVKaDZQXbGEtOw6NZvD4L2yLd47nGkN2QXloiPLGyetrj3Z2pZYcrZBo8hAgMBAAGjaTBnMGUGA1UdAQReMFyAEOncRAPNcvJDoe4WP/gH2U+hNjA0MRIwEAYDVQQKEwlNaWNyb3NvZnQxHjAcBgNVBAMTFUJyayBMaXRlIFRlc3QgUm9vdCBDQYIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAA4IBAQCGyHhMdygS0g2tEUtRT4KFM+qqUY5HBpbIXNAav1a1dmXpHQCziuuxxzu3iq4XwnWUF1OabdDE2cpxNDOWxSsIxfEBf9ifaoz/O1ToJ0K757q2Rm2NWqQ7bNN8ArhvkNWa95S9gk9ZHZLUcjqanf0F8taJCYgzcbUSp+VBe9DcN89sJpYvfiBiAsMVqGPc/fHJgTScK+8QYrTRMubtFmXHbzBSO/KTAP5rBTxse88EGjK5F8wcedvge2Ksk6XjL3sZ19+Oj8KTQ72wihN900p1WQldHrrnbixSpmHBXbHr9U0NQigrJp5NphfuU5j81C8ixvfUdwyLmTv7rNA7GTAD"
		$p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $MyP2SRootCertPubKeyBase64

11. VNet の仮想ネットワーク ゲートウェイを作成します。GatewayType は Vpn に、VpnType は RouteBased にする必要があります。

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Standard -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## クライアントの構成

各クライアントがポイント対サイトを利用して Azure に接続するには、VPN クライアントを接続するように構成し、クライアントにクライアント証明書をインストールする必要があります。Windows クライアントの場合、VPN クライアント構成パッケージが利用できます。詳細については、[VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md#point-to-site-connections) を参照してください。

1. VPN クライアント構成パッケージをダウンロードします。この手順では、次のサンプルを利用し、クライアント構成パッケージをダウンロードします。

		Get-AzureRmVpnClientPackage -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

	PowerShell コマンドレットは URL リンクを返します。Web ブラウザーに返されたリンクをコピーして貼り付け、コンピューターにパッケージをダウンロードします。下は返された URL の例です。

    	"https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
	
2. クライアント コンピューターのルート証明書からクライアント証明書 (*.pfx) を生成しインストールします。自分に適している方法でインストールしてください。自己署名のルート証明書を使用しており、この方法がわからない場合、この記事の終わりにある指示を参照してください。

3. VNet に接続するには、クライアント コンピューターで [VPN 接続] に移動し、作成した VPN 接続を見つけます。仮想ネットワークと同じ名前が付いています。**[接続]** をクリックします。証明書を使用することを示すポップアップ メッセージが表示される場合があります。その場合、**[続行]** をクリックし、昇格された特権を使用します。

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

## makecert を利用し、自己署名ルート証明書を作成するには

makecert は、自己署名ルート証明書を作成する方法の 1 つです。次の手順により、段階的に作成します。

1. Windows 10 を実行しているコンピューターで、Windows 10 SDK を[このリンク](https://dev.windows.com/en-us/downloads/windows-10-sdk)からダウンロードし、インストールします。

2. インストール後、makecert.exe ユーティリティは C:\\Program Files (x86)\\Windows Kits\\10\\bin<arch> で見つかります。
		
	例:
	
		C:\Program Files (x86)\Windows Kits\10\bin\x64\makecert.exe

3. 次に、コンピューターの個人証明書ストアでルート証明書を作成し、インストールします。下の例では、後でアップロードする *.cer* ファイルが作成されます。次のコマンドを管理者として実行します。*RootCertificateName* は、証明書に使用する名前です。

	注記: 何も変更せずに次の例を実行した場合、ルート証明書と、対応する *RootCertificateName.cer* ファイルが作成されます。.cer ファイルはコマンドを実行したディレクトリにあります。証明書は Current User\\Personal\\Certificates にあります。

    	makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

	>[AZURE.NOTE]クライアント証明書の生成元となるルート証明書を作成したので、この証明書を秘密キーと共にエクスポートし、回復できる安全な場所に保存します。

## 自己署名のルート証明書からクライアント証明書を生成するには

以下の手順で自己署名のルート証明書からクライアント証明書を生成し、そのクライアント証明書をエクスポートし、インストールできます。

### クライアント証明書を生成

次の手順は、自己署名のルート証明書からクライアント証明書を生成する 1 つの方法を段階的に説明するものです。同じルート証明書から複数のクライアント証明書を生成できます。その後、各クライアント証明書をエクスポートし、クライアント コンピューターにインストールできます。

1. 自己署名のルート証明書の作成に使用した同じコンピューターで、管理者としてコマンド プロンプトを開きます。
2. クライアント証明書ファイルを保存するディレクトリに移動します。*RootCertificateName* は、生成した自己署名ルート証明書を指します。下の例 (RootCertificateName を自分のルート証明書の名前に変更します) を実行すると、個人用証明書ストアに "ClientCertificateName" という名前のクライアント証明書が作成されます。
3. 次のコマンドを入力します。

    	makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. 証明書はすべて、コンピューターの Current User\\Personal\\Certificates ストアに保存されます。この手順に基づいて、必要な数のクライアント証明書を生成することができます。

### クライアント証明書をエクスポートしてインストールする

仮想ネットワークに接続する各コンピューターへのクライアント証明書のインストールは、必須の手順です。次の手順では、クライアント証明書の手動インストールについて説明します。

1. クライアント証明書をエクスポートするには、*certmgr.msc* を使用します。エクスポートするクライアント証明書を右クリックして、**[すべてのタスク]**、**[エクスポート]** の順にクリックします。証明書のエクスポート ウィザードが開きます。
2. このウィザードで、**[次へ]** をクリックし、**[はい、秘密キーをエクスポートします]** をクリックし、**[次へ]** をクリックします。
3. **[エクスポート ファイルの形式]** ページは初期設定のままにします。その後、**[次へ]** をクリックします。  
4. **[セキュリティ]** ページで、秘密キーを保護する必要があります。パスワードを使用する場合、この証明書に設定するパスワードを必ず記録または記憶してください。**[次へ]** をクリックします。
5. **[エクスポートするファイル]** で、**[参照]** をクリックし、証明書をエクスポートする場所を選択します。**[ファイル名]** では、証明書ファイルの名前を指定します。その後、**[次へ]** をクリックします。
6. **[完了]** をクリックし、証明書をエクスポートします。	
3. *.pfx* ファイルを見つけ、クライアント コンピューターにコピーします。クライアント コンピューターで、*.pfx* ファイルをダブルクリックしてインストールします。**[ストアの場所]** は **[現在のユーザー]** のままにして **[次へ]** をクリックします。
4. **[インポートするファイル]** ページでは、何も変更しないでください。**[次へ]** をクリックします。
5. **[秘密キーの保護]** ページで、証明書にパスワードを使用した場合はそれを入力するか、証明書をインストールするセキュリティ プリンシパルが正しいことを確認し、**[次へ]** をクリックします。
6. **[証明書ストア]** ページでは既定の場所をそのまま選択し、**[次へ]** をクリックします。
7. **[完了]** をクリックします。証明書インストールの **[セキュリティ警告]** で **[はい]** をクリックします。これで証明書がインポートされます。

## 次のステップ

仮想ネットワークに仮想マシンを追加できます。手順については、[仮想マシンの作成](../virtual-machines/virtual-machines-windows-tutorial.md)に関するページを参照してください。

<!---HONumber=AcomDC_0114_2016-->