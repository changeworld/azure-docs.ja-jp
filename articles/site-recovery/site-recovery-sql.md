<properties 
	pageTitle="SQL Server 障害復旧および Azure Site Recovery を使用した SQL Server の保護 | Microsoft Azure" 
	description="この記事では、SQL Server の災害機能の Azure Site Recovery を使用して、SQL Server をレプリケートする方法について説明します。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/06/2016" 
	ms.author="raynew"/>


# SQL Server 障害復旧および Azure Site Recovery を使用した SQL Server の保護 


Azure Site Recovery サービスは、仮想マシンと物理サーバーのレプリケーション、フェールオーバー、復旧を調整してビジネス継続性と障害復旧 (BCDR) 戦略に貢献します。コンピューターを Azure に、またはオンプレミスのセカンダリ データ センターにレプリケートできます。簡単な概要については、「[Azure Site Recovery とは](site-recovery-overview.md)」を参照してください。

 この記事では、SQL Server の BCDR テクノロジおよび Azure Site Recovery の組み合わせを使用してアプリケーションの SQL Server バックエンドを保護する方法について説明します。この記事で説明するシナリオをデプロイするには、SQL Server の障害復旧機能 (フェールオーバー クラスタリング、AlwaysOn 可用性グループ、データベース ミラーリング、ログ配布) および Azure Site Recovery を十分に理解する必要があります。



## 概要

多くのワークロードは、SQL Server を基盤として使用します。SharePoint、Dynamics、および SAP などのアプリケーションは、データ サービスを実装するのに SQL Server を使用します。アプリケーションは、さまざまな方法で SQL Server をデプロイします。

- **スタンドアロンの SQL Server**: SQL Server とすべてのデータベースは、1 つのマシン (物理または仮想) にホストされます。仮想化する場合、ローカルの高可用性のためにホストのクラスタリングを使用します。ゲストレベルの高可用性は実装されません。
- **SQL Server フェールオーバー クラスタリング インスタンス (Always On FCI)**: Windows フェールオーバー クラスターに、共有ディスクが使用された SQL Server インスタンスのノードを 2 つ以上構成します。クラスター インスタンスのいずれかが停止すると、クラスターは SQL Server を別のインスタンスにフェールオーバーできます。この設定は、通常プライマリ サイトの HA で使用されます。この設定では、共有記憶域層の障害や停止は保護されません。共有ディスクは、ISCSI、ファイバー チャネル、または共有 VHDx を使用して実装できます。
- **SQL Always ON 可用性グループ**: 2 つのノードをシェアード ナッシング クラスターに設定します。このクラスターでは、同期レプリケーションと自動フェールオーバーを設定した可用性グループに SQL Server データベースを構成します。

SQL Server では、Enterprise エディションで、リモート サイトにデータベースを復元する復旧テクノロジもネイティブに提供しています。この記事では、次のネイティブ SQL 障害復旧テクノロジを利用し、統合しています。

- Disaster Recovery for SQL Server 2012 または 2014 Enterprise の各エディションの SQL Always On 可用性グループ
- SQL Server Standard エディション (全バージョン) または SQL Server 2008 R2 用の高い安全性モードでの SQL Database ミラーリング


Site Recovery は、次の表のように SQL Server を保護できます。

 |**オンプレミス間** | **オンプレミスと Azure 間** 
---|---|---
**Hyper-V** | あり | あり
**VMware** | あり | あり 
**物理サーバー** | あり | あり


## サポートと統合

これらの SQL Server バージョンは、この記事のシナリオでサポートされます。


- SQL Server 2014 Enterprise および Standard
- SQL Server 2012 Enterprise および Standard
- SQL Server 2008 R2 Enterprise および Standard


Site Recovery は、次の表に要約したネイティブの SQL Server の BCDR テクノロジと統合して、障害復旧ソリューションを提供できます。

