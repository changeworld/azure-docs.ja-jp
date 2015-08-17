<properties
	pageTitle="SharePoint イントラネット ファーム ワークロードのフェーズ 2: ドメイン コントローラーの構成"
	description="イントラネット専用 SharePoint 2013 ファームと SQL Server AlwaysOn 可用性グループを Azure インフラストラクチャ サービスにデプロイする作業の第 2 フェーズでは、2 つの Active Directory ドメイン コントローラーを作成し、構成します。"
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
	ms.date="07/21/2015"
	ms.author="josephd"/>

# SharePoint イントラネット ファーム ワークロードのフェーズ 2: ドメイン コントローラーの構成

イントラネット専用 SharePoint 2013 ファームと SQL Server AlwaysOn 可用性グループを Azure インフラストラクチャ サービスにデプロイする作業のこの第 1 フェーズでは、サービス管理で Azure 仮想ネットワーク内の 2 つのドメイン コント ローラーを構成します。これで、SharePoint ファーム リソースに対するクライアント Web 要求は、VPN または Azure ExpressRoute 接続経由でオンプレミス ネットワークに認証のトラフィックを送信する代わりに、Azure の仮想ネットワーク内で認証できるようになります。

[フェーズ 3](virtual-machines-workload-intranet-sharepoint-phase3.md) に進むには、このフェーズを完了する必要があります。全フェーズについては、「[Deploying SharePoint with SQL Server AlwaysOn Availability Groups in Azure (Azure での SharePoint と SQL Server AlwaysOn 可用性グループのデプロイ)](virtual-machines-workload-intranet-sharepoint-overview.md)」をご覧ください。

## Azure でのドメイン コントローラー仮想マシンの作成

まず、表 M の **[仮想マシン名]** 列に記入する必要があります。また、必要に応じて、**[最小サイズ]** 列で仮想マシンのサイズを変更します。

項目 | 仮想マシン名 | ギャラリー イメージ | 最小サイズ
--- | --- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (最初のドメイン コントローラー (例: DC1)) | Windows Server 2012 R2 Datacenter | A2 (Medium)
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (2 番目のドメイン コントローラー (例: DC2)) | Windows Server 2012 R2 Datacenter | A2 (Medium)
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (最初の SQL Server コンピューター (例: SQL1)) | Microsoft SQL Server 2014 Enterprise - Windows Server 2012 R2 | 	A7
4\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (2 番目の SQL Server コンピューター (例: SQL2)) | Microsoft SQL Server 2014 Enterprise - Windows Server 2012 R2 | 	A7
5\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (クラスターのマジョリティ ノード監視 (例: MN1)) | Windows Server 2012 R2 Datacenter | A1 (Small)
6\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (最初の SharePoint アプリケーション サーバー (例: APP1)) | Microsoft SharePoint Server 2013 評価版 - Windows Server 2012 R2 | A4 (ExtraLarge)
7\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (2 番目の SharePoint アプリケーション サーバー (例: APP2)) | Microsoft SharePoint Server 2013 評価版 - Windows Server 2012 R2 | A4 (ExtraLarge)
8\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (最初の SharePoint Web サーバー (例: WEB1)) | Microsoft SharePoint Server 2013 評価版 - Windows Server 2012 R2 | A4 (ExtraLarge)
9\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (2 番目の SharePoint Web サーバー (例: WEB2)) | Microsoft SharePoint Server 2013 評価版 - Windows Server 2012 R2 | A4 (ExtraLarge)

**表 M - Azure の SharePoint 2013 イントラネット ファームの仮想マシン**

仮想マシンのサイズの一覧については、「[仮想マシンのサイズ](virtual-machines-size-specs.md)」をご覧ください。

Azure PowerShell コマンドの次のブロックを使用して、2 つのドメイン コントローラーの仮想マシンを作成します。< and > の文字を削除して、各変数の値を指定します。この Azure PowerShell コマンド セットでは、次の値を使用します。

