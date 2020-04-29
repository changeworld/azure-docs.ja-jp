---
title: Azure Automation State Configuration による管理のためのマシンのオンボード
description: Azure Automation State Configuration による管理のためのマシンの設定方法
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: d4e008a0bd43f10b01d78a1c388f1ca6fee983ab
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457741"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Azure Automation State Configuration による管理のためのマシンのオンボード

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Azure Automation State Configuration でマシンを管理する理由

Azure Automation State Configuration は、あらゆるクラウドまたはオンプレミスのデータセンターにある Desired State Configuration (DSC) ノードの管理構成サービスです。 これにアクセスするには、Azure portal で **[構成管理]** の **[状態の構成 (DSC)]** を選択します。 

このサービスを使用すると、セキュリティで保護された中央の場所から、数千台のマシンにまたがるスケーラビリティを迅速かつ容易に実現できます。 マシンのオンボード、それらへの宣言型構成の割り当て、さらに指定した望ましい状態への各マシンの準拠を示すレポートの表示を容易に行うことができます。

Azure Automation State Configuration サービスは、Azure Automation Runbook が PowerShell スクリプトに対応する DSC に対応します。 つまり、Azure Automation を使用して、PowerShell スクリプトを管理するのと同様に DSC 構成も管理できます。 Azure Automation State Configuration を使用した場合の利点については、[Azure Automation State Configuration の概要](automation-dsc-overview.md)に関するページを参照してください。

以下のさまざまなマシンを管理する際に、Azure Automation State Configuration を使用できます。

- Azure の仮想マシン
- Azure Virtual Machines (クラシック)
- オンプレミスの物理または仮想 Windows マシン、または Azure 以外のクラウド (AWS EC2 インスタンスなど)
- オンプレミス、Azure、または Azure 以外のクラウド内の物理/仮想 Linux マシン

クラウドからマシン構成を管理する準備ができていない場合は、Azure Automation State Configuration をレポート専用のエンドポイントとして使用できます。 この機能により、DSC 経由で構成を設定 (プッシュ) し、Azure Automation でレポートの詳細を表示できます。

> [!NOTE]
> インストールされている Azure VM Desired State Configuration 拡張機能のバージョンが 2.70 を超えている場合は、Azure Automation State Configuration での Azure VM の管理が追加料金なしで含まれています。 詳細については、[**Automation 価格に関するページ**](https://azure.microsoft.com/pricing/details/automation/)を参照してください。

この記事の以降のセクションでは、上に一覧表示されているマシンを Azure Automation State Configuration にオンボードする方法の概要について説明します。

>[!NOTE]
>この記事は、新しい Azure PowerShell Az モジュールを使用するために更新されました。 AzureRM モジュールはまだ使用でき、少なくとも 2020 年 12 月までは引き続きバグ修正が行われます。 Az モジュールと AzureRM の互換性の詳細については、「[Introducing the new Azure PowerShell Az module (新しい Azure PowerShell Az モジュールの概要)](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)」を参照してください。 Hybrid Runbook Worker での Az モジュールのインストール手順については、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)」を参照してください。 Automation アカウントについては、「[Azure Automation の Azure PowerShell モジュールを更新する方法](automation-update-azure-modules.md)」に従って、モジュールを最新バージョンに更新できます。

## <a name="onboarding-azure-vms"></a>Azure VM のオンボード

