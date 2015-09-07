<properties
	pageTitle="SharePoint Server 2013 ファームのフェーズ 4 | Microsoft Azure"
	description="SharePoint サーバーの仮想マシンおよび新しい SharePoint ファームを作成します。"
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2015"
	ms.author="josephd"/>

# SharePoint イントラネット ファーム ワークロードのフェーズ 4: SharePoint サーバーの構成

イントラネット専用 SharePoint 2013 ファームと SQL Server AlwaysOn 可用性グループを Azure インフラストラクチャ サービスにデプロイする作業のこのフェーズでは、SharePoint ファームのアプリケーション層と Web 層を構築し、SharePoint 構成ウィザードを使用してファームを作成します。

[フェーズ 5](virtual-machines-workload-intranet-sharepoint-phase5.md) に進むには、このフェーズを完了する必要があります。全フェーズについては、「[Deploying SharePoint with SQL Server AlwaysOn Availability Groups in Azure (Azure での SharePoint と SQL Server AlwaysOn 可用性グループのデプロイ)](virtual-machines-workload-intranet-sharepoint-overview.md)」をご覧ください。

## Azure での SharePoint サーバー仮想マシンの作成

SharePoint サーバー仮想マシンは 4 つあります。2 つはフロントエンド Web サーバー用であり、残りの 2 つは SharePoint アプリケーションの管理およびホスト用です。各層の 2 つの SharePoint サーバーによって高可用性が実現されます。

Azure PowerShell コマンドの次のブロックを使用して、4 つの SharePoint サーバーの仮想マシンを作成します。< and > の文字を削除して、各変数の値を指定します。この Azure PowerShell コマンド セットでは、次の表の値を使用します。

- 表 M (仮想マシン)
- 表 V (仮想ネットワーク設定)
- 表 S (サブネット)
- 表 A (可用性セット)
- 表 C (クラウド サービス)

表 M は「[フェーズ 2: ドメイン コントローラーの構成](virtual-machines-workload-intranet-sharepoint-phase2.md)」で、表 V、S、A、C は「[フェーズ 1: Azure の構成](virtual-machines-workload-intranet-sharepoint-phase1.md)」でそれぞれ定義したものです。

