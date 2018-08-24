---
title: Azure Automation Runbook のエラーをトラブルシューティングする
description: Azure Automation Runbook のエラーをトラブルシューティングする方法を説明します。
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 07/13/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 53b35fbdc469639b1fdc09293e05247bcc5d8c31
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714487"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Runbook のエラーをトラブルシューティングする

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Azure Automation Runbook の使用時に発生する認証エラー

### <a name="sign-in-failed"></a>シナリオ: Azure アカウントにサインインできない

#### <a name="issue"></a>問題

`Add-AzureAccount` または `Connect-AzureRmAccount` コマンドレットを使用するときに、次のエラーを受け取ります。
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>原因

このエラーは、資格情報アセット名が無効な場合、または Automation 資格情報アセットのセットアップに使用したユーザー名とパスワードが無効な場合に発生します。

#### <a name="resolution"></a>解決策

次の手順で原因を突き止めます。  

1. Azure に接続するために使用する Automation 資格情報資産名で、 **@** 文字などの特殊文字が使用されていないことを確認します。  
2. ローカル PowerShell ISE エディターで、Azure Automation に保存されているユーザー名とパスワードを使用できることを確認します。 その際、PowerShell ISE で次のコマンドレットを実行します。  

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

### <a name="unable-to-find-subscription"></a>シナリオ: Azure サブスクリプションが見つからない

#### <a name="issue"></a>問題

`Select-AzureSubscription` または `Select-AzureRmSubscription` コマンドレットを使用するときに、次のエラーを受け取ります。

```
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Error

このエラーは、サブスクリプション名が無効な場合、またはサブスクリプションの詳細を取得しようとしている Azure Active Directory ユーザーがサブスクリプションの管理者として構成されていない場合に発生します。

#### <a name="resolution"></a>解決策

Azure で正しく認証され、選択しようとしているサブスクリプションにアクセスできることを次の手順で確認します。  

1. **Select-AzureSubscription** コマンドレットを実行する前に必ず **Add-AzureAccount** を実行してください。  
2. それでもエラー メッセージが表示される場合は、**Add-AzureAccount** コマンドレットの後ろに **Get-AzureSubscription** コマンドレットを追加するというコードの変更を行った後、そのコードを実行します。 Get-AzureSubscription の出力にサブスクリプション詳細が含まれることを確認します。  

   * 出力にサブスクリプション詳細が含まれない場合、サブスクリプションが初期化されていません。  
   * 出力にサブスクリプション詳細が含まれる場合は、 **Select-AzureSubscription** コマンドレットで正しいサブスクリプション名または ID を使用していることを確認します。

### <a name="auth-failed-mfa"></a>シナリオ: 多要素認証が有効になっているために Azure に対する認証が失敗した

#### <a name="issue"></a>問題

Azure のユーザー名とパスワードで Azure に対して認証するときに、次のエラーを受け取ります。

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>原因

Azure アカウントに多要素認証を設定している場合、Azure に対する認証に Azure Active Directory ユーザーを使うことはできません。 代わりに、証明書またはサービス プリンシパルを利用して Azure に対して認証する必要があります。

#### <a name="resolution"></a>解決策

Azure クラシック デプロイ モデルのコマンドレットで証明書を使用する方法については、[証明書を作成し、追加して Azure サービスを管理する](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)方法に関するページを参照してください。 Azure Resource Manager コマンドレットでサービス プリンシパルを使用する方法については、[Azure ポータルでサービス プリンシパルを作成する](../../azure-resource-manager/resource-group-create-service-principal-portal.md)方法に関する記事と [Azure Resource Manager でサービス プリンシパルを認証する](../../azure-resource-manager/resource-group-authenticate-service-principal.md)方法に関する記事を参照してください。

## <a name="common-errors-when-working-with-runbooks"></a>Runbook の使用時に発生する一般的なエラー

### <a name="task-was-cancelled"></a>シナリオ: "タスクが取り消されました" というエラーで Runbook が失敗する

#### <a name="issue"></a>問題

Runbook が、次の例のようなエラーで失敗します。

```
Exception: A task was canceled.
```

#### <a name="cause"></a>原因

以前のバージョンの Azure モジュールを使用すると、このエラーが発生する可能性があります。

#### <a name="resolution"></a>解決策

このエラーは、Azure モジュールを最新のバージョンに更新すると解決できます。

Automation アカウントで、**[モジュール]** をクリックし、**[Azure モジュールの更新]** をクリックします。 この更新には、失敗した Runbook の再実行を完了してから約 15 分かかります。

### <a name="not-recognized-as-cmdlet"></a>シナリオ: 不足しているコマンドレットにより、Runbook が失敗する

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

### <a name="job-attempted-3-times"></a>シナリオ: Runbook ジョブの開始を 3 回試行したが、開始できない

#### <a name="issue"></a>問題

Runbook がエラーで失敗します。

```
The job was tried three times but it failed
```

#### <a name="cause"></a>原因

このエラーは次の理由で発生する可能性があります。

1. メモリの制限。 サンドボックスに割り当てられるメモリの制限については、「[Automation の制限](../../azure-subscription-service-limits.md#automation-limits)」で説明されています。400 MB を超えるメモリを使用すると、ジョブが失敗することがあります。

2. モジュールに互換性がない。 これは、モジュールの依存関係が正しくない場合に発生することがあります。この場合は通常、「Command not found (コマンドが見つかりません)」または「Cannot bind parameter (パラメーターをバインドできません)」というメッセージが Runbook から返されます。

#### <a name="resolution"></a>解決策

次の解決策のいずれでもこの問題は解決されます。

* メモリの制限内で問題を解決するために推奨される方法としては、複数の Runbook 間でワークロードを分割する、メモリ内のデータと同量のデータを処理しない、Runbook からの不要な出力を書き込まない、PowerShell ワークフロー Runbook に書き込むチェックポイントの数を検討する、などがあります。  

* 「[Azure Automation の Azure PowerShell モジュールを更新する方法](../automation-update-azure-modules.md)」の手順に従って Azure モジュールを更新します。  

### <a name="fails-deserialized-object"></a>シナリオ: 逆シリアル化されたオブジェクトであるため、Runbook が失敗する

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

### <a name="quota-exceeded"></a>シナリオ: 割り当てられたクォータを超えているために Runbook ジョブが失敗した

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

### <a name="cmdlet-not-recognized"></a>シナリオ: Runbook の実行時にコマンドレットが認識されない

#### <a name="issue"></a>問題

Runbook ジョブがエラーで失敗します。

```
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>原因

