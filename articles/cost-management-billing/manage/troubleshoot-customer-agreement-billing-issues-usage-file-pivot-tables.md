---
title: 使用状況ファイルのピボット テーブルを使用して Azure MCA の請求に関する問題をトラブルシューティングする
description: この記事は、CSV 使用状況ファイルから作成されたピボット テーブルを使用して、Microsoft 顧客契約 (MCA) の請求に関する問題をトラブルシューティングする場合に役立ちます。
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/07/2021
ms.author: banders
ms.openlocfilehash: cd7507a6bbb9ef8cfe71c95431fe63191cd63275
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711125"
---
# <a name="troubleshoot-mca-billing-issues-with-usage-file-pivot-tables"></a>使用状況ファイルのピボット テーブルを使用して MCA の請求に関する問題をトラブルシューティングする

この記事は、使用状況ファイルのピボット テーブルを使用して、Microsoft 顧客契約 (MCA) の請求に関する問題をトラブルシューティングする場合に役立ちます。 Azure 使用状況ファイルには、Azure の使用状況と消費量に関するすべての情報が含まれています。 このファイル内の情報は、次のことを理解するために役立ちます。

- Azure の予約がどのように使用および適用されているかを理解する
- 請求書を使用して、Cost Management の情報を調整する
- コスト スパイクをトラブルシューティングする
- サービス レベル アグリーメントの払戻額を計算する

使用状況ファイルの情報を使用すると、使用状況の問題をより深く理解し、診断することができます。 使用状況ファイルは、コンマ区切り (CSV) 形式で生成されます。 使用状況ファイルは大きな CSV ファイルになる可能性があるため、Excel のようなスプレッドシート アプリケーションでピボット テーブルとして操作および表示する方が簡単です。 この記事の例では Excel を使用しますが、必要な任意のスプレッドシート アプリケーションを使用できます。

使用状況ファイルにアクセスしてダウンロードできるのは、課金プロファイル所有者、共同作成者、閲覧者、請求書管理者のみです。 詳細については、「[Microsoft 顧客契約に関する使用量のダウンロード](../understand/download-azure-daily-usage.md)」を参照してください。 

## <a name="get-the-data-and-format-it"></a>データの取得と書式設定

Azure 使用状況ファイルは CSV 形式であるため、Excel で使用できるようにデータを準備する必要があります。 データをテーブルとして書式設定するには、次の手順に従います。

1. 「[Azure portal で使用状況をダウンロードする](../understand/download-azure-daily-usage.md)」の手順に従って、使用状況ファイルをダウンロードします。
1. Excel でファイルを開きます。
1. 書式設定されていないデータは、次の例のようになります。  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/raw-csv-data-mca.png" alt-text="書式設定されていないデータを示す例" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/raw-csv-data-mca.png" :::
1. テーブルの最初のフィールド (**invoiceID**) を選択します。
1. Ctrl + Shift + 下方向キーを押し、次に Ctrl + Shift + 右方向キーを押して、テーブル内のすべての情報を選択します。
1. トップ メニューで、 **[挿入]**  >  **[テーブル]** の順に選択します。 [テーブルの作成] ボックスで、 **[先頭行をテーブルの見出しとして使用する]** を選択し、 **[OK]** を選択します。  
:::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="[テーブルの作成] ダイアログを示す例" :::
1. トップ メニューで、 **[挿入]**  >  **[ピボット テーブル]** の順に選択し、 **[OK]** を選択します。 この操作により、ファイル内に新しいシートが作成され、シートの右側にあるピボット テーブル領域に移動します。  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="[ピボットテーブルのフィールド] 領域を示す例" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

[ピボットテーブルのフィールド] 領域は、ドラッグ アンド ドロップ領域です。 次のセクションに進んで、ピボット テーブルを作成します。

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Azure のコストをリソース別に表示するピボット テーブルの作成

このセクションでは、一般的な Azure 使用状況全体をトラブルシューティングできるピボット テーブルを作成します。 このテーブルの例は、リソースを最も消費しているサービスを調べるために役立ちます。 また、最もコストがかかっているリソースや、サービスへの課金方法を確認することもできます。

