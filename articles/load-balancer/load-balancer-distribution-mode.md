<properties 
   pageTitle="ロード バランサー分散モードの構成 |Microsoft Azure"
   description="ソース IP アフィニティをサポートするように Azure Load Balancer の分散モードを構成する方法"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/02/2015"
   ms.author="joaoma" />


# ロード バランサー分散モード (ソース IP アフィニティ)

ソース IP アフィニティと呼ばれる新しい分散モードが導入されています (セッション アフィニティやクライアント IP アフィニティとも呼ばれます)。Azure Load Balancer を 2 組 (ソース IP、接続先 IP) または 3 組 (ソース IP、接続先 IP、プロトコル) を使用するように構成して、使用可能なサーバーにトラフィックをマップできます。ソース IP アフィニティを使用して、同じクライアント コンピューターから開始された接続は、同じ DIP エンドポイントに移動します。

![ハッシュ ベースのロード バランサー](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

ソース IP アフィニティは Azure Load Balancer と RD ゲートウェイ間の非互換性の問題を解決します。これで、1 つのクラウド サービス内に RD ゲートウェイ ファームを構築できます。別の使用シナリオは、実際のデータのアップロードを UDP 経由で行う場合のメディアのアップロードですが、ここではコントロール プレーンが TCP 経由で達成されます。

- クライアントは、まず負荷分散されたパブリック アドレスに TCP セッションを開始し、特定の DIP に接続します。このチャネルは、アクティブなまま接続の状態を監視します。
- 同じクライアント コンピューターからの新しい UDP セッションが、同じ負荷分散されたパブリック エンドポイントに開始されます。この接続は、前の TCP 接続と同じ DIP エンドポイントにも送られるため、メディアのアップロードは TCP 経由のコントロール チャネルを維持しながら高スループットで実行されることが予想されます。
 
負荷分散されたセットが変更されると (仮想マシンの削除や追加)、クライアント要求の分散が再計算されます。最終的に同じサーバーに接続する既存のクライアント セッションからの新しい接続に依存することはできません。また、ソース IP アフィニティ分散モード を使用すると、トラフィックが均等に分散されない可能性があります。プロキシの背後で実行しているクライアントは、1 つの固有のクライアント アプリケーションと見なすことができます。

分散アルゴリズムは、5 つの組 (ソース IP、ソース ポート、接続先 IP、接続先ポート、プロトコルの種類) のハッシュを使用して、使用可能なサーバーにトラフィックをマップします。これは、トランスポート セッション内でのみ持続性を提供します。TCP または UDP の同じセッション内のパケットは、負荷分散されたエンドポイントの背後にある同じデータ センターの IP (DIP) インスタンスに送信されます。クライアントがもう一度接続を開くか、同じソース IP から新しいセッションを開始すると、ソース ポートが変更され、トラフィックは別の DIP エンドポイントに送信されます。

![ハッシュ ベースのロード バランサー](./media/load-balancer-distribution-mode/load-balancer-distribution.png)


## ロード バランサーのソース IP アフィニティ設定の構成
 
仮想マシンでは、powershell を使用してタイムアウトの設定を変更できます。
 
Azure エンドポイントを仮想マシンに追加してロード バランサー分散モードを設定する

	Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution “sourceIP”| Update-AzureVM

>[AZURE.NOTE]LoadBalancerDistribution は、2 組 (ソース IP と接続先 IP) の負荷分散の場合は sourceIP、3 組 (ソース IP、接続先 IP、プロトコル) の負荷分散の場合は sourceIPProtocol に設定できます。設定しない場合は、既定の動作 (5 組の負荷分散) を使用します。


エンドポイント ロード バランサー分散モード構成を取得する

	PS C:\> Get-AzureVM –ServiceName “MyService” –Name “MyVM” | Get-AzureEndpoint

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
 
LoadBalancerDistribution 要素が存在しない場合、Azure Load Balancer は既定の 5 組のアルゴリズムを使用します。

 
### 負荷分散エンドポイント セットで分散モードを設定する

エンドポイントが負荷分散エンドポイント セットの一部である場合、分散モードは負荷分散エンドポイント セットで設定される必要があります。

	Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution "sourceIP"

### 分散モードを変更するクラウド サービス構成

Azure SDK for .NET 2.5 (11 月にリリース予定) を使用して、.csdef に作成される Cloud Services の Cloud Services エンドポイント設定を更新できます。ロード バランサー分散モードを更新して Cloud Services をデプロイするには、デプロイのアップグレードが必要です。エンドポイント設定の .csdef の変更例は次の通りです。

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

サービス管理 API を使用して、ロード バランサーの分散を構成できます。x-ms-version ヘッダーが 2014-09-01 以降のバージョンに設定されていることを確認してください。
 
デプロイで指定した負荷分散セットの構成を更新します。

要求の例

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

	Response

	HTTP/1.1 202 Accepted 
	Cache-Control: no-cache 
	Content-Length: 0 
	Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0 
	x-ms-servedbyregion: ussouth2 
	x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af 
	Date: Thu, 16 Oct 2014 22:49:21 GMT

## 次のステップ

[内部ロード バランサーの概要](load-balancer-internal-overview.md)

[インターネットに接続するロード バランサーの構成の開始](load-balancer-internet-getstarted.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)

<!---HONumber=Nov15_HO2-->