<properties 
   pageTitle="クラシック デプロイ モデルで PowerShell を使用した内部ロード バランサーの作成 | Microsoft Azure"
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

# PowerShell を使用した内部ロード バランサー (クラシック) の作成の開始

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager モデルを使用してこれらの手順を実行する](load-balancer-get-started-ilb-arm-ps.md)方法について説明します。

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


## 仮想マシンの内部ロード バランサー セットの作成

内部ロード バランサー セットと、このセットにトラフィックを送信するサーバーを作成するには、次の手順を実行する必要があります。

1. 負荷分散セットのサーバー間で負荷分散される着信トラフィックのエンドポイントとなる内部負荷分散のインスタンスを作成します。

1. 着信トラフィックを受信する仮想マシンに対応するエンドポイントを追加します。

1. 内部負荷分散インスタンスの仮想 IP (VIP) アドレスにトラフィックを送信するように負荷分散されたトラフィックを送信するサーバーを構成します。


### 手順 1. 内部負荷分散インスタンスを作成する

既存のクラウド サービス、またはリージョンの仮想ネットワークでデプロイされたクラウド サービスでは、次の Windows PowerShell コマンドを使用して内部負荷分散インスタンスを作成できます。

	$svc="<Cloud Service Name>"
	$ilb="<Name of your ILB instance>"
	$subnet="<Name of the subnet within your virtual network>"
	$IP="<The IPv4 address to use on the subnet-optional>"

	Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP


この [Add-azureendpoint](https://msdn.microsoft.com/library/dn495300.aspx) Windows PowerShell コマンドレットでは、DefaultProbe パラメーター セットを使用します。追加のパラメーター セットの詳細については、「[Add-azureendpoint](https://msdn.microsoft.com/library/dn495300.aspx)」をご覧ください。

### 手順 2. 内部負荷分散インスタンスにエンドポイントを追加する

たとえば次のようになります。

	$svc="mytestcloud"
	$vmname="DB1"
	$epname="TCP-1433-1433"
	$lbsetname="lbset"
	$prot="tcp"
	$locport=1433
	$pubport=1433
	$ilb="ilbset"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


### 手順 3. 新しい内部負荷分散エンドポイントにトラフィックを送信するようにサーバーを構成する

トラフィックを負荷分散するサーバーを、内部負荷分散インスタンスの新しい IP アドレス (VIP) を使用するように構成する必要があります。このアドレスは、内部負荷分散インスタンスがリッスンしているアドレスです。多くの場合、必要な操作は、内部負荷分散インスタンスの VIP の DNS レコードの追加または変更のみです。

内部負荷分散インスタンスの作成時に IP アドレスを指定した場合は、既に VIP があります。それ以外の場合は、次のコマンドから VIP を確認できます。

	$svc="<Cloud Service Name>"
	Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer



これらのコマンドを使用するには、値を入力して < と > を削除します。たとえば次のようになります。

	$svc="mytestcloud"
	Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer


Get-azureinternalloadbalancer コマンドの表示から IP アドレスをメモし、サーバーまたは DNS レコードに必要な変更を加えて、トラフィックが VIP に送信されることを確認します。

>[AZURE.NOTE] Microsoft Azure Platform は、さまざまな管理シナリオに静的でパブリックにルーティング可能な IPv4 アドレスを使用します。IP アドレスは 168.63.129.16 です。この IP アドレスはファイアウォールによってブロックされないように設定しておく必要があります。ブロックされると、予期しない動作が発生する可能性があるためです。Azure 内部負荷分散については、この IP アドレスはロード バランサーからの監視プローブによって使用され、負荷分散セットでの仮想マシンの正常性状態が判別されます。ネットワーク セキュリティ グループが、内部負荷分散セットで Azure の仮想マシンへのトラフィックを制限するために使用されている場合、または仮想ネットワーク サブネットに適用されている場合、168.63.129.16 からのトラフィックを許可するネットワーク セキュリティの規則が追加されていることを確認します。


## 内部負荷分散の例

2 つのサンプル構成に負荷分散セットを作成するエンド ツー エンドのプロセスの手順の説明については、次のセクションをご覧ください。

### インターネットに接続された多層アプリケーション

インターネットに接続された一連の Web サーバーに負荷分散データベース サービスを提供します。両方のサーバー セットは、1 つの Azure クラウド サービスでホストされます。TCP ポート 1433 への Web サーバーのトラフィックは、データベース層の 2 つの仮想マシンに分散される必要があります。図 1 に、この構成を示します。

![このデータベース層の内部負荷分散セット](./media/load-balancer-internal-getstarted/IC736321.png)


この構成は次のとおりです。

- 仮想マシンをホストする既存のクラウド サービスの名前は mytestcloud です。

- 既存の 2 つのデータベース サーバーの名前は DB1 と DB2 です。

- Web 層の Web サーバーは、プライベート IP アドレスを使用してデータベース層のデータベース サーバーに接続します。仮想ネットワークに独自の DNS を使用し、内部ロード バランサー セットの A レコードを手動で登録することもできます。

次のコマンドは、**ILBset** という名前の新しい内部ロード バランサー インスタンスを構成し、2 つのデータベース サーバーに対応する仮想マシンにエンドポイントを追加します。

	$svc="mytestcloud"
	$ilb="ilbset"
	Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb 
	$prot="tcp"
	$locport=1433
	$pubport=1433
	$epname="TCP-1433-1433"
	$lbsetname="lbset"
	$vmname="DB1"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

	$epname="TCP-1433-1433-2"
	$vmname="DB2"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


## 内部負荷分散の構成を削除する

内部ロード バランサー インスタンスからエンドポイントとしての仮想マシンを削除するには、次のコマンドを使用します。

	$svc="<Cloud service name>"
	$vmname="<Name of the VM>"
	$epname="<Name of the endpoint>"
	Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

これらのコマンドを使用するには、値を入力して < と > を削除します。

たとえば次のようになります。

	$svc="mytestcloud"
	$vmname="DB1"
	$epname="TCP-1433-1433"
	Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

クラウド サービスから内部ロード バランサー インスタンスを削除するには、次のコマンドを使用します。

	$svc="<Cloud service name>"
	Remove-AzureInternalLoadBalancer -ServiceName $svc

これらのコマンドを使用するには、値を入力して < と > を削除します。

たとえば次のようになります。

	$svc="mytestcloud"
	Remove-AzureInternalLoadBalancer -ServiceName $svc



## 内部ロード バランサーのコマンドレットに関する追加情報


内部負荷分散のコマンドレットに関する追加情報を取得するには、Windows PowerShell プロンプトで、次のコマンドを実行します。

- Get-help New-AzureInternalLoadBalancerConfig -full

- Get-help Add-AzureInternalLoadBalancer -full

- Get-help Get-AzureInternalLoadbalancer -full

- Get-help Remove-AzureInternalLoadBalancer -full

## 次のステップ

[ソース IP アフィニティを使用したロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0629_2016-->