適切な値をすべて指定したら、Azure PowerShell コマンド プロンプトでそのブロックを実行します。

	# Create the first SharePoint application server
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 3 – Availability set name column>"
	$image= Get-AzureVMImage | where { $_.Label -eq "SharePoint Server 2013 Trial" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SharePoint application server."
	$cred2=Get-Credential –Message "Now type the name and password of an account that has permissions to add this virtual machine to the domain."
	$ADDomainName="<name of the AD domain that the server is joining (example CORP)>"
	$domainDNS="<FQDN of the AD domain that the server is joining (example corp.contoso.com)>"
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$subnetName="<Table 6 – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$serviceName="<Table C – Item 3 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the second SharePoint application server
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SharePoint application server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the first SharePoint web server
	$vmName="<Table M – Item 8 - Virtual machine name column>"
	$vmSize="<Table M – Item 8 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 4 – Availability set name column>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SharePoint web server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the second SharePoint web server
	$vmName="<Table M – Item 9 - Virtual machine name column>"
	$vmSize="<Table M – Item 9 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SharePoint web server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

「[リモート デスクトップ接続を使用した仮想マシンへのログオン](virtual-machines-workload-intranet-sharepoint-phase2.md#logon)」の手順を 4 回 (SharePoint サーバーごとに 1 回) 実行し、 [Domain]\\sp\_farm\_db アカウントの資格情報を使用してログオンします。これらの資格情報は、「[フェーズ 2: ドメイン コント ローラーの構成](virtual-machines-workload-intranet-sharepoint-phase2.md)」で作成しました。

「[接続をテストするには](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn)」の手順を 4 回 (SharePoint サーバーごとに 1 回) 実行して、組織のネットワーク上の場所への接続をテストします。

## SharePoint ファームの構成

ファームの最初の SharePoint サーバーを構成するには、次の手順に従います。

1.	最初の SharePoint アプリケーション サーバーのデスクトップで、**[SharePoint 2013 製品構成ウィザード]** をダブルクリックします。プログラムがコンピューターに変更を加えることを許可するよう求められたら、**[はい]** をクリックします。
2.	**[SharePoint 製品へようこそ]** ページで **[次へ]** をクリックします。
3.	サービス (IIS など) が再起動またはリセットされることを警告する **[SharePoint 製品構成ウィザード]** ダイアログが表示されます。**[はい]** をクリックします。
4.	**[サーバー ファームへの接続]** ページで、**[新しいサーバー ファームの作成]** をクリックし、**[次へ]** をクリックします。
5.	**[構成データベースの設定]** ページで、次の操作を行います。
 - **[データベース サーバー]** に、プライマリ データベース サーバーの名前を入力します。
 - **[ユーザー名]**に、「[Domain]**\\sp\_farm\_db** (「[フェーズ 2: ドメイン コントローラーの構成](virtual-machines-workload-intranet-sharepoint-phase2.md)」で作成)」と入力します。sp\_farm\_db アカウントには、データベース サーバーに対する sysadmin 権限があります。
 - **[パスワード]** に、sp\_farm\_db アカウントのパスワードを入力します。
6.	**[次へ]** をクリックします。
7.	**[ファームのセキュリティ設定の指定]** ページで、パスフレーズを 2 回入力します。後で参照できるように、パスフレーズを記録して、安全な場所に保管しておきます。**[次へ]** をクリックします。
8.	**[SharePoint サーバーの全体管理 Web アプリケーションの構成]** ページで、**[次へ]** をクリックします。
9.	**[SharePoint 製品構成ウィザードの終了]** ページが表示されます。**[次へ]** をクリックします。
10.	**[SharePoint 製品の構成中]** ページが表示されます。構成プロセスが完了するまで約 8 分待ちます。
11.	ファームが正常に構成されたら、**[完了]** をクリックします。新しい管理 Web サイトが開始されます。
12.	SharePoint ファームの構成を開始するには、**[ウィザードの開始]** をクリックします。

2 番目の SharePoint アプリケーション サーバーと、2 つのフロントエンド Web サーバーで、次の手順を実行します。

1.	デスクトップで **[SharePoint 2013 製品構成ウィザード]** をダブルクリックします。プログラムがコンピューターに変更を加えることを許可するよう求められたら、**[はい]** をクリックします。
2.	**[SharePoint 製品へようこそ]** ページで **[次へ]** をクリックします。
3.	サービス (IIS など) が再起動またはリセットされることを警告する **[SharePoint 製品構成ウィザード]** ダイアログが表示されます。**[はい]** をクリックします。
4.	**[サーバー ファームへの接続]** ページで、**[既存のサーバー ファームへの接続]** をクリックし、**[次へ]** をクリックします。
5.	**[構成データベースの設定]** ページで、**[データベース サーバー]** にプライマリ データベース サーバーの名前を入力し、**[データベース名の取得]** をクリックします。
6.	データベース名の一覧の **[SharePoint\_Config]** をクリックし、**[次へ]** をクリックします。
7.	**[ファームのセキュリティ設定の指定]** ページで、前の手順のパスフレーズを入力します。**[次へ]** をクリックします。
8.	**[SharePoint 製品構成ウィザードの終了]** ページが表示されます。**[次へ]** をクリックします。
9.	**[構成成功]** ページで **[完了]** をクリックします。

ファームの作成時に、プライマリ SQL Server 仮想マシンで一連のサーバー ログインが構成されます。SQL Server 2012 では、包含データベースのパスワードを持つユーザーの概念が導入されています。データベース自体にすべてのデータベース メタデータとユーザー情報が格納されており、このデータベースで定義されたユーザーは、対応するログインを持つ必要はありません。このデータベース内の情報は、可用性グループによってレプリケートされ、フェールオーバー後に使用できます。詳細については、「[包含データベース](http://go.microsoft.com/fwlink/p/?LinkId=262794)」をご覧ください。

ただし、既定では SharePoint データベースは包含データベースではありません。そのため、SharePoint ファーム アカウントの一連のログインがプライマリ データベース サーバーと同じものになるように、セカンダリ データベース サーバーを手動で構成する必要があります。この同期は、両方のサーバーに同時に接続することで、SQL Server Management Studio から実行できます。

この初期セットアップが完了すると、SharePoint ファームの機能について、さらに多くの構成オプションを使用できます。詳細については、「[Azure インフラストラクチャ サービスでの SharePoint 2013 の計画](http://msdn.microsoft.com/library/dn275958.aspx)」をご覧ください。

## 内部負荷分散の構成

SharePoint ファームへのクライアント トラフィックが 2 つのフロントエンド Web サーバーに均等に分散されるように内部負荷分散を構成します。この場合、名前とサブネットのアドレス空間から割り当てられた独自の IP アドレスで構成される内部負荷分散インスタンスを指定する必要があります。内部ロード バランサー用に選択した IP アドレスが使用できるかどうかを確認するには、次の Azure PowerShell コマンドを使用します。

	$vnet="<Table V – Item 1 – Value column>"
	$testIP="<a chosen IP address from the subnet address space, Table S - Item 1 – Subnet address space column>"
	Test-AzureStaticVNetIP –VNetName $vnet –IPAddress $testIP

**Test-AzureStaticVNetIP** コマンドに表示される **IsAvailable** フィールドが **True** の場合は、その IP アドレスを使用できます。

ローカル コンピューターで Azure PowerShell コマンド プロンプトから次のコマンド セットを実行します。

	$serviceName="<Table C – Item 3 – Cloud service name column>"
	$ilb="<name of your internal load balancer instance>"
	$subnet="<Table S – Item 1 – Subnet name column>"
	$IP="<an available IP address for your ILB instance>"
	Add-AzureInternalLoadBalancer –ServiceName $serviceName -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

	$prot="tcp"
	$locport=80
	$pubport=80
	# This example assumes unsecured HTTP traffic to the SharePoint farm.

	$epname="SPWeb1"
	$vmname="<Table M – Item 8 – Virtual machine name column>"
	Get-AzureVM –ServiceName $serviceName –Name $vmname | Add-AzureEndpoint -Name $epname -LBSetName $ilb -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

	$epname="SPWeb2"
	$vmname="<Table M – Item 9 – Virtual machine name column>"
	Get-AzureVM –ServiceName $serviceName –Name $vmname | Add-AzureEndpoint -Name $epname -LBSetName $ilb -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

次に、SharePoint ファームの完全修飾ドメイン名 (sp.corp.contoso.com など) を、内部ロード バランサー インスタンスに割り当てられている IP アドレス (前の Azure PowerShell コマンド ブロックの **$IP** の値) に解決する DNS アドレス レコードを、組織の DNS インフラストラクチャに追加します。

次の図は、このフェーズが問題なく完了したときの構成を示しています。

![](./media/virtual-machines-workload-intranet-sharepoint-phase4/workload-spsqlao_04.png)

## 次のステップ

このワークロードを引き続き構成するには、「[フェーズ 5: 可用性グループの作成と SharePoint データベースの追加](virtual-machines-workload-intranet-sharepoint-phase5.md)」に進んでください。

## その他のリソース

[Azure での SharePoint と SQL Server AlwaysOn 可用性グループのデプロイ](virtual-machines-workload-intranet-sharepoint-overview.md)

[Azure インフラストラクチャ サービスでホストされる SharePoint ファーム](virtual-machines-sharepoint-infrastructure-services.md)

[SharePoint と SQL Server AlwaysOn のインフォグラフィック](http://go.microsoft.com/fwlink/?LinkId=394788)

[SharePoint 2013 用の Microsoft Azure アーキテクチャ](https://technet.microsoft.com/library/dn635309.aspx)

[Azure インフラストラクチャ サービス実装ガイドライン](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure インフラストラクチャ サービスのワークロード: 高可用な基幹業務アプリケーション](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=August15_HO9-->