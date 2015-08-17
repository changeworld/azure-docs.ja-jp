<properties 
   pageTitle="複数のオンプレミスのサイトを仮想ネットワークに接続"
   description="この記事では、複数のローカルのオンプレミスのサイトを仮想ネットワークに接続する操作について説明します。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/04/2015"
   ms.author="cherylmc" />

# 複数のオンプレミスのサイトを仮想ネットワークに接続

複数のオンプレミスのサイトを単一の仮想ネットワークに接続できます。これは、ハイブリッドなクラウドのソリューションを構築する際は特に魅力的です。Azure 仮想ネットワーク ゲートウェイに複数の接続を行うことは、他のサイト間接続に非常に似ています。実際は、仮想ネットワークに対してルート ベース (または動的ルーティング) の VPN ゲートウェイが構成されている場合は、既存の Azure VPN ゲートウェイを使用できます。

ゲートウェイがポリシー ベース (静的ルーティング) の場合、マルチ サイトに適応するように仮想ネットワークを再構築する必要がなく、ゲートウェイ タイプをいつでも変更可能です。ただし、オンプレミスの VPN ゲートウェイがルート ベースの VPN 構成をサポートしているかどうかの確認は必要となります。ただ、ネットワークの構成ファイルに構成の設定を追加し、自身の仮想ネットワークから追加のサイトに複数の VPN 接続を作成します。

![マルチサイト VPN](./media/vpn-gateway-multi-site/IC727363.png)

## 考慮すべき点

**管理ポータルを使用して、この仮想ネットワークに変更を加えることはできません。** このリリースでは、管理ポータルを使用しないで、ネットワーク構成ファイルに変更を加える必要があります。管理ポータルで変更を行っても、この仮想ネットワークのマルチサイトのリファレンス設定が上書きされます。マルチサイトの手順を終える頃には、ネットワーク構成ファイルを使用することにもだいぶ慣れていることでしょう。しかし、複数の人がネットワーク構成の作業を行う場合、必ず全員がこの制限事項について知っておく必要があります。これは全く管理ポータルを使うことができないという意味ではありません。この特定の仮想ネットワークへの構成の変更を行うとき以外は管理ポータルを使うことができます。

## 開始する前に

構成を開始する前に、次の前提があることを確認してください。

