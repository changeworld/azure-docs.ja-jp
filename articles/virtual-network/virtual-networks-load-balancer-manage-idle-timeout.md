<properties 
   authors="danielceckert" 
   documentationCenter="dev-center-name" 
   editor=""
   manager="jefco" 
   pageTitle="管理: ロード バランサーのアイドル タイムアウト" 
   description="Azure ロード バランサーのアイドル タイムアウトの管理機能" 
   services="virtual-network" 
   />

<tags
   ms.author="danecke"
   ms.date="05/27/2015"
   ms.devlang="na"
   ms.service="virtual-network"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   />
   
# 仮想ネットワークの管理: ロード バランサーの TCP アイドル タイムアウト

**TCP アイドル タイムアウト**を使うと、Azure ロード バランサーに関連するクライアントとサーバーのセッションで、アイドル状態を保証する時間のしきい値を指定できます。TCP アイドル タイムアウトの値は 4 分です (Azure ロード バランサーの既定)。つまり、Azure ロード バランサーに関連するクライアントとサーバーのセッションで 4 分間アイドル状態が続くと、接続が終了します。

クライアントとサーバーの接続が終了すると、クライアント アプリケーションで "基礎になる接続が閉じられました: 維持される必要があった接続が、サーバーによって切断されました" というエラー メッセージが表示されます。

アイドル状態が長時間続く場合にも接続を維持するには、[TCP Keep-Alive](http://tools.ietf.org/html/rfc1122#page-101) を使うのが一般的です [(MSDN の例)](http://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)。TCP Keep-Alive が使用された場合、単純なパケットがクライアントによって定期的に (通常はサーバーのアイドル状態のタイムアウトしきい値よりも短い頻度) 送信されます。他のアクティビティが発生していなくても、サーバーによってこのパケット送信が接続アクティビティであると認識され、アイドル タイムアウト値に達することがなくなり、長時間接続が維持されます。

TCP Keep-Alive は便利ですが、モバイル アプリケーションでは有効ではありません。モバイル デバイスの限られた電力を消費するためです。モバイル アプリケーションで TCP Keep-Alive を使うと、継続的にネットワーク接続用に電力が使用されるため、バッテリーの消費が激しくなります。

モバイル デバイスの場合は、Azure ロード バランサーがサポートしている TCP アイドル タイムアウトの構成を使用してください。TCP アイドル タイムアウトは、受信接続を 4 分から 30 分の間で任意に設定できます (TCP アイドル タイムアウトは発信接続には設定できません)。これによって、クライアントがアクティブでない状態を長く続けても、サーバーとの接続をより長い間維持できます。モバイル デバイスのアプリケーションで TCP Keep-Alive 機能を使用し、30 分を超えてアイドル状態を続けてもサーバーとの接続を維持できます。ただし、この長時間の TCP アイドル タイムアウトでは、モバイル デバイスの電力消費を大幅に抑えながら、TCP Keep-Alive 要求を通常よりはるかに少ない頻度で送信します。

## 実装

TCP アイドル タイムアウトは次のように構成できます。

* [インスタンスレベル パブリック IP](http://msdn.microsoft.com/library/azure/dn690118.aspx)
* [負荷分散エンドポイント セット](http://msdn.microsoft.com/library/azure/dn655055.aspx)
* [仮想マシン エンドポイント](../virtual-machines/virtual-machines-set-up-endpoints.md)
* [Web ロール](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)
* [worker ロール](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)

## 次のステップ
* TBD

## PowerShell の例
[最新の Azure PowerShell リリース](https://github.com/Azure/azure-sdk-tools/releases)をダウンロードしてお試しください。

### インスタンスレベル パブリック IP の TCP タイムアウトを 15 分で構成します。

    Set-AzurePublicIP –PublicIPName webip –VM MyVM -IdleTimeoutInMinutes 15

IdleTimeoutInMinutes の設定は任意です。設定しない場合、既定のタイムアウト時間は 4 分です。4 分から 30 分の間で設定できます。

### 仮想マシンでの Azure エンドポイントの作成時にアイドル タイムアウトを設定します。

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM

### アイドル タイムアウトの構成を取得します。

    PS C:> Get-AzureVM –ServiceName “MyService” –Name “MyVM” | Get-AzureEndpoint
    
    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15
    
### 負荷分散エンドポイント セットでの TCP タイムアウトを設定します。

エンドポイントが負荷分散エンドポイント セットの一部である場合、TCP タイムアウトは負荷分散エンドポイント セットで設定される必要があります。

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15

## クラウド サービスの例

Azure SDK for .NET を使用してクラウド サービスをアップデートできます。

クラウド サービスのエンドポイントの設定は、.csdef で行われます。そのため、クラウド サービスのデプロイで TCP タイムアウトをアップデートするには、デプロイのアップグレードが必要です。TCP タイムアウトがパブリック IP 向けにのみ指定されている場合は例外として扱われます。パブリック IP は .cscfg で設定され、デプロイのアップデートやアップグレードを通じて更新されます。

エンドポイント設定の .csdef の変更は次のように行います。

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
      </Endpoints>
    </WorkerRole>The .cscfg changes for the timeout setting on Public IPs are:
    
    <NetworkConfiguration>
      <VirtualNetworkSite name="VNet"/>
      <AddressAssignments>
        <InstanceAddress roleName="VMRolePersisted">
          <PublicIPs>
            <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
          </PublicIPs>
        </InstanceAddress>
      </AddressAssignments>
    </NetworkConfiguration>
    
## API の例

ロード バランサーの分散は、サービス管理 API を使って構成できます。x-ms-version ヘッダーが 2014-06-01 以降のバージョンで設定されていることをご確認ください。

### デプロイされているすべての仮想マシンで、指定した負荷分散入力エンドポイントの構成をアップデートします。

#### 要求

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

LoadBalancerDistribution の値は、2 組のアフィニティの sourceIP、3 組のアフィニティの sourceIPProtocol、なし (アフィニティなし、つまり 5 組) で設定できます。

#### Response

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
        <LocalPort>local-port-number</LocalPort>
        <Port>external-port-number</Port>
        <LoadBalancerProbe>
          <Path>path-of-probe</Path>
          <Port>port-assigned-to-probe</Port>
          <Protocol>probe-protocol</Protocol>
          <IntervalInSeconds>interval-of-probe</IntervalInSeconds>
          <TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
        </LoadBalancerProbe>
        <LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
        <Protocol>endpoint-protocol</Protocol>
        <IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
        <EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
        <EndpointACL>
          <Rules>
            <Rule>
              <Order>priority-of-the-rule</Order>
              <Action>permit-rule</Action>
              <RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
              <Description>description-of-the-rule</Description>
            </Rule>
          </Rules>
        </EndpointACL>
      </InputEndpoint>
    </LoadBalancedEndpointList>
 

<!---HONumber=58_postMigration-->