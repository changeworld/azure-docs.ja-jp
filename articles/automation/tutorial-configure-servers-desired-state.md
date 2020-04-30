---
title: 目的の状態へのサーバーの構成と Azure Automation での誤差の管理
description: チュートリアル - サーバーの構成を Azure Automation State Configuration で管理する
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: a02c664ddf0802ad5ac306f98de14b7c0d5d7271
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678705"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>目的の状態にサーバーを構成して誤差を管理する

Azure Automation State Configuration を使うと、サーバーの構成を指定し、時間が経過してもサーバーが指定した状態を保つようにすることができます。

> [!div class="checklist"]
> - Azure Automation DSC によって管理する VM をオンボードする
> - Azure Automation に構成をアップロードする
> - 構成をノードの構成としてコンパイルする
> - ノードの構成を管理対象ノードに割り当てる
> - 管理対象ノードの準拠状態を確認する

このチュートリアルでは、IIS を VM に確実にインストールする簡単な [DSC 構成](/powershell/scripting/dsc/configurations/configurations)を使います。

>[!NOTE]
>この記事は、新しい Azure PowerShell Az モジュールを使用するために更新されました。 AzureRM モジュールはまだ使用でき、少なくとも 2020 年 12 月までは引き続きバグ修正が行われます。 Az モジュールと AzureRM の互換性の詳細については、「[Introducing the new Azure PowerShell Az module (新しい Azure PowerShell Az モジュールの概要)](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)」を参照してください。 Hybrid Runbook Worker での Az モジュールのインストール手順については、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)」を参照してください。 Automation アカウントについては、「[Azure Automation の Azure PowerShell モジュールを更新する方法](automation-update-azure-modules.md)」に従って、モジュールを最新バージョンに更新できます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

- Azure Automation アカウント。 Azure Automation 実行アカウントの作成手順については、 [Azure 実行アカウント](automation-sec-configure-azure-runas-account.md)に関するページをご覧ください。
- Windows Server 2008 R2 以降を実行している Azure Resource Manager VM (クラシックではない)。 VM の作成手順については、[Azure portal での最初の Windows 仮想マシンの作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md)に関するページを参照してください。
- Azure PowerShell モジュール バージョン 3.6 以降。 バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/azurerm/install-azurerm-ps)に関するページを参照してください。
- Desired State Configuration (DSC) に関する知識。 DSC については、「[Windows PowerShell Desired State Configuration の概要](/powershell/scripting/dsc/overview/overview)」をご覧ください。

## <a name="support-for-partial-configurations"></a>部分構成のサポート

Azure Automation State Configuration では[部分構成](/powershell/scripting/dsc/pull-server/partialconfigs)の使用がサポートされています。 このシナリオでは、DSC は複数の構成を別々に管理するように構成されており、各構成は Azure Automation から取得されます。 ただし、ノードに割り当てることができる構成はAutomation アカウントあたり 1 つだけです。 つまり、1 つのノードに 2 つの構成を使用している場合、2 つの Automation アカウントが必要になります。

プル サービスから部分構成を登録する方法の詳細については、[部分構成](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)に関するドキュメントを参照してください。

コードとしての構成を使用し、チームが連携してサーバーを共同で管理する方法の詳細については、「[CI/CD パイプラインでの DSC のロールについて](/powershell/scripting/dsc/overview/authoringadvanced)」を参照してください。

## <a name="log-in-to-azure"></a>Azure にログインする

[Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) コマンドレットを使用して Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>構成を作成して Azure Automation にアップロードする


テキスト エディターで次のように入力し、**TestConfig.ps1** としてローカルに保存します。

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

> [!NOTE]
> DSC リソースを提供するモジュールを複数インポートする必要があるより高度なシナリオでは、ご自分の構成にモジュールごとに `Import-DscResource` 行があることを確認してください。

[Import-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration?view=azps-3.7.0) コマンドレットを呼び出して、構成を Automation アカウントにアップロードします。

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>構成をノードの構成としてコンパイルする

ノードに DSC 構成を割り当てるには、先に DSC 構成をノードの構成としてコンパイルする必要があります。 「[DSC 構成](/powershell/scripting/dsc/configurations/configurations)」を参照してください。

[Start-AzAutomationDscCompilationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob?view=azps-3.7.0) コマンドレットを呼び出して、`TestConfig` 構成を、Automation アカウントの `TestConfig.WebServer` というノード構成に コンパイルします。

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>State Configuration によって管理される VM を登録する

