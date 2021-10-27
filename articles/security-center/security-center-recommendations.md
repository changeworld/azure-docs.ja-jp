---
title: Security recommendations in Azure Security Center
description: このドキュメントでは、Azure Security Center での推奨事項に従ってご使用の Azure のリソースを保護し、セキュリティ ポリシーを使用してコンプライアンスを順守する方法について説明します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 186c155ea1ec4d69abde6147997fe4659a3e833f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003605"
---
# <a name="review-your-security-recommendations"></a>セキュリティの推奨事項を確認する

このトピックでは、Azure Security Center で推奨事項を確認し、理解し、Azure リソースの保護に役立てる方法について説明します。

## <a name="monitor-recommendations"></a>推奨事項の監視<a name="monitor-recommendations"></a>

Security Center はリソースのセキュリティの状態を分析して、潜在的な脆弱性を特定します。 

1. Security Center のメニューから **[推奨事項]** ページを開いて、お使いの環境に適用できる推奨事項を確認します。 推奨事項は、セキュリティ コントロールにグループ化されています。

    :::image type="content" source="./media/security-center-recommendations/view-recommendations.png" alt-text="セキュリティ コントロールにグループ化された推奨事項。" lightbox="./media/security-center-recommendations/view-recommendations.png":::

1. リソースの種類、重大度、環境、あるいは自分にとって重要なその他の条件に固有の推奨を見つけるには、推奨事項一覧に任意のフィルターを使用してください。

    :::image type="content" source="media/security-center-recommendations/recommendation-list-filters.png" alt-text="Azure Security Center 推奨事項一覧を絞り込むためのフィルター。":::

1. コントロールを展開し、特定の推奨事項を選択して [推奨事項の詳細] ページを表示します。

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="推奨事項の詳細ページ。" lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    このページには次の項目が含まれています。

    1. サポートされている推奨事項については、上部のツールバーに次のボタンのいずれか、またはすべてが表示されます。
        - **適用** および **拒否** (「[適用/拒否の推奨事項を使用した構成ミスの防止](prevent-misconfigurations.md)」を参照)。
        - 基になるポリシーの Azure Policy エントリに直接アクセスするための **ポリシー定義の表示**。
        - **クエリを開く** - すべての推奨事項には、Azure Resource Graph Explorer を使用して、影響を受けるリソースの詳細情報を表示するオプションがあります。
    1. **重大度インジケーター**。
    1. **更新間隔** (該当する場合)。
    1. この推奨事項に対して除外が存在する場合は、除外されたリソースの数を示す **除外されたリソースの数**。
    1. **説明** - セキュリティの問題に関する短い説明。
    1. 関連する場合、詳細ページには **関連する推奨事項** の表も含まれています。

        リレーションシップの種類は次のとおりです。

        - **前提条件** - 選択した推奨事項の前に完了する必要がある推奨事項
        - **代替** - 選択した推奨事項の目的を別の方法で達成する、異なる推奨事項
        - **依存** - 選択した推奨事項が前提条件となる推奨事項

        それぞれの関連推奨事項に対して、[影響を受けるリソース] 列に異常なリソースの数が表示されます。

        > [!TIP]
        > 関連推奨事項が淡色表示されている場合、その依存関係はまだ完了していないため、使用できません。

    1. **修正手順** - 影響を受けるリソースのセキュリティの問題を修正するために必要な手動の手順の説明。 **[修正]** オプション** を含む推奨事項の場合は、推奨される修正プログラムをリソースに適用する前に、 **[修正ロジックを表示]** を選択できます。
    1. **影響を受けるリソース** - リソースはタブにグループ化されています。
        - **正常なリソース** - 影響を受けていないか、既に問題を修正した関連するリソース。
        - **異常なリソース** – 特定された問題の影響を受けているリソース。
        - **適用されないリソース** – 推奨事項で明確な回答を得ることができないリソース。 [適用されないリソース] タブには、各リソースの理由も含まれています。 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="理由が表示されている [適用されないリソース] タブ。":::
    1. 推奨事項を修復するか、ロジック アプリをトリガーするアクション ボタン。


## <a name="review-recommendation-data-in-azure-resource-graph-explorer-arg"></a>Azure Resource Graph Explorer (ARG) で推奨事項データを確認する

推奨事項の詳細ページのツール バーには、 **[クエリを開く]** ボタンが表示され、[Azure Resource Graph (ARG)](../governance/resource-graph/index.yml) で詳細を確認できます。これは、複数のサブスクリプションにまたがって Security Center のセキュリティ態勢データのクエリを実行できる Azure サービスです。

ARG は、堅牢なフィルター処理、グループ化、並べ替え機能を使用して、クラウド環境全体で大規模にクエリを実行する機能を備え、効率的なリソース探索ができるように設計されています。 これは、Azure サブスクリプション全体の情報を、プログラムから、または Azure portal 内ですばやく効率的に照会する方法です。

[Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/) を使用すると、ASC データを他のリソース プロパティと相互参照できます。

たとえば、この推奨事項の詳細ページには、影響を受ける 15 のリソースが表示されます。

:::image type="content" source="./media/security-center-recommendations/open-query.png" alt-text="推奨事項の詳細ページの **[クエリを開く]** ボタン。":::

基になるクエリを開いて実行すると、Azure Resource Graph Explorer で、この推奨事項に対して同じ 15 のリソースとその正常性状態が表示されます。 

:::image type="content" source="./media/security-center-recommendations/run-query.png" alt-text="前のスクリーンショットで示された推奨事項の結果を表示する Azure Resource Graph Explorer。":::


## <a name="preview-recommendations"></a>推奨事項のプレビュー

**プレビュー** のフラグが設定されている推奨事項は、セキュリティ スコアの計算からは除外されます。

それでも、その推奨事項はプレビュー期間が終了した時点でスコアに反映されるため、可能な限り修復しておく必要があります。

プレビューの推奨事項の例を次に示します。

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="プレビュー フラグが設定された推奨事項。":::
 
## <a name="next-steps"></a>次のステップ

このドキュメントでは、Security Center のセキュリティに関する推奨事項について説明しました。 関連情報:

- [推奨事項の修正](security-center-remediate-recommendations.md) - Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明します。
- [適用/拒否の推奨事項を使用した構成ミスの防止](prevent-misconfigurations.md)。
- [Security Center トリガーへの応答の自動化](workflow-automation.md) - 推奨事項への応答を自動化します
- [推奨事項からリソースを除外する](exempt-resource.md)
- [セキュリティの推奨事項 - リファレンス ガイド](recommendations-reference.md)