---
title: Azure Automation Runbook のエラーをトラブルシューティングする
description: Azure Automation Runbook で発生する可能性のある問題のトラブルシューティングと解決の方法について学習します。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 26c5c5b31d5f3f9e1a642c0bafb947190e479055
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632624"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Runbook のエラーをトラブルシューティングする

Azure Automation で Runbook を実行しているときにエラーが発生した場合は、次の手順を使用すると、問題が診断しやすくなります。

1. **ローカル コンピューターで Runbook スクリプトが正常に実行されることを確保します。** 

    言語リファレンスと学習モジュールについては、[PowerShell のドキュメント](/powershell/scripting/overview)または [Python のドキュメント](https://docs.python.org/3/)を参照してください。 次のような一般的なエラーは、スクリプトをローカルで実行することによって検出し、解決することができます。

      * モジュールの不足
      * 構文エラー
      * 論理エラー

2. **Runbook の[エラー ストリーム](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output)を調査します。**

    これらのストリームで特定のメッセージを確認し、この記事に記載されているエラーと比較します。

3. **ノードと Automation ワークスペースに必要なモジュールを確保します。** 

    Runbook でモジュールをインポートする場合は、[モジュールのインポート](../shared-resources/modules.md#importing-modules)に関する記述で一覧表示されている手順を使用して、それらが Automation アカウントで使用可能であることを確認します。 [Azure Automation の Azure モジュールを更新する方法](..//automation-update-azure-modules.md)に関するページの手順に従って、モジュールを最新バージョンに更新してください。 トラブルシューティングの詳細については、[モジュールのトラブルシューティング](shared-resources.md#modules)に関する記述を参照してください。

4. **Runbook が中断されたか、予期せず失敗した場合に実行します。**

    * [ジョブの状態を確認する](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) (Runbook の状態と考えられるいくつかの原因が定義されています)。
    * [新たな出力を Runbook に追加](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams)して、Runbook の中断前に何が発生したかを特定する。
    * ジョブによってスローされる[例外を処理する](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions)。

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>シナリオ:Login-AzureRMAccount を実行してログインする

### <a name="issue"></a>問題

Runbook を実行すると、次の例外を受け取ります。

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>原因

このエラーは、実行アカウントを使用していないとき、または実行アカウントの有効期限が切れたときに発生することがあります。 「[Azure Automation の実行アカウントを管理する](https://docs.microsoft.com/azure/automation/manage-runas-account)」を参照してください。

このエラーには、次の 2 つの主要な原因があります。

* さまざまなバージョンの AzureRM または Az モジュールがあります。
* 別のサブスクリプションのリソースにアクセスしようとしています。

### <a name="resolution"></a>解像度

1 つの AzureRM または Az モジュールを更新した後でこのエラーが発生する場合は、すべてのモジュールを同じバージョンに更新する必要があります。

別のサブスクリプションのリソースにアクセスしようとしている場合は、次の手順に従ってアクセス許可を構成できます。

1. Automation の実行アカウントに移動し、アプリケーション ID とサムプリントをコピーします。
  ![アプリケーション ID とサムプリントをコピーする](../media/troubleshoot-runbooks/collect-app-id.png)
1. Automation アカウントがホストされていないサブスクリプションの Access Control に移動し、新しいロールの割り当てを追加します。
  ![アクセス制御](../media/troubleshoot-runbooks/access-control.png)
1. 前に収集したアプリケーション ID を追加します。 共同作成者のアクセス許可を選択します。
   ![ロールの割り当てを追加する](../media/troubleshoot-runbooks/add-role-assignment.png)
1. サブスクリプションの名前をコピーします。
1. 次の Runbook コードを使用して、Automation アカウントから他のサブスクリプションへのアクセス許可をテストできるようになります。 `"\<CertificateThumbprint\>"` を、手順 1. でコピーした値に置き換えます。 `"\<SubscriptionName\>"` を、手順 4. でコピーした値に置き換えます。

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzureRmSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzureRmSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzureRmContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>シナリオ:Azure サブスクリプションが見つからない

### <a name="issue"></a>問題

`Select-AzureSubscription` または `Select-AzureRmSubscription` コマンドレットを使用するときに、次のエラーが表示されます。

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>エラー

このエラーは、次の場合に発生する可能性があります。

* サブスクリプション名が有効ではない。
* サブスクリプションの詳細を取得しようとしている Azure Active Directory ユーザーが、サブスクリプションの管理者として構成されていない。

### <a name="resolution"></a>解像度

以下の手順に従って、Azure に対して認証済みで、選択しようとしているサブスクリプションにアクセスできるかどうかを確認します。

1. スクリプトがスタンドアロンで動作することを確認するには、Azure Automation の外部でテストします。
2. `Select-AzureSubscription` コマンドレットを実行する前に、スクリプトで `Add-AzureAccount` コマンドレットが実行されていることを確認します。
3. Runbook の先頭に `Disable-AzureRmContextAutosave –Scope Process` を追加します。 このコマンドレットを呼び出すことで、すべての資格情報が現在の Runbook の実行にのみ適用されるようになります。
4. それでもこのエラー メッセージが表示される場合は、`Add-AzureAccount` コマンドレットの `AzureRmContext` パラメーターを追加してコードを変更してから、コードを実行します。

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

## <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>シナリオ:多要素認証が有効になっているために Azure に対する認証が失敗した

### <a name="issue"></a>問題

Azure のユーザー名とパスワードで Azure に対して認証するときに、次のエラーを受け取ります。

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>原因

Azure アカウントに多要素認証を設定している場合、Azure に対する認証に Azure Active Directory ユーザーを使うことはできません。 代わりに、証明書またはサービス プリンシパルを使用して認証する必要があります。

### <a name="resolution"></a>解像度

Azure クラシック デプロイ モデルのコマンドレットで証明書を使用する場合は、[Azure サービスを管理するための証明書の作成および追加](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)に関するページを参照してください。 Azure Resource Manager コマンドレットでサービス プリンシパルを使用する場合は、[Azure portal を使用するサービス プリンシパルの作成](../../active-directory/develop/howto-create-service-principal-portal.md)および [Azure Resource Manager でのサービス プリンシパルの認証](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)に関するページを参照してください。

## <a name="scenario-you-see-an-error-in-your-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>シナリオ:ジョブ ストリームで get_SerializationSettings メソッドについてのエラーが表示される

### <a name="issue"></a>問題

Runbook のジョブ ストリームで、次のエラーが表示されます。

```error
Connect-AzureRMAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzureRmAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

### <a name="cause"></a>原因

このエラーは、Runbook で AzureRM と Az モジュールの両方のコマンドレットを使用することで発生します。 AzureRM モジュールをインポートする前に、Az モジュールをインポートすると発生します。

### <a name="resolution"></a>解像度

Az および AzureRM コマンドレットは、同じ Runbook にインポートして使用することはできません。 Azure Automation での Az コマンドレットの詳細については、「[Azure Automation での Az モジュールのサポート](../az-modules.md)」を参照してください。

## <a name="scenario-the-runbook-fails-with-the-error-a-task-was-canceled"></a><a name="task-was-cancelled"></a>シナリオ:Runbook がエラーで失敗:タスクはキャンセルされました

### <a name="issue"></a>問題

Runbook が、次の例のようなエラーで失敗します。

```error
Exception: A task was canceled.
```

### <a name="cause"></a>原因

以前のバージョンの Azure モジュールを使用すると、このエラーが発生する可能性があります。

### <a name="resolution"></a>解像度

このエラーは、Azure モジュールを最新バージョンに更新することで解決できます。 

1. Automation アカウントで、 **[モジュール]** 、 **[Azure モジュールの更新]** の順にクリックします。 
2. 更新には約 15 分かかります。 完了したら、失敗した Runbook を再実行します。

モジュールの更新の詳細については、「[Azure Automation で Azure モジュールを更新する](../automation-update-azure-modules.md)」を参照してください。

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>シナリオ:複数のサブスクリプションを処理するときに Runbook が失敗する

### <a name="issue"></a>問題

Runbook を実行したときに、Runbook が Azure リソースを管理できません。

### <a name="cause"></a>原因

Runbook が、実行中に正しいコンテキストを使用していません。

### <a name="resolution"></a>解像度

1 つの Runbook で複数の Runbook を呼び出すと、サブスクリプション コンテキストが失われる可能性があります。 サブスクリプション コンテキストが確実に Runbook に渡されるようにするには、クライアント Runbook で `AzureRmContext` パラメーターの `Start-AzureRmAutomationRunbook` コマンドレットにコンテキストを渡すようにします。 `Scope` パラメーターを `Process` に設定した `Disable-AzureRmContextAutosave` コマンドレットを使用して、指定された資格情報が確実に現在の Runbook にのみ使用されるようにします。 詳細については、「[複数のサブスクリプションの操作](../automation-runbook-execution.md#working-with-multiple-subscriptions)」を参照してください。

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRmContext $AzureContext `
    –Parameters $params –wait
```

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-script"></a><a name="not-recognized-as-cmdlet"></a>シナリオ:コマンドレット、関数、スクリプトの名前として用語が認識されない

### <a name="issue"></a>問題

Runbook が、次の例のようなエラーで失敗します。

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>原因

このエラーは、次の理由で発生する可能性があります。

* コマンドレットを含むモジュールが、Automation アカウントにインポートされていない。
* コマンドレットを含むモジュールはインポートされているが、最新ではない。

### <a name="resolution"></a>解像度

このエラーを解決するには、次のいずれかのタスクを実行します。 

* Azure モジュールの場合は、Automation アカウントでモジュールを更新する方法について、「[Azure Automation の Azure PowerShell モジュールを更新する方法](../automation-update-azure-modules.md)」を参照してください。

* Azure 以外のモジュールの場合は、モジュールが Automation アカウントにインポートされていることを確認してください。

## <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-failed-to-start-each-time"></a><a name="job-attempted-3-times"></a>シナリオ:Runbook ジョブの開始を 3 回試行したが、開始できなかった

### <a name="issue"></a>問題

Runbook が次のエラーで失敗します。

```error
The job was tried three times but it failed
```

### <a name="cause"></a>原因

このエラーは、次のいずれかの問題が原因で発生します。

* メモリの制限。 400 MB を超えるメモリを使用すると、ジョブが失敗する可能性があります。 サンドボックスに割り当てられるメモリの制限については、[Automation サービスの制限](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)に関するセクションを参照してください。 

* ネットワーク ソケット。 Azure サンドボックスの同時ネットワーク ソケット数は 1000 に制限されています。 [Automation サービスの制限](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)に関するセクションを参照してください。

* モジュールに互換性がない。 モジュールの依存関係が正しくない可能性があります。 この場合、Runbook では通常、`Command not found` または `Cannot bind parameter` メッセージが返されます。

* サンドボックスで Active Directory による認証が行われない。 Runbook で、Azure サンドボックス内で実行されている実行可能ファイルまたはサブプロセスを呼び出そうとしました。 Azure Active Directory 認証ライブラリ (ADAL) を使用して Azure AD で認証するように Runbook を構成することはサポートされていません。

* 例外データが多すぎる。 Runbook により、例外データの出力ストリームへの書き込みが過剰に試行されました。

### <a name="resolution"></a>解像度

* メモリの制限、ネットワーク ソケット。 メモリの制限内で問題を解決するために推奨される方法としては、複数の Runbook 間でワークロードを分割する、メモリ内のデータの処理量を減らす、Runbook からの不要な出力を書き込まない、PowerShell Workflow Runbook に書き込むチェックポイントの数を検討する、などがあります。 `$myVar.clear` などの clear メソッドを使用して変数をクリアし、`[GC]::Collect` を使用してガベージ コレクションをすぐに実行します。 これにより、実行時の Runbook のメモリ専有領域が小さくなります。

* モジュールに互換性がない。 「[Azure Automation の Azure PowerShell モジュールを更新する方法](../automation-update-azure-modules.md)」の手順に従って Azure モジュールを更新します。

* サンドボックスでは Active Directory による認証が行われない。 Runbook で Azure AD に対する認証を行うときは、Automation アカウントで Azure AD モジュールが使用可能になっていることを確認します。 Runbook によって自動化されるタスクを実行するために必要なアクセス許可を、必ず実行アカウントに付与してください。

  Azure サンドボックスで実行されている実行可能ファイルまたはサブプロセスを Runbook で呼び出すことができない場合は、[Hybrid Runbook Worker](../automation-hrw-run-runbooks.md) で Runbook を使用します。 ハイブリッド worker には、Azure サンドボックスに存在するようなメモリとネットワークの制限はありません。

* 例外データが多すぎる。 ジョブ出力ストリームでは 1 MB の制限があります。 Runbook では確実に、`try` および `catch` ブロックを使用して、実行可能ファイルまたはサブプロセスへの呼び出しを囲むようにしてください。 操作で例外がスローされる場合は、コードによってその例外から Automation 変数にメッセージを書き込みます。 この手法により、メッセージがジョブ出力ストリームに書き込まれなくなります。

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>シナリオ:Azure アカウントにサインインできない

### <a name="issue"></a>問題

`Add-AzureAccount` または `Connect-AzureRmAccount` コマンドレットを使用するときに、次のいずれかのエラーが表示されます。

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>原因

これらのエラーは、資格情報資産名が有効でない場合に発生します。 Automation 資格情報資産の設定に使用したユーザー名とパスワードが有効でない場合にも発生する可能性があります。

### <a name="resolution"></a>解像度

次の手順で原因を突き止めます。

1. 特殊文字が使用されていないことを確認します。 これらの特殊文字には、Azure に接続するために使用している Automation 資格情報資産名の `\@` 文字が含まれます。
2. ローカルの PowerShell ISE エディターで、Azure Automation 資格情報に格納されているユーザー名とパスワードを使用できるかどうかを確認します。 PowerShell ISE で、次のコマンドレットを実行します。

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzureRmAccount –Credential $Cred
   ```

3. ローカルで認証に失敗した場合、Azure Active Directory 資格情報が適切に設定されていません。 Azure Active Directory アカウントが正しく設定されるようにするために、「[Azure Active Directory を使用した Azure の認証](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/)」というブログ記事を参照してください。

4. エラーが一時的なものであると思われる場合は、認証をより堅牢にするために認証ルーチンに再試行ロジックを追加してみてください。

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       #Logging in to Azure...
       $connectionResult = Connect-AzureRmAccount `
                              -ServicePrincipal `
                              -TenantId $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>シナリオ:オブジェクト参照がオブジェクト インスタンスに設定されていない

### <a name="issue"></a>問題

`Wait` パラメーターを使用して子 Runbook を呼び出すときに、出力ストリームにオブジェクトが含まれていると、次のエラーが表示されます。

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>原因

ストリームにオブジェクトが含まれている場合、`Start-AzureRmAutomationRunbook` では出力ストリームが正しく処理されません。

### <a name="resolution"></a>解像度

ポーリング ロジックを実装し、[Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) コマンドレットを使用して出力を取得することをお勧めします。 このロジックのサンプルは以下のように定義されています。

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzureRmAutomationJob
}

$jobResults | Get-AzureRmAutomationJobOutput | Get-AzureRmAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>シナリオ:逆シリアル化されたオブジェクトであるため、Runbook が失敗する

### <a name="issue"></a>問題

Runbook がエラーで失敗します。

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>原因

Runbook が PowerShell ワークフローの場合、ワークフローが中断された場合に Runbook の状態を維持できるように、複雑なオブジェクトが逆シリアル化形式で保存されます。

### <a name="resolution"></a>解像度

この問題を解決するには、次のいずれかの解決策を使用します。

* コマンドレット間で複雑なオブジェクトをパイプ処理する場合、これらのコマンドレットを `InlineScript` アクティビティでラップします。
* オブジェクト全体を渡すのではなく、複雑なオブジェクトから、必要な名前または値を渡します。
* PowerShell ワークフロー Runbook ではなく PowerShell Runbook を使用します。

## <a name="scenario-runbook-job-fails-because-allocated-quota-exceeded"></a><a name="quota-exceeded"></a>シナリオ:割り当てられたクォータを超えているために Runbook ジョブが失敗する

### <a name="issue"></a>問題

Runbook ジョブがエラーで失敗します。

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>原因

ジョブの実行がアカウントの 500 分の無料クォータを超えるとこのエラーが発生します。 このクォータは、すべての種類のジョブ実行タスクに適用されます。 これらのタスクには、ジョブのテスト、ポータルからのジョブの開始、Webhook を使用したジョブの実行、Azure portal またはデータセンターを使用して実行するジョブのスケジュール設定などがあります。 Automation の料金については、「[Automation の料金](https://azure.microsoft.com/pricing/details/automation/)」を参照してください。

### <a name="resolution"></a>解像度

毎月 500 分を超える処理を使用する場合は、サブスクリプションを Free レベルから Basic レベルに変更します。

1. Azure サブスクリプションにサインインします。
2. アップグレードする Automation アカウントを選択します。
3. **[設定]** 、 **[料金]** の順にクリックします。
4. ページの下部にある **[有効]** をクリックして、アカウントを Basic レベルにアップグレードします。

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>シナリオ:Runbook の実行時にコマンドレットが認識されない

### <a name="issue"></a>問題

Runbook ジョブがエラーで失敗します。

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>原因

このエラーは、Runbook で使用しているコマンドレットを PowerShell エンジンが見つけられないときに発生します。 コマンドレットを含むモジュールがアカウントにないか、Runbook 名に名前の競合があるか、あるいはコマンドレットが別のモジュールにも存在していて Automation で名前を解決できない可能性があります。

### <a name="resolution"></a>解像度

問題を解決するには、次のいずれかの解決策を使用します。

* コマンドレット名を正しく入力したことを確認します。
* 確実に、コマンドレットが Automation アカウントに存在し、競合がないようにします。 コマンドレットの存在を確認するには、Runbook を編集モードで開き、ライブラリで見つけるコマンドレットを検索するか、`Get-Command <CommandName>` を実行します。 コマンドレットがアカウントで利用できることと他のコマンドレットや Runbook と名前が競合しないことを確認したら、コマンドレットをキャンバスに追加し、Runbook に設定されている有効なパラメーターを使用していることを確認します。
* 名前が競合し、コマンドレットが 2 つの異なるモジュールで利用できる場合、コマンドレットの完全修飾名を利用することで、問題を解決します。 たとえば、`ModuleName\CmdletName` を使用できます。
* ハイブリッド worker グループでオンプレミスの Runbook を実行する場合は、ハイブリッド worker をホストするマシンにモジュールとコマンドレットが確実にインストールされているようにします。

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>シナリオ:実行時間の長い Runbook が完了しない

### <a name="issue"></a>問題

Runbook は、3 時間実行した後、停止状態になります。 このエラーが表示されることもあります。

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

これは、Azure Automation 内のプロセスの[フェア シェア](../automation-runbook-execution.md#fair-share)監視のための、Azure サンドボックスでの設計による動作です。 プロセスの実行時間が 3 時間を超えた場合、フェア シェアによって自動的に Runbook が停止されます。 フェア シェアの時間の制限を超える Runbook の状態は、Runbook の種類によって異なります。 PowerShell および Python Runbook は、停止状態に設定されます。 PowerShell Workflow Runbook は、失敗に設定されます。

### <a name="cause"></a>原因

Runbook が、Azure サンドボックスのフェア シェアによって許可されている 3 時間の制限を超えて実行されました。

### <a name="resolution"></a>解像度

推奨される解決策の 1 つは、[Hybrid Runbook Worker](../automation-hrw-run-runbooks.md) で Runbook を実行することです。 ハイブリッド worker には、Azure サンドボックスに存在するフェア シェアによる 3 時間の Runbook 制限は適用されません。 予期しないローカル インフラストラクチャの問題が発生した場合の再起動動作をサポートするには、Hybrid Runbook Worker 上で実行される Runbook を開発する必要があります。

もう 1 つの解決策は、[子 Runbook](../automation-child-runbooks.md) を作成して Runbook を最適化することです。 Runbook で、複数のリソースに対して同じ関数をループ処理する場合 (複数のデータベースに対するデータベース処理などで)、関数を子 Runbook に移動することができます。 各子 Runbook は、別のプロセスで並列に実行されます。 この動作によって、親 Runbook の完了までにかかる合計時間は減ります。

子 Runbook のシナリオを実現する PowerShell コマンドレットは次のとおりです。

* [Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook)。 このコマンドレットを使用すると、Runbook を起動し、パラメーターを Runbook に渡すことができます。

* [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob)。 子 Runbook の完了後に実行する必要がある操作がある場合は、このコマンドレットを使用して、各子のジョブの状態を確認できます。

## <a name="scenario-status-400-bad-request-when-calling-a-webhook"></a><a name="expired webhook"></a>シナリオ:状態:Webhook を呼び出すときに 400 Bad Request が発生する

### <a name="issue"></a>問題

Azure Automation Runbook の Webhook を呼び出そうとすると、次のエラーが表示されます。

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>原因

呼び出そうとしている Webhook が、無効または期限切れです。

### <a name="resolution"></a>解像度

Webhook が無効な場合は、Azure portal から Webhook を再度有効にすることができます。 Webhook の有効期限が切れている場合は、削除してから再作成する必要があります。 Webhook がまだ期限切れでない場合にのみ、[Webhook を更新](../automation-webhooks.md#renew-webhook)できます。

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>シナリオ:429:The request rate is currently too large (現在、要求レートが大きすぎます)

### <a name="issue"></a>問題

`Get-AzureRmAutomationJobOutput` コマンドレットを実行すると、次のエラー メッセージを受け取ります。

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>原因

このエラーは、多数の[冗長ストリーム](../automation-runbook-output-and-messages.md#verbose-stream)がある Runbook からジョブ出力を取得するときに発生する可能性があります。

### <a name="resolution"></a>解像度

このエラーを解決するには、次のいずれかの操作を行います。

* Runbook を編集し、出力されるジョブ ストリームの数を減らします。

* コマンドレットを実行するときに取得されるストリーム数を減らします。 これを行うために、`Get-AzureRmAutomationJobOutput` コマンドレットに `Stream` パラメーターの値を設定して、出力ストリームのみを取得できます。 

## <a name="scenario-powershell-job-fails-with-error-cannot-invoke-method"></a><a name="cannot-invoke-method"></a>シナリオ:PowerShell ジョブが次のエラーで失敗する: メソッドを呼び出せません

### <a name="issue"></a>問題

Azure で実行されている Runbook で PowerShell ジョブを開始するときに、次のエラー メッセージが表示されます。

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>原因

このエラーは、Azure サンドボックスで実行されている Runbook を、[完全言語モード](/powershell/module/microsoft.powershell.core/about/about_language_modes)で実行できないことを示している可能性があります。

### <a name="resolution"></a>解像度

このエラーを解決するには、2 つの方法があります。

* `Start-Job` を使用する代わりに、`Start-AzureRmAutomationRunbook` を使って Runbook を開始します。
* Hybrid Runbook Worker で Runbook を実行してみます。

この動作や、Azure Automation Runbook の他の動作の詳細については、「[Runbook の動作](../automation-runbook-execution.md#runbook-behavior)」を参照してください。

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>シナリオ:Linux Hybrid Runbook Worker で、Runbook に署名するときにパスワードの入力が求められる

### <a name="issue"></a>問題

Linux Hybrid Runbook Worker で `sudo` コマンドを実行すると、パスワードの入力を求める想定外のプロンプトが表示されます。

### <a name="cause"></a>原因

Linux 用 Log Analytics エージェントの **nxautomationuser** アカウントが、**sudoers** ファイルで正しく構成されていません。 Hybrid Runbook Worker では、Linux Runbook Worker で Runbook に署名できるように、アカウントのアクセス許可やその他のデータが適切に構成されている必要があります。

### <a name="resolution"></a>解像度

* マシン上で、Hybrid Runbook Worker に GnuPG (GPG) の実行可能ファイルがあることを確認します。

* **sudoers** ファイルで **nxautomationuser** アカウントの構成を確認します。 「[Hybrid Runbook Worker での Runbook の実行](../automation-hrw-run-runbooks.md)」を参照してください

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>シナリオ:Azure Automation 上の PnP PowerShell Runbook でコマンドレットが失敗する

### <a name="issue"></a>問題

PnP PowerShell で生成されたオブジェクトが Runbook から Azure Automation の出力に直接書き込まれると、コマンドレットの出力をオートメーションにストリーム バックすることができません。

### <a name="cause"></a>原因

この問題は、戻りオブジェクトをキャッチせずに、`add-pnplistitem` などの PnP PowerShell コマンドレットを呼び出す Runbook を Azure Automation で処理する場合に最もよく発生します。

### <a name="resolution"></a>解像度

スクリプトを編集して戻り値を変数に割り当て、コマンドレットからオブジェクト全体が標準出力に書き出されないようにします。 次に示すように、スクリプトを使って出力ストリームをコマンドレットにリダイレクトできます。

```azurecli
  $null = add-pnplistitem
```

スクリプトを使ってコマンドレットの出力を解析する場合は、単に出力をストリーミングするのではなく、スクリプトで出力を変数に格納し、変数を操作する必要があります。

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="my-problem-isnt-listed-above"></a><a name="other"></a>問題が上記の一覧にない

以下のセクションでは、その他の一般的なエラーを一覧表示し、問題の解決に役立つサポート ドキュメントを提供します。

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>Hybrid Runbook Worker がジョブを実行しないか、応答していない

Azure Automation ではなく、Hybrid Runbook Worker でジョブを実行している場合は、[ハイブリッド worker 自体のトラブルシューティング](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)が必要になることがあります。

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Runbook がアクセス許可なし、または類似した問題によって失敗する

実行アカウントの Azure リソースに対するアクセス許可が、お使いの現在のアカウントと同じでない可能性があります。 ご自身の実行アカウントに、お使いのスクリプトで使用されている[リソースにアクセスするためのアクセス許可がある](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)ことを確認してください。

### <a name="issues-passing-parameters-into-webhooks"></a>パラメーターを Webhook に渡すときに問題が発生する

パラメーターを Webhook に渡す場合のヘルプについては、「[Webhook から Runbook を開始する](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook)」を参照してください。

### <a name="issues-using-az-modules"></a>Az モジュールを使用するときに問題が発生する

Az モジュールと AzureRM モジュールを同じ Automation アカウントで使用することはできません。 詳細については、[Runbook の Az モジュール](https://docs.microsoft.com/azure/automation/az-modules)に関するページを参照してください。

### <a name="inconsistent-behavior-in-runbooks"></a>Runbook 内の一貫性のない動作

[Runbook の実行](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior)に関する記述のガイダンスに従って、Runbook での同時実行ジョブ、リソースの複数回の作成、またはその他のタイミングに依存するロジックに関する問題を回避してください。

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Runbook が、アクセス許可なし、禁止 (403)、または類似したエラーによって失敗する

実行アカウントの Azure リソースに対するアクセス許可が、お使いの現在のアカウントと同じでない可能性があります。 ご自身の実行アカウントに、お使いのスクリプトで使用されている[リソースにアクセスするためのアクセス許可がある](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)ことを確認してください。

### <a name="runbooks-were-working-but-suddenly-stopped"></a>以前は動作していた Runbook が突然停止した

* 実行アカウントが確実に有効期限内であるようにします。 [証明書の更新](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal)に関するセクションを参照してください。
* [Webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) を使用して Runbook を開始する場合は、Webhook が確実に有効期限内であるようにします。

### <a name="passing-parameters-into-webhooks"></a>パラメーターをスクリプトに渡す

パラメーターを Webhook に渡す場合のヘルプについては、「[Webhook から Runbook を開始する](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook)」を参照してください。

### <a name="using-az-modules"></a>Az モジュールの使用

Az モジュールと AzureRM モジュールを同じ Automation アカウントで使用することはできません。 [Runbook 内の Az モジュール](https://docs.microsoft.com/azure/automation/az-modules)に関するページを参照してください。

### <a name="using-self-signed-certificates"></a>自己署名証明書の使用

自己署名証明書を使用する場合は、「[新しい証明書の作成](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate)」を参照してください。

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>Runbook に Azure サンドボックスを使用しているときにアクセスが拒否されました

Azure サンドボックスでは、すべてのアウトプロセス COM サーバーへのアクセスを防ぎます。 たとえば、サンドボックス アプリケーションまたは Runbook では、Windows Management Instrumentation (WMI) や Windows インストーラー サービス (msiserver.exe) を呼び出すことはできません。 サンドボックスの使用について詳しくは、「[Azure Automation での Runbook の実行](https://docs.microsoft.com/azure/automation/automation-runbook-execution)」を参照してください。

## <a name="recommended-documents"></a>推奨されるドキュメント

* [Azure Automation での Runbook を開始する](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Azure Automation での Runbook の実行](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
* さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
