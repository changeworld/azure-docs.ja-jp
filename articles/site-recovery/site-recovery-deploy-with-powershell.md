<properties
	pageTitle="PowerShell を使用してオンプレミスの VMM サイトと Azure 間の保護を自動化する"
	description="PowerShell を使用して Azure Site Recovery のデプロイを自動化します。"
	services="site-recovery"
	documentationCenter=""
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/08/2015"
	ms.author="lauraa"/>

#  PowerShell を使用して Azure Site Recovery をデプロイする
Windows PowerShell® はタスクベースのコマンドライン シェルとスクリプト言語であり、システム管理に特化して設計されています。Azure Site Recovery の PowerShell コマンドレットの使用は、VMM が管理する Hyper-V のサイトと Azure との間でサポートされます。

## 概要

Azure Site Recovery は、さまざまなデプロイ シナリオでの仮想マシンのレプリケーション、フェールオーバー、復旧を調整してビジネス継続性と障害復旧 (BCDR) 戦略に貢献します。デプロイ シナリオのすべての一覧については、「[Azure Site Recovery の概要](site-recovery-overview.md)」を参照してください。

この記事では、PowerShell を使用して、VMM プライベート クラウドに配置されている Hyper-V ホスト サーバー上の仮想マシンで実行されているワークロードの保護を調整し、自動化するような、Azure Site Recovery をデプロイするための一般的なタスクを自動化する方法を示します。このシナリオでは、仮想マシンはプライマリ VMM サイトから Azure に Hyper-V レプリカを使用してレプリケートされます。

ガイドには、シナリオの前提条件が含まれています。また、Site Recovery コンテナーを設定する方法、ソース VMM サーバーに Azure Site Recovery プロバイダーをインストールする方法、このコンテナーにサーバーを登録する方法、Azure ストレージ アカウントを追加する方法、Hyper-V ホスト サーバーに Azure Recovery Services エージェントをインストールする方法、保護されるすべての仮想マシンに適用される VMM クラウドの保護設定を構成する方法、およびこれらの仮想マシンの保護を有効にする方法についても説明しています。すべてが正しく動作していることを確認するために、最後にフェールオーバーをテストします。

