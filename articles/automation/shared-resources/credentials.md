---
title: Azure Automation での資格情報資産
description: Azure Automation の資格情報資産には、Runbook または DSC 構成によってアクセスされるリソースの認証に使用できるセキュリティ資格情報が含まれます。 この記事では、資格情報資産を作成し、Runbook または DSC 構成でそれを使用する方法について説明します。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c8b63a2676690004d23094b490fea0ef150ab9cb
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546393"
---
# <a name="credential-assets-in-azure-automation"></a>Azure Automation での資格情報資産

Automation 資格情報資産は、ユーザー名とパスワードなどのセキュリティ資格情報を含むオブジェクトを保持しています。 Runbook と DSC 構成では、認証のための [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) オブジェクトを受け入れるコマンドレットを使用します。 または、`PSCredential` オブジェクトのユーザー名とパスワードを抽出して、認証を必要とする一部のアプリケーションまたはサービスに提供することもできます。 

Azure Automation では、資格情報のプロパティが安全に保存されます。 Runbook または DSC 構成を経由したプロパティへのアクセスでは、[Get-AutomationPSCredential](#activities-used-to-access-credentials) アクティビティを使用します。

> [!NOTE]
> Azure Automation でセキュリティ保護される資産としては、資格情報、証明書、接続、暗号化された変数などがあります。 これらの資産は、各 Automation アカウント用に生成された一意のキーを使って暗号化され、Azure Automation に保存されます。 このキーは Key Vault に格納されます。 セキュリティで保護された資産を保存する前に、キーが Key Vault から読み込まれ、資産の暗号化に使われます。

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>資格情報資産に使用される Azure PowerShell Az コマンドレット

Azure PowerShell Az モジュールの一部として、Windows PowerShell で Automation 資格情報資産を作成および管理するには、次の表のコマンドレットを使用します。 これらのコマンドレットは、Automation Runbook と DSC 構成に使用できる [Az.Automation モジュール](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)に付属しています。 「[Azure Automation での Az モジュールのサポート](https://docs.microsoft.com/azure/automation/az-modules)」を参照してください。

| コマンドレット | 説明 |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |資格情報資産に関する情報を取得します。 このコマンドレットは、`PSCredential` オブジェクトを返しません。  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |新しい Automation 資格情報を作成します。 |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Automation 資格情報を削除します。 |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |既存の Automation 資格情報のプロパティを設定します。 |

## <a name="activities-used-to-access-credentials"></a>資格情報へのアクセスに使用するアクティビティ

次の表のアクティビティは、Runbook および DSC 構成で資格情報にアクセスするために使用されます。

| アクティビティ | 説明 |
|:--- |:--- |
| `Get-AutomationPSCredential` |Runbook または DSC 構成で使用する資格情報を取得します。 資格情報は、`PSCredential` オブジェクトの形式です。 |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |ユーザー名とパスワードの入力を求めるプロンプトが表示された資格情報を取得します。 |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | 資格情報資産を作成します。 |

Azure Automation Authoring Toolkit を使用したローカル開発の場合、`Get-AutomationPSCredential` コマンドレットは、アセンブリ [AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9) の一部となります。 Azure でオートメーション コンテキストを使用する場合、コマンドレットは `Orchestrator.AssetManagement.Cmdlets` にあります。 「[Azure Automation でモジュールを管理する](modules.md)」を参照してください。

コード内の `PSCredential` オブジェクトを取得するには、[PowerShell ISE の Microsoft Azure Automation ISE アドオン](https://github.com/azureautomation/azure-automation-ise-addon)をインストールします。

```azurepowershell
Install-Module AzureAutomationAuthoringToolkit -Scope CurrentUser -Force
```

スクリプトでは、次の例のように、必要に応じて必要なモジュールをインポートすることもできます。 

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> `Get-AutomationPSCredential` の `Name` パラメーターに変数を使用することは避けてください。 この変数を使用すると、デザイン時に、Runbook または DSC 構成と資格情報資産との間の依存関係の検出が複雑になる可能性があります。

## <a name="python2-functions-that-access-credentials"></a>資格情報にアクセスする Python2 関数

次の表の関数を使用して、Python2 Runbook の資格情報にアクセスします。

| 機能 | 説明 |
|:---|:---|
| `automationassets.get_automation_credential` | 資格情報資産に関する情報を取得します。 |

> [!NOTE]
> 資産関数にアクセスするには、お使いの Python Runbook の上部にある `automationassets` モジュールをインポートする必要があります。

## <a name="creating-a-new-credential-asset"></a>新しい資格情報資産の作成

Azure portal または Windows PowerShell を使用して、新しい資格情報資産を作成できます。

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Azure portal での新しい資格情報資産の作成

1. Automation アカウントから、 **[共有リソース]** の下の **[資格証明]** を選択します。
1. **[資格情報の追加]** を選択します。
2. [新しい資格情報] ウィンドウで、命名規則に従って、適切な資格情報名を入力します。 
3. **[ユーザー名]** フィールドにアクセス ID を入力します。 
4. 両方のパスワード フィールドに、シークレット アクセス キーを入力します。

    ![新しい資格情報を作成する](../media/credentials/credential-create.png)

5. [多要素認証] チェックボックスがオンになっている場合は、オフにします。 
6. **[作成]** をクリックして、新しい資格情報資産を保存します。

> [!NOTE]
> Azure Automation では、多要素認証を使用するユーザー アカウントをサポートしていません。

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Windows PowerShell で新しい資格情報資産を作成する

次の例では、新しい Automation 資格情報資産を作成する方法について説明します。 最初に名前とパスワードで `PSCredential` オブジェクトを作成し、それを使用して資格情報資産を作成します。 または、`Get-Credential` コマンドレットを使用して、ユーザーに名前とパスワードの入力を求めることもできます。

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>PowerShell 資格情報の使用

Runbook または DSC 構成では、`Get-AutomationPSCredential` アクティビティを使用して、資格情報資産を取得します。 このアクティビティでは、資格情報を必要とするアクティビティまたはコマンドレットで使用できる `PSCredential` オブジェクトを取得します。 また、資格情報オブジェクトのプロパティを取得して個別に使用することもできます。 このオブジェクトには、ユーザー名とセキュリティで保護されたパスワードのプロパティがあります。 または、[GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) メソッドを使用して、安全ではないバージョンのパスワードを表す [NetworkCredential](/dotnet/api/system.net.networkcredential) オブジェクトを取得することもできます。

> [!NOTE]
> `Get-AzAutomationCredential` では、認証に使用できる `PSCredential` オブジェクトを取得しません。 資格情報に関する情報が提供されるだけです。 Runbook で資格情報を使用する必要がある場合は、`Get-AutomationPSCredential` を使用して `PSCredential` オブジェクトとして取得する必要があります。

### <a name="textual-runbook-example"></a>テキスト形式の Runbook の例

次の例では、Runbook で PowerShell 資格情報を使用する方法を示しています。 資格情報を取得して、そのユーザー名とパスワードを変数に割り当てます。


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

資格情報を使用して [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0) で Azure を認証することもできます。 ほとんどの状況下では、[[実行アカウント]](../manage-runas-account.md) を使用して、[Get-AzAutomationConnection](../automation-connections.md) との接続を取得する必要があります。


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>グラフィカルな Runbook の例

グラフィカル エディターの [ライブラリ] ウィンドウで資格情報を右クリックし、 **[キャンバスに追加]** を選択することで、`Get-AutomationPSCredential` アクティビティをグラフィカルな Runbook に追加できます。

![キャンバスに資格情報を追加する](../media/credentials/credential-add-canvas.png)

次の図は、グラフィカルな Runbook で資格情報を使用する例を示したものです。 この例では、「[Azure Automation で Azure AD を使用して Azure に対して認証する](../automation-use-azure-ad.md)」で説明されているように、資格情報によって、Azure リソースに対する認証が Runbook に提供されています。 最初のアクティビティは、Azure サブスクリプションへのアクセス権を持つ資格情報を取得します。 その後、アカウント接続アクティビティは、この資格情報を使用して、その後にあるすべてのアクティビティに認証を提供します。 ここでは、`Get-AutomationPSCredential` が単一オブジェクトを想定しているため、[パイプライン リンク](../automation-graphical-authoring-intro.md#links-and-workflow)が使用されています。  

![キャンバスに資格情報を追加する](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>DSC 構成での資格情報の使用

Azure Automation の DSC 構成では、`Get-AutomationPSCredential` を使用して資格情報資産を操作できますが、パラメーターを使用して資格情報資産を渡すこともできます。 詳細については、「 [Azure Automation DSC での構成のコンパイル](../automation-dsc-compile.md#credential-assets)」を参照してください。

## <a name="using-credentials-in-python2"></a>Python2 の資格情報の使用

次の例では、Python2 Runbook で資格情報にアクセスする例を示しています。


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>次のステップ

* グラフィカル作成でのリンクの詳細については、[グラフィカル作成でのリンク](../automation-graphical-authoring-intro.md#links-and-workflow)に関するページを参照してください。
* Automation のさまざまな認証方法を理解するには、[Azure Automation のセキュリティ](../automation-security-overview.md)に関するページを参照してください。
* グラフィカル Runbook の使用を開始するには、「[初めてのグラフィカルな Runbook](../automation-first-runbook-graphical.md)」を参照してください。
* PowerShell Workflow Runbook の使用を開始するには、「 [最初の PowerShell Workflow Runbook](../automation-first-runbook-textual.md)」を参照してください。
* Python2 Runbook の使用を開始するには、[初めての Python2 Runbook](../automation-first-runbook-textual-python2.md) に関するページを参照してください。 
