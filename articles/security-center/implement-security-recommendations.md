---
title: Microsoft Defender for Cloud でセキュリティに関する推奨事項を実装する | Microsoft Docs
description: この記事では、リソースを保護してセキュリティ ポリシーを満たすために、Microsoft Defender for Cloud で推奨事項に対応する方法について説明します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: d01a8f806154cdf0260f0dcf8c9f6c65386c99f6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437917"
---
# <a name="implement-security-recommendations-in-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud でセキュリティに関する推奨事項を実装する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

レコメンデーションは、リソースのセキュリティを強化する方法を提案します。 レコメンデーションに記載されている修復手順に従って、レコメンデーションを実装します。

## <a name="remediation-steps"></a>修復手順 <a name="remediation-steps"></a>

すべてのレコメンデーションを確認したら、最初に修復するレコメンデーションを決定します。 セキュリティ スコアを上げる可能性が最も高いセキュリティ制御を最優先にすることをお勧めします。

1. 一覧から、レコメンデーションを選択します。

1. 「**修復の手順**」セクションの指示に従ってください。 レコメンデーションごとに、独自の指示のセットがあります。 HTTPS 経由のトラフィックのみを許可するようにアプリケーションを構成するための修復手順のスクリーンショットを次に示します。

    :::image type="content" source="./media/implement-security-recommendations/security-center-remediate-recommendation.png" alt-text="レコメンデーションの手動修復手順。" lightbox="./media/implement-security-recommendations/security-center-remediate-recommendation.png":::

1. 完了すると、問題が解決されたかどうかを示す通知が表示されます。

## <a name="fix-button"></a>[修正プログラム] ボタン

修復を単純化し、環境のセキュリティを向上させる (そしてセキュリティ スコアを上げる) ため、多くのレコメンデーションには **修正プログラム** オプションが含まれています。

**修正プログラム** を使用すると、複数のリソースに関するレコメンデーションを迅速に修復できます。

> [!TIP]
> **修正プログラム** は、特定のレコメンデーションにのみ使用できます。 使用可能な修正プログラムがあるレコメンデーションを確認するには、レコメンデーションの一覧に対して **対応アクション** フィルターを使用します。
> 
> :::image type="content" source="media/implement-security-recommendations/quick-fix-filter.png" alt-text="レコメンデーション リストの上にあるフィルターを使用して、修正プログラム オプションがあるレコメンデーションを見つける。":::

**修正プログラム** を実装するには:

1. **[修正プログラム]** アクション アイコンが表示されているレコメンデーションの一覧 :::image type="icon" source="media/implement-security-recommendations/fix-icon.png" border="false"::: から、推奨事項を選択します。

    :::image type="content" source="./media/implement-security-recommendations/security-center-recommendations-fix-action.png" alt-text="修正プログラム アクションを含むレコメンデーションを強調表示するレコメンデーションのリスト" lightbox="./media/implement-security-recommendations/security-center-recommendations-fix-action.png":::

1. **[正常でないリソース]** タブで、レコメンデーションを実装するリソースを選択し、 **[修復]** を選択します。

    > [!NOTE]
    > 一覧表示されるリソースの一部は、変更するための適切なアクセス許可を持っていないために無効な場合があります。

1. 確認ボックスで、修復の詳細と影響を確認します。

    ![クイック修正。](./media/implement-security-recommendations/security-center-quick-fix-view.png)

    > [!NOTE]
    > 影響は、 **[修復]** をクリックした後に開かれる **[リソースの修復]** ウィンドウの灰色のボックスに表示されます。 ここには、**修正プログラム** を進めたときに実行される変更内容が表示されます。

1. 必要に応じて関連するパラメーターを挿入し、修復を承認します。

    > [!NOTE]
    > 修復が完了してから **[正常なリソース]** タブにリソースが表示されるまでに数分かかる場合があります。修復アクションを表示するには、[アクティビティ ログ](#activity-log)を確認します。

1. 完了すると、修復が成功したかどうかを知らせる通知が表示されます。

## <a name="fix-actions-logged-to-the-activity-log"></a>アクティビティ ログに記録された修正プログラム アクション<a name="activity-log"></a>

修復操作では、テンプレートのデプロイまたは REST API `PATCH` 要求を使用して、リソースに構成を適用します。 これらの操作は、[Azure のアクティビティ ログ](../azure-monitor/essentials/activity-log.md)に記録されます。


## <a name="next-steps"></a>次のステップ

このドキュメントでは、Defender for Cloud で推奨事項を修復する方法について説明しました。 環境に対して推奨事項が定義および選択される方法については、次のページをご覧ください。

- [セキュリティ ポリシー、イニシアティブ、および推奨事項とは](security-policy-concept.md)