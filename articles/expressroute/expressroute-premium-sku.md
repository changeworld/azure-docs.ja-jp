<properties 
   pageTitle="ExpressRoute Premium アドオンを有効または無効にする方法 | Microsoft Azure"
   description="ExpressRoute 回線の ExpressRoute Premium アドオンを有効または無効にする方法ExpressRoute Premium では、ExpressRoute 回線に最大 10,000 個の (パブリックおよびプライベートのピアリングの) ルートと最大 10 個の仮想ネットワークを追加できます。あるリージョンの仮想ネットワークを別のリージョンの ExpressRoute 回線にリンクすることもできます。"
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
   ms.date="06/02/2015"
   ms.author="cherylmc" />

# ExpressRoute 回線用の ExpressRoute Premium アドオンの構成

ExpressRoute Premium は、次に示す一連の機能を提供するサービスです。

 - パブリック ピアリングおよびプライベート ピアリング用ルートの上限が 4,000 から 10,000 に増加されたルーティング テーブル。
 - ExpressRoute 回線に接続できる数が増加 (既定は 10) した仮想ネットワーク (VNet)。 
 - Microsoft のコア ネットワーク経由のグローバル接続。ある地理的リージョンにある VNET を別のリージョン内の ExpressRoute 回線に接続できるようになります。**例:** 西ヨーロッパで作成された VNET をシリコン バレーで作成された ExpressRoute 回線に接続できます。

ExpressRoute Premium アドオンの詳細については、「[ExpressRoute FAQ](expressroute-faqs.md)」ページを参照してください。料金については、[料金の詳細](http://azure.microsoft.com/pricing/details/expressroute/)に関するページを参照してください。

ここでは、次の操作を行うときに役立つ手順について説明します。

- Premium アドオンを有効にして ExpressRoute 回線を作成する。
- Premium アドオンを有効にするために既存の ExpressRoute 回線を更新する。
- 回線の ExpressRoute Premium アドオンを無効にする。


## Premium アドオン機能を有効にして ExpressRoute 回線を作成するには

###  開始する前に

構成を開始する前に、次の前提条件があることを確認してください。

- Azure サブスクリプション
- Azure PowerShell の最新バージョン

###  1.ExpressRoute 用の PowerShell モジュールをインポートする

Windows PowerShell は、Azure でのワークロードのデプロイメントと管理を制御し、自動化するために使用できる、強力なスクリプティング環境です。詳細については、[MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx) にある PowerShell のドキュメントを参照してください。

次のコマンドレットを使用して、ExpressRoute 用の PowerShell モジュールをインポートします。


	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'


### 2.Premium アドオン機能を有効にして新しい ExpressRoute 回線を構成する

新しい ExpressRoute 回線を作成するときに Premium アドオンを有効にして作成することができます。[NSP](expressroute-configuring-nsps.md) または [EXP](expressroute-configuring-exps.md) で ExpressRoute 回線を作成する手順に従います。New-AzureDedicatedCircuit コマンドレットに、SKU を指定できる新しいオプション パラメーターが追加されました。SKU には「Standard」または「Premium」を指定できます。既定値は「standard」です。SKU に「Premium」を指定すると、回線の Premium アドオン機能が有効になります。


		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -Sku Premium


### 3.ExpressRoute Premium アドオンが有効になったことを確認する
回線の ExpressRoute Premium アドオンが有効になったかどうかを調べて確認することができます。次の例では、ExpressRoute 回線の ExpressRoute Premium アドオン機能は有効になっていません。アドオンが有効になっている場合は、SKU に「***Premium***」と表示されます。

		PS C:\> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled




## 既存の ExpressRoute 回線の ExpressRoute Premium アドオンを有効にするには
既に作成済みの ExpressRoute 回線の ExpressRoute Premium アドオン機能を有効にすることができます。


1. **ExpressRoute 回線の詳細を取得する**

	次の PowerShell コマンドレットを使用して、ExpressRoute 回線の詳細を取得できます。
		

    	PS C:\> Get-AzureDedicatedCircuit
	
	このコマンドは、サブスクリプションで作成したすべての回線の一覧を返します。サービス キーがある場合は、次のコマンドを使用して特定の ExpressRoute 回線の詳細を取得することができます

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


2. **回線の ExpressRoute Premium アドオンを有効にする**


	次の PowerShell コマンドレットを使用して、既存の回線の ExpressRoute Premium アドオンを有効にできます。
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled

	これで、回線の ExpressRoute Premium アドオン機能が有効になりました。このコマンドが正常に実行された時点で、Premium アドオン機能の課金が始まることに注意してください。


## ExpressRoute 回線の ExpressRoute Premium アドオンを無効にするには

Premium アドオンが有効になっている ExpressRoute 回線の ExpressRoute Premium アドオンを無効にすることができます。

**注**: その前に、Premium アドオン機能の一覧に表示されている機能の使用を確実に停止しておく必要があります。Microsoft は、回線にリンクされている VNet が 10 個より多い場合、Premium アドオンを無効にする要求を無視します。また、Premium アドオンを無効にした後に、5,000 より多いルートをアドバタイズした場合、Microsoft は BGP セッションをドロップします。

1. **ExpressRoute 回線の詳細を取得する**

	次の PowerShell コマンドレットを使用して、ExpressRoute 回線の詳細を取得できます。
		

    	PS C:\> Get-AzureDedicatedCircuit
	
	このコマンドは、サブスクリプションで作成したすべての回線の一覧を返します。サービス キーがある場合は、次のコマンドを使用して特定の ExpressRoute 回線の詳細を取得することができます

		 PS C:\> Get-AzureDedicatedCircuit -ServiceKey <skey>

	<skey> を実際のサービス キーで置き換えます。
	
		PS C:\> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled


3. **回線の ExpressRoute Premium アドオンを無効にする**


	次の PowerShell コマンドレットを使用して、既存の回線の ExpressRoute Premium アドオンを無効にできます。
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Standard

	これで、回線の ExpressRoute Premium アドオンは無効になります。


 

<!---HONumber=July15_HO4-->