- 表 M (仮想マシン)
- 表 V (仮想ネットワーク設定)
- 表 S (サブネット)
- 表 A (可用性セット)
- 表 C (クラウド サービス)

V、S、A、C の各表は、「[フェーズ 1: Azure の構成](virtual-machines-workload-intranet-sharepoint-phase1.md)」で定義したものです。

適切な値をすべて指定したら、Azure PowerShell コマンド プロンプトでそのブロックを実行します。

	# Create the first domain controller
	$vmName="<Table M – Item 1 - Virtual machine name column>"
	$vmSize="<Table M – Item 1 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 1 – Availability set name column>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred=Get-Credential –Message "Type the name and password of the local administrator account for the first domain controller."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$subnetName="<Table S – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$vm1 | Set-AzureStaticVNetIP -IPAddress <Table V – Item 6 – Value column>

	$serviceName="<Table C – Item 1 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the second domain controller
	$vmName="<Table M – Item 2 - Virtual machine name column>"
	$vmSize="<Table M – Item 2 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred=Get-Credential –Message "Type the name and password of the local administrator account for the second domain controller."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$vm1 | Set-AzureStaticVNetIP -IPAddress <Table V – Item 7 – Value column>

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

## 最初のドメイン コントローラーの構成

ローカル管理者アカウントの資格情報を使用して、最初のドメイン コントローラー コンピューターにログオンします。

### <a id="logon"></a>リモート デスクトップ接続を使用して、仮想マシンにログオンするには

1.	Azure ポータルで、左パネルの **[仮想マシン]** をクリックします。
2.	VM に接続するには、VM 名の横の **[状態]** 列で **[実行中]** をクリックします。
3.	ページの下部にあるコマンド バーで、**[接続]** をクリックします。
4.	.rdp ファイルを取得することが通知されます。**[OK]** をクリックします。
5.	ブラウザーのダイアログが表示され、"manage.windowsazure.com から ComputerName.rdp を開くか、または保存しますか?" というメッセージが表示されます。 **[開く]** をクリックします。
6.	**[リモート デスクトップ接続]** ダイアログで、**[接続]** をクリックします。
7.	**[Windows セキュリティ]** ダイアログで、**[別のアカウントを使用]** をクリックします。
8.	**[ユーザー名]** に、VM の名前と、VM で作成されたローカル管理者アカウント (ローカル コンピューター アカウント) のユーザー名を入力します。書式 *ComputerName*\*LocalAdministratorAccountName* を使用します。
9.	**[パスワード]** に、ローカル管理者アカウントのパスワードを入力します。
10.	**[OK]** をクリックします。
11.	**[リモート デスクトップ接続]** ダイアログで、**[はい]** をクリックします。新しいマシンのデスクトップがリモート デスクトップ セッション ウィンドウに表示されます。

次に、最初のドメイン コントローラーにデータ ディスクを追加する必要があります。

### <a id="datadisk"></a>空のディスクを初期化するには

1.	サーバー マネージャーの左ウィンドウで、**[ファイル サービスと記憶域サービス]** をクリックし、**[ディスク]** をクリックします。
2.	コンテンツ ウィンドウで、**[ディスク]** グループの (**[パーティション]** が **[不明]** に設定されている) **[ディスク 2]** をクリックします。
3.	**[タスク]** をクリックし、**[ボリューム]** をクリックします。
4.	新しいボリューム ウィザードの **[開始する前に]** ページで **[次へ]** をクリックします。
5.	**[サーバーとディスクの選択]** ページで、**[ディスク 2]** をクリックし、**[次へ]** をクリックします。メッセージが表示されたら、**[OK]** をクリックします。
6.	**[ボリュームのサイズの指定]** ページで、**[次へ]** をクリックします。
7.	**[ドライブ文字またはフォルダーへの割り当て]** ページで、**[次へ]** をクリックします。
8.	**[ファイル システム形式の選択]** ページで、**[次へ]** をクリックします。
9.	**[選択内容の確認]** ページで、**[作成]** をクリックします。
10.	初期化が完了したら、**[閉じる]** をクリックします。

