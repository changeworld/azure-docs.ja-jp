<properties 
	pageTitle="SQL Server および Azure Site Recovery を使用した障害復旧" 
	description="Azure Site Recovery は、セカンダリ オンプレミス サイトまたは Azure への SQL Server のレプリケーション、フェールオーバー、および復旧を調整します。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="raynew"/>


# SQL Server および Azure Site Recovery を使用した障害復旧 

Site Recovery は、仮想マシンと物理サーバーのレプリケーション、フェールオーバー、および復旧を調整してビジネス継続性と障害復旧 (BCDR) 戦略を支援する Azure サービスです。Site Recovery では、いくつかのレプリケーション メカニズムをサポートし、常にマシンを Azure またはセカンダリ データセンターで保護し、Azure またはセカンダリ データセンターにレプリケートおよびフェールオーバーします。すべてのデプロイ シナリオの概要については、「[Azure Site Recovery の概要](site-recovery-overview.md)」を参照してください。

 この記事では、SQL Server の BCDR テクノロジおよび Site Recovery の組み合わせを使用してアプリケーションの SQL Server バックエンドを保護する方法について説明します。この記事で説明するシナリオをデプロイするには、SQL Server の BCDR 機能 (フェールオーバー クラスタリング、AlwaysOn 可用性グループ、データベース ミラーリング、ログ配布) および Site Recovery を十分に理解する必要があります。



## 概要

多くのワークロードは、SQL Server を基盤として使用します。SharePoint、Dynamics、および SAP などのアプリケーションは、データ サービスを実装するのに SQL Server を使用します。SQL Server 可用性グループなどの SQL Server の高可用性と障害復旧機能は、SQL Server データベースを保護し、復旧します。

Site Recovery は、Hyper-V 仮想マシン、VMware 仮想マシン、または物理サーバーとして実行される SQL Server を保護できます。

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td colspan = "2"><b>Hyper-V</b></td>
		<td colspan = "2"><b>VMware</b></td>
		<td colspan = "2"><b>物理サーバー</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>オンプレミス間</td>
		<td>オンプレミスと Azure 間</td>
		<td>オンプレミス間</td>
		<td>オンプレミスと Azure 間</td>
		<td>オンプレミス間</td>
		<td>オンプレミスと Azure 間</td>
    </tr>
	<tr align="left" valign="top">
		<td>あり</td>
		<td>あり</td>
		<td>あり</td>
		<td>近日対応予定</td>
		<td>あり</td>
		<td>近日対応予定</td>
    </tr>
    </tbody>
    </table>

## サポートと統合

Site Recovery は、次の表に要約したネイティブの SQL Server の BCDR テクノロジと統合して、障害復旧ソリューションを提供できます。

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>機能</b></td>
		<td><b>詳細</b></td>
		<td><b>SQL Server のバージョン</b></td>
    </tr>
    </tr><tr align="left" valign="top">
		<td><b>AlwaysOn 可用性グループ</b></td>
		<td><p>SQL Server の複数のスタンドアロン インスタンスであり、複数のノードを持つフェールオーバー クラスターでそれぞれが実行されます。</p> <p>データベースは、SQL Server インスタンス上でコピー (ミラー化) が可能なフェールオーバー グループにグループ化できるため、共有記憶域は必要ありません</p> <p>プライマリ サイトと 1 つまたは複数のセカンダリ サイトの間で障害復旧を実現します。2 つのノードをシェアード ナッシング クラスターに設定できます。このクラスターでは、同期レプリケーションと自動フェールオーバーを設定した可用性グループに SQL Server データベースを構成しておきます。</p></td>
		<td>SQL Server 2014/2012 Enterprise エディション</td>
    </tr>
	<tr align="left" valign="top">
		<td><b>フェールオーバー クラスタリング (AlwaysOn FCI)</b></td>
		<td><p>SQL Server は、Windows のフェールオーバー クラスタリングを利用して、オンプレミスの SQL Server ワークロードの高可用性を実現しています。</p><p>共有ディスクを備えた、SQL Server のインスタンスを実行しているノードは、フェールオーバー クラスター内に構成されます。インスタンスがダウンした場合、クラスターは別のクラスターにフェールオーバーします。</p> <p>クラスターは、共有記憶域のエラーまたは障害からは保護しません。共有ディスクは、iSCSI、ファイバー チャネル、または共有 VHDX を使用して実装できます。</p></td>
		<td><p>SQL Server Enterprise エディション</p> <p>SQL Server Standard エディション (2 つのノードのみに制限)</p></td>
	<tr align="left" valign="top">
		<td><b>高い安全性モードでのデータベース ミラーリング</b></td>
		<td>1 つのセカンダリ コピーで 1 つのデータベースを保護します。高い安全性 (同期) と高パフォーマンス (非同期) の両方のレプリケーション モードで使用できます。フェールオーバー クラスターは必要はありません。</td>
		<td><p>SQL Server 2008 R2</p><p>SQL Server Enterprise のすべてのエディション</p></td>
    </tr>
    </tr>
	<tr align="left" valign="top">
		<td><b>スタンドアロンの SQL Server</b></td>
		<td>SQL Server とデータベースは、単一のサーバー (物理または仮想) でホストされます。サーバーが仮想である場合、ホスト クラスタリングが高可用性のために使用されます。ゲストレベルの高可用性はありません。</td>
		<td>Enterprise または Standard エディション</td>
 
    </tbody>
    </table>

