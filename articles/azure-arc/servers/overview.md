---
title: Azure Arc 対応サーバーの概要
description: Azure Arc 対応サーバーを使用して、Azure の外部でホストされているサーバーを Azure リソースと同じように管理する方法について説明します。
ms.date: 09/30/2021
ms.topic: overview
ms.openlocfilehash: c390cbbb6f08f4f9082b0764125ab9a14407de95
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287269"
---
# <a name="what-is-azure-arc-enabled-servers"></a>Azure Arc 対応サーバーとは

Azure Arc 対応サーバーを使用すると、Azure の "*外部*"、企業ネットワーク上、または他のクラウド プロバイダーでホストされている Windows および Linux の物理サーバーと仮想マシンを管理できます。 この管理エクスペリエンスは、ネイティブ Azure 仮想マシンの管理方法と一致するように設計されています。 ハイブリッド マシンは、Azure に接続されると接続済みマシンになり、Azure 内のリソースとして扱われます。 接続されている各マシンにはリソース ID があり、それによってマシンをリソース グループに含めることができます。 Azure Policy やタグの適用などの標準的な Azure コンストラクトの恩恵を受けることができます。 顧客のオンプレミス インフラストラクチャを管理するサービス プロバイダーは、現在ネイティブ Azure リソースで行っているのと同様に、[Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) を使用して複数の顧客環境で各自のハイブリッド マシンを管理できます。

ハイブリッド マシンでこのエクスペリエンスを実現するには、Azure Connected Machine エージェントを各マシンにインストールする必要があります。 このエージェントは他の機能をまったく持たず、Azure [Log Analytics エージェント](../../azure-monitor/agents/log-analytics-agent.md)に代わるものでもありません。 次の場合、Windows および Linux 向けの Log Analytics エージェントが必要です。

* マシン上で実行されている OS とワークロードを予防的に監視する。
* Automation Runbook、または Update Management などのソリューションを使用して管理する。
* [Microsoft Defender for Cloud](../../security-center/security-center-introduction.md) などの他の Azure サービスを使用する。

## <a name="supported-cloud-operations"></a>サポートされているクラウド運用 

マシンを Azure Arc 対応サーバーに接続すると、次の表に示す運用機能を実行できるようになります。

