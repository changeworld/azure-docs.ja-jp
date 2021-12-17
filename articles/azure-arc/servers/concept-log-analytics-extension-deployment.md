---
title: Arc 対応サーバーに Log Analytics エージェントをデプロイする
description: この記事では、ローカル データセンターまたは他のクラウド環境の Azure Arc 対応サーバーに登録されている Windows および Linux ベースのマシンに Log Analytics エージェントをデプロイするさまざまな方法について説明します。
ms.date: 10/22/2021
ms.topic: conceptual
ms.openlocfilehash: 5ba1ccc8407903ca40677938b19846a7e6c205a0
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132369457"
---
# <a name="understand-deployment-options-for-the-log-analytics-agent-on-azure-arc-enabled-servers"></a>Azure Arc 対応サーバー上の Log Analytics エージェントでのデプロイ オプションを理解する

Azure Monitor では、Log Analytics エージェントをインストールし、Azure Arc 対応サーバーに登録されているマシンまたはサーバーをサービスに接続するための、複数の方法がサポートされています。 Azure Arc 対応サーバーによってサポートされている Azure VM 拡張機能フレームワークにより、デプロイ後の構成と自動化タスクが提供され、Azure VM の場合と同様にハイブリッド マシンの管理を簡略化できます。

次のことを行う場合は、Log Analytics エージェントが必要です。

* [VM の分析情報](../../azure-monitor/vm/vminsights-overview.md)を使用して、マシンまたはサーバーで実行されているオペレーティング システムとワークロードを監視します。 [Azure Monitor](../../azure-monitor/overview.md) の他の機能を使用して、さらに分析を行ってアラートを生成します。
* [Microsoft Defender for Cloud](../../defender-for-cloud/defender-for-cloud-introduction.md) と [Microsoft Sentinel](../../sentinel/overview.md) を使用して、Azure のセキュリティ監視を実行します。
* [Azure Automation の Update Management](../../automation/update-management/overview.md) を使用して、オペレーティング システムの更新プログラムを管理します。
* [Azure Automation の変更履歴とインベントリ](../../automation/change-tracking/overview.md)を使用して、インベントリを収集し、変更を追跡します。
* [Azure Automation の Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) を使用して、マシン上で直接、および環境内のリソースに対して、Automation Runbook を実行します。

