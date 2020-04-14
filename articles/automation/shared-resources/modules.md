---
title: Azure Automation でモジュールを管理する
description: この記事では、Azure Automation でモジュールを管理する方法について説明します。
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 605d1bc72406a9aeecc9273f9bd2d7fd2b30ab11
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618690"
---
# <a name="manage-modules-in-azure-automation"></a>Azure Automation でモジュールを管理する

PowerShell モジュールを Azure Automation にインポートして、それらのコマンドレットを Runbook で使用できるようにし、DSC リソースを DSC 構成で使用できるようにすることができます。 バックグラウンドで、Azure Automation にはこれらのモジュールが格納されます。 Runbook ジョブと DSC コンパイル ジョブの実行時に、Automation によって Azure Automation サンド ボックスにそれらが読み込まれ、そこで Runbook が実行され、DSC 構成がコンパイルされます。 また、モジュール内のすべての DSC リソースは、Automation DSC プル サーバーに自動的に配置されます。 DSC 構成を適用するときに、マシンからそれらをプルできます。

Azure Automation で使用されるモジュールは、独自に作成したカスタム モジュール、PowerShell ギャラリーのモジュール、または Azure 用の AzureRM および Az モジュールの場合があります。 Automation アカウントを作成すると、一部のモジュールが既定でインポートされます。

## <a name="default-modules"></a>既定のモジュール

次の表は、Automation アカウントの作成時に既定でインポートされるモジュールの一覧です。 Automation を使用してこれらのモジュールの新しいバージョンをインポートできます。 ただし、新しいバージョンを削除した場合でも、Automation アカウントから元のバージョンを削除することはできません。

|モジュール名|Version|
|---|---|
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

次の表は、すべての Automation アカウントにインポートされる内部 `Orchestrator.AssetManagement.Cmdlets` モジュールのコマンドレットの一覧です。 これらのコマンドレットは、Runbook および DSC 構成でアクセスでき、Automation アカウント内のアセットを操作できるようにします。 さらに、内部コマンドレットは、暗号化された変数、資格情報、および暗号化された接続からシークレットを取得できるようにします。 Azure PowerShell コマンドレットはこれらのシークレットを取得できません。 これらのコマンドレットでは、それを使用するとき (実行アカウントを使用して Azure に対して認証するときなど) に Azure に暗黙的に接続する必要はありません。

>[!NOTE]
>これらの内部コマンドレットは、Windows Hybrid Runbook Worker では使用できますが、Linux Hybrid Runbook Worker では使用できません。 コンピューター上で直接、または環境内のリソースに対して実行される Runbook の場合、対応する [AzureRM.Automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) または [Az モジュール](../az-modules.md) コマンドレットを使用します。 

|名前|説明|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="importing-modules"></a>モジュールのインポート

Automation アカウントにモジュールをインポートする方法は複数あります。 以降のセクションで、モジュールをインポートするさまざまな方法を示します。

> [!NOTE]
> Azure Automation で使用されるモジュール内のファイルの最大パス サイズは 140 文字です。 Automation では、`Import-Module` を使用してパス サイズが 140 文字を超えるファイルを PowerShell セッションにインポートすることはできません。

### <a name="import-modules-in-azure-portal"></a>Azure portal でモジュールをインポートする

Azure portal でモジュールをインポートするには:

1. Automation アカウントに移動します。
2. **[共有リソース]** の下にある **[モジュール]** を選択します。
3. **[モジュールの追加]** をクリックします。 
4. モジュールを含む **.zip** ファイルを選択します。
5. **[OK]** をクリックしてプロセスのインポートを開始します。

### <a name="import-modules-using-powershell"></a>PowerShell を使用してモジュールをインポートする

[New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) コマンドレットを使用してモジュールを Automation アカウントにインポートできます。 このコマンドレットは、モジュールの .zip パッケージの URL を受け取ります。

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

