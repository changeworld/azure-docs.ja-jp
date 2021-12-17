---
title: Azure Automation でのコンテキスト切り替え
description: この記事では、コンテキスト切り替えと、Runbook の問題を回避する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 09/27/2021
ms.topic: conceptual
ms.openlocfilehash: 121e302708fecd20934f6ba57bd08590e45a7429
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129233202"
---
# <a name="context-switching-in-azure-automation"></a>Azure Automation でのコンテキスト切り替え

コンテキスト切り替えとは、あるプロセスのコンテキストによって別のプロセスのコンテキストが変更されることです。 "Azure コンテキスト" とは、Azure PowerShell コマンドレットの対象を定義する一連の情報です。 コンテキストは次のプロパティで構成されます。

|プロパティ | 説明 |
|---|---|
|名前 | コンテキストの名前。|
|Account | Azure との通信の認証に使用されるユーザー名またはサービス プリンシパル。|
|環境 | Azure グローバルまたは各国の Azure クラウドの 1 つ (Azure Government など) を表します。 また、Azure Stack などのハイブリッド クラウド プラットフォームを指定することもできます。|
|サブスクリプション | 管理する必要のあるリソースを含む Azure サブスクリプションを表します。|
|Tenant | 1 つの組織を表す Azure Active Directory の信頼された専用インスタンス。|
|資格情報 | ID の確認と Azure 内に含まれているリソースへのアクセスの認可のために Azure で使用される情報。|

複数のサブスクリプションにアクセスできるアカウントがサインオンすると、それらのサブスクリプションはどれでもユーザーのコンテキストに追加できるようになります。 正しいものであることを保証するため、接続時にはサブスクリプションを宣言する必要があります。 たとえば、 `Add-AzAccount -Credential $Cred -subscription 'cd4dxxxx-xxxx-xxxx-xxxx-xxxxxxxx9749'`を使用します。 ただし、あるサブスクリプションを管理する Runbook が、同じ Automation アカウントから別のサブスクリプションのリソースを管理する他の Runbook と同じサンドボックス プロセスで実行されている場合、問題が発生するおそれがあります。 1 つの Runbook によって加えられたコンテキストへの変更が、既定のコンテキストを使用している他の Runbook に影響を与えるおそれがあります。 コンテキストには使用する資格情報やターゲットのサブスクリプションなどの情報が含まれているので、コマンドレットが間違ったサブスクリプションをターゲットにし、その結果 `not found` またはアクセス許可エラーが発生するおそれがあります。 この問題は、**コンテキスト切り替え** と呼ばれます。

## <a name="manage-azure-contexts"></a>Azure コンテキストを管理する

Runbook が間違ったサブスクリプションのリソースに対して実行されないようにするため、次の推奨事項をご確認ください。

1. 各 Runbook の最初に次のコマンドを使用して、Automation Runbook 内でサンドボックス コンテキストの保存を無効にします: `Disable-AzContextAutosave -Scope Process`。
1. Azure PowerShell コマンドレットでは、`-DefaultProfile` パラメーターがサポートされています。 このパラメーターは、同じプロセスで複数のスクリプトを実行できるようにするために、すべての Az および Azure Resource Manager (AzureRM) コマンドレットに追加されました。これにより、使用するコンテキストをコマンドレットごとに指定することができます。 コンテキスト オブジェクトは、作成された時に加えて、変更されるたびに Runbook に保存します。 その後に、Az または AzureRM コマンドレットで行われる各呼び出しで参照します。 たとえば、「 `$AzureContext = Set-AzContext -SubscriptionId $subID` 」のように入力します。
1. コンテキスト オブジェクトを PowerShell コマンドレットに渡します (例: `Get-AzVM -ResourceGroupName "myGroup" -DefaultProfile $AzureContext`)。

コンテキスト切り替えを回避するための推奨事項に従ってシステム割り当てマネージド ID を使用する PowerShell Runbook コード スニペットを次に示します。

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext

# Pass context object - even though the context had just been set
# This is the step that guarantees the context will not be switched.
Get-AzVM -ResourceGroupName "resourceGroupName" -DefaultProfile $AzureContext | Select Name
```

## <a name="possible-symptoms"></a>考えられる現象

これらの推奨事項に従わなくても問題が発生しない場合もあるとはいえ、そのおそれがあるのは確かです。 この状況の根底にある問題はタイミングです。これは、他の Runbook でコンテキストが切り替わる時点で各 Runbook によって何が実行されているかによって異なります。 次に、考えられるエラー メッセージの例をいくつか示します。 ただし、これらのエラー メッセージはコンテキスト切り替え以外の条件が原因で発生する可能性もあります。

`The subscription named <subscription name> cannot be found.`

```error
Get-AzVM : The client '<automation-runas-account-guid>' with object id '<automation-runas-account-guid>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope '/subscriptions/<subcriptionIdOfSubscriptionWichDoesntContainTheVM>/resourceGroups/REsourceGroupName/providers/Microsoft.Compute/virtualMachines/VMName '.
   ErrorCode: AuthorizationFailed
   StatusCode: 403
   ReasonPhrase: Forbidden Operation
   ID : <AGuidRepresentingTheOperation> At line:51 char:7 + $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $UNBV... +
```

```error
Get-AzureRmResource : Resource group "SomeResourceGroupName" could not be found.
... resources = Get-AzResource -ResourceGroupName $group.ResourceGro ...
                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Get-AzResource], CloudException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.ResourceManager.Cmdlets.Implementation.GetAzureResourceCmdlet
```

## <a name="next-steps"></a>次のステップ

- [Azure Automation アカウントの認証の概要](automation-security-overview.md)
- [Azure Automation での Runbook の実行](automation-runbook-execution.md)
- [Azure Automation でモジュールを管理する](./shared-resources/modules.md)

