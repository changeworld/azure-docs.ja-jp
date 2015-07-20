<properties
   pageTitle="共存する ExpressRoute 接続とサイト間 VPN 接続の構成"
   description="このチュートリアルでは、ExpressRoute とサイト間 VPN 接続を共存させる構成について説明します。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/30/2015"
   ms.author="cherylmc"/>

# 共存する ExpressRoute とサイト間 VPN の接続の構成

ExpressRoute とサイト間 VPN を同じ仮想ネットワークに接続できるようになりました。2 つのシナリオと 2 つの構成手順から選択できます。

### シナリオ

次の異なる 2 つのシナリオがあります。次の図は、両方のシナリオを示しています。

- **シナリオ 1** - 1 つのローカル ネットワークを使用している場合は、ExpressRoute がアクティブなリンクになり、サイト間 VPN はバックアップになります。ExpressRoute の接続に失敗した場合は、サイト間 VPN の接続がアクティブになります。このシナリオは、高可用性にふさわしいものです。

- **シナリオ 2** - 2 つのローカル ネットワークを使用している場合は、一方を ExpressRoute 経由で Azure に接続し、他方をサイト間 VPN 経由で接続できます。この場合は、両方の接続が同時にアクティブになります。


![共存](media/expressroute-coexist/coexist1.jpg)


### 構成手順

選択できる 2 つの構成手順があります。接続先の既存の仮想ネットワークがある場合と、新しい仮想ネットワークを作成する場合とでは、選択できる構成手順が異なります。