このシナリオの設定時に問題が発生した場合は、[Azure Recovery Services フォーラム](http://go.microsoft.com/fwlink/?LinkId=313628)に質問を投稿してください。


## 開始する前に

次の前提条件を満たしていることを確認してください。
### Azure の前提条件

- [Microsoft Azure](http://azure.microsoft.com/) のアカウントが必要です。お持ちでない場合は、[無料試用版](http://aka.ms/try-azure)で作業を開始してください。また、「[Azure Site Recovery Manager の料金](http://go.microsoft.com/fwlink/?LinkId=378268)」も参照してください。
- Azure にレプリケートしたデータを格納するために Azure ストレージのアカウントが必要になります。アカウントでは geo レプリケーションを有効にする必要があります。アカウントは Azure Site Recovery サービスと同じリージョンである必要があり、同じサブスクリプションに関連付けられている必要があります。Azure ストレージのセットアップの詳細については、「[Microsoft Azure ストレージの概要](http://go.microsoft.com/fwlink/?LinkId=398704)」を参照してください。
- 保護する仮想マシンが Azure 要件に準拠していることを確認する必要があります。詳細については、[仮想マシンのサポートに関するページ](https://msdn.microsoft.com/library/azure/dn469078.aspx#BKMK_E2A)を参照してください。

### VMM の前提条件
- System Center 2012 R2 で実行される VMM サーバーが必要です。
- 保護する仮想マシンを含むすべての VMM サーバーが Azure Site Recovery プロバイダーを実行している必要があります。このプロバイダーは Azure Site Recovery のデプロイ時にインストールされます。
- 保護する VMM サーバーにクラウドが少なくとも 1 つ必要です。このクラウドには以下のものが含まれている必要があります。
	- 1 つ以上の VMM ホスト グループ。
	- 各ホスト グループ内に 1 つ以上の Hyper-V ホスト サーバーまたはクラスター。
	- ソース Hyper-V サーバー上に配置された 1 つ以上の仮想マシン。仮想マシンは世代 1 にする必要があります。
- VMM クラウドの設定について理解を深めます。
	- プライベート VMM クラウドの詳細については、[System Center 2012 R2 VMM のプライベート クラウドの新機能に関するページ](http://go.microsoft.com/fwlink/?LinkId=324952)および [VMM 2012 とクラウドに関するページ](http://go.microsoft.com/fwlink/?LinkId=324956)を参照してください。
	- [VMM クラウド ファブリックの構成に関するページ](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)を参照してください。
	- クラウド ファブリック要素の配置後、プライベート クラウドの作成について、「[VMM でのプライベート クラウドの作成](http://go.microsoft.com/fwlink/?LinkId=324953)」および「[チュートリアル: System Center 2012 SP1 VMM でのプライベート クラウドの作成](http://go.microsoft.com/fwlink/?LinkId=324954)」を参照してください。

### Hyper-V の前提条件

- ホスト Hyper-V サーバーは、少なくとも Hyper-V ロールを持つ Windows Server 2012 を実行しており、最新の更新プログラムがインストールされている必要があります。
- クラスターで Hyper-V を実行している場合に、静的 IP アドレス ベースのクラスターが存在すると、クラスター ブローカーが自動的に作成されません。クラスター ブローカーを手動で構成する必要があります。手順については、「[Hyper-V レプリカ ブローカーを構成する](http://go.microsoft.com/fwlink/?LinkId=403937)」を参照してください。
- 保護を管理するすべての Hyper-V ホスト サーバーまたはクラスターが VMM クラウドに属している必要があります。

### ネットワーク マッピングの前提条件
仮想マシンを Azure ネットワーク マッピングで保護する場合、ソース VMM サーバー上の VM ネットワークとターゲットの Azure ネットワークをマップして以下を実現します。

- 同じネットワーク上でフェールオーバーするすべてのマシンは、どの復旧計画に含まれていても、相互に接続できる。
- ターゲットの Azure ネットワークでネットワーク ゲートウェイをセットアップすると、仮想マシンを他のオンプレミスの仮想マシンに接続できる。
- ネットワーク マッピングを構成しない場合、Azure へのフェールオーバー後、同じ復旧計画でフェールオーバーする仮想マシンのみ相互に接続できる。

ネットワーク マッピングをデプロイする場合は、以下のことが必要になります。

- ソース VMM サーバー上の保護する仮想マシンが VM ネットワークに接続している。そのネットワークは、クラウドに関連付けられた論理ネットワークにリンクされている必要があります。
- レプリケートされた仮想マシンがフェールオーバー後に接続できる Azure ネットワーク。フェールオーバー時にこのネットワークを選択します。ネットワークは Azure Site Recovery サブスクリプションと同じリージョンである必要があります。
- ネットワーク マッピングについての理解を深める。
	- [VMM での論理ネットワークの構成](http://go.microsoft.com/fwlink/?LinkId=386307)
	- [VMM での VM ネットワークとゲートウェイの構成](http://go.microsoft.com/fwlink/?LinkId=386308)
	- [Azure における仮想ネットワークの構成および監視](http://go.microsoft.com/fwlink/?LinkId=402555)

###PowerShell の前提条件
Azure PowerShell を使用する準備が整っていることを確認してください。PowerShell を使用している場合は、0.8.10 以降のバージョンにアップグレードする必要があります。PowerShell の設定方法の詳細については、[Azure PowerShell のインストールと構成の方法](powershell-install-configure.md)に関するページを参照してください。PowerShell を設定して構成したら、サービスで使用可能なすべてのコマンドレットを[ここ](https://msdn.microsoft.com/library/dn850420.aspx)に表示できます。

Azure PowerShell でのパラメーター値、入力、出力の一般的な処理方法など、コマンドレットの使用に役立つヒントについては、「[Azure コマンドレットの概要](https://msdn.microsoft.com/library/azure/jj554332.aspx)」を参照してください。

## ステップ 1: サブスクリプションを設定する 

PowerShell で、次のコマンドレットを実行します。



			$UserName = "<user@live.com>"
	$Password = "<password>"
	$AzureSubscriptionName = "prod_sub1"

	$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
	$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $securePassword
	Add-AzureAccount -Credential $Cred;
	$AzureSubscription = Select-AzureSubscription -SubscriptionName $AzureSubscriptionName


"< >" 内の要素をユーザー固有の情報に置き換えます。

## ステップ 2: Site Recovery コンテナーを作成する

PowerShell で "< >" 内の要素をユーザー固有の情報に置き換え、次のコマンドを実行します。

```

	$VaultName = "<testvault123>"
	$VaultGeo  = "<Southeast Asia>"
	$OutputPathForSettingsFile = "<c:>"

```


```
	New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
	$vault = Get-AzureSiteRecoveryVault -Name $VaultName;

```

## ステップ 3: コンテナー登録キーを生成する

コンテナーの登録キーを生成します。Azure Site Recovery プロバイダーをダウンロードして VMM サーバーにインストールした後で、このキーを使用して、VMM サーバーをコンテナーに登録します。

1.	コンテナー設定ファイルを取得し、コンテキストを設定します。
	
	```
	
		$VaultName = "<testvault123>"
		$VaultGeo  = "<Southeast Asia>"
		$OutputPathForSettingsFile = "<c:>"
	
		$VaultSetingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;
	
	```
	
2.	次のコマンドを実行して、コンテナーのコンテキストを設定します。
	
	```	
		$VaultSettingFilePath = $vaultSetingsFile.FilePath 
		$VaultContext = Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath -ErrorAction Stop
```

## ステップ 4: Azure Site Recovery プロバイダーをインストールする

1.	VMM マシンで、次のコマンドを実行してディレクトリを作成します。
	
	```
	
		pushd C:\ASR\
	
	```
	
2. 次のコマンドを実行して、ダウンロードしたプロバイダーを使用して、ファイルを抽出します。
	
	```
	
		AzureSiteRecoveryProvider.exe /x:. /q
	
	```
	
3. 次のコマンドを使用してプロバイダーをインストールします。
	
	```
	
	.\SetupDr.exe /i
	
	```
	
	```
	
	$installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
	do
	{
	                $isNotInstalled = $true;
	                if(Test-Path $installationRegPath)
	                {
	                                $isNotInstalled = $false;
	                }
	}While($isNotInstalled)
	
	```
	
	インストールが完了するのを待ちます。
	
4. 次のコマンドを使用して、コンテナーにサーバーを登録します。
	
	```
	
	$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
	pushd $BinPath
	$encryptionFilePath = "C:\temp"
	.\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
	
	```
	
## ステップ 5: Azure のストレージ アカウントを作成する

Azure のストレージ アカウントを持っていない場合、次のコマンドを実行して、geo レプリケーションが有効になっているアカウントを作成します。

```

$StorageAccountName = "teststorageacc1"
$StorageAccountGeo  = "Southeast Asia"

New-AzureStorageAccount -StorageAccountName $StorageAccountName -Label $StorageAccountName -Location $StorageAccountGeo;

```

ストレージ アカウントは Azure Site Recovery サービスと同じリージョンである必要があり、同じサブスクリプションに関連付けられている必要があります。


## ステップ 6: Azure Recovery Services エージェントをインストールする

Azure ポータルから、保護する VMM クラウドに配置されている Hyper-V ホスト サーバーごとに、Azure Recovery Services エージェントをインストールします。

すべての VMM ホストで次のコマンドを実行します。

```

	marsagentinstaller.exe /q /nu

```


## ステップ 7: クラウドの保護設定を構成する

1.	次のコマンドを実行して、Azure にクラウド保護のプロファイルを作成します。
	
	```
	
	$ReplicationFrequencyInSeconds = "300";
	$ProfileResult = New-AzureSiteRecoveryProtectionProfileObject -ReplicationProvider 	HyperVReplica -RecoveryAzureSubscription $AzureSubscriptionName `
	-RecoveryAzureStorageAccount $StorageAccountName -ReplicationFrequencyInSeconds 	$ReplicationFrequencyInSeconds;
	
	```
	
2.	次のコマンドを実行して、保護コンテナーを取得します。
	
	```
	
		$PrimaryCloud = "testcloud"
		$protectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $PrimaryCloud;	
	
	```
	
3.	クラウドと保護コンテナーとのアソシエーションを開始します。
	
	```
	
		$associationJob = Start-AzureSiteRecoveryProtectionProfileAssociationJob -	ProtectionProfile $profileResult -PrimaryProtectionContainer $protectionContainer;		
	
	```
	
4.	ジョブが完了したら、次のコマンドを実行します。

			$job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
			if($job -eq $null -or $job.StateDescription -ne "Completed")
			{
				$isJobLeftForProcessing = $true;
			}
5. ジョブの処理が完了したら、次のコマンドを実行します。

		if($isJobLeftForProcessing)
			{
			Start-Sleep -Seconds 60
			}
				}While($isJobLeftForProcessing)
	
	
操作の完了を確認するには、[アクティビティを監視する](#monitor)の手順に従います。

## ステップ 8: ネットワーク マッピングを構成する
ネットワーク マッピングを開始する前に、ソース VMM サーバー上の仮想マシンが VM ネットワークに接続されていることを確認してください。さらに、1 つまたは複数の Azure 仮想ネットワークを作成します。複数の VM ネットワークを 1 つの Azure ネットワークにマップできることに注意してください。

ターゲット ネットワークに複数のサブネットがあり、そのサブネットのいずれかが、ソースの仮想マシンが配置されているサブネットと同じ名前である場合、フェールオーバー後、レプリカの仮想マシンはそのターゲット サブネットに接続することに注意してください。ターゲットのサブネットで名前が一致するものがなければ、仮想マシンはネットワークの最初のサブネットに接続されます。

最初のコマンドは、現在の Azure Site Recovery コンテナーのサーバーを取得します。このコマンドは、$Servers 配列変数に、Microsoft Azure Site Recovery のサーバーを格納します。



	$Servers = Get-AzureSiteRecoveryServer


2 番目のコマンドは、$Servers 配列内の最初のサーバーの Site Recovery ネットワークを取得します。コマンドは、$Networks 変数に、ネットワークを格納します。

```

	$Networks = Get-AzureSiteRecoveryNetwork -Server $Servers[0]

```

3 番目のコマンドは、Get-AzureSubscription コマンドレットを使用して Azure のサブスクリプションを取得し、$Subscriptions 変数にその値を格納します。

```

	$Subscriptions = Get-AzureSubscription

```

4 番目のコマンドは、Get AzureVNetSite コマンドレットを使用して Azure の仮想ネットワークを取得し、$AzureVmNetworks 変数にその値を格納します。

```

	$AzureVmNetworks = Get-AzureVNetSite

```

最後のコマンドレットは、プライマリ ネットワークと Azure の仮想マシンのネットワーク間のマッピングを作成します。このコマンドレットは $Networks の最初の要素として、プライマリ ネットワークを指定します。このコマンドレットは、仮想マシンのネットワークを、その ID を使用して $AzureVmNetworks の最初の要素として指定します。このコマンドは、Azure サブスクリプションの ID を含みます。

```

PS C:> New-AzureSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureSubscriptionId $Subscriptions[0].SubscriptionId -AzureVMNetworkId $AzureVmNetworks[0].Id

```

## ステップ 9: 仮想マシンの保護を有効化する

サーバー、クラウド、およびネットワークを正しく構成した後で、クラウド内の仮想マシンの保護を有効にすることができます。以下の点に注意してください。

仮想マシンは Azure 要件を満たしている必要があります。計画ガイドの「<a href="http://go.microsoft.com/fwlink/?LinkId=402602">前提条件とサポート</a>」で確認してください。

オペレーティング システムとオペレーティング システム ディスクの保護を有効にするには、仮想マシンにプロパティを設定する必要があります。仮想マシン テンプレートを使用して VMM 内で仮想マシンを作成する際に、プロパティを設定できます。また、仮想マシンのプロパティの **[全般]** タブと **[ハードウェア構成]** タブで既存の仮想マシンに対してこれらのプロパティを設定することもできます。VMM でこれらのプロパティを設定していない場合は、Azure Site Recovery ポータルで構成できます。


	
1.	保護を有効にするには、次のコマンドを実行して保護コンテナーを取得します。
		
	```
	
	$ProtectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $CloudName
	
	```
	
2. 次のコマンドを実行して、保護エンティティ (VM) を取得します。
		
	```
	
	$protectionEntity = Get-AzureSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer
		
		```
			
3. 次のコマンドを実行して VM の DR を有効にします。

	
	$jobResult = Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Protection Enable -Force
	

## デプロイのテスト

展開をテストするために、1 台の仮想マシンに対するテスト フェールオーバーを実行することや、複数の仮想マシンで構成される復旧計画を作成して、その計画のテスト フェールオーバーを実行することができます。テスト フェールオーバーは、孤立したネットワークでフェールオーバーと復旧のシミュレーションを実行します。以下の点に注意してください。

- フェールオーバー後に Azure でリモート デスクトップを使用して仮想マシンに接続する場合は、テストのフェールオーバーを実行する前に、仮想マシンのリモート デスクトップ接続を有効にします。
- フェールオーバー後、パブリック IP アドレスを使用して、Azure の仮想マシンにリモート デスクトップで接続します。この接続では、パブリック アドレスを使用する仮想マシンの接続を妨げるドメイン ポリシーを使用していないことを確認してください。

操作の完了を確認するには、[アクティビティを監視する](#monitor)の手順に従います。

### 復旧計画の作成

1. 次のデータを使用して .xml ファイルを復旧計画のテンプレートとして作成し、「C:\\RPTemplatePath.xml」として保存します。
2. RecoveryPlan ノードの Id、Name、PrimaryServerId、SecondaryServerId を変更します。
3. ProtectionEntity ノードの PrimaryProtectionEntityId (VMM の vmid) を変更します。
4. ProtectionEntity ノードを追加することでさらに VM を追加することができます。
	
	```
	
	<#
	<?xml version="1.0" encoding="utf-16"?>
	<RecoveryPlan Id="d0323b26-5be2-471b-addc-0a8742796610" Name="rp-test" 	PrimaryServerId="9350a530-d5af-435b-9f2b-b941b5d9fcd5" 	SecondaryServerId="21a9403c-6ec1-44f2-b744-b4e50b792387" Description="" 	Version="V2014_07">
	  <Actions />
	  <ActionGroups>
	    <ShutdownAllActionGroup Id="ShutdownAllActionGroup">
	      <PreActionSequence />
	      <PostActionSequence />
	    </ShutdownAllActionGroup>
	    <FailoverAllActionGroup Id="FailoverAllActionGroup">
	      <PreActionSequence />
	      <PostActionSequence />
	    </FailoverAllActionGroup>
	    <BootActionGroup Id="DefaultActionGroup">
	      <PreActionSequence />
	      <PostActionSequence />
	      <ProtectionEntity PrimaryProtectionEntityId="d4c8ce92-a613-4c63-9b03-	cf163cc36ef8" />
	    </BootActionGroup>
	  </ActionGroups>
	  <ActionGroupSequence>
	    <ActionGroup Id="ShutdownAllActionGroup" ActionId="ShutdownAllActionGroup" 	Before="FailoverAllActionGroup" />
	    <ActionGroup Id="FailoverAllActionGroup" ActionId="FailoverAllActionGroup" 	After="ShutdownAllActionGroup" Before="DefaultActionGroup" />
	    <ActionGroup Id="DefaultActionGroup" ActionId="DefaultActionGroup" After="FailoverAllActionGroup"/>
	  </ActionGroupSequence>
	</RecoveryPlan>
	#>
	
	```
	
4. テンプレートのデータを入力します。
	
	```
	
	$TemplatePath = "C:\RPTemplatePath.xml";
	
	```
	
5. RecoveryPlan を作成します。
	
	```
	
		$RPCreationJob = New-AzureSiteRecoveryRecoveryPlan -File $TemplatePath -WaitForCompletion;
	
	```
	
### テスト フェールオーバーの実行

1. 次のコマンドを実行して RecoveryPlan オブジェクトを取得します。
	
	```
	
		$RPObject = Get-AzureSiteRecoveryRecoveryPlan -Name $RPName;
	
	```
	
2. 次のコマンドを実行してテスト フェールオーバーを開始します。
	
	```
	
	$jobIDResult = Start-AzureSiteRecoveryTestFailoverJob -RecoveryPlan $RPObject -Direction PrimaryToRecovery;
	
	```
	
## <a name=monitor></a> アクティビティを監視する

アクティビティを監視するには、次のコマンドを使用します。ジョブの処理が終了するまで待機する必要があります。

```

Do
{
                $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
                Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
                if($job -eq $null -or $job.StateDescription -ne "Completed")
                {
                                $isJobLeftForProcessing = $true;
                }

```

```

if($isJobLeftForProcessing)
                {
                                Start-Sleep -Seconds 60
                }
}While($isJobLeftForProcessing)

```


##<a id="next" name="next" href="#next"></a>次のステップ
<UL>

<LI>Azure Site Recovery の PowerShell コマンドレットの詳細については、<a href="https://msdn.microsoft.com/library/dn850420.aspx">この記事</a>を参照してください。

<LI>完全な運用環境で Azure Site Recovery の計画とデプロイを実行するには、「<a href="http://go.microsoft.com/fwlink/?LinkId=321294">Azure Site Recovery 計画ガイド</a>」と「<a href="http://go.microsoft.com/fwlink/?LinkId=321295">Azure Site Recovery デプロイ ガイド</a>」を参照してください。</LI>

<LI>ご不明な点やご質問などがありましたら、<a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services フォーラム</a>にアクセスしてください。</LI> </UL>

<!---HONumber=July15_HO2-->