**機能** |**詳細** | **SQL Server のバージョン** 
---|---|---
**AlwaysOn 可用性グループ** | SQL Server の複数のスタンドアロン インスタンスであり、複数のノードを持つフェールオーバー クラスターでそれぞれが実行されます。<br/><br/>データベースは、SQL Server インスタンス上でコピー (ミラー化) が可能なフェールオーバー グループにグループ化できるため、共有記憶域は必要ありません<br/><br/>プライマリ サイトと 1 つまたは複数のセカンダリ サイトの間で障害復旧を実現します。2 つのノードをシェアード ナッシング クラスターに設定できます。このクラスターでは、同期レプリケーションと自動フェールオーバーを設定した可用性グループに SQL Server データベースを構成しておきます。 | SQL Server 2014 および 2012 Enterprise エディション
**フェールオーバー クラスタリング (AlwaysOn FCI)** | SQL Server は、Windows のフェールオーバー クラスタリングを利用して、オンプレミスの SQL Server ワークロードの高可用性を実現しています。<br/><br/>共有ディスクを備えた、SQL Server のインスタンスを実行しているノードは、フェールオーバー クラスター内に構成されます。インスタンスがダウンした場合、クラスターは別のクラスターにフェールオーバーします。<br/><br/>クラスターは、共有記憶域のエラーまたは障害からは保護しません。共有ディスクは、iSCSI、ファイバー チャネル、または共有 VHDX を使用して実装できます。 | SQL Server Enterprise エディション<br/><br/>SQL Server Standard エディション (2 つのノードのみに制限)
**データベース ミラーリング (高い安全性モード)** | 1 つのセカンダリ コピーで 1 つのデータベースを保護します。高い安全性 (同期) と高パフォーマンス (非同期) の両方のレプリケーション モードで使用できます。フェールオーバー クラスターは必要はありません。 | SQL Server 2008 R2<br/><br/>SQL Server Enterprise のすべてのエディション
**スタンドアロンの SQL Server** | SQL Server とデータベースは、単一のサーバー (物理または仮想) でホストされます。サーバーが仮想である場合、ホスト クラスタリングが高可用性のために使用されます。ゲストレベルの高可用性はありません。 | Enterprise または Standard エディション

## デプロイメントに関する推奨事項


次の表は、Site Recovery に SQL Server の BCDR テクノロジを統合するための推奨事項を示しています。

**バージョン** |**エディション** | **デプロイ** | **オンプレミスからオンプレミス** | **オンプレミスから Azure** 
---|---|---|---|---
SQL Server 2014 または 2012 | Enterprise | フェールオーバー クラスター インスタンス | AlwaysOn 可用性グループ | AlwaysOn 可用性グループ
| Enterprise | 高可用性のための AlwaysOn 可用性グループ | AlwaysOn 可用性グループ | AlwaysOn 可用性グループ
 | Standard | フェールオーバー クラスター インスタンス (FCI) | ローカルのミラーを使用した Site Recovery レプリケーション | ローカルのミラーを使用した Site Recovery レプリケーション
 | Enterprise または Standard | スタンドアロン | Site Recovery レプリケーション | Site Recovery レプリケーション
SQL Server 2008 R2 | Enterprise または Standard | フェールオーバー クラスター インスタンス (FCI) | ローカルのミラーを使用した Site Recovery レプリケーション | ローカルのミラーを使用した Site Recovery レプリケーション
 | Enterprise または Standard | スタンドアロン | Site Recovery レプリケーション | Site Recovery レプリケーション
SQL Server (全バージョン) | Enterprise または Standard | フェールオーバー クラスター インスタンス - DTC アプリケーション | Site Recovery レプリケーション | サポートされていません


## デプロイメントの前提条件

ここでは、開始する前に必要な項目を示します。