|運用機能 |説明 | 
|--------------------|------------|
|**ガバナンス** ||
| Azure Policy |マシン内の設定を監査するために、[Azure Policy のゲスト構成](../../governance/policy/concepts/guest-configuration.md)を割り当てます。 Arc 対応サーバーで Azure Policy のゲスト構成ポリシーを使用するコストについては、Azure Policy の[価格ガイド](https://azure.microsoft.com/pricing/details/azure-policy/)を参照してください。|
|**保護** ||
| Microsoft Defender for Cloud | Azure 以外のサーバーを [Microsoft Defender for Endpoint](/microsoft-365/security/defender-endpoint) で保護します。たとえば、[Microsoft Defender for Cloud](../../security-center/defender-for-servers-introduction.md) を利用したり、脅威を検出したり、脆弱性を管理したり、セキュリティ上の潜在的脅威がないか、積極的に監視したりします。 Microsoft Defender for Cloud は、検出された脅威からアラートと修復の提案を提示します。 |
| Microsoft Sentinel | Arc 対応サーバーに接続されているマシンを [Microsoft Sentinel で構成](scenario-onboard-azure-sentinel.md)して、セキュリティ関連のイベントを収集し、それらを他のデータ ソースと関連付けることができます。 |
|**構成** ||
| Azure Automation |PowerShell と Python [Runbook](../../automation/automation-runbook-execution.md) を使用して、頻繁で時間のかかる管理タスクを自動化します。<br> [変更履歴とインベントリ](../../automation/change-tracking/overview.md)を使用して、インストールされているソフトウェア、Microsoft サービス、Windows レジストリとファイル、および Linux デーモンに関する構成変更を評価します。<br> [Update Management](../../automation/update-management/overview.md) を使用して、Windows と Linux サーバーのオペレーティング システム用の更新プログラムを管理します。 |
| Azure Automanage (プレビュー) | [Arc 対応サーバーで Automanage マシン](../../automanage/automanage-arc.md)を使用するときに、一連の Azure サービスのオンボードと構成が自動化されます。 |
| VM 拡張機能 | Azure 以外の Windows または Linux マシンに、サポートされている [Arc 対応サーバーの VM 拡張機能](manage-vm-extensions.md)を使用して、デプロイ後の構成および自動化タスクを提供します。 |
|**監視**|
| Azure Monitor | [VM insights](../../azure-monitor/vm/vminsights-overview.md) を使用して、接続されているマシンのゲスト オペレーティング システムのパフォーマンスを監視します。また、アプリケーション コンポーネントを検出して、そのプロセスや、他のリソースとの依存関係を監視します。 [Log Analytics エージェント](../../azure-monitor/agents/agents-overview.md#log-analytics-agent)を使用して、マシンで実行されているオペレーティング システムやワークロードから、パフォーマンス データやイベントなどの他のログ データを収集します。 このデータは、[Log Analytics ワークスペース](../../azure-monitor/logs/design-logs-deployment.md)に格納されます。 |

> [!NOTE]
> 現時点では、Azure Automation の Update Management を Azure Arc 対応サーバーから直接有効にすることはサポートされていません。 要件とサーバーに対する有効化の方法を理解するには、「[Automation アカウントから Update Management を有効にする](../../automation/update-management/enable-from-automation-account.md)」を参照してください。

ハイブリッド マシンから収集されて Log Analytics ワークスペースに格納されるログ データには、[リソース コンテキスト](../../azure-monitor/logs/design-logs-deployment.md#access-mode)のログ アクセスをサポートするために、リソース ID など、マシンに固有のプロパティが含まれるようになりました。

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

Azure Arc 対応サーバーを使用してハイブリッドおよびマルチクラウド環境で Azure の監視、セキュリティ、更新サービスを実装する方法の詳細については、次のビデオをご覧ください。

> [!VIDEO https://www.youtube.com/embed/mJnmXBrU1ao]

## <a name="supported-regions"></a>サポートされているリージョン

Azure Arc 対応サーバーでサポートされているリージョンの確定的な一覧については、[リージョン別の Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)に関するページを参照してください。

ほとんどの場合、インストール スクリプトを作成するときに選択する場所は、マシンの場所に最も近い Azure リージョンにする必要があります。 保存データは、指定したリージョンを含む Azure 地域内に保存されます。データ所在地の要件がある場合は、これがリージョンの選択に影響する可能性もあります。 マシンが接続されている Azure リージョンが障害の影響を受ける場合、接続されたマシンは影響を受けませんが、Azure を使用した管理操作を完了できない可能性があります。 リージョンで障害が発生したとき、地理的に冗長なサービスをサポートする複数の場所がある場合、各場所のマシンを別の Azure リージョンに接続することをお勧めします。

接続されているマシンに関する次のメタデータ情報が収集され、Azure Arc マシン リソースが構成されているリージョンに保存されます。

- オペレーティング システムの名前とバージョン
- コンピューター名
- コンピューターの完全修飾ドメイン名 (FQDN)
- Connected Machine エージェントのバージョン

たとえば、マシンが米国東部リージョンの Azure Arc に登録されている場合、このデータは米国リージョンに保存されます。

## <a name="supported-environments"></a>サポートされている環境

Azure Arc 対応サーバーでは、Azure の "*外部*" でホストされている物理サーバーと仮想マシンの管理がサポートされます。 VM をホストするハイブリッド クラウド環境のうち、サポートされている環境の詳細については、[Connected Machine エージェントの前提条件](agent-overview.md#supported-environments)に関するページを参照してください。

> [!NOTE]
> Azure Arc 対応サーバーは、Azure で実行される仮想マシンの管理を可能にするようには設計されておらず、サポートされていません。

## <a name="agent-status"></a>エージェントの状態

Connected Machine エージェントは、5 分間隔でハートビート メッセージをサービスに送信します。 これらのハートビート メッセージをサービスがマシンから受信できなくなると、そのマシンはオフラインと見なされ、15 分から 30 分以内にポータルでの状態が自動的に **[切断]** に変更されます。 その後、Connected Machine エージェントからハートビート メッセージを受信すると、状態は自動的に **[接続]** に変更されます。

## <a name="service-limits"></a>サービスの制限

Azure Arc 対応サーバーには、各リソース グループに作成できるインスタンスの数に制限があります。 サブスクリプションまたはサービス レベルの制限はありません。 どのようなリソースの種類の制限があるかについては、[リソース インスタンスの制限](../../azure-resource-manager/management/resources-without-resource-group-limit.md#microsofthybridcompute)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* 複数のハイブリッド マシンにまたがって Azure Arc 対応サーバーを評価または有効にする前に、[Connected Machine エージェントの概要](agent-overview.md)に関するページを確認して、要件、エージェントに関する技術的な詳細、デプロイ方法を理解してください。

* [計画と展開ガイド](plan-at-scale-deployment.md)を参照して、任意の規模で Azure Arc 対応サーバーをデプロイし、一元的な管理と監視を実装する計画を立ててください。
