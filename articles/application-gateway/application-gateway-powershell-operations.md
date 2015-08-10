<properties 
   pageTitle="アプリケーション ゲートウェイの作成、起動、または削除 | Microsoft Azure"
   description="このページでは、Azure Application Gateway を作成、構成、起動、および削除する方法について説明します。"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/30/2015"
   ms.author="joaoma"/>

# アプリケーション ゲートウェイの作成、起動、または削除

このリリースでは、PowerShell または REST API の呼び出しを使用してアプリケーション ゲートウェイを作成できます。ポータルおよび CLI のサポートは、今後のリリースで提供されます。この記事では、アプリケーション ゲートウェイを作成、構成、起動、および削除する手順について説明します。

## 開始する前に

1. Web Platform Installer を使用して、Azure PowerShell コマンドレットの最新バージョンをインストールします。最新バージョンは、[ダウンロード ページ](http://azure.microsoft.com/downloads/)の **Windows PowerShell** のセクションからダウンロードしてインストールできます。
2. 有効なサブネットが存在する作業用の仮想ネットワークがあることを確認します。
3. 仮想ネットワーク内、またはパブリック IP/VIP が割り当てられたバックエンド サーバーがあることを確認します。


新しいアプリケーション ゲートウェイを作成するには、次の手順を順番に実行します。

1. [新しいアプリケーション ゲートウェイの作成](#create-a-new-application-gateway)
2. [ゲートウェイの構成](#configure-the-gateway)
3. [ゲートウェイ構成の設定](#set-the-gateway-configuration)
4. [ゲートウェイの起動](#start-the-gateway)
4. [ゲートウェイの状態の確認](#verify-the-gateway-status)

アプリケーション ゲートウェイを削除するには、「[アプリケーション ゲートウェイの削除](#delete-an-application-gateway)」に移動します。

## 新しいアプリケーション ゲートウェイの作成

**ゲートウェイを作成する**には、`New-AzureApplicationGateway` コマンドレットを独自の値に置き換えて使用します。この時点ではゲートウェイの課金は開始されません。課金は後の手順でゲートウェイが正しく起動されたときに開始します。

このサンプルの最初の行はコマンドレットを示し、その後に出力が続きます。
    
	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

ゲートウェイが作成されたことを**確認する**には、`Get-AzureApplicationGateway` コマンドレットを使用します。

サンプルでは、*Description*、*InstanceCount*、および*GatewaySize* は省略可能なパラメーターです。*InstanceCount* の既定値は 2、最大値は 10 です。*GatewaySize* の既定値は Medium です。その他の値は Small および Large です。ゲートウェイがまだ起動していないため、*Vip* と *DnsName* は空白のまま表示されます。これらの値は、ゲートウェイが実行中の状態になったときに作成されます。




	PS C:\> Get-AzureApplicationGateway AppGwTest
	Name          : AppGwTest
	Description   : 
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Stopped
	VirtualIPs    : {}
	DnsName       :


## ゲートウェイの構成

アプリケーション ゲートウェイの構成は、複数の値で構成されます。値を相互に関連付けて構成を作成します。

値は次のとおりです。

- **バックエンド サーバー プール:** バックエンド サーバーの IP アドレスの一覧です。一覧の IP アドレスは、VNet サブネットに属しているか、パブリック IP/VIP である必要があります。 
- **バックエンド サーバー プールの設定:** すべてのプールには、ポート、プロトコル、cookie ベースのアフィニティなどの設定があります。これらの設定はプールに関連付けられ、プール内のすべてのサーバーに適用されます。
- **フロントエンド ポート:** このポートは、アプリケーション ゲートウェイに開かれたパブリック ポートです。このポートにトラフィックがヒットすると、バックエンド サーバーのいずれかにリダイレクトされます。
- **リスナー:** リスナーには、フロントエンド ポート、プロトコル (Http または Https、大文字小文字の区別あり)、および SSL 証明書名 (オフロードの SSL を構成する場合) があります。 
- **ルール:** ルールはリスナーとバックエンド サーバー プールを結び付け、トラフィックが特定のリスナーにヒットした際に送られるバックエンド サーバー プールを定義します。現在、*basic* ルールのみサポートされます。*basic* ルールは、ラウンド ロビンの負荷分散です。

構成は、構成オブジェクトを作成するか、構成 XML ファイルを使用して構築できます。構成 XML ファイルを使用して構成を構築するには、次のサンプルを使用します。

 **構成 XML のサンプル**

	<?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>FrontendPort1</Name>
	            <Port>80</Port>
	        </FrontendPort>
	    </FrontendPorts>
	    <BackendAddressPools>
	        <BackendAddressPool>
	            <Name>BackendPool1</Name>
	            <IPAddresses>
	                <IPAddress>10.0.0.1</IPAddress>
	                <IPAddress>10.0.0.2</IPAddress>
	            </IPAddresses>
	        </BackendAddressPool>
	    </BackendAddressPools>
	    <BackendHttpSettingsList>
	        <BackendHttpSettings>
	            <Name>BackendSetting1</Name>
	            <Port>80</Port>
	            <Protocol>Http</Protocol>
	            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
	        </BackendHttpSettings>
	    </BackendHttpSettingsList>
	    <HttpListeners>
	        <HttpListener>
	            <Name>HTTPListener1</Name>
	            <FrontendPort>FrontendPort1</FrontendPort>
	            <Protocol>Http</Protocol>
	        </HttpListener>
	    </HttpListeners>
	    <HttpLoadBalancingRules>
	        <HttpLoadBalancingRule>
	            <Name>HttpLBRule1</Name>
	            <Type>basic</Type>
	            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
	            <Listener>HTTPListener1</Listener>
	            <BackendAddressPool>BackendPool1</BackendAddressPool>
	        </HttpLoadBalancingRule>
	    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>

## ゲートウェイ構成の設定

次に、アプリケーション ゲートウェイを設定します。構成オブジェクトまたは構成 XML ファイルで `Set-AzureApplicationGatewayConfig` コマンドレットを使用できます。


	PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

	VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
	VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## ゲートウェイの起動

ゲートウェイを構成したら、`Start-AzureApplicationGateway` コマンドレットを使用してゲートウェイを起動します。アプリケーション ゲートウェイの課金は、ゲートウェイが正常に起動された後に開始します。


**注:** `Start-AzureApplicationGateway` コマンドレットの実行には最大で 15 ～ 20 分かかる場合があります。



	PS C:\> Start-AzureApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## ゲートウェイの状態の確認

`Get-AzureApplicationGateway` コマンドレットを使用してゲートウェイの状態を確認します。前の手順で *Start-AzureApplicationGateway* が成功した場合、State は *Running* になり、Vip と DnsName に有効な値が入力されます。

このサンプルは、起動に成功し、実行中で、`http://<generated-dns-name>.cloudapp.net` 宛のトラフィックを受け入れる準備が完了しているアプリケーション ゲートウェイを示します。

	PS C:\> Get-AzureApplicationGateway AppGwTest 

	VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
	VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
	Name          : AppGwTest 
	Description   : 
	VnetName      : testvnet1 
	Subnets       : {Subnet-1} 
	InstanceCount : 2 
	GatewaySize   : Medium 
	State         : Running 
	Vip           : 138.91.170.26 
	DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net


## アプリケーション ゲートウェイの削除

アプリケーション ゲートウェイを削除するには、次の手順を順番に実行する必要があります。

1. `Stop-AzureApplicationGateway` コマンドレットを使用してゲートウェイを停止します。 
2. `Remove-AzureApplicationGateway` コマンドレットを使用してゲートウェイを削除します。
3. `Get-AzureApplicationGateway` コマンドレットを使用して削除されたゲートウェイを確認します。

このサンプルの最初の行は `Stop-AzureApplicationGateway` コマンドレットを示し、その後に出力が続きます。

	PS C:\> Stop-AzureApplicationGateway AppGwTest 

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway 
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

アプリケーション ゲートウェイが Stopped 状態になったら、`Remove-AzureApplicationGateway` コマンドレットを使用してサービスを削除します。


	PS C:\> Remove-AzureApplicationGateway AppGwTest 

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway 
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

サービスが削除されていることを確認するには、`Get-AzureApplicationGateway` コマンドレットを使用します。この手順は必須ではありません。


	PS C:\> Get-AzureApplicationGateway AppGwTest 

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway 

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist. 
	.....

## 次のステップ

SSL オフロードを構成する場合は、「[SSL オフロードのアプリケーション ゲートウェイの構成](application-gateway-ssl.md)」を参照してください。

ILB とともに使用するようにアプリケーション ゲートウェイを構成する場合は、「[内部ロード バランサー (ILB) を使用したアプリケーション ゲートウェイの作成](application-gateway-ilb.md)」を参照してください。

負荷分散のオプション全般の詳細については、次を参照してください。

- [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure の Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=July15_HO5-->