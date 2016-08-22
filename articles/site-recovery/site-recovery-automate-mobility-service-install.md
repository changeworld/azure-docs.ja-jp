<properties
	pageTitle="Azure Automation DSC で Azure Site Recovery を使用して VMware 仮想マシンを Azure にレプリケートする | Microsoft Azure"
	description="Azure Automation DSC を使用して Azure に ASR モビリティ サービスと仮想/物理マシン用 Azure エージェントを自動的にデプロイする方法について説明します。"
	services="site-recovery"
	documentationCenter=""
	authors="krnese"
	manager="lorenr"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/26/2016"
	ms.author="krnese"/>

# Azure Automation DSC で Azure Site Recovery を使用して VMware VM を Azure にレプリケートする

OMS では、ビジネス継続性計画の一部として利用できる包括的なバックアップおよび障害復旧ソリューションが提供されています。

当初は HYPER-V レプリカを使用した HYPER-V に対応していましたが、お客様が複数のハイパーバイザーとプラットフォームをクラウドでデプロイしていることを認識したため、多様なセットアップをサポートするように拡張しました。

現在 VMware のワークロードや物理サーバーを実行していて、Azure が接続先である場合、Azure との通信およびデータ レプリケーションの処理は、Site Recovery コンポーネントのすべてを実行する管理サーバーで行われます。

## OMS Automation DSC を使用して ASR モビリティ サービスをデプロイする
初めに、この管理サーバーが実際に行う処理を簡単に説明します。

管理サーバーは、通信の調整およびデータ レプリケーション プロセスと回復プロセスの管理を行う**構成**など、いくつかのサーバー ロールを実行します。

また、**プロセス** ロールではレプリケーション ゲートウェイとして機能し、保護されたソース マシンからレプリケーション データを受信してそのデータをキャッシュ、圧縮、暗号化によって最適化してから、Azure ストレージ アカウントに送信します。また、プロセス ロールの機能の 1 つとして、保護されたマシンへのモビリティ サービスのインストールをプッシュし、VMware VM の自動検出を実行します。

Azure でのフェールバックについては**マスター ターゲット** ロールがこれを担当し、この操作の一環としてレプリケーション データを処理します。

保護されたマシンについては、Azure にレプリケートするすべてのマシン (VMware VM または物理サーバー) にデプロイされているコンポーネントである**モビリティ サービス**を活用しています。その役割は、マシン上のデータ書き込みをキャプチャして、管理サーバー (プロセス ロール) に転送することです。

ビジネス継続性を扱う際には、ワークロードおよびインフラストラクチャだけでなく、RTO と RPO の要件を満たすために必要なコンポーネントを理解することが重要です。このコンテキストでは、モビリティ サービスが、ワークロードを想定どおりに保護するための鍵となります。

それでは、いくつかの OMS コンポーネントを使用して、セットアップを信頼性が高く保護されたものにする最適な方法とはどのようなものでしょうか。

この記事では、OMS Site Recovery と共に OMS Automation DSC を利用して、モビリティ サービスと Azure VM エージェントを保護対象の Windows マシンにデプロイしし、Azure がレプリケーション対象の場合はこれらが常に実行されるようにする方法についての例を示します。

## 前提条件

- 必須のセットアップを格納するリポジトリ
- 管理サーバーへの登録に必要なパスフレーズを格納するリポジトリ
- 保護を有効にするコンピューターにインストールされている Windows Management Framework 5.0 (OMS Automation DSC の要件)

WMF 4.0 を使用して、Windows コンピューターに対して DSC を使用する場合は、「分離された環境での DSC の使用」セクションを参照してください。

(管理サーバーごとに一意のパスフレーズが生成されます。複数の管理サーバーをデプロイする場合は、passphrase.txt ファイルに正しいパスフレーズが保存されていることを確認してください)

モビリティ サービスは、コマンドラインからインストールでき、いくつかの引数を使用できます。

このため、バイナリを (セットアップからの抽出後に) 取得し、DSC 構成を使用して取得できる場所に保存する必要があります。

## 手順 1: バイナリの抽出

1. このセットアップに必要なファイルを抽出するために、管理サーバーで **\\Microsoft Azure Site Recovery\\home\\svsystems\\pushinstallsvc\\repository** ディレクトリに移動します。

    このフォルダーに、次の名前の MSI ファイルがあります。

    **Microsoft-ASR\_UA\_<バージョン>_Windows\_GA_<日付>\_Release.exe**

    次のコマンドレットを使用してインストーラーを抽出します。

    .\\Microsoft-ASR\_UA\_9.1.0.0_Windows\_GA_02May2016\_release.exe /q /x:C:\\Users\\Administrator\\Desktop\\Mobility\_Service\\Extract

