<properties
   pageTitle="管理: ロード バランサー分散モード (ソース IP アフィニティ)"
   description="Azure ロード バランサー分散モードの管理機能"
   services="virtual-network"
   documentationCenter=""
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   />

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="telmos"
   />

# 仮想ネットワークの管理: ロード バランサー分散モード (ソース IP アフィニティ)
**ソース IP アフィニティ** (**セッション アフィニティ**や**クライアント IP アフィニティ**とも呼ばれます) は、Azure のロード バランサー分散モードであり、各クライアントの接続を異なる Azure ホスト サーバーに動的に分散するのではなく (既定のロード バランサーの動作)、クライアント 1 つと Azure にホストされたサーバー 1 つを結び付けます。

ソース IP アフィニティを使うと、Azure のロード バランサーを、2 組の組み合わせ (ソース IP と接続先の IP) か、3 組の組み合わせ (ソース IP、接続先の IP、プロトコル) で構成し、利用可能な Azure ホスト サーバーのプールにトラフィックをマッピングできます。ソース IP アフィニティを使うときは、同じクライアント コンピューターで初期化された接続は 1 つの DIP エンドポイント (1 つの Azure ホスト サーバー) で処理されます。

## サービスの配信元

ソース IP アフィニティは以前の [Azure ロード バランサーと RD ゲートウェイ (DOC) との非互換性の問題](http://go.microsoft.com/fwlink/p/?LinkId=517389)を解決します。

## 実装

ソース IP アフィニティは、次のように構成できます。

* [仮想マシン エンドポイント](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md)
* [負荷分散エンドポイント セット](../load-balancer/load-balancer-overview.md)
* [Web ロール](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)
* [worker ロール](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)

## シナリオ
1. 1 つのクラウド サービスを使ったリモート デスクトップ ゲートウェイ クラスター
2. メディアのアップロード (つまりデータ用 UDP と 制御用 TCP)
  * クライアントで TCP セッションが Azure でホストされた負荷分散パブリック IP アドレスに対して初期化されます。
  * クライアントの要求はロード バランサーによって DIP に転送されます。このチャンネルは動作し続け、接続の状態を監視します。
  * クライアントは同じ Azure でホストされた負荷分散パブリック IP アドレスに対して UDP セッションを初期化します。
  * Azure ロード バランサーは、TCP 接続として同じ DIP エンドポイントに要求を転送します。
  * クライアントは TCP を通じて制御チャンネルを維持して信頼性を確保しながら、より高い UDP のスループットでメディアをアップロードします。

## 注意事項
* 負荷分散セットが変更された場合 (仮想マシンを追加や削除した場合)、クライアント チャネルの分散は再計算されます。既存のクライアントからの新しい接続は、もともと使用されていたものとは異なるサーバーで処理される可能性があります。
* ソース IP アフィニティを使用すると、複数の Azure ホスト サーバーにトラフィックが均等に分散されない可能性があります。
* プロキシ経由でトラフィックをルーティングするクライアントは、Azure ロード バランサーからは 1 つのクライアントとして認識される可能性があります。

## PowerShell の例
[最新の Azure PowerShell リリース](https://github.com/Azure/azure-sdk-tools/releases)をダウンロードしてお試しください。

### Azure エンドポイントを仮想マシンに追加してロード バランサー分散モードを設定する

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution “sourceIP”| Update-AzureVM  

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -LoadBalancerDistribution "sourceIP"| Update-AzureVM  

LoadBalancerDistribution は、2 組 (ソース IP と接続先 IP) の負荷分散の場合は sourceIP、3 組 (ソース IP、接続先 IP、プロトコル) の負荷分散の場合は sourceIPProtocol に設定できます。設定しない場合は、既定の動作 (5 組の負荷分散) を使用します。

### エンドポイント ロード バランサー分散モード構成を取得する
    PS C:\> Get-AzureVM –ServiceName "mySvc" -Name "MyVM1" | Get-AzureEndpoint

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
    LoadBalancerDistribution : sourceIP

LoadBalancerDistribution 要素が存在しない場合、Azure ロード バランサーは既定の 5 組のアルゴリズムを使用します。

### 負荷分散エンドポイント セットで分散モードを設定する

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution "sourceIP"

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 LoadBalancerDistribution "sourceIP"

エンドポイントが負荷分散エンドポイント セットの一部である場合、分散モードは負荷分散エンドポイント セットで設定される必要があります。

## クラウド サービスの例

Azure SDK for .NET を使用してクラウド サービスをアップデートできます。

クラウド サービスのエンドポイントの設定は、.csdef で行われます。ロード バランサー分散モードをアップデートしてクラウド サービスをデプロイするには、デプロイのアップグレードが必要です。

エンドポイント設定の .csdef の変更例は次の通りです。

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
      </Endpoints>
    </WorkerRole>
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

ロード バランサーの分散は、サービス管理 API を使って構成できます。x-ms-version ヘッダーが 2014-09-01 以降のバージョンで設定されていることをご確認ください。

### デプロイで指定した負荷分散セットの構成をアップデートします。

#### 要求

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet

    x-ms-version: 2014-09-01

    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

LoadBalancerDistribution の値は、2 組のアフィニティの sourceIP、3 組のアフィニティの sourceIPProtocol、なし (アフィニティなし、つまり 5 組) で設定できます。

#### Response

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

<!---HONumber=AcomDC_0323_2016-->