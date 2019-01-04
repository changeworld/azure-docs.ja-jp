---
title: Azure Automation Runbook のエラーをトラブルシューティングする
description: Azure Automation Runbook のエラーをトラブルシューティングする方法を説明します。
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 12/04/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 41eb31ecabb20ec9eec3db13d5eda9f9cfbe6c69
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015468"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Runbook のエラーをトラブルシューティングする

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Azure Automation Runbook の使用時に発生する認証エラー

### <a name="sign-in-failed"></a>シナリオ:Azure アカウントにサインインできない

#### <a name="issue"></a>問題

`Add-AzureAccount` または `Connect-AzureRmAccount` コマンドレットを使用するときに、次のエラーを受け取ります。
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>原因

このエラーは、資格情報資産名が無効な場合、または Automation 資格情報資産のセットアップに使用したユーザー名とパスワードが無効な場合に発生します。

#### <a name="resolution"></a>解決策

次の手順で原因を突き止めます。  

1. Azure に接続するために使用する Automation 資格情報資産名で、**@** 文字などの特殊文字が使用されていないことを確認します。  
2. ローカル PowerShell ISE エディターで、Azure Automation に保存されているユーザー名とパスワードを使用できることを確認します。 PowerShell ISE で次のコマンドレットを実行して、ユーザー名とパスワードが正しいことを確認できます。  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. ローカルで認証に失敗した場合、Azure Active Directory 資格情報が正しく設定されていないことになります。 Azure Active Directory アカウントを正しく設定する方法については、「 [Authenticating to Azure using Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) 」 (Azure Active Directory を使用して Azure を認証する) というブログ投稿を参照してください。  

4. 一時的なエラーと思われる場合は、認証をより強固にするために認証ルーチンに再試行ロジックを追加してみてください。

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
   $LogonAttempt++
   # Logging in to Azure...
   $connectionResult = Connect-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $servicePrincipalConnection.TenantId `
      -ApplicationId $servicePrincipalConnection.ApplicationId `
      -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

   Start-Sleep -Seconds 30
   }
   ```

### <a name="unable-to-find-subscription"></a>シナリオ:Azure サブスクリプションが見つからない

#### <a name="issue"></a>問題

`Select-AzureSubscription` または `Select-AzureRmSubscription` コマンドレットを使用するときに、次のエラーを受け取ります。

```
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Error

このエラーは、サブスクリプション名が無効な場合、またはサブスクリプションの詳細を取得しようとしている Azure Active Directory ユーザーがサブスクリプションの管理者として構成されていない場合に発生します。

#### <a name="resolution"></a>解決策

Azure で正しく認証され、選択しようとしているサブスクリプションにアクセスできることを次の手順で確認します。  

1. Azure Automation の外部でスクリプトをテストし、スクリプトがスタンドアロンで機能することを確認します。
2. **Select-AzureSubscription** コマンドレットを実行する前に、必ず **Add-AzureAccount** コマンドレットを実行してください。  
3. それでもエラー メッセージが表示される場合は、**Add-AzureAccount** コマンドレットの後に **-AzureRmContext** パラメーターを追加してコードを変更してから、コードを実行します。

   ```powershell
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

### <a name="auth-failed-mfa"></a>シナリオ:多要素認証が有効になっているために Azure に対する認証が失敗した

#### <a name="issue"></a>問題

Azure のユーザー名とパスワードで Azure に対して認証するときに、次のエラーを受け取ります。

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>原因

Azure アカウントに多要素認証を設定している場合、Azure に対する認証に Azure Active Directory ユーザーを使うことはできません。 代わりに、証明書またはサービス プリンシパルを利用して Azure に対して認証する必要があります。

#### <a name="resolution"></a>解決策

Azure クラシック デプロイ モデルのコマンドレットで証明書を使用する方法については、[証明書を作成し、追加して Azure サービスを管理する](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)方法に関するページを参照してください。 Azure Resource Manager コマンドレットでサービス プリンシパルを使用する方法については、[Azure ポータルでサービス プリンシパルを作成する](../../active-directory/develop/howto-create-service-principal-portal.md)方法に関する記事と [Azure Resource Manager でサービス プリンシパルを認証する](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)方法に関する記事を参照してください。

## <a name="common-errors-when-working-with-runbooks"></a>Runbook の使用時に発生する一般的なエラー

### <a name="task-was-cancelled"></a>シナリオ:Runbook がエラーで失敗:タスクはキャンセルされました

#### <a name="issue"></a>問題

Runbook が、次の例のようなエラーで失敗します。

```
Exception: A task was canceled.
```

#### <a name="cause"></a>原因

以前のバージョンの Azure モジュールを使用すると、このエラーが発生する可能性があります。

#### <a name="resolution"></a>解決策

このエラーは、Azure モジュールを最新のバージョンに更新すると解決できます。

Automation アカウントで、**[モジュール]** をクリックし、**[Azure モジュールの更新]** をクリックします。 この更新には、失敗した Runbook の再実行を完了してから約 15 分かかります。 モジュールの更新の詳細については、「[Azure Automation で Azure モジュールを更新する](../automation-update-azure-modules.md)」を参照してください。

### <a name="child-runbook-auth-failure"></a>シナリオ:複数のサブスクリプションを処理するときに子 Runbook が失敗する

#### <a name="issue"></a>問題

`Start-AzureRmRunbook` を使用して子 Runbook を実行したときに、子 Runbook が Azure リソースを管理できません。

#### <a name="cause"></a>原因

子 Runbook は、実行中に正しいコンテキストを使用していません。

#### <a name="resolution"></a>解決策

複数のサブスクリプションを使用している場合は、子 Runbook を呼び出したときにサブスクリプションのコンテキストが失われることがあります。 サブスクリプションのコンテキストが子 Runbook に渡されるようにするには、コマンドレットに `AzureRmContext` パラメーターを追加してそれにコンテキストを渡します。

```azurepowershell-interactive
# Connect to Azure with RunAs account
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

