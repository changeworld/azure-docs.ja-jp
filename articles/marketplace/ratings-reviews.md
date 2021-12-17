---
title: 商業マーケットプレースの [評価とレビュー] ダッシュボード
description: Microsoft AppSource および Azure Marketplace でのオファーに関する顧客フィードバックの統合ビューにアクセスする方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 09/27/2021
ms.openlocfilehash: df8d3d610262d9d544b9161c14d790a54d00f3cc
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129082644"
---
# <a name="ratings-and-reviews-dashboard-in-commercial-marketplace-analytics"></a>商業マーケットプレース分析の [評価とレビュー] ダッシュボード

この記事では、パートナー センターの [評価とレビュー] ダッシュボードについて説明します。 このダッシュボードには、Microsoft AppSource および Azure Marketplace でのオファーに関する顧客フィードバックの統合ビューが表示されます。 顧客は、両方のマーケットプレースでオファーを参照、検索、および購入する際に、入手したオファーの評価やレビューを書き残すことができます。

- 顧客は、新しい評価やレビューを送信したり、送信した既存の評価やレビューを更新または削除したりすることができます。 顧客は、自分が所有する評価とレビューにのみ変更を加えることができます。  
- レビューは、Azure Marketplace または AppSource のオファーの製品表示ページにある [レビュー] タブに投稿されます。 顧客は、自分の名前を含めることも、匿名で投稿することもできます。  

>[!NOTE]
> 分析の用語の詳細な定義については、「<bpt id="p1">[</bpt>コマーシャル マーケットプレース分析に関するよく寄せられる質問と用語<ept id="p1">](analytics-faq.yml)</ept>」を参照してください。

## <a name="access-the-ratings--reviews-dashboard"></a>[評価とレビュー] ダッシュボードにアクセスする

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

