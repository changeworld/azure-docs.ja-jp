---
title: Azure Automation Update Management の展開プラン
description: この記事では、Azure Automation Update Management の展開を準備する際の考慮事項と決定事項について説明します。
services: automation
ms.subservice: update-management
ms.date: 09/28/2021
ms.topic: conceptual
ms.openlocfilehash: fcf7235ae071dece392a11bacbf9f750569380ae
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129715305"
---
# <a name="plan-your-update-management-deployment"></a>Update Management の展開を計画する

## <a name="step-1---automation-account"></a>ステップ 1 - Automation アカウント

Update Management は Azure Automation の機能であるため、Automation アカウントが必要です。 サブスクリプションの既存の Automation アカウントを使用するか、または Update Management 専用で他の Automation 機能には使用できないアカウントを新規に作成することができます。

## <a name="step-2---azure-monitor-logs"></a>ステップ 2 - Azure Monitor ログ

Update Management は、Azure Monitor の Log Analytics ワークスペースを使用して、管理されたコンピューターから収集される評価および更新ステータス ログ データを格納します。 Log Analytics との統合により、Azure Monitor で詳細な分析やアラートも利用できるようになります。 サブスクリプションの既存のワークスペースを使用するか、または Update Management 専用の新しいワークスペースを作成することができます。

Azure Monitor ログと Log Analytics ワークスペースを初めて使用する場合は、<bpt id="p1">[</bpt>Log Analytics ワークスペースの設計<ept id="p1">](../../azure-monitor/logs/design-logs-deployment.md)</ept>に関する展開ガイドを参照してください。 

## <a name="step-3---supported-operating-systems"></a>ステップ 3 - サポートされているオペレーティング システム

Update Management は、Windows Server および Linux オペレーティング システムの特定のバージョンをサポートしています。 Update Management を有効にする前に、対象のマシンが<bpt id="p1">[</bpt>オペレーティング システムの要件<ept id="p1">](operating-system-requirements.md)</ept>を満たしていることを確認してください。 

## <a name="step-4---log-analytics-agent"></a>ステップ 4 - Log Analytics エージェント

Update Management をサポートするには、Windows および Linux 向けの <bpt id="p1">[</bpt>Log Analytics エージェント<ept id="p1">](../../azure-monitor/agents/log-analytics-agent.md)</ept>が必要です。 エージェントは、データ コレクションと Automation システム Hybrid Runbook Worker ロールの両方に使用され、マシン上での評価および更新プログラムの展開の管理に使用される Update Management Runbook をサポートします。 

Azure VM で Log Analytics エージェントがまだインストールされていない場合、VM の Update Management を有効にすると、<bpt id="p1">[</bpt>Windows<ept id="p1">](../../virtual-machines/extensions/oms-windows.md)</ept> または <bpt id="p2">[</bpt>Linux<ept id="p2">](../../virtual-machines/extensions/oms-linux.md)</ept> 向けの Log Analytics VM 拡張機能を使用してエージェントが自動的にインストールされます。 エージェントは、Update Management が有効になっている Automation アカウントにリンクされている Log Analytics ワークスペースに報告するように構成されています。