2. すべてのファイルを選択し、圧縮 (zip 形式) フォルダーに送ります。

これで終了です。 OMS Automation DSC を使用してモビリティ サービスのセットアップを自動化するために必要なバイナリを取得できました。

### パスフレーズ
次に、この zip 形式フォルダーを配置する場所を決定する必要があります。この例では、後で示すように Azure のストレージ アカウントを使用していますが、エージェントがプロセスの一部として管理サーバーに登録できるように、セットアップに必要なパスフレーズもここに配置しています。

管理サーバーをデプロイするときに取得したパスフレーズは、passphrase.txt という txt ファイルに保存します。

この例では、zip 形式フォルダーとパスフレーズの両方を Azure ストレージ アカウントの専用コンテナーに配置しています。

![フォルダーの場所](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

ネットワーク内の共有上にこれらのファイルを保持することもできます。ただし、後で使用する DSC リソースにアクセスが設定され、セットアップとパスフレーズを取得できることを確認する必要があります。

## 手順 2: DSC 構成の作成
この例のセットアップでは WMF 5.0 を使用します。つまり、マシンで OMS Automation DSC により正常に構成を適用するために、WMF 5.0 が必要となります。

この例の環境では、次の DSC 構成を使用します。

```
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
    $Arguments = '/Role ' + '"' + $Role + '"' + ' /InstallLocation ' + '"' + $Install + '"' + ' /CSEndpoint ' + '"' + $CSEndpoint + '"' + ' /PassphraseFilePath ' +  '"' +$LocalPassphrase + '"'  

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        xRemoteFile Setup {
           URI = $RemoteFile
           DestinationPath = $TempDestination
           DependsOn = "[File]Directory"
            }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = "[File]Directory"
            }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = "[File]Directory"
            }

        File Directory {
            DestinationPath = "C:\Temp\ASRSetup"
            Type = "Directory"            
            }

        Archive ASRzip {
           Path = $TempDestination
           Destination = "C:\Temp\ASRSetup"
           DependsOn = "[xRemotefile]Setup"
           }

        Package Install {
            Path = "C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE"
            Ensure = "Present"
            Name = "Microsoft Azure Site Recovery mobility Service/Master Target Server"
            ProductId = "275197FC-14FD-4560-A5EB-38217F80CBD1"
            Arguments = $Arguments
            DependsOn = "[Archive]ASRzip"
            }

        Package AzureAgent {
            Path = "C:\Temp\AzureVmAgent.msi"
            Ensure = "Present"
            Name = "Windows Azure VM Agent - 2.7.1198.735"
            ProductId = "5CF4D04A-F16C-4892-9196-6025EA61F964"
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = "[Package]Install"
            }

        Service ASRvx {
            Name = "svagents"
            Ensure = "Present"
            State = "Running"
            DependsOn = "[Package]Install"
            }

        Service ASR {
            Name = "InMage Scout Application Service"
            Ensure = "Present"
            State = "Running"
            DependsOn = "[Package]Install"
            }

        Service AzureAgentService {
            Name = "WindowsAzureGuestAgent"
            Ensure = "Present"
            State = "Running"
            DependsOn = "[Package]AzureAgent"
            }

        Service AzureTelemetry {
            Name = "WindowsAzureTelemetryService"
            Ensure = "Present"
            State = "Running"
            DependsOn = "[Package]AzureAgent"
            }
    }
}
```
この構成では、次の操作を行います。

- 変数によって、モビリティ サービスと Azure VM エージェントのバイナリ、およびパスフレーズの取得場所が構成に指定され、出力の保存先も示されます。
- 'xRemoteFile' を使用してリポジトリからファイルをダウンロードできるように、xPSDesiredStateConfiguration DscResource をインポートします。
- バイナリを格納するディレクトリを作成します。
- Archive リソースにより、zip 形式フォルダーからファイルを抽出します。
- パッケージの 'Install' リソースにより、特定の引数を指定して UNIFIEDAGENT.EXE インストーラーからモビリティ サービスをインストールします (引数を作成する変数は、環境に合わせて変更する必要があります)。
- パッケージ 'AzureAgent' により、Azure で実行されるすべての VM で推奨される Azure VM エージェントをインストールします。このエージェントにより、VM のフェールオーバー後に拡張機能を追加することもできるようになります。
- Service リソースにより、関連するモビリティ サービスと Azure のサービスが常に実行されているようにします。

構成は、コンピューター上のフォルダーに **ASRMobilityService** として保存しました。

(実際の管理サーバーに合わせて構成の CSIP を置換して、エージェントが正しく接続され、正しいパスフレーズが使用されるようにしてください)。

## 手順 3 – OMS Automation DSC へのアップロード

作成した DSC 構成によって必要な DSC リソース モジュール (xPSDesiredStateConfiguration) がインポートされるため、DSC 構成をアップロードする前に、OMS Automation にこのモジュールをインポートする必要があります。

Automation アカウントにログインし、AssetsàModules に移動して、[Browse Gallery (ギャラリーの参照)] をクリックします。

ここで、モジュールを検索し、アカウントにインポートすることができます。

![モジュールのインポート](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

この作業が完了したら、Azure RM モジュールをインストールしたコンピューターに移動し、新しく作成した DSC 構成のインポートを開始します。

### コマンドレットのインポート

PowerShell で、Azure サブスクリプションにログオンし、環境に合わせてコマンドレットを変更します。

最初に、次のコマンドレットを使用して OMS Automation DSC へ構成をアップロードします。

```
Import-AzureRmAutomationDscConfiguration `
                                        -AutomationAccountName KNOMSAA `
                                        -ResourceGroupName KNOMS `
                                        -SourcePath C:\ASR\ASRMobilityService.ps1 `
                                        -Published `
                                        -Description "DSC Config for Mobility Service"
```

次に、OMS Automation DSC で構成をコンパイルして、ノードを登録できるようにします。

### OMS Automation DSC での構成のコンパイル

これを行うために、次のコマンドレットを実行します。

```
Start-AzureRmAutomationDscCompilationJob `
                                        -AutomationAccountName KNOMSAA `
                                        -ResourceGroupName KNOMS `
                                        -ConfigurationName ASRMobilityService
```

実際にはホストされている DSC プル サービスに構成をデプロイしているため、この処理には数分かかることがあります。

処理が完了したら、PowerShell (Get AzureRmAutomationDscCompilationJob) または portal.azure.com を使用して、ジョブの情報を取得できます。

![ジョブの取得](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

DSC 構成が正常に OMS Automation DSC へ公開およびアップロードされました。

## 手順 4 – OMS Automation DSC へのマシンのオンボード
* このシナリオを完了するための前提条件の 1 つとして、Windows コンピューターが WMF の最新バージョンで更新されている必要があります。お使いのプラットフォームに適切なバージョンは、https://www.microsoft.com/download/details.aspx?id=50395* からダウンロードおよびインストールできます。

ノードに適用する DSC の metaconfig を作成します。正常に作成するには、Azure で選択した Automation アカウントのエンドポイント URL とプライマリ キーを取得する必要があります。

これらの値は、Automation アカウントの [すべての設定] ブレードの [キー] にあります。

![キーの値](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

この例の環境には、OMS Site Recovery で保護する Windows Server 2012 R2 物理サーバーがあります。

サーバーを Automation DSC のエンドポイントに関連付ける前に、レジストリに保留中のファイル名変更操作がないかどうかを確認することをお勧めします。保留中のファイル名変更操作があると、再起動が保留されてセットアップを完了できない可能性があります。

### レジストリ内における保留中のファイル名変更操作の確認

次のコマンドレットを実行して、サーバー上に保留中の再起動がないかどうかを確認します。

```
Get-ItemProperty "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager" | select PendingFileRenameOperations
```
空白が返された場合は、セットアップを続行できます。それ以外の場合は、メンテナンス期間中にサーバーを再起動して、これに対処する必要があります。

サーバーに構成を適用するために、PowerShell ISE を起動して次のスクリプトを実行します。これは、本質的には DSC ローカル構成の構成であり、ローカル構成マネージャーのエンジンに対して OMS Automation DSC サービスに登録し、特定の構成 (ASRMobilityService.localhost) を取得するように指示します。

```
[DSCLocalConfigurationManager()]

Configuration metaconfig
{
    node localhost
    {

        Settings
        {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }
                ResourceRepositoryWeb AzureAutomationDSC
            {
                ServerURL = 'https://we-agentservice-prod-1.azure-automation.net/accounts/4d54e439-8540-4c8f-8e0f-3d7afdda6746'
                RegistrationKey = 'KaTg5zTtI/gf8OGiN28L9FT2gMbowzOv3P9rouQfhxcR+42Mac80QKxOFXYLMdKxRdz8QtJiXLb7noG1U4zJ6g=='
            }
                ConfigurationRepositoryWeb AzureAutomationDSC
            {
                ServerURL = 'https://we-agentservice-prod-1.azure-automation.net/accounts/4d54e439-8540-4c8f-8e0f-3d7afdda6746'
                RegistrationKey = 'KaTg5zTtI/gf8OGiN28L9FT2gMbowzOv3P9rouQfhxcR+42Mac80QKxOFXYLMdKxRdz8QtJiXLb7noG1U4zJ6g=='
                ConfigurationNames = 'ASRMobilityService.localhost'
            }
                ReportServerWeb AzureAutomationDSC
            {
                ServerURL = 'https://we-agentservice-prod-1.azure-automation.net/accounts/4d54e439-8540-4c8f-8e0f-3d7afdda6746'
                RegistrationKey = 'KaTg5zTtI/gf8OGiN28L9FT2gMbowzOv3P9rouQfhxcR+42Mac80QKxOFXYLMdKxRdz8QtJiXLb7noG1U4zJ6g=='
            }

    }
}
metaconfig

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

この構成は、ローカル構成マネージャーがマネージャー自体を OMS Automation DSC に登録するように構成します。基本的には、エンジンの動作方法と構成のずれ (ApplyAndAutoCorrect) が発生した場合の動作を指示するとともに、必要な再起動が行われた場合に以降の構成を続行するように指示します。

これを実行すると、ノードは Automation DSC への登録を開始します。

![ノードの登録](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

portal.azure.com に戻ると、新しく登録したノードがポータルに表示されていることがわかります。

![ノードの登録](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

サーバーで、次の PowerShell コマンドレットを実行して、登録が正しく行われたことを確認できます。

```
Get-DscLocalConfigurationManager
```

構成がプルされてサーバーに適用された後で、次のコマンドレットを実行してこれを確認できます。

```
Get-DscConfigurationStatus
```

出力には、サーバーがその構成を正常にプルしていることが示されます。

![ノードの登録](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

さらに、モビリティ サービスのセットアップでは、‘<SystemDrive>\\ProgramData\\ASRSetupLogs’ に独自のログが配置されます。

これで完了です。Site Recovery で保護するコンピューター上にモビリティ サービスが正常にデプロイおよび登録され、必要なサービスが常に DSC で実行されるようになります。

![ノードの登録](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

これが管理サーバーによって検出された後で、保護の構成に進んで Site Recovery によるマシンでのレプリケーションを有効にできます。

## 分離された環境での DSC の使用

コンピューターがインターネットに接続されていない場合でも、DSC を使用して、保護するワークロード上にモビリティ サービスをデプロイおよび構成することができます。

あるいは、環境内で、OMS Automation DSC で使用できるものと同じ機能を提供する独自の DSC プル サーバーをインスタンス化できます。この場合、クライアントは、DSC エンドポイントに登録されると構成をプルします。ただし、プッシュを使用する別のオプションもあります。この場合、ローカルまたはリモートのどちらかにより手動で DSC 構成をマシンにプッシュします。

この例では computername のパラメーターを追加しており、リモートのファイルは保護対象のマシンからアクセスできるリモート共有に配置され、スクリプトの末尾で構成を設定してからターゲット コンピューターへの DSC 構成の適用を開始しています。

### 前提条件

·xPSDesiredStateConfiguration PowerShell モジュールがインストールされている

WMF 5.0 がインストールされている Windows マシンでは、ターゲット コンピューターで次のコマンドレットを実行するだけで xPSDesiredStateConfiguration モジュールをインストールできます。

```
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

また、WMF 4.0 を使用してモジュールを Windows コンピューターに配信する必要がある場合は、PowerShellGet (WMF 5.0) が存在するコンピューターでこのコマンドレットを実行して、モジュールをダウンロードおよび保存することもできます。

```
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

また WMF 4.0 では、次の更新プログラムがコンピューターにインストールされているかどうかも確認します。

https://www.microsoft.com/download/details.aspx?id=40749

WMF 5.0 または 4.0 がインストール済みの Windows コンピューターのどちらにも、次の構成をプッシュできます。

```
configuration ASRMobilityService {

    param (
    [Parameter(Mandatory=$true)]
    $computername
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = "C:\Temp\Mobility_service\passphrase.txt"
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role ' + '"' + $Role + '"' + ' /InstallLocation ' + '"' + $Install + '"' + ' /CSEndpoint ' + '"' + $CSEndpoint + '"' + ' /PassphraseFilePath ' +  '"' +$LocalPassphrase + '"'  

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $computername {      

        xRemoteFile Setup {
           URI = $RemoteFile
           DestinationPath = $TempDestination
           DependsOn = "[File]Directory"
            }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = "[File]Directory"
            }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = "[File]Directory"
            }

        File Directory {
            DestinationPath = "C:\Temp\ASRSetup"
            Type = "Directory"            
            }

        Archive ASRzip {
           Path = $TempDestination
           Destination = "C:\Temp\ASRSetup"
           DependsOn = "[xRemotefile]Setup"
           }

        Package Install {
            Path = "C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE"
            Ensure = "Present"
            Name = "Microsoft Azure Site Recovery mobility Service/Master Target Server"
            ProductId = "275197FC-14FD-4560-A5EB-38217F80CBD1"
            Arguments = $Arguments
            DependsOn = "[Archive]ASRzip"
            }

        Package AzureAgent {
            Path = "C:\Temp\AzureVmAgent.msi"
            Ensure = "Present"
            Name = "Windows Azure VM Agent - 2.7.1198.735"
            ProductId = "5CF4D04A-F16C-4892-9196-6025EA61F964"
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = "[Package]Install"
            }

        Service ASRvx {
            Name = "svagents"
            State = "Running"
            DependsOn = "[Package]Install"
            }

        Service ASR {
            Name = "InMage Scout Application Service"
            State = "Running"
            DependsOn = "[Package]Install"
            }

        Service AzureAgentService {
            Name = "WindowsAzureGuestAgent"
            State = "Running"
            DependsOn = "[Package]AzureAgent"
            }

        Service AzureTelemetry {
            Name = "WindowsAzureTelemetryService"
            State = "Running"
            DependsOn = "[Package]AzureAgent"
            }
    }
}
ASRMobilityService

Start-DscConfiguration -ComputerName $computername .\ASRMobilityService -wait -force -Verbose
```

独自の DSC プル サーバーを社内ネットワーク内でインスタンス化して OMS Automation DSC で使用できるのと同じ機能を模倣するには、https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396 のガイド参照してください。

## 省略可能: Azure Resource Manager テンプレートを使用した DSC 構成のデプロイ

この記事ではこれまで、独自の DSC 構成を作成して自動的にモビリティ サービスと Azure VM エージェントをデプロイし、これらが保護対象のするコンピューターで実行されるようにする方法に重点を置いてきました。こうした方法以外にも、この DSC 構成を新規または既存の Azure Automation アカウントにデプロイする Azure Resource Manager ## ## テンプレートがあります。このテンプレートの入力パラメーターを使用して、環境の変数が含まれる Automation 資産を作成できます。

デプロイ後は、このガイドの手順 4 を参照してコンピューターを追加できます。

このテンプレートは、次の操作を行います。

· 既存の OMS Automation アカウントの使用、または新規 OMS Automation アカウントの作成

· 次に対する入力パラメーターの受け取り:

	· ASRRemoteFile – the location where you have stored the Mobility Service setup
	· ASRPassphrase – the location where you have stored the passphrase.txt file
	· ASRCSEndpoint – the IP address of your Management server
	· Import xPSDesiredStateConfiguration PowerShell module
	· Create and compile the DSC configuration


上記の手順をすべて正しい順序で実行すると、コンピューターを保護対象として簡単に追加できるようになります。

デプロイの手順も含まれるテンプレートは、次の場所にあります。

https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC

PowerShell を使用したデプロイ:

```
$OMSAutomationRGname = 'KNOMS'

$DSCJobGuid = (New-Guid).Guid

New-AzureRmResourceGroupDeployment `
                                  -Name "DSC3" `
                                  -ResourceGroupName $OMSAutomationRGname `
                                  -TemplateFile https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json `
                                  -OMSAutomationAccountName KNOMSAA `
                                  -ASRRemoteFile "https://knrecstor01.blob.core.windows.net/asr/ASR.zip" `
                                  -ASRRemotePassphrase "https://knrecstor01.blob.core.windows.net/asr/passphrase.txt" `
                                  -ASRCSEndpoint '10.0.0.115' `
                                  -DSCJobGuid $DSCJobGuid `
                                  -Verbose
```

## 次のステップ:

モビリティ サービスのエージェントをデプロイした後は、引き続き仮想マシンの[レプリケーションを有効にする](site-recovery-vmware-to-azure.md#step-6-replicate-applications)ことができます。

<!---HONumber=AcomDC_0810_2016-->