### <a name="not-recognized-as-cmdlet"></a>シナリオ:不足しているコマンドレットにより、Runbook が失敗する

#### <a name="issue"></a>問題

Runbook が、次の例のようなエラーで失敗します。

```
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>原因

このエラーは次の理由で発生する可能性があります。

1. コマンドレットを含むモジュールが、Automation アカウントにインポートされていない
2. コマンドレットを含むモジュールはインポートされているが、最新ではない

#### <a name="resolution"></a>解決策

このエラーは、次のタスクのいずれかを実行して解決できる場合があります。

モジュールが Azure モジュールの場合、Automation アカウントでモジュールを更新する方法について、「[Azure Automation の Azure PowerShell モジュールを更新する方法](../automation-update-azure-modules.md)」を参照してください。

個別のモジュールの場合、Automation アカウントにモジュールがインポートされていることを確認します。

### <a name="job-attempted-3-times"></a>シナリオ:Runbook ジョブの開始を 3 回試行したが、開始できない

#### <a name="issue"></a>問題

Runbook がエラーで失敗します。

```
The job was tried three times but it failed
```

#### <a name="cause"></a>原因

このエラーは次の理由で発生する可能性があります。

1. メモリの制限。 サンドボックスに割り当てられるメモリの制限については、「[Automation サービスの制限](../../azure-subscription-service-limits.md#automation-limits)」で説明されています。400 MB を超えるメモリを使用すると、ジョブが失敗することがあります。

1. ネットワーク ソケット。 「[Automation サービスの制限](../../azure-subscription-service-limits.md#automation-limits)」の説明のように、Azure サンド ボックスの同時ネットワーク ソケット数は 1,000 に制限されています。

1. モジュールに互換性がない。 このエラーは、モジュールの依存関係が正しくない場合に発生することがあります。この場合は通常、「Command not found (コマンドが見つかりません)」または「Cannot bind parameter (パラメーターをバインドできません)」というメッセージが Runbook から返されます。

#### <a name="resolution"></a>解決策

次の解決策のいずれでもこの問題は解決されます。

* メモリの制限内で問題を解決するために推奨される方法としては、複数の Runbook 間でワークロードを分割する、メモリ内のデータと同量のデータを処理しない、Runbook からの不要な出力を書き込まない、PowerShell Workflow Runbook に書き込むチェックポイントの数を検討する、などがあります。 `$myVar.clear()` などの clear メソッドを使用して変数をクリアし、`[GC]::Collect()` を使用してガベージ コレクションをすぐに実行します。これにより、実行時の Runbook のメモリ専有領域が小さくなります。

* 「[Azure Automation の Azure PowerShell モジュールを更新する方法](../automation-update-azure-modules.md)」の手順に従って Azure モジュールを更新します。  

* 別の解決策は、[Hybrid Runbook Worker](../automation-hrw-run-runbooks.md) で Runbook を実行することです。 ハイブリッド worker には、Azure サンドボックスのようなメモリとネットワークの制限はありません。

### <a name="fails-deserialized-object"></a>シナリオ:逆シリアル化されたオブジェクトであるため、Runbook が失敗する

#### <a name="issue"></a>問題

Runbook がエラーで失敗します。

```
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>原因

