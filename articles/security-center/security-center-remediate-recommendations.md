---
title: Azure Security Center でレコメンデーションを修復する | Microsoft Docs
description: この記事では、リソースを保護してセキュリティ ポリシーを順守するために、Azure Security Center でレコメンデーションを修復する方法について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: 3d4b968d0d5e091d72cf718d58b11ade68ddf8c6
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637090"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Azure Security Center の修復レコメンデーション

レコメンデーションは、リソースのセキュリティを強化する方法を提案します。 レコメンデーションに記載されている修復手順に従って、レコメンデーションを実装します。

## <a name="remediation-steps"></a>修復手順 <a name="remediation-steps"></a>

すべてのレコメンデーションを確認したら、最初に修復するレコメンデーションを決定します。 [セキュリティ スコアの影響](security-center-recommendations.md#monitor-recommendations)を使用して、最初にすることの優先順位を決めることをお勧めします。

1. 一覧から、レコメンデーションをクリックします。

1. 「**修復の手順**」セクションの指示に従ってください。 レコメンデーションごとに、独自の指示のセットがあります。 HTTPS 経由のトラフィックのみを許可するようにアプリケーションを構成するための修復手順のスクリーンショットを次に示します。

    ![推奨事項の詳細](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. 完了すると、修復が成功したかどうかを知らせる通知が表示されます。

## <a name="quick-fix-remediation"></a>クイック修復のレコメンデーション<a name="one-click"></a>

クイック修復によって修復が簡単になり、セキュリティ スコアをすばやく向上させ、環境内のセキュリティを強化することができます。

クイック修復を使用すると、複数のリソースに関するレコメンデーションを迅速に修復できます。 

> [!TIP]
> クイック修復は、特定のレコメンデーションにのみ使用できます。 クイック修正オプションがあるレコメンデーションを確認するには、レコメンデーションの一覧の一番上にある専用フィルターを使用します。
> 
> :::image type="content" source="media/security-center-remediate-recommendations/quick-fix-filter.png" alt-text="レコメンデーション リストの上にあるフィルターを使用して、クイック修正オプションがあるレコメンデーションを見つける":::

クイック修復レコメンデーションを実装するには、次の手順に従います。

1. **クイック修復** ラベルを含むレコメンデーションの一覧から レコメンデーションをクリックします。

    [![クイック修復を選択します](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)。

1. **[正常でないリソース]** タブで、推奨事項を実装するリソースを選択し、 **[修復]** をクリックします。

    > [!NOTE]
    > 一覧表示されるリソースの一部は、変更するための適切なアクセス許可を持っていないために無効な場合があります。

1. 確認ボックスで、修復の詳細と影響を確認します。

    ![クイック修復](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > 影響は、 **[修復]** をクリックした後に開かれる **[リソースの修復]** ウィンドウの灰色のボックスに表示されます。 ここには、クイック修復を進めたときに実行される変更内容が表示されます。

1. 必要に応じて関連するパラメーターを挿入し、修復を承認します。

    > [!NOTE]
    > 修復が完了してから **[正常なリソース]** タブにリソースが表示されるまでに数分かかる場合があります。修復アクションを表示するには、[アクティビティ ログ](#activity-log)を確認します。

1. 完了すると、修復が成功したかどうかを知らせる通知が表示されます。

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>アクティビティ ログでのクイック修復のログ<a name="activity-log"></a>

修復操作では、テンプレートのデプロイまたは REST PATCH API の呼び出しを使用して、リソースに構成を適用します。 これらの操作は、[Azure のアクティビティ ログ](../azure-resource-manager/management/view-activity-logs.md)に記録されます。


## <a name="next-steps"></a>次のステップ

このドキュメントでは、Security Center でレコメンデーションを修復する方法について説明しました。 Security Center の詳細については、次のトピックを参照してください。

* 「[Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md)」 - Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* 「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」 - Azure リソースの正常性を監視する方法について説明しています。
