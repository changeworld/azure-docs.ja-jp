---
title: Azure Policy と Azure Blueprints によるセキュリティ コンプライアンス
description: ASD ISM および Essential 8 に関連したオーストラリア政府機関向けの Azure Policy および Azure Blueprints によるコンプライアンスの保証とセキュリティの適用
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: 79ee7b4a0d21322e8c2e497eecd1704356282eba
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "117029226"
---
# <a name="security-compliance-with-azure-policy-and-azure-blueprints"></a>Azure Policy と Azure Blueprints によるセキュリティ コンプライアンス

すべての組織には、オンプレミス、クラウド ネイティブ、ハイブリッド環境のいずれであっても、IT 環境内にガバナンスを適用するという課題があります。 Microsoft Azure 環境が、設計、規制、およびセキュリティの各要件を満たすようにするには、堅牢な技術的ガバナンス フレームワークが必要です。

オーストラリア政府機関の場合、リスクを評価するときに考慮すべき重要な統制は、[Australian Cyber Security Centre (ACSC) の Information Security Manual](https://acsc.gov.au/infosec/ism/index.htm) (ISM) です。 ISM に詳細に記載されている統制の大部分は、技術的ガバナンスの適用を効果的に管理および実施する必要があることを求めています。 お使いの環境で構成を評価して実施するための適切なツールを用意することが重要です。

Microsoft Azure には、これらの課題に役立つ 2 つの無料サービスである Azure Policy と Azure Blueprints が用意されています。

## <a name="azure-policy"></a>Azure Policy

Azure Policy によって、組織の IT ガバナンスの技術的な要素を適用できるようになります。 Azure Policy には、増大し続ける組み込みポリシーのライブラリが含まれています。 各ポリシーは、対象となる Azure リソースに対して規則と効果を適用します。

リソースにポリシーが割り当てられると、そのポリシーに対する全体的なコンプライアンスが評価され、必要に応じて修復されます。

この組み込みの Azure ポリシーのライブラリを使用すると、組織は ACSC ISM で検出された種類の統制をすばやく適用できます。 統制の例を次に示します。

* システム更新プログラムの欠落に関する仮想マシンの監視
* 多要素認証のための高度なアクセス許可を持つアカウントの監査
* 暗号化されていない SQL Database の特定
* カスタムの Azure ロールベースのアクセス制御 (Azure RBAC) の使用の監視
* リソースを作成できる Azure リージョンの制限

組み込みの Azure ポリシー定義でガバナンスまたは規制による統制が満たされない場合、カスタム定義を作成して割り当てることができます。 すべての Azure ポリシー定義は JSON で定義され、標準の[定義構造](../governance/policy/concepts/definition-structure.md)に従います。 また、既存の Azure ポリシー定義を複製して、カスタム ポリシー定義の基礎を形成することもできます。

個々の Azure ポリシーをリソースに割り当てると、特に複雑な環境や厳格な規制要件を持つ環境では、管理者に大きなオーバーヘッドが生じる可能性があります。 これらの課題に対応するために、一連の Azure ポリシーをまとめてグループ化し、Azure ポリシー イニシアティブを作成できます。 関連する Azure ポリシーを結合するためにポリシー イニシアティブが使用され、グループとして適用されるとき、それが特定のセキュリティ目標またはコンプライアンス目標のベースとなります。 Microsoft は、特定の規制要件を満たすように設計された定義を含む、組み込みの Azure ポリシー イニシアティブ定義を追加しています。

![規制に対するコンプライアンス ポリシー イニシアティブ](media/regulatory-initiatives.png)

すべての Azure ポリシーおよびイニシアティブは、割り当てスコープに割り当てられます。 このスコープは、Azure サブスクリプション、Azure 管理グループ、または Azure リソース グループのいずれかのレベルで定義されます。 必要な Azure ポリシーまたはポリシー イニシアティブが割り当てられると、組織では新しく作成されたすべての Azure リソースに対して構成要件を適用できるようになります。

新しい Azure ポリシーまたはイニシアティブの割り当ては、既存の Azure リソースに影響しません。 ただし、Azure Policy によって、組織は既存の Azure リソースのコンプライアンスを確認できます。 非準拠として識別されたすべてのリソースは、組織の裁量で修復できます

### <a name="azure-policy-and-initiatives-in-action"></a>有効な Azure のポリシーとイニシアティブ

使用できる組み込みの Azure ポリシーとイニシアティブの定義は、Azure portal のポリシー セクションの [定義] ノードにあります。

![組み込みの Azure ポリシー定義](media/policy-definitions.png)

組み込み定義のライブラリを使用すると、組織の要件を満たすポリシーをすばやく検索し、ポリシー定義を確認して、適切なリソースにポリシーを割り当てることができます。 たとえば ISM では、特権を持つすべてのユーザーおよび重要なデータ リポジトリにアクセスできるすべてのユーザーに対して多要素認証 (MFA) を求めています。 Azure Policy では、Azure ポリシー定義から "MFA" を検索できます。

![Azure AD MFA ポリシー](media/mfa-policies.png)

適切なポリシーを特定したら、目的のスコープにポリシーを割り当てます。 要件を満たす組み込みポリシーがない場合は、既存のポリシーを複製して、必要な変更を行うことができます。

![既存の Azure ポリシーを複製する](media/duplicate-policy.png)

また、Microsoft では、カスタム Azure ポリシーを作成するための "クイックスタート" として、Azure ポリシー サンプルのコレクションを [GitHub](https://github.com/Azure/azure-policy) に提供しています。 これらのポリシー サンプルは、Azure portal 内の Azure ポリシー エディターに直接コピーできます。

Azure ポリシー イニシアティブを作成するとき、組み込みとカスタムの両方の使用可能なポリシー定義の一覧を並べ替えて、必要な定義を追加できます。

たとえば、Windows 仮想マシンに関連するすべてのポリシーについて、使用可能な Azure ポリシー定義の一覧を検索することができます。 次に、推奨される仮想マシンの強化手法を適用するように設計されたイニシアティブに定義を追加します。

![Azure ポリシーの一覧](media/initiative-definitions.png)

Azure ポリシーまたはポリシー イニシアティブを割り当てスコープに割り当てるときに、Azure 管理グループまたは Azure リソース グループを除外することによって、ポリシーの効果から Azure リソースを除外することができます。

### <a name="real-time-enforcement-and-compliance-assessment"></a>リアルタイムの適用とコンプライアンスの評価

次の条件が満たされると、スコープ内の Azure リソースの Azure ポリシー コンプライアンス スキャンが実行されます。

* Azure ポリシーまたは Azure ポリシー イニシアティブが割り当てられている場合
* 既存の Azure ポリシーまたはイニシアティブのスコープが変更された場合
* API 経由でオンデマンド (1 時間あたり最大 10 回までのスキャン)
* 24 時間ごとに 1回 (既定の動作)

1 つの Azure リソースに対するポリシー コンプライアンス スキャンは、リソースが変更されてから 15 分後に実行されます。

リソースの Azure ポリシー コンプライアンスの概要については、ポリシー コンプライアンス ダッシュボードを使用して Azure portal 内で確認できます。

![Azure ポリシーのコンプライアンス スコア](media/simple-compliance.png)

[リソースの全体的なコンプライアンス] のパーセントの数字は、割り当て済みのすべての Azure ポリシーに対する、スコープ内のすべてのデプロイ済みリソースのコンプライアンスの総計を示しています。 これにより、準拠していない環境内のリソースを識別し、これらのリソースを最も適切に修復する計画を立てることができます。

ポリシーのコンプライアンス ダッシュボードには、各リソースの変更履歴も含まれています。 割り当てられたポリシーにリソースが準拠しなくなったと識別され、自動修復が有効になっていない場合、変更を行ったユーザー、変更された内容、およびそのリソースに変更が加えられた日時を表示できます。

## <a name="azure-blueprints"></a>Azure Blueprint

Azure Blueprints は、Azure Policy の機能を次のものと結合することによって拡張します。

* Azure RBAC
* Azure リソース グループ
* [Azure Resource Manager のテンプレート](../azure-resource-manager/templates/syntax.md)

Blueprints によって、Resource Manager テンプレートから Azure リソースをデプロイし、Azure RBAC を構成し、Azure Policy を割り当てることによって構成を適用および監査する、環境設計を作成できます。 Blueprints は、編集可能でデプロイ可能な環境テンプレートを形成します。 ブループリントが作成されたら、Azure サブスクリプションに割り当てることができます。 割り当てが完了すると、ブループリント内で定義されているすべての Azure リソースが作成され、Azure ポリシーが適用されます。 Azure ブループリントで定義されたリソースのデプロイと構成は、Azure portal の Azure ブループリント コンソールから監視できます。

編集された Azure ブループリントは、Azure portal で再発行する必要があります。 ブループリントが再発行されるたびに、ブループリントのバージョン番号が増えます。 バージョン番号を使用すると、組織の Azure サブスクリプションにデプロイされている特定のバージョンのブループリントを判別できます。 必要に応じて、現在割り当てられているブループリントのバージョンを最新バージョンに更新できます。

Azure ブループリントを使用してデプロイされたリソースは、デプロイ時に [Azure のリソース ロック](../azure-resource-manager/management/lock-resources.md)を使用して構成できます。 リソース ロックによって、リソースが誤って変更または削除されるのを防ぐことができます。

Microsoft は、さまざまな業界および規制要件に対応する Azure ブループリント テンプレートを開発しています。 使用可能な Azure ブループリント定義の現在のライブラリは、Azure portal または Service Trust Portal の [Azure のセキュリティとコンプライアンスのブループリント](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview/)のページで確認できます。

### <a name="azure-blueprint-artifacts"></a>Azure ブループリント アーティファクト

Azure ブループリントを作成するには、最初に空のブループリント テンプレートを使用するか、既存のいずれかのサンプル ブループリントを出発点として使用します。 デプロイの一部として構成されるブループリントにアーティファクトを追加できます。

![Azure ブループリント アーティファクト](media/blueprint-artifacts.png)

これらのアーティファクトには、お使いの環境がシステム強化用の ISM 統制などの規制要件に準拠するために必要な構成を適用する、Azure リソース グループとリソース、および関連付けられている Azure Policy とポリシー イニシアティブが含まれます。

これらの各アーティファクトはパラメーターを使用して構成することもできます。 これらの値は、ブループリントが Azure サブスクリプションに割り当てられてデプロイされるときに提供されます。 パラメーターを使用すると、1 つのブループリントを作成し、基になるブループリントを編集することなく、さまざまな環境にリソースをデプロイすることができます。

Microsoft は、CI/CD パイプラインを使用してブループリントを組織で管理およびデプロできるようにするために、Azure ブループリントを作成および管理するための Azure PowerShell および CLI コマンドレットを開発しています。

## <a name="next-steps"></a>次のステップ

この記事では、Azure Policy と Azure Blueprints を使用して、ガバナンスとセキュリティを適用する方法について説明しました。 上位レベルでの学習が済んだので、次は各サービスの使用方法について詳しく説明します。

* [Azure Policy の概要](../governance/policy/overview.md)
* [Azure Blueprints の概要](https://azure.microsoft.com/services/blueprints/)
* [Azure Policy のサンプル](../governance/policy/samples/index.md)
* [Azure Policy のサンプル リポジトリ](https://github.com/Azure/azure-policy)
* [Azure Policy の定義の構造](../governance/policy/concepts/definition-structure.md)
* [Azure Policy の効果](../governance/policy/concepts/effects.md)