Runbook が PowerShell ワークフローの場合、ワークフローが中断された場合に Runbook の状態を維持できるように、複雑なオブジェクトが逆シリアル化形式で保存されます。

#### <a name="resolution"></a>解決策

次の 3 つの解決策のいずれでもこの問題は解決されます。

1. コマンドレット間で複雑なオブジェクトをパイプ処理する場合、これらのコマンドレットを InlineScript でラップします。
2. オブジェクト全体を渡すのではなく、複雑なオブジェクトから、必要な名前または値を渡します。
3. PowerShell ワークフロー Runbook ではなく PowerShell Runbook を使用します。

### <a name="quota-exceeded"></a>シナリオ:割り当てられたクォータを超えているために Runbook ジョブが失敗した

#### <a name="issue"></a>問題

Runbook ジョブがエラーで失敗します。

```
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>原因

ジョブの実行がアカウントの 500 分の無料クォータを超えるとこのエラーが発生します。 このクォータは、ジョブをテストする、ポータルからジョブを開始する、Webhook でジョブを実行する、Azure ポータルまたはデータセンターを利用して実行するジョブをスケジュールするなど、あらゆる種類のジョブ実行タスクに適用されます。 Automation の料金については、「[Automation の料金](https://azure.microsoft.com/pricing/details/automation/)」を参照してください。

#### <a name="resolution"></a>解決策

毎月 500 分以上の処理を使用する場合、サブスクリプションを Free レベルから Basic レベルに変更する必要があります。 次の手順で Basic レベルにアップグレードできます。  

1. Azure サブスクリプションにサインインします。  
2. アップグレードする Automation アカウントを選択します。  
3. **[設定]** > **[価格]** の順にクリックします。
4. ページ下部にある **[有効]** をクリックして、アカウントを **Basic** レベルにアップグレードします。

### <a name="cmdlet-not-recognized"></a>シナリオ:Runbook の実行時にコマンドレットが認識されない

#### <a name="issue"></a>問題

Runbook ジョブがエラーで失敗します。

```
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>原因

このエラーは、Runbook で使用しているコマンドレットを PowerShell エンジンが見つけられないときに発生します。 この原因としては、コマンドレットを含むモジュールがアカウントにない、Runbook 名に名前の競合がある、コマンドレットが別のモジュールにも存在し、Automation が名前を解決できないなどが考えられます。

#### <a name="resolution"></a>解決策

次の解決策のいずれでもこの問題は解決されます。  

* コマンドレット名を正しく入力していることを確認します。  
* Automation アカウントにコマンドレットが存在し、競合がないことを確認します。 コマンドレットの存在を確認するには、Runbook を編集モードで開き、ライブラリで見つけるコマンドレットを検索し、`Get-Command <CommandName>` を実行します。 コマンドレットがアカウントで利用できることと他のコマンドレットや Runbook と名前が競合しないことを確認したら、それをキャンバスに追加し、Runbook に設定されている有効なパラメーターを使用していることを確認します。  
* 名前が競合し、コマンドレットが 2 つの異なるモジュールで利用できる場合、コマンドレットの完全修飾名を利用することで解決できます。 たとえば、**ModuleName\CmdletName** を使用できます。  
* ハイブリッド worker グループでオンプレミスの runbook を実行する場合は、モジュールとコマンドレットがハイブリッド worker をホストしているマシンにインストールされていることを確認します。

### <a name="long-running-runbook"></a>シナリオ:実行時間の長い Runbook が完了しない

#### <a name="issue"></a>問題

Runbook は、3 時間実行すると**停止**状態になります。 次のエラーを受け取る可能性もあります。

