---
title: "PowerShell を使用して VMM クラウド内の Hyper-V VM をセカンダリ サイトにレプリケートする (Azure Resource Manager) | Microsoft Docs"
description: "PowerShell (Resource Manager) を使用して VMM クラウド内の Hyper-V VM をセカンダリ VMM サイトにレプリケートする方法について説明します。"
services: site-recovery
author: sujaytalasila
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: sutalasi
ms.openlocfilehash: a5e36546494223b20844303f3f76782746658411
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="replicate-hyper-v-vms-to-a-secondary-site-using-powershell-resource-manager"></a>PowerShell (Resource Manager) を使用して Hyper-V VM をセカンダリ サイトへレプリケートする

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して、System Center Virtual Machine Manager (VMM) クラウドの Hyper-V VM をセカンダリ オンプレミス サイトの VMM クラウドにレプリケートする手順を自動化する方法について説明します。

## <a name="prerequisites"></a>前提条件

- [シナリオのアーキテクチャとコンポーネント](hyper-v-vmm-architecture.md)を確認する。
- すべてのコンポーネントの[サポート要件](site-recovery-support-matrix-to-sec-site.md)を確認する。
- VMM サーバーと Hyper-V ホストが[サポート要件](site-recovery-support-matrix-to-sec-site.md)に準拠していることを確認する。
- レプリケートする VM が[レプリケートされるマシンのサポート要件](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions)に準拠していることを確認する。


## <a name="prepare-for-network-mapping"></a>ネットワーク マッピングを準備する

[ネットワーク マッピング](hyper-v-vmm-network-mapping.md)は、ソース クラウドとターゲット クラウド内のオンプレミス VMM VM ネットワーク間をマップします。 マッピングでは次が行われます。

- フェールオーバー後に VM を適切なターゲット VM ネットワークに接続します。 
- ターゲット Hyper-V ホスト サーバーにレプリカ VM を適切に配置します。 
- ネットワーク マッピングを構成しない場合、レプリカ VM はフェールオーバー後に VM ネットワークに接続されません。

次のように VMM を準備します。

