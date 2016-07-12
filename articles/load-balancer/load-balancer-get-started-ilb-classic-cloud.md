<properties 
   pageTitle="クラシック デプロイ モデルでのクラウド サービスの内部ロード バランサーの作成 | Microsoft Azure"
   description="クラシック デプロイ モデルで PowerShell を使用して、内部ロード バランサーを作成する方法について説明します。"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="joaoma" />

# クラウド サービスの内部ロード バランサー (クラシック) の作成の開始

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

<BR>

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager モデルを使用してこれらの手順を実行する](load-balancer-get-started-ilb-arm-ps.md)方法について説明します。


## クラウド サービスの内部ロード バランサーを構成する

内部ロード バランサーは、仮想マシンとクラウド サービスの両方でサポートされています。リージョンの仮想ネットワークの外部にあるクラウド サービスで作成された内部ロード バランサー エンドポイントは、そのクラウド サービス内でのみアクセスできます。

次の例に示すように、内部ロード バランサーの構成は、クラウド サービスでの最初のデプロイの作成時に設定する必要があります。

>[AZURE.IMPORTANT] 以下の手順の前提条件は、クラウド デプロイのために仮想ネットワークを作成しておくことです。内部負荷分散を作成するには仮想ネットワーク名とサブネット名が必要です。

### 手順 1.

Visual Studio でクラウド デプロイのサービス構成ファイル (.cscfg) を開き、ネットワーク構成の最後の "`</Role>`" 項目の下に、内部負荷分散を作成するための次のセクションを追加します。




	<NetworkConfiguration>
	  <LoadBalancers>
	    <LoadBalancer name="name of the load balancer">
	      <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
	    </LoadBalancer>
	  </LoadBalancers>
	</NetworkConfiguration>


ネットワーク構成ファイルの値を追加し、どのようになるか確認しましょう。この例では、「test\_vnet」という名前のサブネットを「test\_subnet」という名前のサブネット 10.0.0.0/24 と静的 IP 10.0.0.4 で作成したと仮定します。ロード バランサーの名前は「testLB」になります。

	<NetworkConfiguration>
	  <LoadBalancers>
	    <LoadBalancer name="testLB">
	      <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
	    </LoadBalancer>
	  </LoadBalancers>
	</NetworkConfiguration>

ロード バランサー スキーマの詳細については、「[ロード バランサーの追加](https://msdn.microsoft.com/library/azure/dn722411.aspx)」を参照してください。

### 手順 2.


サービス定義 (.csdef) ファイルを変更し、内部負荷分散にエンドポイントを追加します。ロール インスタンスが作成された直後に、サービス定義ファイルによりそのロール インスタンスが内部負荷分散に追加されます。


	<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
	  <Endpoints>
	    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
	  </Endpoints>
	</WorkerRole>

前の例と同じ値を利用してサービス定義ファイルに値を追加してみましょう。

	<WorkerRole name=WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
	  <Endpoints>
	    <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
	  </Endpoints>
	</WorkerRole>

ネットワーク トラフィックの負荷は testLB ロード バランサーを使用して分散されます。受信要求にはポート 80 が使用されます。worker ロール インスタンスへの送信もポート 80 で行われます。


## 次のステップ

[ソース IP アフィニティを使用したロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0629_2016-->