---
title: トラブルシューティング - Azure Automation Hybrid Runbook Worker
description: この記事では、Azure Automation Hybrid Runbook Worker のトラブルシューティングについて説明します。
services: automation
ms.service: automation
ms.component: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a95c9f1edd6983c915316f2900885a8131245860
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437835"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Hybrid Runbook Worker のトラブルシューティング

この記事では、Hybrid Runbook Worker での問題のトラブルシューティングについて説明します。

## <a name="general"></a>全般

Hybrid Runbook Worker は、Automation アカウントと通信してワーカーの登録、Runbook ジョブの受信、および状態の報告を行うためにエージェントに依存しています。 Windows では、このエージェントは Microsoft Monitoring Agent です。 Linux では OMS エージェント for Linux です。

### <a name="runbook-execution-fails"></a>シナリオ:Runbook の実行が失敗する

#### <a name="issue"></a>問題

Runbook の実行が失敗し、次のエラーを受け取ります。

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Runbook は、3 回実行を試みると短時間中断されます。 Runbook の正常な完了を中断する可能性がある条件があり、関連するエラー メッセージに理由を示す追加情報は含まれません。

#### <a name="cause"></a>原因

考えられる原因は次のとおりです。

* Runbook がローカル リソースで認証できない

* Hybrid Worker がプロキシまたはファイアウォールの内側にある

* Runbook がローカル リソースで認証できない

* Hybrid Runbook Worker の機能を実行するように指定されているコンピューターがハードウェアの最小要件を満たしている

#### <a name="resolution"></a>解決策

コンピューターがポート 443 で *.azure-automation.net に発信アクセスできることを確認します。

Hybrid Runbook Worker を実行するコンピューターは、この機能をホストするよう指定する前に、ハードウェアの最小要件を満たしている必要があります。 満たしていない場合、他のバックグラウンド プロセスのリソース使用状況や Runbook 実行中の競合によっては、コンピューターが過負荷になり Runbook ジョブが遅延またはタイムアウトする可能性があります。

Hybrid Runbook Worker の機能を実行するように指定されているコンピューターがハードウェアの最小要件を満たしていることを確認します。 満たしている場合は、CPU とメモリの使用状況を監視して、Hybrid Runbook Worker プロセスのパフォーマンスと Windows の間の相関関係を調べます。 メモリまたは CPU に負荷がかかる場合は、リソースのボトルネックを解消してエラーを解決するには、アップグレード、プロセッサの追加、またはメモリの増設が必要である可能性があります。 または、最小要件を満たす異なるコンピューティング リソースを選択し、ワークロードがさらに多くのリソースを必要とすることを示したときは拡張します。

