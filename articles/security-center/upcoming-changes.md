---
title: Azure Security Center に対する今後の重要な変更
description: 知っておく必要があり、計画する必要がある Azure Security Center の今後の変更
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 07/25/2021
ms.author: memildin
ms.openlocfilehash: 2770c3532dd83051f9c2c7dcc770850e85aeae4b
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2021
ms.locfileid: "114689577"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Azure Security Center に対する今後の重要な変更

> [!IMPORTANT]
> このページの情報は、販売を開始する前に大幅に変更される可能性があるプレリリース製品または機能に関連しています。 Microsoft は、ここで提供される情報に対して、明示または黙示を問わず、一切の確約または保証を行わないものとします。

このページでは、Security Center に対して計画されている変更について説明します。 ここでは、セキュリティ スコアやワークフローなどに影響する可能性がある、製品に対して計画されている変更について説明します。

最新のリリース ノートをお探しの場合は、「[Azure Security Center の最新情報](release-notes.md)」をご覧ください。


## <a name="planned-changes"></a>計画されている変更

| 計画されている変更                                                                                                                                                                                          | 変更予定日 |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [ISO 27001 のレガシ実装を新しい ISO 27001:2013 で置き換え予定](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)                                            | 2021 年 7 月                 |
| [ "お使いのマシンで Log Analytics エージェントの正常性の問題を解決する必要がある" という推奨事項の廃止](#deprecating-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines) | 2021 年 7 月                 |
| [Azure Resource Manager アラート用の Azure Defender の論理的な再編成](#logical-reorganization-of-azure-defender-for-resource-manager-alerts)                                                           | 2021 年 8 月               |
| [CSV エクスポートが 20 MB に制限される](#csv-exports-to-be-limited-to-20-mb)                                                                                                                               | 2021 年 8 月               |
| [[Azure Defender を有効にする] セキュリティ コントロールをセキュア スコアに含める](#enable-azure-defender-security-control-to-be-included-in-secure-score)                                                         | 2021 年第 3 四半期                   |
| [SQL データベースで機密データを分類するための推奨事項の強化](#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)                                               | Q1 2022                   ||                                                                                                                                                                                                         |                           |


### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>ISO 27001 のレガシ実装を新しい ISO 27001:2013 で置き換え予定

**変更予定日:** 2021 年 7 月

ISO 27001 のレガシ実装は Security Center の規制コンプライアンス ダッシュボードから削除されます。 Security Center で ISO 27001 へのコンプライアンスを追跡している場合は、関連するすべての管理グループまたはサブスクリプションに対して新しい ISO 27001:2013 標準をオンボードすると、現在のレガシ ISO 27001 が間もなくダッシュボードから削除されます。

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="ISO 27001 のレガシ実装の削除に関するメッセージが表示されている Security Center の規制コンプライアンス ダッシュボード。" lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="deprecating-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines"></a>"お使いのマシンで Log Analytics エージェントの正常性の問題を解決する必要がある" という推奨事項の廃止

**変更予定日:** 2021 年 7 月

**お使いのマシンで Log Analytics エージェントの正常性の問題を解決する必要がある** という推奨事項は、Security Center のクラウド セキュリティ態勢管理 (CSPM) の取り組みと整合性がないという点で、セキュリティ スコアに影響することがわかりました。 通常、CSPM はセキュリティ構成の誤りを特定する方法に関連するものです。 エージェントの正常性の問題は、このカテゴリの問題には該当しません。

また、この推奨事項は、Security Center に関連する他のエージェントと比較した場合、特異なものです。これは、正常性の問題に関する推奨事項を持つ唯一のエージェントだからです。

この推奨事項は廃止になります。

この廃止を受けて、Log Analytics エージェントのインストールに関する推奨事項についても少し変更します (**Log Analytics エージェントを ... にインストールする必要があります**)。

この変更は、セキュリティ スコアに影響を与える可能性があります。 ほとんどのサブスクリプションでは、変更によってスコアが上がることが期待できますが、インストールの推奨事項を更新すると、場合によってはスコアが下がる可能性があります。

> [!TIP]
> また、[[資産インベントリ]](asset-inventory.md) ページも、この変更の影響も受けます。マシンが監視されているか、監視されていないか、または部分的に監視されているかどうかに関する情報も表示されるためです (正常性の問題があるエージェントに言及する状態)。 


### <a name="logical-reorganization-of-azure-defender-for-resource-manager-alerts"></a>Azure Resource Manager アラート用の Azure Defender の論理的な再編成

**変更予定日:** 2021 年 8 月

次に示すアラートは、現在、[Azure Defender For Resource Manager](defender-for-resource-manager-introduction.md) プランの一部として提供されています。

Azure Defender プランの論理的な再編成の一環として、一部のアラートは **Azure Defender for Resource Manager** から **サーバー用 Azure Defender** に移動されます。

アラートは、次の 2 つの主要原則に従って編成されます。

- さまざまな種類の Azure リソースにわたるコントロールプレーンの保護を提供するアラートは、Azure Defender for Resource Manager の一部になります。
- 特定のワークロードを保護するアラートは、そのワークロードに関連する対応する Azure Defender プランに移動されます。

これらは現在、Azure Defender for Resource Manager に属するアラートであり、この変更の結果として、サーバー用 Azure Defender に移動されます。

- ARM_AmBroadFilesExclusion
- ARM_AmDisablementAndCodeExecution
- ARM_AmDisablement
- ARM_AmFileExclusionAndCodeExecution
- ARM_AmTempFileExclusionAndCodeExecution
- ARM_AmTempFileExclusion
- ARM_AmRealtimeProtectionDisabled
- ARM_AmTempRealtimeProtectionDisablement
- ARM_AmRealtimeProtectionDisablementAndCodeExec
- ARM_AmMalwareCampaignRelatedExclusion
- ARM_AmTemporarilyDisablement
- ARM_UnusualAmFileExclusion
- ARM_CustomScriptExtensionSuspiciousCmd
- ARM_CustomScriptExtensionSuspiciousEntryPoint
- ARM_CustomScriptExtensionSuspiciousPayload
- ARM_CustomScriptExtensionSuspiciousFailure
- ARM_CustomScriptExtensionUnusualDeletion
- ARM_CustomScriptExtensionUnusualExecution
- ARM_VMAccessUnusualConfigReset
- ARM_VMAccessUnusualPasswordReset
- ARM_VMAccessUnusualSSHReset

[Azure Defender for Resource Manager](defender-for-resource-manager-introduction.md) と[サーバー用 Azure Defender](defender-for-servers-introduction.md) の詳細を参照してください。


### <a name="csv-exports-to-be-limited-to-20-mb"></a>CSV エクスポートが 20 MB に制限される

**変更予定日:** 2021 年 8 月

Security Center の推奨事項に関するデータをエクスポートする場合、現在ダウンロードできるデータの量に制限はありません。

:::image type="content" source="media/upcoming-changes/download-csv-report.png" alt-text="推奨事項に関するデータをエクスポートするための Security Center の [CSV レポートのダウンロード] ボタン。":::

この変更により、20 MB の制限を設けます。

大量のデータをエクスポートする必要がある場合は、選択する前に使用可能なフィルターを使用するか、またはサブスクリプションのサブセットを選択してデータをバッチでダウンロードします。

:::image type="content" source="media/upcoming-changes/filter-subscriptions.png" alt-text="Azure portal でサブスクリプションをフィルター処理する":::

詳細については、[セキュリティに関する推奨事項の CSV エクスポートの実行](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations)に関するページを参照してください。

### <a name="enable-azure-defender-security-control-to-be-included-in-secure-score"></a>[Azure Defender を有効にする] セキュリティ コントロールをセキュア スコアに含める

**変更予定日:** 2021 年第 3 四半期

Security Center のセキュリティ強化に関する推奨事項は、セキュリティ コントロールにグループ化されています。 各コントロールは、関連するセキュリティの推奨事項の論理グループであり、脆弱な攻撃対象領域を反映しています。 総合的なセキュア スコアに対する各セキュリティ コントロールの影響は、推奨事項に関するページと、「[セキュリティ コントロールとその推奨事項](secure-score-security-controls.md#security-controls-and-their-recommendations)」のコントロール リストで明確に示されています。

その導入以降、 **[Azure Defender を有効にする]** コントロールの考えられる最大スコアは 0 ポイントとなりました。 **この変更により、コントロールはセキュア スコアに影響するようになります**。

Azure Defender を有効にすると、Security Center の無料モードの機能を、プライベートおよび他のパブリック クラウドで実行されているワークロードまで拡張し、ハイブリッド クラウド ワークロード全体に統合されたセキュリティ管理と脅威防止機能を提供することになります。 Azure Defender の主な機能には、サーバーの統合された Microsoft Defender for Endpoint ライセンス、仮想マシンとコンテナー レジストリの脆弱性スキャン、高度な行動分析と機械学習に基づくセキュリティ アラート、およびコンテナーのセキュリティ機能などがあります。 完全なリストについては、「[Azure Security Center (無料) と有効化された Azure Defender](security-center-pricing.md)」を参照してください。

この変更により、Azure Defender で保護されていないサブスクリプションのセキュア スコアに影響があります。 スコアに影響しないようにするため、この変更が発生する前に Azure Defender を有効にすることをお勧めします。 

詳細については、「[クイックスタート: Azure Defender を有効にする](enable-azure-defender.md)」を参照してください。

### <a name="enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases"></a>SQL データベースで機密データを分類するための推奨事項の強化

**変更予定日:** 2022 年第 1 四半期

"**データ分類の適用**" セキュリティ コントロールにおける推奨事項 "**SQL データベースの機密データを分類する必要がある**" は、より Microsoft のデータ分類戦略に沿った新しいバージョンで置き換えられる予定です。 これにより、推奨事項の ID も変更されます (現在は b0df6f56-862d-4730-8597-38c0fd4ebd59)。


## <a name="next-steps"></a>次のステップ

製品に対する最近のすべての変更については、「[Azure Security Center の最新情報](release-notes.md)」を参照してください。