次に、最初のドメイン コントローラーから組織のネットワーク上の場所への接続をテストします。

### <a id="testconn"></a>接続をテストするには

1.	デスクトップで Windows PowerShell コマンド プロンプトを開きます。
2.	組織のネットワーク上のリソースの名前と IP アドレスに対して **ping** コマンドを実行します。

この手順により、DNS 名前解決が正しく機能していること (オンプレミス DNS サーバーで仮想マシンが正しく構成されていること) と、クロスプレミス仮想ネットワーク間でパケットを送受信できることが確認されます。

次に、最初のドメイン コントローラーの Windows PowerShell コマンド プロンプトで、次のコマンドを実行します。

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

コンピューターが再起動します。

## 2 番目のドメイン コントローラーの構成

ローカル管理者アカウントの資格情報を使用して、2 番目のドメイン コントローラー コンピューターにログオンします。手順については、「[リモート デスクトップ接続を使用した仮想マシンへのログオン](#logon)」をご覧ください。

次に、2 番目のドメイン コントローラーにデータ ディスクを追加する必要があります。「[空のディスクを初期化するには](#datadisk)」の手順をご覧ください。

次に、2 番目のドメイン コントローラーから組織のネットワーク上の場所への接続をテストします。「[接続をテストするには](#testconn)」の手順をご覧ください。この手順を実行して、DNS 名前解決が正しく機能していること (オンプレミス DNS サーバーで仮想マシンが正しく構成されていること) と、クロスプレミス仮想ネットワーク間でパケットを送受信できることを確認します。

次に、2 番目のドメイン コントローラーの Windows PowerShell コマンド プロンプトで、次のコマンドを実行します。

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

コンピューターが再起動します。

## SharePoint ファームのアカウントとアクセス許可の構成

SharePoint ファームでは、次のユーザー アカウントが必要です。

- sp\_farm: SharePoint ファームを管理するためのユーザー アカウント。
- sp\_farm\_db: SQL Server インスタンスに対する sysadmin 権限を持つユーザー アカウント。
- sp\_install: ロールと機能をインストールするために必要なドメイン管理権限を持つユーザー アカウント。
- sqlservice: SQL Server インスタンスを実行できるユーザー アカウント。

次に、ドメイン コントローラーがメンバーであるドメインのドメイン管理者アカウントを使用して、任意のコンピューターにログオンします。管理者レベルの Windows PowerShell コマンド プロンプトを開き、次のコマンドを 1 つずつ実行します。

	New-ADUser -SamAccountName sp_farm -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_farm" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

	New-ADUser -SamAccountName sp_farm_db -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_farm_db" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

	New-ADUser -SamAccountName sp_install -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_install" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

	New-	ADUser -SamAccountName sqlservice -AccountPassword (read-host "Set user password" -assecurestring) -name "sqlservice" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

コマンドごとにパスワードの入力を求められます。これらのアカウント名とパスワードを記録し、安全な場所に保管しておきます。

次に、次の手順を実行して、アカウント プロパティを新しいユーザー アカウントに追加します。

1.	スタート画面で、「**Active Directory ユーザー**」と入力し、**[Active Directory ユーザーとコンピューター]** をクリックします。
2.	ツリー ウィンドウでドメインを開き、**[ユーザー]** をクリックします。
3.	コンテンツ ウィンドウで、**[sp\_install]** を右クリックし、**[グループに追加]** をクリックします。
4.	**[グループの選択]** ダイアログで、「**domain admins**」と入力し、**[OK]** を 2 回クリックします。
5.	ダイアログで **[表示]** をクリックし、[高度な機能] をクリックします。このオプションでは、すべての非表示コンテナーと、Active Directory オブジェクトのプロパティ ウィンドウ内の非表示タブを表示できます。
6.	ドメイン名を右クリックし、**[プロパティ]** をクリックします。
7.	**[プロパティ]** ダイアログで、**[セキュリティ]** タブをクリックし、**[詳細設定]** をクリックします。
8.	**[<YourDomain> のセキュリティの詳細設定]** ウィンドウで、**[追加]** をクリックします。
9.	**[<YourDomain> のアクセス許可エントリ]** ウィンドウで、**[プリンシパルの選択]** をクリックします。
10.	テキスト ボックスに「**<YourDomain>\\sp\_install**」と入力し、**[OK]** をクリックします。
11.	**[コンピューター オブジェクトの作成]** の **[許可]** を選択し、**[OK]** を 3 回クリックします。

次に、DNS サーバーとして使用する 2 つの新しいドメイン コントローラーの IP アドレスが仮想マシンに割り当てられるように、仮想ネットワークの DNS サーバーを更新します。この手順では、表 V (仮想ネットワーク設定) の値を使用します。

1.	Azure ポータルの左ウィンドウで、**[ネットワーク]** をクリックし、仮想ネットワークの名前 (テーブル V - 項目 1 - [値] 列) をクリックします。
2.	**[構成]** をクリックします。
3.	**[DNS サーバー]** で、オンプレミス ネットワークに配置されている DNS サーバーに対応するエントリを削除します。
4.	**[DNS サーバー]** で、次の 2 つの表項目のフレンドリ名と IP アドレスを持つ 2 つのエントリを追加します。
 - 表 V - 項目 6 - [値] 列
 - 表 V - 項目 7 - [値] 列
5.	下部にあるコマンド バーで、**[保存]** をクリックします。
6.	Azure ポータルの左ウィンドウで、**[仮想マシン]** をクリックし、最初のドメイン コントローラーの名前の横にある **[状態]** 列をクリックします。
7.	コマンド バーで **[再起動]** をクリックします。
8.	最初のドメイン コントローラーが起動したら、2 番目のドメイン コントローラーの名前の横にある **[状態]** 列をクリックします。
9.	コマンド バーで **[再起動]** をクリックします。2 番目のドメイン コントローラーが起動するまで待ちます。

2 つのドメイン コントローラーを再起動するのは、オンプレミス DNS サーバーで DNS サーバーとして構成されないようにするためです。これらのドメイン コントローラーは、それ自体が DNS サーバーであるため、ドメイン コントローラーに昇格されたときに、オンプレミス DNS サーバーで DNS フォワーダーとして自動的に構成されます。

次に、Azure 仮想ネットワークのサーバーでローカル ドメイン コントローラーが使用されるように、Active Directory レプリケーション サイトを作成する必要があります。sp\_install アカウントを使用してプライマリ ドメイン コントローラーにログオンし、管理者レベルの Windows PowerShell コマンド プロンプトで次のコマンドを実行します。

	$vnet="<Table V – Item 1 – Value column>"
	$vnetSpace="<Table V – Item 5 – Value column>"
	New-ADReplicationSite -Name $vnet
	New-ADReplicationSubnet –Name $vnetSpace –Site $vnet

次の図は、このフェーズが問題なく完了したときの構成を示しています。プレースホルダーにコンピューター名が示されています。

![](./media/virtual-machines-workload-intranet-sharepoint-phase2/workload-spsqlao_02.png)

## 次のステップ

このワークロードを引き続き構成するには、「[フェーズ 3: SQL Server インフラストラクチャの構成](virtual-machines-workload-intranet-sharepoint-phase3.md)」に進んでください。

## その他のリソース

[Azure での SharePoint と SQL Server AlwaysOn 可用性グループのデプロイ](virtual-machines-workload-intranet-sharepoint-overview.md)

[Azure インフラストラクチャ サービスでホストされる SharePoint ファーム](virtual-machines-sharepoint-infrastructure-services.md)

[SharePoint と SQL Server AlwaysOn のインフォグラフィック](http://go.microsoft.com/fwlink/?LinkId=394788)

[SharePoint 2013 用の Microsoft Azure アーキテクチャ](https://technet.microsoft.com/library/dn635309.aspx)

[Azure インフラストラクチャ サービス実装ガイドライン](virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=August15_HO6-->