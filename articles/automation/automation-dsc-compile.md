---
title: Azure Automation State Configuration での構成のコンパイル
description: この記事では、Azure Automation の Desired State Configuration (DSC) 構成をコンパイルする方法について説明します。
services: automation
ms.subservice: dsc
ms.date: 09/10/2018
ms.topic: conceptual
ms.openlocfilehash: a4bdd28d2ad8f692b561d414af15b90b1609bac4
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462124"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Azure Automation State Configuration での DSC 構成のコンパイル

Azure Automation State Configuration を使用して、Desired State Configuration (DSC) 構成をコンパイルするには、Azure portal で行う方法と、Windows PowerShell を使用する 2 通りの方法があります。 次の表は、各方法の特徴に基づいて、どちらの方法をどのような場合に使用するか決定するのに役立ちます。

- Azure State Configuration コンパイル サービス
  - 対話型ユーザー インターフェイスを使用する初心者向けの方法
   - ジョブの状態を簡単に追跡できる

- Windows PowerShell
  - ローカルのワークステーションまたはビルド サービスでの Windows PowerShell からの呼び出し
  - 開発テスト パイプラインとの統合
  - 複雑なパラメーター値の提供
  - 大規模なノード データと非ノード データの操作
  - パフォーマンスの大幅な向上

コンパイルの詳細については、「[Azure Resource Manager テンプレートを使用した Desired State Configuration 拡張機能](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details)」を参照してください。

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Azure State Configuration での DSC 構成のコンパイル

### <a name="portal"></a>ポータル

1. Automation アカウントから、 **[状態の構成 (DSC)]** をクリックします。
1. **[構成]** タブをクリックして、コンパイルする構成の名前をクリックします。
1. **[コンパイル]** をクリックします。
1. 構成にパラメーターが含まれていない場合は、コンパイルの実行を確認するメッセージが表示されます。 構成にパラメーターが含まれている場合は、 **[構成のコンパイル]** ブレードが開くので、パラメーター値を入力できます。
1. [コンパイル ジョブ] ページが開くので、コンパイル ジョブの状態を追跡できます。 また、このページを使用して、ジョブが Azure Automation State Configuration プル サーバーに配置するノード構成 (MOF 構成ドキュメント) を追跡することもできます。

### <a name="azure-powershell"></a>Azure PowerShell

[Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) を使用して、Windows PowerShell を使用したコンパイルを開始できます。 次のサンプル コードでは、SampleConfig という名前の DSC 構成のコンパイルを開始します。

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

**Start-AzAutomationDscCompilationJob** により、ジョブの状態を追跡するのに使用できるコンパイル ジョブ オブジェクトが返されます。 このコンパイル ジョブ オブジェクトは、[Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) と共に使用してコンパイル ジョブの状態を確認し、[Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) と共に使用してそのストリーム (出力) を表示することができます。 次のサンプル コードでは、SampleConfig 構成のコンパイルを開始し、コンパイルが完了するまで待機した後、ストリームを表示します。

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>基本パラメーターの宣言

DSC 構成のパラメーターの宣言 (パラメーターの種類、プロパティなど) は、Azure Automation Runbook と同じように動作します。 Runbook のパラメーターの詳細については、「 [Azure Automation での Runbook を開始する](automation-starting-a-runbook.md) 」を参照してください。

次の例では、コンパイル時に生成される、**ParametersExample.sample** ノード構成のプロパティの値を指定するために、**FeatureName** と **IsPresent** という 2 つのパラメーターを使用します。

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

Azure Automation State Configuration ポータルまたは Azure PowerShell で、基本パラメーターを使用する DSC 構成をコンパイルできます。

#### <a name="portal"></a>ポータル

ポータルで、 **[コンパイル]** をクリックすると、パラメーター値を入力できます。