次の表は、Site Recovery デプロイに SQL Server の BCDR テクノロジを統合するための推奨事項を示しています。

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>バージョン</b></td>
		<td><b>エディション</b></td>
		<td><b>デプロイ</b></td>
		<td><b>オンプレミス間</b></td>
		<td><b>オンプレミスと Azure 間</b>&lt;/td
    </tr>
    <tr align="left" valign="top">
		<td rowspan = "3">SQL Server 2014 または 2012</td>
		<td rowspan = "2">Enterprise</td>
		<td>フェールオーバー クラスター インスタンス</td>
		<td>AlwaysOn 可用性グループ</td>
		<td>AlwaysOn 可用性グループ</td>
    </tr>
	<tr align="left" valign="top">
		<td>高可用性のための AlwaysOn 可用性グループ</td>
		<td>AlwaysOn 可用性グループ</td>
		<td>AlwaysOn 可用性グループ</td>
    </tr>
	<tr align="left" valign="top">
		<td>Standard</td>
		<td>フェールオーバー クラスター インスタンス</td>
		<td>ローカルのミラーを使用した Site Recovery レプリケーション</td>
		<td>ローカルのミラーを使用した Site Recovery レプリケーション</td>
    </tr>
	<tr align="left" valign="top">
		<td>Enterprise または Standard</td>
		<td>スタンドアロン</td>
		<td>ローカルのミラーを使用した Site Recovery レプリケーション</td>
		<td>ローカルのミラーを使用した Site Recovery レプリケーション</td>
    </tr>
	<tr align="left" valign="top">
		<td>SQL Server 2008 R2</td><td>Enterprise または Standard</td>
		<td>スタンドアロン</td>
		<td>ローカルのミラーを使用した Site Recovery レプリケーション</td>
		<td>ローカルのミラーを使用した Site Recovery レプリケーション</td>
    </tr>
    </tbody>
    </table>


## デプロイの前提条件

ここでは、開始する前に必要な項目を示します。