このエラーは、Runbook で使用しているコマンドレットを PowerShell エンジンが見つけられないときに発生します。 この原因としては、コマンドレットが含まれるモジュールがアカウントにない、Runbook 名に名前の競合がある、コマンドレットが別のモジュールにも存在し、Automation が名前を解決できないなどが考えられます。

#### <a name="resolution"></a>解決策

次の解決策のいずれでもこの問題は解決されます。  

* コマンドレット名を正しく入力していることを確認します。  
* Automation アカウントにコマンドレットが存在し、競合がないことを確認します。 コマンドレットの存在を確認するには、Runbook を編集モードで開き、ライブラリで見つけるコマンドレットを検索し、`Get-Command <CommandName>` を実行します。 コマンドレットがアカウントで利用できることと他のコマンドレットや Runbook と名前が競合しないことを確認したら、それをキャンバスに追加し、Runbook に設定されている有効なパラメーターを使用していることを確認します。  
* 名前が競合し、コマンドレットが 2 つの異なるモジュールで利用できる場合、コマンドレットの完全修飾名を利用することで解決できます。 たとえば、**ModuleName\CmdletName** を使用できます。  
* ハイブリッド worker グループでオンプレミスの runbook を実行する場合は、モジュール/コマンドレットがハイブリッド worker をホストしているコンピューターにインストールされていることを確認します。

### <a name="evicted-from-checkpoint"></a>シナリオ: Runbook を長時間実行するといつも次の例外で失敗する: [The job cannot continue running because it was repeatedly evicted from the same checkpoint]\(ジョブは同じチェックポイントから繰り返し削除されたため、実行を継続できません\)

#### <a name="issue"></a>問題

これは Azure Automation 内のプロセスの "フェア シェア" 監視のための設計による動作です。3 時間以上実行している Runbook は自動的に中断されます。 ただし、返されるエラー メッセージでは "次" のオプションは提供されません。

#### <a name="cause"></a>原因

さまざまな理由から Runbook は中断されることがあります。 ほとんどの場合、中断はエラーのために発生します。 たとえば、Runbook のキャッチされない例外、ネットワーク障害、Runbook を実行している Runbook Worker でのクラッシュなどは、すべて Runbook が中断される原因になり、再開時には最後のチェックポイントから開始されます。

#### <a name="resolution"></a>解決策

この問題を回避するための解決策では、ワークフローでのチェックポイントを使用します。 詳細については、「[PowerShell ワークフローについての説明](../automation-powershell-workflow.md#checkpoints)」をご覧ください。 "フェア シェア" およびチェックポイントの詳細については、ブログ記事「[Using Checkpoints in Runbooks](https://azure.microsoft.com/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/)」(Runbook でのチェックポイントの使用) を参照してください。

### <a name="long-running-runbook"></a>シナリオ: 実行時間の長い Runbook が完了しない

#### <a name="issue"></a>問題

これは Azure Automation 内のプロセスの "フェア シェア" 監視のための Azure サンドボックスでの設計による動作です。3 時間以上実行している Runbook は自動的に中断されます。

#### <a name="cause"></a>原因

Runbook が、Azure サンドボックスのフェア シェアによって許可されている 3 時間の制限を超えて実行しました

#### <a name="resolution"></a>解決策

推奨される解決策は、[Hybrid Runbook Worker](../automation-hrw-run-runbooks.md) で Runbook を実行することです。 Hybrid Worker には、Azure サンドボックスのような[フェア シェア](../automation-runbook-execution.md#fair-share)による 3 時間の Runbook 制限はありません。

## <a name="common-errors-when-importing-modules"></a>モジュールのインポート時に発生する一般的なエラー

### <a name="module-fails-to-import"></a>シナリオ: モジュールがインポートに失敗するか、インポート後、コマンドレットを実行できない

#### <a name="issue"></a>問題

モジュールがインポートに失敗するか、成功してもコマンドレットが抽出されません。

#### <a name="cause"></a>原因

モジュールが正常に Azure Automation にインポートできない一般的な理由には次が考えられます。

* 構造が Automation で必要とされる構造と一致しません。
* Automation アカウントにデプロイされていない別のモジュールにモジュールが依存しています。
* モジュールのフォルダーにその依存関係がありません。
* モジュールのアップロードに `New-AzureRmAutomationModule` コマンドレットを使用していますが、完全なストレージ パスを与えていないか、公共でアクセスできる URL でモジュールを読み込んでいません。

#### <a name="resolution"></a>解決策

次の解決策のいずれでもこの問題は解決されます。

* モジュールの形式が次のようになっていることを確認します: ModuleName.Zip **->** ModuleName またはバージョン番号 **->** (ModuleName.psm1、ModuleName.psd1)
* .psd1 ファイルを開き、モジュールに依存関係があるかどうかを確認します。 依存関係がある場合、それらのモジュールを Automation アカウントにアップロードします。
* 参照される .dll がモジュール フォルダーにあることを確認します。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
* さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。