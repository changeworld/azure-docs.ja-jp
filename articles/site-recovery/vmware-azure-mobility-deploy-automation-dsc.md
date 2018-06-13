---
title: Azure Automation DSC を使用して Site Recovery モビリティ サービスをデプロイする | Microsoft Docs
description: Azure に VMware VM と物理サーバーをレプリケートするために Azure Automation DSC を使用して Azure Site Recovery モビリティ サービスと Azure エージェントを自動デプロイする方法についての説明
services: site-recovery
author: krnese
manager: lorenr
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: krnese
ms.openlocfilehash: 0bf1b551ba578cd152201c131bd60470bdc9d86a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
ms.locfileid: "29811359"
---
# <a name="deploy-the-mobility-service-with-azure-automation-dsc"></a>Azure Automation DSC を使用してモビリティ サービスをデプロイする

Mobility Service は、[Azure Site Recovery](site-recovery-overview.md) を使用して Azure にレプリケートする、VMware VM および物理サーバーにデプロイします。

この記事では、Azure Automation Desired State Configuration (DSC) を使用して Windows コンピューターにサービスをデプロイする方法について説明します。


## <a name="prerequisites"></a>前提条件

* 必須のセットアップを格納するリポジトリ
* 管理サーバーへの登録に必要なパスフレーズを格納するリポジトリ。 特定の構成サーバーに対して一意のパスフレーズが生成されます。 
* 保護を有効にするコンピューターには Windows Management Framework (WMF) 5.0 をインストールする必要があります。 これは Automation DSC の要件です。
WMF 4.0 がインストールされている Windows コンピューターに対して DSC を使用する場合は、[分離された環境での DSC の使用](## Use DSC in disconnected environments)に関するページを参照してください。
 

## <a name="extract-binaries"></a>バイナリの抽出

モビリティ サービスは、コマンドラインからインストールでき、いくつかの引数を使用できます。 バイナリを (セットアップからの抽出後に) 取得し、DSC 構成を使用して取得できる場所に保存する必要があります。

1. このセットアップに必要なファイルを抽出するために、管理サーバーで **\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository** ディレクトリに移動します。
2. このフォルダーに **Microsoft-ASR_UA_version_Windows_GA_date_Release.exe** という MSI ファイルがあることを確認します。
3. **.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe /q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract** コマンドを使用してインストーラーを抽出します。
2. すべてのファイルを選択し、圧縮 (zip 形式) フォルダーに送ります。

Automation DSC を使用してモビリティ サービスのセットアップを自動化するために必要なバイナリを取得できました。

## <a name="store-the-passphrase"></a>パスフレーズの保存

この zip 形式フォルダーを配置する場所を決定する必要があります。 Azure ストレージ アカウントを使用して、セットアップに必要なパスフレーズを保存できます。これについては後で説明します。 エージェントはこれをプロセスの一部として管理サーバーに登録します。

1. テキスト ファイル (passphrase.txt) で構成サーバーをデプロイしたときに取得したパスフレーズを保存します。
2. zip 形式フォルダーとパスフレーズの両方を Azure ストレージ アカウントの専用コンテナーに配置します。


ネットワーク内の共有上にこれらのファイルを保持することもできます。 ただし、後で使用する DSC リソースにアクセスが設定され、セットアップとパスフレーズを取得できることを確認する必要があります。

## <a name="create-the-dsc-configuration"></a>DSC 構成の作成

このセットアップでは WMF 5.0 を使用します。 つまり、Automation DSC によって正常に構成を適用するために、WMF 5.0 が必要となります。

この環境では、次の例の DSC 構成を使用します。

```powershell
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
この構成では、次の操作を行います。

* 変数によって、モビリティ サービスと Azure VM エージェントのバイナリ、およびパスフレーズの取得場所が構成に指定され、出力の保存先も示されます。
* `xRemoteFile` を使用してリポジトリからファイルをダウンロードできるように、xPSDesiredStateConfiguration DSC リソースをインポートします。
* バイナリを格納するディレクトリを作成します。
* Archive リソースにより、zip 形式フォルダーからファイルを抽出します。
* パッケージ 'Install' により、特定の引数を指定して、UNIFIEDAGENT.EXE インストーラーからモビリティ サービスをインストールします  (引数を作成する変数は、環境に合わせて変更する必要があります)。
* パッケージ 'AzureAgent' により、Azure で実行されるすべての VM で推奨される Azure VM エージェントをインストールします。 このエージェントにより、VM のフェールオーバー後に拡張機能を追加することもできるようになります。
* Service リソースにより、関連するモビリティ サービスと Azure のサービスが常に実行されているようにします。

**ASRMobilityService**として構成を保存します。

> [!NOTE]
> 実際の管理サーバーに合わせて構成の CSIP を置換して、エージェントが正しく接続され、正しいパスフレーズが使用されるようにしてください。
>
>

## <a name="upload-to-automation-dsc"></a>Automation DSC へのアップロード
作成した DSC 構成によって必要な DSC リソース モジュール (xPSDesiredStateConfiguration) がインポートされるため、DSC 構成をアップロードする前に、Automation にこのモジュールをインポートする必要があります。

1. Automation アカウントにサインインし、**[アセット]** > **[モジュール]** に移動して、**[Browse Gallery (ギャラリーの参照)]** をクリックします。
2. モジュールを検索し、アカウントにインポートします。
3. この作業が完了したら、Azure Resource Manager モジュールをインストールしたコンピューターに移動し、新しく作成した DSC 構成のインポートを開始します。

## <a name="import-cmdlets"></a>コマンドレットのインポート

1. PowerShell で、Azure サブスクリプションにサインインします。
2. 環境に合わせてコマンドレットを変更し、Automation アカウント情報を変数内にキャプチャします。

    ```powershell
    $AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
    ```

3. 次のコマンドレットを使用して Automation DSC へ構成をアップロードします。

    ```powershell
    $ImportArgs = @{
        SourcePath = 'C:\ASR\ASRMobilityService.ps1'
        Published = $true
        Description = 'DSC Config for Mobility Service'
    }
    $AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
    ```

## <a name="compile-the-configuration-in-automation-dsc"></a>Automation DSC での構成のコンパイル

1. Automation DSC で構成をコンパイルして、ノードを登録できるようにします。 これを行うために、次のコマンドレットを実行します。

    ```    powershell
    $AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
    ```

実際にはホストされている DSC プル サービスに構成をデプロイしているため、この処理には数分かかることがあります。

2. 構成をコンパイルしたら、PowerShell (Get AzureRmAutomationDscCompilationJob) または [Azure ポータル](https://portal.azure.com/)を使用して、ジョブの情報を取得できます。

    ![ジョブの取得](./media/vmware-azure-mobility-deploy-automation-dsc/retrieve-job.png)

DSC 構成が正常に Automation DSC へ公開およびアップロードされました。

## <a name="onboard-machines-to-automation-dsc"></a>Automation DSC へのマシンの追加


1. Windows コンピューターが WMF の最新バージョンで更新されていることを確認します。 お使いのプラットフォームに適切なバージョンは、 [ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=50395)からダウンロードおよびインストールできます。
2. ノードに適用する DSC の metaconfig を作成します。 正常に作成するには、Azure で選択した Automation アカウントのエンドポイント URL とプライマリ キーを取得する必要があります。 これらの値は、Automation アカウントの **[すべての設定]** ブレードの **[キー]** にあります。

    ![キーの値](./media/vmware-azure-mobility-deploy-automation-dsc/key-values.png)

この例には、Site Recovery で保護する Windows Server 2012 R2 物理サーバーがあります。

## <a name="check-for-any-pending-file-rename-operations"></a>保留中のファイル名変更操作の確認

サーバーを Automation DSC のエンドポイントに関連付ける前に、レジストリに保留中のファイル名変更操作がないかどうかを確認することをお勧めします。 保留中のファイル名変更操作があると、再起動が保留されてセットアップを完了できない可能性があります。

1. 次のコマンドレットを実行して、サーバー上に保留中の再起動がないかどうかを確認します。

    ```powershell
    Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
    ```
2. 空白が返された場合は、セットアップを続行できます。 それ以外の場合は、メンテナンス期間中にサーバーを再起動して、これに対処する必要があります。
3. サーバーに構成を適用するために、PowerShell Integrated Scripting Environment (ISE) を起動して次のスクリプトを実行します。 これは、本質的には DSC ローカル構成であり、ローカル構成マネージャーのエンジンに対して Automation DSC サービスに登録し、特定の構成 (ASRMobilityService.localhost) を取得するように指示します。

    ```powershell
    [DSCLocalConfigurationManager()]
    configuration metaconfig {
        param (
            $URL,
            $Key
        )
        node localhost {
            Settings {
                RefreshFrequencyMins = '30'
                RebootNodeIfNeeded = $true
                RefreshMode = 'PULL'
                ActionAfterReboot = 'ContinueConfiguration'
                ConfigurationMode = 'ApplyAndMonitor'
                AllowModuleOverwrite = $true
            }

            ResourceRepositoryWeb AzureAutomationDSC {
                ServerURL = $URL
                RegistrationKey = $Key
            }

            ConfigurationRepositoryWeb AzureAutomationDSC {
                ServerURL = $URL
                RegistrationKey = $Key
                ConfigurationNames = 'ASRMobilityService.localhost'
            }

            ReportServerWeb AzureAutomationDSC {
                ServerURL = $URL
                RegistrationKey = $Key
            }
        }
    }
    metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'
    
    Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
    ```

この構成は、ローカル構成マネージャーがマネージャー自体を Automation DSC に登録するように構成します。 基本的には、エンジンの動作方法と構成のずれ (ApplyAndAutoCorrect) が発生した場合の動作を指示するとともに、必要な再起動が行われた場合に以降の構成を続行するように指示します。

1. このスクリプトを実行すると、ノードは Automation DSC への登録を開始します。

    ![進行中のノード登録](./media/vmware-azure-mobility-deploy-automation-dsc/register-node.png)

2. Azure ポータルに戻ると、新しく登録したノードがポータルに表示されていることがわかります。

    ![ポータルの登録されたノード](./media/vmware-azure-mobility-deploy-automation-dsc/registered-node.png)

3. サーバーで、次の PowerShell コマンドレットを実行して、ノードが正しく登録されたことを確認できます。

    ```powershell
    Get-DscLocalConfigurationManager
    ```

4. 構成がプルされてサーバーに適用された後で、次のコマンドレットを実行してこれを確認できます。

    ```powershell
    Get-DscConfigurationStatus
    ```

出力には、サーバーがその構成を正常にプルしていることが示されます。

![出力](./media/vmware-azure-mobility-deploy-automation-dsc/successful-config.png)

さらに、モビリティ サービスのセットアップでは、*SystemDrive*\ProgramData\ASRSetupLogs に独自のログが配置されます。

これで完了です。 Site Recovery で保護するコンピューター上にモビリティ サービスが正常にデプロイおよび登録されます。 DSC により、必要なサービスが常に実行されるようになります。

![デプロイに成功](./media/vmware-azure-mobility-deploy-automation-dsc/successful-install.png)

正常なデプロイが管理サーバーによって検出された後で、保護の構成に進んで Site Recovery を使用したマシンでのレプリケーションを有効にできます。

## <a name="use-dsc-in-disconnected-environments"></a>分離された環境での DSC の使用

コンピューターがインターネットに接続されていない場合でも、DSC を使用して、保護するワークロード上にモビリティ サービスをデプロイおよび構成することができます。

あるいは、環境内で、Automation DSC で使用できるものと同じ機能を提供する独自の DSC プル サーバーをインスタンス化できます。 この場合、クライアントは、DSC エンドポイントに登録された後に構成をプルします。 ただし、プッシュを使用する別のオプションもあります。この場合、ローカルまたはリモートのどちらかにより手動で DSC 構成をマシンにプッシュします。

この例では、このコンピューター名に追加のパラメーターがあることに注意してください。 リモートのファイルは現在、保護対象のマシンからアクセスできるリモート共有に配置されています。 スクリプトの末尾で構成を設定してから、ターゲット コンピューターへの DSC 構成の適用を開始しています。

### <a name="prerequisites"></a>前提条件
xPSDesiredStateConfiguration PowerShell モジュールがインストールされていることを確認します。 WMF 5.0 がインストールされている Windows マシンでは、ターゲット コンピューターで次のコマンドレットを実行するだけで xPSDesiredStateConfiguration モジュールをインストールできます。

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

また、WMF 4.0 がインストール済みの Windows コンピューターにモジュールを配信する必要がある場合は、モジュールをダウンロードおよび保存することもできます。 PowerShellGet (WMF 5.0) が存在するコンピューターでこのコマンドレットを実行します。

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

また WMF 4.0 では、 [Windows 8.1 update KB2883200](https://www.microsoft.com/download/details.aspx?id=40749) がコンピューターにインストールされているかどうかも確認します。

WMF 5.0 または WMF 4.0 がインストール済みの Windows コンピューターに、次の構成をプッシュできます。

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

独自の DSC プル サーバーを社内ネットワーク内でインスタンス化して Automation DSC で使用できる機能を模倣するには、「 [Setting up a DSC web pull server (DSC Web プル サーバーのセットアップ)](https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396)」を参照してください。

## <a name="optional-deploy-a-dsc-configuration-by-using-an-azure-resource-manager-template"></a>省略可能: Azure Resource Manager テンプレートを使用した DSC 構成のデプロイ
この記事では、独自の DSC 構成を作成して自動的にモビリティ サービスと Azure VM エージェントをデプロイし、これらが保護対象のコンピューターで実行されるようにする方法に重点を置いてきました。 こうした方法以外にも、この DSC 構成を新規または既存の Azure Automation アカウントにデプロイする Azure Resource Manager テンプレートがあります。 このテンプレートの入力パラメーターを使用して、環境の変数が含まれる Automation 資産を作成できます。

テンプレートのデプロイ後は、このガイドの手順 4 を参照してコンピューターを追加できます。

このテンプレートは、次の操作を行います。

1. 既存の Automation アカウントの使用、または新規 Automation アカウントの作成
2. 次に対する入力パラメーターの受け取り:
   * ASRRemoteFile--モビリティ サービスのセットアップを保存した場所
   * ASRPassphrase--passphrase.txt ファイルを保存した場所
   * ASRCSEndpoint--管理サーバーの IP アドレス
3. xPSDesiredStateConfiguration PowerShell モジュールのインポート
4. DSC 構成の作成とコンパイル

上記の手順をすべて正しい順序で実行すると、コンピューターを保護対象として追加できるようになります。

デプロイの手順も含まれるテンプレートは [GitHub](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC)にあります。

PowerShell を使用してテンプレートをデプロイする

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## <a name="next-steps"></a>次の手順
モビリティ サービスのエージェントをデプロイした後は、仮想マシンの [レプリケーションを有効にする](vmware-azure-tutorial.md) ことができます。
