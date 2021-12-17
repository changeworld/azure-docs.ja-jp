---
title: Azure Automation の概要
description: この記事では、Azure Automation の概要、および Azure Automation を使用してインフラストラクチャとアプリケーションのライフサイクルを自動化する方法を説明します。
services: automation
keywords: Azure Automation, DSC, PowerShell, State Configuration, Update Management, Change Tracking, DSC, インベントリ, Runbook, Python, グラフィカル
ms.date: 10/25/2021
ms.topic: overview
ms.openlocfilehash: c3b08c17845bec53d55c45bdd405aac9081a4a76
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714297"
---
# <a name="what-is-azure-automation"></a>Azure Automation とは

クラウド運用には、次の 3 つの広範な領域で自動化が必要です。

* デプロイと管理 - 反復可能で一貫性のあるインフラストラクチャとしてのコードを提供します。
* 応答 - 問題を診断して解決するための、イベントベースの自動化を作成します。
* 調整 - 他の Azure サービスおよび製品やサード パーティのサービスおよび製品との間で調整を行い、統合します。

Azure Automation は、Azure 環境と非 Azure 環境の全体で一貫性ある管理をサポートするクラウドベースの自動化、オペレーティング システムの更新、および構成サービスを提供します。 プロセスの自動化、構成管理、更新管理、共有機能、および異種環境機能が含まれます。

:::image type="content" source="./media/overview/automation-overview.png" alt-text="Automation の機能の図" border="true":::

上記の要件を実現する Azure サービスは複数あります。各サービスには一連の機能が含まれており、クラウド ソリューションを構築するためのプログラミング可能なプラットフォームとしての役割を果たします。 たとえば、Azure Bicep と Resource Manager には、Azure リソースのデプロイに使用する反復可能で一貫性のあるテンプレートを開発するための言語が用意されています。 Azure Automation はそのテンプレートを処理することで Azure リソースをデプロイし、デプロイ後の一連の構成タスクを処理できます。

Automation を使用することで、エンタープライズのワークロードとリソースのデプロイ、運用、使用停止を完全に制御できます。

## <a name="process-automation"></a>プロセスの自動化

頻繁に発生し、時間がかかり、エラーが発生しやすい管理タスクは、Azure Automation のプロセス オートメーションで自動化することができます。 このサービスを利用して、ビジネスの価値を高める作業に集中できます。 また、エラーを減らして効率性を向上できるため、運用コストの削減にも役立ちます。 プロセス オートメーションの運用環境について詳しくは、「[Azure Automation での Runbook の実行](automation-runbook-execution.md)」を参照してください。

プロセス オートメーションでは、エンド ツー エンドのプロセスのデプロイ、構成、管理に必要なサード パーティ システムと Azure サービスを統合することができます。 このサービスを使用すると、グラフィック、PowerShell、または Python の [Runbook](automation-runbook-types.md) を作成できます。 Windows または Linux マシン上で直接 Runbook を実行したり、オンプレミスまたは他のクラウド環境のリソースに対して Runbook を実行してそれらのローカル リソースを管理するには、[Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) をマシンにデプロイします。

要求は [Webhook](automation-webhooks.md) によって実行することができます。Azure Logic Apps、Azure 関数、ITSM 製品またはサービス、DevOps、および監視システムからオートメーションをトリガーすることによって、継続的デリバリーと運用が確保されます。

## <a name="configuration-management"></a>構成管理

Azure Automation の構成管理は、2 つの機能によってサポートされています。

* 変更履歴とインベントリ
* Azure Automation State Configuration

### <a name="change-tracking-and-inventory"></a>変更履歴とインベントリ

変更履歴とインベントリでは、さまざまな機能を組み合わせることで、Linux と Windows の仮想マシンおよびサーバー インフラストラクチャの変更を追跡できます。 このサービスを利用すれば、環境内のサービス、デーモン、ソフトウェア、レジストリ、ファイル全体にわたって変更を追跡し、不要な変更を診断したりアラートを生成したりすることができます。 インベントリのサポートにより、ゲスト リソースでクエリを実行して、インストール済みのアプリケーションやその他の構成アイテムを可視化できます。 この機能の詳細については、[変更履歴とインベントリ](change-tracking/overview.md)に関する記事を参照してください。

### <a name="azure-automation-state-configuration"></a>Azure Automation State Configuration

