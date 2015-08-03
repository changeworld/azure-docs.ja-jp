<properties 
   pageTitle="ExpressRoute 帯域幅の動的アップグレード | Microsoft Azure"
   description="ダウンタイムなしで ExpressRoute 回線の帯域幅のサイズを動的に増やす方法。"
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
   ms.date="06/03/2015"
   ms.author="cherylmc" />

# ダウンタイムなしでの ExpressRoute 回線帯域幅の動的アップグレード

ダウンタイムを発生させずに ExpressRoute 回線のサイズを大きくすることができます。次の手順は、PowerShell を使用して ExpressRoute 回線の帯域幅を更新するのに役立ちます。制限および制約事項の詳細は、「[ExpressRoute の FAQ](expressroute-faqs.md)」ページを参照してください。

##  構成の前提条件

構成を開始する前に、次の前提条件があることを確認してください。

- Azure サブスクリプション
- Azure PowerShell の最新バージョン
- 構成済みで動作中のアクティブな ExpressRoute 回線


##  PowerShell を使用して設定を構成する

Windows PowerShell は、Azure でのワークロードのデプロイメントと管理を制御し、自動化するために使用できる、強力なスクリプティング環境です。詳細については、[MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx) にある PowerShell のドキュメントを参照してください。

1. **ExpressRoute 用の PowerShell モジュールをインポートします。**

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **ExpressRoute 回線の詳細を取得する**

	次の PowerShell コマンドレットを使用して、ExpressRoute 回線の詳細を取得できます。
		

    	PS C:\> Get-AzureDedicatedCircuit
	
	このコマンドは、サブスクリプションで作成したすべての回線の一覧を返します。サービス キーがある場合は、特定の ExpressRoute 回線の詳細を取得するために次のコマンドを使用できます。

		 PS C:\> Get-AzureDedicatedCircuit -ServiceKey <skey>

	<skey> を実際のサービス キーで置き換えます。
	
		PS C:\> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled


3. **Microsoft 側で ExpressRoute 回線の帯域幅を増やす**
	
	プロバイダーでサポートされている帯域幅のオプションについては、「[ExpressRoute の FAQ](expressroute-faqs.md)」ページを確認してください。既存の回線のサイズを超えるサイズを選択することができます。必要なサイズを決定した後、次のコマンドを使用して、回線のサイズを変更することができます。

		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

	回線のサイズは、Microsoft 側で増やされます。更新された帯域幅のオプションの課金は、この時点から開始されます。

4. **サーバー プロバイダー側で ExpressRoute 回線の帯域幅を増やす**

	接続プロバイダー (NSP / EXP) に連絡し、更新された帯域幅の情報を提供してください。タスクを完了するためにサービス プロバイダーで規定されている注文更新プロセスに従います。

 

<!---HONumber=July15_HO4-->