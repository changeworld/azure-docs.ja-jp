---
title: パートナー センターの評価とレビューの分析ダッシュボード
description: Microsoft AppSource および Azure Marketplace でのオファーに関する顧客フィードバックの統合ビューにアクセスする方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 07/06/2021
ms.openlocfilehash: d1a4f749e883ecc184ac54e16a7eeb5d83686ad6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732732"
---
# <a name="ratings--reviews-analytics-dashboard-in-partner-center"></a>パートナー センターの評価とレビューの分析ダッシュボード

この記事では、パートナー センターの [評価とレビュー] ダッシュボードについて説明します。 このダッシュボードには、Microsoft AppSource および Azure Marketplace でのオファーに関する顧客フィードバックの統合ビューが表示されます。 顧客は、両方のマーケットプレースでオファーを参照、検索、および購入する際に、入手したオファーの評価やレビューを書き残すことができます。

- 顧客は、新しい評価やレビューを送信したり、送信した既存の評価やレビューを更新または削除したりすることができます。 顧客は、自分が所有する評価とレビューにのみ変更を加えることができます。  
- レビューは、Azure Marketplace または AppSource のオファーの製品表示ページにある [レビュー] タブに投稿されます。 顧客は、自分の名前を含めることも、匿名で投稿することもできます。  

>[!NOTE]
> 分析の用語の詳細な定義については、「[コマーシャル マーケットプレース分析に関するよく寄せられる質問と用語](analytics-faq.yml)」を参照してください。

## <a name="access-the-dashboard"></a>ダッシュボードにアクセスする

パートナー センターの [コマーシャル マーケットプレース ダッシュボード](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)で、 **[[分析]](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** セクションを展開し、 **[評価とレビュー]** を選択します。

ダッシュボードには、以下のカスタム アクティビティがグラフィカルに表示されます。

- 評価とレビュー  
- レビュー コメント

**Marketplace の分析情報** のタブを使用して、Microsoft AppSource と Azure Marketplace でのオファーのメトリックを個別に表示します。 特定のオファーのメトリックを表示するには、ドロップダウン リストからそのオファーを選択します。

### <a name="ratings--reviews-summary"></a>評価とレビューの概要

評価とレビューの概要セクションには、選択した日付範囲について以下のメトリックが表示されます。

- **平均評価:** 選択したオファーに対して顧客によって送信された全評価の加重平均星評価。
- **評価の内訳:** 評価を送信した顧客数別の星評価の内訳。 棒グラフは、実際の評価と改訂された評価 (更新された評価数) で積み上げられます。
- **合計評価:** 送信された評価の総数。 この数には、レビューがある評価とない評価も含まれます。
- **レビューがある評価:** 送信されたレビューの数。

:::image type="content" source="media/marketplace-publisher-guide-rating-reviews/analyze-ratings-summary.png" alt-text="パートナー センターの分析評価とレビューの概要を図示" lightbox="media/marketplace-publisher-guide-rating-reviews/analyze-ratings-summary.png":::

### <a name="review-comments"></a>レビュー コメント

レビューは、投稿された日時の順に表示されます。 既定のビューにはすべてのレビューが表示され、ドロップダウン メニューの **評価フィルター** を使用して、星評価でレビューをフィルター処理できます。 さらに、レビューに出現するキーワードで検索することもできます。  

:::image type="content" source="media/marketplace-publisher-guide-rating-reviews/analyze-reviews.png" alt-text="パートナー センターの分析レビュー コメントを図示" lightbox="media/marketplace-publisher-guide-rating-reviews/analyze-reviews.png":::

### <a name="responding-to-a-review"></a>レビューへの応答

ユーザーからのレビューに応答できます。応答は、Azure Marketplace または AppSource の店舗トップに表示されます。 この機能が適用されるプランの種類は、Azure アプリケーション、Azure Container、Azure 仮想マシン、Dynamics 365 Business Central、Dynamics 365 for Customer Engagement & Power Apps、Dynamics 365 Operations、IoT Edge モジュール、管理サービス、Power BI アプリ、サービスとしてのソフトウェアです。 レビューに応答するには、次の手順に従います。

1. **[評価とレビュー]** タブを選択し、 **[Azure Marketplace]** または **[AppSource]** を選択します。 **フィルター** を選択 してレビューの一覧を絞り込み、たとえば特定の星評価を持つレビューのみを表示できます

:::image type="content" source="media/marketplace-publisher-guide-rating-reviews/ratings-and-reviews.png" alt-text="AppSource での評価とレビューを示します" lightbox="media/marketplace-publisher-guide-rating-reviews/ratings-and-reviews.png":::
2. 応答するレビューの **[応答]** リンクを選択し、**テキスト ボックス** に返信を入力して、 **[返信の送信]** を選択します。

応答は、AppSource の製品詳細ページの元のレビューのテキストの下に表示され、Azure Marketplace オンラインの店舗トップに表示されます。

#### <a name="appsource"></a>AppSource

:::image type="content" source="media/marketplace-publisher-guide-rating-reviews/review-reply-appsource.png" alt-text="Appsource レビューの応答を図示" lightbox="media/marketplace-publisher-guide-rating-reviews/review-reply-appsource.png":::

#### <a name="azure-marketplace-online-store"></a>Azure Marketplace オンライン ストア

:::image type="content" source="media/marketplace-publisher-guide-rating-reviews/az-mp-online-store.png" alt-text="Azure Marketplace オンライン ストアの応答を図示" lightbox="media/marketplace-publisher-guide-rating-reviews/az-mp-online-store.png":::

### <a name="editing-or-deleting-a-response-to-a-review"></a>レビューへの応答を編集または削除する

レビューに対する応答を編集または削除するには、 **[編集]** または **[削除]** を選択します。

:::image type="content" source="media/marketplace-publisher-guide-rating-reviews/edit-or-delete-reply.png" alt-text="応答を編集または削除するオプションを図示":::

### <a name="contacting-users-after-a-review-has-been-posted"></a>レビューの投稿後にユーザーに連絡する

レビューを投稿するときに、ユーザーは発行元から連絡を受け取る同意を与えることができます。 ユーザーが同意すると、パートナー センター のレビューの上部に通知が表示され、レビューを投稿したユーザーの電子メール アドレスが表示されます。

:::image type="content" source="media/marketplace-publisher-guide-rating-reviews/contacting-consenting-customer.png" alt-text="同意する顧客への連絡を図示":::

## <a name="next-steps"></a>次のステップ

- オファーのマーケットプレース アクティビティを要約した集計データのグラフ、傾向、値については、「[コマーシャル マーケットプレース分析の概要ダッシュボード](summary-dashboard.md)」を参照してください。
- グラフィカルでダウンロード可能な形式での注文の詳細については、「[コマーシャル マーケットプレース分析の注文ダッシュボード](orders-dashboard.md)」を参照してください。
- 仮想マシン (VM) プランの使用量と従量制課金メトリックについては、「[コマーシャル マーケットプレース分析の使用量ダッシュボード](usage-dashboard.md)」を参照してください。
- 成長傾向など、顧客の詳細については、「[コマーシャル マーケットプレース分析の顧客ダッシュボード](customer-dashboard.md)」を参照してください。
- 過去 30 日間のダウンロード要求の一覧については、「[コマーシャル マーケットプレース分析のダウンロード ダッシュボード](downloads-dashboard.md)」を参照してください。
