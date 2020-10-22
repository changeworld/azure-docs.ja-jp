---
title: Security recommendations in Azure Security Center
description: このドキュメントでは、Azure セキュリティ センターでの推奨事項に従ってご使用の Azure のリソースを保護し、セキュリティ ポリシーを使用してコンプライアンスを順守する方法について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 0efb2c26a63418835dd2b7561a1fb9d3a9d5fee8
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945561"
---
# <a name="security-recommendations-in-azure-security-center"></a>Security recommendations in Azure Security Center 
このトピックでは、Azure セキュリティ センターで推奨事項を確認し、理解し、Azure リソースの保護に役立てる方法について説明します。


## <a name="what-are-security-recommendations"></a>セキュリティに関する推奨事項とは

推奨事項とは、リソースを保護するために実行する操作です。

Security Center では、Azure リソースのセキュリティの状態が定期的に分析されて、潜在的なセキュリティ脆弱性が特定されます。 その後、これらの脆弱性を修正する方法に関する推奨事項が提供されます。

それぞれの推奨事項の内容は次のとおりです。

- 問題の簡単な説明
- 推奨事項を実装するために実行する修復手順
- 影響を受けるリソース

## <a name="monitor-recommendations"></a>推奨事項の監視<a name="monitor-recommendations"></a>

Security Center はリソースのセキュリティの状態を分析して、潜在的な脆弱性を特定します。 

1. Security Center のメニューから **[推奨事項]** ページを開いて、お使いの環境に適用できる推奨事項を確認します。 推奨事項は、セキュリティ コントロールにグループ化されています。

      ![セキュリティ コントロールにグループ化された推奨事項](./media/security-center-recommendations/view-recommendations.png)

1. コントロールを展開し、特定の推奨事項を選択して [推奨事項の詳細] ページを表示します。

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="推奨事項の詳細ページ。" lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    このページには次の項目が含まれています。

    1. サポートされている推奨事項の **[適用]** と **[拒否]** ボタン (「[適用/拒否の推奨事項を使用した構成ミスの防止](prevent-misconfigurations.md)」を参照)
    1. **重大度インジケーター**
    1. **更新間隔** (該当する場合) 
    1. **説明** - 問題の簡単な説明
    1. **修正手順** - 影響を受けるリソースのセキュリティの問題を修正するために必要な手動の手順の説明。 "クイック修正" を含む推奨事項の場合は、推奨される修正プログラムをリソースに適用する前に、 **[修正ロジックを表示]** を選択することができます。 
    1. **影響を受けるリソース** - リソースはタブにグループ化されています。
        - **正常なリソース** - 影響を受けていないか、既に問題を修正した関連するリソース。
        - **異常なリソース** – 特定された問題の影響を受けているリソース。
        - **適用されないリソース** – 推奨事項で明確な回答を得ることができないリソース。 [適用されないリソース] タブには、各リソースの理由も含まれています。 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="推奨事項の詳細ページ。":::
    1. 推奨事項を修復するか、ロジック アプリをトリガーするアクション ボタン。

## <a name="preview-recommendations"></a>推奨事項のプレビュー

**プレビュー**のフラグが設定されている推奨事項は、セキュリティ スコアの計算からは除外されます。

それでも、その推奨事項はプレビュー期間が終了した時点でスコアに反映されるため、可能な限り修復しておく必要があります。

プレビューの推奨事項の例を次に示します。

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="推奨事項の詳細ページ。":::
 
## <a name="next-steps"></a>次のステップ

このドキュメントでは、セキュリティ センターのセキュリティに関する推奨事項について説明しました。 推奨事項を修正する方法については、次を参照してください。

- [推奨事項の修正](security-center-remediate-recommendations.md) -- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明します。
- [適用/拒否の推奨事項を使用した構成ミスの防止](prevent-misconfigurations.md)。
