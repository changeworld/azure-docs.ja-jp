---
title: Azure Security Center に対する今後の重要な変更
description: 知っておく必要があり、計画する必要がある Azure Security Center の今後の変更
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 06/13/2021
ms.author: memildin
ms.openlocfilehash: a490a08946a7357af41cce04051ef01765c8fbe5
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112062246"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Azure Security Center に対する今後の重要な変更

> [!IMPORTANT]
> このページの情報は、販売を開始する前に大幅に変更される可能性があるプレリリース製品または機能に関連しています。 Microsoft は、ここで提供される情報に対して、明示または黙示を問わず、一切の確約または保証を行わないものとします。

このページでは、Security Center に対して計画されている変更について説明します。 ここでは、セキュリティ スコアやワークフローなどに影響する可能性がある、製品に対して計画されている変更について説明します。

最新のリリース ノートをお探しの場合は、「[Azure Security Center の最新情報](release-notes.md)」をご覧ください。


## <a name="planned-changes"></a>計画されている変更

| 計画されている変更                                                                                                                                                        | 変更予定日 |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [ISO 27001 のレガシ実装を新しい ISO 27001:2013 で置き換え予定](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)          | 2021 年 6 月                 |
| [SQL データ分類の推奨事項を改善](#enhancements-to-sql-data-classification-recommendation)                                                     | 2021 年第 3 四半期                   |
| [[Azure Defender を有効にする] セキュリティ コントロールをセキュア スコアに含める](#enable-azure-defender-security-control-to-be-included-in-secure-score)                       | 2021 年第 3 四半期                   |
|                                                                                                                                                                       |                           |


### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>ISO 27001 のレガシ実装を新しい ISO 27001:2013 で置き換え予定

ISO 27001 のレガシ実装は Security Center の規制コンプライアンス ダッシュボードから削除されます。 Security Center で ISO 27001 へのコンプライアンスを追跡している場合は、関連するすべての管理グループまたはサブスクリプションに対して新しい ISO 27001:2013 標準をオンボードすると、現在のレガシ ISO 27001 が間もなくダッシュボードから削除されます。

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="ISO 27001 のレガシ実装の削除に関するメッセージが表示されている Security Center の規制コンプライアンス ダッシュボード。" lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="enhancements-to-sql-data-classification-recommendation"></a>SQL データ分類の推奨事項を改善

**変更予定日:** 2021 年第 3 四半期

"**データ分類の適用**" セキュリティ コントロールにおける推奨事項 "**SQL データベースの機密データを分類する必要がある**" は、より Microsoft のデータ分類戦略に沿った新しいバージョンで置き換えられる予定です。 これにより、推奨事項の ID も変更されます (現在は b0df6f56-862d-4730-8597-38c0fd4ebd59)。

### <a name="enable-azure-defender-security-control-to-be-included-in-secure-score"></a>[Azure Defender を有効にする] セキュリティ コントロールをセキュア スコアに含める

**変更予定日:** 2021 年第 3 四半期

Security Center のセキュリティ強化に関する推奨事項は、セキュリティ コントロールにグループ化されています。 各コントロールは、関連するセキュリティの推奨事項の論理グループであり、脆弱な攻撃対象領域を反映しています。 総合的なセキュア スコアに対する各セキュリティ コントロールの影響は、推奨事項に関するページと、「[セキュリティ コントロールとその推奨事項](secure-score-security-controls.md#security-controls-and-their-recommendations)」のコントロール リストで明確に示されています。

その導入以降、 **[Azure Defender を有効にする]** コントロールの考えられる最大スコアは 0 ポイントとなりました。 **この変更により、コントロールはセキュア スコアに影響するようになります**。

Azure Defender を有効にすると、Security Center の無料モードの機能を、プライベートおよび他のパブリック クラウドで実行されているワークロードまで拡張し、ハイブリッド クラウド ワークロード全体に統合されたセキュリティ管理と脅威防止機能を提供することになります。 Azure Defender の主な機能には、サーバーの統合された Microsoft Defender for Endpoint ライセンス、仮想マシンとコンテナー レジストリの脆弱性スキャン、高度な行動分析と機械学習に基づくセキュリティ アラート、およびコンテナーのセキュリティ機能などがあります。 完全なリストについては、「[Azure Security Center (無料) と有効化された Azure Defender](security-center-pricing.md)」を参照してください。

この変更により、Azure Defender で保護されていないサブスクリプションのセキュア スコアに影響があります。 スコアに影響しないようにするため、この変更が発生する前に Azure Defender を有効にすることをお勧めします。 

詳細については、「[クイックスタート: Azure Defender を有効にする](enable-azure-defender.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

製品に対する最近のすべての変更については、「[Azure Security Center の最新情報](release-notes.md)」を参照してください。