- サポートされている SQL Server のバージョンを実行しているオンプレミスの SQL Server デプロイ通常、SQL Server には Active Directory も必要です。
- デプロイするシナリオの前提条件。前提条件は、各デプロイの記事に記載されています。これらは、「[Site Recovery の概要](site-recovery-overview.md)」で一覧となっていて、リンクされています。
- Azure で復旧を設定する場合は、[Azure 仮想マシン準備状態評価](http://www.microsoft.com/download/details.aspx?id=40898)ツールを SQL Server 仮想マシン上で実行し、SQL Server 仮想マシンに Azure および Site Recovery との互換性があることを確認する必要があります。

## 保護の設定

次の 2 つの手順を実行する必要があります。

- Active Directory のセットアップ
- SQL Server クラスターまたはスタンドアロン サーバーに対する保護の設定

### Active Directory のセットアップ

SQL Server を正常に実行するには、セカンダリ復旧サイトに Active Directory が必要です。これには、次の 2 つのオプションがあります。

- **小規模企業**—アプリケーションの数が少なく、オンプレミスのサイト用のドメイン コントローラーが 1 つしかない場合にサイト全体をフェールオーバーするには、Site Recovery レプリケーションを使用して、セカンダリ データセンターまたは Azure にドメイン コント ローラーをレプリケートすることをお勧めします。

- **中規模および大企業**—アプリケーションの数が多く、Active Directory フォレストを実行している場合にアプリケーションまたはワークロード単位でフェールオーバーするには、追加のドメイン コントローラーをセカンダリ データセンターまたは Azure にセットアップすることをお勧めします。AlwaysOn 可用性グループを使用してリモート サイトに復旧する場合は、もう 1 つ追加のドメイン コント ローラーをセカンダリ サイトまたは Azure にセットアップして、復旧した SQL Server インスタンスに対して使用することをお勧めします。

このドキュメントに記載された手順は、2 番目の拠点でドメイン コントローラーが使用できることを前提としています。

### スタンドアロンの SQL Server に対する保護の設定


この構成では、Site Recovery レプリケーションを使用して、SQL Server マシンを保護することをお勧めします。正確な手順は、SQL Server が仮想マシンまたは物理サーバーのどちらとして設定されているか、および Azure またはセカンダリ オンプレミス サイトのどちらにレプリケートするかによって異なります。可能なデプロイ シナリオに関する手順については、「[Site Recovery の概要](site-recovery-overview.md)」を参照してください。


### SQL Server クラスター (2012 または 2014 Enterprise) の保護の設定

SQL Server が可用性グループまたはフェールオーバー クラスター インスタンスを使用して高可用性を実現している場合は、復旧サイトでも可用性グループを使用することをお勧めします。このガイダンスは、分散トランザクションを使用しないアプリケーション向けであることに注意してください。

##### オンプレミス間

1. 可用性グループに[データベースを構成](https://msdn.microsoft.com/library/hh213078.aspx)します。
2. セカンダリ サイトに、新しい仮想ネットワークを作成します。
3. 新しい仮想ネットワークとプライマリ サイトの間に、サイト間 VPN を設定します。
4. 復旧サイトに仮想マシンを作成し、SQL Server を仮想マシンにインストールします。
5. 新しい SQL Server 仮想マシンに、既存の AlwaysOn 可用性グループを拡張します。この SQL Server インスタンスは、非同期レプリカ コピーとして構成します。
6. 可用性グループ リスナーを作成するか、または既存のリスナーを更新して、非同期のレプリカ仮想マシンを含めます。
7. アプリケーション ファームがリスナーを使用してセットアップされていることを確認します。データベース サーバー名を使用してセットアップされている場合は、リスナーを使用するように更新して、フェールオーバー後に再構成する必要がないようにしてください。

#### オンプレミスと Azure 間

各可用性グループには専用のリスナーが必要で、各リスナーを構成するには個別のクラウド サービスが必要となるため、Azure にレプリケートする場合に複数の可用性グループを構成することは困難です。すべてのデータベースを含む可用性グループを 1 つ構成することをお勧めします。

1. Azure 仮想ネットワークを作成します。
2. オンプレミスのサイトとこのネットワークの間に、サイト間 VPN を設定します。
3. ネットワーク内に新しい SQL Server Azure 仮想マシンを作成し、非同期の可用性グループのレプリカとして構成します。Azure へのフェールオーバーの後に SQL Server 層に対して高可用性が必要な場合は、Azure に 2 つの非同期レプリカのコピーを構成します。
4. 仮想ネットワークでドメイン コントローラーのレプリカを設定します。
5. 仮想マシンの拡張機能が、仮想マシン上で有効であるかどうかを確認します。これは、復旧計画で SQL Server 固有のスクリプトをプッシュするために必要です。
6. Azure の内部ロード バランサーを使用して、可用性グループ用の SQL Server リスナーを構成します。
7. リスナーを使用してデータベース層にアクセスするには、アプリケーション層を構成します。分散トランザクションを使用するアプリケーションの場合は、SAN レプリケーションまたは VMware サイト間レプリケーションと共に Site Recovery を使用することをお勧めします。

### SQL Server クラスター (Standard または 2008 R2) の保護の設定

SQL Server Standard エディション または SQL Server 2008 R2 を実行するクラスターでは、Site Recovery レプリケーションを使用して SQL Server を保護することをお勧めします。

#### オンプレミス間

- Hyper-V 環境では、アプリケーションが分散トランザクションを使用している場合は、[SAN レプリケーションを使用した Site Recovery](site-recovery-vmm-san.md) をデプロイすることをお勧めします。

- VMware 環境では、[VMware 間](site-recovery-vmware-to-vmware.md) の保護をデプロイすることをお勧めします。

#### オンプレミスと Azure 間

Site Recovery は、Azure にレプリケートするときに、ゲスト クラスター サポートをサポートしていません。SQL Server も Standard エディション用に低コストの障害復旧ソリューションを提供しません。スタンドアロンの SQL Server でオンプレミスの SQL Server クラスターを保護し、Azure で復旧することをお勧めします。


1. オンプレミスのサイトで、追加のスタンドアロンの SQL Server のインスタンスを構成します。
2. このインスタンスを、保護が必要なデータベースのミラーとして機能するように構成します。高い安全性モードでのミラーリングの構成
3.	環境に基づいて ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) または [VMware](site-recovery-vmware-to-azure.md))、オンプレミスのサイトで Site Recovery を構成します。
4.	Azure に新しい SQL Server インスタンスをレプリケートするには、Site Recovery レプリケーションを使用します。これは高い安全性のミラー コピーなので、プライマリ クラスターと同期されますが、Site Recovery レプリケーションを使用して Azure にレプリケートされます。

