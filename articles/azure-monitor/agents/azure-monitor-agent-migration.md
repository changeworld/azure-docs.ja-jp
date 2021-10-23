---
title: レガシ エージェントから新しい Azure Monitor エージェントへの移行
description: 既存のレガシ エージェントから新しい Azure Monitor エージェント (AMA) およびデータ収集ルール (DCR) への移行に関するガイダンス。
ms.topic: conceptual
author: shseth
ms.author: shseth
ms.date: 7/12/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: d3f2fa2457ef52fc02e2fd5b7d183918e2e9e09c
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177989"
---
# <a name="migrate-from-log-analytics-agents"></a>Log Analytics エージェントからの移行
この記事では、新しい Azure Monitor エージェント (AMA) およびデータ収集ルール (DCR) に移行するタイミングと方法について、大まかなガイダンスを提供します。 この記事は、新しい移行ツールが利用可能になったときに更新されます。


## <a name="review"></a>レビュー
- 今すぐ、または後で新しい Azure Monitor エージェントに移行する必要があるかどうかを判断するために、「[Azure Monitor エージェントの概要](./azure-monitor-agent-overview.md#should-i-switch-to-the-azure-monitor-agent)」でガイダンスを参照してください。
- Azure Monitor エージェントの新しい機能について、既存の機能、サービス、およびソリューションの可用性について、現在の制限事項について「[Azure Monitor エージェントの概要](./agents-overview.md#azure-monitor-agent)」を参照してください。


## <a name="test-migration-by-using-the-azure-portal"></a>Azure portal を使用した移行のテスト
1. 移行中に安全にデプロイできるようにするには、既存の Log Analytics エージェントを実行している、非運用環境でいくつかのリソースを使用してテストを開始します。 これらのテスト リソースで収集されたデータを検証したら、同じ手順に従って運用環境にロールアウトします。
1. **[モニター]**  >  **[設定]**  >  **[データ収集ルール]** の順に移動し、[新しいデータ収集ルールを作成](./data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal)して既存のデータ型の一部の収集を開始します。 ポータル GUI を使用すると、すべてのターゲット リソースに対して次の手順が実行されます。
    - システム割り当てマネージド ID の有効化
    - Azure Monitor エージェント拡張機能のインストール
    - データ収集ルールの関連付けの作成とデプロイ
1. 検証データは、Azure Monitor エージェントを使用して期待どおりに送られます。 **ハートビート** テーブルで、新しいエージェント バージョンの値を確認します。 既存の Log Analytics エージェントを通過するデータと一致していることを確認します。


## <a name="at-scale-migration-by-using-policies"></a>ポリシーを使用した大規模な移行
1. まず、次の基準を使用して、MMA または OMS で現在の監視設定を分析します。
    - 仮想マシン、仮想マシン スケール セット、オンプレミス サーバーなどの移行元
    - パフォーマンス カウンター、Windows イベント ログ、Syslog などのデータ ソース
    - Log Analytics ワークスペースなどの移行先
1. 前の構成を使用して、[新しいデータ収集ルールを作成](/rest/api/monitor/datacollectionrules/create#examples)します。 ベスト プラクティスとして、Windows と Linux ソースに対して個別のデータ収集ルールを作成することをお勧めします。 または、データ収集のニーズが異なるチームごとに、個別のデータ収集ルールを作成することもできます。
1. 移行先リソースで[システム割り当てマネージド ID を有効にします](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md#system-assigned-managed-identity)。
2. Azure Monitor エージェント拡張機能をインストールします。 [組み込みのポリシー イニシアチブ](azure-monitor-agent-install.md#install-with-azure-policy)を使用して、すべてのターゲット リソースにデータ収集ルールの関連付けをデプロイします。 前のデータ収集ルールを入力パラメーターとして指定します。 
1. 検証データは、Azure Monitor エージェントを使用して期待どおりに送られます。 **ハートビート** テーブルで、新しいエージェント バージョンの値を確認します。 既存の Log Analytics エージェントを通過するデータと一致していることを確認します。
2. ダッシュボード、アラート、runbook worker など、すべてのダウンストリームの依存関係を検証します。 ブックはすべて、新しいエージェントからのデータを使用して引き続き機能します。
3. リソースから [Log Analytics エージェントをアンインストール](./agent-manage.md#uninstall-agent)します。 System Center Operations Manager シナリオや、Azure Monitor エージェントでまだ使用できないその他のソリューションに対して使用する必要がある場合は、アンインストールしないでください。
4. 以前に Log Analytics エージェントによって使用されていた構成ファイル、ワークスペース キー、または証明書を消去します。


