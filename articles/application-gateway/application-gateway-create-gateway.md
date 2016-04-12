<properties
   pageTitle="Application Gateway の作成、起動、または削除 | Microsoft Azure"
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
   ms.date="04/05/2016"
   ms.author="joaoma"/>

# Application Gateway の作成、起動、または削除

Azure Application Gateway はレイヤー 7 のロード バランサーです。クラウドでもオンプレミスでも、異なるサーバー間のフェールオーバーと HTTP 要求のパフォーマンス ルーティングを提供します。Application Gateway は、HTTP 負荷分散、Cookie ベースのセッション アフィニティ、Secure Sockets Layer (SSL) オフロードなどのアプリケーション配信機能を備えています。

> [AZURE.SELECTOR]
- [Azure Classic PowerShell (Azure クラシック PowerShell)](application-gateway-create-gateway.md)
- [Azure Resource Manager の PowerShell](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager テンプレート](application-gateway-create-gateway-arm-template.md)


<BR>

この記事では、Application Gateway を作成、構成、起動、および削除する手順について説明します。


## 開始する前に

1. Web Platform Installer を使用して、Azure PowerShell コマンドレットの最新バージョンをインストールします。[ダウンロード ページ](https://azure.microsoft.com/downloads/)の **Windows PowerShell** セクションから最新バージョンをダウンロードしてインストールできます。
2. 有効なサブネットがある作業用の仮想ネットワークがあることを確認します。仮想マシンまたはクラウドのデプロイメントでサブネットを使用していないことを確認します。Application Gateway そのものが、仮想ネットワーク サブネットに含まれている必要があります。
3. Application Gateway を使用するように構成するサーバーが存在している必要があります。つまり、仮想ネットワーク内、または割り当てられたパブリック IP/VIP を使用してエンドポイントが作成されている必要があります。

## Application Gateway の作成に必要な構成


**New-AzureApplicationGateway** コマンドを使用して Application Gateway を作成した時点では、構成は設定されていません。新しく作成したリソースは、XML または構成オブジェクトを使用して構成する必要があります。


値は次のとおりです。

- **バックエンド サーバー プール:** バックエンド サーバーの IP アドレスの一覧。一覧の IP アドレスは、仮想ネットワークのサブネットに属しているか、パブリック IP/VIP である必要があります。
- **バックエンド サーバー プールの設定:** すべてのプールには、ポート、プロトコル、Cookie ベースのアフィニティなどの設定があります。これらの設定はプールに関連付けられ、プール内のすべてのサーバーに適用されます。
- **フロントエンド ポート:** このポートは、Application Gateway で開かれたパブリック ポートです。このポートにトラフィックがヒットすると、バックエンド サーバーのいずれかにリダイレクトされます。
- **リスナー:** リスナーには、フロントエンド ポート、プロトコル (Http または Https、大文字小文字の区別あり)、および SSL 証明書名 (オフロードの SSL を構成する場合) があります。
- **ルール:** ルールはリスナーとバックエンド サーバー プールを結び付け、トラフィックが特定のリスナーにヒットした際に送られるバックエンド サーバー プールを定義します。


## 新しいアプリケーション ゲートウェイの作成

Application Gateway を作成するには:

1. Application Gateway のリソースを作成します。
2. 構成 XML ファイルまたは構成オブジェクトを作成します。
3. 新しく作成した Application Gateway のリソースに構成をコミットします。

>[AZURE.NOTE] Application Gateway のカスタム プローブを構成する必要がある場合は、「[Azure Resource Manager で PowerShell を使用して Azure Application Gateway のカスタム プローブを作成する](application-gateway-create-probe-classic-ps.md)」を参照してください。詳細については、「[Application Gateway による正常性監視の概要](application-gateway-probe-overview.md)」を参照してください。


### Application Gateway リソースの作成

ゲートウェイを作成するには、**New-AzureApplicationGateway** コマンドレットを独自の値に置き換えて使用します。この時点ではゲートウェイの課金は開始されません。課金は後の手順でゲートウェイが正しく起動されたときに開始します。

次の例では、"testvnet1" という仮想ネットワークと "subnet-1” というサブネットを使用して新しい Application Gateway を作成します。


	New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Description* 、 *InstanceCount* 、および *GatewaySize* は省略可能なパラメーターです。


ゲートウェイが作成されたことを確認するには、**Get-AzureApplicationGateway** コマンドレットを使用します。




	Get-AzureApplicationGateway AppGwTest
	Name          : AppGwTest
	Description   :
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Stopped
	VirtualIPs    : {}
	DnsName       :

>[AZURE.NOTE]  *InstanceCount* の既定値は 2、最大値は 10 です。*GatewaySize* の既定値は Medium です。Small、Medium、Large から選択します。


 ゲートウェイがまだ起動していないため、*VirtualIPs* と *DnsName* は空白のまま表示されます。これらの値は、ゲートウェイが実行中の状態になったときに作成されます。

## Application Gateway の構成

Application Gateway は、XML または構成オブジェクトを使用して構成できます。

## XML を使用して Application Gateway を構成する

次の例では、XML ファイルを使用して、すべての Application Gateway 設定を構成し、Application Gateway のリソースにコミットします。

### 手順 1.  

次のテキストをメモ帳にコピーします。

	<?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>(name-of-your-frontend-port)</Name>
	            <Port>(port number)</Port>
	        </FrontendPort>
	    </FrontendPorts>
	    <BackendAddressPools>
	        <BackendAddressPool>
	            <Name>(name-of-your-backend-pool)</Name>
	            <IPAddresses>
	                <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
	                <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
	            </IPAddresses>
	        </BackendAddressPool>
	    </BackendAddressPools>
	    <BackendHttpSettingsList>
	        <BackendHttpSettings>
	            <Name>(backend-setting-name-to-configure-rule)</Name>
	            <Port>80</Port>
	            <Protocol>[Http|Https]</Protocol>
	            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
	        </BackendHttpSettings>
	    </BackendHttpSettingsList>
	    <HttpListeners>
	        <HttpListener>
	            <Name>(name-of-the-listener)</Name>
	            <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
	            <Protocol>[Http|Https]</Protocol>
	        </HttpListener>
	    </HttpListeners>
	    <HttpLoadBalancingRules>
	        <HttpLoadBalancingRule>
	            <Name>(name-of-load-balancing-rule)</Name>
	            <Type>basic</Type>
	            <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
	            <Listener>(name-of-the-listener)</Listener>
	            <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
	        </HttpLoadBalancingRule>
	    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>

構成項目のかっこに囲まれた値を編集します。拡張子 .xml のファイルに保存します。

>[AZURE.IMPORTANT] プロトコル項目 Http または Https は、大文字小文字を区別します。

次の例では、パブリック ポート 80 で HTTP トラフィックを負荷分散する Application Gateway を設定し、2 つの IP アドレスのバックエンド ポート 80 にネットワーク トラフィックを送信する構成ファイルを使用する方法を示します。

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


### 手順 2.

次に、Application Gateway を設定します。**Set-AzureApplicationGatewayConfig** コマンドレットと構成 XML ファイルを使用します。


	Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"

	VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig
	VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## 構成オブジェクトを使用して Application Gateway を構成する

次の例では、構成オブジェクトを使用して Application Gateway を構成する方法を示します。すべての構成項目は個別に構成した後に、Application Gateway の構成オブジェクトに追加する必要があります。構成オブジェクトを作成したら、**Set-AzureApplicationGateway** コマンドを使用して、前の手順で作成した Application Gateway のリソースに構成をコミットします。

>[AZURE.NOTE] 各構成オブジェクトに値を割り当てる前に、PowerShell でストレージとして使用するオブジェクトの種類を宣言する必要があります。個別の項目を作成する最初の行では、Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model (オブジェクト名) を使用する項目を定義します。

### 手順 1.

すべての個別の構成項目を作成します。

次の例で示すように、フロントエンド IP を作成します。

	PS C:\> $fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
	PS C:\> $fip.Name = "fip1"
	PS C:\> $fip.Type = "Private"
	PS C:\> $fip.StaticIPAddress = "10.0.0.5"

次の例で示すように、フロントエンド ポートを作成します。

	PS C:\> $fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
	PS C:\> $fep.Name = "fep1"
	PS C:\> $fep.Port = 80

バックエンド サーバー プールを作成します。

 次の例で示すように、バックエンド サーバー プールに追加する IP アドレスを定義します。


	PS C:\> $servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
	PS C:\> $servers.Add("10.0.0.1")
	PS C:\> $servers.Add("10.0.0.2")

 $server オブジェクトを使用して、バックエンド プール オブジェクト ($pool) に値を追加します。

	PS C:\> $pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
	PS C:\> $pool.BackendServers = $servers
	PS C:\> $pool.Name = "pool1"

バックエンド サーバー プール設定を作成します。

	PS C:\> $setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
	PS C:\> $setting.Name = "setting1"
	PS C:\> $setting.CookieBasedAffinity = "enabled"
	PS C:\> $setting.Port = 80
	PS C:\> $setting.Protocol = "http"

リスナーを作成します。

	PS C:\> $listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
	PS C:\> $listener.Name = "listener1"
	PS C:\> $listener.FrontendPort = "fep1"
	PS C:\> $listener.FrontendIP = "fip1"
	PS C:\> $listener.Protocol = "http"
	PS C:\> $listener.SslCert = ""

ルールを作成します。

	PS C:\> $rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
	PS C:\> $rule.Name = "rule1"
	PS C:\> $rule.Type = "basic"
	PS C:\> $rule.BackendHttpSettings = "setting1"
	PS C:\> $rule.Listener = "listener1"
	PS C:\> $rule.BackendAddressPool = "pool1"

### 手順 2.

Application Gateway の構成オブジェクト ($appgwconfig) にすべての個別の構成項目を割り当てます。

フロントエンド IP を構成に追加します。

	PS C:\> $appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
	PS C:\> $appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
	PS C:\> $appgwconfig.FrontendIPConfigurations.Add($fip)

フロントエンド ポートを構成に追加します。

	PS C:\> $appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
	PS C:\> $appgwconfig.FrontendPorts.Add($fep)

バックエンド サーバー プールを構成に追加します。

	PS C:\> $appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
	PS C:\> $appgwconfig.BackendAddressPools.Add($pool)  

バックエンド プールの設定を構成に追加します。

	PS C:\> $appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
	PS C:\> $appgwconfig.BackendHttpSettingsList.Add($setting)

リスナーを構成に追加します。

	PS C:\> $appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
	PS C:\> $appgwconfig.HttpListeners.Add($listener)

ルールを構成に追加します。

	PS C:\> $appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
	PS C:\> $appgwconfig.HttpLoadBalancingRules.Add($rule)

### 手順 3.

**Set-AzureApplicationGatewayConfig** を使用して、構成オブジェクトを Application Gateway リソースにコミットします。

	Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig

## ゲートウェイの起動

ゲートウェイを構成したら、**Start-AzureApplicationGateway** コマンドレットを使用してゲートウェイを起動します。Application Gateway の課金は、ゲートウェイが正常に起動された後に開始します。


> [AZURE.NOTE] **Start-AzureApplicationGateway** コマンドレットは、終了までに最大で 15 ～ 20 分かかる場合があります。



	Start-AzureApplicationGateway AppGwTest

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## ゲートウェイの状態の確認

**Get-AzureApplicationGateway** コマンドレットを使用して、ゲートウェイの状態を確認します。前の手順で **Start-AzureApplicationGateway** が成功した場合、*State* は Running になり、*Vip* と *DnsName* に有効な値が入ります。

次の例では、起動に成功し、実行中で、`http://<generated-dns-name>.cloudapp.net` 方向のトラフィックを受け入れる準備が完了している Application Gateway を示します。

	Get-AzureApplicationGateway AppGwTest

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


## Application Gateway の削除

Application Gateway を削除するには:

1. ゲートウェイを停止するには、**Stop-AzureApplicationGateway** コマンドレットを使用します。
2. ゲートウェイを削除するには、**Remove-AzureApplicationGateway** コマンドレットを使用します。
3. **Get-AzureApplicationGateway** コマンドレットを使用して、ゲートウェイが削除されたことを確認します。

次の例の最初の行は **Stop-AzureApplicationGateway** コマンドレットを示し、その後に出力が続きます。

	Stop-AzureApplicationGateway AppGwTest

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Application Gateway が停止状態になったら、**Remove-AzureApplicationGateway** コマンドレットを使用してサービスを削除します。


	Remove-AzureApplicationGateway AppGwTest

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

サービスが削除されたことを確認するには、**Get-AzureApplicationGateway** コマンドレットを使用します。この手順は必須ではありません。


	Get-AzureApplicationGateway AppGwTest

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
	.....

## 次のステップ

SSL オフロードを構成する場合は、「[クラシック デプロイ モデルを使用して SSL オフロード用にアプリケーション ゲートウェイを構成する](application-gateway-ssl.md)」を参照してください。

内部ロード バランサーと共に使用するように Application Gateway を構成する場合は、「[内部ロード バランサー (ILB) を使用したアプリケーション ゲートウェイの作成](application-gateway-ilb.md)」を参照してください。

負荷分散のオプション全般の詳細については、次を参照してください。

- [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure の Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0406_2016-->