- Azure サブスクリプション。Azure サブスクリプションを持っていない場合は、[MSDN サブスクライバーの特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料試用版](http://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。

- 各オンプレミスのロケーションと互換性のある VPN ハードウェア「[Virtual Netowrk に使用する VPN デバイスについて](http://go.microsoft.com/fwlink/p/?linkid=615099)」を参照して、使用するデバイスが互換性のあるものであることを確認してください。

- 各 VPN デバイスの外部接続用パブリック IPv4 IP アドレス。IP アドレスを NAT の内側に割り当てることはできません。これが要件です。

-   最新バージョンの Azure PowerShell コマンドレット。[ダウンロード ページ](http://azure.microsoft.com/downloads/)の Windows PowerShell セクションから最新バージョンをダウンロードしてインストールできます。

- VPN ハードウェアの構成に詳しい作業者VPN デバイスを構成する際に、管理ポータルから自動生成の VPN スクリプトを使用することはできません。そのため、VPN デバイスの構成に精通している必要があり、そうでなければ精通している人と一緒に作業を行ってください。

- 仮想ネットワークに使用する予定の IP アドレス範囲 (まだ 1 つも作成していない場合)。

- 接続する各ローカル ネットワークの IP アドレス範囲。接続しようとしている各ローカル ネットワーク サイトの IP アドレス範囲が重複しないように確認する必要があります。そうしないと、構成をアップロードする際に、管理ポータルまたは REST API によって拒否されます。例えば、2 つのローカル ネットワーク サイトの両方が IP アドレス範囲 10.2.3.0/24 を含んでおり、送信先アドレスが 10.2.3.3 のパッケージを持っている場合、アドレス範囲が重複しているため、Azure はパッケージを送ろうとしているのがどちらのサイトなのかわかりません。ルーティングの問題を避けるため、Azure は重複した範囲を持つ構成ファイルをアップロードすることを許可しません。

## 仮想ネットワークとゲートウェイの作成

1. **動的ルーティング ゲートウェイでサイト間 VPN を作成します** すでに作成されている場合は、作業の必要はありません。 [仮想ネットワーク構成設定のエクスポート](#export)に進んでください。まだ作成していない場合は、以下の作業を行います。

	**サイト間仮想ネットワークがすでに存在するが、静的ルーティング ゲートウェイである場合:** **1.** ゲートウェイ タイプを動的ルーティングに変更します。マルチサイトの VPN は動的ルーティング ゲートウェイを必要とします。ゲートウェイ タイプを変更するには、最初に既存のゲートウェイを削除し、新規で作成します。手順については、「[VPN ゲートウェイ ルーティング タイプの変更](vpn-gateway-configure-vpn-gateway-mp.md/#how-to-change-your-vpn-gateway-type)」を参照してください。**2.** 新しいゲートウェイを構成し、VPN トンネルを作成します。手順については、「[管理ポータルでの VPN ゲートウェイの構成](vpn-gateway-configure-vpn-gateway-mp.md)」を参照してください。
	
	**サイト間仮想ネットワークが存在しない場合:** **1.** 次の手順によりサイト間仮想ネットワークを作成します。[管理ポータルでのサイト間 VPN を使用した仮想ネットワークの作成](vpn-gateway-site-to-site-create.md)**2.** 次の手順により動的ルーティング ゲートウェイを構成します。[管理ポータルでの VPN ゲートウェイの構成](vpn-gateway-configure-vpn-gateway-mp.md)必ずゲートウェイ タイプに**動的ルーティング**を選択してください。



1. **<a name="export"></a>仮想ネットワーク構成設定をエクスポートします。** ネットワーク構成ファイルをエクスポートするには、「[ネットワーク設定をエクスポートするには](../virtual-network/virtual-networks-using-network-configuration-file.md#export-and-import-virtual-network-settings-using-the-management-portal)」を参照してください。エクスポートするファイルは新しいマルチサイト設定の構成に使われます。

1. **ネットワーク構成ファイルを開きます。** 前のステップでダウンロードしたネットワーク構成ファイルを開きます。任意の xml エディターを使用してください。ファイルは次のようになります。

		<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
		  <VirtualNetworkConfiguration>
		    <LocalNetworkSites>
		      <LocalNetworkSite name="Site1">
		        <AddressSpace>
		          <AddressPrefix>10.0.0.0/16</AddressPrefix>
		          <AddressPrefix>10.1.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
		      </LocalNetworkSite>
		      <LocalNetworkSite name="Site2">
		        <AddressSpace>
		          <AddressPrefix>10.2.0.0/16</AddressPrefix>
		          <AddressPrefix>10.3.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
		      </LocalNetworkSite>
		    </LocalNetworkSites>
		    <VirtualNetworkSites>
		      <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
		        <AddressSpace>
		          <AddressPrefix>10.20.0.0/16</AddressPrefix>
		          <AddressPrefix>10.21.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="FE">
		            <AddressPrefix>10.20.0.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="BE">
		            <AddressPrefix>10.20.1.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="GatewaySubnet">
		            <AddressPrefix>10.20.2.0/29</AddressPrefix>
		          </Subnet>
		        </Subnets>
		        <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="Site1">
		              <Connection type="IPsec" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
		      </VirtualNetworkSite>
		    </VirtualNetworkSites>
		  </VirtualNetworkConfiguration>
		</NetworkConfiguration>

1. ネットワーク構成ファイルに複数のサイトのリファレンスを追加します。サイト リファレンスの情報を追加または削除する場合、ConnectionsToLocalNetwork/LocalNetworkSiteRef に対して構成の変更を行います。新しいローカル サイトのリファレンスを追加することで、Azure が新しくトンネルを作成します。下の例で、ネットワーク構成はシングル サイト接続のものです。

		<Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

	追加のサイト リファレンスを加える (マルチサイトの構成を作成する) には、下の例で示すように、単純に "LocalNetworkSiteRef" の行を追加します。

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

1. **ネットワーク構成ファイルを保存し、インポートします。** ネットワーク構成ファイルのインポートをするには、「[ネットワーク設定をインポートするには](../virtual-network/../virtual-network/virtual-networks-using-network-configuration-file.md#export-and-import-virtual-network-settings-using-the-management-portal)」を参照してください。変更をしてこのファイルをインポートすると、新しいトンネルが追加されます。トンネルは先ほど作成した動的ゲートウェイを使用します。



1. **VPN トンネル用に事前共有キーをダウンロードします。** 新しいトンネルが追加されたら、PowerShell コマンドレット "Get-AzureVNetGatewayKey" を使用して、IPsec/IKE 事前共有キーを各トンネル用に取得してください。

	次に例を示します。

		PS C:\> Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

		PS C:\> Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

	必要に応じて、*Get Virual Network Gateway Shared Key* という REST API を使用して事前共有キーを取得することもできます。

## 接続の確認

**マルチサイトのトンネルの状態を確認します。** 各トンネルのキーをダウンロードしたら、接続を確認します。*Get-AzureVnetConnection* を使用して、下の例で示すように仮想ネットワークのトンネルのリストを取得します。VNet1 とは VNet の名前です。

		PS C:\Users\yushwang\Azure> Get-AzureVnetConnection -VNetName VNET1
		
		ConnectivityState         : Connected
		EgressBytesTransferred    : 661530
		IngressBytesTransferred   : 519207
		LastConnectionEstablished : 5/2/2014 2:51:40 PM
		LastEventID               : 23401
		LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
		LastEventTimeStamp        : 5/2/2014 2:51:40 PM
		LocalNetworkSiteName      : Site1
		OperationDescription      : Get-AzureVNetConnection
		OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
		OperationStatus           : Succeeded
		
		ConnectivityState         : Connected
		EgressBytesTransferred    : 789398
		IngressBytesTransferred   : 143908
		LastConnectionEstablished : 5/2/2014 3:20:40 PM
		LastEventID               : 23401
		LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
		LastEventTimeStamp        : 5/2/2014 2:51:40 PM
		LocalNetworkSiteName      : Site2
		OperationDescription      : Get-AzureVNetConnection
		OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
		OperationStatus           : Succeeded

## 次のステップ

VPN ゲートウェイについてより詳しくは、「[VPN Gateway について](../vpn-gateway/vpn-gateway-about-vpngateways.md)」を参照してください。

<!---HONumber=August15_HO6-->