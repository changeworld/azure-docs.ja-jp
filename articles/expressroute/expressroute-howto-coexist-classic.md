<properties
   pageTitle="共存できる ExpressRoute とサイト間 VPN の接続の構成 | Microsoft Azure"
   description="このチュートリアルでは、共存できる ExpressRoute 接続とサイト間 VPN 接続を構成する手順について説明します。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2015"
   ms.author="cherylmc"/>

# VNet の共存する ExpressRoute 接続とサイト間 VPN 接続の構成

サイト間 VPN と ExpressRoute が構成可能な場合、いくつかの利点があります。ExressRoute 用にセキュリティで保護されたフェールオーバー パスとしてサイト間 VPN を構成したり、サイト間 VPN を使用して、ネットワークの一部ではないものの、ExpressRoute 経由で接続されているサイトに接続したりすることができます。この記事では、両方のシナリオを構成する手順について説明します。この記事は、クラシック デプロイメント モードを使用して作成された接続を対象としています。

>[AZURE.IMPORTANT]Azure は現在、2 つのデプロイ モデル (リソース マネージャーおよび従来のモデル) で使用できることを理解しておくことが重要です。構成を開始する前に、デプロイ モデルとツールについて理解しておくようにしてください。デプロイメント モデルについては、「[Azure デプロイ モデル](../azure-classic-rm.md)」を参照してください。


ExpressRoute 回線を事前に構成してから、以下の手順に従ってください。以下の手順に従う前に、必ず、[ExpressRoute 回線の作成](expressroute-howto-circuit-classic.md)と[ルーティングの構成](expressroute-howto-routing-classic.md)に関するガイドに従ってください。

## 制限と制限事項

- **トランジット ルーティングはサポートされていません:** サイト間 VPN 経由で接続されたローカル ネットワークと ExpressRoute 経由で接続されたローカル ネットワーク間で (Azure 経由で) ルーティングすることはできません。
- **ポイント対サイトはサポートされていません:** ExpressRoute に接続されているのと同じ VNet に対しては、ポイント対サイト VPN 接続を有効にできません。ポイント対サイト VPN と ExpressRoute を同じ VNet に共存させることはできません。
- **標準または高性能ゲートウェイのみ:** ExpressRoute ゲートウェイとサイト間 VPN ゲートウェイの両方で標準または高性能ゲートウェイを使用する必要があります。ゲートウェイの SKU については、[ゲートウェイの SKU](../vpn-gateway/vpn-gateway-about-vpngateways.md) に関するページをご覧ください。
- **静的ルートの要件:** ローカル ネットワークが ExpressRoute とサイト間 VPN の両方に接続されている場合は、ローカル ネットワーク内で静的ルートを構成して、パブリック インターネットへのサイト間 VPN 接続をルーティングする必要があります。
- **ExpressRoute ゲートウェイを最初に構成する必要があります:** サイト間 VPN ゲートウェイを追加する前に、まず、ExpressRoute ゲートウェイを作成する必要があります。

## ExpressRoute のフェールオーバー パスとしてサイト間 VPN を構成する

ExpressRoute 用にバックアップとしてサイト間 VPN 接続を構成することができます。これは、Azure のプライベート ピアリング パスにリンクされている仮想ネットワークにのみ適用されます。Azure パブリックおよび Microsoft ピアリングを通じてアクセス可能なサービスの VPN ベースのフェールオーバー ソリューションはありません。ExpressRoute 回線は常にプライマリ リンクです。データは、ExpressRoute 回線で障害が発生した場合にのみ、サイト間 VPN パスを通過します。

![共存](media/expressroute-howto-coexist-classic/scenario1.jpg)

## ExpressRoute 経由で接続されていないサイトに接続するようにサイト間 VPN を構成する

サイト間 VPN 経由で Azure に直接接続するサイトや、ExpressRoute 経由で接続するサイトがあるネットワークを構成することができます。

![共存](media/expressroute-howto-coexist-classic/scenario2.jpg)

>[AZURE.NOTE]トランジット ルーターとして仮想ネットワークを構成することはできません。

## 作成と構成

共存できる接続を構成するために、選択できる 2 とおりの手順があります。接続先にする既存の仮想ネットワークがある場合と、新しい仮想ネットワークを作成する場合とでは、選択できる構成手順が異なります。

- **新しい仮想ネットワークおよび共存する接続を作成する**:
	
	仮想ネットワークがまだない場合、この手順では、新しい仮想ネットワークを作成し、新しい ExpressRoute 接続とサイト間 VPN 接続を作成する方法を説明します。構成するには、この記事の「**ExpressRoute とサイト間接続の両方で新しい仮想ネットワークを作成する**」の手順に従います。