1. ソースおよびターゲット VMM サーバー上に [VMM 論理ネットワーク](https://docs.microsoft.com/system-center/vmm/network-logical)があることを確認します。
    - ソース サーバー上の論理ネットワークは、Hyper-V ホストが配置されているソース クラウドと関連付けられている必要があります。
    - ターゲット サーバーの論理ネットワークは、ターゲット クラウドと関連付けられている必要があります。
1. ソースおよびターゲット VMM サーバー上に [VM ネットワーク](https://docs.microsoft.com/system-center/vmm/network-virtual)があることを確認します。 VM ネットワークは、各場所の論理ネットワークにリンクされている必要があります。
2. ソース Hyper-V ホスト上の VM をソース VM ネットワークに接続します。 

## <a name="prepare-for-powershell"></a>PowerShell の準備

Azure PowerShell を使用する準備が整っていることを確認してください。

- PowerShell を使用している場合は、0.8.10 以降のバージョンにアップグレードする必要があります。  PowerShell の設定の詳細については、[こちら](/powershell/azureps-cmdlets-docs)を参照してください。
- PowerShell の設定と構成を完了したら、[サービス コマンドレット](/powershell/azure/overview)を確認します。
- Azure PowerShell のパラメーター値、入力、出力の使用方法については、[概要](/powershell/azure/get-started-azureps)に関するガイドを参照してください。

## <a name="set-up-a-subscription"></a>サブスクリプションを設定する
1. Azure PowerShell で Azure アカウントにログインします。

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred
2. サブスクリプションとサブスクリプション ID の一覧を取得します。 Recovery Services コンテナーを作成するサブスクリプションの ID をメモします。   

        Get-AzureRmSubscription
3. コンテナーに合わせてサブスクリプションを設定します。

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する
1. Azure Resource Manager リソース グループを作成します (まだ存在しない場合)。

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. 新しい Recovery Services コンテナーを作成し、コンテナー オブジェクトを変数に格納します (変数は後で使用します)。 

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location
   
    コンテナー オブジェクトは、作成後に Get-AzureRMRecoveryServicesVault コマンドレットを使用して取得できます。

## <a name="set-the-vault-context"></a>コンテナーのコンテキストを設定する
1. 既存のコンテナーを取得します。

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. コンテナーのコンテキストを設定します。

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-azure-site-recovery-provider"></a>Azure Site Recovery プロバイダーのインストール
1. VMM マシンで、次のコマンドを実行してディレクトリを作成します。

       New-Item c:\ASR -type directory
2. 次のコマンドを実行して、ダウンロードしたプロバイダー設定ファイルを使用してファイルを抽出します。pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. プロバイダーをインストールし、インストールが完了するまで待ちます。

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

4. サーバーをコンテナーに登録します。

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="create-and-associate-a-replication-policy"></a>レプリケーション ポリシーを作成して関連付ける
1. 次のように、レプリケーション ポリシー (この場合は Hyper-V 2012 R2) を作成します。

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > VMM クラウドには、複数バージョンの Windows Server を実行する Hyper-V ホストが含まれる可能性がありますが、レプリケーション ポリシーはオペレーティング システムの特定のバージョン用です。 さまざまなオペレーティング システムでさまざまなホストを実行している場合は、システムごとに個別のレプリケーション ポリシーを作成します。 たとえば、Windows Server 2012 で実行されているホストが 5 台、Windows Server 2012 R2 のホストが 3 台ある場合は、各オペレーティング システムに対して 1 つずつ、つまり 2 つのレプリケーション ポリシーを作成します。

2. プライマリ保護コンテナー (プライマリ VMM クラウド) と復旧保護コンテナー (復旧 VMM クラウド) を取得します。

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
2. フレンドリ名を使用して、作成したレプリケーション ポリシーを取得します。

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. 保護コンテナー (VMM クラウド) とレプリケーション ポリシーの関連付けを開始します。

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
4. ポリシー関連付けジョブが完了するのを待ちます。 次の PowerShell スニペットを使用して、ジョブが完了したかどうかを確認することができます。

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

   ジョブの処理が完了したら、次のコマンドを実行します。

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

操作の完了を確認するには、「[アクティビティを監視する](#monitor-activity)」の手順を実行します。

##  <a name="configure-network-mapping"></a>ネットワーク マッピングの構成
1. このコマンドを使用して、現在のコンテナーのサーバーを取得します。 このコマンドで、$Servers 配列変数に Azure Site Recovery サーバーが格納されます。

        $Servers = Get-AzureRmSiteRecoveryServer
2. このコマンドを実行して、ソース VMM サーバーとターゲット VMM サーバーのネットワークを取得します。

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > ソース VMM サーバーは、サーバー配列で 1 つ目または 2 つ目として指定できます。 VMM サーバー名を確認し、ネットワークを適切に取得します。


3. このコマンドレットは、プライマリ ネットワークと復旧ネットワークの間のマッピングを作成します。 プライマリ ネットワークを $PrimaryNetworks の最初の要素として、復旧ネットワークを $RecoveryNetworks の最初の要素として指定します。

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>VM の保護を有効にする
サーバー、クラウド、ネットワークを正しく構成したら、クラウド内の VM の保護を有効にすることができます。

1. 保護を有効にするには、次のコマンドを実行して保護コンテナーを取得します。

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. 次のように保護エンティティ (VM) を取得します。

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. VM のレプリケーションを有効にします。

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

デプロイをテストするには、単一の仮想マシンに対してテスト フェールオーバーを実行するか、複数の VM を含む復旧計画を作成し、計画のテスト フェールオーバーを実行します。 テスト フェールオーバーは、孤立したネットワークでフェールオーバーと復旧のシミュレーションを実行します。

1. VM がフェールオーバーする VM を取得します。

       $Servers = Get-AzureRmSiteRecoveryServer
       $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]
2. 次のようにテスト フェールオーバーを実行します。
    - 単一の VM の場合

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
    - 復旧計画の場合:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

操作の完了を確認するには、「[アクティビティを監視する](#monitor-activity)」の手順を実行します。

## <a name="run-planned-and-unplanned-failovers"></a>計画フェールオーバーと計画外のフェールオーバーの実行

1. 計画フェールオーバーは実行次のように実行します。
    -  単一の VM の場合:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity
    - 復旧計画の場合

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan
2. 計画外のフェールオーバーは次のように実行します。
    - 単一の VM の場合
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

    - 復旧計画の場合:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>アクティビティを監視する
フェールオーバー アクティビティを監視するには、次のコマンドを使用します。 ジョブの処理が終了するまで待機する必要があります。

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>次の手順

Site Recovery と Azure Resource Manager PowerShell コマンドレットの詳細については、[こちら](/powershell/module/azurerm.recoveryservices.backup/#recovery)を参照してください。
