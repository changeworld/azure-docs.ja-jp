<properties 
   pageTitle="複数のサブスクリプションの間で ExpressRoute 回線を共有する | Microsoft Azure"
   description="この記事では、複数の Azure サブスクリプション間で ExpressRoute 回線を共有する手順について説明します。"
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/25/2015"
   ms.author="cherylmc" />

# 複数のサブスクリプションで ExpressRoute 回線を共有する

複数のサブスクリプションで、1 つの ExpressRoute 回線を共有できます。**図 1** に、複数のサブスクリプションで ExpressRoute 回線を共有するしくみについて概略を示します。

大規模クラウド内のそれぞれの小規模クラウドは、組織内のさまざまな部門に属するサブスクリプションを表すために使用されています。組織内の各部門はサービスのデプロイ用に固有のサブスクリプションを使用できますが、1 つの ExpressRoute 専用回線を共有することで、企業ネットワークに接続し直すことができます。1 つの部門 (この例ではIT 部門) で ExpressRoute 専用回線を所有できます。専用回線の接続と帯域幅の料金は、専用回線の所有者が負担することになります。組織内の他のサブスクリプションも ExpressRoute 回線を使用できます。

**図 1**

![サブスクリプションの共有](./media/expressroute-share-circuit/IC766124.png)

## 管理

"回線所有者" とは、ExpressRoute 専用回線が作成されたサブスクリプションの管理者または共同管理者です。回線所有者は、他のサブスクリプションの管理者または共同管理者 (ワークフロー図では "回線ユーザー" と呼ぶ) が専用回線を使用するのを許可できます。組織の専用回線の使用を承認される回線ユーザーは、いったん承認されると、サブスクリプションの VNet を専用回線にリンクできるようになります。

回線所有者は、承認をいつでも変更し、取り消す権限を持っています。承認を取り消すと、アクセスが取り消されたサブスクリプションからすべてのリンクが削除されます。

## ワークフロー

1. 回線所有者は、その他のサブスクリプションの管理者が指定された回線を使用することを承認します。次の例では、回線 (Contoso IT) の管理者は、Microsoft (Live) ID を指定して、別のサブスクリプション (Contoso Sales) の管理者が最大 2 個の VNET を回線にリンクできるようにします。コマンドレットは、指定された Microsoft ID に電子メールを送信しません。回線所有者が、承認が完了したことをその他のサブスクリプション所有者に明示的に通知する必要があります。

		PS C:> New-AzureDedicatedCircuitLinkAuthorization -ServiceKey '6ed7e310-1a02-4261-915f-6ccfedc416f1' -Description 'SalesTeam' -Limit 2 -MicrosoftIds 'salesadmin@contoso.com'
		
		Description         : SalesTeam 
		Limit               : 2 
		LinkAuthorizationId : e2bc2645-6fd4-44a4-94f5-f2e43e6953ed 
		MicrosoftIds        : salesadmin@contoso.com 
		Used                : 0

1. 回線所有者が通知を受け取ったら、承認されたサブスクリプションの管理者は次のコマンドレットを実行して、回線のサービス キーを取得できます。この例では、Contoso Sales の管理者は、指定された Microsoft ID の salesadmin@contoso.com を使って最初にサインインする必要があります。

		PS C:> Get-AzureAuthorizedDedicatedCircuit
		
		Bandwidth                        : 100
		CircuitName                      : ContosoIT
		Location                         : Washington DC
		MaximumAllowedLinks              : 2
		ServiceKey                       : 6ed7e310-1a02-4261-915f-6ccfedc416f1
		ServiceProviderName              : ###########
		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled
		UsedLinks                        : 0

1. 承認されたサブスクリプションの管理者は、次のコマンドレットを実行してリンク操作を完了します。

		PS C:> New-AzureDedicatedCircuitLink –servicekey 6ed7e310-1a02-4261-915f-6ccfedc416f1 –VnetName 'SalesVNET1' 
		
			State VnetName 
			----- -------- 
			Provisioned SalesVNET1

以上です。Azure 上の Contoso の Sales VNet は、ContosoIT が作成/所有した回線にリンクされました。

## 承認の管理

回線所有者は、最大 10 個の Azure サブスクリプションで回線を共有できます。また、この回線に対して承認されたユーザーを表示できます。所有者はいつでも承認を取り消すことができます。回線所有者が LinkAuthorizationId で識別された承認を取り消すと、その承認で許可されているすべてのリンクが直ちに削除されます。リンクされた VNET では、ExpressRoute 回線を経由したオンプレミスのネットワークへの接続が失われます。

	PS C:> Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: 6ed7e310-1a02-4261-915f-6ccfedc416f1 
	
	Description         : EngineeringTeam 
	Limit               : 3 
	LinkAuthorizationId : cc958457-c8c1-4f16-af09-e7f099da64bf 
	MicrosoftIds        : engadmin@contoso.com 
	Used                : 1 
	
	Description         : MarketingTeam 
	Limit               : 1 
	LinkAuthorizationId : d972726f-c7b9-4658-8598-ad3208ac9348 
	MicrosoftIds        : marketingadmin@contoso.com 
	Used                : 0 
	
	Description         : SalesTeam 
	Limit               : 2 
	LinkAuthorizationId : e2bc2645-6fd4-44a4-94f5-f2e43e6953ed 
	MicrosoftIds        : salesadmin@contoso.com 
	Used                : 2 
	
	PS C:> Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey '6ed7e310-1a02-4261-915f-6ccfedc416f1' -AuthorizationId 'e2bc2645-6fd4-44a4-94f5-f2e43e6953ed'


次の図は、承認ワークフローを示しています。

![サブスクリプションの共有のワークフロー](./media/expressroute-share-circuit/IC759525.png)

## 次のステップ

ExpressRoute の詳細については、「[ExpressRoute の技術概要](expressroute-introduction.md)」を参照してください。

<!---HONumber=July15_HO2-->