この記事では、環境内の複数の運用物理サーバーまたは仮想マシンでの、Log Analytics エージェント VM 拡張機能のデプロイ方法を確認して、組織に最適な機能を判断するのに役立つようにします。 新しい Azure Monitor エージェントに関心があり、詳細に比較したい場合は、「[Azure Monitor エージェントの概要](../../azure-monitor//agents/agents-overview.md)」記事をご覧ください。  

## <a name="installation-options"></a>インストール オプション

さまざまな方法の 1 つまたは組み合わせを使用して、VM 拡張機能をインストールできます。 このセクションでは、検討できるようにそれぞれについて説明します。

### <a name="using-arc-enabled-servers"></a>Arc 対応サーバーの使用

この方法では、[Azure portal](manage-vm-extensions-portal.md)、[PowerShell](manage-vm-extensions-powershell.md)、[Azure CLI](manage-vm-extensions-cli.md)、または [Azure Resource Manager (ARM) テンプレート](manage-vm-extensions-template.md)を使用して、VM 拡張機能のインストール、管理、削除を行うことができます。

#### <a name="advantages"></a>長所

* テストの目的で役立ちます。
* 管理するマシンが数台の場合に便利です。

#### <a name="disadvantages"></a>短所

* Azure Resource Manager テンプレートを使用する場合は自動化が制限され、それ以外の場合は時間がかかります。
* 1 つの Arc 対応サーバーだけを対象にでき、複数のインスタンスは対象にできません。
* 報告先として指定できるワークスペースは 1 つのみです。 最大 4 つのワークスペースに報告するように Log Analytics Windows エージェント VM 拡張機能を構成するには、PowerShell または Azure CLI を使用する必要があります。
* ポータルからの依存関係エージェントのデプロイはサポートされていません。 PowerShell、Azure CLI、または ARM テンプレートのみを使用できます。

### <a name="using-azure-policy"></a>Azure Policy の使用

Azure Policy を使用して、環境内のマシンに Log Analytics エージェント VM 拡張機能を大規模にデプロイし、構成のコンプライアンスを維持できます。 これを実現するには、"**Azure Arc が有効な Linux サーバー上で Log Analytics 拡張機能を構成する**" または "**Azure Arc が有効な Windows サーバー上で Log Analytics 拡張機能を構成する**" のポリシー定義、または "**VM 用 Azure Monitor を有効にする**" ポリシー イニシアティブを使用します。

Azure Policy には、Azure Monitor に関連するいくつかの定義があらかじめ組み込まれています。 **[監視]** カテゴリの組み込みポリシーの完全な一覧については、「[Azure Monitor 用の Azure Policy 組み込み定義](../../azure-monitor/policy-reference.md)」をご覧ください。

#### <a name="advantages"></a>長所

* VM 拡張機能が削除された場合は、ポリシーの評価後に再インストールされます。
* 新しい Azure Arc 対応サーバーが Azure に登録されると、VM 拡張機能が識別されてインストールされます。
* 報告先として指定できるワークスペースは 1 つのみです。 最大 4 つのワークスペースに報告するように Log Analytics Windows エージェント VM 拡張機能を構成するには、PowerShell または Azure CLI を使用する必要があります。

#### <a name="disadvantages"></a>短所

* Log Analytics VM 拡張機能がインストールされ、指定した Log Analytics ワークスペースに報告するようにエージェントが構成されるのは、"**Azure Arc が有効な <** *オペレーティング システム* **> サーバー上で Log Analytics 拡張機能を構成する**" ポリシーだけです。 VM の分析情報を使用して、オペレーティング システムのパフォーマンスを監視し、実行中のプロセスと依存関係を他のリソースにマップすることに関心がある場合は、ポリシー イニシアティブ "**VM 用 Azure Monitor を有効にする**" を適用する必要があります。 Log Analytics VM 拡張機能と Dependency Agent VM 拡張機能の両方がインストールされて構成されます。これは必須です。
* 標準コンプライアンス評価サイクルは 24 時間に 1 回です。 サブスクリプションまたはリソース グループの評価スキャンは、Azure CLI、Azure PowerShell、または REST API への呼び出しを使用して、または Azure Policy Compliance Scan GitHub アクションを使用して開始できます。 詳しくは、「[評価のトリガー](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)」をご覧ください。

### <a name="using-azure-automation"></a>Azure Automation の使用

Azure Automation でのプロセス自動化動作環境と、PowerShell および Python Runbook のサポートにより、環境内のマシンへの大規模な Log Analytics エージェント VM 拡張機能のデプロイを自動化できます。

#### <a name="advantages"></a>長所

* 使い慣れたスクリプト言語でスクリプト化した方法を使用して、デプロイと構成を自動化できます。
* 実行するスケジュールを定義および制御できます。
* マネージド ID を使用して、Automation アカウントから Arc 対応サーバーに対して安全に認証が行われます。

#### <a name="disadvantages"></a>短所

* Azure Automation アカウントが必要です。
* Azure Automation での Runbook の作成と管理の経験。
* 対象のオペレーティング システムに応じて、PowerShell または Python での Runbook の作成。

## <a name="next-steps"></a>次の手順

* Azure Automation の Update Management を使用してオペレーティング システムの更新プログラムを管理するには、[Automation アカウントからの有効化](../../automation/update-management/enable-from-automation-account.md)に関する記事を確認し、その手順に従ってマシンによるワークスペースへの報告を有効にします。

* Azure Automation の変更履歴とインベントリを使用して変更を追跡するには、[Automation アカウントからの有効化](../../automation/change-tracking/enable-from-automation-account.md)に関する記事を確認し、その手順に従ってマシンによるワークスペースへの報告を有効にします。

* Azure Automation のユーザー Hybrid Runbook Worker 機能を使用すると、Arc 対応サーバーに登録されているサーバーまたはマシンで Runbook を直接実行できます。 [Hybrid Runbook Worker VM 拡張機能のデプロイ](../../automation/extension-based-hybrid-runbook-worker-install.md)に関する記事をご覧ください。

* セキュリティ関連のイベントの収集を Microsoft Sentinel で開始する場合は、「[Microsoft Sentinel にオンボードする](scenario-onboard-azure-sentinel.md)」を参照し、Microsoft Defender for Cloud で収集する場合は「[Microsoft Defender for Cloud にオンボードする](../../security-center/quickstart-onboard-machines.md)」を参照してください。

* マシンのパフォーマンスを確認し、検出されたアプリケーション コンポーネントを表示するには、VM の分析情報の[パフォーマンスの監視](../../azure-monitor/vm/vminsights-performance.md)と[依存関係のマップ](../../azure-monitor/vm/vminsights-maps.md)に関する記事をご覧ください。
