---
title: Azure Automation への Linux Hybrid Runbook Worker のデプロイ
description: この記事では、Azure Automation Hybrid Runbook Worker をインストールして、ローカル データ センターやクラウド環境にある Linux ベースのコンピューター上で Runbook を実行する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: a6cf348142d694a03da24f32793fc72325701931
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835225"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker を展開する

Azure Automation の Hybrid Runbook Worker 機能を使う、ロールをホスティングしているコンピューター上で環境内のリソースに対して Runbook を直接実行して、これらのローカル リソースを管理できます。 Linux Hybrid Runbook Worker は、昇格が必要なコマンドを実行するために昇格させることができる特殊なユーザーとして Runbook を実行します。 Runbook は Azure Automation で格納および管理された後、1 つ以上の指定されたコンピューターに配信されます。

この記事では、Linux マシンに Hybrid Runbook Worker をインストールする方法、worker を削除する方法、および Hybrid Runbook Worker グループを削除する方法について説明します。

## <a name="supported-linux-operating-systems"></a>サポートされている Linux オペレーティング システム

Hybrid Runbook Worker 機能は、次のディストリビューションをサポートします。

* Amazon Linux 2012.09 ～ 2015.09 (x86/x64)
* CentOS Linux 5、6、および 7 (x86/x64)
* Oracle Linux 5、6、および 7 (x86/x64)
* Red Hat Enterprise Linux Server 5、6、および 7 (x86/x64)
* Debian GNU/Linux 6、7、および 8 (x86/x64)
* Ubuntu 12.04 LTS、14.04 LTS、16.04 LTS、および 18.04 (x86/x64)
* SUSE Linux Enterprise Server 11 および 12 (x86/x64)

## <a name="supported-runbook-types"></a>サポートされている Runbook の種類

Linux Hybrid Runbook Worker は、Azure Automation での一連の完全な Runbook の種類をサポートしていません。

Linux Hybrid Worker では、次の Runbook の種類が機能します。

* Python 2
* PowerShell

  > [!NOTE]
  > PowerShell Runbook を使うには、Linux マシンに PowerShell Core がインストールされている必要があります。 インストール方法については、「[Linux への PowerShell Core のインストール](/powershell/scripting/install/installing-powershell-core-on-linux)」をご覧ください。

Linux Hybrid Worker では、次の Runbook の種類は機能しません。

* PowerShell ワークフロー
* グラフィカル
* グラフィカル PowerShell ワークフロー

## <a name="deployment-requirements"></a>デプロイ要件

Linux Hybrid Runbook Worker の最小要件は次のようになります。

* 2 コア
* 4 GB の RAM
* ポート 443 (送信)

### <a name="package-requirements"></a>パッケージの要件

| **必須パッケージ** | **説明** | **最小バージョン**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C ライブラリ| 2.5-12 |
|Openssl| OpenSSL ライブラリ | 1.0 (TLS 1.1 と TLS 1.2 がサポートされます)|
|Curl | cURL Web クライアント | 7.15.5|
|Python-ctypes | Python 2.x が必要 |
|PAM | Pluggable Authentication Module (プラグ可能な認証モジュール)|
| **オプション パッケージ** | **説明** | **最小バージョン**|
| PowerShell Core | PowerShell の Runbook を実行するには、PowerShell をインストールする必要があります。インストール方法については、「[Linux への PowerShell Core のインストール](/powershell/scripting/install/installing-powershell-core-on-linux)」をご覧ください。  | 6.0.0 |

## <a name="install-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker をインストールする

Linux コンピューターに Hybrid Runbook Worker をインストールして構成するには、簡単な手動プロセスに従います。 Hybrid Runbook Worker ソリューションを Azure Log Analytics ワークスペースで有効にする必要があります。その後、一連のコマンドを実行してコンピューターを worker として登録し、グループに追加します。

