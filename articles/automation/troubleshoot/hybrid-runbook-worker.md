---
title: トラブルシューティング - Azure Automation Hybrid Runbook Worker
description: この記事では、Azure Automation Hybrid Runbook Worker をトラブルシューティングするための情報について説明します。
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d2587af0ada18b5c4271e7411783fe60211a3479
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637857"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Hybrid Runbook Worker のトラブルシューティング

この記事では、Hybrid Runbook Worker での問題のトラブルシューティングについて説明します。

## <a name="general"></a>全般

Hybrid Runbook Worker は、Automation アカウントと通信してワーカーの登録、Runbook ジョブの受信、および状態の報告を行うためにエージェントに依存しています。 Windows では、このエージェントは Microsoft Monitoring Agent (MMA) とも呼ばれる Windows 用 Log Analytics エージェントです。 Linux の場合は、Linux 用 Log Analytics エージェントです。

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>シナリオ:Runbook の実行が失敗する

#### <a name="issue"></a>問題

Runbook の実行が失敗し、次のエラーを受け取ります。

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Runbook は 3 回実行を試みると、短時間中断されます。 Runbook の完了を妨げる可能性がある状況が存在します。 関連するエラー メッセージに追加情報が含まれていない場合があります。

#### <a name="cause"></a>原因

以下の原因が考えられます。

* Runbook がローカル リソースで認証できない。

* Hybrid Worker がプロキシまたはファイアウォールの内側にある。

* Hybrid Runbook Worker の機能を実行するように構成されているコンピューターが、ハードウェアの最小要件を満たしていない。

#### <a name="resolution"></a>解像度

コンピューターがポート 443 で * **.azure-automation.net**に発信アクセスできることを確認します。

Hybrid Runbook Worker を実行するコンピューターは、この機能をホストするようにワーカーを構成する前に、ハードウェアの最小要件を満たしている必要があります。 Runbook とそれらが使用するバック グラウンド プロセスによってシステムが過剰に使用され、それが原因で Runbook ジョブの遅延またはタイムアウトが発生する可能性があります。

Hybrid Runbook Worker の機能を実行するコンピューターがハードウェアの最小要件を満たしていることを確認します。 満たしている場合は、CPU とメモリの使用を監視して、Hybrid Runbook Worker プロセスのパフォーマンスと Windows の間の相関関係を調べます。 メモリまたは CPU の負荷は、リソースのアップグレードが必要であることを示している可能性があります。 最小要件を満たす別のコンピューティング リソースを選択し、ワークロードの需要によって増加が必要であることが示された時点でスケーリングすることもできます。

**Microsoft-SMA** のイベント ログで、`Win32 Process Exited with code [4294967295]` という説明の対応するイベントを確認します。 このエラーの原因は、Runbook で認証が構成されていないか、または Hybrid Runbook Worker グループの実行資格情報が指定されていないことです。 Runbook の認証が正しく構成されていることを確認するには、「[Hybrid Runbook Worker での Runbook の実行](../automation-hrw-run-runbooks.md)」で Runbook のアクセス許可を確認してください。

### <a name="scenario-event-15011-in-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>シナリオ:Hybrid Runbook Worker のイベント 15011

#### <a name="issue"></a>問題

Hybrid Runbook Worker では、クエリ結果が無効であることを示すイベント 15011 が受信されます。 ワーカーで [SignalR サーバー](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1)との接続を開こうとするときに、次のエラーが表示されます。

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>原因

Hybrid Runbook Worker が自動デプロイ ソリューション用に正しく構成されていません。 このソリューションには、VM を Log Analytics ワークスペースに接続するパーツが含まれています。 PowerShell スクリプトにより、指定された名前のサブスクリプション内でワークスペースが検索されます。 このケースでは、Log Analytics ワークスペースは別のサブスクリプションにあります。 スクリプトでワークスペースが見つからず、作成が試みられますが、その名前は既に使用されています。 そのため、デプロイが失敗します。

#### <a name="resolution"></a>解像度

この問題を解決するには、2 つの方法があります。

* 別のサブスクリプション内で Log Analytics ワークスペースを検索するよう、PowerShell スクリプトを変更します。 これは、今後多くの Hybrid Runbook Worker マシンをデプロイする予定の場合に適したソリューションです。

* Orchestrator サンドボックスで実行するようにワーカー マシンを手動で構成します。 次に、Azure Automation アカウントで作成された Runbook をワーカーで実行して、機能をテストします。