次の図は、この設定を示しています。

![Standard クラスター](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)



##復旧計画の作成

復旧計画は、一緒にフェールオーバーするマシンをグループ化します。開始する前に、[復旧計画](site-recovery-create-recovery-plans.md)と[フェールオーバー](site-recovery-failover.md)の詳細情報を参照してください。


### SQL Server クラスター (SQL Server 2012/2014 Enterprise) の復旧計画の作成

#### Azure へのフェールオーバー用の SQL Server スクリプトの構成

このシナリオでは、復旧計画用にカスタム スクリプトと Azure Automation を利用して、SQL Server 可用性グループのスクリプト化されたフェールオーバーを構成します。

1.	可用性グループをフェールオーバーするスクリプトのローカル ファイルを作成します。このサンプル スクリプトでは、Azure レプリカ上の可用性グループへのパスを指定し、そのレプリカ インスタンスに可用性グループをフェールオーバーします。このスクリプトは、カスタム スクリプトの拡張を使用して可用性グループを渡すことによって、SQL Server レプリカ仮想マシン上で実行されます。



    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2.	スクリプトを Azure ストレージ アカウント内の BLOB にアップロードします。次の例を使用します。

    	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
    	Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.	Azure の SQL Server レプリカ仮想マシンでスクリプトを呼び出す Azure Automation Runbook を作成します。これを行うには、次のサンプル スクリプトを使用します。復旧計画での Automation Runbook の使用方法については、[詳細情報](site-recovery-runbook-automation.md)を参照してください。

    	workflow SQLAvailabilityGroupFailover
    	{
    		param (
        		[Object]$RecoveryPlanContext
    		)

    		$Cred = Get-AutomationPSCredential -name 'AzureCredential'
	
    		#Connect to Azure
    		$AzureAccount = Add-AzureAccount -Credential $Cred
    		$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    		Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
    		InLineScript
    		{
     		#Update the script with name of your storage account, key and blob name
     		$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
     		$sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
     		Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
     		if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
       			{
           		#Skipping TFO in this version.
           		#We will update the script in a follow-up post with TFO support
           		Write-output "tfo: Skipping SQL Failover";
       			}
     		else
       			{
           		Write-output "pfo/ufo";
           		#Get the SQL Azure Replica VM.
           		#Update the script to use the name of your VM and Cloud Service
           		$VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
           		Write-Output "Installing custom script extension"
           		#Install the Custom Script Extension on teh SQL Replica VM
           		Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
           		Write-output "Starting AG Failover";
           		#Execute the SQL Failover script
           		#Pass the SQL AG path as the argument.
       
           		$AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
           		Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
           		Write-output "Completed AG Failover";

       			}
        
    		}
    	}

4.	アプリケーションの復旧計画を作成するときに、可用性グループをフェールオーバーする Automation Runbook を呼び出す「pre-Group 1 boot」というスクリプト化した手順を追加します。

#### セカンダリ サイトへのフェールオーバー用の SQL Server スクリプトの構成

1. このサンプル スクリプトは、プライマリ サイトおよびセカンダリ サイト上の VMM ライブラリに追加します。

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. アプリケーションの復旧計画を作成するときに、可用性グループをフェールオーバーするスクリプトを呼び出す「pre-Group 1 boot」というスクリプト化した手順を追加します。

### SQL Server クラスター (Standard) の復旧計画の作成

#### Azure へのフェールオーバー用の SQL Server スクリプトの構成

1.	SQL Server データベース ミラーをフェールオーバーするスクリプトのローカル ファイルを作成します。次のサンプル スクリプトを使用します。

    	Param(
    	[string]$database
    	)
    	Import-module sqlps
    	Invoke-sqlcmd –query “ALTER DATABASE $database SET PARTNER FORCE_SERVICE_ALLOW_DATA_LOSS”

