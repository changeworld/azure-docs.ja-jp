<properties 
   pageTitle="クラシック デプロイメントを使用した SSL オフロード用の Application Gateway の構成 | Microsoft Azure"
   description="この記事では、Azure クラシックのデプロイメント モデルを使用して、SSL オフロード用のアプリケーション ゲートウェイを作成する方法について説明します。"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/28/2015"
   ms.author="joaoma"/>

# クラシック デプロイメント モデルを使用して SSL オフロード用の Application Gateway を構成する 

> [AZURE.SELECTOR]
-[Azure Classic Powershell](application-gateway-ssl.md)
-[Azure Resource Manager Powershell](application-gateway-ssl-arm.md)

ゲートウェイでの SSL セッションを停止するように Application Gateway を構成し、Web ファーム上で発生するコストのかかる SSL 暗号化解除タスクを回避することができます。また、SSL オフロードはフロントエンド サーバーのセットアップと Web アプリケーションの管理も簡素化します。

>[AZURE.IMPORTANT]Azure リソースを使用する前に、Azure は現在、リソース マネージャーのデプロイ モデルとクラシック デプロイ モデルの 2 種類を備えていることを理解しておくことが重要です。Azure リソースを使用する前に、必ず[デプロイ モデルとツール](azure-classic-rm.md)について理解しておいてください。この記事の上部にあるタブをクリックすると、さまざまなツールについてのドキュメントを参照できます。このドキュメントでは、Azure クラシックのデプロイメント モデルを使用した Application Gateway の作成について説明します。Azure リソース マネージャー バージョンを使用するには、「[Azure リソース マネージャーを使用して Application Gateway SSL オフロードを構成する](application-gateway-ssl-arm.md)」に進んでください。


## 開始する前に

