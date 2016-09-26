<properties
   pageTitle="ロード バランサーの TCP アイドル タイムアウトの構成 | Microsoft Azure"
   description="ロード バランサーの TCP アイドル タイムアウトの構成"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/03/2016"
   ms.author="sewhee" />

# ロード バランサーの TCP アイドル タイムアウト設定を変更する

既定の構成では、Azure Load Balancer はアイドル タイムアウトが 4 分に設定されています。

つまり、非アクティブ状態の期間がタイムアウト値よりも長い場合は、クライアントとクラウド サービス間の TCP または HTTP セッションがまだ続いているという保証はありません。

接続が解除されると、「基になる接続が閉じられました: 維持される必要があった接続が、サーバーによって切断されました」というエラー メッセージがクライアント アプリケーションに表示されます。

接続をアクティブに維持する時間を延ばすには、TCP Keep-alive を使用するのが一般的です (.NET の例は[こちら](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)でご覧いただけます)。 接続のアクティビティがなくなったことを検知すると、パケットが送信されます。このネットワーク アクティビティにより、アイドル タイムアウトを回避して、接続を長時間維持できるようになります。

接続の切断を避けるためには、アイドル タイムアウト設定よりも小さい間隔で、TCP keep-alive を構成するか、アイドル タイムアウト値を大きくします。

TCP Keep-alive は、バッテリーの制約がないシナリオでは有効ですが、通常、モバイル アプリケーションにはお勧めしません。モバイル アプリケーションから TCP Keep-alive を使用すると、デバイスのバッテリーの消耗を速める可能性が高くなります。

このようなシナリオをサポートするために追加されたのが、構成可能なアイドル タイムアウトです。これにより、タイムアウトを 4 ～ 30 分に設定できるようになりました。この設定は着信接続のみ使用できます。

![TCP タイムアウト](./media/load-balancer-tcp-idle-timeout/image1.png)

次のセクションでは、仮想マシンとクラウド サービスのアイドル タイムアウト設定を変更する方法について説明します。

>[AZURE.NOTE] これらの設定の構成を行うには、Azure PowerShell の最新パッケージがインストールされていることを確認してください。

## インスタンスレベル パブリック IP の TCP タイムアウトを 15 分で構成します。

    Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15

`IdleTimeoutInMinutes` はオプションです。設定しない場合、既定のタイムアウト時間は 4 分です。

>[AZURE.NOTE] 設定できるタイムアウトの範囲は 4 ～ 30 分です。

## 仮想マシンでの Azure エンドポイントの作成時にアイドル タイムアウトを設定する

エンドポイントのタイムアウト設定を変更します。

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM

アイドル タイムアウトの構成を取得します。

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
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

## 負荷分散エンドポイント セットでの TCP タイムアウトを設定する

エンドポイントが負荷分散エンドポイント セットの一部である場合、TCP タイムアウトは負荷分散エンドポイント セットで設定される必要があります。

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15

## クラウド サービスのタイムアウト設定を変更する

Azure SDK for .NET 2.4 を使用してクラウド サービスを更新できます。

クラウド サービスのエンドポイントの設定は、.csdef ファイルで行います。クラウド サービスを展開するための TCP タイムアウトを更新するには、デプロイのアップグレードが必要です。TCP タイムアウトがパブリック IP 向けにのみ指定されている場合は例外として扱われます。パブリック IP は .cscfg ファイルで設定され、デプロイのアップデートやアップグレードを通じて更新することができます。

エンドポイント設定の .csdef の変更は次のように行います。

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
      </Endpoints>
    </WorkerRole>

パブリック IP のタイムアウト設定の .cscfg の変更は次のように行います。

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

## Rest API の例

TCP アイドルのタイムアウトは、サービス管理 API を使って構成できます。x-ms-version ヘッダーが 2014-06-01 以降のバージョンで設定されていることをご確認ください。

デプロイされているすべての仮想マシンで、指定した負荷分散入力エンドポイントの構成をアップデートします。

    Request:

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>
<BR>

    Response:

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

## 次のステップ

[内部ロード バランサーの概要](load-balancer-internal-overview.md)

[インターネットに接続するロード バランサーの構成の開始](load-balancer-get-started-internet-arm-ps.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

<!---HONumber=AcomDC_0914_2016-->