先に進む前に、Automation アカウントがリンクされている Log Analytics ワークスペースを書き留めておきます。 また、Automation アカウントのプライマリ キーも書き留めます。 Azure portal から、Automation アカウントを選び、ワークスペース ID の**ワークスペース**、およびプライマリ キーの**キー**を選ぶことで、両方を検索できます。 Hybrid Runbook Worker に必要なポートとアドレスについては、「[ネットワークを構成する](automation-hybrid-runbook-worker.md#network-planning)」をご覧ください。

>[!NOTE]
> Linux Hybrid Worker のインストール中は、対応する sudo アクセス許可を持った [nxautomation アカウント](automation-runbook-execution.md#log-analytics-agent-for-linux)が存在していなければなりません。 そのアカウントが存在しない、または適切なアクセス許可がアカウントにない状態でワーカーをインストールしようとすると、インストールは失敗します。

1. 次のいずれかの方法を使って、Azure において Hybrid Runbook Worker ソリューションを有効にします。

   * [Azure Monitor ログのワークスペースへの追加](../log-analytics/log-analytics-add-solutions.md)に関するページの手順を使用して、Hybrid Runbook Worker をサブスクリプションに追加します。
   * 次のコマンドレットを実行します。

        ```azurepowershell-interactive
         Set-AzOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. 次のコマンドを実行して、Log Analytics エージェント for Linux をインストールします。 \<WorkspaceID\> と \<WorkspaceKey\> は、お使いのワークスペースの適切な値に置き換えてください。

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. 次のコマンドを、 *-w*、 *-k*、 *-g*、および *-e* パラメーターの値を変更して実行します。 *-g* パラメーターを、新しい Linux Hybrid Runbook Worker を参加させる Hybrid Runbook Worker グループの名前の値に置き換えます。 この名前が Automation アカウントに存在しない場合は、その名前を持つ新しい Hybrid Runbook Worker グループが作成されます。

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. コマンドが完了すると、Azure portal の [ハイブリッド Worker グループ] ページに、新しいグループとメンバーの数が表示されます。 既存のグループの場合は、メンバーの数がインクリメントされます。 [ハイブリッド worker グループ] ページ上にあるリストからグループを選択し、 **[ハイブリッド worker]** タイルを選択できます。 [ハイブリッド worker] ページで、グループの各メンバーが一覧表示されます。

> [!NOTE]
> Azure VM 用の Linux で Azure Monitor 仮想マシン拡張機能を使用している場合は、`autoUpgradeMinorVersion` を false に設定することをお勧めします。これは、バージョンを自動的にアップグレードすると、Hybrid Runbook Worker で問題が発生する場合があるからです。 拡張機能を手動でアップグレードする方法については、「[Azure CLI でのデプロイ](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)」を参照してください。

## <a name="turn-off-signature-validation"></a>署名の検証をオフにする

既定では、Linux Hybrid Runbook Worker は、署名の検証を必要とします。 worker に対して署名されていない Runbook を実行すると、`Signature validation failed` エラーが表示されます。 署名の検証をオフにするには、次のコマンドを実行します。 2 番目のパラメーターは Log Analytics のワークスペース ID に置き換えます。

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-linux-computer"></a><a name="remove-linux-hybrid-runbook-worker"></a>オンプレミスの Linux コンピューターから Hybrid Runbook Worker を削除する

Hybrid Runbook Worker で `ls /var/opt/microsoft/omsagent` コマンドを使用して、ワークスペース ID を取得できます。 ワークスペース ID を使用して名前が付けられたフォルダーが作成されます。

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> このコードで、コンピューターから Linux 用 Log Analytics エージェントは削除されません。 Hybrid Runbook Worker ロールの機能と構成のみが削除されます。

## <a name="remove-a-hybrid-worker-group"></a>ハイブリッド worker グループを削除する

Linux コンピューターの Hybrid Runbook Worker グループを削除するには、Windows ハイブリッド worker グループの場合と同じ手順を使用します。 詳細については、「[ハイブリッド worker グループを削除する](automation-windows-hrw-install.md#remove-a-hybrid-worker-group)」を参照してください。

## <a name="next-steps"></a>次のステップ

* オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を学習するには、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」をご覧ください。
* Hybrid Runbook Worker のトラブルシューティングを行う方法については、「[Hybrid Runbook Worker のトラブルシューティング - Linux](troubleshoot/hybrid-runbook-worker.md#linux)」を参照してください。