![構成のコンパイル パラメーター](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell には、[ハッシュテーブル](/powershell/module/microsoft.powershell.core/about/about_hash_tables)形式のパラメーターが必要です。キーはパラメーター名と一致し、値はパラメーター値と一致します。

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

PSCredentials をパラメーターとして渡す方法の詳細については、以下の「[資格情報資産](#credential-assets)」を参照してください。

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Azure Automation で複合リソースを含む構成をコンパイルする

**複合リソース**機能によって、構成の内側に入れ子のリソースとして DSC 構成を使用することができます。 これにより、1 つのリソースに複数の構成を適用することができます。 「[Composite resources: Using a DSC configuration as a resource](/powershell/scripting/dsc/resources/authoringresourcecomposite)」 (複合リソース: リソースとしての DSC 構成の使用) を参照して、複合リソースの詳細について確認してください。

> [!NOTE]
> 複合リソースを含む構成を正しくコンパイルするには、最初に、その複合リソースが依存する DSC リソースを Azure Automation に確実にインポートする必要があります。

DSC 複合リソースの追加は、Azure Automation に PowerShell モジュールを追加するのと同じです。 このプロセスは、「[Azure Automation でモジュールを管理する](/azure/automation/shared-resources/modules)」に記載されています。

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Azure Automation で構成をコンパイルするときに ConfigurationData を管理する

**ConfigurationData** 機能によって、PowerShell DSC を使用しているときに、環境固有の構成から構造上の構成を分離することができます。 ConfigurationData の詳細については、[PowerShell DSC で "環境" から "構造" を分離する](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx)に関する記事を参照してください。

> [!NOTE]
> ConfigurationData は、Azure PowerShell を使用して Azure Automation State Configuration でコンパイルする場合に使用できますが、Azure portal では使用できません。

次の DSC 構成の例では、$ConfigurationData および $AllNodes キーワードを介して ConfigurationData を使用します。 この例では、[xWebAdministration](https://www.powershellgallery.com/packages/xWebAdministration/) モジュールも必要です。

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

上記の DSC 構成は、Windows PowerShell を使用してコンパイルできます。 以下のスクリプトでは、2 つのノード構成が Azure Automation State Configuration プル サービスに追加されます。ConfigurationDataSample.MyVM1 と ConfigurationDataSample.MyVM3 です。

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>コンパイル時に Azure Automation の資産を操作する

資産の参照方法は、Azure Automation State Configuration と Runbook の両方で同じです。 詳細については、「

- [証明書](automation-certificates.md)
- [接続](automation-connections.md)
- [資格情報](automation-credentials.md)
- [変数](automation-variables.md)

#### <a name="credential-assets"></a>資格情報資産

Azure Automation の DSC 構成は、**Get-AutomationPSCredential** コマンドレットを使用して Automation の資格情報資産を参照できます。 構成に PSCredential 型のパラメーターがある場合、Azure Automation の資格情報資産の文字列名をコマンドレットに渡して資格情報を取得することで、**Get-AutomationPSCredential** を使用できます。 その後、そのオブジェクトを PSCredential オブジェクトを必要とするパラメーターに使用することができます。 バックグラウンドで、この名前の Azure Automation 資格情報資産が取得され、構成に渡されます。 次の例で、この操作の動作を示します。

ノード構成で資格情報を安全に保持するには、ノード構成 MOF ファイルの資格情報を暗号化する必要があります。 ノード構成 MOF の生成時にプレーン テキストで資格情報を出力するためのアクセス許可があることを PowerShell DSC に明示する必要があります。 PowerShell DSC では、コンパイル ジョブによって生成された MOF ファイル全体を Azure Automation が暗号化することを認識していません。

構成データを使用して、生成されたノード構成 MOF に資格情報をプレーン テキストで出力するアクセス許可を PowerShell DSC に付与するには、`PSDscAllowPlainTextPassword = $true` を渡します。 この情報は、DSC 構成に表示され、資格情報を使用するノード ブロック名ごとに、ConfigurationData を介して渡すことができます。

次の例は、Automation 資格情報資産を使用する DSC 構成の例です。

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

PowerShell を使用して上記の DSC 構成をコンパイルできます。 

以下の PowerShell では、2 つのノード構成が Azure Automation State Configuration プル サーバーに追加されます。CredentialSample.MyVM1 と CredentialSample.MyVM2 です。

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

>[!NOTE]
>コンパイルが完了すると、次のようなエラーが表示される場合があります。" **"Microsoft.PowerShell.Management" スナップインが既にインポートされているため、"Microsoft.PowerShell.Management" モジュールがインポートされませんでした。** " この警告は無視してかまいません。

## <a name="compiling-a-dsc-configuration-in-windows-powershell"></a>Windows PowerShell で DSC 構成をコンパイルする

Windows PowerShell に DSC 構成をコンパイルする手順は、PowerShell DSC のドキュメント、「[構成の作成、コンパイル、適用](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)」に記述されています。
プロセスは、開発者のワークステーションから、または [Azure DevOps](https://dev.azure.com) などのビルド サービスから実行できます。
結果として得られたノード構成の MOF を Azure State Configuration サービスに直接インポートできます。 

>[!NOTE]
>ノード構成ファイルを Azure Automation にインポートするには、ファイル サイズを 1 MB 以下にする必要があります。

Azure の外部でコンパイルしたノード構成 (MOF) をインポートすることもできます。 このアプローチには、パフォーマンスや信頼性など、複数の利点があります。

Windows PowerShell でのコンパイルには、構成コンテンツに署名するオプションが用意されています。DSC エージェントにより、署名されたノード構成が管理対象ノードでローカルに検証されます。 検証によって、ノードに適用されている構成が承認済みのソースからのものであることが保証されます。 ノード構成の署名の詳細については、[WMF 5.1 の機能強化 - 構成とモジュールに署名する方法](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)に関するページを参照してください。

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Azure portal でノード構成をインポートする

1. [Automation アカウント] から、 **[構成管理]** の **[状態の構成 (DSC)]** をクリックします。
1. [状態の構成 (DSC)] ページで **[構成]** タブをクリックして、 **[+ 追加]** をクリックします。
1. [インポート] ページで、 **[ノード構成ファイル]** テキストボックスの横にあるフォルダー アイコンをクリックして、ローカル コンピューター上のノード構成ファイル (MOF) を参照します。

   ![ローカル ファイルの参照](./media/automation-dsc-compile/import-browse.png)

1. **[構成名]** テキスト ボックスに名前を入力します。 この名前は、ノード構成のコンパイル元の構成の名前と一致する必要があります。
1. **[OK]** をクリックします。

### <a name="import-a-node-configuration-with-azure-powershell"></a>Azure PowerShell でノード構成をインポートする

[Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) コマンドレットを使用して、ノード構成を Automation アカウントにインポートできます。

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>次のステップ

- 使用を開始するには、「[Azure Automation State Configuration の使用開始](automation-dsc-getting-started.md)」をご覧ください。
- DSC 構成をコンパイルしてターゲット ノードに割り当てることができるようにする方法の詳細については、「[Azure Automation State Configuration での構成のコンパイル](automation-dsc-compile.md)」をご覧ください。
- PowerShell コマンドレットのリファレンスについては、[Azure Automation State Configuration のコマンドレット](/powershell/module/az.automation)に関するページをご覧ください。
- 料金情報については、[Azure Automation State Configuration の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。
- 継続的なデプロイ パイプラインで Azure Automation State Configuration を使う例については、「[Automation State Configuration と Chocolatey を使用した仮想マシンへの継続的なデプロイ](automation-dsc-cd-chocolatey.md)」をご覧ください。