[Azure Automation State Configuration](automation-dsc-overview.md) は、PowerShell DSC (Desired State Configuration) 用のクラウドベースの機能であり、エンタープライズ環境に必要なサービスを備えています。 この機能を使用すると、DSC リソースを Azure Automation で管理し、Azure クラウド内の DSC プル サーバーから仮想マシンおよび物理マシンに構成を適用できます。

## <a name="update-management"></a>更新管理

Azure Automation には、Windows システムと Linux システムのハイブリッド環境全体に対する[更新管理](./update-management/overview.md)機能が含まれています。 更新管理を使用することで、Azure を始めとするクラウドとオンプレミスの垣根を越えて更新の準拠状態を可視化することができます。 この機能を使用すると、デプロイ スケジュールを作成して、定義済みのメンテナンス期間中に更新プログラムがインストールされるように調整できます。 マシンにインストールすべきでない更新プログラムは、更新管理機能を使用して、デプロイから除外することができます。

## <a name="shared-capabilities"></a>共有機能

Azure Automation には、共有リソース、ロールベースのアクセス制御、柔軟なスケジューリング、ソース管理統合、監査、タグ付けなど、さまざまな共有機能が用意されています。

### <a name="shared-resources"></a><a name="shared-resources"></a>共有リソース

Azure Automation は、環境を大きな規模で容易に自動化して構成できるようにする一連の共有リソースで構成されます。

* **[スケジュール](./shared-resources/schedules.md)** - あらかじめ定義されている時刻にオートメーション操作をトリガーします。
* **[モジュール](./shared-resources/modules.md)** - Azure やその他のシステムを管理します。 モジュールは、Microsoft、サード パーティ、コミュニティ、カスタム定義コマンドレット、および DSC リソース用の Automation アカウントにインポートできます。
* **[モジュール ギャラリー](automation-runbook-gallery.md)** - PowerShell ギャラリーとのネイティブな統合をサポートします。Runbook を表示してそれらを Automation アカウントにインポートすることができます。 PowerShell ギャラリーと Microsoft スクリプト センターからプロセスの統合と作成をすぐに開始できます。
* **[Python 2 および 3 パッケージ](python-packages.md)** - Automation アカウントでは、Python 2 および 3 Runbook がサポートされます。
* **[資格情報](./shared-resources/credentials.md)** - Runbook と構成で実行時に使用される可能性がある秘匿性の高い情報を安全に格納します。
* **[接続](automation-connections.md)** - システムへの接続に使用される一般的な情報の名前と値の組を格納します。 実行時に Runbook と構成内で使用される接続をモジュールの作成者が定義します。
* **[証明書](./shared-resources/certificates.md)** - デプロイされたリソースに対し、Runbook または DSC 構成が実行時にアクセスする際の認証とセキュリティ保護に使用される情報を定義します。
* **[変数](./shared-resources/variables.md)** - Runbook と構成間で使用できるコンテンツを保持します。 Runbook またはそれらを参照する構成を変更することなく、変数の値を変更できます。

### <a name="role-based-access-control"></a>ロールベースのアクセス制御

Azure Automation では、Automation アカウントとそのリソースへのアクセスを制限する Azure ロールベースのアクセス制御 (Azure RBAC) がサポートされています。 Automation アカウント、Runbook、ジョブに対する Azure RBAC の構成について詳しくは、[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)に関するページを参照してください。

### <a name="source-control-integration"></a>ソース管理の統合

Azure Automation では、[ソース管理の統合](source-control-integration.md)がサポートされます。 Runbook または構成をソース管理システムにチェックインできる場面で、構成をコードに昇格させることができます。

## <a name="heterogeneous-support-windows-and-linux"></a>異種環境のサポート (Windows と Linux)

Automation は、企業ネットワーク上の Windows および Linux 物理サーバー、または他のクラウド プロバイダーの Azure 以外の仮想マシンにまたがって動作するように設計されています。 デプロイされたワークロードとそれらが実行されるオペレーティング システムとを自動化、構成する一貫した方法が実現されています。 Azure Automation の Hybrid Runbook Worker 機能により、ロールをホストしている Azure 以外の物理サーバーまたは仮想マシン上で直接、環境中のリソースに対して Runbook を実行することで、それらのローカル リソースを管理できます。

[Arc 対応サーバー](../azure-arc/servers/overview.md)を使用すると、Azure 以外のマシンに対して一貫したデプロイおよび管理エクスペリエンスが提供されます。 VM 拡張機能フレームワークを使用して Automation サービスとの統合が可能になるため、Hybrid Runbook Worker ロールをデプロイし、Update Management や変更履歴とインベントリへのオンボードを簡略化することができます。

