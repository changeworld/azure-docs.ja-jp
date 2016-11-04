---
title: AlwaysOn 可用性グループ リスナーの構成 – Microsoft Azure
description: 1 つ以上の IP アドレスを持つ内部ロード バランサーを使用して、Azure Resouce Manager モデルで可用性グループ リスナーを構成します。
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: monicar

ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: MikeRayMSFT

---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manger"></a>1 つ以上の AlwaysOn 可用性グループ リスナーの構成 - Resource Manger
このトピックでは、以下の 2 つのことを行う方法を示します。

* PowerShell コマンドレットを使用して、SQL Server 可用性グループ用の内部ロード バランサーを作成します。
* ロード バランサーに追加の IP アドレスを追加して、複数の SQL Server 可用性グループをサポートします。 

> **重要**: Azure Virtual Machines 上の Always On 可用性グループの複数のリスナーのサポートはプレビュー機能で、ライセンス契約 (例: Enterprise Agreement、Microsoft Azure 契約、マイクロソフト オンライン サブスクリプション契約) に定められたプレビューの規定に従うものとします。また、[Microsoft Azure プレビューの追加使用条件](http://azure.microsoft.com/support/legal/preview-supplemental-terms/)が適用される場合があります。 
> 
> 

SQL Server において、可用性グループ リスナーは、クライアントがプライマリまたはセカンダリ レプリカ内のデータベースにアクセスするために接続する仮想ネットワーク名です。 Azure Virtual Machines では、ロード バランサーによってリスナーの IP アドレスが保持されます。 ロード バランサーは、プローブ ポートでリッスンしている SQL Server のインスタンスにトラフィックをルーティングします。 ほとんどの場合は、可用性グループでは内部ロード バランサーが使用されます。 Azure 内部ロード バランサーは、1 つ以上の IP アドレスをホストできます。 各 IP アドレスで、特定のプローブ ポートが使用されます。 このドキュメントでは、PowerShell を使用して新しいロード バランサーを作成したり、SQL Server 可用性グループ用の既存のロード バランサーに IP アドレスを追加したりする方法を説明します。 

内部ロード バランサーに複数の IP アドレスを割り当てる機能は Azure の新機能であり、Resource Manager モデルでのみ利用できます。 この作業を行うには、SQL Server 可用性グループが Resource Manager モデルの Azure 仮想マシンにデプロイされている必要があります。 両方の SQL Server 仮想マシンが同じ可用性セットに属している必要があります。 [Microsoft のテンプレート](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) を使用すると、Azure Resource Manager で自動的に可用性グループを作成することができます。 このテンプレートでは、内部ロード バランサーを含む可用性グループが自動的に作成されます。 必要に応じて [AlwaysOn 可用性グループを手動で構成](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)することもできます。

このトピックは、可用性グループの構成が既に済んでいることを前提としています。  

関連トピック:

* [Azure VM での AlwaysOn 可用性グループの構成 (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
* [Azure リソース マネージャーと PowerShell を使用した VNet 間の接続の構成](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [Start your PowerShell session](../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>Windows ファイアウォールの構成
SQL Server のアクセスを許可するように Windows ファイアウォールを構成します。 SQL Server インスタンスによって使用されるポートに加え、リスナー プローブによって使用されるポートへの TCP 接続を許可するようにファイアウォールを構成する必要があります。 詳細については、「 [Configure a Windows Firewall for Database Engine Access (データベース エンジンにアクセスできるように Windows ファイアウォールを構成する)](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1)」を参照してください。 SQL Server のポートとプローブ ポート用の受信規則を作成します。

## <a name="example-script:-create-an-internal-load-balancer-with-powershell"></a>サンプル スクリプト: PowerShell を使用して内部ロード バランサーを作成する
> [!NOTE]
> [Microsoft テンプレート](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) を使用して可用性グループを作成した場合、この手順を完了する必要はありません。 
> 
> 

次の PowerShell スクリプトでは、内部ロード バランサーを作成し、負荷分散規則を構成した後、ロード バランサーの IP アドレスを設定します。 スクリプトを実行するには、Windows PowerShell ISE を開いてスクリプト ウィンドウにスクリプトを貼り付けます。 `Login-AzureRMAccount` を使用して PowerShell にログインします。 複数の Azure サブスクリプションがある場合は、 `Select-AzureRmSubscription ` を使用してサブスクリプションを設定します。 

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the Front End configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the Back End configuration

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzureRMLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script:-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a>サンプル スクリプト: PowerShell を使用して既存のロード バランサーに IP アドレスを追加する
複数の可用性グループを使用するには、PowerShell を使用して既存のロード バランサーに追加の IP アドレスを追加します。 各 IP アドレスには、独自の負荷分散規則、プローブ ポート、およびフロント ポートが必要です。

フロント エンド ポートは、アプリケーションが SQL Server インスタンスに接続するために使用するポートです。 複数の異なる可用性グループの IP アドレスで同じフロント エンド ポートを使用できます。

> [!NOTE]
> SQL Server 可用性グループの場合は、各 IP アドレスに特定のプローブ ポートが必要です。 たとえば、ロード バランサー上の 1 つの IP アドレスでプローブ ポート 59999 が使用されている場合、そのロード バランサー上の他の IP アドレスではプローブ ポート 59999 を使用できません。 
> 
> 

* ロード バランサーの制限については、「 **ネットワークの制限 - Azure Resource Manager** 」にある「 [ロード バランサーごとのプライベート フロント エンド IP](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)」を参照してください。
* 可用性グループの制限については、「 [Restrictions (Availability Groups) (制限 (可用性グループ))](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG)」を参照してください。

次のスクリプトは、既存のロード バランサーに新しい IP アドレスを追加します。 環境に合わせて変数を更新してください。 ILB では、負荷分散フロント エンド ポート用のリスナー ポートが使用されます。 このポートには、SQL Server がリッスンしているポートを使用できます。 SQL Server の既定のインスタンスでは、これはポート 1433 です。 バック エンド ポートがフロント エンド ポートと同じになるように、可用性グループの負荷分散規則ではフローティング IP (ダイレクト サーバー リターン) が必要です。

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzureRmLoadBalancer 

$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzureRmLoadBalancer   
``` 



## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>ロード バランサーの IP アドレスを使用するようにクラスターを構成する
次に、クラスター上のリスナーを構成し、リスナーをオンラインに切り替えます。 そのために必要な作業は次のとおりです。 

1. 可用性グループ リスナーをフェールオーバー クラスターで作成する 
2. リスナーをオンラインにする

## <a name="1.-create-the-availability-group-listener-on-the-failover-cluster"></a>1.可用性グループ リスナーをフェールオーバー クラスターで作成する
この手順では、フェールオーバー クラスター マネージャーを使用してフェールオーバー クラスターにクライアント アクセス ポイントを追加した後、PowerShell を使用して、プローブ ポートでリッスンするようにクラスター リソースを構成します。 

* プライマリ レプリカのホストとなっている Azure 仮想マシンに RDP で接続します。 
* フェールオーバー クラスター マネージャーを開きます。
* **Networks** ノードを選択し、クラスター ネットワーク名をメモします。 この名前は、PowerShell スクリプトの `$ClusterNetworkName` 変数に使用します。
* クラスター名を展開して、 **[ロール]**をクリックします。
* **[ロール]** ウィンドウで、可用性グループ名を右クリックし、**[リソースの追加]**  >  **[クライアント アクセス ポイント]** の順に選択します。
* **[名前]** ボックスで、この新しいリスナーの名前を作成し、**[次へ]** を 2 回クリックし、**[完了]** をクリックします。 この時点では、リスナーまたはリソースをオンラインにしないでください。
  
  > [!NOTE]
  > この新しいリスナーの名前は、アプリケーションが SQL Server 可用性グループ内のデータベースに接続するために使用するネットワーク名になります。
  > 
  > 
* **[リソース]** タブをクリックして、作成したクライアント アクセス ポイントを展開します。 IP リソースを右クリックし、[プロパティ] をクリックします。 IP アドレスの名前をメモします。 この名前は、PowerShell スクリプトの `$IPResourceName` 変数に使用します。
* **[IP アドレス]** で **[静的 IP アドレス]** をクリックし、静的 IP アドレスを設定します。Azure ポータルでロード バランサーの IP アドレスを設定するときに使用したものと同じアドレスを指定してください。 
* このアドレスに対して NetBIOS を無効にし、 **[OK]**をクリックします。 ソリューションが複数の Azure VNet にまたがる場合は、IP リソースごとにこの手順を繰り返します。 
* SQL Server 可用性グループ リソースが IP アドレスに依存するようにします。 クラスター マネージャーでリソースを右クリックします (これは、**[リソース]** タブの **[その他のリソース]** にあります)。 
* 現在プライマリ レプリカのホストとなっているクラスター ノードから、管理者特権で PowerShell ISE を開き、新しいスクリプトに次のコマンドを貼り付けます。 **[依存関係]** タブで、リスナーの名前をクリックします。 
  
    ```powershell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>
  
    Import-Module FailoverClusters
  
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```
* 変数を修正して PowerShell スクリプトを実行し、新しいリスナーの IP アドレスとポートを構成します。
  
  > [!NOTE]
  > SQL Server が別個のリージョンに存在する場合は、PowerShell スクリプトを 2 回実行する必要があります。 1 回目は、クラスターのネットワーク名、クラスターの IP リソース名、ロード バランサーの IP アドレスを 1 つ目のリソース グループに基づいて指定します。 2 回目は、クラスターのネットワーク名、クラスターの IP リソース名、ロード バランサーの IP アドレスを 2 つ目のリソース グループに基づいて指定します。
  > 
  > 

これでクラスターに可用性グループ リスナー リソースが割り当てられました。

## <a name="2.-bring-the-listener-online"></a>2.リスナーをオンラインにする
可用性グループ リスナー リソースを構成したら、アプリケーションがリスナーを使って可用性グループ内のデータベースに接続できるように、リスナーをオンラインに切り替えます。

* フェールオーバー クラスター マネージャーに戻ります。 **[ロール]** を展開し、[可用性グループ] を強調表示します。 **[リソース]** タブで、リスナー名を右クリックし、**[プロパティ]** をクリックします。
* **[依存関係]** タブをクリックします。 複数のリソースが一覧表示される場合は、IP アドレスに OR (AND ではなく) 依存関係があることを確認します。 **[OK]**をクリックします。
* リスナー名を右クリックし、 **[オンラインにする]**をクリックします。
* リスナーがオンラインになったら、**[リソース]** タブで可用性グループを右クリックし、**[プロパティ]** をクリックします。
* リスナー名のリソース (IP アドレス リソース名ではなく) への依存関係を作成します。 **[OK]**をクリックします。
* SQL Server Management Studio を起動し、プライマリ レプリカに接続します。
* **[AlwaysOn 高可用性]** | 、**[可用性グループ]** | 、**[可用性グループ リスナー]** の順に移動します。 
* フェールオーバー クラスター マネージャーで作成したリスナー名が表示されます。 リスナー名を右クリックし、 **[プロパティ]**をクリックします。
* **[ポート]** ボックスで、以前に使用した $EndpointPort (既定値は 1433) を使用し、可用性グループ リスナーのポート番号を指定して、**[OK]** をクリックします。

これで、SQL Server 可用性グループが Resource Manager モードの Azure 仮想マシンにデプロイされました。 

## <a name="test-the-connection-to-the-listener"></a>リスナーへの接続をテストする
接続をテストするには、次の手順に従います。

1. 同じ仮想ネットワークに存在する、レプリカを所有していない SQL Server に RDP で接続します。 クラスター内の別の SQL Server がそれに該当します。
2. **sqlcmd** ユーティリティを使用して接続をテストします。 たとえば次のスクリプトは、Windows 認証を使用し、リスナー経由でプライマリ レプリカとの **sqlcmd** 接続を確立しています。
   
        sqlmd -S <listenerName> -E
   
    リスナーが既定のポート (1433) 以外のポートを使用している場合は、そのポートを接続文字列で指定します。 たとえば、次の sqlcmd コマンドは、ポート 1435 でリスナーに接続します。 
   
        sqlcmd -S <listenerName>,1435 -E

SQLCMD 接続では、プライマリ レプリカをホストしている SQL Server インスタンスに対して自動的に接続されます。 

> [!NOTE]
> 指定したポートは、両方の SQL Server のファイアウォールで必ず開放してください。 使用する TCP ポートに対する入力方向の規則が両方のサーバーに必要となります。 詳細については、「 [ファイアウォール規則を追加または編集する](http://technet.microsoft.com/library/cc753558.aspx) 」を参照してください。 
> 
> 

## <a name="guidelines-and-limitations"></a>ガイドラインと制限
内部ロード バランサーを使用した Azure の可用性グループ リスナーに関する次のガイドラインを確認してください。

* 内部ロード バランサーでは、同じ仮想ネットワーク内からしかリスナーにアクセスできません。

## <a name="for-more-information"></a>BLOB の詳細
詳細については、「 [Azure VM での AlwaysOn 可用性グループの手動構成](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)」をご覧ください。

### <a name="powershell-cmdlets"></a>PowerShell コマンドレット
Azure 仮想マシン用の内部ロード バランサーを作成するには、次の PowerShell コマンドレットを使用します。

* `New-AzureRmLoadBalancer` は、ロード バランサーを作成します。 詳細については、「 [New-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt619450.aspx) 」を参照してください。 
* `New-AzureRMLoadBalancerFrontendIpConfig` は、ロード バランサー用のフロント エンド IP 構成を作成します。 詳細については、「 [New-AzureRMLoadBalancerFrontendIpConfig](http://msdn.microsoft.com/library/mt603510.aspx) 」を参照してください。
* `New-AzureRmLoadBalancerRuleConfig` は、ロード バランサー用の規則の構成を作成します。 詳細については、「 [New-AzureRmLoadBalancerRuleConfig](http://msdn.microsoft.com/library/mt619391.aspx) 」を参照してください。 
* `New-AzureRMLoadBalancerBackendAddressPoolConfig` は、ロード バランサー用のバックエンド アドレス プール構成を作成します。 詳細については、「 [New-AzureRmLoadBalancerBackendAddressPoolConfig](http://msdn.microsoft.com/library/mt603791.aspx) 」を参照してください。 
* `New-AzureRmLoadBalancerProbeConfig` は、ロード バランサー用のプローブの構成を作成します。 詳細については、「 [New-AzureRmLoadBalancerProbeConfig](http://msdn.microsoft.com/library/mt603847.aspx) 」を参照してください。

Azure リソース グループからロード バランサーを削除する必要がある場合は、 `Remove-AzureRmLoadBalancer`を使用します。 詳細については、「 [Delete-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx)」を参照してください。

<!--HONumber=Oct16_HO2-->