1. Web Platform Installer を使用して、Azure PowerShell コマンドレットの最新バージョンをインストールします。最新バージョンは、[ダウンロード ページ](http://azure.microsoft.com/downloads/)の **Windows PowerShell** のセクションからダウンロードしてインストールできます。
2. 有効なサブネットがある作業用の仮想ネットワークがあることを確認します。
3. 仮想ネットワーク内、またはパブリック IP/VIP が割り当てられたバックエンド サーバーがあることを確認します。

アプリケーション ゲートウェイで SSL オフロードを構成するには、次の手順を順番に実行します。

1. [新しいアプリケーション ゲートウェイの作成](#create-a-new-application-gateway)
2. [SSL 証明書のアップロード](#upload-ssl-certificates) 
3. [ゲートウェイの構成](#configure-the-gateway)
4. [ゲートウェイ構成の設定](#set-the-gateway-configuration)
5. [ゲートウェイの起動](#start-the-gateway)
6. [ゲートウェイの状態の確認](#verify-the-gateway-status)


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

このサンプルの最初の行はコマンドレットを示し、その後に出力が続きます。

	PS C:\> Get-AzureApplicationGateway AppGwTest

	VERBOSE: 4:39:39 PM - Begin Operation:
	Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
	Operation: Get-AzureApplicationGateway
	Name: AppGwTest	
	Description: 
	VnetName: testvnet1 
	Subnets: {Subnet-1} 
	InstanceCount: 2 
	GatewaySize: Medium 
	State: Stopped 
	VirtualIPs: 
	DnsName:


## SSL 証明書のアップロード 

`Add-AzureApplicationGatewaySslCertificate` を使用して、サーバー証明書を *pfx* 形式でアプリケーション ゲートウェイにアップロードします。証明書の名前はユーザーが指定し、アプリケーション ゲートウェイ内で一意である必要があります。この証明書はアプリケーション ゲートウェイ上のすべての証明書管理操作でこの名前で呼ばれます。

このサンプルの最初の行はコマンドレットを示し、その後に出力が続きます。サンプル内の値を独自の値に置き換えます。

	PS C:\> Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file> 
	
	VERBOSE: 5:05:23 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate 
	VERBOSE: 5:06:29 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   21fdc5a0-3bf7-2c12-ad98-192e0dd078ef

次に、証明書のアップロードを検証します。`Get-AzureApplicationGatewayCertificate` を使用します。

このサンプルの最初の行はコマンドレットを示し、その後に出力が続きます。

	PS C:\> Get-AzureApplicationGatewaySslCertificate AppGwTest 

	VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate 
	VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
	Name           : SslCert 
	SubjectName    : CN=gwcert.app.test.contoso.com 
	Thumbprint     : AF5ADD77E160A01A6......EE48D1A 
	ThumbprintAlgo : sha1RSA
	State..........: Provisioned


## ゲートウェイの構成

アプリケーション ゲートウェイの構成は、複数の値で構成されます。値を相互に関連付けて構成を作成します。

値は次のとおりです。
 
- **バックエンド サーバー プール:** バックエンド サーバーの IP アドレスの一覧。一覧の IP アドレスは、VNet サブネットに属しているか、パブリック IP/VIP である必要があります。 
- **バックエンド サーバー プールの設定:** すべてのプールには、ポート、プロトコル、cookie ベースのアフィニティなどの設定があります。これらの設定はプールに関連付けられ、プール内のすべてのサーバーに適用されます。
- **フロントエンド ポート:** このポートは、アプリケーション ゲートウェイで開かれたパブリック ポートです。このポートにトラフィックがヒットすると、バックエンド サーバーのいずれかにリダイレクトされます。
- **リスナー:** リスナーには、フロントエンド ポート、プロトコル (Http または Https、大文字小文字の区別あり)、および SSL 証明書名 (オフロードの SSL を構成する場合) があります。 
- **ルール:** ルールはリスナーとバックエンド サーバー プールを結び付け、トラフィックが特定のリスナーにヒットした際に送られるバックエンド サーバー プールを定義します。現在、*basic* ルールのみサポートされます。*basic* ルールは、ラウンド ロビンの負荷分散です。

**構成に関する追加の注意:**

SSL 証明書の構成では、**HttpListener** のプロトコルを *Https* (大文字小文字の区別あり) に変更する必要があります。**SslCert** 要素は、前述の SSL 証明書のアップロードで使用したものと同じ名前を値に設定して **HttpListener** に追加する必要があります。フロント エンド ポートは 443 に更新する必要があります。

**cookie ベースのアフィニティを有効にするには**: クライアント セッションからの要求が常に Web ファーム内の同じ VM に送られるようにアプリケーション ゲートウェイを構成できます。これはセッション cookie の挿入によって行われ、ゲートウェイがトラフィックを適切に送信できるようにします。cookie ベースのアフィニティを有効にするには、**BackendHttpSettings** 要素で **CookieBasedAffinity** を *Enabled* に設定します。



構成は、構成オブジェクトを作成するか、構成 XML ファイルを使用して構築できます。構成 XML ファイルを使用して構成を構築するには、次のサンプルを使用します。

**構成 XML のサンプル**


	    <?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendIPConfigurations />
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>FrontendPort1</Name>
	            <Port>443</Port>
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
	            <Protocol>Https</Protocol>
	            <SslCert>GWCert</SslCert>
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

次に、アプリケーション ゲートウェイを設定します。構成オブジェクトまたは構成 XML ファイルのいずれの場合でも、`Set-AzureApplicationGatewayConfig` コマンドレットを使用できます。


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

このサンプルは、起動に成功し、実行中で、トラフィックを受け取る準備が完了しているアプリケーション ゲートウェイを示します。

	PS C:\> Get-AzureApplicationGateway AppGwTest 

	Name          : AppGwTest2
	Description   : 
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Running
	VirtualIPs    : {23.96.22.241}
	DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net


## 次のステップ


負荷分散のオプション全般の詳細については、次を参照してください。

- [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure の Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=Nov15_HO3-->