### <a name="scenario-windows-azure-vms-automatically-dropped-from-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>シナリオ:ハイブリッド worker グループから Windows Azure VM が自動的に削除される

#### <a name="issue"></a>問題

ワーカー マシンが長時間オフになっている場合、Hybrid Runbook Worker または VM が表示されません。

#### <a name="cause"></a>原因

Hybrid Runbook Worker マシンで、Azure Automation に対する ping が 30 日以上行われていません。 その結果、Automation によって Hybrid Runbook Worker グループまたはシステム ワーカー グループが削除されました。 

#### <a name="resolution"></a>解像度

ワーカー マシンを起動し、Azure Automation に登録します。 Runbook 環境をインストールし Azure Automation に接続する手順については、「[Windows Hybrid Runbook Worker をデプロイする](../automation-windows-hrw-install.md)」を参照してください。

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-hybrid-runbook-worker"></a><a name="no-cert-found"></a>シナリオ:Hybrid Runbook Worker の証明書ストア内に証明書が見つからない

#### <a name="issue"></a>問題

Hybrid Runbook Worker で実行される Runbook が次のエラー メッセージで失敗します。

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```
#### <a name="cause"></a>原因

このエラーは、Hybrid Runbook Worker 上で実行される Runbook 内で[実行アカウント](../manage-runas-account.md)を使用しようとしたが、その実行アカウントの証明書が存在しない場合に発生します。 既定では、Hybrid Runbook Worker には、実行アカウントを正常に動作させるために必要なローカルの証明書資産はありません。

#### <a name="resolution"></a>解像度

Hybrid Runbook Worker が Azure VM の場合は、[Azure リソース用のマネージド ID](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) を代わりに使用できます。 このシナリオでは、実行アカウントの代わりに Azure VM のマネージド ID を使用して Azure リソースへの認証を可能にすることで、認証を簡素化します。 Hybrid Runbook Worker がオンプレミスのコンピューターである場合は、実行アカウント証明書をコンピューターにインストールする必要があります。 証明書をインストールする方法については、「[Hybrid Runbook Worker での Runbook の実行](../automation-hrw-run-runbooks.md)」で PowerShell Runbook Export-RunAsCertificateToHybridWorker を実行するための手順を参照してください。

### <a name="scenario-error-403-during-registration-of-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>シナリオ:Hybrid Runbook Worker の登録中にエラー 403 が発生した

#### <a name="issue"></a>問題

ワーカーの初期登録フェーズが失敗し、次のエラー (403) が表示されます。

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>原因

以下の原因が考えられます。

* エージェントの設定で、ワークスペース ID かワークスペース キー (プライマリ) に入力ミスがあります。 

* Hybrid Runbook Worker で構成をダウンロードできず、アカウント リンク エラーが発生します。 Azure では、ソリューションを有効にした場合、Log Analytics ワークスペースと Automation アカウントをリンクするために特定のリージョンのみがサポートされます。 また、コンピューターで誤った日付や時刻が設定されている可能性もあります。 時刻が現在の時刻より 15 分後または前である場合、オンボードは失敗します。

#### <a name="resolution"></a>解像度

##### <a name="mistyped-workspace-idkey"></a>ワークスペース ID/キーの入力ミス
エージェントのワークスペース ID またはワークスペース キーに入力ミスがあるかどうかを確認するには、Windows エージェントの場合は[Windows エージェントでのワークスペースの追加または削除](../../azure-monitor/platform/agent-manage.md#windows-agent)に関する記事を、Linux エージェントの場合は[Linux エージェントでのワークスペースの追加または削除](../../azure-monitor/platform/agent-manage.md#linux-agent)に関する記事を参照してください。  Azure portal から完全な文字列を選択し、コピーと貼り付けを慎重に行う必要があります。

##### <a name="configuration-not-downloaded"></a>構成がダウンロードされていない

Log Analytics ワークスペースと Automation アカウントは、リンクされたリージョン内にある必要があります。 サポートされているリージョンの一覧については、[Azure Automation と Log Analytics ワークスペースのマッピング](../how-to/region-mappings.md)に関する記事を参照してください。

コンピューターの日付またはタイム ゾーン、あるいはその両方の更新が必要な場合もあります。 カスタムの時間範囲を選択する場合は、その範囲が UTC であることを確認してください。これは、ローカル タイムゾーンとは異なる場合があります。

## <a name="linux"></a>Linux

Linux Hybrid Runbook Worker は、[Linux 用 Log Analytics エージェント](../../azure-monitor/platform/log-analytics-agent.md) を使用することにより、Automation アカウントと通信してワーカーの登録、Runbook ジョブの受信、状態の報告を行います。 ワーカーの登録に失敗した場合に考えられるエラーの原因を次に示します。

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>シナリオ:Linux 用 Log Analytics エージェントが実行されていない

#### <a name="issue"></a>問題

Linux 用 Log Analytics エージェントが実行されていない

#### <a name="cause"></a>原因

エージェントが実行されていない場合、Linux Hybrid Runbook Worker は Azure Automation と通信できません。 さまざまな理由で、エージェントが実行されていない可能性があります。

#### <a name="resolution"></a>解像度

 `ps -ef | grep python` コマンドを入力して、このエージェントが実行されていることを確認します。 次のように、**nxautomation** ユーザー アカウントの Python プロセスが出力されます。 Update Management または Azure Automation ソリューションが有効でない場合、次のどのプロセスも実行されていません。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

次の一覧は、Linux Hybrid Runbook Worker のために開始されるプロセスを示します。 これらはすべて **/var/opt/microsoft/omsagent/state/automationworker/** ディレクトリに配置されています。

* **oms.conf** - worker マネージャー プロセスです。 これは、DSC から直接開始されます。

* **worker.conf** - 自動登録ハイブリッド worker プロセスです。 worker マネージャーによって開始されます。 このプロセスは Update Management によって使用され、ユーザーに透過的です。 Update Management ソリューションがコンピューター上で有効でない場合、このプロセスは存在しません。

* **diy/worker.conf** - DIY ハイブリッド worker プロセスです。 DIY ハイブリッド ワーカー プロセスは、Hybrid Runbook Worker でユーザーの Runbook を実行するために使用されます。 キーの詳細での自動登録ハイブリッド worker プロセスとの違いは、異なる構成を使用していることだけです。 Azure Automation ソリューションが無効であり、DIY Linux ハイブリッド worker が登録されていない場合、このプロセスは存在しません。

エージェントが実行されていない場合、次のコマンドを実行してサービスを開始します。`sudo /opt/microsoft/omsagent/bin/service_control restart`

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>シナリオ:指定したクラスが存在しない

**/var/opt/microsoft/omsconfig/omsconfig.log** にエラー `The specified class does not exist..` が表示された場合、Linux 用 Log Analytics エージェントを更新する必要があります。 エージェントを再インストールするには、次のコマンドを実行します。

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows Hybrid Runbook Worker は、[Windows 用 Log Analytics エージェント](../../azure-monitor/platform/log-analytics-agent.md) を使用することにより、Automation アカウントと通信してワーカーの登録、Runbook ジョブの受信、状態の報告を行います。 このセクションには、ワーカーの登録に失敗した場合の考えられる理由をいくつか示しています。

### <a name="scenario-the-microsoft-monitoring-agent-isnt-running"></a><a name="mma-not-running"></a>シナリオ:Microsoft Monitoring Agent が実行されていません。

#### <a name="issue"></a>問題

`healthservice` サービスが Hybrid Runbook Worker マシンで実行されていません。

#### <a name="cause"></a>原因

Microsoft Monitoring Agent サービスが実行されていない場合、Hybrid Runbook Worker は Azure Automation と通信できません。

#### <a name="resolution"></a>解像度

PowerShell で次のコマンドを入力して、このエージェントが実行されていることを確認します: `Get-Service healthservice`。 サービスが停止している場合は、PowerShell で次のコマンドを入力してサービスを開始します: `Start-Service healthservice`。

### <a name="scenario-event-4502-in-operations-manager-log"></a><a name="event-4502"></a>シナリオ:Operations Manager ログのイベント 4502

#### <a name="issue"></a>問題

**アプリケーションとサービス ログ\Operations Manager** のイベント ログで、イベント 4502 と、`Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` および次の説明を含む EventMessage が表示されます。<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>原因

この問題は、プロキシまたはネットワークのファイアウォールが Microsoft Azure への通信をブロックしていることが原因で発生する可能性があります。 コンピューターがポート 443 で * **.azure-automation.net**に発信アクセスできることを確認します。 

#### <a name="resolution"></a>解像度

ログは、各ハイブリッド worker の **C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes** にローカルに格納されます。 警告やエラー イベントが、**アプリケーションとサービス ログ\Microsoft-SMA\Operations** と**アプリケーションとサービス ログ\Operations Manager** イベント ログにあるかどうかを確認できます。 これらのログは、Azure Automation へのロールのオンボードに影響する接続や他の種類の問題、または通常の操作中に発生した問題を示すものです。 Log Analytics エージェントに関する問題のトラブルシューティングの詳細については、[Log Analytics Windows エージェントの問題のトラブルシューティング](../../azure-monitor/platform/agent-windows-troubleshoot.md)に関するページを参照してください。

ハイブリッド worker により、クラウドで実行されている Runbook ジョブが出力とメッセージを送信するのと同じ方法で、[Runbook の出力とメッセージ](../automation-runbook-output-and-messages.md)が Azure Automation に送信されます。 詳細ストリームと進行状況ストリームは、Runbook の場合と同じように有効にすることができます。

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>シナリオ:Orchestrator.Sandbox.exe をプロキシ経由で Office 365 に接続できない

#### <a name="issue"></a>問題

Windows Hybrid Runbook Worker で実行されるスクリプトを、想定どおりに Orchestrator サンドボックスの Office 365 に接続できません。 スクリプトでは、接続に [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) を使用しています。 

**Orchestrator.Sandbox.exe.config** を調整してプロキシとバイパス リストを設定しても、サンドボックスに正常に接続されません。 同じプロキシとバイパス リストが設定された **Powershell_ise.exe.config** ファイルは想定どおりに動作するようです。 Service Management Automation (SMA) ログと PowerShell ログでは、プロキシに関する情報が提供されません。

#### <a name="cause"></a>原因

サーバー上の Active Directory フェデレーション サービス (ADFS) への接続でプロキシをバイパスすることはできません。 PowerShell サンドボックスはログインしたユーザーとして実行されることに注意してください。 ただし、Orchestrator サンドボックスは大幅にカスタマイズされており、**Orchestrator.Sandbox.exe.config** ファイルの設定は無視されることがあります。 それにはコンピューターまたは MMA のプロキシ設定を処理するための特殊なコードが含まれていますが、そのコードで他のカスタム プロキシ設定が処理されることはありません。 

#### <a name="resolution"></a>解像度

PowerShell コマンドレットのスクリプトを MSOnline モジュールではなく Azure AD モジュールを使用するように移行することで、Orchestrator サンドボックスの問題を解決できます。 「[Orchestrator から Azure Automation (ベータ版) へ移行する](https://docs.microsoft.com/azure/automation/automation-orchestrator-migration)」を参照してください。

引き続き MSOnline モジュールのコマンドレットを使用する場合は、[Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7) を使用するようにスクリプトを変更します。 `ComputerName` パラメーターと `Credential` パラメーターの値を指定します。 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

このコード変更によって、指定された資格情報のコンテキストで、まったく新しい PowerShell セッションが開始されます。 これで、アクティブ ユーザーを認証するプロキシ サーバー経由でトラフィックを送信できます。

>[!NOTE]
>このソリューションを使用すると、サンドボックス構成ファイルを操作する必要がなくなります。 スクリプトを使用して構成ファイルを正常に動作させることができた場合でも、Hybrid Runbook Worker エージェントが更新されるたびにそのファイルは消去されます。

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>シナリオ:Hybrid Runbook Worker の報告がない

#### <a name="issue"></a>問題

Hybrid Runbook Worker マシンは動作していますが、ワークスペース内のマシンのハートビート データが表示されません。

次のクエリの例は、ワークスペース内のマシンとそれらの最後のハートビートを示しています:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>原因

この問題は、Hybrid Runbook Worker 上のキャッシュの破損が原因である可能性があります。

#### <a name="resolution"></a>解像度

この問題を解決するには、Hybrid Runbook Worker にサインインし、次のスクリプトを実行します。 このスクリプトは Microsoft Monitoring Agent を停止し、そのキャッシュを削除し、サービスを再起動します。 この操作により、Hybrid Runbook Worker の構成が Azure Automation から強制的に再ダウンロードされます。

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>シナリオ:Hybrid Runbook Worker を追加できない

#### <a name="issue"></a>問題

`Add-HybridRunbookWorker` コマンドレットを使用して Hybrid Runbook Worker を追加しようとすると、次のメッセージが表示されます。

```error
Machine is already registered
```

#### <a name="cause"></a>原因

この問題は、マシンが既に別の Automation アカウントに登録されている場合や、マシンから Hybrid Runbook Worker を削除した後にそれを再度追加しようとした場合に発生する可能性があります。

#### <a name="resolution"></a>解像度

この問題を解決するには、次のレジストリ キーを削除し、`HealthService` を再起動して、もう一度 `Add-HybridRunbookWorker` コマンドレットを試してください。

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
* さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。