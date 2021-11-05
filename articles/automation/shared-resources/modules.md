---
title: Azure Automation でモジュールを管理する
description: この記事では、PowerShell モジュールを使用して、Runbook と DSC 構成の DSC リソースでコマンドレットを有効にする方法について説明します。
services: automation
ms.subservice: shared-capabilities
ms.date: 11/01/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 733cc6cb4b783a379c20328f0a31a4373ee3e372
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131427235"
---
# <a name="manage-modules-in-azure-automation"></a>Azure Automation でモジュールを管理する

Azure Automation では、複数の PowerShell モジュールを使用して、Runbook と DSC 構成の DSC リソースでコマンドレットを有効にすることができます。 サポートされているモジュールは次のとおりです。

* [Azure PowerShell Az.Automation](/powershell/azure/new-azureps-module-az)。
* [Azure PowerShell AzureRM.Automation](/powershell/module/azurerm.automation/)。
* その他の PowerShell モジュール。
* 内部の `Orchestrator.AssetManagement.Cmdlets` モジュール。
* Python 2 モジュール。
* 作成したカスタム モジュール。

Automation アカウントを作成すると、Azure Automation は既定で一部のモジュールをインポートします。 「[既定のモジュール](#default-modules)」を参照してください。

## <a name="sandboxes"></a>サンドボックス

Automation が Runbook と DSC コンパイル ジョブを実行すると、モジュールがサンドボックスに読み込まれ、そこで Runbook を実行し、DSC 構成をコンパイルできるようになります。 また、Automation は、DSC リソースを自動的に DSC プル サーバー上のモジュールに配置します。 マシンは、DSC 構成を適用するときに、そのリソースをプルできます。

クラウド サンドボックスでは、最大 48 のシステム呼び出しがサポートされ、他のすべての呼び出しはセキュリティ上の理由で制限されます。 資格情報の管理や一部のネットワークなどのその他の機能は、クラウド サンドボックスではサポートされません。

含まれるモジュールとコマンドレットの数が多いため、サポートされていない呼び出しを行うコマンドレットを事前に把握することは困難です。 一般的に、コマンドレットのうち、昇格されたアクセス権を必要とするもの、パラメーターとして資格情報を必要とするもの、ネットワークに関連するものには問題が見られます。 AIPService PowerShell モジュールの [Connect-AipService](/powershell/module/aipservice/connect-aipservice) や DNSClient モジュールの [Resolve-DnsName](/powershell/module/dnsclient/resolve-dnsname) など、フルスタックのネットワーク操作を実行するコマンドレットは、サンドボックスではサポートされません。

これらは、サンドボックスに関する既知の制限事項です。 推奨される対処法は、[Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md) をデプロイすること、または [Azure Functions](../../azure-functions/functions-overview.md) を使用することです。

> [!IMPORTANT] 
> Az モジュールを使用して実行するように設計されたスクリプトには、キーワード "AzureRm" を含めないでください。 たとえコメントであっても、このキーワードを含めると、AzureRm が読み込まれ、Az モジュールと競合する可能性があります。

## <a name="default-modules"></a>既定のモジュール

新しい Automation アカウントにはすべて、最新バージョンの PowerShell Az モジュールが既定でインポートされています。 Az モジュールは AzureRM の置き換えであり、Azure で使用するための推奨されるモジュールです。 新しい Automation アカウントの **既定のモジュール** には、既存の 24 の AzureRM モジュールと 60 以上の Az モジュールが含まれています。

Automation アカウントのユーザーがモジュールを最新の Az モジュールに更新するためのネイティブ オプションがあります。 この操作では、すべてのモジュール依存関係をバックエンドで処理するため、モジュールを[手動で](../automation-update-azure-modules.md#update-az-modules)更新したり、Runbook を実行して [Azure モジュールを更新](../automation-update-azure-modules.md#obtain-a-runbook-to-use-for-updates)したりする手間が省けます。  

既存の Automation アカウントに AzureRM モジュールしかない場合は、[[Az モジュールの更新]](../automation-update-azure-modules.md#update-az-modules) オプションにより、ユーザーが選択したバージョンの Az モジュールで Automation アカウントが更新されます。  

既存の Automation アカウントに AzureRM と一部の Az モジュールがある場合は、そのオプションにより、残りの Az モジュールがその Automation アカウントにインポートされます。 既存の Az モジュールが優先されるため、更新操作でこれらのモジュールは更新されません。 これは、Runbook によって使用されているモジュールを誤って更新することで、モジュールの更新操作が Runbook の実行エラーを発生させることがないようにするためです。 このシナリオで推奨される方法は、まず既存の Az モジュールを削除し、その後に更新操作を実行して、最新の Az モジュールが Automation アカウントにインポートされるようにすることです。 既定ではインポートされない、このようなモジュールの種類は **[カスタム]** と呼ばれます。  **[カスタム]** モジュールは、常に **既定の** モジュールより優先されます。  

例: Az モジュール 6.3.0 によって提供されるバージョン 2.3.0 でインポートされた `Az.Aks` モジュールが既にあり、Az モジュールを最新の 6.4.0 バージョンに更新しようとしているとします。 更新操作では、`Az.Aks` を除き、すべての Az モジュールが 6.4.0 パッケージからインポートされます。 最新バージョンの `Az.Aks` を使用するには、まず既存のモジュールを削除し、その後に更新操作を実行します。あるいは、「[Az モジュールをインポートする](#import-az-modules)」の説明に従って、このモジュールを個別に更新して、別のバージョンの特定のモジュールをインポートすることもできます。  

次の表は、Automation アカウントを作成したときに Azure Automation によって既定でインポートされるモジュールの一覧を示しています。 Automation を使用してこれらのモジュールの新しいバージョンをインポートできます。 ただし、新しいバージョンを削除した場合でも、Automation アカウントから元のバージョンを削除することはできません。

既定のモジュールは、グローバル モジュールとも呼ばれます。 Azure portal で、アカウントが作成されたときにインポートされたモジュールを表示すると、 **[グローバル モジュール]** プロパティが **true** になります。

![Azure portal のグローバル モジュール プロパティのスクリーンショット](../media/modules/automation-global-modules.png)

> [!NOTE]
> [Start/Stop VMs during off-hours](../automation-solution-vm-management.md) 機能のデプロイに使用される Automation アカウントでモジュールおよび Runbook を変更することはお勧めしません。

|モジュール名|Version|
|---|---|
|Az.* | [PowerShell ギャラリー](https://www.powershellgallery.com/packages/Az)の **[パッケージの詳細]** にある完全な一覧を参照|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0.2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="internal-cmdlets"></a>内部コマンドレット

Azure Automation では、Azure サンドボックス環境内または Windows Hybrid Runbook Worker 上で Runbook を実行する場合にのみ利用可能な内部コマンドレットがサポートされています。 内部モジュール `Orchestrator.AssetManagement.Cmdlets` は、Windows Hybrid Runbook Worker ロールがマシンにインストールされるときに、既定で Automation アカウントにインストールされます。 

次の表では、内部コマンドレットを定義しています。 これらのコマンドレットは、Automation アカウント リソースを操作するために Azure PowerShell コマンドレットの代わりに使用するように設計されています。 それらは、暗号化された変数、資格情報、および暗号化された接続からシークレットを取得できます。

|名前|説明|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

内部コマンドレットの名前付けは、Az および AzureRM コマンドレットの場合と異なることに注意してください。 内部コマンドレット名には、名詞に `Azure` や `Az` などの単語は含まれませんが、`Automation` という語が使用されます。 Azure サンドボックスまたは Windows Hybrid Runbook Worker では、Runbook の実行中に Az または AzureRM コマンドレットの代わりにこれらを使用することをお勧めします。これは、必要なパラメーターが減り、実行中にジョブのコンテキストで実行されるためです。

Runbook のコンテキスト外で Automation リソースを操作する場合には Az または AzureRM コマンドレットの使用してください。 

## <a name="python-modules"></a>Python モジュール

Azure Automation では、Python 2 Runbook を作成できます。 Python モジュールについては、「[Azure Automation で Python 2 パッケージを管理する](../python-packages.md)」を参照してください。

## <a name="custom-modules"></a>カスタム モジュール

Azure Automation は、Runbook と DSC 構成で使用するために作成するカスタム PowerShell モジュールをサポートしています。 カスタムモジュールの 1 つの種類は、必要に応じて、モジュール コマンドレットのカスタム機能を定義するメタデータのファイルを含む統合モジュールです。 統合モジュールの使用例については、「[接続の種類の追加](../automation-connections.md#add-a-connection-type)」を参照してください。

Azure Automation は、カスタム モジュールをインポートしてそのコマンドレットを利用可能にすることができます。 バックグラウンドでは、他のモジュールと同じように、モジュールを格納し、Azure サンドボックスで使用します。

## <a name="migrate-to-az-modules"></a>Az モジュールへの移行

このセクションでは、Automation で Az モジュールに移行する方法について説明します。 詳細については、「[AzureRM から Az への Azure PowerShell の移行](/powershell/azure/migrate-from-azurerm-to-az)」を参照してください。

AzureRM モジュールと Az モジュールを同じ Automation アカウントで実行することはお勧めできません。 AzureRM から Az に移行することが確実である場合は、完全な移行を完全にコミットすることをお勧めします。 Automation では、多くの場合、起動時間を節約するために Automation アカウントのサンドボックスが再利用されます。 モジュールの完全な移行を行っていない場合、AzureRM モジュールのみを使用するジョブを開始し、次に Az モジュールのみを使用する別のジョブを開始することになります。 サンドボックスは間もなくクラッシュし、モジュールに互換性がないことを示すエラーが返されます。 このような状況では、特定の Runbook または構成ではクラッシュがランダムに発生します。

> [!NOTE]
> 新しい Automation アカウントを作成すると、Az モジュールへの移行後でも、Automation によって引き続き AzureRM モジュールが既定でインストールされます。

### <a name="test-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>モジュールを移行する前に Runbook と DSC 構成をテストする

Az モジュールに移行する前に、別の Automation アカウントですべての Runbook と DSC 構成を必ずテストしてください。 

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>AzureRM モジュールが使用されるすべての Runbook を停止してスケジュールを解除する

AzureRM モジュールを使用する既存の Runbook または DSC 構成を決して実行しないようにするために、影響を受けるすべての Runbook と構成を停止し、スケジュールを解除する必要があります。 まず、各 Runbook または DSC 構成と、それぞれのスケジュールを別々に確認して、必要に応じて、後で項目を確実に再スケジュールできるようにします。

スケジュールを削除する準備ができたら、Azure portal または [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) コマンドレットのいずれかを使用できます。 [スケジュールの削除](schedules.md#remove-a-schedule)に関するページを参照してください。

### <a name="remove-azurerm-modules"></a>AzureRM モジュールを削除する

Az モジュールをインポートする前に、AzureRM モジュールを削除することができます。 ただし、これを行うと、ソース管理の同期が中断され、まだスケジュールされているスクリプトが失敗する可能性があります。 モジュールを削除することに決めたら、「[AzureRM のアンインストール](/powershell/azure/migrate-from-azurerm-to-az#uninstall-azurerm)」を参照してください。

### <a name="import-az-modules"></a>Az モジュールをインポートする

Automation アカウントに Az モジュールをインポートしても、Runbook で使用される PowerShell セッションにそのモジュールが自動的にインポートされることはありません。 モジュールは、次の状況で PowerShell セッションにインポートされます。

* Runbook がモジュールからコマンドレットを呼び出したとき。
* Runbook で [Import-Module](/powershell/module/microsoft.powershell.core/import-module) コマンドレットを使用してモジュールが明示的にインポートされたとき。
* Runbook で [using module](/powershell/module/microsoft.powershell.core/about/about_using#module-syntax) ステートメントを使用してモジュールが明示的にインポートされたとき。 using ステートメントは、Windows PowerShell 5.0 以降でサポートされており、クラスと列挙型のインポートをサポートしています。
* Runbook が別の依存モジュールをインポートするとき。

Az モジュールを Automation アカウントにインポートするには、Azure portal を使用します。 [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) には他の Az モジュールが依存しているため、必ず他のモジュールの前にこのモジュールをインポートするようにしてください。

> [!NOTE]
>  **PowerShell 7.1 (プレビュー)** のサポートの導入に伴い、 **[ギャラリーの参照]** オプションが次のように変更されました。

-  **[プロセス オートメーション]**  >  **[モジュール]** ブレードで **[ギャラリーの参照]** を使用できます。 
-  **[モジュール]** ページには、 **[モジュール バージョン]** と **[ランタイム バージョン]** という 2 つの新しい列が表示されます

1. Azure [Portal](https://portal.azure.com) にサインインします。
1. **Automation アカウント** を検索して選択します。
1. **[Automation アカウント]** ページで、一覧からお使いの Automation アカウントを選択します。
1. Automation アカウントから、 **[共有リソース]** の **[モジュール]** を選択します。
1. **[モジュールの追加]** を選択します。 **[モジュールの追加]** ページでは、次のいずれかのオプションを選択できます。
      1. **[ファイルの参照]** - ローカル コンピューターからファイルを選択します。
      1. **[ギャラリーで参照]** - ギャラリーから既存のモジュールを参照して選択できます。
1. **[選択]** をクリックしてモジュールを選択します。
1. **[ランタイム バージョン]** を選択し、 **[インポート]** をクリックします。

      :::image type="content" source="../media/modules/import-module.png" alt-text="Automation アカウントにモジュールをインポートする画面のスクリーンショット。":::

1. **[モジュール]** ページの Automation アカウントの下で、インポートされたモジュールを表示することができます。

このインポートは、インポートするモジュールを [PowerShell ギャラリー](https://www.powershellgallery.com)で検索して行うこともできます。 モジュールが見つかったら、それを選択し、 **[Azure Automation]** タブを選択します。 **[Azure Automation にデプロイする]** を選択します。

:::image type="content" source="../media/modules/import-gallery.png" alt-text="PowerShell ギャラリーからモジュールを直接インポートする画面のスクリーンショット。":::

### <a name="test-your-runbooks"></a>Runbook をテストする

Az モジュールを Automation アカウントにインポートしたら、その新しいモジュールを使用できるように Runbook および DSC 構成の編集を開始することができます。 新しいコマンドレットを使用するという Runbook の変更をテストする 1 つの方法として、Runbook の先頭で `Enable-AzureRmAlias -Scope Process` コマンドを使用できます。 このコマンドを Runbook に追加することで、変更なしでスクリプトを実行できます。

## <a name="author-modules"></a>モジュールを作成する

Azure Automation で使用するカスタム PowerShell モジュールを作成するときは、このセクションの考慮事項に従うことをお勧めします。 インポート用にモジュールを準備するには、少なくとも、モジュール フォルダーと同じ名前の .psd1、.psm1、または PowerShell モジュールの **.dll** ファイルを作成する必要があります。 次に、Azure Automation が 1 つのファイルとしてインポートできるように、モジュール フォルダーを zip 形式に圧縮します。 **.zip** パッケージは、含まれているモジュール フォルダーと同じ名前にする必要があります。

PowerShell モジュールの作成の詳細については、「[PowerShell スクリプト モジュールを記述する方法](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module)」を参照してください。

### <a name="version-folder"></a>バージョン フォルダー

PowerShell のサイド バイ サイドのモジュール バージョン管理では、PowerShell 内で複数のバージョンのモジュールを使用できます。 これは特定のバージョンの PowerShell モジュールに対してのみテストされて動作する古いスクリプトがあるが、他のスクリプトについては同じ PowerShell モジュールの新しいバージョンが必要な場合に便利です。

複数のバージョンが含まれるように PowerShell モジュールを構築するには、モジュール フォルダーを作成し、このモジュール フォルダー内に、使用可能にするモジュールのバージョンごとのフォルダーを作成します。 次の例では、*TestModule* というモジュール内に、1.0.0 と 2.0.0 の 2 つのバージョンがあります。

```dos
TestModule
   1.0.0
   2.0.0
```

各バージョンのフォルダー内で、モジュールを構成する PowerShell の .psm1、.psd1、または PowerShell モジュールの **.dll** ファイルをそれぞれのバージョンのフォルダーにコピーします。 Azure Automation が 1 つの .zip ファイルとしてインポートできるように、モジュール フォルダーを zip 形式に圧縮します。 Automation ではインポートされたモジュールの最上位バージョンのみが表示されますが、モジュール パッケージにサイド バイ サイド バージョンのモジュールが含まれている場合は、これらのすべてを Runbook または DSC 構成で使用できます。  

Automation では、同じパッケージ内のサイド バイ サイド バージョンを含むモジュールがサポートされますが、モジュール パッケージ インポート全体で複数のバージョンのモジュールを使用することはサポートされていません。 たとえば、バージョン 1 と 2 を含む **モジュール A** を Automation アカウントにインポートします。 後で、バージョン 3 と 4 を含むように **モジュール A** を更新し、Automation アカウントにインポートすると、すべての Runbook または DSC 構成内でバージョン 3 と 4 のみが使用できます。 1、2、3、4 のすべてのバージョンを使用できるようにする必要がある場合、インポートする .zip ファイルにバージョン 1、2、3、4 が含まれている必要があります。

Runbook 間で同じモジュールの異なるバージョンを使用する場合は、`Import-Module` コマンドレットを使用して `-RequiredVersion <version>` パラメーターを含めることで、Runbook で使用するバージョンを常に宣言する必要があります。 使用するバージョンが最新バージョンであってもそうです。 これは、Runbook ジョブは同じサンドボックス内で実行できるためです。 サンドボックス内の以前のジョブによって指定されていたため、特定のバージョン番号のモジュールがサンドボックスに既に明示的に読み込まれている場合、そのサンドボックスの後続のジョブでは、そのモジュールの最新バージョンが自動的に読み込まれることはありません。 これは、その一部のバージョンが既にサンドボックスに読み込まれているためです。

DSC リソースの場合は、次のコマンドを使用して特定のバージョンを指定します。

```powershell
Import-DscResource -ModuleName <ModuleName> -ModuleVersion <version>
```

### <a name="help-information"></a>ヘルプ情報

モジュール内のすべてのコマンドレットの概要、説明、ヘルプ URI を含める。 PowerShell では、`Get-Help` コマンドレットを使用して、コマンドレットのヘルプ情報を定義できます。 次の例では、 **.psm1** モジュール ファイルで概要およびヘルプ URI を定義する方法を示します。

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  この情報を指定すると、PowerShell コンソールで `Get-Help` コマンドレットを使用してヘルプ テキストを表示できます。 このテキストは、Azure portal にも表示されます。

  ![統合モジュールのヘルプのスクリーンショット](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>接続の種類

モジュールから外部サービスに接続する場合は、[カスタム統合モジュール](#custom-modules)を使用して、接続の種類を定義します。 モジュール内の各コマンドレットはその接続の種類 (接続オブジェクト) のインスタンスをパラメーターとして受け取ります。 ユーザーは、コマンドレットを呼び出すたびに、接続資産のパラメーターをそのコマンドレットの対応するパラメーターにマップします。

![Azure portal でカスタム接続を使用する](../media/modules/connection-create-new.png)

次の Runbook 例は、`ContosoConnection` という Contoso 接続資産を使用して Contoso リソースにアクセスし、外部サービスからデータを返します。 次の例では、フィールドが `PSCredential` オブジェクトの `UserName` および `Password` プロパティにマップされ、続いてコマンドレットに渡されます。

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

この動作を行うためのより簡単で優れた方法は、接続オブジェクトをコマンドレットに直接渡すというものです。

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

コマンドレットを似た動作にするには、パラメーターの接続フィールドではなく、接続オブジェクトをパラメーターとして直接受け取ることができるようにします。 Automation を使用していないユーザーが、接続オブジェクトとして機能するハッシュテーブルを作成することなく、コマンドレットを呼び出すことができるようにするには、通常、それぞれにパラメーター セットが必要です。 接続フィールドのプロパティを渡すために、パラメーター セット `UserAccount` が使用されます。 `ConnectionObject` を使用すると、接続をそのまま渡すことができます。

### <a name="output-type"></a>出力の種類

モジュール内のすべてのコマンドレットの出力の種類を定義します。 コマンドレットの出力の種類を定義すると、設計時の IntelliSense で、作成時のコマンドレットの出力プロパティを判断できます。 この方法は、設計時の知識がモジュールでの容易なユーザー エクスペリエンスの鍵となる、Runbook のグラフィカルな作成時に特に役立ちます。

`[OutputType([<MyOutputType>])]` を追加します。`MyOutputType` は有効な種類です。 `OutputType` の詳細については、「[関数の OutputTypeAttribute の概要](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)」を参照してください。 次のコードは、`OutputType` をコマンドレットに追加する例です。

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![グラフィカルな Runbook 出力の種類のスクリーンショット](../media/modules/runbook-graphical-module-output-type.png)

  この動作は、PowerShell 統合サービス環境のコマンドレットの出力の "先行入力" 機能と似ていますが、実行する必要はありません。

  ![POSH IntelliSense のスクリーンショット](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>コマンドレットの状態

モジュール内のすべてのコマンドレットをステートレスにします。 複数の Runbook ジョブは、同じ `AppDomain` および同じプロセスとサンドボックスで同時に実行できます。 これらのレベルで共有された状態がある場合、ジョブが互いに影響し合うことがあります。 この動作は、断続的で診断の難しい問題を招く可能性があります。 悪い例を次に示します。

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

### <a name="module-dependency"></a>モジュールの依存関係

xcopy を使用してコピーできるパッケージにモジュールが完全に含まれていることを確認します。 Automation のモジュールは、Runbook を実行するときに Automation の Sandbox に配布されます。 モジュールは、それらを実行するホストとは独立して動作する必要があります。

モジュール パッケージを zip 形式で圧縮して移動できるようにし、別のホストの PowerShell 環境にインポートしたときに通常通りに機能する状態にする必要があります。 これを行うには、モジュールが Automation にインポートされるときに、zip 形式のモジュール フォルダーの外部にあるファイルに依存していないことを確認します。

モジュールは、ホスト上の独自のレジストリ設定に依存しないようにする必要があります。 例としては、製品のインストール時に行われる設定があります。

### <a name="module-file-paths"></a>モジュール ファイルのパス

モジュール内のすべてのファイルが、140 文字未満のパスであることを確認します。 長さが 140 文字を超えるパスを使用すると、Runbook のインポートで問題が発生します。 Automation では、`Import-Module` を使用してパス サイズが 140 文字を超えるファイルを PowerShell セッションにインポートすることはできません。

## <a name="import-modules"></a>モジュールをインポートする

このセクションでは、Automation アカウントにモジュールをインポートできるいくつかの方法を定義します。

### <a name="import-modules-in-the-azure-portal"></a>Azure portal でモジュールをインポートする

Azure portal でモジュールをインポートするには:

1. ポータルで、 **[Automation アカウント]** を検索して選択します。
1. **[Automation アカウント]** ページで、一覧からお使いの Automation アカウントを選択します。
1. **[共有リソース]** の下にある **[モジュール]** を選択します。
1. **[モジュールの追加]** を選択します。
1. モジュールを含む **.zip** ファイルを選択します。
1. **[OK]** を選択してプロセスのインポートを開始します。

### <a name="import-modules-by-using-powershell"></a>PowerShell を使用してモジュールをインポートする

[New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule) コマンドレットを使用して、モジュールを Automation アカウントにインポートできます。 このコマンドレットは、モジュールの .zip パッケージの URL を受け取ります。

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

同じコマンドレットを使用して、PowerShell ギャラリーからモジュールを直接インポートすることもできます。 必ず [PowerShell ギャラリー](https://www.powershellgallery.com)から `ModuleName` と `ModuleVersion` を取得してください。

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>PowerShell ギャラリーからモジュールをインポートする

[PowerShell ギャラリー](https://www.powershellgallery.com) モジュールは、ギャラリーから、または Automation アカウントから直接インポートできます。

PowerShell ギャラリーから直接モジュールをインポートするには:

1. https://www.powershellgallery.com に移動し、インポートするモジュールを検索します。
2. **[インストール オプション]** の下にある **[Azure Automation]** タブで **[Deploy to Azure Automation]\(Azure Automation にデプロイする\)** を選択します。 このアクションにより Azure portal が開きます。 
3. [インポート] ページで、Automation アカウントを選択し、 **[OK]** を選択します。

![PowerShell ギャラリーのインポート モジュールのスクリーンショット](../media/modules/powershell-gallery.png)

PowerShell ギャラリー モジュールを Automation アカウントから直接インポートするには:

1. ポータルで、 **[Automation アカウント]** を検索して選択します。
1. **[Automation アカウント]** ページで、一覧からお使いの Automation アカウントを選択します。
1. **[共有リソース]** の下にある **[モジュール]** を選択します。 
1. **[ギャラリーの参照]** を選択し、ギャラリーでモジュールを検索します。 
1. インポートするモジュールを選択し、 **[インポート]** を選択します。 
1. **[OK]** を選択してインポート プロセスを開始します。

![Azure portal から PowerShell ギャラリー モジュールをインポートする画面のスクリーンショット](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>モジュールを削除する

モジュールに問題がある、またはモジュールの以前のバージョンにロールバックする必要がある場合は、そのモジュールを Automation アカウントから削除できます。 Automation アカウントを作成するときにインポートされた[既定のモジュール](#default-modules)の元のバージョンを削除することはできません。 削除するモジュールが[既定のモジュール](#default-modules)のいずれかの新しいバージョンである場合、Automation アカウントを使用してインストールされたバージョンにロールバックします。 それ以外の場合、Automation アカウントから削除したモジュールはすべて削除されます。

### <a name="delete-modules-in-the-azure-portal"></a>Azure portal でモジュールを削除する

Azure portal でモジュールを削除するには:

1. ポータルで、 **[Automation アカウント]** を検索して選択します。
1. **[Automation アカウント]** ページで、一覧からお使いの Automation アカウントを選択します。
1. **[共有リソース]** の下にある **[モジュール]** を選択します。
1. 削除するモジュールを選択します。
1. [モジュール] ページで、 **[削除]** を選択します。 このモジュールが[既定のモジュール](#default-modules)のいずれかである場合、Automation アカウントが作成されたときに存在していたバージョンにロールバックされます。

### <a name="delete-modules-by-using-powershell"></a>PowerShell を使用してモジュールを削除する

PowerShell を使用してモジュールを削除するには、次のコマンドを実行します。

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>次のステップ

* Azure PowerShell モジュールの使用方法の詳細については、「[Azure PowerShell の概要](/powershell/azure/get-started-azureps)」をご覧ください。

* PowerShell モジュールの作成の詳細については、「[Windows PowerShell モジュールの作成](/powershell/scripting/developer/module/writing-a-windows-powershell-module)」をご覧ください。