Azure Automation State Configuration を使用すると、Azure VM (クラシックと Resource Manager の両方)、オンプレミスの VM、Linux マシン、AWS VM、オンプレミスの物理マシンを管理できます。 このトピックでは、Azure Resource Manager VM を登録する方法のみを説明します。 他の種類のマシンの登録について詳しくは、「[Azure Automation State Configuration による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」をご覧ください。

[Register-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationDscNode?view=azps-3.7.0) コマンドレットを呼び出して、VM を管理対象ノードとして Azure Automation State Configuration に登録します。 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>構成モードの設定を指定する

[Register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) コマンドレットを使用して、VM を管理対象ノードとして登録し、構成のプロパティを指定します。 たとえば、`ConfigurationMode` プロパティの値として `ApplyOnly` を指定することにより、マシンの状態を 1 回だけ適用するように指定できます。 State Configuration は初期チェック後に構成の適用を試みません。

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

また、`ConfigurationModeFrequencyMins`プロパティを使うことで、DSC が構成の状態をチェックする頻度も指定できます。 DSC 構成の設定について詳しくは、「[ローカル構成マネージャーの構成](/powershell/scripting/dsc/managing-nodes/metaConfig)」をご覧ください。

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>ノードの構成を管理対象ノードに割り当てる

コンパイル済みのノード構成を構成対象の VM 割り当てることができる状態になりました。

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

これにより、`TestConfig.WebServer` という名前のノード構成が、登録済みの DSC ノード `DscVm` に割り当てられます。 既定では、DSC ノードはノード構成に準拠していることを 30 分ごとにチェックされます。 準拠チェック間隔を変更する方法については、「[ローカル構成マネージャーの構成](/powershell/scripting/dsc/managing-nodes/metaConfig)」をご覧ください。

## <a name="check-the-compliance-status-of-a-managed-node"></a>管理対象ノードの準拠状態を確認する

[Get-AzAutomationDscNodeReport](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport?view=azps-3.7.0) コマンドレットを使用して、管理対象ノードの準拠状態に関するレポートを取得できます。

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>サービスからノードを削除する

Azure Automation State Configuration にノードを追加すると、Local Configuration Manager の設定はサービスに登録され、構成とマシンの構成に必要なモジュールがプルされるように設定されます。
サービスからノードを削除することを選択した場合は、Azure portal または Az コマンドレットを使用して削除できます。

> [!NOTE]
> サービスからノードを登録解除しても、Local Configuration Manager 設定のみが設定されるため、ノードはサービスに接続しなくなります。
> これは、現在ノードに適用されている構成には影響しません。
> 現在の構成を削除するには、[PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) を使用するか、ローカル構成ファイルを削除します (これが Linux ノードの唯一のオプションです)。

### <a name="azure-portal"></a>Azure portal

Azure Automation から、目次の **[State configuration (DSC)]\(状態の構成 (DSC)\)** をクリックします。
次に **[ノード]** をクリックしてサービスに登録されているノードの一覧を表示します。
削除するノードの名前をクリックします。
開いた [ノード] ビューで、 **[登録解除]** をクリックします。

### <a name="powershell"></a>PowerShell

PowerShell を使用して Azure Automation State Configuration サービスからノードを登録解除するには、コマンドレット [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0) のドキュメントに従ってください。

## <a name="next-steps"></a>次のステップ

- 使用を開始するには、「[Azure Automation State Configuration の使用開始](automation-dsc-getting-started.md)」をご覧ください。
- ノードをオンボードにする方法は、「[Azure Automation State Configuration による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」をご覧ください。
- DSC 構成をコンパイルしてターゲット ノードに割り当てることができるようにする方法の詳細については、「[Azure Automation State Configuration での構成のコンパイル](automation-dsc-compile.md)」をご覧ください。
- PowerShell コマンドレットのリファレンスについては、[Azure Automation State Configuration のコマンドレット](/powershell/module/azurerm.automation/#automation)に関するページをご覧ください。
- 料金情報については、[Azure Automation State Configuration の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。
- 継続的なデプロイ パイプラインで Azure Automation State Configuration を使う例については、「[Automation State Configuration と Chocolatey を使用した仮想マシンへの継続的なデプロイ](automation-dsc-cd-chocolatey.md)」をご覧ください。
