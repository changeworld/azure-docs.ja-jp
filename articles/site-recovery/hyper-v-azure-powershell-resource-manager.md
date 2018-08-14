---
title: PowerShell と Azure Resource Manager を使用して Hyper-V VM をレプリケートする | Microsoft Docs
description: PowerShell とAzure Resource Manager を使用して、Azure に対する Hyper-V VM レプリケーションを Azure Site Recovery で自動化します。
services: site-recovery
author: bsiva
manager: abhiag
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: bsiva
ms.openlocfilehash: 721bb725538b0b1f6eb0e7132b99e75491b6f969
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038437"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>PowerShell と Azure Resource Manager を使用して Azure に対する Hyper-V VM のディザスター リカバリーを設定する

[Azure Site Recovery](site-recovery-overview.md) は、Azure 仮想マシン (VM)、オンプレミスの VM、および物理サーバーのレプリケーション、フェールオーバー、復旧を調整して、ビジネス継続性とディザスター リカバリー (BCDR) 戦略に貢献します。

この記事では、Windows PowerShell と Azure Resource Manager を使って、Hyper-V VM を Azure にレプリケートする方法を説明します。 この記事で使用している例では、Hyper-V ホストで実行されている 1 つの VM を Azure にレプリケートする方法を示します。

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell は、Windows PowerShell を使用して Azure を管理するためのコマンドレットを提供します。 Azure 内のサーバーに対する保護と復旧は、Azure PowerShell 用 Azure Resource Manager に用意されている Site Recovery の PowerShell コマンドレットを使って行うことができます。

