<properties
	pageTitle="PowerShell と Microsoft Azure リソース マネージャーを使用して異なる VMM サイト間で VM を保護します。"
	description="PowerShell と Azure リソース マネージャーを使用して、オンプレミスの VMM サイトと Azure 間で保護を自動化します。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="08/26/2015"
	ms.author="raynew"/>
	

#  VMM サイト間での PowerShell と Azure リソース マネージャーの使用


## 概要

Azure Site Recovery は、さまざまなデプロイ シナリオでの仮想マシンのレプリケーション、フェールオーバー、復旧を調整してビジネス継続性と障害復旧 (BCDR) 戦略に貢献します。デプロイ シナリオのすべての一覧については、「[Azure Site Recovery の概要](site-recovery-overview.md)」を参照してください。

Azure PowerShell は、Windows PowerShell から Azure を管理するコマンドレットを提供するモジュールです。これを 2 種類のモジュール (Azure Profile モジュールまたは Azure リソース マネージャー (ARM) モジュール) と共に使用できます。

この記事では、Windows PowerShell® を ARM と共に使用して Azure Site Recovery をデプロイし、2 つの VMM サイト間で仮想マシンの保護を構成して調整する方法について説明します。プライマリ サイトの VMM プライベート クラウドにある Hyper-V ホスト サーバーで実行中の仮想マシンがレプリケートされ、Hyper-V レプリカを使用してセカンダリ VMM サイトにフェールオーバーします。

