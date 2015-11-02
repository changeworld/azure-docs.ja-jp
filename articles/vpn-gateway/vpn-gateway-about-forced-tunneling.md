<properties pageTitle="Microsoft Azure VPN Gateways の強制トンネリングの構成 | Microsoft Azure" description="クロスプレミスの VPN ゲートウェイを含む仮想ネットワークの場合、インターネットへのすべてのトラフィックをオンプレミスの場所にリダイレクトまたは ”強制的” に戻すことができます。" services="vpn-gateway" documentationCenter="na" authors="cherylmc" manager="carolz" editor="" tags="azure-service-management"/>
<tags  
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2015"
   ms.author="cherylmc" />

# 強制トンネリングについて

この記事は、従来のデプロイ モデルを使用して作成された VNet および VPN ゲートウェイを対象としています。リソース マネージャー モデルを使用して作成された VNet および VPN ゲートウェイの強制トンネリングの構成に関する指示がある場合、このページの先頭へのリンクを追加します。

>[AZURE.NOTE]Azure は現在、2 つのデプロイメント モデル (リソース マネージャーおよびクラシック) で使用できることを理解しておくことは重要です。構成を開始する前に、デプロイ モデルとツールについて理解しておくようにしてください。デプロイメント モデルについては、「[Azure デプロイ モデル](../azure-classic-rm.md)」を参照してください。

## 強制トンネリングについて

強制トンネリングによって、検査および監査には、サイト間の VPN トンネルを介して、インターネットへのすべてのトラフィックをオンプレミス ロケーションにリダイレクトまたは "強制" できます。これは、ほとんどの企業 IT ポリシーの重要なセキュリティ要件です。強制トンネリングを使用しない場合、Azure の VM からインターネットへのトラフィックは、トラフィックを検査または監査できるオプションを使用せずに、常に Azure ネットワーク インフラストラクチャからインターネットへ直接トラバースします。認証されていないインターネット アクセスは、情報の漏えいまたは他の種類のセキュリティ侵害を招く可能性があります。

以下の図には、強制トンネリングのしくみを示しています。