**Microsoft-SMA** のイベント ログで " *Win32 Process Exited with code [4294967295]*" という説明の対応するイベントを確認します。 このエラーの原因は、Runbook で認証が構成されていないか、または Hybrid Worker グループの Run As 資格情報が指定されていません。 「[Runbook のアクセス許可](../automation-hrw-run-runbooks.md#runbook-permissions) 」を参照して、Runbook の認証を正しく構成してあることを確認します。

## <a name="linux"></a> Linux

Linux Hybrid Runbook Worker は、Automation アカウントと通信してワーカーの登録、Runbook ジョブの受信、および状態の報告を行うために OMS エージェント for Linux に依存しています。 ワーカーの登録に失敗した場合に考えられるエラーの原因を次に示します。

### <a name="oms-agent-not-running"></a>シナリオ:OMS エージェント for Linux が実行されていない

OMS エージェント for Linux が実行されていない場合、Linux Hybrid Runbook Worker は Azure Automation と通信できません。 次のコマンドを入力して、このエージェントが実行されていることを確認します: `ps -ef | grep python`。 次のように、**nxautomation** ユーザー アカウントの python プロセスが出力されます。 Update Management または Azure Automation ソリューションが有効でない場合、次のどのプロセスも実行されません。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

次の一覧は、Linux Hybrid Runbook Worker のために開始されるプロセスを示します。 これらはすべて `/var/opt/microsoft/omsagent/state/automationworker/` ディレクトリにあります。

* **oms.conf** - このプロセスはワーカー マネージャー プロセスで、DSC から直接開始されます。

* **worker.conf** - このプロセスは Auto Registered Hybrid (自動登録ハイブリッド) ワーカー プロセスで、ワーカー マネージャーによって開始されます。 このプロセスは Update Management によって使用され、ユーザーに透過的です。 Update Management ソリューションがコンピューター上で有効でない場合、このプロセスは存在しません。

* **diy/worker.conf** - このプロセスは DIY ハイブリッド ワーカー プロセスです。 DIY ハイブリッド ワーカー プロセスは、Hybrid Runbook Worker でユーザーの Runbook を実行するために使用されます。 自動登録ハイブリッド ワーカー プロセスとの違いは、異なる構成を使用するキーの詳細だけです。 Azure Automation ソリューションが有効でない場合、このプロセスは存在せず、DIY Linux ハイブリッド worker は登録されません。

OMS エージェント for Linux が実行されていない場合、次のコマンドを実行してサービスを開始します。`sudo /opt/microsoft/omsagent/bin/service_control restart`

### <a name="class-does-not-exist"></a>シナリオ:指定されたクラスが存在しない

次のようなエラーが表示された場合:**指定されたクラスが存在しません..** `/var/opt/microsoft/omsconfig/omsconfig.log` で表示された場合、OMS エージェント for Linux を更新する必要があります。 OMS Agent を再インストールするには、次のコマンドを実行します。

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a> Windows

Windows Hybrid Runbook Worker は、Automation アカウントと通信して worker の登録、Runbook ジョブの受信、および状態の報告を行うために Microsoft Monitoring Agent に依存しています。 ワーカーの登録に失敗した場合に考えられるエラーの原因を次に示します。

### <a name="mma-not-running"></a>シナリオ:Microsoft Monitoring Agent が動作していない

#### <a name="issue"></a>問題

`healthservice` サービスが Hybrid Runbook Worker マシンで実行されていません。

#### <a name="cause"></a>原因

Microsoft Monitoring Agent の Windows サービスが実行されていない場合、このシナリオにより Hybrid Runbook Worker は Azure Automation と通信できません。

#### <a name="resolution"></a>解決策

PowerShell で次のコマンドを入力して、このエージェントが実行されていることを確認します: `Get-Service healthservice`。 サービスが停止している場合は、PowerShell で次のコマンドを入力してサービスを開始します: `Start-Service healthservice`。

### <a name="event-4502"></a>Operations Manager ログのイベント 4502

#### <a name="issue"></a>問題

**アプリケーションとサービス ログ\Operations Manager** のイベント ログで、イベント 4502 と、**Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** および次の説明を含む EventMessage が表示されます:*サービス \<wsid\>.oms.opinsights.azure.com によって提示された証明書は、Microsoft サービスで使用する証明機関によって発行されたものではありません。TLS/SSL 通信を遮断するプロキシが実行されているかどうかをネットワーク管理者に問い合わせてください。記事 KB3126513 に、接続の問題に関するトラブルシューティング情報が記載されています。*"

#### <a name="cause"></a>原因

この問題は、プロキシまたはネットワークのファイアウォールが Microsoft Azure への通信をブロックしていることが原因で発生する可能性があります。 コンピューターがポート 443 で *.azure-automation.net に発信アクセスできることを確認します。

#### <a name="resolution"></a>解決策

ログは、各ハイブリッド worker の C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes にローカルに格納されます。 Azure Automation へのロールのオンボードに影響する接続などの問題や、通常の動作中に発生した問題を示唆する警告やエラー イベントが、**アプリケーションとサービス ログ\Microsoft-SMA\Operations** と**アプリケーションとサービス ログ\Operations Manager** イベント ログにあるかどうかをチェックできます。

[Runbook の出力とメッセージ](../automation-runbook-output-and-messages.md) は、クラウドで実行される Runbook ジョブと同様に、Hybrid Worker から Azure Automation に送られます。 他の Runbook と同じ方法で、Verbose および Progress ストリームも有効にできます。

### <a name="corrupt-cache"></a> Hybrid Runbook Worker の報告がない

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

#### <a name="resolution"></a>解決策

この問題を解決するには、Hybrid Runbook Worker にサインインし、次のスクリプトを実行します。 このスクリプトは Microsoft Monitoring Agent を停止し、そのキャッシュを削除し、サービスを再起動します。 この操作により、Hybrid Runbook Worker の構成が Azure Automation から強制的に再ダウンロードされます。

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
* さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