2.	スクリプトを Azure ストレージ アカウント内の BLOB にアップロードします。次のサンプル スクリプトを使用します。

    	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
    	Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.	Azure の SQL Server レプリカ仮想マシンでスクリプトを呼び出す Azure Automation Runbook を作成します。これを行うには、次のサンプル スクリプトを使用します。復旧計画での Automation Runbook の使用方法については、[詳細情報](site-recovery-runbook-automation.md)を参照してください。これを行う前に、フェールオーバーした SQL Server 仮想マシン上で仮想マシン エージェントが実行されていることを確認します。

    	workflow SQLAvailabilityGroupFailover
		{
    		param (
        		[Object]$RecoveryPlanContext
    		)

    	$Cred = Get-AutomationPSCredential -name 'AzureCredential'
	
    	#Connect to Azure
    	$AzureAccount = Add-AzureAccount -Credential $Cred
    	$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    	Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
    	InLineScript
    	{
     	#Update the script with name of your storage account, key and blob name
     	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
     	$sasuri = New-AzureStorageBlobSASToken -Container "script-container" -Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
     	Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
     	if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
       		{
           		#Skipping TFO in this version.
           		#We will update the script in a follow-up post with TFO support
           		Write-output "tfo: Skipping SQL Failover";
       		}
     	else
       			{
           		Write-output "pfo/ufo";
           		#Get the SQL Azure Replica VM.
           		#Update the script to use the name of your VM and Cloud Service
           		$VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
           		Write-Output "Installing custom script extension"
           		#Install the Custom Script Extension on teh SQL Replica VM
           		Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
           		Write-output "Starting AG Failover";
           		#Execute the SQL Failover script
           		#Pass the SQL AG path as the argument.
       
           		$AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
           		Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
           		Write-output "Completed AG Failover";

       			}
        
    		}
		}



4. 次の手順を、SQL Server 層をフェールオーバーする復旧計画に追加します。

	- 計画されたフェールオーバーでは、"グループのシャットダウン" 後にプライマリ クラスターをシャットダウンするプライマリ側のスクリプトを追加します。
	- SQL Server データベース ミラーの仮想マシンを復旧計画 (可能であれば最初のブート グループ) に追加します。
3.	上記の自動化スクリプトを使用して、この仮想マシン内のミラー コピーをフェールオーバーするフェールオーバー後のスクリプトを追加します。データベース インスタンス名は変更される可能性があるため、新しいデータベースを使用するようアプリケーション層を再構成する必要があります。


#### セカンダリ サイトへのフェールオーバー用の SQL Server スクリプトの構成

1.	このサンプル スクリプトは、プライマリ サイトおよびセカンダリ サイト上の VMM ライブラリに追加します。

    	Param(
    	[string]$database
    	)
    	Import-module sqlps
    	Invoke-sqlcmd –query “ALTER DATABASE $database SET PARTNER FORCE_SERVICE_ALLOW_DATA_LOSS”

2.	SQL Server データベース ミラーの仮想マシンを復旧計画 (可能であれば最初のブート グループ) に追加します。
3.	上記の VMM スクリプトを使用して、この仮想マシン内のミラー コピーをフェールオーバーするフェールオーバー後のスクリプトを追加します。データベース インスタンス名は変更される可能性があるため、新しいデータベースを使用するようアプリケーション層を再構成する必要があります。





## テスト フェールオーバーの考慮事項

AlwaysOn 可用性グループを使用している場合は、SQL Server 層のテスト フェールオーバーを実行できません。別の方法として、以下のオプションについて検討します。

- 方法 1

	1. アプリケーション層とフロントエンド層のテスト フェールオーバーを実行します。
	2. 読み取り専用モードでレプリカのコピーにアクセスするようアプリケーション層を更新し、アプリケーションの読み取り専用テストを実行します。

- 方法 2
1.	(サイト間の VMM の複製または Azure Backup を使用して) レプリカの SQL Server 仮想マシン インスタンスのコピーを作成し、テスト ネットワークで起動します。
2.	復旧計画を使用して、テスト フェールオーバーを実行します。

## フェールバックに関する考慮事項

SQL Standard のクラスターの場合、計画外のフェールオーバー後のフェールバックに SQL Server のバックアップが必要となり、ミラー インスタンスから元のクラスターに復元してから、ミラーを再確立する必要があります。





 

<!---HONumber=58_postMigration-->