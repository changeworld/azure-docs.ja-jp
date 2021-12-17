---
title: Azure の使用状況の概要の詳細を表示し、直接 EA 加入契約に関するレポートをダウンロードする
description: この記事では、直接 Enterprise Agreement (EA) 加入契約のエンタープライズ管理者が、その使用状況データ、使用された Azure 前払い、その他の使用状況に関連した料金の概要を Azure portal に表示する方法について説明します。
author: bandersmsft
ms.author: banders
ms.date: 11/16/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: sapnakeshari
ms.openlocfilehash: be9040fea81a1a8980874ebd5ec125f170b1bf42
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718547"
---
# <a name="view-your-usage-summary-details-and-download-reports-for-direct-ea-enrollments"></a>使用状況の概要の詳細を表示し、直接 EA 加入契約に関するレポートをダウンロードする

この記事では、直接 Enterprise Agreement (EA) 加入契約のエンタープライズ管理者が、その使用状況データ、使用された Azure 前払い、その他の使用状況に関連した料金の概要を Azure portal に表示する方法について説明します。 料金は、加入契約のすべてのアカウントとサブスクリプションにわたって概要レベルで表示されます。

「[EA 管理者が使用量と請求書を管理する](https://www.youtube.com/watch?v=bO8V9eLfQHY)」ビデオをご覧ください。 これは、[Azure portal でのダイレクト Enterprise のお客様の請求エクスペリエンス](https://www.youtube.com/playlist?list=PLeZrVF6SXmsoHSnAgrDDzL0W5j8KevFIm) シリーズ ビデオの一部です。

>[!VIDEO https://www.youtube.com/embed/bO8V9eLfQHY]

## <a name="prerequisites"></a>前提条件

請求書上の料金をレビューまたは承認するには、Enterprise Administrator である必要があります。 詳細については、「[Understand Azure Enterprise Agreement administrative roles in Azure](understand-ea-roles.md)」(Azure の Azure Enterprise Agreement 管理者ロールを理解する) を参照してください。 組織のエンタープライズ管理者がだれであるかがわからない場合は、Azure portal でサポート リクエストを作成してください。

## <a name="review-usage-charges"></a>使用料金を確認する

特定のアカウントの詳細な使用状況を表示するには、使用状況の詳細レポートをダウンロードします。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[コストの管理と請求]** を検索し、それを選択します。
1. ナビゲーション メニューから **[課金スコープ]** を選択してから、操作する課金アカウントを選択します。
1. ナビゲーション メニューで、**[使用量 + 請求金額]** を選択します。  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/navigation-usage-charges.png" alt-text="[使用量 + 請求金額] ページを示すスクリーンショット。" lightbox="./media/direct-ea-azure-usage-charges-invoices/navigation-usage-charges.png" :::
1. 過去数年間の詳細を表示するには、**[期間]** を選択します。

次の表は、Azure portal の [使用量 + 請求金額] ページに表示される用語と説明の一覧を示しています。 Azure portal に表示される料金は米国ドルです。

| **用語** | **説明** |
| --- | --- |
| Month | 使用月 |
| クレジット請求金額 | その特定の期間中に適用されるクレジット。 |
| サービス超過分 | 組織の使用料金がクレジット残高を超えています。 |
| 個別請求 | 組織が使用したサービスにクレジットが対応していない場合。 |
| Azure Marketplace | Azure Marketplace での購入と使用は組織のクレジットの対象ではなく、個別に請求されます。 |
| 合計料金 | クレジット請求金額 + サービス超過分 + 個別請求 + Azure Marketplace |

## <a name="download-usage-charges-csv-file"></a>使用料金の CSV ファイルをダウンロードする

エンタープライズ管理者は、[使用量と請求金額をダウンロードする] ページを使用して、次のレポートを CSV ファイルとしてダウンロードします。

- **使用状況の詳細** - 選択に応じて、CSV ファイルでは、RI (予約) 購入を含むすべての請求金額 (使用量と購入額) が提供されます。 または、予約購入を含む償却費 (使用量と購入額)。
- **Marketplace ストアの請求料金** - Marketplace ストアの請求料金の CSV ファイルには、指定された請求期間の使用量ベースの Marketplace の請求金額の日ごとの内訳が含まれています。
- **価格シート** - 価格シートの CSV ファイルには、指定された加入契約と請求期間の測定ごとの該当する料金が含まれています。
- **残高と概要** - 残高と概要の CSV ファイルには、残高、新規購入、Marketplace のサービス料金、調整、超過料金に関する情報の月単位の概要が含まれています。

1. その月のレポートの横にある **[ダウンロード]** 記号を選択します。  
:::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/download-usage-charges-csv.png" alt-text="[ダウンロード] オプションを示すスクリーンショット。" :::
1. [使用量と請求金額をダウンロードする] ページで、ダウンロードするレポートの **[ドキュメントの準備]** を選択します。  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/prepare-document.png" alt-text="[ドキュメントの準備] ページを示すスクリーンショット。" lightbox="./media/direct-ea-azure-usage-charges-invoices/prepare-document.png" :::
1. 月間使用量によっては、Azure でダウンロードを準備するまでに時間がかかることがあります。 ダウンロードの準備ができたら、**[CSV のダウンロード]** を選択します。

エンタープライズ管理者はまた、[使用量 + 請求金額] ページの下部で、選択された期間の料金の全体的な概要を確認することもできます。

:::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/usage-charges-summary.png" alt-text="使用料金の概要を示すスクリーンショット。" lightbox="./media/direct-ea-azure-usage-charges-invoices/prepare-document.png":::

## <a name="download-or-view-your-azure-billing-invoice"></a>Azure の請求書をダウンロードまたは表示する

請求書を [Azure Portal](https://portal.azure.com) からダウンロードするか、メールで送信することができます。 請求書は、登録のために請求書を受信するよう設定されているユーザーに送信されます。

請求書の表示や取得のためのアクセス許可を持っているのはエンタープライズ管理者だけです。 課金情報へのアクセス権の取得に関する詳細については、[ロールを使用した Azure の課金へのアクセス管理](manage-billing-access.md)に関するページをご覧ください。

請求サイクル中に、次のいずれかのイベントが発生すると、Azure の請求書が発行されます。

- **サービス超過分** - 組織の使用料金がクレジット残高を超えています。
- **個別請求の料金** - 組織で使用したサービスは、クレジットの対象ではありません。 以下のサービスについては、クレジットの残高にかかわらず請求書が発行されます。
  - Canonical
  - Citrix XenApp Essentials
  - Citrix XenDesktop
  - 登録ユーザー
  - OpenLogic
  - Remote Access Rights XenApp Essentials 登録ユーザー
  - Ubuntu Advantage
  - Visual Studio Enterprise (月払い)
  - Visual Studio Enterprise (年払い)
  - Visual Studio Professional (月払い)
  - Visual Studio Professional (年払い)
- **Marketplace の請求金額** - Azure Marketplace での購入と使用は、組織のクレジットの対象ではありません。 そのため、クレジットの残高に関係なく、Marketplace の料金が請求されます。 Azure portal では、エンタープライズ管理者が Marketplace での購入を有効および無効にすることができます。

請求書には、最初に Azure の使用料金とそれに関連したコストが表示され、その後に Marketplace の請求金額が表示されます。 クレジット残高がある場合は、それが Azure の使用状況に適用され、請求書には Azure の使用状況と Marketplace の使用状況が表示されますが、最後にコストは表示されません。

### <a name="download-your-azure-invoices-pdf"></a>Azure 請求書 (.pdf) のダウンロード

ほとんどのサブスクリプションでは、Azure portal で請求書をダウンロードできます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[コストの管理と請求]** を検索し、それを選択します。
1. ナビゲーション メニューから **[課金スコープ]** を選択してから、操作する課金アカウントを選択します。
1. ナビゲーション メニューで、 **[請求書]** を選択します。 [請求書] ページには、過去 12 か月間に生成されたすべての請求書とクレジット メモが表示されます。  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/invoices-page.png" alt-text="[請求書] ページを示すスクリーンショット。" lightbox="./media/direct-ea-azure-usage-charges-invoices/invoices-page.png" :::
    
1. [請求書] ページで、ダウンロードする請求書の行を見つけます。 行の右にある省略記号 (**…**) を選択します。
1. コンテキスト メニューで、**[ダウンロード]** を選択します。  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/download-context-menu.png" alt-text="[ダウンロード] コンテキスト メニューを示すスクリーンショット。"  :::

期間を選択して、最大で過去 3 年間の請求書の詳細を表示できます。

その請求書に PDF ファイルの 40 ページ分を超える品目が複数含まれている場合は、複数のドキュメント番号に分割されます。

次の表は、[請求書] ページに表示される用語と説明の一覧を示しています。

| **用語** | **説明** |
| --- | --- |
| 発行日 | 請求書またはクレジット メモが生成された日付。 |
| ドキュメント番号 | 請求書またはクレジット メモの番号。 |
| 請求期間 | 請求書またはクレジット メモの請求期間。 |
| 発注番号 | 請求書またはクレジット メモの発注番号。 |
| 合計金額 | 請求書またはクレジットの合計金額。 |

## <a name="review-credit-charges"></a>クレジット料金を確認する

このセクションの情報では、Azure 前払い (以前は年額コミットメントと呼ばれていました) の開始残高、期末残高、クレジットの調整を表示する方法について説明します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[コストの管理と請求]** を検索し、それを選択します。
1. ナビゲーション メニューから **[課金スコープ]** を選択してから、操作する課金アカウントを選択します。
1. ナビゲーション メニューで、**[クレジットとコミットメント]** を選択します。
1. [クレジット] タブには、クレジットの内訳と、一定期間にわたる残高を示すグラフが表示されます。  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/credits-tab.png" alt-text="[クレジット] タブを示すスクリーンショット。" lightbox="./media/direct-ea-azure-usage-charges-invoices/credits-tab.png" :::

次の表は、[クレジット] タブに表示される用語と説明の一覧を示しています。

| **用語** | **説明** |
| --- | --- |
| Month | クレジットの月 |
| 開始クレジット | その月の開始クレジット |
| 新しいクレジット | 追加された新しいクレジット |
| 調整 | その月に行われた調整 |
| 料金に適用されるクレジット | 生成された請求書またはクレジットの合計金額 |
| 終了クレジット | クレジット終了残高 |

## <a name="review-reservation-transaction-details"></a>予約トランザクションの詳細を確認する

Azure portal で Enterprise Agreement に対して行われたすべての予約を表示できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[コストの管理と請求]** を検索し、それを選択します。
1. ナビゲーション メニューから **[課金スコープ]** を選択してから、操作する課金アカウントを選択します。
1. ナビゲーション メニューで、**[予約トランザクション]** を選択します。 次の図に示されている価格は例です。  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/reservation-transactions.png" alt-text="[予約トランザクション] ページを示すスクリーンショット。" lightbox="./media/direct-ea-azure-usage-charges-invoices/reservation-transactions.png" :::

次の表は、[予約トランザクション] ページに表示される用語と説明の一覧を示しています。

| **用語** | **説明** |
| --- | --- |
| Date | 予約が行われた日付 |
| 名前 | 予約の名前 |
| 説明 | 予約の説明 |
| 購入サブスクリプション | 予約が行われたサブスクリプション |
| 購入アカウント | 予約が行われた購入アカウント |
| 請求頻度 | 予約の請求頻度 |
| Type | トランザクションの種類。 [購入] や [払戻] など。 |
| Quantity | 購入された予約数量 |
| 金額 (米国ドル) | 予約コスト |

## <a name="csv-report-formatting-issues"></a>CSV レポートの書式設定に関する問題

Excel で CSV レポートを表示したときに請求通貨がユーロである場合は、コンマとピリオドに関する書式設定の問題が発生することがあります。

示されているコストは例です。

たとえば、次のように表示されることがあります。

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
|---|--- | ---|---|
| 時間 | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

しかし、次のように表示される *必要があります*。

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| 時間 | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

この書式設定の問題は、Excel のインポート機能での既定の設定のために発生します。 Excel では、すべてのフィールドを *[標準]* テキストとしてインポートし、数値が数学的な標準で区切られていることを前提にしています。 たとえば、*1,000.00* です。

実際の通貨で千の位の区切り文字にピリオド (**.**) を、小数位の区切り文字にコンマ (**,**) を使用している場合は、間違って表示されます。 たとえば、*1.000,00* です。 インポートの結果は、地域の言語設定によって異なる場合があります。

書式設定に問題がない状態で CSV ファイルをインポートするには、次のようにします。

1. Microsoft Excel で、 **[ファイル]**  >  **[開く]** の順に移動します。 テキストのインポート ウィザードが表示されます。
1. **[元のデータの形式]** で、 **[コンマやタブなどの区切り文字によってフィールドごとに区切られたデータ]** を選択します。 既定値は **[固定幅]** です。
1. **[次へ]** を選択します。
1. **[区切り記号]** で、**[コンマ]** のチェック ボックスをオンにします。 **[タブ]** をオフにします (オンになっている場合)。
1. **[次へ]** を選択します。
1. **ResourceRate** および **ExtendedCost** 列までスクロールします。
1. **ResourceRate** 列を選択します。 それが黒色で強調表示されます。
1. **[列のデータ形式]** セクションで、 **[標準]** ではなく、 **[テキスト]** を選択します。 列ヘッダーが **[標準]** から **[テキスト]** に変更されます。
1. **[拡張原価]** 列について、手順 8 と 9 を繰り返してから、 **[完了]** を選択します。

> [!TIP]
> CSV ファイルを Excel で自動的に開くように設定している場合は、代わりに Excel で **Open** 関数を使用する必要があります。 Excel で、 **[ファイル]**  >  **[開く]** の順に移動します。

## <a name="next-steps"></a>次のステップ

- 直接エンタープライズ管理者が Azure portal で実行する一般的なタスクについては、[Azure での直接 EA の管理](direct-ea-administration.md)に関するページを参照してください。