## <a name="common-scenarios"></a>一般的なシナリオ

Azure Automation は、インフラストラクチャとアプリケーションのライフサイクル全体にわたる管理をサポートします。 一般的なシナリオは、次のとおりです。

* **タスクのスケジュール** - VM またはサービスを夜間に停止して日中に稼働させ、週ごと、または月ごとの定期的なメンテナンス ワークフローを実行します。
* **Runbook の作成** - PowerShell、PowerShell ワークフロー、グラフィック、Python 2 および 3、DSC の Runbook を共通の言語で作成します。
* **リソースのビルドとデプロイ** - Runbook と Azure Resource Manager テンプレートを使用して、ハイブリッド環境全体に仮想マシンをデプロイします。 Jenkins や Azure DevOps などの開発ツールに統合します。
* **VM の構成** - インフラストラクチャとアプリケーションに適した構成を使用して Windows マシンと Linux マシンを評価、構成します。
* **知識の共有** - 組織がワークロードを提供して管理する方法に関する知識をシステムに転送します。
* **インベントリの取得** - ターゲット設定、レポート作成、コンプライアンスを目的に、デプロイされたリソースの完全なインベントリを取得します。
* **変更の検出** - 構成エラーを引き起こす可能性があるマシン変更を識別して分離し、運用上のコンプライアンスを向上させます。 それらを修復するか、管理システムにエスカレートします。
* **定期的なメンテナンス** - 古くなったデータの削除、SQL データベースのインデックスの再作成など、設定された間隔で行う必要があるタスクを実行します。
* **アラートへの応答** - コストベース、システムベース、サービスベースのアラートや、リソース使用率に関するアラートが生成された場合の応答を調整します。
* **ハイブリッド自動化** - オンプレミスのサーバーとサービス (SQL Server、Active Directory、SharePoint Server など) を管理または自動化します。
* **Azure リソースのライフサイクル管理** - IaaS および PaaS サービス向け。
* **Dev/Test 自動化のシナリオ** - リソースの開始と停止、リソースのスケーリングなど。
* **ガバナンス関連の自動化** - タグ、ロックなどを自動的に適用または更新します。
* **Azure Site Recovery** - Site Recovery DR ワークフローで定義されている事前および事後スクリプトを調整します。
* **Windows Virtual Desktop** - 使用率に基づいて VM のスケーリングを調整したり、VM を開始または停止したりします。

要件に応じて、次の Azure サービスの 1 つ以上が、Azure Automation と統合または Azure Automation を補完して、要件を満たすことができるようにします。

* [Azure Arc 対応サーバー](../azure-arc/servers/overview.md)を使用すると、Update Management、変更履歴とインベントリ、および Hybrid Runbook Worker ロールへのハイブリッド マシンのオンボードを簡略化できます。
* [Azure アラート アクション グループ](../azure-monitor/alerts/action-groups.md)を使用すると、アラートが発生した場合に Automation Runbook を開始できます。
* [Azure Monitor](../azure-monitor/overview.md) では、Automation アカウントからメトリックとログ データを収集して詳細な分析を行い、テレメトリに基づいてアクションを実行します。 Update Management や変更履歴とインベントリなどの Automation 機能は、その機能の要素を提供する Log Analytics ワークスペースに依存しています。
* [Azure Policy](../governance/policy/samples/built-in-policies.md) には、Automation アカウントのさまざまなセキュリティ標準に対するコンプライアンスを確立および維持するのに役立つイニシアチブ定義が含まれています。
* [Azure Site Recovery](../site-recovery/site-recovery-runbook-automation.md) では、Azure Automation Runbook を使用することで、復旧計画を自動化できます。

これらの Azure サービスは、HTTP Webhook または API メソッドを使用して、Automation ジョブおよび Runbook リソースを処理できます。

* [Azure Logic Apps](../connectors/built-in.md)
* [Azure Power Apps](/connectors/azureautomation)
* [Azure Event Grid](../event-grid/handler-webhooks.md)
* [Azure Power Automate](/connectors/azureautomation)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-azure-automation"></a>Azure Automation の価格

Azure Automation に関する価格は、[価格](https://azure.microsoft.com/pricing/details/automation/)ページで確認できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Automation アカウントを作成する](./quickstarts/create-account-portal.md)