![強制トンネリング](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

上記の例では、フロントエンドのサブネットは、トンネリングを強制されません。フロントエンドのサブネット内のワークロードは、直接、インターネットから顧客の要求を承認し応答し続けることができます。Mid-tier およびMid-tier のサブネットは、トンネリングを強制されます。これら 2 つのサブネットからのインターネットへのオウトバウンド接続は、S2S VPN トンネルの 1 つを介して、オンプレミス サイトに ”強制” リダイレクトされます。これにより、必要な多層サービス アーキテクチャが継続的に使用可能になっている間は、Azure Virtual Machines やクラウド サービスからのインターネット アクセスを制限および検査することができます。また、仮想ネットワーク内に、インターネットに接続されたワークロードがない場合、全体の仮想ネットワークに強制トンネリングを適用するオプションがあります。

## 要件と考慮事項

Azure では、強制トンネリングは仮想ネットワークのユーザー定義ルートを使用して構成されます。オンプレミス サイトへのトラフィックのリダイレクトは、Azure VPN Gateway への既定のルートとして表されます。以下のセクションでは、Azure Virtual Network のルーティング テーブルおよびルートの現在の制限を一覧表示します。



-  各仮想ネットワーク サブネットには、システム ルーティング テーブルが組み込まれています。システム ルーティング テーブルには、次の 3 つのグループがあります。

	- **ローカル VNet ルーティング:** 直接、同じ仮想ネットワーク内の宛先 VM へ
	
	- **オンプレミス ルート:** Azure VPN ゲートウェイへ
	
	- **既定のルート:**直接、インターネットへ。前の 2 つのルートが網羅していないプライベート IP アドレスへ宛先送信されるパケットは削除されることに注意してください。



-  ユーザー定義ルートをリリースすることにより、既定のルートを追加するルーティング テーブルを作成し、そのルーティング テーブルを、ご使用の VNet サブネットに関連付け、それらのサブネットでの強制トンネリングを有効にします。

- 強制トンネリングは、動的ルーティング VPN ゲートウェイ (静的ゲートウェイではない) を持つ VNet に関連付ける必要があります。仮想ネットワークに接続されたクロスプレミス ローカル サイト間で「既定のサイト」を設定する必要があります。

- ExpressRoute の強制トンネリングは、このメカニズムを介して構成されていませんが、代わりに ExpressRoute BGP ピアリング セッションを介して既定ルートを告知することで有効になっています。詳細については、[ExpressRoute Documentation](https://azure.microsoft.com/documentation/services/expressroute/) を参照してください。

## 構成の概要

以下の手順は、仮想ネットワークで強制トンネリングを指定するのに役立ちます。構成手順は、以下に示す仮想ネットワークの netcfg ファイルの例に対応します。

この例では、仮想ネットワークである ”MultiTier-VNet” には、*Frontend*、*Midtier*、および*Backend* の 3 つのサブネットがあり、 *DefaultSiteHQ*、および 3 つの*Branch* の計 4 つのクロス プレミス接続があります。以下の手順で *DefaultSiteHQ* を強制トンネリングの既定のサイト接続として設定し、強制トンネリングが使用されるように *Midtier* と *Backend* を構成します。

	<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
	</VirtualNetworkSite>

### 前提条件

- Azure サブスクリプション

- 構成済みの仮想ネットワーク。

- Web Platform Installer を使用した、Azure PowerShell コマンドレットの最新バージョン。[ダウンロード ページ](http://azure.microsoft.com/downloads/)の **Windows PowerShell** セクションから最新バージョンをダウンロードしてインストールできます。

## 強制トンネリングについて

強制トンネリングを構成するには、以下の手順を使用します。

1. ルーティング テーブルを作成します。ルート テーブルを作成するには、以下のコマンドレットを使用します。

		New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"

1. 既定のルートをルーティング テーブルに追加します。

	次のコマンドレット例は、手順 1. で作成された、ルーティング テーブルに既定のルートを追加します。サポートされている唯一のルートには、"VPNGateway" ネクストホップへの宛先プレフィックスの「0.0.0.0/0」が付いています。
 
		Set-AzureRoute –RouteTableName "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway

1. サブネットには、ルーティング テーブルを関連付けます。

	ルーティング テーブルが作成され、ルートが追加された後は、以下のコマンドレットを使用して、ルート テーブルを VNet サブネットに追加または関連付けます。以下のサンプルでは、ルート テーブル "MyRouteTable" を VNet MultiTier-VNet の Midtier および Backend サブネットに追加します。

		Set-AzureSubnetRouteTable -VNetName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"

		Set-AzureSubnetRouteTable -VNetName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"

1. 強制トンネリングに既定のサイトを割り当てます。

	前の手順で、サンプルのコマンドレット スクリプトが、ルーティング テーブルを作成して、ルート テーブルを 2 つの VNet のサブネットに関連付けました。残りの手順は、仮想ネットワークの複数サイト間接続でローカルのサイトを規定のサイトまたはトンネルとして選択します。

		$DefaultSite = @("DefaultSiteHQ")
		Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"

## その他の PowerShell コマンドレット

強制トンネリングの構成に役立つその他の PowerShell コマンドレットを以下に示します。

**ルート テーブルを削除するには:**

	Remove-AzureRouteTable -RouteTableName <routeTableName>

**ルート テーブルを一覧表示するには:**

	Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]

**ルート テーブルからルートを削除するには:**

	Remove-AzureRouteTable –Name <routeTableName>

**サブネットからのルートを削除するには:**

	Remove-AzureSubnetRouteTable –VNetName <virtualNetworkName> -SubnetName <subnetName>

**サブネットに関連付けられたルート テーブルの一覧表示するには:**
	
	Get-AzureSubnetRouteTable -VNetName <virtualNetworkName> -SubnetName <subnetName>

**VNet VPN ゲートウェイから既定のサイトを削除するには:**

	Remove-AzureVnetGatewayDefaultSites -VNetName <virtualNetworkName>

## 次のステップ


ユーザー定義のルートについては、「[ユーザー定義のルートと IP 転送](../virtual-network/virtual-networks-udr-overview.md)」を参照してください。

ネットワーク トラフィックをセキュリティで保護する方法については、「[ネットワーク セキュリティ グループ (NSG) について](../virtual-network/virtual-networks-nsg.md)」を参照してください。Azure VNet ゲートウェイ サブネットにはネットワーク セキュリティ グループを適用してはならないことに注意してください。

<!---HONumber=Oct15_HO4-->