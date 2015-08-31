<properties 
   pageTitle="SQL AlwaysOn のロード バランサーの構成 |Microsoft Azure"
   description="SQL AlwaysOn と連携するようにロード バランサーを構成し、PowerShell を利用して SQL の実装用にロード バランサーを作成する方法"
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
   ms.date="08/12/2015"
   ms.author="joaoma" />

# SQL AlwaysOn のロード バランサーの構成

SQL Server AlwaysOn 可用性グループは、ILB と共に実行できるようになりました。可用性グループは、高可用性と障害復旧を目的とした SQL Server の主要なソリューションです。可用性グループ リスナーによって、クライアント アプリケーションは、構成されているレプリカの数に関係なく、プライマリ レプリカにシームレスに接続できます。

リスナー (DNS) 名は負荷分散された IP アドレスにマップされ、Azure の Load Balancer によって、着信トラフィックがレプリカ セットのプライマリ サーバーのみに送られます。


SQL Server AlwaysOn (リスナー) エンドポイントでは、ILB のサポートを使用できます。リスナーのアクセスを制御できるため、Virtual Network (VNet) の特定のサブネットから負荷分散された IP アドレスを選択できるようになりました。

リスナーで ILB を使用することで、SQL サーバー エンドポイント (例: Server=tcp:ListenerName,1433;Database=DatabaseName) には次の場所からのみアクセスできます。

同じ Virtual Network 内のサービスと VM、接続済みのオンプレミス ネットワークのサービスと VM、相互接続された Vnet のサービス と VM

![ILB\_SQLAO\_NewPic](./media/load-balancer-configure-sqlao/sqlao1.jpg)


内部ロード バランサーの構成には、PowerShell のみを使用できます。


## サービスに内部ロード バランサーを追加する 

### 手順 1.

次の例では、"Subnet-1" という名前のサブネットを含む仮想ネットワークを構成します。

	Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc

手順 2.

## 各 VM で ILB の負荷分散エンドポイントを追加する

	Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –
	DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

 	Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

上の例では、sqlsvc1 および sqlsvc2 という 2 つの VM がクラウド サービス "Sqlsvc" で実行されます。DirectServerReturn スイッチで ILB を作成したら、負荷分散されたエンドポイントを ILB に追加して、SQL が可用性グループのリスナーを構成できるようにします。

SQL AlwaysOn の作成について詳しくは、「[チュートリアル: Azure AlwaysOn 可用性グループ](https://msdn.microsoft.com/library/dn249504.aspx)」または「[Using the Portal Gallery (ポータル ギャラリーの使用)](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx)」を参照してください。


## 関連項目

[インターネットに接続するロード バランサーの構成の開始](load-balancer-internet-getstarted.md)

[内部ロード バランサーの構成の開始](load-balancer-internal-getstarted.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=August15_HO8-->