```
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

これは Azure Automation 内のプロセスの "fair share" 監視のための Azure サンドボックスでの設計による動作です。3 時間以上実行している Runbook は自動的に中断されます。 fair-share の時間の制限を超える Runbook の状態は、Runbook の種類によって異なります。 PowerShell Runbook と Python Runbook は、**停止**状態に設定されます。 PowerShell Workflow Runbook は、**失敗**に設定されます。

#### <a name="cause"></a>原因

Runbook が、Azure サンドボックスの fair share によって許可されている 3 時間の制限を超えて実行しました。

#### <a name="resolution"></a>解決策

推奨される解決策の 1 つは、[Hybrid Runbook Worker](../automation-hrw-run-runbooks.md) で Runbook を実行することです。

Hybrid Worker には、Azure サンドボックスのような [fair share](../automation-runbook-execution.md#fair-share) による 3 時間の Runbook 制限はありません。 Hybrid Runbook Worker は 3 時間の fair share 制限では制限されていないものの、この Hybrid Runbook Worker 上で実行される Runbook は、予期しないローカル インフラストラクチャの問題が発生したときに再起動の動作がサポートされるよう、今後も開発していく必要があります。

もう 1 つのオプションは、[子 Runbook](../automation-child-runbooks.md) を作成して Runbook を最適化することです。 Runbook によって多数のリソース上で同じ関数をループ処理する場合 (複数のデータベースで同じデータベース操作を行うなど)、その関数を子 Runbook に移動することができます。 これらの各子 Runbook は、別々のプロセスで並列に実行されるため、親 Runbook が完了するまでの合計時間が減ります。

子 Runbook のシナリオを実現する PowerShell コマンドレットは次のとおりです。

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook): このコマンドレットを使用すると、Runbook を起動し、パラメーターを Runbook に渡すことができます

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob): このコマンドレットを使用すると、子 Runbook の完了後に実行する必要のある操作がある場合、各子のジョブの状態を確認できます。

### <a name="expired webhook"></a>シナリオ:状態:Webhook を呼び出すときに 400 Bad Request が発生する

#### <a name="issue"></a>問題

Azure Automation Runbook の Webhook を呼び出そうとすると、次のエラーが表示されます。

```error
400 Bad Request : This webhook has expired or is disabled
```

#### <a name="cause"></a>原因

呼び出そうとしている Webhook は無効か期限切れです。

#### <a name="resolution"></a>解決策

Webhook が無効な場合は、Azure portal から Webhook を再度有効にすることができます。 Webhook が期限切れの場合は、Webhook を削除して再作成する必要があります。 Webhook がまだ期限切れではない場合にのみ、[Webhook を更新](../automation-webhooks.md#renew-webhook)できます。

### <a name="429"></a>シナリオ:429:The request rate is currently too large. Please try again (現在、要求レートが大きすぎます。もう一度実行してください)

#### <a name="issue"></a>問題

`Get-AzureRmAutomationJobOutput` コマンドレットを実行すると、次のエラー メッセージが表示される場合があります。

```
429: The request rate is currently too large. Please try again
```

#### <a name="cause"></a>原因

このエラーは、多数の[冗長ストリーム](../automation-runbook-output-and-messages.md#verbose-stream)がある Runbook からジョブ出力を取得するときに発生する可能性があります。

#### <a name="resolution"></a>解決策

このエラーを解決するには、次の 2 つの方法があります。

* Runbook を編集し、出力されるジョブ ストリームの数を減らします。
* コマンドレットを実行するときに取得されるストリーム数を減らします。 この場合は、`Get-AzureRmAutomationJobOutput` コマンドレットに `-Stream Output` パラメーターを指定して出力ストリームのみを取得します。 

## <a name="common-errors-when-importing-modules"></a>モジュールのインポート時に発生する一般的なエラー

### <a name="module-fails-to-import"></a>シナリオ:モジュールがインポートに失敗するか、インポート後、コマンドレットを実行できない

#### <a name="issue"></a>問題

モジュールがインポートに失敗するか、成功してもコマンドレットが抽出されません。

#### <a name="cause"></a>原因

モジュールが正常に Azure Automation にインポートできない一般的な理由には次が考えられます。

* 構造が Automation で必要とされる構造と一致しません。
* Automation アカウントにデプロイされていない別のモジュールにモジュールが依存しています。
* モジュールのフォルダーにその依存関係がありません。
* モジュールのアップロードに `New-AzureRmAutomationModule` コマンドレットを使用していますが、完全なストレージ パスを指定していないか、パブリックにアクセスできる URL でモジュールを読み込んでいません。

#### <a name="resolution"></a>解決策

次の解決策のいずれでもこの問題は解決されます。

* モジュールの形式が次のようになっていることを確認します。ModuleName.Zip **->** モジュール名またはバージョン番号 **->** (ModuleName.psm1、ModuleName.psd1)
* .psd1 ファイルを開き、モジュールに依存関係があるかどうかを確認します。 依存関係がある場合、それらのモジュールを Automation アカウントにアップロードします。
* 参照される .dll がモジュール フォルダーにあることを確認します。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
* さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。