Azure Automation State Configuration では、Azure portal、Azure Resource Manager テンプレート、または PowerShell を使用して、構成管理のために Azure VM を容易にオンボードできます。 Azure VM Desired State Configuration 拡張機能は内部で、しかも管理者が VM にリモート接続しなくても、VM を Azure Automation State Configuration に登録します。 Azure 拡張機能は非同期的に実行されるため、その進行状況の追跡やトラブルシューティングの手順は、この記事の「[Azure 仮想マシンのオンボードに関するトラブルシューティング](#troubleshooting-azure-virtual-machine-onboarding)」のセクションで説明されています。

> [!NOTE]
>DSC の Linux ノードへの配置では **/tmp** フォルダーが使用されます。 `nxautomation` などのモジュールは、適切な場所にインストールする前に、確認のために一時的にダウンロードされます。 モジュールが正しくインストールされるようにするには、Linux 用 Log Analytics エージェントに **/tmp** フォルダーに対する読み取り/書き込みアクセス許可が必要です。<br><br>
>Linux 用 Log Analytics エージェントは `omsagent` ユーザーとして実行されます。 `omsagent` ユーザーに書き込みアクセス許可を付与するには、コマンド `setfacl -m u:omsagent:rwx /tmp` を実行します。

### <a name="onboard-a-vm-using-azure-portal"></a>Azure portal を使用して VM をオンボードする

[Azure portal](https://portal.azure.com/) を使用して Azure VM を Azure Automation State Configuration にオンボードするには:

1. VM をオンボードする Azure Automation アカウントに移動します。 

2. [状態の構成] ページで、 **[ノード]** タブを選択し、 **[追加]** をクリックします。

3. オンボードする VM を選択します。

4. マシンに PowerShell Desired State 拡張機能がインストールされておらず、電源の状態が実行中である場合は、 **[接続]** をクリックします。

5. **[登録]** で、ユース ケースに必要な [PowerShell DSC Local Configuration Manager の値](/powershell/scripting/dsc/managing-nodes/metaConfig)を入力します。 必要に応じて、VM に割り当てるノード構成を入力できます。

![onboarding](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して VM をオンボードする

Azure Resource Manager テンプレートを使用して VM を配置し、Azure Automation State Configuration にオンボードできます。 Azure Automation State Configuration に既存の VM をオンボードする例のテンプレートについては、「[Server managed by Desired State Configuration service (Desired State Configuration サービスによって管理されるサーバー)](https://azure.microsoft.com/resources/templates/101-automation-configuration/)」を参照してください。 仮想マシン スケール セットを管理する場合のテンプレートの例については、「[Azure Automation によって管理される仮想マシンスケール セットの構成](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)」を参照してください。

### <a name="onboard-machines-using-powershell"></a>PowerShell を使用してマシンをオンボードする

VM を Azure Automation State Configuration にオンボードするには、PowerShell の [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) コマンドレットを使用できます。 

> [!NOTE]
>`Register-AzAutomationDscNode` コマンドレットは、Windows 拡張機能のみをトリガーするため、現在は Windows を実行しているマシンにのみ実装されています。

### <a name="register-vms-across-azure-subscriptions"></a>Azure サブスクリプションにまたがって VM を登録する

他の Azure サブスクリプションから VM を登録するための最適な方法は、Azure Resource Manager 配置テンプレートで DSC 拡張機能を使用することです。 例については、「[Azure Resource Manager テンプレートを使用した Desired State Configuration 拡張機能](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)」を参照してください。

このテンプレートでパラメーターとして使用する登録キーと登録 URL を見つけるには、この記事の「[登録を使用した安全なオンボード](#onboarding-securely-using-registration)」のセクションを参照してください。

## <a name="onboarding-physicalvirtual-windows-machines"></a>物理または仮想 Windows マシンのオンボード

オンプレミスまたは他のクラウド環境 (AWS EC2 など) で実行されている Windows サーバーを、Azure Automation State Configuration にオンボードできます。 これらのサーバーには、[Azure への発信アクセス](automation-dsc-overview.md#network-planning)が必要です。

1. Azure Automation State Configuration にオンボードするマシンに最新バージョンの [WMF 5](https://aka.ms/wmf5latest) がインストールされていることを確認します。 さらに、オンボード操作に使用しているコンピューターに WMF 5 がインストールされている必要があります。
1. 「[DSC メタ構成の生成](#generating-dsc-metaconfigurations)」のセクションの指示に従って、必要な DSC メタ構成が含まれたフォルダーを作成します。 
1. 次のコマンドレットを使用して、オンボードするマシンに PowerShell DSC メタ構成をリモートで適用します。 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. PowerShell DSC メタ構成をリモートで適用できない場合は、オンボードするマシンに **metaconfigurations** フォルダーをコピーします。 次に、マシンでローカルに [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) を呼び出すコードを追加します。
1. Azure portal またはコマンドレットを使用して、オンボードするマシンが Azure Automation アカウントで登録されている State Configuration ノードとして表示されることを確認します。

## <a name="onboarding-physicalvirtual-linux-machines"></a>物理または仮想 Linux マシンのオンボード

オンプレミスまたはその他のクラウド環境で実行されている Linux サーバーを Azure Automation State Configuration にオンボードできます。 これらのサーバーには、[Azure への発信アクセス](automation-dsc-overview.md#network-planning)が必要です。

1. Azure Automation State Configuration にオンボードするマシンに最新バージョンの [PowerShell Desired State Configuration for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) がインストールされていることを確認します。
2. [PowerShell DSC Local Configuration Manager の既定値](/powershell/scripting/dsc/managing-nodes/metaConfig4)がユース ケースに一致し、Azure Automation State Configuration からのプルとそこへの報告の両方を行うようにマシンをオンボードする場合:

   - Azure Automation State Configuration にオンボードする各 Linux マシンで、`Register.py` を使用して、PowerShell DSC Local Configuration Manager の既定値でオンボードします。

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Automation アカウントの登録キーと登録 URL を見つけるには、この記事の「[登録を使用した安全なオンボード](#onboarding-securely-using-registration)」のセクションを参照してください。

3. PowerShell DSC Local Configuration Manager (LCM) の既定値がユース ケースに一致しない場合、または Azure Automation State Configuration への報告のみを行うマシンをオンボードする場合は、手順 4. ～ 7. に従います。 それ以外の場合は、直接手順 7. に進みます。

4. 「[DSC メタ構成の生成](#generating-dsc-metaconfigurations)」のセクションの指示に従って、必要な DSC メタ構成が含まれたフォルダーを生成します。

5. オンボードに使用されるマシンに最新バージョンの [WMF 5](https://aka.ms/wmf5latest) がインストールされていることを確認します。

6. 次のようにコードを追加して、オンボードするマシンに PowerShell DSC メタ構成をリモートで適用します。

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. PowerShell DSC メタ構成をリモートで適用できない場合は、リモート マシンに対応するメタ構成を手順 4. で説明されているフォルダーから Linux マシンにコピーします。

8. Azure Automation State Configuration にオンボードする各 Linux マシンで `Set-DscLocalConfigurationManager.py` をローカルで呼び出すコードを追加します。

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Azure portal またはコマンドレットを使用して、オンボードするマシンが Azure Automation アカウントで登録されている DSC ノードとして表示されるようになったことを確認します。

## <a name="generating-dsc-metaconfigurations"></a>DSC メタ構成の生成

マシンを Azure Automation State Configuration にオンボードするために、[DSC メタ構成](/powershell/scripting/dsc/managing-nodes/metaConfig)を生成できます。 この構成は、DSC エージェントに Azure Automation State Configuration からのプルまたはそこへの報告、あるいはその両方を行うよう指示します。 Azure Automation State Configuration の DSC メタ構成は、PowerShell DSC 構成または Azure Automation PowerShell コマンドレットのどちらかを使用して生成できます。

> [!NOTE]
> DSC メタ構成には Automation アカウントにマシンをオンボードするために必要な管理用の機密データが含まれています。 作成した DSC メタ構成は適切に保護し、使用後は削除してください。

メタ構成のプロキシ サポートは、Windows PowerShell DSC エンジンである LCM によって制御されます。 LCM はすべてのターゲット ノードで実行され、DSC メタ構成スクリプトに含まれている構成リソースを呼び出す役割を果たします。 必要に応じて `ConfigurationRepositoryWeb`、`ResourceRepositoryWeb`、`ReportServerWeb` ブロックにプロキシ URL とプロキシ資格情報の定義を追加することによって、メタ構成にプロキシ サポートを含めることができます。 「[ローカル構成マネージャーの構成](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7)」を参照してください。

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>DSC 構成を使用して DSC メタ構成を生成する

1. ローカル環境内のマシンで、管理者として VSCode (またはお気に入りのエディター) を開きます。 マシンには最新バージョンの [WMF 5](https://aka.ms/wmf5latest) がインストールされている必要があります。
1. 次のスクリプトをローカルにコピーします。 このスクリプトには、メタ構成を作成するための PowerShell DSC 構成と、メタ構成の作成を開始するためのコマンドが含まれています。

    > [!NOTE]
    > Azure portal では、State Configuration ノードの構成名は大文字と小文字が区別されます。 大文字と小文字が一致しない場合、そのノードは **[ノード]** タブに表示されません。

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl       = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. お使いの Automation アカウントの登録キーと URL のほか、オンボードするマシンの名前を入力します。 その他のパラメーターはすべて省略可能です。 Automation アカウントの登録キーと登録 URL を見つけるには、「[登録を使用した安全なオンボード](#onboarding-securely-using-registration)」のセクションを参照してください。

1. マシンが Azure Automation State Configuration に DSC 状態情報を報告するが、構成または PowerShell モジュールをプルしないようにする場合は、`ReportOnly` パラメーターを true に設定します。

1. `ReportOnly` が設定されていない場合、マシンは Azure Automation State Configuration に DSC 状態情報を報告し、構成または PowerShell モジュールをプルします。 `ConfigurationRepositoryWeb`、`ResourceRepositoryWeb`、`ReportServerWeb` ブロックで、それに応じてパラメーターを設定します。

1. スクリプトを実行します。 (管理者として) オンボードするマシンの PowerShell DSC メタ構成が含まれた、**DscMetaConfigs** という名前の作業ディレクトリ フォルダーが作成されます。

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Azure Automation コマンドレットを使用して DSC メタ構成を生成する

PowerShell DSC LCM の既定値がユース ケースに一致し、Azure Automation State Configuration からのプルとそこへの報告の両方を行うようにマシンをオンボードする場合は、Azure Automation コマンドレットを使用して、必要な DSC メタ構成をより簡単に生成できます。

1. ローカル環境内のマシンで、管理者として PowerShell コンソールまたは VSCode を開きます。
2. [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) を使用して、Azure Resource Manager に接続します。
3. ノードを設定している Automation アカウントから、オンボードするマシンの PowerShell DSC メタ構成をダウンロードします。

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. (管理者として) オンボードするマシンの PowerShell DSC メタ構成が含まれた、**DscMetaConfigs** フォルダーが作成されます。

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>登録を使用した安全なオンボード

WMF 5 DSC 登録プロトコルを使用すると、マシンを Azure Automation アカウントに安全にオンボードできます。 このプロトコルでは、DSC ノードを PowerShell DSC プルまたはレポート サーバー (Azure Automation State Configuration を含む) に対して認証できます。 各ノードを登録 URL でサーバーに登録し、登録キーを使用して認証します。 登録中に、DSC ノードと DSC プルまたはレポート サーバーは、そのノードを登録後のサーバーに対して認証するために使用する一意の証明書をネゴシエートします。 このプロセスにより、オンボードされたノードが互いに偽装することが防止されます (たとえば、ノードが侵害され、悪意を持って動作している場合)。 登録の後、登録キーはもう認証には使用されず、ノードから削除されます。

State Configuration 登録プロトコルに必要な情報は、Azure Portal の **[アカウント設定]** の **[キー]** から取得できます。 

![Azure の自動化キーと URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- 登録 URL は、[キー] ページにある URL のフィールドです。
- 登録キーは、[キー] ページの **[プライマリ アクセス キー]** フィールドまたは **[セカンダリ アクセス キー]** フィールドの値です。 いずれかのキーを使用できます。

セキュリティを強化するために、[キー] ページで Automation アカウントのプライマリおよびセカンダリ アクセス キーをいつでも再生成できます。 キーの再生成により、将来のノード登録では以前のキーを使用できなくなります。

## <a name="re-registering-a-node"></a>ノードの再登録

マシンを Azure Automation State Configuration に DSC ノードとして登録した後、将来、次のようないくつか理由でそのノードの再登録が必要になります。

- **証明書の更新。** Windows Server 2019 より前のバージョンの Windows Server では、1 年後に有効期限が切れる認証用の一意の証明書を各ノードが自動的にネゴシエートします。 更新せずに証明書の有効期限が切れた場合、そのノードは Azure Automation と通信できなくなり、`Unresponsive` とマークされます。 PowerShell DSC 登録プロトコルでは現在、有効期限が近づいている証明書を自動的に更新することができないため、1 年後にノードを再登録する必要があります。 再登録の前に、各ノードで WMF 5 RTM が実行されていることを確認してください。 

    証明書の期限切れ日時から 90 日以内に、または証明書の期限切れ日時が過ぎた後に再登録を実行すると、新しい証明書が生成され、使用されます。 Windows Server 2019 以降には、この問題への解決策が含まれています。

- **DSC LCM 値の変更。** ノードの初期登録中に設定された [PowerShell DSC LCM 値](/powershell/scripting/dsc/managing-nodes/metaConfig4) (`ConfigurationMode` など) の変更が必要になることがあります。 現在、これらの DSC エージェント値は再登録でしか変更できません。 1 つの例外は、ノードに割り当てられているノード構成値です。 これは、Azure Automation DSC で直接変更できます。

ノードは、このドキュメントで説明されているオンボード方法のいずれかを使用して、そのノードを最初に登録したときと同様に再登録できます。 ノードを再登録する前に、Azure Automation State Configuration のノードの登録を解除する必要はありません。

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Azure 仮想マシンのオンボードに関するトラブルシューティング

Azure Automation State Configuration を使用すると、構成管理のための Azure Windows VM を簡単にオンボードできます。 内部で、Azure VM Desired State Configuration 拡張機能を使用して、VM を Azure Automation State Configuration に登録します。 Azure VM Desired State Configuration 拡張機能は非同期的に実行されるため、その進行状況の追跡やその実行のトラブルシューティングが重要になる場合があります。

> [!NOTE]
> Azure VM Desired State Configuration 拡張機能を使用する Azure Automation State Configuration に Azure Windows VM をオンボードするいずれの方法でも、Azure Automation に登録済みとして表示されるまでに最大 1 時間かかる場合があります。 この遅延は、VM を Azure Automation State Configuration にオンボードするために必要な WMF 5 の Azure VM Desired State Configuration 拡張機能による VM へのインストールのためです。

Azure VM Desired State Configuration 拡張機能の状態をトラブルシューティングまたは表示するには:

1. Azure portal で、オンボードされる VM に移動します。
2. **[設定]** の **[拡張機能]** をクリックします。 
3. 次に、オペレーティング システムに応じて **[DSC]** または **[DSCForLinux]** を選択します。 
4. 詳細は、 **[詳細な状態の表示]** をクリックして表示できます。

トラブルシューティングの詳細については、「[Azure Automation Desired State Configuration (DSC) の問題をトラブルシューティングする](./troubleshoot/desired-state-configuration.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- 使用を開始するには、「[Azure Automation State Configuration の使用開始](automation-dsc-getting-started.md)」をご覧ください。
- DSC 構成をコンパイルしてターゲット ノードに割り当てることができるようにする方法の詳細については、「[Azure Automation State Configuration での構成のコンパイル](automation-dsc-compile.md)」をご覧ください。
- PowerShell コマンドレットのリファレンスについては、「[Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)」をご覧ください。
- 料金情報については、[Azure Automation State Configuration の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。
- 継続的配置パイプラインで Azure Automation State Configuration を使用する例については、[使用例: Azure Automation State Configuration と Chocolatey を使用した仮想マシンへの継続的配置](automation-dsc-cd-chocolatey.md)に関するページを参照してください。