1. [ピボットテーブルのフィールド] 領域で、 **[MeterCategory]** と **[Product]** を **[行]** セクションにドラッグします。 **[Product]** を **[MeterCategory]** の下に配置します。  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="[行] に配置された [MeterCategory] と [Product] を示す例" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. 次に、 **[costInBillingCurrenty]** 列を **[値]** セクションに追加します。 また、代わりに **[Quantity]** 列を使用して、消費単位やトランザクションに関する情報を取得することもできます。 たとえば、GB や時間数などです。 または、USD、EUR、INR などのさまざまな通貨単位のコストの代わりに、トランザクション数も取得できます。  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="ピボット テーブルに追加されたフィールドを示す例" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. これで、汎用的な消費量調査のためのダッシュボードが作成されました。 ピボット テーブルのフィルター オプションを使用して特定のサービスをフィルター処理できます。  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="ピボット テーブルの行ラベルのフィルター オプションを示す例" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    ピボット テーブルで 2 番目のレベル (たとえば、リソース) をフィルター処理するには、テーブル内で 2 番目のレベルの項目を選択します。  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="[フィールドの選択] のフィルター オプションを示す例" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. **[ResourceID]** 列を **[行]** 領域の **[Product]** の下にドラッグすると、各サービスのコストがリソース別に表示されます。
1. **[date]** 列を **[列]** 領域に追加すると、製品の日次消費量が表示されます。  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-date.png" alt-text="[date] を [列] 領域に配置する場所を示す例" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-date.png" :::
1. 月を展開したり、折りたたんだりするには、各月の列の **+** 記号を使用します。  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="+ 記号を示す例" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" :::

**[Cost]** と **[Quantity]** の両方の列を **[値]** 領域に追加するのはオプションです。 そうすることで、ピボット テーブルの [列] セクションに [Date] 列がある場合、各月と日の下のデータ セクションごとに 2 つの列が作成されます。

追加のフィルターについては、[InvoiceSection]、[costCenter]、[SubscriptionID]、[ResourceGroupName]、または [Tags] を [フィルター] セクションに追加し、必要な範囲を選択できます。

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>特定のリソースのコストを表示するピボット テーブルの作成

1 つのリソースで、さまざまなサービスに対して複数の料金が発生する場合があります。 たとえば、仮想マシンの場合、コンピューティング、OS ライセンス、帯域幅 (データ転送)、RI 使用量、スナップショット用ストレージの料金が発生する可能性があります。 特定のリソースの全体的な使用状況を確認する場合は常に、次の手順でダッシュボードを作成し、使用状況ファイルを使用して全体的な使用状況を表示します。

1. 右側のメニューで、 **[ResourceID]** を、ピボット テーブル メニューの **[フィルター]** セクションにドラッグします。
1. コストを表示するリソースを選択します。 リソース名を **[検索]** ボックスに入力して検索します。  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/resource-id-search.png" alt-text="resourceID を検索する場所を示す例" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/resource-id-search.png" :::
1. **[meterCategory]** と **[Product]** を **[行]** 領域に追加します。 **[Product]** を **[meterCategory]** の下に配置します。  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" alt-text="[meterCategory] を [ピボット テーブルのフィールド] に配置する場所を示す例" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" :::
1. 次に **[Extended Cost]** 列を **[値]** セクションに追加します。 また、代わりに [Consumed Quantity] 列を使用して、消費単位やトランザクションに関する情報を取得することもできます。 たとえば、GB や時間数などです。 または、USD、EUR、INR などのさまざまな通貨単位のコストの代わりに、トランザクション数も取得できます。 これで、リソースで使用されるすべてのサービスを表示するダッシュボードが作成されました。
1. **[Date]** 列を **[列]** セクションに追加します。 これにより、日次消費量が表示されます。
1. 展開および縮小するには、各月の列にある **+** アイコンを使用します。  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="+ 記号を示す例" :::

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

[!INCLUDE [Troubleshoot usage spikes](../../../includes/cost-management-billing-troubleshoot-usage-spikes.md)]

## <a name="next-steps"></a>次のステップ

- [コスト分析を使用してコストを調査および分析する](../costs/quick-acm-cost-analysis.md)。