この記事の内容を使用するには PowerShell の専門家である必要はありませんが、モジュール、コマンドレット、セッションなどの基本的な概念を理解していることを前提としています。Windows PowerShell の詳細については、「[Windows PowerShell の概要](http://technet.microsoft.com/library/hh857337.aspx)」と「[Azure リソース マネージャーでの Windows PowerShell の使用](powershell-azure-resource-manager.md)」をご覧ください。

この記事には、シナリオの前提条件が含まれています。また、Azure PowerShell を設定して Site Recovery を使用する方法、Site Recovery 資格情報コンテナーを作成する方法、VMM サーバーに Azure Site Recovery プロバイダーをインストールし、これらを資格情報コンテナーに登録する方法、保護されるすべての仮想マシンに適用される VMM クラウドの保護設定を構成する方法、これらの仮想マシンの保護を有効にする方法についても説明しています。すべてが正しく動作していることを確認するために、最後にフェールオーバーをテストします。


## 開始する前に

次の前提条件を満たしていることを確認してください。

- [Microsoft Azure](http://azure.microsoft.com/) のアカウントが必要です。[Microsoft Azure](http://azure.microsoft.com/) のアカウントが必要です。アカウントがなくても、[無料試用版](pricing/free-trial/)を使用できます。また、「[Azure Site Recovery Manager の料金](http://azure.microsoft.com/pricing/details/site-recovery/)」も参照してください。
- System Center 2012 R2 で実行されているプライマリ サイトとセカンダリ サイトには、VMM サーバーが必要です。
- 各 VMM サーバーには、次を備えた少なくとも 1 つのクラウドが必要です。
	- 1 つ以上の VMM ホスト グループ。
	- 各ホスト グループ内に 1 つ以上の Hyper-V ホスト サーバーまたはクラスター。
	- ソース Hyper-V サーバー上に配置された 1 つ以上の仮想マシン。
	- VMM クラウドの設定について理解を深めます。

		- [What’s New in Private Cloud with System Center 2012 R2 VMM (System Center 2012 R2 VMM のプライベート クラウドの新機能)](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=dfgvHAmYryA) および [VMM 2012 and the clouds (VMM 2012 とクラウド)](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html)
		- [VMM クラウド ファブリックの構成](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
		- [VMM でのプライベート クラウドの作成](https://technet.microsoft.com/library/jj860425.aspx)および[Walkthrough: Creating private clouds with System Center 2012 SP1 VMM (チュートリアル: System Center 2012 SP1 VMM を使用したプライベート クラウドの作成)](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)
- 1 つ以上のホスト Hyper-V サーバーは、少なくとも Hyper-V ロールを持つ Windows Server 2012 を実行しており、最新の更新プログラムがインストールされている必要があります。VMM クラウドには、サーバーまたはクラスターが含まれている必要があります。
- クラスターで Hyper-V を実行している場合に、静的 IP アドレス ベースのクラスターが存在すると、クラスター ブローカーが自動的に作成されません。クラスター ブローカーを手動で構成する必要があります。手順については、「[Hyper-V レプリカ ブローカーを構成する](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)」を参照してください。

	- Azure PowerShell が必要です。Azure PowerShell バージョン 0.9.6 以降を実行していることを確認します。「[Azure PowerShell のインストールおよび構成方法](powershell-install-configure.md)」をご覧ください。 
	- Azure PowerShell をインストールすると、カスタマイズされたコンソールがインストールされます。このコンソールまたは Windows PowerShell ISE などの他のホスト プログラムから、PowerShell コマンドを実行できます。

## ステップ 1: PowerShell を設定する


1. PowerShell コンソールを開き、次のコマンドを実行して ARM モジュールに切り替えます。

    `Switch-AzureMode AzureResourceManager`

3. 次のコマンドを実行して、PowerShell セッションに Azure アカウントを追加します。このコマンドレットは、自分のアカウントのログイン資格情報をユーザーに求めます。

    `Add-AzureAccount`

	テナントの代理として作業している CSP パートナーの場合は、Azure アカウントを追加するときに顧客をテナントとして指定する必要があります。

    `Add-AzureAccount-Tenant "customer"`

5. 1 つのアカウントで複数のサブスクリプションを持つことができるため、アカウントで使用するサブスクリプションを関連付ける必要があります。

    `Select-AzureSubscription -SubscriptionName $SubscriptionName`

6. サブスクリプションで初めて Site Recovery コマンドレットを使用する場合は、Site Recovery の Azure プロバイダーを登録する必要があります。

    `Register-AzureProvider -ProviderNamespace Microsoft.SiteRecovery`

## ステップ 2: Site Recovery 資格情報コンテナーを設定する

1. Site Recovery 資格情報コンテナーをまだ取得していない場合は、[New-AzureSiteRecoveryVault](https://msdn.microsoft.com/library/azure/dn954225.aspx) コマンドレットを実行して 1 つのコンテナーを作成する必要があります。

    `New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
    $vault = Get-AzureSiteRecoveryVault -Name $VaultName;`

## ステップ 3: コンテナー登録キーを生成する

1. [Get AzureSiteRecoveryVaultSettingsFile](https://msdn.microsoft.com/library/azure/dn850404.aspx) コマンドレットを実行して資格情報コンテナー登録キーを取得します。資格情報コンテナーに VMM サーバーを登録するには、このキーが必要です。

    `$VaultSettingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;`

## ステップ 4: Azure Site Recovery プロバイダーをインストールする

1. Site Recovery 資格情報コンテナーの [クイック スタート] ページから、プロバイダーのインストール ファイルをダウンロードします。
2. 各 VMM サーバーで、次のコマンドを使用してフォルダーを作成します。

    `pushd C:\ASR`

3. 次のコマンドを実行して、ダウンロードしたプロバイダーからファイルを展開します。

    `AzureSiteRecoveryProvider.exe /x:. /q`

4. 次のコマンドを実行して、プロバイダーをインストールします。

    `.\SetupDr.exe /i` `$installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
	do
	{
                $isNotInstalled = $true;
                if(Test-Path $installationRegPath)
                {
                                $isNotInstalled = $false;
                }
	}While($isNotInstalled)`

5. インストールの完了を待って、資格情報コンテナーにサーバーを登録します。

    `$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
	pushd $BinPath
	$encryptionFilePath = "C:\temp"
	.\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice`

## ステップ 5: 資格情報コンテナーのコンテキストを設定する

1. Get AzureSiteRecoveryVault コマンドレットを実行して、特定の資格情報コンテナーですべてのコマンドが実行されていることを確認します。

    `$Vault = Get-AzureSiteRecoveryVault -ResouceGroupName $ResourceGroupName | where { $_.Name -eq $VaultName}`

2. 資格情報コンテナーの設定をダウンロードします。

    `$VaultSettingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Vault $Vault -Path $OutputPathForSettingsFile`

3. 資格情報コンテナーでコマンドレットが実行されていることを確認するには、次のコマンドを実行します。

    `Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSetingsFile.FilePath`

## ステップ 3: クラウドの保護設定を構成する

VMM サーバーが資格情報コンテナーに登録されたら、登録済みの VMM サーバーのクラウドに配置された Hyper-V ホストのすべての仮想マシンに適用されるクラウドの保護設定を構成できます。

1. プライマリ クラウドとセカンダリ クラウドの資格情報コンテナーにコンテナーを作成します。

    `$PrimaryContainer = Get-AzureSiteRecoveryProtectionContainer -FriendlyName  $PrimaryCloudName` `$$RecoveryContainer = Get-AzureSiteRecoveryProtectionContainer -FriendlyName  $RecoveryCloudName`

2. 保護設定を構成してクラウドに適用します。

    `New-AzureSiteRecoveryProtectionProfile -Name $ProtectionProfileName -ReplicationProvider HyperVReplica -ReplicationMethod Online -ReplicationFrequencyInSeconds 30 -RecoveryPoints 1 -ApplicationConsistentSnapshotFrequencyInHours 0 -ReplicationPort 8083 -Authentication Kerberos -AllowReplicaDeletion`

3. クラウドのコンテナーをクラウドの保護設定に関連付けるジョブを開始します。

    `Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $ProtectionProfile -PrimaryProtectionContainer $PrimaryContainer -RecoveryProtectionContainer $RecoveryContainer`


## ステップ 4: VM 保護を有効にする

VMM クラウドで VM の保護を有効にします。

1. 保護する VM を取得します。

    `$VM = Get-AzureSiteRecoveryProtectionEntity -ProtectionContainer $PrimaryContainer -FriendlyName $VMName `

2. VM の保護を有効にします。

    `Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $VM -Protection Enable`


## ステップ 5: テスト フェールオーバーを実行する

1.	フェールオーバーする VM を選択します。

    `$VM = Get-AzureSiteRecoveryProtectionEntity -ProtectionContainer $PrimaryContainer -FriendlyName  $VMName`

2. テスト フェールオーバー ジョブを実行します。

    `$ currentJob = Start-AzureSiteRecoveryTestFailoverJob -ProtectionEntity $VM -Direction PrimaryToRecovery`

3. フェールオーバーした VM がセカンダリ サイトに表示されることを確認し、フェールオーバーを完了します。

    `Resume-AzureSiteRecoveryJob -Id $currentJob.Name`


## 次のステップ

このシナリオに関するご質問やご意見については、[Site Recovery フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr/)をご覧ください。

<!---HONumber=Oct15_HO3-->