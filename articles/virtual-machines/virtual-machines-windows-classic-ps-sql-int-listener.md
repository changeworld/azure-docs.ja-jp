<properties
	pageTitle="AlwaysOn 可用性グループの ILB リスナーの構成 | Microsoft Azure"
	description="このチュートリアルでは、クラシック デプロイメント モデルを使用して作成されたリソースを使用し、内部ロード バランサー (ILB) を使用して Azure で AlwaysOn 可用性グループ リスナーを作成します。"
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="MikeRayMSFT"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/08/2016"
	ms.author="mikeray" />

# Azure での AlwaysOn 可用性グループの ILB リスナーの構成

> [AZURE.SELECTOR]
- [内部リスナー](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [外部リスナー](virtual-machines-windows-classic-ps-sql-ext-listener.md)

## 概要

このトピックでは、**内部ロード バランサー (ILB)** を使用して AlwaysOn 可用性グループのリスナーを構成する方法について説明します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Resource Manager モデルで AlwaysOn 可用性グループの ILB リスナーを構成する方法については、「[Azure の AlwaysOn 可用性グループに使用する内部ロード バランサーの構成](virtual-machines-windows-portal-sql-alwayson-int-listener.md)」をご覧ください。


可用性グループには、オンプレミスのみ、Azure のみ、またはオンプレミスと Azure の両方にまたがるハイブリッド構成のレプリカを含めることができます。Azure レプリカは、同じリージョン内に配置することも、複数の仮想ネットワーク (VNet) を使用して複数のリージョンに配置することもできます。後述の手順では、既に[可用性グループは構成している](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)ものの、リスナーは構成していないと仮定しています。

## 内部リスナーのガイドラインと制限事項
ILB を使用した Azure の可用性グループ リスナーに関する次のガイドラインを確認してください。

- 可用性グループ リスナーは、Windows Server 2008 R2、Windows Server 2012、および Windows Server 2012 R2 でサポートされます。

- 内部可用性グループ リスナーは、クラウド サービスごとに 1 つのみサポートされます。これは、リスナーが ILB に対して構成されており、クラウド サービスごとに 1 つの ILB しか持たないためです。ただし、複数の外部リスナーを作成することができます。詳細については、「[Azure での AlwaysOn 可用性グループの外部リスナーの構成](virtual-machines-windows-classic-ps-sql-ext-listener.md)」をご覧ください。

- クラウド サービスのパブリック VIP を使用する外部リスナーも含まれるクラウド サービスに内部リスナーを作成することはできません。

## リスナーのアクセシビリティを決定する

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

この記事では、**内部ロード バランサー (ILB)** を使用するリスナーの作成を中心に説明します。パブリック/外部リスナーが必要な場合は、この記事の、[外部リスナー](virtual-machines-windows-classic-ps-sql-ext-listener.md)を設定するための手順を説明するバージョンを参照してください。

## Direct Server Return を使用して負荷分散 VM エンドポイントを作成する

ILB の場合、まず内部ロード バランサーを作成する必要があります。これについては、後述のスクリプトで行います。

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. **ILB** の場合、静的 IP アドレスを割り当てる必要があります。最初に、次のコマンドを実行して現在の VNet 構成を確認します。

		(Get-AzureVNetConfig).XMLConfiguration

1. レプリカをホストする VM が含まれるサブネットの**サブネット**名をメモします。これは、スクリプトの **$SubnetName** パラメーターで使用します。

1. 次に、レプリカをホストする VM が含まれるサブネットの **VirtualNetworkSite** 名と開始する **AddressPrefix** をメモします。両方の値を **Test-AzureStaticVNetIP** コマンドに渡し、**AvailableAddresses** を確認することによって、使用可能な IP アドレスを探します。たとえば、VNet の名前が *MyVNet* であり、サブネット アドレスの範囲が *172.16.0.128* から始まる場合、次のコマンドを実行すると、使用可能なアドレスのリストが表示されます。

		(Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses

1. 使用可能なアドレスのいずれかを選択し、後述のスクリプトの **$ILBStaticIP** パラメーターで使用します。

3. 次の PowerShell スクリプトをテキスト エディターにコピーし、環境に合わせて変数の値を設定します (一部のパラメーターには既定値が指定されています)。アフィニティ グループを使用している既存のデプロイでは、ILB を追加できないことに注意してください。ILB の要件の詳細については、「[内部ロード バランサー](../load-balancer/load-balancer-internal-overview.md)」を参照してください。また、可用性グループが複数の Azure リージョンにまたがっている場合、各データセンターにおいて、そのデータセンターに存在するクラウド サービスおよびノードごとにスクリプトを 1 回ずつ実行する必要があります。

		# Define variables
		$ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
		$AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
		$SubnetName = "<MySubnetName>" # subnet name that the replicas use in the VNet
		$ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
		$ILBName = "AGListenerLB" # customize the ILB name or use this default value

		# Create the ILB
		Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

		# Configure a load balanced endpoint for each node in $AGNodes using ILB
		ForEach ($node in $AGNodes)
		{
			Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
		}

1. 変数の設定後、スクリプトを、テキスト エディターからそれを実行する Azure PowerShell セッションにコピーします。プロンプトにまだ >> が表示される場合、スクリプトの実行が確実に開始されるようにするため、Enter キーを再度押します。

>[AZURE.NOTE] 現時点において、Azure クラシック ポータルでは内部 Load Balancer をサポートしていないため、Azure クラシック ポータルには ILB とエンドポイントのどちらも表示されません。ただし、エンドポイントで Load Balancer が実行されている場合は、**Get-AzureEndpoint** によって内部 IP アドレスが返されます。それ以外の場合、Null が返されます。

## KB2854082 がインストールされていることを確認する (必要に応じて)

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## 可用性グループ ノードでファイアウォール ポートを開く

[AZURE.INCLUDE [ファイアウォール](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## 可用性グループ リスナーを作成する

[AZURE.INCLUDE [ファイアウォール](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. ILB の場合、先ほど作成した内部 Load Balancer (ILB) の IP アドレスを使用する必要があります。この IP アドレスを取得するには、PowerShell で次のスクリプトを使用します。

		# Define variables
		$ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
		(Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

1. VM のいずれかで、次の PowerShell スクリプトをテキスト エディターにコピーし、変数を先ほどメモした値に設定します。

		# Define variables
		$ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
		$IPResourceName = "<IPResourceName>" # the IP Address resource name
		$ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)

		Import-Module FailoverClusters

		# If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.

		# Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
		# cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

1. 変数の設定後、管理者特権で Windows PowerShell ウィンドウを開いて、スクリプトをテキスト エディターからコピーし、それを実行する Azure PowerShell セッションに貼り付けます。プロンプトにまだ >> が表示される場合、スクリプトの実行が確実に開始されるようにするため、Enter キーを再度押します。

2. 各 VM で、これを繰り返します。このスクリプトでは、クラウド サービスの IP アドレスを使用して IP アドレス リソースを構成し、プローブ ポートなどの他のパラメーターを設定します。IP アドレス リソースがオンラインになると、プローブ ポートにおいて、このチュートリアルで先ほど作成した負荷分散エンドポイントからのポーリングに応答できます。

## リスナーをオンラインにする

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## フォローアップ項目

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## 可用性グループ リスナー(同一 VNet 内) をテストする

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## 次のステップ

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]

<!---HONumber=AcomDC_0629_2016-->