Azure 以外の VM やサーバーの場合、Windows または Linux 向けの Log Analytics エージェントをインストールして、リンクされたワークスペースに報告させる必要があります。 最初にマシンを <bpt id="p1">[</bpt>Azure Arc 対応サーバー<ept id="p1">](../../azure-arc/servers/overview.md)</ept>に接続し、次に Azure Policy を使用して <bpt id="p2">[</bpt>Linux または Windows Azure Arc マシンに Log Analytics エージェントをデプロイする<ept id="p2">](../../governance/policy/samples/built-in-policies.md#monitoring)</ept>組み込みポリシー定義を割り当てることにより、Windows または Linux 用の Log Analytics エージェントをインストールすることをお勧めします。 また、<bpt id="p1">[</bpt>VM 分析情報<ept id="p1">](../../azure-monitor/vm/vminsights-overview.md)</ept>を使用してマシンの監視を行う場合は、代わりに <bpt id="p2">[</bpt>Azure Monitor for VMs を有効にする<ept id="p2">](../../governance/policy/samples/built-in-initiatives.md#monitoring)</ept>イニシアティブを使用します。

Operations Manager によって現在管理されているマシンを有効にする場合、新しいエージェントは必要ありません。 管理グループを Log Analytics ワークスペースに接続すると、ワークスペースの情報がエージェントの構成に追加されます。

1 つのマシンを複数の Log Analytics ワークスペースに Update Management 用に登録すること (マルチホームとも言われます) は、サポートされていません。

## <a name="step-5---network-planning"></a><bpt id="p1"><a name="ports"></bpt><ept id="p1"></a></ept>ステップ 5 - ネットワークの計画

Update Management をサポートするためにネットワークを準備するには、一部のインフラストラクチャ コンポーネントの構成が必要になることがあります。 たとえば、Update Management と Azure Monitor によって使用される通信が通過できるようにファイアウォール ポートを開きます。

Update Management に必要なポート、URL、およびその他のネットワークの詳細 (Hybrid Runbook Worker ロールなど) については、<bpt id="p1">[</bpt>Azure Automation のネットワーク構成<ept id="p1">](../automation-network-configuration.md)</ept>に関する記事を確認してください。 Azure VM から安全かつプライベートに Automation サービスに接続するには、<bpt id="p1">[</bpt>Azure Private Link の使用<ept id="p1">](../how-to/private-link-security.md)</ept>に関するページを確認してください。 

Windows マシンでは、Windows Update エージェントに必要なすべてのエンドポイントへのトラフィックも許可する必要があります。 必要なエンドポイントの更新された一覧は、「<bpt id="p1">[</bpt>HTTP またはプロキシに関連する問題<ept id="p1">](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)</ept>」で確認できます。 ローカル環境に <bpt id="p1">[</bpt>Windows Server Update Services<ept id="p1">](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)</ept> (WSUS) が展開されている場合は、<bpt id="p2">[</bpt>WSUS キー<ept id="p2">](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)</ept>で指定されているサーバーへのトラフィックも許可する必要があります。

Red Hat Linux マシンで必要なエンドポイントについては、「<bpt id="p1">[</bpt>RHUI コンテンツ配信サーバーの IP アドレス<ept id="p1">](../../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers)</ept>」をご覧ください。 他の Linux ディストリビューションについては、プロバイダーのドキュメントをご覧ください。

インターネットに接続するネットワーク上のマシンが IT セキュリティ ポリシーで許可されていない場合、<bpt id="p1">[</bpt>Log Analytics ゲートウェイ<ept id="p1">](../../azure-monitor/agents/gateway.md)</ept>を設定し、マシンをゲートウェイ経由で Azure Automation および Azure Monitor に接続するように構成できます。

## <a name="step-6---permissions"></a>ステップ 6 - アクセス許可

更新プログラムのデプロイを作成および管理するには、特定のアクセス許可が必要です。 これらのアクセス許可の詳細については、<bpt id="p1">[</bpt>Update Management へのロールベースのアクセス<ept id="p1">](../automation-role-based-access-control.md#update-management-permissions)</ept>に関するページをご覧ください。

## <a name="step-7---windows-update-agent"></a>ステップ 7 - Windows Update エージェント

Azure Automation Update Management では、Windows の更新プログラムのダウンロードとインストールに Windows Update エージェントを使用しています。 マシン上の Windows Update Agent (WUA) が Windows Server Update Services (WSUS) または Microsoft Update に接続するために使用する特定のグループ ポリシー設定があります。 これらのグループ ポリシー設定は、ソフトウェア更新プログラムのコンプライアンスのためにスキャンを実行する場合、およびソフトウェア更新プログラムを自動的に更新する場合にも使用されます。 推奨事項を確認するには、「<bpt id="p1">[</bpt>Update Management 用に Windows Update の設定を構成する<ept id="p1">](configure-wuagent.md)</ept>」を参照してください。

## <a name="step-8---linux-repository"></a>ステップ 8 - Linux リポジトリ

Azure Marketplace から入手できるオンデマンドの Red Hat Enterprise Linux (RHEL) イメージから作成した VM は、Azure にデプロイされた Red Hat Update Infrastructure (RHUI) にアクセスするよう登録されています。 その他の Linux ディストリビューションは、そのディストリビューションでサポートされている方法を使用して、ディストリビューションのオンライン ファイル リポジトリから更新する必要があります。

Red Hat Enterprise バージョン 6 の更新プログラムを分類するには、yum-security プラグインをインストールする必要があります。 Red Hat Enterprise Linux 7 では、プラグインは既に yum 自体の一部であるため、何もインストールする必要はありません。 詳細については、次の Red Hat の[ナレッジ記事](https://access.redhat.com/solutions/10021)を参照してください。

## <a name="step-9---plan-deployment-targets"></a>ステップ 9 - 展開ターゲットを計画する

Update Management では、Azure または Azure 以外のマシンを表す動的グループを更新プログラムのターゲットに指定できます。これにより、特定のマシンが常に最適な時点で適切な更新プログラムを取得できるようになります。 動的グループは展開時に解決され、次の条件に基づいています。

* サブスクリプション
* リソース グループ
* 場所
* Tags 

Azure 以外のマシンでは、動的グループは保存された検索 (<bpt id="p1">[</bpt>コンピューター グループ<ept id="p1">](../../azure-monitor/logs/computer-groups.md)</ept>とも呼ばれます) を使用します。 マシンのグループを対象とする更新プログラムの展開は、特定の Azure VM ではなく、Update Management の <bpt id="p1">**</bpt>[展開スケジュール]<ept id="p1">**</ept> オプションで Automation アカウントに対してのみ表示されます。

あるいは、選択されている Azure VM についてのみ更新プログラムを管理できます。 特定のマシンを対象とする更新プログラムの展開は、Update Management の <bpt id="p1">**</bpt>[展開スケジュール]<ept id="p1">**</ept> オプションで、マシンと Automation アカウントの両方に対して表示されます。 

## <a name="next-steps"></a>次のステップ

次のいずれかの方法を使用して、Update Management を有効にし、管理するマシンを選択します。

- Azure <bpt id="p1">[</bpt>Resource Manager テンプレート<ept id="p1">](enable-from-template.md)</ept>を使用して、お使いのサブスクリプション内の新しいまたは既存の Automation アカウントと Azure Monitor Log Analytics ワークスペースに Update Management をデプロイする。 管理する必要があるマシンのスコープは構成されません。これは、テンプレートを使用した後の個別の手順として実行されます。

- 1 台以上の Azure マシンと Azure 以外のマシン (Azure Arc 対応サーバーを含む) に対しては、お使いの [Automation アカウント](enable-from-automation-account.md)から。

- <bpt id="p1">**</bpt>Enable-AutomationSolution<ept id="p1">**</ept> <bpt id="p2">[</bpt>Runbook<ept id="p2">](enable-from-runbook.md)</ept> を使用した Azure VM のオンボードの自動化。

- <bpt id="p1">[</bpt>選択した Azure VM<ept id="p1">](enable-from-vm.md)</ept> に対しては、Azure portal の <bpt id="p2">**</bpt>[仮想マシン]<ept id="p2">**</ept> ページから。 このシナリオは、Linux VM 用と Windows VM 用があります。

- <bpt id="p1">[</bpt>複数の Azure VM<ept id="p1">](enable-from-portal.md)</ept> に対しては、Azure portal の <bpt id="p2">**</bpt>[仮想マシン]<ept id="p2">**</ept> ページからそれらを選択することで。