この記事の内容を使用するには PowerShell の専門家である必要はありませんが、モジュール、コマンドレット、セッションなどの基本的な概念を理解していることを前提としています。 [Windows PowerShell の概要](http://technet.microsoft.com/library/hh857337.aspx)に関するページと [Azure Resource Manager での Azure PowerShell の使用](../powershell-azure-resource-manager.md)に関するページをご覧ください。

> [!NOTE]
> クラウド ソリューション プロバイダー (CSP) プログラムに参加しているマイクロソフト パートナーは、顧客のサーバーの保護を、それぞれの CSP サブスクリプション (テナント サブスクリプション) に対して構成および管理することができます。
>
>

## <a name="before-you-start"></a>開始する前に
次の前提条件を満たしていることを確認してください。

* [Microsoft Azure](https://azure.microsoft.com/) アカウント。 アカウントがなくても、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。 また、「 [Azure Site Recovery Manager の料金](https://azure.microsoft.com/pricing/details/site-recovery/)」も参照してください。
* Azure PowerShell 1.0 今回のリリースとそのインストール方法については、「[Azure PowerShell 1.0](https://azure.microsoft.com/)」を参照してください。
* [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) モジュールと [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) モジュール。 最新版のモジュールを [PowerShell ギャラリー](https://www.powershellgallery.com/)

また、この記事で説明されている特定の例には、次の前提条件があります。

* 1 つ以上の VM を含む Windows Server 2012 R2 または Microsoft Hyper-V Server 2012 R2 を実行する Hyper-V ホスト。 Hyper-V サーバーは、直接、またはプロキシを経由して、インターネットに接続されている必要があります。
* レプリケートする VM は、[こちらの前提条件](hyper-v-azure-support-matrix.md#replicated-vms)に従う必要があります。

## <a name="step-1-sign-in-to-your-azure-account"></a>手順 1. Azure アカウントにログインする

1. PowerShell コンソールを開いて次のコマンドを実行し、Azure アカウントにログインします。 コマンドレットを実行すると Web ページが表示され、次のアカウントの資格情報 **Connect-AzureRmAccount** を入力するように求められます。
    - または、**-Credential** パラメーターを使用して、**Connect-AzureRmAccount** コマンドレットのパラメーターとしてアカウントの資格情報を含められます。
    - CSP パートナーがテナントの代理としてサインインする場合は、その顧客をテナントとして指定します。該当するテナント ID またはテナントのプライマリ ドメイン名で指定してください。 たとえば、「**Connect-AzureRmAccount -Tenant "fabrikam.com"**」と入力します。
2. 1 つのアカウントが複数のサブスクリプションを持つことができるため、使用するサブスクリプションをアカウントに関連付けます。

    `Select-AzureRmSubscription -SubscriptionName $SubscriptionName`

3. 次のコマンドを使用して、該当するサブスクリプションに対して Recovery Services 用と Site Recovery 用の Azure プロバイダーが登録され、使用できる状態になっていることを確認します。

    `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

4. コマンド出力で **RegistrationState** が **Registered** に設定されていることを確認し、手順 2. に進みます。 設定されていない場合は、次のコマンドを実行して、登録されていないプロバイダーを該当するサブスクリプションに登録する必要があります。

    `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery` `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

5. 次のコマンドを使用して、プロバイダーが正しく登録されたことを確認します。

    `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

## <a name="step-2-set-up-the-vault"></a>手順 2: コンテナーを設定する

1. 資格情報コンテナーの作成先となる Azure Resource Manager リソース グループを作成するか、既存のリソース グループを使用します。 次のように、新しいリソース グループを作成します。 $ResourceGroupName 変数には、作成するリソース グループの名前が格納され、$Geo 変数には、リソース グループの作成先となる Azure リージョン (例: "ブラジル南部") が格納されます。

    `New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo` 

2. サブスクリプションのリソース グループの一覧を取得するには、**Get-AzureRmResourceGroup** コマンドレットを実行します。
2. 次のコマンドを使用して、Azure Recovery Services の新しい資格情報コンテナーを作成します。

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    既存のコンテナーの一覧を取得するには、**Get-AzureRmRecoveryServicesVault** コマンドレットを使用します。


## <a name="step-3-set-the-recovery-services-vault-context"></a>手順 3. Recovery Services 資格情報コンテナーのコンテキストを設定する

次のように、コンテナーのコンテキストを設定します。

`Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault`

## <a name="step-4-create-a-hyper-v-site"></a>手順 4: Hyper-V サイトを作成する

1. 次のコマンドを使用して、新しい Hyper-V サイトを作成します。

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

2. このコマンドレットは Site Recovery ジョブを起動してサイトを作成し、Site Recovery ジョブ オブジェクトを返します。 ジョブの完了を待って、ジョブが正常に完了したことを確認します。
3. **Get-AzureRmSiteRecoveryJob** を使用して、ジョブ オブジェクトを取得し、ジョブの現在の状態を確認します。
4. 次のように、サイトの登録キーを生成してダウンロードします。

    ```
    $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path
    ```

5. ダウンロードしたキーを Hyper-V ホストにコピーします。 このキーは、Hyper-V ホストをサイトに登録するときに必要になります。

## <a name="step-5-install-the-provider-and-agent"></a>手順 5: プロバイダーとエージェントをインストールする

1. 最新版のプロバイダーのインストーラーを[マイクロソフト](https://aka.ms/downloaddra)からダウンロードします。
2. Hyper-V ホストでインストーラーを実行します。
3. インストールが完了したら、そのまま登録手順に進みます。
4. メッセージに従って、ダウンロードしたキーを入力し、Hyper-V ホストの登録を完了します。
5. 次のように、Hyper-V ホストがサイトに登録されたことを確認します。

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy"></a>手順 6: レプリケーション ポリシーを作成する

開始する前に、指定されたストレージ アカウントが、コンテナーと同じ Azure リージョンに存在する必要があることに注意してください。また、geo レプリケーションが有効になっている必要もあります。

1. 次のように、レプリケーション ポリシーを作成します。

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

2. 返されたジョブをチェックし、レプリケーション ポリシーが正常に作成されたことを確認します。

3. 次のように、サイトに対応する保護コンテナーを取得します。

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3. 次のように、保護コンテナーをレプリケーション ポリシーに関連付けます。

     $Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName   $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

4. 関連付けが正常に完了するのを待ちます。

## <a name="step-7-enable-vm-protection"></a>手順 7: VM 保護を有効にする

1. 次のように、保護する VM に対応する保護エンティティを取得します。

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. VM を保護します。 保護対象の VM に複数のディスクが接続されている場合は、 *OSDiskName* パラメーターを使ってオペレーティング システム ディスクを指定します。

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

3. 初回レプリケーション後、VM が保護された状態になるまで待ちます。 これには、レプリケートされるデータ量と Azure で使用できるアップストリーム帯域幅などの要因に応じて、しばらく時間がかかることがあります。 保護された状態になると、ジョブの State および StateDescription は次のように更新されます。 

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. 復旧のプロパティ (VM ロール サイズなど)、およびフェールオーバー後に VM NIC を接続する Azure ネットワークを更新します。

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob

        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8-run-a-test-failover"></a>ステップ 8: テスト フェールオーバーを実行する
1. 次のように、テスト フェールオーバーを実行します。

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. Azure でテスト VM が作成されていることを確認します。 テスト フェールオーバー ジョブは、Azure にテスト VM を作成した後に中断されます。
3. クリーンアップして、テスト フェールオーバーを完了するには、次を実行します。

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

## <a name="next-steps"></a>次の手順
Azure Site Recovery と Azure Resource Manager PowerShell コマンドレットの[詳細を確認](https://docs.microsoft.com/powershell/module/azurerm.siterecovery)します。
