---
title: Azure Automation の Linux Hybrid Runbook Worker
description: この記事では、Azure Automation Hybrid Runbook Worker をインストールして、ローカル データ センターやクラウド環境にある Linux ベースのコンピューターで Runbook を実行できるようにする方法について説明します。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: e95f5d585fa97a62b709e73b6ed6eacafe69a2b3
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158736"
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker のデプロイ方法

Azure Automation の Hybrid Runbook Worker 機能を使用すると、ロールをホスティングしているコンピューター上で環境内のリソースに対して Runbook を直接実行して、これらのローカル リソースを管理できます。 Linux Hybrid Runbook Worker は、昇格が必要なコマンドを実行するために昇格させることができる特殊なユーザーとして Runbook を実行します。 Runbook は Azure Automation で格納および管理された後、1 つ以上の指定されたコンピューターに配信されます。 この記事では、Linux コンピューターに Hybrid Runbook Worker をインストールする方法について説明します。

## <a name="supported-linux-operating-systems"></a>サポートされている Linux オペレーティング システム

サポートされている Linux ディストリビューションの一覧を次に示します:

* Amazon Linux 2012.09 --> 2015.09 (x86/x64)
* CentOS Linux 5、6、および 7 (x86/x64)
* Oracle Linux 5、6、および 7 (x86/x64)
* Red Hat Enterprise Linux Server 5、6 および 7 (x86/x64)
* Debian GNU/Linux 6、7、および 8 (x86/x64)
* Ubuntu 12.04 LTS、14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 および 12 (x86/x64)

## <a name="installing-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker のインストール

Linux コンピューターに Hybrid Runbook Worker をインストールして構成するには、ロールを手動でインストールおよび構成するための非常に簡単なプロセスに従います。 **Automation Hybrid Worker** ソリューションを Log Analytics ワークスペースで有効にする必要があります。その後、一連のコマンドを実行してコンピューターを worker として登録し、新規または既存のグループに追加します。

Linux Hybrid Runbook Worker の最小要件は次のようになります。

* 少なくとも 2 つのコア
* 少なくとも 4 GB の RAM
* ポート 443 (送信)

### <a name="package-requirements"></a>パッケージの要件

| **必須パッケージ** | **説明** | **最小バージョン**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C ライブラリ| 2.5-12 |
|Openssl| OpenSSL ライブラリ | 0.9.8e または 1.0|
|Curl | cURL Web クライアント | 7.15.5|
|Python-ctypes | |
|PAM | Pluggable Authentication Module (プラグ可能な認証モジュール)|

