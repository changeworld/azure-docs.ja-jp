---
title: コマーシャル マーケットプレース分析におけるパートナー センターの [注文] ダッシュボード | Microsoft AppSource および Azure Marketplace
description: コマーシャル マーケットプレース オファーの注文に関する、グラフィカルでダウンロード可能な形式の分析レポートにアクセスする方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 09/27/2021
ms.openlocfilehash: 85352f2cb1b2a0fe042b36fbe795435250385f65
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130246344"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>コマーシャル マーケットプレース分析での [注文] ダッシュボード

この記事では、パートナー センターの [注文] ダッシュボードについて説明します。 このダッシュボードには、増加傾向など、注文に関する情報が、グラフィカルかつダウンロード可能な形式で表示されます。

>[!NOTE]
> 分析の用語の詳細な定義については、「[コマーシャル マーケットプレース分析の用語とよく寄せられる質問](./analytics-faq.yml)」を参照してください。

## <a name="orders-dashboard"></a>[注文] ダッシュボード

<bpt id="p1">[</bpt>[注文] ダッシュボード<ept id="p1">](https://go.microsoft.com/fwlink/?linkid=2165914)</ept>には、すべてのサービスとしてのソフトウェア (SaaS) オファーの現在の注文が表示されます。 次の項目のグラフィカル表示を見ることができます。

- 注文数の傾向
- シートごと、およびサイトごとの注文数の傾向
- オファー別および SKU 別の注文数
- 地域別注文数
- [注文の詳細] テーブル
- [注文] ページのフィルター

> [!NOTE]
> 顧客の獲得からパートナー センターでの報告までの最大待ち時間は、48 時間です。

## <a name="access-the-orders-dashboard"></a>[注文] ダッシュボードにアクセスする

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

1. <bpt id="p1">[</bpt>パートナー センター<ept id="p1">](https://partner.microsoft.com/dashboard/home)</ept>にサインインします。
1. ホーム ページで、 **[分析情報]** タイルを選択します。

    [ ![パートナー センター ホーム ページの [分析情報] タイルの画像。](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. 左側のメニューで **[注文]** を選択します。

#### <a name="current-view"></a><bpt id="p1">[</bpt>現在のビュー<ept id="p1">](#tab/current-view)</ept>

1. <bpt id="p1">[</bpt>パートナー センター<ept id="p1">](https://partner.microsoft.com/dashboard/home)</ept>にサインインします。
1. 左側のナビゲーション メニューで、 **[商業マーケットプレース]**  >  **[分析]**  >  **[概要]** を選択します。

---

## <a name="elements-of-the-orders-dashboard"></a>[注文] ダッシュボードの要素

次のセクションでは、[注文] ダッシュボードの使用方法とデータの読み方について説明します。

### <a name="month-range"></a>月範囲

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

各ページの右上隅には、月範囲の選択が表示されます。 過去 6 か月または 12 か月の月範囲を選択するか、12 か月を最大期間とするカスタムの月範囲を選択して、 <bpt id="p1">**</bpt>[注文]<ept id="p1">**</ept> ページのグラフの出力をカスタマイズします。 既定の月範囲 (計算期間) は 6 か月です。

[ ![[注文] ダッシュボードの月フィルターの図。](./media/orders-dashboard/order-workspace-filters.png) ](./media/orders-dashboard/order-workspace-filters.png#lightbox)

#### <a name="current-view"></a><bpt id="p1">[</bpt>現在のビュー<ept id="p1">](#tab/current-view)</ept>

各ページの右上隅には、月範囲の選択が表示されます。 過去 6 か月または 12 か月の月範囲を選択するか、12 か月を最大期間とするカスタムの月範囲を選択して、 <bpt id="p1">**</bpt>[注文]<ept id="p1">**</ept> ページのグラフの出力をカスタマイズします。 既定の月範囲 (計算期間) は 6 か月です。

<bpt id="p1">:::image type="content" source="./media/orders-dashboard/month-filters.png" alt-text="</bpt>[注文] ダッシュボードの月フィルターの画像。<ept id=&quot;p1&quot;>":::</ept>

---

> [!NOTE]
> 視覚化ウィジェットおよびエクスポート レポートのすべてのメトリックでは、ユーザーが選択した計算期間が優先されます。

### <a name="orders-trend"></a>注文数の傾向

このセクションには <bpt id="p1">**</bpt>[注文]<ept id="p1">**</ept> グラフがあり、選択した計算期間でのアクティブな注文とキャンセルされた注文の傾向が示されます。 メトリックと増加傾向は折れ線グラフで表され、グラフの線にマウス ポインターを合わせると、各月の値が表示されます。 ウィジェットの注文メトリックの下にあるパーセント値は、選択した計算期間での増加量または減少量を表します。

<bpt id="p1">_</bpt>[Active]\(アクティブ\)<ept id="p1">_</ept> と <bpt id="p2">_</bpt>[Canceled]\(キャンセル\)<ept id="p2">_</ept> の 2 つの注文カウンターがあります。

- <bpt id="p1">**</bpt>[Active]\(アクティブ\)<ept id="p1">**</ept> は、選択した日付範囲内で顧客が行った注文の数を示します。
- <bpt id="p1">**</bpt>[Cancelled]\(キャンセル\)<ept id="p1">**</ept> は、以前購入された後、選択した日付範囲内でキャンセルされた注文の数を示します。

<bpt id="p1">[</bpt><ph id="ph1">![</ph>[注文] ダッシュボードの [注文] ウィジェットの画像。アクティブな注文とキャンセルされた注文の傾向が示されています。<ept id="p1">](./media/orders-dashboard/orders-trend.png)](./media/orders-dashboard/orders-trend.png#lightbox)</ept>

### <a name="orders-by-per-seat-and-site-trend"></a>シートごとおよびサイトごとの注文数の傾向

<bpt id="p1">**</bpt>[Orders by per seat and site-based]\(シートごとの注文数とサイト ベースの注文数\)<ept id="p1">**</ept> 折れ線グラフには、顧客が購入したサイトごとの SaaS 注文数とシートごとの SaaS 注文数のメトリックおよび傾向が示されます (このグラフには、キャンセルされた注文が含まれます)。

<bpt id="p1">[</bpt><ph id="ph1">![</ph>[注文] ダッシュボードの [注文] ウィジェットの画像。シートごとおよびサイトごとの注文数の傾向が示されています。<ept id="p1">](./media/orders-dashboard/seats-sites.png)](./media/orders-dashboard/seats-sites.png#lightbox)</ept>

SaaS オファーでは、各プランで 2 つの料金モデル、つまり、定額 (サイト ベース) とユーザーごと (シート ベース) のいずれかを使用できます。

- <bpt id="p1">**</bpt>定額<ept id="p1">**</ept>: 月額制または年額制の定額料金で、オファーを利用できるようにします。 これはサイトベースの価格とも呼ばれます。
- <bpt id="p1">**</bpt>ユーザーごと<ept id="p1">**</ept>: オファーを利用できる (つまり、シートを占有する) ユーザーの数に基づいた価格で、オファーを利用できるようにします。 この使用量ベースのモデルでは、プランでサポートするユーザーの最小数と最大数を設定できます。 複数のプランを作成して、ユーザーの数に基づいたさまざまな価格ポイントを構成できます。 これらのフィールドは省略可能です。 選択しない場合、ユーザー数は制限がないものとして見なされます (最小は 1、最大はサービスでサポートできる数)。 これらのフィールドは、プラン更新の一環として編集できます。
- <bpt id="p1">**</bpt>従量制課金<ept id="p1">**</ept>: 定額料金に上乗せ。 この価格モデルでは、必要に応じて、マーケットプレース測定サービス API を使用する従量制課金プランを定義して、定額料金の対象外となる使用量分を顧客に請求できます。

シート、サイト、および従量ベースの課金について詳しくは、「<bpt id="p1">[</bpt>コマーシャル マーケットプレースの SaaS オファーを計画する方法<ept id="p1">](plan-saas-offer.md)</ept>」を参照してください。

### <a name="orders-by-offers-and-skus"></a>オファー別および SKU 別の注文数

[Orders by Offers and SKU]\(オファー別および SKU 別の注文数\) グラフでは、すべてのオファーの測定値と傾向が示されます。

- 上位のオファーがグラフに表示され、残りのオファーは <bpt id="p1">**</bpt>[Rest All]\(残りすべて\)<ept id="p1">**</ept> としてグループ化されます。
- 凡例で特定のオファーを選択して、そのオファーと関連する SKU のみをグラフに表示できます。
- グラフのスライスにポインターを合わせると、注文数とそのオファーの割合が、すべてのオファーの合計注文数と比較して表示されます。
- <bpt id="p1">**</bpt>オファーごとの注文の傾向<ept id="p1">**</ept>には、月単位での増加傾向が表示されます。 月の列はオファー名別の注文数を表します。 折れ線グラフでは、増加率の傾向を Z 軸にプロットして表示します。

<bpt id="p1">:::image type="content" source="./media/orders-dashboard/orders-by-offers.png" alt-text="</bpt>[注文] ダッシュボードの [オファーごとの注文数] グラフの画像。<ept id=&quot;p1&quot;>":::</ept>

オファーを選択し、そのオファーの SKU を最大 3 つ選択することで、オファー、SKU、およびシートの前月比の傾向を表示できます。

### <a name="orders-by-geography"></a>地域別注文数

このヒートマップには、選択した計算期間での注文の合計数と、地域に対して新たに追加された注文の増加率が表示されます。  マップ上の明るい色は顧客数が少ないことを表し、暗い色は顧客数が多いことを表します。 テーブル内のレコードを選択すると、特定の国や地域にズームインできます。

<bpt id="p1">[</bpt><ph id="ph1">![</ph>[注文] ダッシュボードの [Geographical spread]\(地理的分布\) グラフの画像。<ept id="p1">](./media/orders-dashboard/geographical-spread.png)](./media/orders-dashboard/geographical-spread.png#lightbox)</ept>

次のことを考慮してください。

- マップを動かして正確な場所を見ることができます。
- 特定の場所にズーム インできます。
- このヒートマップには、特定の場所での顧客数、注文数、正規化された使用量 (時間) の詳細を表示するための補助グリッドがあります。
- グリッドで国またはリージョンを検索して選択すると、マップ内の場所にズームできます。 地図内の **[ホーム]** ボタンを選択すると、元のビューに戻ります。

### <a name="orders-details-table"></a>[注文の詳細] テーブル

注文詳細テーブルには、購入日で並べ替えた上位 1,000 件の注文の番号付きリストが表示されます。

- グリッドの各列で並べ替えることができます。
- レコード数が 1,000 件未満の場合は、.CSV または .TSV ファイルにデータを抽出できます。
- レコード数が 1,000 件を超える場合、エクスポートされたデータは今後 30 日間、ダウンロード ページに非同期で配置されます。
- <bpt id="p1">**</bpt>[注文の詳細]<ept id="p1">**</ept> テーブルにフィルターを適用すると、目的のデータのみ表示されます。 国および地域、Azure ライセンスの種類、コマーシャル マーケットプレース ライセンスの種類、オファーの種類、注文の状態、無料評価版、コマーシャル マーケットプレース サブスクリプション ID、顧客 ID、および会社名でフィルター処理します。
- 保護された顧客が注文を行った場合、 <bpt id="p1">**</bpt>[Orders Detailed Data]\(注文の詳細データ\)<ept id="p1">**</ept> 内の情報はマスクされます (************)。

<bpt id="p1">***</bpt>表 1: データ用語の辞書<ept id="p1">***</ept>

| 列名<br>ユーザー インターフェイス | 属性名 | 定義 | プログラムによるアクセス<br>レポートの列名 |
| ------------ | ------------- | ------------- | ------------- |
| Marketplace サブスクリプション ID | Marketplace サブスクリプション ID | 顧客がコマーシャル マーケットプレース オファーを購入するときに使用した Azure サブスクリプションに関連付けられている一意識別子。 インフラストラクチャ オファーの場合、これは顧客の Azure サブスクリプション GUID です。 Saas オファーでは、SaaS の購入に Azure サブスクリプションが必要ないため、これはゼロとして表示されます。 | Marketplace サブスクリプション ID |
| MonthStartDate | 月の開始日 | 月の開始日は、購入の月を表します。 形式は yyyy-mm-dd です。 | MonthStartDate |
| プランの種類 | プランの種類 | コマーシャル マーケットプレース オファリングの種類。 | OfferType |
| Azure ライセンスの種類 | Azure ライセンスの種類 | Azure を購入した顧客が使用するライセンス契約の種類。 チャネルとも呼ばれます。 設定できる値は次のとおりです。<ul><li><bpt id="p1">[</bpt>クラウド ソリューション プロバイダー<ept id="p1">](cloud-solution-providers.md)</ept></li><li>エンタープライズ</li><li>リセラーを通じたエンタープライズ</li><li>従量課金制</li><li>GTM</li></ul> | AzureLicenseType |
| Marketplace ライセンスの種類 | Marketplace ライセンスの種類 | コマーシャル マーケットプレース オファーの課金方法。 各値を次に示します。<ul><li>[クラウド ソリューション プロバイダー](cloud-solution-providers.md) (CSP)</li><li>エンタープライズ (EA)</li><li>リセラーを通じたエンタープライズ</li><li>従量課金制</li><li>[市場投入](co-sell-overview.md) (GTM)</li></ul> | MarketplaceLicenseType |
| SKU | SKU | オファーに関連付けられたプラン | SKU |
| 顧客の国 | 顧客の国および地域 | 顧客が指定した国や地域の名前。 国または地域は、顧客の Azure サブスクリプションの国またはリージョン地域とは異なる場合があります。 | CustomerCountry |
| プレビュー SKU | プレビュー SKU | この値により、SKU に "プレビュー" とタグ付けしたかどうかがわかります。 SKU がそれに応じてタグ付けされている場合、この値は "はい" になります。このイメージをデプロイして使用できるのは、認証した Azure サブスクリプションのみです。 SKU が "プレビュー" と識別定されていない場合、この値は "いいえ" になります。 | IsPreviewSKU |
| 注文 ID | 注文 ID | コマーシャル マーケットプレース サービスの顧客の注文の一意識別子。 仮想マシンの使用量ベースのプランは、注文に関連付けられていません。 | OrderId |
| Order Quantity | Order Quantity | アクティブな注文の注文 ID に関連付けられた資産の数 | OrderQuantity |
| クラウド インスタンス名 | クラウド インスタンス名 | VM のデプロイが存在する Microsoft Cloud。 | CloudInstanceName |
| 新しい顧客 | 新しい顧客 | この値は、新しい顧客が 1 つ以上のオファーを初めて購入したかどうかを示します。 "取得日" と同じ月内の場合、この値は "はい" です。 報告された月よりも前に顧客がいずれかのオファーを購入した場合、値は "いいえ" になります。 | IsNewCustomer |
| 注文の状態 | 注文の状態 | データが最後に更新された時点でのコマーシャル マーケットプレースの注文の状態。 | OrderStatus |
| 注文のキャンセル日 | 注文のキャンセル日 | コマーシャル マーケットプレースの注文がキャンセルされた日付。 | OrderCancelDate |
| 顧客の会社名 | 顧客の会社名 | 顧客が指定した会社名。 名前は、顧客の Azure サブスクリプションの名前とは異なる場合があります。 | CustomerCompanyName |
| 注文の発注日 | 注文の発注日 | コマーシャル マーケットプレースの注文が作成された日付。 形式は yyyy-mm-dd です。 | OrderPurchaseDate |
| プラン名 | プラン名 | コマーシャル マーケットプレース オファリングの名前。 | OfferName |
| 評価版の終了日 | 評価版の終了日 | この注文の試用期間の終了予定日または終了日。 | TrialEndDate |
| Customer ID | Customer ID | 顧客に割り当てられている一意の識別子。 顧客は 0 個以上の Azure Marketplace サブスクリプションを所持できます。 | CustomerId |
| 課金アカウント ID | 課金アカウント ID | 請求書が生成されるアカウントの識別子。 **課金アカウント ID** を **customerID** にマップして、支払い取引レポートを、顧客、注文、使用状況のレポートに関連付けます。 | BillingAccountId |
| AssetCount | 資産数 | 注文 ID に関連付けられた資産の数。 | 非推奨 |
| 使用不可 * | TermStartDate | 注文の期間の開始日を示します。 | TermStartDate |
| 使用不可 * | TermEndDate | 注文の期間の終了日を示します。 | TermEndDate |
| 使用不可 * | purchaseRecordId | 注文に関連する購入レコードの識別子。 | purchaseRecordId |
| 使用不可 * | purchaseRecordLineItemId | この注文に関連する購入レコードの品目の識別子。 | purchaseRecordLineItemId |
| 使用不可 * | EstimatedCharges | 課税前にすべての注文単位に対して顧客に請求される価格。 内税方式の国では、この価格に税金が含まれますが、それ以外の場合は含まれません。 | EstimatedCharges |
| 使用不可 * | 通貨 | オファーの BillingCurrency | 通貨 |
| 使用不可 * | HasTrial | オファーで試用期間が有効になっているかどうかを表します。 | HasTrial |
|||||

<ph id="ph1">`*`</ph> これらの新しいフィールドは現在、ISVOrderV2 データセットで提供されており、プログラムでアクセスできます。

### <a name="orders-page-filters"></a>[注文] ページのフィルター

<bpt id="p1">**</bpt>[注文]<ept id="p1">**</ept> ページのフィルターは、[注文] ページ レベルで適用されます。 1 つ以上のフィルターを選択して、選択した表示条件に合うグラフを表示することや、[Detailed orders data]\(詳細な注文データ\) グリッド/エクスポートで目的のデータを表示することができます。 フィルターは、[注文] ページの右上隅で選択した月範囲に対して抽出されたデータに適用されます。

> [!TIP]
> ウィジェットの右上隅にあるダウンロード アイコンを使用して、データをダウンロードできます。 "サムズアップ" アイコンまたは "サムズダウン" アイコンをクリックすることで、各ウィジェットに関するフィードバックを提供できます。

## <a name="next-steps"></a>次のステップ

- コマーシャル マーケットプレースで利用可能な分析レポートの概要については、「[パートナー センターでのコマーシャル マーケットプレース向け分析レポートにアクセスする](analytics.md)」を参照してください。
- オファーのマーケットプレース アクティビティを要約した集計データのグラフ、傾向、値については、「[コマーシャル マーケットプレース分析の概要ダッシュボード](./summary-dashboard.md)」を参照してください。
- グラフィカルでダウンロード可能な形式での注文の詳細については、「[コマーシャル マーケットプレース分析の注文ダッシュボード](orders-dashboard.md)」を参照してください。
- 仮想マシン (VM) プランの使用量と従量制課金メトリックについては、「[コマーシャル マーケットプレース分析の使用量ダッシュボード](./usage-dashboard.md)」を参照してください。
- 過去 30 日間のダウンロード要求の一覧については、「[コマーシャル マーケットプレース分析のダウンロード ダッシュボード](downloads-dashboard.md)」を参照してください。
- Azure Marketplace と AppSource でのオファーに関する顧客からのフィードバックを統合して表示する方法については、「<bpt id="p1">[</bpt>パートナー センターの評価とレビューの分析ダッシュボード<ept id="p1">](ratings-reviews.md)</ept>」を参照してください。
- コマーシャル マーケットプレース分析についてよく寄せられる質問と、データ用語の包括的な辞書については、「[コマーシャル マーケットプレース分析の用語とよく寄せられる質問](./analytics-faq.yml)」を参照してください。