同じコマンドレットを使用して、PowerShell ギャラリーからモジュールを直接インポートすることもできます。 必ず [PowerShell ギャラリー](https://www.powershellgallery.com)から `ModuleName` と `ModuleVersion` を取得してください。

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzureRmAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```
### <a name="import-modules-from-powershell-gallery"></a>PowerShell ギャラリーからモジュールをインポートする

[PowerShell ギャラリー](https://www.powershellgallery.com) モジュールは、ギャラリーから、または Automation アカウントから直接インポートできます。

PowerShell ギャラリーから直接モジュールをインポートするには:

1. https://www.powershellgallery.com に移動し、インポートするモジュールを検索します。
2. **[インストール オプション]** の下にある **[Azure Automation]** タブで **[Deploy to Azure Automation]** (Azure Automation にデプロイする) をクリックします。 このアクションにより Azure portal が開きます。 
3. [インポート] ページで、Automation アカウントを選択し、 **[OK]** をクリックします。

![PowerShell ギャラリーのインポート モジュール](../media/modules/powershell-gallery.png)

PowerShell ギャラリー モジュールを Automation アカウントから直接インポートするには:

1. **[共有リソース]** の下にある **[モジュール]** を選択します。 
2. モジュールのページで **[ギャラリーの参照]** をクリックし、ギャラリーでモジュールを検索します。 
3. インポートするモジュールを選択し、 **[インポート]** をクリックします。 
4. [インポート] ページで **[OK]** をクリックしてインポート プロセスを開始します。

![Azure portal からの PowerShell ギャラリーのインポート](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>モジュールの削除

モジュールに問題がある、またはモジュールの以前のバージョンにロールバックする必要がある場合は、そのモジュールを Automation アカウントから削除できます。 Automation アカウントを作成するときにインポートされた[既定のモジュール](#default-modules)の元のバージョンを削除することはできません。 削除するモジュールが[既定のモジュール](#default-modules)のいずれかの新しいバージョンである場合、Automation アカウントを使用してインストールされたバージョンにロールバックします。 それ以外の場合、Automation アカウントから削除したモジュールはすべて削除されます。

### <a name="delete-modules-in-azure-portal"></a>Azure portal でモジュールを削除する

Azure portal でモジュールを削除するには:

1. Automation アカウントに移動し、 **[共有リソース]** の下にある **[モジュール]** を選択します。 
2. 削除するモジュールを選択します。 
3. **[モジュール]** ページで、 **[削除]** を選択します。 このモジュールが[既定のモジュール](#default-modules)のいずれかである場合、Automation アカウントが作成されたときに存在していたバージョンにロールバックされます。

### <a name="delete-modules-using-powershell"></a>PowerShell を使用してモジュールを削除する

PowerShell を使用してモジュールを削除するには、次のコマンドを実行します。

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```
## <a name="adding-a-connection-type-to-your-module"></a>接続の種類をモジュールに追加する

省略可能なメタデータ ファイルをモジュールに追加することにより、Automation アカウントで使用するカスタムの[接続の種類](../automation-connections.md)を提供できます。 このファイルを使用して、Automation アカウントのモジュールのコマンドレットで使用される Azure Automation の接続の種類を指定します。 これを実現するには、まず PowerShell モジュールを作成する方法を知る必要があります。 「[PowerShell スクリプト モジュールを記述する方法](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module)」を参照してください。

![Azure portal でカスタム接続を使用する](../media/modules/connection-create-new.png)

接続の種類のプロパティを指定するファイルの名前は **&lt;ModuleName&gt;-Automation.json** であり、圧縮された **.zip** ファイルのモジュール フォルダー内にあります。 このファイルには、モジュールが示すシステムまたはサービスに接続するために必要な接続のフィールドが含まれています。 この構成では、Azure Automation で接続の種類を作成できます。 このファイルを使用すると、モジュールの接続の種類について、フィールド名、種類、フィールドを暗号化するか省略可能にするかを指定できます。 次の例は、ユーザー名とパスワードのプロパティを定義する **.json** ファイル形式のテンプレートです。

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="best-practices-for-authoring-modules"></a>モジュールを作成するためのベスト プラクティス

Azure Automation で使用する PowerShell モジュールを作成するときは、このセクションの考慮事項に従うことをお勧めします。

### <a name="version-folder"></a>バージョン フォルダー

モジュールの **.zip** パッケージ内にバージョン フォルダーを含めないでください。  この問題は Runbook にはあまり関係ありませんが、State Configuration サービスでは問題が発生します。 DSC によって管理されるノードにモジュールが配布されるときに、Azure Automation によってバージョン フォルダーが自動的に作成されます。 バージョン フォルダーが存在する場合、最終的に 2 つのインスタンスが作成されます。 DSC モジュールのフォルダー構造の例を次に示します。

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
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
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  この情報を指定すると、PowerShell コンソールで `Get-Help` コマンドレットを使用してヘルプ テキストを表示できます。 このテキストは、Azure portal にも表示されます。

  ![統合モジュールのヘルプ](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>接続の種類

モジュールから外部サービスに接続する場合は、[接続の種類](#adding-a-connection-type-to-your-module)を定義します。 モジュール内の各コマンドレットが接続オブジェクト (その接続の種類のインスタンス) をパラメーターとして受け取るようにします。 ユーザーは、コマンドレットを呼び出すたびに、接続資産のパラメーターをそのコマンドレットの対応するパラメーターにマップします。 上記の Runbook 例に基づいて、これは `ContosoConnection` という Contoso 接続資産例を使用して、Contoso リソースにアクセスし、外部サービスからのデータを返します。

  次の例では、フィールドが `PSCredential` オブジェクトの `UserName` および `Password` のプロパティにマップされ、続いてコマンドレットに渡されます。

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  この動作のためのより簡単かつ優れた方法では、接続オブジェクトをコマンドレットに直接渡します。

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  コマンドレットを似た動作にするには、パラメーターの接続フィールドではなく、接続オブジェクトをパラメーターとして直接受け取ることができるようにします。 Azure Automation を使用していないユーザーが、接続オブジェクトとして機能するハッシュテーブルを作成することなく、コマンドレットを呼び出すことができるようにするには、通常、それぞれにパラメーター セットが必要です。 接続フィールドのプロパティを渡すために、パラメーター セット `UserAccount` が使用されます。 `ConnectionObject` を使用すると、接続をそのまま渡すことができます。

### <a name="output-type"></a>出力の種類

モジュール内のすべてのコマンドレットの出力の種類を定義します。 コマンドレットの出力の種類を定義すると、設計時の IntelliSense で、作成時に使用するコマンドレットの出力のプロパティを確認できます。 この方法は、Automation Runbook のグラフィカル作成時に特に役に立ちます。グラフィカル作成時にユーザーがモジュールを簡単に利用できるようにするには、設計時の情報が重要です。

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

  ![Graphical Runbook Output Type](../media/modules/runbook-graphical-module-output-type.png)

  この動作は、PowerShell ISE のコマンドレットの出力の "先行入力" 機能と似ていますが、実行する必要はありません。

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

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

モジュールが xcopy 対応のパッケージに完全に含まれていることを確認します。 Azure Automation のモジュールは、Runbook を実行するときに Automation の Sandbox に配布されます。 モジュールは、それらを実行するホストとは独立して動作する必要があります。 

モジュール パッケージを zip 形式で圧縮して移動できるようにし、別のホストの PowerShell 環境にインポートしたときに通常通りに機能する状態にする必要があります。 これを行うには、モジュールが Azure Automation にインポートされるときに、zip 形式のモジュール フォルダーの外部にあるファイルに依存していないことを確認します。 

モジュールは、ホスト上の独自のレジストリ設定に依存しないようにする必要があります。 例として、製品のインストール時に行われる設定があります。 

モジュール内のすべてのファイルが、140 文字未満のパスであることを確認します。 140 文字を超えるパスを使用すると、Runbook のインポートで問題が発生します。 このベスト プラクティスに従っていない場合、Azure Automation ではモジュールを使用できません。  

### <a name="references-to-azurerm-and-az"></a>AzureRM と Az の参照

モジュール内で [Azure PowerShell Az モジュール](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)を参照している場合は、AzureRM も参照していないことを確認します。 AzureRM モジュールと共に Az モジュールを使用することはできません。 Az は Runbook でサポートされていますが、既定ではインポートされません。 Az モジュールと考慮事項の詳細については、[Azure Automation での Az モジュールのサポート](../az-modules.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* PowerShell モジュールの作成の詳細については、「[Windows PowerShell モジュールの作成](/powershell/scripting/developer/windows-powershell)」を参照してください。