続行する前に、Automation アカウントがリンクされている Log Analytics ワークスペースだけでなく、Automation アカウントの主キーにも注意する必要があります。 ポータルから、Automation アカウント、ワークスペース ID の**ワークスペース**、および主キーの**キー**を選択することで両方を検索できます。 Hybrid Runbook Worker に必要なポートとアドレスについては、「[Configuring your network](automation-hybrid-runbook-worker.md#network-planning)」をご覧ください。

1. Azure で "Automation Hybrid Worker" ソリューションを有効にします。 これは以下のいずれかの方法で実行できます。

   1. 「[Azure Log Analytics 管理ソリューションをワークスペースに追加する](../log-analytics/log-analytics-add-solutions.md)」の手順を使用して、**Automation Hybrid Worker** ソリューションをサブスクリプションに追加します。
   1. 次のコマンドレットを実行します。

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. 次のコマンドを実行して OMS Agent for Linux をインストールします。\<WorkspaceID\> と \<WorkspaceKey\> の部分は、対象ワークスペースの適切な値に置き換えます。

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. 次のコマンドを、*-w*、*-k*、*-g*、および *-e* パラメーターの値を変更して実行します。 *-g* パラメーターを、新しい Linux Hybrid Runbook Worker を参加させる Hybrid Runbook Worker グループの名前の値に置き換えます。 この名前がまだ Automation アカウントに存在しない場合は、その名前を持つ新しい Hybrid Runbook Worker グループが作成されます。

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. コマンドが完了すると、Azure Portal の [ハイブリッド worker グループ] ページに新しいグループとメンバー数が表示されますが、既存のグループの場合はメンバー数が追加されます。 **[ハイブリッド worker グループ]** ページ上にあるリストからグループを選択し、**[ハイブリッド worker]** タイルを選択できます。 **[ハイブリッド worker]** ページで、グループの各メンバーが一覧表示されます。

## <a name="turning-off-signature-validation"></a>署名の検証をオフにする

既定では、Linux Hybrid Runbook Worker は、署名の検証を必要とします。 ワーカーに対して未署名の Runbook を実行した場合は、"署名の検証が失敗しました" を含むエラーが表示されます。 署名の検証をオフにするには、2 番目のパラメーターを Log Analytics ワークスペース ID に置き換えて、次のコマンドを実行します。

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>サポートされている Runbook の種類

Linux Hybrid Runbook Worker は、Azure オートメーション内にある一連の完全な Runbook の種類をサポートしていません。

Linux Hybrid Worker では、次の Runbook の種類が機能します。

* Python 2
* PowerShell

Linux Hybrid Worker では、次の Runbook の種類は機能しません。

* PowerShell ワークフロー
* グラフィカル
* グラフィカル PowerShell ワークフロー

## <a name="troubleshooting"></a>トラブルシューティング

Linux Hybrid Runbook Worker は、Automation アカウントと通信してワーカーの登録、Runbook ジョブの受信、および状態の報告を行うために OMS エージェント for Linux に依存しています。 ワーカーの登録に失敗した場合に考えられるエラーの原因を次に示します。

### <a name="the-oms-agent-for-linux-is-not-running"></a>OMS エージェント for Linux が実行されていない

OMS エージェント for Linux が実行されていない場合、Linux Hybrid Runbook Worker は Azure Automation と通信できません。 次のコマンドを入力して、このエージェントが実行されていることを確認します: `ps -ef | grep python`。 次のように、**nxautomation** ユーザー アカウントの python プロセスが出力されます。 Update Management または Azure Automation ソリューションが有効でない場合、次のどのプロセスも実行されません。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

次の一覧は、Linux Hybrid Runbook Worker のために開始されるプロセスを示します。 すべて `/var/opt/microsoft/omsagent/state/automationworker/` ディレクトリに位置します。

* **oms.conf** - これはワーカー マネージャー プロセスで、DSC から直接開始されます。

* **worker.conf** - このプロセスは Auto Registered Hybrid (自動登録ハイブリッド) ワーカー プロセスで、ワーカー マネージャーによって開始されます。 このプロセスは Update Management によって使用され、ユーザーに透過的です。 Update Management ソリューションがコンピューター上で有効でない場合、このプロセスは存在しません。

* **diy/worker.conf** - このプロセスは DIY ハイブリッド ワーカー プロセスです。 DIY ハイブリッド ワーカー プロセスは、Hybrid Runbook Worker でユーザーの Runbook を実行するために使用されます。 自動登録ハイブリッド ワーカー プロセスとの違いは、異なる構成を使用するキーの詳細だけです。 Azure Automation ソリューションが有効でない場合、このプロセスは存在せず、DIY Linux Hybrid Worker は登録されません。

OMS エージェント for Linux が実行されていない場合、次のコマンドを実行してサービスを開始します: `sudo /opt/microsoft/omsagent/bin/service_control restart`。

### <a name="the-specified-class-does-not-exist"></a>指定されたクラスが存在しない

「**指定されたクラスが存在しません**」というエラーが `/var/opt/microsoft/omsconfig/omsconfig.log` で表示された場合、OMS エージェント for Linux を更新する必要があります。 OMS Agent を再インストールするには、次のコマンドを実行します:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

Update Management の問題を解決する方法の追加手順については、[Update Management のトラブルシューティング](automation-update-management.md#troubleshooting) ページをご覧ください。

## <a name="next-steps"></a>次の手順

* 「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照して、オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を確認します。
* Hybrid Runbook Worker を削除する方法については、「[Remove Azure Automation Hybrid Runbook Workers (Azure Automation の Hybrid Runbook Worker の削除)](automation-hybrid-runbook-worker.md#removing-hybrid-runbook-worker)」をご覧ください。