- **共存する接続用に既存の仮想ネットワークを構成する**:

	既存のサイト間 VPN 接続または ExpressRoute 接続を使用して、仮想ネットワークを既に配置している場合があります。「**既存の仮想ネットワークの共存する接続を構成する**」では、ゲートウェイを削除し、新しい ExpressRoute 接続とサイト間 VPN 接続を作成する手順について説明します。新しい接続を作成する場合は、非常に特殊な順序で完了する必要がありますので注意してください。他の記事の手順を使用してゲートウェイと接続を作成しないでください。

	この手順では、共存できる接続を作成するために、ゲートウェイを削除する必要があるので、新しいゲートウェイを構成します。これは、ゲートウェイを削除して接続を再作成する間、クロスプレミス接続でダウンタイムが発生しますが、VM やサービスを新しい仮想ネットワークに移行する必要がないことを意味します。移行するように構成されている場合でも、VM やサービスは、ゲートウェイの構成中にロード バランサーを経由して通信できます。


## ExpressRoute とサイト間接続の両方で新しい仮想ネットワークを作成する

この手順では、VNet を作成し、共存するサイト間接続と ExpressRoute 接続を作成します。

1. 最新バージョンの PowerShell コマンドレットを使用していることを確認します。[ダウンロード](http://azure.microsoft.com/downloads/) ページの PowerShell セクションから、最新の PowerShell コマンドレットをダウンロードしてインストールできます。

2. 仮想ネットワークのスキーマを作成します。ネットワーク構成ファイルの使用の詳細については、「[ネットワーク構成ファイルを使用した Virtual Network の構成](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal)」を参照してください。構成スキーマの詳細については、「[Azure Virtual Network の構成スキーマ](https://msdn.microsoft.com/library/azure/jj157100.aspx)」を参照してください。

	スキーマを作成する場合は、次の値を使用していることを確認します。

	- 仮想ネットワークのゲートウェイ サブネットは /27 またはこれより短いプレフィックス (/26 や /25 など) にする必要があります。
	- ゲートウェイ接続の種類は "Dedicated" です。

		      <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
		        <AddressSpace>
		          <AddressPrefix>10.17.159.192/26</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="Subnet-1">
		            <AddressPrefix>10.17.159.192/27</AddressPrefix>
		          </Subnet>
		          <Subnet name="GatewaySubnet">
		            <AddressPrefix>10.17.159.224/27</AddressPrefix>
		          </Subnet>
		        </Subnets>
		        <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
		      </VirtualNetworkSite>

3. xml スキーマ ファイルを作成して構成したら、ファイルをアップロードします。これにより、仮想ネットワークが作成されます。

	次のコマンドレットを使用してファイルをアップロードし、値を自身の値に置き換えます。

		Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'

4. ExpressRoute ゲートウェイを作成します。GatewaySKU を *Standard* または *HighPerformance* に指定し、GatewayType を *DynamicRouting* に指定します。

	次のサンプルを使用して、自身の値に置き換えます。

		New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance

5. ExpressRoute ゲートウェイを ExpressRoute 回線にリンクします。この手順が完了すると、オンプレミスのネットワークと Azure 間の接続が ExpressRoute 経由で確立されます。

		New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET

6. 次に、サイト間 VPN ゲートウェイを作成します。GatewaySKU を *Standard* または *HighPerformance* にし、GatewayType を *DynamicRouting* にする必要があります。

		New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance

	ゲートウェイ ID とパブリック IP を含む仮想ネットワーク ゲートウェイ設定を取得するには、`Get-AzureVirtualNetworkGateway` コマンドレットを使用します。

		Get-AzureVirtualNetworkGateway

		GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
		GatewayName          : S2SVPN
		LastEventData        :
		GatewayType          : DynamicRouting
		LastEventTimeStamp   : 5/29/2015 4:41:41 PM
		LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
		LastEventID          : 23002
		State                : Provisioned
		VIPAddress           : 104.43.x.y
		DefaultSite          :
		GatewaySKU           : HighPerformance
		Location             :
		VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
		SubnetId             :
		EnableBgp            : False
		OperationDescription : Get-AzureVirtualNetworkGateway
		OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
		OperationStatus      : Succeeded

7. ローカル サイト VPN ゲートウェイのエンティティを作成します。このコマンドは、オンプレミスの VPN ゲートウェイを構成しません。代わりに、パブリック IP やオンプレミスのアドレス空間などのローカル ゲートウェイ設定を指定できるため、Azure VPN ゲートウェイがこれに接続できます。

	> [AZURE.IMPORTANT]サイト間 VPN のローカル サイトは、netcfg で定義されていません。代わりに、このコマンドレットを使用してローカル サイトのパラメーターを指定する必要があります。管理ポータルまたは netcfg ファイルを使用して、これを定義することはできません。

	次のサンプルを使用して、自身の値に置き換えます。

	`New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <local-network- gateway-public-IP> -AddressSpace <local-network-address-space>`

	**注:** ローカル ネットワークに複数のルートがある場合は、それらすべてを配列として渡すことができます。$MyLocalNetworkAddress = @("10.1.2.0/24","10.1.3.0/24","10.2.1.0/24")


	ゲートウェイ ID とパブリック IP を含む仮想ネットワーク ゲートウェイ設定を取得するには、`Get-AzureVirtualNetworkGateway` コマンドレットを使用します。次の例を参照してください。

		Get-AzureLocalNetworkGateway

		GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
		GatewayName          : MyLocalNetwork
		IpAddress            : 23.39.x.y
		AddressSpace         : {10.1.2.0/24}
		OperationDescription : Get-AzureLocalNetworkGateway
		OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
		OperationStatus      : Succeeded


8. ローカルの VPN デバイスを構成して新しいゲートウェイに接続します。VPN デバイスを構成するときに、手順 6 で取得した情報を使用します。VPN デバイス構成の詳細については、「[VPN デバイスの構成](http://go.microsoft.com/fwlink/p/?linkid=615099)」を参照してください。

9. Azure のサイト間 VPN ゲートウェイをローカル ゲートウェイにリンクします。

	この例では、connectedEntityId がローカル ゲートウェイ ID です。これは、`Get-AzureLocalNetworkGateway` を実行すると見つけることができます。`Get-AzureVirtualNetworkGateway` コマンドレットを使用すると、virtualNetworkGatewayId を見つけることができます。この手順の後に、ローカル ネットワークと Azure 間の接続がサイト間 VPN 接続経由で確立されます。


	`New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>`

## 既存の仮想ネットワークの共存する接続を構成する

ExpressRoute 接続またはサイト間 VPN 接続経由で接続されている既存の仮想ネットワークがある場合は、既存の仮想ネットワークに接続する両方の接続を有効にするために、まず、既存のゲートウェイを削除する必要があります。これは、この構成で作業している間、ローカル環境からゲートウェイ経由で仮想ネットワークに接続できなくなるということです。

**構成を開始する前に:** ゲートウェイ サブネットのサイズを増やせるように、仮想ネットワーク内に十分な IP アドレスが残っていることを確認します。

1. 最新バージョンの PowerShell コマンドレットをダウンロードします。[ダウンロード](http://azure.microsoft.com/downloads/) ページの PowerShell セクションから、最新の PowerShell コマンドレットをダウンロードしてインストールできます。

2. 既存のサイト間 VPN ゲートウェイを削除します。次のコマンドレットを使用して、自身の値に置き換えます。

	`Remove-AzureVNetGateway –VnetName MyAzureVNET`

2. 仮想ネットワークのスキーマをエクスポートします。次の PowerShell コマンドレットを使用して、自身の値に置き換えます。

	`Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”`

3. ゲートウェイ サブネットが /27 またはこれより短いプレフィックス (/26 や /25 など) になるように、ネットワーク構成ファイルのスキーマを編集します。次の例を参照してください。ネットワーク構成ファイルの使用の詳細については、「[ネットワーク構成ファイルを使用した Virtual Network の構成](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal)」を参照してください。構成スキーマの詳細については、「[Azure Virtual Network の構成スキーマ](https://msdn.microsoft.com/library/azure/jj157100.aspx)」を参照してください。

          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>

4. 以前のゲートウェイがサイト間 VPN であった場合は、接続の種類を **Dedicated** に変更する必要もあります。

		         <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>

5. この時点では、VNet にゲートウェイがありません。新しいゲートウェイを作成し、接続を完了するために、この記事の「[ExpressRoute とサイト間接続の両方で新しい仮想ネットワークを作成する](#create-a-new-virtual-network-with-both-expressroute-and-site-to-site-connectivity)」の**手順 3.** に進むことができます。

## 次のステップ

ExpressRoute の詳細については、「[ExpressRoute の FAQ](expressroute-faqs.md)」を参照してください。

<!---HONumber=Sept15_HO4-->