1. <bpt id="p1">[</bpt>パートナー センター<ept id="p1">](https://partner.microsoft.com/dashboard/home)</ept>にサインインします。
1. ホーム ページで、 **[分析情報]** タイルを選択します。

    [ ![パートナー センター ホーム ページの [分析情報] タイルの画像。](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. 左側のメニューで、 **[評価とレビュー]** を選択します。

#### <a name="current-view"></a><bpt id="p1">[</bpt>現在のビュー<ept id="p1">](#tab/current-view)</ept>

パートナー センターの<bpt id="p1">[</bpt>コマーシャル マーケットプレース ダッシュボード<ept id="p1">](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)</ept>で、 <bpt id="p2">**</bpt><bpt id="p3">[</bpt>[分析]<ept id="p3">](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)</ept><ept id="p2">**</ept> セクションを展開し、 <bpt id="p4">**</bpt>[評価とレビュー]<ept id="p4">**</ept> を選択します。

---

ダッシュボードには、以下のカスタム アクティビティがグラフィカルに表示されます。

- Ratings  
- レビュー コメント

**[Marketplace Insights]\(Marketplace の分析情報\)** タブを使用して、Microsoft AppSource と Azure Marketplace のプランのメトリックを個別に表示します。 特定のオファーのメトリックを表示するには、ドロップダウン リストからそのオファーを選択します。

### <a name="ratings-and-reviews-summary"></a>評価とレビューの概要

概要セクションには、選択した日付範囲に関する以下のメトリックが表示されます。

- <bpt id="p1">**</bpt>平均評価:<ept id="p1">**</ept> 選択したオファーに対して顧客によって送信された全評価の加重平均星評価。
- <bpt id="p1">**</bpt>評価の内訳:<ept id="p1">**</ept> 評価を送信した顧客数別の星評価の内訳。 棒グラフは、実際の評価と改訂された評価 (更新された評価数) で積み上げられます。
- <bpt id="p1">**</bpt>合計評価:<ept id="p1">**</ept> 送信された評価の総数。 この数には、レビューがある評価とない評価も含まれます。
- <bpt id="p1">**</bpt>レビューがある評価:<ept id="p1">**</ept> 送信されたレビューの数。

<bpt id="p1">:::image type="content" source="media/marketplace-publisher-guide-rating-reviews/analyze-ratings-summary.png" alt-text="</bpt>パートナー センターの分析評価とレビューの概要を図示<ept id=&quot;p1&quot;>" lightbox="media/marketplace-publisher-guide-rating-reviews/analyze-ratings-summary.png":::</ept>

### <a name="review-comments"></a>レビュー コメント

レビューは、投稿された日時の順に表示されます。 既定のビューにはすべてのレビューが表示され、ドロップダウン メニューの<bpt id="p1">**</bpt>評価フィルター<ept id="p1">**</ept>を使用して、星評価でレビューをフィルター処理できます。 さらに、レビューに出現するキーワードで検索することもできます。  

<bpt id="p1">:::image type="content" source="media/marketplace-publisher-guide-rating-reviews/analyze-reviews.png" alt-text="</bpt>パートナー センターの分析レビュー コメントを図示<ept id=&quot;p1&quot;>" lightbox="media/marketplace-publisher-guide-rating-reviews/analyze-reviews.png":::</ept>

### <a name="respond-to-a-review"></a>レビューに応答する

ユーザーからのレビューに応答できます。応答は、Azure Marketplace または AppSource の店舗トップに表示されます。 この機能が適用されるプランの種類は、Azure アプリケーション、Azure Container、Azure 仮想マシン、Dynamics 365 Business Central、Dynamics 365 for Customer Engagement & Power Apps、Dynamics 365 Operations、IoT Edge モジュール、管理サービス、Power BI アプリ、サービスとしてのソフトウェアです。

レビューに応答するには、次の手順に従います。

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

1. **[評価とレビュー]** ページで、 **[Azure Marketplace]** または **[AppSource]** を選択します。 **フィルター** を選択してレビューの一覧を絞り込み、たとえば特定の星評価を持つレビューのみを表示できます。

    [![[評価とレビュー] ページの画像。](media/marketplace-publisher-guide-rating-reviews/ratings-and-reviews-workspace.png)](media/marketplace-publisher-guide-rating-reviews/ratings-and-reviews-workspace.png#lightbox)

1. 応答するレビューの <bpt id="p1">**</bpt>[応答]<ept id="p1">**</ept> リンクを選択し、<bpt id="p2">**</bpt>テキスト ボックス<ept id="p2">**</ept>に返信を入力して、 <bpt id="p3">**</bpt>[返信の送信]<ept id="p3">**</ept> を選択します。

応答は、AppSource の製品詳細ページの元のレビューのテキストの下に表示され、Azure Marketplace オンラインの店舗トップに表示されます。

#### <a name="current-view"></a><bpt id="p1">[</bpt>現在のビュー<ept id="p1">](#tab/current-view)</ept>

1. **[評価とレビュー]** ページで、 **[Azure Marketplace]** または **[AppSource]** を選択します。 <bpt id="p1">**</bpt>フィルター<ept id="p1">**</ept>を選択 してレビューの一覧を絞り込み、たとえば特定の星評価を持つレビューのみを表示できます

    <bpt id="p1">:::image type="content" source="media/marketplace-publisher-guide-rating-reviews/ratings-and-reviews.png" alt-text="</bpt>AppSource での評価とレビューを示します<ept id=&quot;p1&quot;>" lightbox="media/marketplace-publisher-guide-rating-reviews/ratings-and-reviews.png":::</ept>

1. 応答するレビューの <bpt id="p1">**</bpt>[応答]<ept id="p1">**</ept> リンクを選択し、<bpt id="p2">**</bpt>テキスト ボックス<ept id="p2">**</ept>に返信を入力して、 <bpt id="p3">**</bpt>[返信の送信]<ept id="p3">**</ept> を選択します。

応答は、AppSource の製品詳細ページの元のレビューのテキストの下に表示され、Azure Marketplace オンラインの店舗トップに表示されます。

---

#### <a name="appsource"></a>AppSource

:::image type="content" source="media/marketplace-publisher-guide-rating-reviews/review-reply-appsource.png" alt-text="AppSource のレビュー応答の画像" lightbox="media/marketplace-publisher-guide-rating-reviews/review-reply-appsource.png":::

#### <a name="azure-marketplace-online-store"></a>Azure Marketplace オンライン ストア

<bpt id="p1">:::image type="content" source="media/marketplace-publisher-guide-rating-reviews/az-mp-online-store.png" alt-text="</bpt>Azure Marketplace オンライン ストアの応答を図示<ept id=&quot;p1&quot;>" lightbox="media/marketplace-publisher-guide-rating-reviews/az-mp-online-store.png":::</ept>

### <a name="editing-or-deleting-a-response-to-a-review"></a>レビューへの応答を編集または削除する

レビューに対する応答を編集または削除するには、 <bpt id="p1">**</bpt>[編集]<ept id="p1">**</ept> または <bpt id="p2">**</bpt>[削除]<ept id="p2">**</ept> を選択します。

<bpt id="p1">:::image type="content" source="media/marketplace-publisher-guide-rating-reviews/edit-or-delete-reply.png" alt-text="</bpt>応答を編集または削除するオプションを図示<ept id=&quot;p1&quot;>":::</ept>

### <a name="contacting-users-after-a-review-has-been-posted"></a>レビューの投稿後にユーザーに連絡する

レビューを投稿するときに、ユーザーは発行元から連絡を受け取る同意を与えることができます。 ユーザーが同意すると、パートナー センター のレビューの上部に通知が表示され、レビューを投稿したユーザーの電子メール アドレスが表示されます。

<bpt id="p1">:::image type="content" source="media/marketplace-publisher-guide-rating-reviews/contacting-consenting-customer.png" alt-text="</bpt>同意する顧客への連絡を図示<ept id=&quot;p1&quot;>":::</ept>

## <a name="next-steps"></a>次のステップ

- オファーのマーケットプレース アクティビティを要約した集計データのグラフ、傾向、値については、「<bpt id="p1">[</bpt>コマーシャル マーケットプレース分析の概要ダッシュボード<ept id="p1">](summary-dashboard.md)</ept>」を参照してください。
- グラフィカルでダウンロード可能な形式での注文の詳細については、「<bpt id="p1">[</bpt>コマーシャル マーケットプレース分析の注文ダッシュボード<ept id="p1">](orders-dashboard.md)</ept>」を参照してください。
- 仮想マシン (VM) プランの使用量と従量制課金メトリックについては、「<bpt id="p1">[</bpt>コマーシャル マーケットプレース分析の使用量ダッシュボード<ept id="p1">](usage-dashboard.md)</ept>」を参照してください。
- 成長傾向など、顧客の詳細については、「<bpt id="p1">[</bpt>コマーシャル マーケットプレース分析の顧客ダッシュボード<ept id="p1">](customer-dashboard.md)</ept>」を参照してください。
- 過去 30 日間のダウンロード要求の一覧については、「<bpt id="p1">[</bpt>コマーシャル マーケットプレース分析のダウンロード ダッシュボード<ept id="p1">](downloads-dashboard.md)</ept>」を参照してください。
