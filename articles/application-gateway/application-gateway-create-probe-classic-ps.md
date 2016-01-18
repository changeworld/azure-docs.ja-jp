<properties 
   pageTitle="クラシック デプロイ モデルで PowerShell を使用して Application Gateway のカスタム プローブを作成する | Microsoft Azure"
   description="クラシック デプロイ モデルで PowerShell を使用して、Application Gateway のカスタム プローブを作成する方法の説明"
   services="application-gateway"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="joaoma" />

# PowerShell を使用して Application Gateway (クラシック) のカスタム プローブを作成する


[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)].

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](application-gateway-create-probe-ps.md).


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


## 新しいアプリケーション ゲートウェイの作成 

Application Gateway を作成するには、次の手順を順番に実行する必要があります。

1. Application Gateway のリソースを作成します。
2. 構成 XML ファイルまたは構成オブジェクトを作成します。
3. 新しく作成した Application Gateway のリソースに構成をコミットします。

### アプリケーション ゲートウェイのリソースの作成

ゲートウェイを作成するには、`New-AzureApplicationGateway` コマンドレットを使用して、値を独自の値に置き換えて使用します。この時点ではゲートウェイの課金は開始されません。課金は後の手順でゲートウェイが正しく起動されたときに開始します。

次の例では、"testvnet1" という仮想ネットワークと "subnet-1” というサブネットを使用して新しい Application Gateway を作成します。


	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Description*、*InstanceCount*、および *GatewaySize* は省略可能なパラメーターです。


ゲートウェイが作成されたことを**確認する**には、`Get-AzureApplicationGateway` コマンドレットを使用します。


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

>[AZURE.NOTE]*InstanceCount* の既定値は 2、最大値は 10 です。*GatewaySize* の既定値は Medium です。Small、Medium、Large から選択します。


 ゲートウェイがまだ起動していないため、*Vip* と *DnsName* は空白のまま表示されます。これらの値は、ゲートウェイが実行中の状態になったときに作成されます。

## アプリケーション ゲートウェイの構成

Application Gateway は、XML または構成オブジェクトを使用して構成できます。

## XML を使用してアプリケーション ゲートウェイを構成する

次の例では、XML ファイルを使用して、すべての Application Gateway 設定を構成し、Application Gateway のリソースにコミットします。

### 手順 1.  

次のテキストをメモ帳にコピーします。


	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name>
            <Type>Private</Type>
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>    
	<FrontendPorts>
        <FrontendPort>
            <Name>port1</Name>
            <Port>80</Port>
        </FrontendPort>
    </FrontendPorts>
    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>pool1</Name>
            <IPAddresses>
                <IPAddress>1.1.1.1</IPAddress>
				<IPAddress>2.2.2.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>listener1</Name>
            <FrontendIP>fip1</FrontendIP>
	    <FrontendPort>port1</FrontendPort>
            <Protocol>Http</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>lbrule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>setting1</BackendHttpSettings>
            <Listener>listener1</Listener>
            <BackendAddressPool>pool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>


構成項目のかっこに囲まれた値を編集します。拡張子 .xml のファイルに保存します。

次の例では、パブリック ポート 80 で Http トラフィックを負荷分散するアプリケーション ゲートウェイを設定し、カスタム プローブを使用して 2 つの IP アドレスのバックエンド ポート 80 にネットワーク トラフィックを送信する構成ファイルを使用する方法を示します。

>[AZURE.IMPORTANT]プロトコル項目 Http または Https は、大文字小文字を区別します。


新しい構成項目<Probe>追加して、カスタム プローブを構成します。

構成パラメーターは次のとおりです。

- **Name** - カスタム プローブの参照名
- **Protocol** - 使用されるプロトコル (有効な値は Http または Https です)
- **Host** と **Path** - インスタンスの状態を判断するためにアプリケーション ゲートウェイにより呼び出される完全な URL パス。たとえば、"http://contoso.com/" という Web サイトがある場合、HTTP 応答が正常かどうかをプローブでチェックするためには、カスタム プローブを "http://contoso.com/path/custompath.htm" に対して構成します。 
- **Interval** - プローブのチェック間隔を秒単位で指定します。 
- **Timeout** - プローブの HTTP 応答チェックのタイムアウト期間を定義します。
- **UnhealthyThreshold** - バック エンド インスタンスへの*異常*フラグの設定に必要な HTTP 応答の失敗数です。

プローブの名前は、<BackendHttpSettings>どのバック エンド プールがカスタム プローブ設定を使用するかを割り当てる構成で参照されます。

## 既存のアプリケーション ゲートウェイへのカスタム プローブ構成の追加

アプリケーション ゲートウェイの現在の構成を変更するには、現在の XML 構成ファイルの取得、カスタム プローブを追加するための変更、新しい XML 設定を使用したアプリケーション ゲートウェイの構成という 3 つの手順を行う必要があります。

### 手順 1

get-AzureApplicationGatewayConfig を使用して XML ファイルを取得します。この操作により、プローブ設定の追加をするために変更する XML 構成ファイルがエクスポートされます。
	
	get-AzureApplicationGatewayConfig -Name <application gateway name> -Exporttofile "<path to file>"


### 手順 2. 

テキスト エディターで XML ファイルを開きます。`<frontendport>` セクションの後に `<probe>` セクションを追加します。
	
	<Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>

XML の [BackendHttpSettings] セクションで、次の例に示すようにプローブを追加します。
    
        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>

XML ファイルを保存します。


### 手順 3. 

`Set-AzureApplicationGatewayConfig` を使用して、新しい XML ファイルでアプリケーション ゲートウェイの構成を更新します。これにより、新しい構成でアプリケーション ゲートウェイが更新されます。

	set-AzureApplicationGatewayConfig -Name <application gateway name> -Configfile "<path to file>"


## 次のステップ

SSL オフロードを構成する場合は、「[SSL オフロードの Application Gateway の構成](application-gateway-ssl.md)」を参照してください。

ILB とともに使用するように Application Gateway を構成する場合は、「[内部ロード バランサー (ILB) を使用した Application Gateway の作成](application-gateway-ilb.md)」を参照してください。

<!---HONumber=AcomDC_0107_2016-->