- サポートされている SQL Server のバージョンを実行しているオンプレミスの SQL Server デプロイメント通常、SQL Server には Active Directory も必要です。
- デプロイするシナリオの前提条件。前提条件は、各デプロイメントの記事に記載されています。「[Site Recovery の概要](site-recovery-overview.md)」には、これらの記事へのリンクが記載されています。
- Azure で復旧を設定する場合は、[Azure 仮想マシン準備状態評価](http://www.microsoft.com/download/details.aspx?id=40898)ツールを SQL Server 仮想マシン上で実行し、SQL Server 仮想マシンに Azure および Site Recovery との互換性があることを確認する必要があります。


## Active Directory のセットアップ

SQL Server を正常に実行するには、セカンダリ復旧サイトに Active Directory が必要です。これには、次の 2 つのオプションがあります。

- **小規模企業**—アプリケーションの数が少なく、オンプレミスのサイト用のドメイン コントローラーが 1 つしかない場合にサイト全体をフェールオーバーするには、Site Recovery レプリケーションを使用して、セカンダリ データセンターまたは Azure にドメイン コント ローラーをレプリケートすることをお勧めします。

- **中規模および大企業**—アプリケーションの数が多く、Active Directory フォレストを実行している場合にアプリケーションまたはワークロード単位でフェールオーバーするには、追加のドメイン コントローラーをセカンダリ データセンターまたは Azure にセットアップすることをお勧めします。AlwaysOn 可用性グループを使用してリモート サイトに復旧する場合は、もう 1 つ追加のドメイン コント ローラーをセカンダリ サイトまたは Azure にセットアップして、復旧した SQL Server インスタンスに対して使用することをお勧めします。

このドキュメントに記載された手順は、2 番目の拠点でドメイン コントローラーが使用できることを前提としています。Site Recovery を使用して Active Directory を保護する方法については、[こちら](site-recovery-active-directory.md)を参照してください。

## 保護の SQL Server Always-On との統合 (オンプレミスから Azure へ)

### VMM クラウド内の Hyper-V VM を保護する

Site Recovery は SQL AlwaysOn をネイティブでサポートします。Azure の仮想マシンを "セカンダリ" として設定した状態で SQL 可用性グループを作成した場合、Site Recovery を使用して可用性グループのフェールオーバーを管理できます。

>[AZURE.NOTE] この機能は現在プレビュー段階です。プライマリ データセンターの VMM クラウドで Hyper-V ホスト サーバーが管理されると使用できます。

#### 前提条件

VMM からレプリケートする場合、SQL AlwaysOn を Site Recovery に統合するために必要な前提条件を次に示します。

- オンプレミスの SQL Server (スタンドアロン サーバーまたはフェールオーバー クラスター)。
- SQL Server がインストールされている 1 つまたは複数の Azure Virtual Machines
- オンプレミスの SQL Server と Azure で実行されている SQL Server 間の SQL 可用性グループ セットアップ
- PowerShell リモート処理をオンプレミスの SQL Server マシンで有効にする必要があります。VMM Server から SQL Server へのリモート PowerShell 呼び出しが可能になっている必要があります。
- オンプレミスの SQL Server では、SQL ユーザー グループにユーザー アカウントを追加する際に、少なくとも次のアクセス権を付与する必要があります。
	- ALTER AVAILABILITY GROUP: [これ](https://msdn.microsoft.com/library/hh231018.aspx)と[この](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)アクセス許可
	- ALTER DATABASE - [この](https://msdn.microsoft.com/library/ff877956.aspx#Security)アクセス許可
- そのアカウントの実行アカウントは事前に VMM Server 上で作成しておく必要があります。
- SQL PS モジュールをオンプレミスで実行している SQL Servers と Azure Virtual Machines 上にインストールする必要があります。
- Azure で実行している仮想マシン上に VM Agent をインストールする必要があります。
- Azure の仮想マシン上で実行している SQL Server で NTAUTHORITY\\System に次のアクセス権が付与されている必要があります。
	- ALTER AVAILABILITY GROUP: [これ](https://msdn.microsoft.com/library/hh231018.aspx)と[この](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)アクセス許可
	- ALTER DATABASE - [この](https://msdn.microsoft.com/library/ff877956.aspx#Security)アクセス許可

####  手順 1: SQL Server を追加する


1. **[SQL の追加]** をクリックして、新規の SQL Server を追加します。

	![Add SQL](./media/site-recovery-sql/add-sql.png)

2. **[SQL 設定の構成]** の **[名前]** に、SQL Server を示すフレンドリ名を入力します。
3. **[SQL Server (FQDN)]** に、追加するソース SQL Server の FQDN を入力します。SQL Server をフェールオーバー クラスターにインストールする場合、クラスターの FQDN を指定し、クラスター ノードの FQDN には指定しないでください。
4. **[SQL Server インスタンス]** で、既定のインスタンスを選択するか、カスタム インスタンス名を入力します。
5. **[VMM サーバー]** で、Site Recovery コンテナーに登録されている VMM サーバーを選択します。Site Recovery は SQL Server との通信にこの VMM Server を使用します。
6. **[アカウントとして実行]** に、指定した VMM サーバーで作成された実行アカウント名を入力します。このアカウントは SQL Server へのアクセスに使用され、SQL Server マシンの可用性グループで読み取りおよびフェールオーバーのアクセス権を持っている必要があります。

	![Add SQL Dialog](./media/site-recovery-sql/add-sql-dialog.png)

SQL Server を追加すると、**[SQL Server]** タブに表示されます。

![SQL Server List](./media/site-recovery-sql/sql-server-list.png)


#### 手順 2: SQL 可用性グループを追加する

1. SQL Server マシンを追加したら、次の手順は Site Recovery への可用性グループの追加です。それを行うには、前の手順で追加した SQL Server の中をドリルダウンし、[SQL 可用性グループの追加] をクリックします。

	![Add SQL AG](./media/site-recovery-sql/add-sqlag.png)

2. SQL 可用性グループは Azure の 1 つまたは複数の仮想マシンへのレプリケートが可能です。SQL 可用性グループの追加の際、Site Recovery によって可用性グループをフェールオーバーさせる Azure 仮想マシンの名前およびサブスクリプションを指定する必要があります。

	![Add SQL AG Dialog](./media/site-recovery-sql/add-sqlag-dialog.png)

3. 上の例では、Availability Group (可用性グループ) DB1-AG がフェールオーバーの際に、サブスクリプション DevTesting2 内で実行している仮想マシン SQLAGVM2 でプライマリとなります。

>[AZURE.NOTE] 上の手順で追加した SQL Server 上でプライマリとなる可用性グループのみが Site Recovery に追加可能です。SQL Server に可用性グループ プライマリを作成した場合、もしくは SQL Server を追加したあとに SQL Server にさらに可用性グループを追加した場合は、SQL Server 上で使用可能な [更新] オプションで更新をかけてください。

#### 手順 3: 復旧計画を作成する

次の手順は仮想マシンと可用性グループの両方を使用した復旧計画の作成です。手順 1 で使用した VMM Server と同じものをソースとして、Microsoft Azure をターゲットとして選択してください。

![復旧計画の作成](./media/site-recovery-sql/create-rp1.png)

![復旧計画の作成](./media/site-recovery-sql/create-rp2.png)

例では、Sharepoint アプリケーションはバックエンドに SQL 可用性グループを使用している 3 つの仮想マシンで構成されています。この復旧計画では、アプリケーションを構成する可用性グループと仮想マシンの両方を選択できます。

仮想マシンを他のフェールオーバー グループに移動して、フェールオーバーの実行順序を指定することで復旧計画をカスタマイズすることもできます。可用性グループはどのアプリケーションでもバックエンドとして使用されるので、常に一番最初にフェールオーバーされます。

![復旧計画のカスタマイズ](./media/site-recovery-sql/customize-rp.png)

### 手順 4: フェールオーバー

可用性グループを復旧計画に追加した後は、他のフェールオーバー オプションも使用可能になります。

フェールオーバー | 詳細
--- | ---
**計画されたフェールオーバー** | 計画されたフェールオーバーとはデータ損失のないフェールオーバーを意味します。そのためには、SQL 可用性グループの可用性モードを最初に "同期" に設定し、フェールオーバーがトリガーされることで、Site Recovery に可用性グループを追加した際に指定した仮想マシンに対して可用性グループが "プライマリ" となります。フェールオーバーが完了すると、可用性モードは計画されたフェールオーバーがトリガーされる前と同じ値に設定されます。
**計画されていないフェールオーバー** | 計画されていないフェールオーバーではデータの損失が生じることがあります。計画されていないフェールオーバーがトリガーされても、可用性グループの可用性モードは変更されず、Site Recovery に可用性グループを追加した際に指定した仮想マシンに対して "プライマリ" となっています。計画されていないフェールオーバーが完了すると、SQL Server で実行しているオンプレミスのサーバーが再び使用可能となり、レプリケーションの反転が可用性グループに対してトリガーされる必要があります。この操作は復旧計画では使用できず、[SQL Servers] タブの下の SQL 可用性グループにて行われることに注意してください。
**テスト フェールオーバー** | SQL 可用性グループのテスト フェールオーバーはサポートされていません。SQL 可用性グループを含む復旧計画のテスト フェールオーバーをトリガーしたとしても、可用性グループに対するフェールオーバーはスキップされます。


次のフェールオーバー オプションを検討してください。

オプション | 詳細
--- | ---
**方法 1** | 1\.アプリケーション層とフロントエンド層のテスト フェールオーバーを実行します。<br/><br/>2.読み取り専用モードでレプリカのコピーにアクセスするようアプリケーション層を更新し、アプリケーションの読み取り専用テストを実行します。
**方法 2** | 1\.(サイト間の VMM の複製または Azure Backup を使用して) レプリカの SQL Server 仮想マシン インスタンスのコピーを作成し、テスト ネットワークで起動します。<br/><br/>2.復旧計画を使用して、テスト フェールオーバーを実行します。

手順 5: フェールバック

可用性グループをオンプレミスの SQL Server で再度 "プライマリ" にする場合、復旧計画で計画性フェールオーバーをトリガーし、方向を Microsoft Azure からオンプレミスの VMM Server に選択することで行うことができます。

>[AZURE.NOTE] 計画されていないフェールオーバーの後、可用性グループで反転レプリケーションをトリガーしてレプリケーションを再開させる必要があります。これが行われるまでは、レプリケーションが中断されたままです。



### VMM なしのマシンを保護する

VMM Server で管理されない環境については、Azure Automation Runbooks を使用して、SQL 可用性グループのスクリプト化されたフェールオーバーを構成できます。以下が構成手順です。

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

## 保護を SQL AlwaysOn と統合する (オンプレミスからオンプレミスへ)

SQL Server が可用性グループまたはフェールオーバー クラスター インスタンスを使用して高可用性を実現している場合は、復旧サイトでも可用性グループを使用することをお勧めします。このガイダンスは、分散トランザクションを使用しないアプリケーション向けであることに注意してください。

1. 可用性グループに[データベースを構成](https://msdn.microsoft.com/library/hh213078.aspx)します。
2. セカンダリ サイトに、新しい仮想ネットワークを作成します。
3. 新しい仮想ネットワークとプライマリ サイトの間に、サイト間 VPN を設定します。
4. 復旧サイトに仮想マシンを作成し、SQL Server を仮想マシンにインストールします。
5. 新しい SQL Server 仮想マシンに、既存の AlwaysOn 可用性グループを拡張します。この SQL Server インスタンスは、非同期レプリカ コピーとして構成します。
6. 可用性グループ リスナーを作成するか、または既存のリスナーを更新して、非同期のレプリカ仮想マシンを含めます。
7. アプリケーション ファームがリスナーを使用してセットアップされていることを確認します。データベース サーバー名を使用してセットアップされている場合は、リスナーを使用するように更新して、フェールオーバー後に再構成する必要がないようにしてください。

分散トランザクションを使用するアプリケーションの場合は、[SAN レプリケーション](site-recovery-vmm-san.md)または[ VMware/物理サーバー サイト間レプリケーション](site-recovery-vmware-to-vmware.md)と共に Site Recovery を使用することをお勧めします。

### 復旧計画に関する考慮事項

1. このサンプル スクリプトは、プライマリ サイトおよびセカンダリ サイト上の VMM ライブラリに追加します。

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. アプリケーションの復旧計画を作成するときに、可用性グループをフェールオーバーするスクリプトを呼び出す「pre-Group 1 boot」というスクリプト化した手順を追加します。



## スタンドアロンの SQL Server を保護する

この構成では、Site Recovery レプリケーションを使用して、SQL Server マシンを保護することをお勧めします。正確な手順は、SQL Server が仮想マシンまたは物理サーバーのどちらとして設定されているか、および Azure またはセカンダリ オンプレミス サイトのどちらにレプリケートするかによって異なります。可能なデプロイメント シナリオに関する手順については、「[Site Recovery の概要](site-recovery-overview.md)」を参照してください。


## SQL Server クラスターを保護する (Standard または 2008 R2)

SQL Server Standard エディション または SQL Server 2008 R2 を実行するクラスターでは、Site Recovery レプリケーションを使用して SQL Server を保護することをお勧めします。

### オンプレミス間

- アプリケーションが分散トランザクションを使用している場合は、Hyper-V 環境には [SAN レプリケーションを使用した Site Recovery](site-recovery-vmm-san.md) を、VMware 環境には [VMware/物理サーバーから VMware へ](site-recovery-vmware-to-vmware.md)をデプロイすることをお勧めします。

- DTC 以外のアプリケーションの場合、前述のアプローチで、ローカルの安全性の高い DB ミラーを使用し、スタンドアロン サーバーとしてクラスターを復旧します。

### オンプレミスと Azure 間

Site Recovery は、Azure にレプリケートするときに、ゲスト クラスター サポートをサポートしていません。SQL Server も Standard エディション用に低コストの障害復旧ソリューションを提供しません。スタンドアロンの SQL Server でオンプレミスの SQL Server クラスターを保護し、Azure で復旧することをお勧めします。


1. オンプレミスのサイトで、追加のスタンドアロン SQL Server インスタンスを構成します。
2. このインスタンスを、保護が必要なデータベースのミラーとして機能するように構成します。高い安全性モードでのミラーリングの構成
3.	環境に基づいて ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) または [VMware/物理サーバー](site-recovery-vmware-to-azure-classic.md))、オンプレミスのサイトで Site Recovery を構成します。
4.	Azure に新しい SQL Server インスタンスをレプリケートするには、Site Recovery レプリケーションを使用します。これは高い安全性のミラー コピーなので、プライマリ クラスターと同期されますが、Site Recovery レプリケーションを使用して Azure にレプリケートされます。

次の図は、この設定を示しています。

![Standard クラスター](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)


### フェールバックに関する考慮事項

SQL Standard のクラスターの場合、計画外のフェールオーバー後のフェールバックには、SQL のバックアップ、ミラー インスタンスから元のクラスターへの復元、およびミラーの再確立が必要になります。

## 次のステップ
[Site Recovery をデプロイする準備](site-recovery-best-practices.md)について参照してください。










 

<!---HONumber=AcomDC_0706_2016-->