- [接続を共存させて新しい VNet を作成する](#create-a-new-vnet-with-coexisting-connections): 仮想ネットワークがない場合、この手順では、新しい仮想ネットワークの作成と、新しい ExpressRoute 接続とサイト間 VPN 接続の作成について説明します。  


- [共存している接続に既存の VNet を構成する](#configure-your-existing-vnet-for-coexisting-connections): 仮想ネットワークを既存のサイト間 VPN 接続または ExpressRoute 接続に既に配置している場合があります。共存する接続を作成すると、ゲートウェイを削除するように要求されるため、共存させることができる新しいゲートウェイを構成します。これは、ゲートウェイを削除して接続を再作成する間、クロスプレミス接続でダウンタイムが発生しますが、VM やサービスを新しい仮想ネットワークに移行する必要がないことを意味します。移行するように構成されている場合でも、VM やサービスは、ゲートウェイの構成中にロード バランサーを経由して通信できます。

	この手順では、ゲートウェイの削除と、新しい ExpressRoute 接続とサイト間 VPN 接続の作成について説明します。新しい接続を作成する場合は、非常に特殊な順序で完了する必要がありますので注意してください。他の記事の手順を使用してゲートウェイと接続を作成しないでください。

### 注意と制限

- ExpressRoute ゲートウェイとサイト間 VPN ゲートウェイは両方とも、標準または高パフォーマンス ゲートウェイの SKU である必要があります。ゲートウェイの SKU については、「[ゲートウェイの SKU](../vpn-gateway/vpn-gateway-about-vpngateways.md)」を参照してください。
- ローカル ネットワークが ExpressRoute とサイト間 VPN の両方に接続されている場合は (シナリオ 1)、ローカル ネットワーク内で静的ルートを構成して、パブリック インターネットへのサイト間 VPN 接続をルーティングする必要があります。 
- サイト間 VPN ゲートウェイを追加する前に、まず ExpressRoute ゲートウェイを作成する必要があります。
- サイト間 VPN 経由で接続されたローカル ネットワークと ExpressRoute 経由で接続されたローカル ネットワーク間で (Azure 経由で) ルーティングすることはできません。
- どちらの手順も、既に ExpressRoute 回線を構成していることを前提としています。構成していない場合は、次の記事を参照してください。 

	- [ネットワーク サービス プロバイダー (NSP) による ExpressRoute 接続の構成](expressroute-configuring-nsps.md) 
	- [Exchange プロバイダーを通じて ExpressRoute 接続を構成する](expressroute-configuring-exps.md)


## 接続を共存させて新しい VNet を作成する

1. 最新バージョンの PowerShell コマンドレットを使用していることを確認します。[ダウンロード](http://azure.microsoft.com/downloads/) ページの PowerShell セクションから、最新の PowerShell コマンドレットをダウンロードしてインストールできます。
2. 仮想ネットワークのスキーマを作成します。ネットワーク構成ファイルの使用の詳細については、「[ネットワーク構成ファイルを使用した仮想ネットワークの構成](../virtual-network/virtual-networks-using-network-configuration-file.md)」を参照してください。構成スキーマの詳細については、「[Azure 仮想ネットワークの構成スキーマ](https://msdn.microsoft.com/library/azure/jj157100.aspx)」を参照してください。

	スキーマを作成する場合は、次の値を使用していることを確認します。

	- 仮想ネットワークのゲートウェイ サブネットは /27 (またはこれより短いプレフィックス) です。
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

	`Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'`
4. ExpressRoute ゲートウェイを作成します。GatewaySKU を *Standard* または *HighPerformance* に指定し、GatewayType を *DynamicRouting* に指定します。 

	次のサンプルを使用して、自身の値に置き換えます。

	`New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance`

5. ExpressRoute ゲートウェイを ExpressRoute 回線にリンクします。この手順が完了すると、オンプレミスのネットワークと Azure 間の接続が ExpressRoute 経由で確立されます。

	`New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET`

6. 次に、サイト間 VPN ゲートウェイを作成します。GatewaySKU を *Standard* または *HighPerformance* にし、GatewayType を *DynamicRouting* にする必要があります。

	`New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance`

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

	**重要:** サイト間 VPN のローカル サイトは、netcfg で定義されていません。代わりに、このコマンドレットを使用してローカル サイトのパラメーターを指定する必要があります。管理ポータルまたは netcfg ファイルを使用して、これを定義することはできません。

	次のサンプルを使用して、自身の値に置き換えます。

	`New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <local-network- gateway-public-IP> -AddressSpace <local-network-address-space>`

	ゲートウェイ ID とパブリック IP を含む仮想ネットワーク ゲートウェイ設定を取得するには、`Get-AzureVirtualNetworkGateway` コマンドレットを使用します。次の例を見てください。

		Get-AzureLocalNetworkGateway
		
		GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
		GatewayName          : MyLocalNetwork
		IpAddress            : 23.39.x.y
		AddressSpace         : {10.1.2.0/24}
		OperationDescription : Get-AzureLocalNetworkGateway
		OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
		OperationStatus      : Succeeded

	
8. ローカルの VPN デバイスを構成して新しいゲートウェイに接続します。VPN デバイスを構成するときに、手順 6 で取得した情報を使用します。VPN デバイス構成の詳細については、「[VPN デバイスの構成](vpn-gateway-configure-vpn-gateway-mp.md/#gather-information-for-your-vpn-device-configuration)」を参照してください。
	

9. Azure のサイト間 VPN ゲートウェイをローカル ゲートウェイにリンクします。

	この例では、connectedEntityId がローカル ゲートウェイ ID です。これは、`Get-AzureLocalNetworkGateway` を実行すると見つけることができます。`Get-AzureVirtualNetworkGateway` コマンドレットを使用すると、virtualNetworkGatewayId を見つけることができます。この手順の後に、ローカル ネットワークと Azure 間の接続がサイト間 VPN 接続経由で確立されます。


	`New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>`


## 共存している接続に既存の VNet を構成する

ExpressRoute 接続またはサイト間 VPN 接続経由で接続されている既存の仮想ネットワークがある場合は、既存の仮想ネットワークに接続する両方の接続を有効にするために、まず既存のゲートウェイを削除する必要があります。これは、この構成で作業している間、ローカル環境からゲートウェイ経由で仮想ネットワークに接続できなくなるということです。

**構成を開始する前に:** ゲートウェイ サブネットのサイズを増やせるように、仮想ネットワーク内に十分な IP アドレスが残っていることを確認します。


1. 最新バージョンの PowerShell コマンドレットを使用していることを確認します。[ダウンロード](http://azure.microsoft.com/downloads/) ページの PowerShell セクションから、最新の PowerShell コマンドレットをダウンロードしてインストールできます。
 
2. 既存のサイト間 VPN ゲートウェイを削除します。次のコマンドレットを使用して、自身の値に置き換えます。

	`Remove-AzureVNetGateway –VnetName MyAzureVNET`

2. 仮想ネットワークのスキーマをエクスポートします。次の PowerShell コマンドレットを使用して、自身の値に置き換えます。

	`Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”`
3. ゲートウェイ サブネットが /27 (またはこれより短いプレフィックス) になるように、ネットワーク構成ファイルのスキーマを編集します。次の例を見てください。ネットワーク構成ファイルの使用の詳細については、「[ネットワーク構成ファイルを使用した仮想ネットワークの構成](../virtual-network/virtual-networks-using-network-configuration-file.md)」を参照してください。構成スキーマの詳細については、「[Azure 仮想ネットワークの構成スキーマ](https://msdn.microsoft.com/library/azure/jj157100.aspx)」を参照してください。


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
5. この時点では、VNet にゲートウェイがありません。新しいゲートウェイを作成して接続を完了するには、この記事の**手順 3** の[接続を共存させて新しい VNet を作成する](#create-a-new-vnet-with-coexisting-connections)に進みます。



## 次のステップ
	
ExpressRoute の詳細については、「[ExpressRoute の技術概要](expressroute-introduction.md)」を参照してください。

VPN ゲートウェイの詳細については、「[VPN ゲートウェイについて](../vpn-gateway/vpn-gateway-about-vpngateways.md)」を参照してください。

<!---HONumber=July15_HO2-->