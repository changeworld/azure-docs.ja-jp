---
title: Azure Arc for servers (プレビュー) の概要
description: Azure Arc for servers を使用して、Azure の外部でホストされているマシンを Azure リソースと同じように管理する方法について説明します。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: azure automation, DSC, powershell, 望ましい状態の構成, 更新管理, 変更追跡, インベントリ, Runbook, Python, グラフィカル, ハイブリッド
ms.date: 03/24/2020
ms.topic: overview
ms.openlocfilehash: 95a01db7d4d889df4695390bfd0d01510d83a817
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648048"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>Azure Arc for servers (プレビュー) とは

Azure Arc for servers (プレビュー) を使用すると、Azure の外部 (企業ネットワークや Azure 以外のクラウド プロバイダー) でホストされている Windows や Linux のマシンを、ネイティブの Azure 仮想マシンと同じように管理することができます。 ハイブリッド マシンは、Azure に接続されると接続済みマシンになり、Azure 内のリソースとして扱われます。 接続済みマシンにはそれぞれリソース ID があり、サブスクリプション内のリソース グループの一部として管理されます。また、Azure Policy やタグの適用などの標準的な Azure のコンストラクトの利点も活用できます。

Azure の外部でホストされているハイブリッド マシンでこのエクスペリエンスを実現するには、Azure への接続を予定している各マシンに Azure Connected Machine エージェントがインストールされている必要があります。 このエージェントは他の機能をまったく持たず、Azure [Log Analytics エージェント](../../azure-monitor/platform/log-analytics-agent.md)に代わるものでもありません。 マシン上で実行されている OS とワークロードをプロアクティブに監視したい場合、それを Automation Runbook やソリューション (Update Management など) を使用して管理したい場合、または他の Azure サービス ([Azure Security Center](../../security-center/security-center-intro.md) など) を使用したい場合は、Windows 用および Linux 用の Log Analytics エージェントが必要となります。

>[!NOTE]
>このプレビュー リリースは評価を目的としたものであり、運用環境の重要なマシンの管理には使用しないことをお勧めします。
>

## <a name="supported-scenarios"></a>サポートされるシナリオ

Azure Arc for servers (プレビュー) は、接続済みのマシンに関して次のシナリオをサポートします。

- Azure 仮想マシンのポリシーの割り当てと同じエクスペリエンスを使用して [Azure Policy のゲスト構成](../../governance/policy/concepts/guest-configuration.md)を割り当てます。
- Log Analytics エージェントによって収集されたログ データ。マシンが登録されている Log Analytics ワークスペースに格納されます。 ハイブリッド マシンからのログ データに、リソース ID など、マシンに固有のプロパティが含まれるようになりました。それを利用し、[resource-context](../../azure-monitor/platform/design-logs-deployment.md#access-mode) ログ アクセスをサポートできます。

## <a name="supported-regions"></a>サポートされているリージョン

Azure Arc for servers (プレビュー) では、特定のリージョンのみがサポートされています。

- WestUS2
- 西ヨーロッパ
- WestAsia

ほとんどの場合、インストール スクリプトを作成するときに選択する場所は、マシンの場所に最も近い Azure リージョンにする必要があります。 保存データは、指定したリージョンを含む Azure geography 内に格納されます。データ所在地に関する要件がある場合は、これがリージョンの選択に影響する可能性もあります。 マシンが接続されている Azure リージョンが障害の影響を受ける場合、接続されたマシンは影響を受けませんが、Azure を使用した管理操作を完了できない可能性があります。 リージョンの障害が発生した場合の回復力のために、地理的に冗長なサービスを提供する複数の場所がある場合は、各場所のマシンを別の Azure リージョンに接続することをお勧めします。

### <a name="agent-status"></a>エージェントの状態

Connected Machine エージェントは、5 分間隔でハートビート メッセージをサービスに送信します。 これらのハートビート メッセージをサービスがマシンから受信できなくなると、そのマシンはオフラインと見なされ、15 分から 30 分以内にポータルでの状態が自動的に **[切断]** に変更されます。 その後、Connected Machine エージェントからハートビート メッセージを受信すると、状態は自動的に **[接続]** に変更されます。

## <a name="next-steps"></a>次のステップ

複数のハイブリッド マシン間で Arc for servers (プレビュー) を評価または有効化する前に、[Connected Machine エージェントの概要](agent-overview.md)の記事を確認し、必要なもの、エージェントに関する技術的な詳細、デプロイ方法を把握してください。
