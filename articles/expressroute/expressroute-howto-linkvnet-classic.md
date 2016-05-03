<properties 
   pageTitle="クラシック デプロイ モデルと PowerShell を使用した ExpressRoute 回線への仮想ネットワークのリンク | Microsoft Azure"
   description="このドキュメントでは、クラシック デプロイ モデルと PowerShell を使用して ExpressRoute 回線に仮想ネットワーク (VNet) をリンクする方法の概要について説明します。"
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/14/2016"
   ms.author="ganesr" />

# ExpressRoute 回線への仮想ネットワークのリンク

> [AZURE.SELECTOR]
- [Azure ポータル - Resource Manager](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell - Resource Manager](expressroute-howto-linkvnet-arm.md)
- [PowerShell - クラシック](expressroute-howto-linkvnet-classic.md)



この記事では、クラシック デプロイ モデルと PowerShell を使用して ExpressRoute 回線に仮想ネットワーク (VNet) をリンクする方法について説明します。仮想ネットワークは、同じサブスクリプションにあっても、別のサブスクリプションの一部であってもかいまいません。

**Azure のデプロイ モデルについて**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## 構成の前提条件

1. Azure PowerShell モジュールの最新バージョンが必要になります。[Azure ダウンロード ページ](https://azure.microsoft.com/downloads/)の PowerShell セクションから、最新の PowerShell モジュールをダウンロードすることができます。Azure PowerShell モジュールを使用するようにコンピューターを構成する方法の手順を示す、[Azure PowerShell をインストールして構成する方法](../powershell-install-configure.md)の手順に従ってください。 
2. 構成を開始する前に、必ず、[前提条件](expressroute-prerequisites.md)ページ、[ルーティングの要件](expressroute-routing.md)ページ、および[ワークフロー](expressroute-workflows.md) ページを確認してください。
3. アクティブな ExpressRoute 回線が必要です。 
	- 手順に従って、[ExpressRoute 回線を作成](expressroute-howto-circuit-classic.md)し、接続プロバイダー経由で回線を有効にしてください。 
	- 回線用に Azure プライベート ピアリングが構成されていることを確認してください。ルーティング手順については、[ルーティングの構成](expressroute-howto-routing-classic.md)に関する記事を参照してください。 
	- Azure プライベート ピアリングを構成する必要があります。また、ネットワークと Microsoft 間の BGP ピアリングを起動して、エンド ツー エンド接続を有効にする必要があります。

最大 10 個の VNet を ExpressRoute 回線にリンクできます。ExpressRoute 回線はすべて同じ地理的リージョンにある必要があります。ExpressRoute Premium アドオンを有効にした場合は、ExpressRoute 回線に多数の仮想ネットワークをリンクできます。Premium アドオンの詳細については、[FAQ](expressroute-faqs.md) を確認してください。

## 同じサブスクリプション内の VNet を回線に接続する

次のコマンドレットを使用して、ExpressRoute 回線に仮想ネットワークをリンクすることができます。コマンドレットを実行する前に、仮想ネットワーク ゲートウェイが作成されており、リンクの準備ができていることを確認してください。

	New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
	Provisioned

## 異なるサブスクリプション内の VNet を回線に接続する

複数のサブスクリプションで ExpressRoute 回線を共有することができます。下図に、複数のサブスクリプションで ExpressRoute 回線を共有するしくみについて概略を示します。

大規模クラウド内のそれぞれの小規模クラウドは、組織内のさまざまな部門に属するサブスクリプションを表すために使用されています。組織内の各部門はサービスのデプロイ用に固有のサブスクリプションを使用できますが、1 つの ExpressRoute 回線を共有することで、オンプレミス ネットワークに接続し直すことができます。1 つの部門 (この例では IT) で ExpressRoute 回線を所有できます。組織内の他のサブスクリプションも ExpressRoute 回線を使用できます。

>[AZURE.NOTE] 専用回線の接続と帯域幅の料金は、ExpressRoute 回線の所有者が負担することになります。すべての仮想ネットワークが同じ帯域幅を共有します。

![サブスクリプション間接続](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### 管理

*回線所有者*とは、ExpressRoute 回線が作成されたサブスクリプションの管理者または共同管理者です。回線所有者は、他のサブスクリプションの管理者または共同管理者 (ワークフロー図では*回線ユーザー*と呼ぶ) が所有する専用回線を使用することを承認できます。組織の ExpressRoute 回線の使用を承認される回線ユーザーは、いったん承認されると、サブスクリプションの VNet を ExpressRoute 回線にリンクできるようになります。

回線所有者は、承認をいつでも変更し、取り消す権限を持っています。承認を取り消すと、アクセスが取り消されたサブスクリプションからすべてのリンクが削除されます。

### 回線所有者の操作 

#### 承認の作成
	
回線所有者は、その他のサブスクリプションの管理者が指定された回線を使用することを承認します。次の例では、回線 (Contoso IT) の管理者は、Microsoft ID を指定して、別のサブスクリプション (Dev-Test) の管理者が最大 2 個の VNET を回線にリンクできるようにします。コマンドレットは、指定された Microsoft ID に電子メールを送信しません。回線所有者が、承認が完了したことをその他のサブスクリプション所有者に明示的に通知する必要があります。

	New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
		
	Description         : Dev-Test Links 
	Limit               : 2 
	LinkAuthorizationId : ********************************** 
	MicrosoftIds        : devtest@contoso.com 
	Used                : 0

#### 承認の確認

回線所有者は、次のコマンドレットを実行して、特定の回線で発行されるすべての承認を確認できます。

	Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
	
	Description         : EngineeringTeam 
	Limit               : 3 
	LinkAuthorizationId : #################################### 
	MicrosoftIds        : engadmin@contoso.com 
	Used                : 1 
	
	Description         : MarketingTeam 
	Limit               : 1 
	LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ 
	MicrosoftIds        : marketingadmin@contoso.com 
	Used                : 0 
	
	Description         : Dev-Test Links 
	Limit               : 2 
	LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
	MicrosoftIds        : salesadmin@contoso.com 
	Used                : 2 
	

#### 承認の更新

回線所有者は、次のコマンドレットを使用して承認を変更できます。

	Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
		
	Description         : Dev-Test Links 
	Limit               : 5 
	LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
	MicrosoftIds        : devtest@contoso.com 
	Used                : 0


#### 承認の削除

回線所有者は、次のコマンドレットを実行して、ユーザーに対する承認を取り消したり削除したりすることができます。

	Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### 回線ユーザーの操作

#### 承認の確認

回線ユーザーは、次のコマンドレットを使用して承認を確認できます。

	Get-AzureAuthorizedDedicatedCircuit
		
	Bandwidth                        : 200
	CircuitName                      : ContosoIT
	Location                         : Washington DC
	MaximumAllowedLinks              : 2
	ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
	ServiceProviderName              : equinix
	ServiceProviderProvisioningState : Provisioned
	Status                           : Enabled
	UsedLinks                        : 0

#### リンク承認の適用

回線ユーザーは以下のコマンドレットを実行して、リンク承認を適用することができます。

	New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1' 
		
	State VnetName 
	----- -------- 
	Provisioned SalesVNET1

## 次のステップ

ExpressRoute の詳細については、「[ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。

<!---HONumber=AcomDC_0420_2016-->