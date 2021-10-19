---
title: 使用状況ファイルのピボット テーブルを使用して Azure CSP の請求に関する問題をトラブルシューティングする
description: この記事は、CSV 使用状況ファイルから作成されたピボット テーブルを使用して、Azure クラウド ソリューション プロバイダー (CSP) の請求に関する問題をトラブルシューティングする場合に役立ちます。
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/07/2021
ms.author: banders
ms.openlocfilehash: de526c46a9aace4a6740b8b9afaab5cc23f3f87d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711102"
---
# <a name="troubleshoot-csp-billing-issues-with-usage-file-pivot-tables"></a>使用状況ファイルのピボット テーブルを使用して CSP の請求に関する問題をトラブルシューティングする

この記事は、パートナー センターの調整 (使用状況) ファイルのピボット テーブルを使用して、クラウド ソリューション プロバイダー (CSP) の請求に関する問題をトラブルシューティングする場合に役立ちます。 Azure 使用状況ファイルには、Azure の使用状況と消費量に関するすべての情報が含まれています。 このファイル内の情報は、次のことを理解するために役立ちます。

- Azure の予約がどのように使用および適用されているかを理解する
- 請求書を使用して、Cost Management の情報を調整する
- コスト スパイクをトラブルシューティングする
- サービス レベル アグリーメントの払戻額を計算する

使用状況ファイルの情報を使用すると、使用状況の問題をより深く理解し、診断することができます。 使用状況ファイルは、コンマ区切り (CSV) 形式で生成されます。 使用状況ファイルは大きな CSV ファイルになる可能性があるため、Excel のようなスプレッドシート アプリケーションでピボット テーブルとして操作および表示する方が簡単です。 この記事の例では Excel を使用しますが、必要な任意のスプレッドシート アプリケーションを使用できます。

調整ファイルにアクセスしてダウンロードできるのは、課金管理者とグローバル管理者のみです。 詳細については、「[パートナー センターの調整ファイルで品目を読み取る方法を学習する](/partner-center/use-the-reconciliation-files)」を参照してください。

## <a name="get-the-data-and-format-it"></a>データの取得と書式設定

Azure 使用状況ファイルは CSV 形式であるため、Excel で使用できるようにデータを準備する必要があります。 データをテーブルとして書式設定するには、次の手順に従います。

1. 「[請求書を検索する](/partner-center/read-your-bill#find-your-bill)」の手順に従って使用状況ファイルをダウンロードします。
1. Excel でファイルを開きます。
1. 書式設定されていないデータは、次の例のようになります。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" alt-text="Excel で表示されている書式設定されていないデータを示す例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" :::
1. テーブルの最初のフィールド (**PartnerID**) を選択します。
1. Ctrl + Shift + 下方向キーを押し、次に Ctrl + Shift + 右方向キーを押して、テーブル内のすべての情報を選択します。
1. トップ メニューで、 **[挿入]**  >  **[テーブル]** の順に選択します。 [テーブルの作成] ボックスで、 **[先頭行をテーブルの見出しとして使用する]** を選択し、 **[OK]** を選択します。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="[テーブルの作成] ダイアログを示す例" :::
1. トップ メニューで、 **[挿入]**  >  **[ピボット テーブル]** の順に選択し、 **[OK]** を選択します。 この操作により、ファイル内に新しいシートが作成され、シートの右側にあるピボット テーブル領域に移動します。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="[ピボットテーブルのフィールド] 領域を示す例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

[ピボットテーブルのフィールド] 領域は、ドラッグ アンド ドロップ領域です。 次のセクションに進んで、ピボット テーブルを作成します。

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Azure のコストをリソース別に表示するピボット テーブルの作成

このセクションでは、一般的な Azure 使用状況全体をトラブルシューティングできるピボット テーブルを作成します。 このテーブルの例は、リソースを最も消費しているサービスを調べるために役立ちます。 また、最もコストがかかっているリソースを表示したり、サービスへの課金方法を確認したりすることもできます。

1. [ピボットテーブルのフィールド] 領域で、 **[Service Name]** と **[Resource]** を **[行]** 領域にドラッグします。 **[Resource]** を **[Service Name]** の下に配置します。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="[行] に配置された [Service Name] と [Resource] を示す例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. 次に **[Post-Tax Total]** を **[値]** 領域に配置します。 また、代わりに [Consumed Quantity] 列を使用して、消費単位やトランザクションに関する情報を取得することもできます。 たとえば、GB や時間数などです。 または、USD、EUR、INR などのさまざまな通貨単位のコストの代わりに、トランザクション数も取得できます。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="ピボット テーブルのフィールドに追加された列を示す例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. これで、汎用的な消費量調査のためのダッシュボードが作成されました。 ピボット テーブルのフィルター オプションを使用して特定のサービスをフィルター処理できます。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="ピボット テーブルの行ラベルのフィルター オプションを示す例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    ピボット テーブルで 2 番目のレベル (たとえば、リソース) をフィルター処理するには、テーブル内で 2 番目のレベルの項目を選択します。
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="[フィールドの選択] のフィルター オプションを示す例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. 追加のフィルターについては、 **[SubscriptionID]** と **[Customer Company Name]** を **[フィルター]** 領域に追加し、目的の範囲を選択できます。

## <a name="create-a-pivot-table-to-view-azure-usage-by-date"></a>Azure の使用状況を日付別に表示するピボット テーブルの作成

このセクションでは、一般的な Azure 使用状況全体を使用済み数量と日付別にトラブルシューティングするためのピボット テーブルを作成します。 これは、課金スパイクを日付およびサービス別に特定するために役立ちます。 また、最もコストがかかっているリソースを表示したり、サービスへの課金方法を確認したりすることもできます。

調整ファイルには、2 つのテーブルがあります。 1 つは上部にあり (メイン テーブル)、もう 1 つのテーブルはドキュメントの下部にあります。 この 2 番目のテーブルには、ほとんど同じ情報が含まれていますが、価格またはコストの詳細は含まれていません。 これには、使用日と使用済み数量が含まれています。

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" alt-text="2 つのデータ テーブルがある調整ファイルを示す例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" :::

1. 「[データの取得と書式設定](#get-the-data-and-format-it)」セクションと同じ手順を使用して、調整ファイルの下部にある情報を含む Excel テーブルを作成します。
1. テーブルの準備ができ、ピボット テーブル シートを作成したら、「Azure のコストをリソース別に表示するピボット テーブルの作成」セクションと同じ手順を使用して、ダッシュボードを準備します。 [Post-Tax total] を使用する代わりに、 **[Consumed quantity]** を **[値]** 領域に配置します。
1. **[Usage Date]** を [列] セクションに追加します。 ピボット テーブルは、次の例のようになります。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" alt-text="最終的なピボット テーブルのフィールドを示す例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" :::
1. これで、日付ごとの使用量を表示するダッシュボードが作成されました。 各月を拡張するには、 **+** 記号を選択します。

このダッシュボードには、使用済み数量が、GB、時間、転送などの単位で表示されます。

1 日あたりの料金を表示するには、 **[Resource GUID]** を **[行]** 領域に追加できます。 上のテーブルで、リソースの単価 ( **[ListPrice]** ) を追加します。 **[ListPrice]** に **[Consumed quantity]** を乗算して、税引き前の料金を計算します。 金額は一致している必要があります。

一部のリソース (サービス) では、使用済み数量によって価格がスケーリングされています。 たとえば、一部のリソースでは、最初の 100 GB の消費量については価格が高く、その後使用された GB の価格は低くなります。 コストを手動で計算する場合、スケーリング価格を考慮してください。

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>特定のリソースのコストを表示するピボット テーブルの作成

1 つのリソースで、さまざまなサービスに対して複数の料金が発生する場合があります。 たとえば、仮想マシンの場合、コンピューティング、OS ライセンス、帯域幅 (データ転送)、RI 使用量、スナップショット用ストレージの料金が発生する可能性があります。 特定のリソースの全体的な使用状況を確認する場合は常に、次の手順でダッシュボードを作成し、使用状況ファイルを使用して全体的な使用状況を表示します。

調整ファイルには、リソース固有の詳細が含まれていません。 そのため、使用状況の集計ファイルを使用します。 ご利用のサブスクリプションに関する使用状況の集計ファイルを提供してもらうには、[Azure 課金サポート](https://go.microsoft.com/fwlink/?linkid=2083458)に連絡してください。 集計ファイルは、サブスクリプション レベルで生成されます。 書式設定されていないデータは、次の例のようになります。

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" alt-text="書式設定されていない使用状況の集計ファイルを示す例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" :::

このファイルには、次の列が含まれています。

- **UsageStart** と **UsageEnd** - 各品目 (各使用単位) の日付。 たとえば、毎日。
- **MeteredResourceID** - Azure では、これは測定 ID に対応します。
- **Properties** - インスタンス ID (リソース名) と、場所などのその他の詳細が含まれます。
- **Quantity** - 調整ファイル内の使用済み数量。

1. テーブルの最初のフィールド (**PartnerID**) を選択します。  
1. Ctrl + Shift + 下方向キーを押し、次に Ctrl + Shift + 右方向キーを押して、テーブル内のすべての情報を選択します。
1. トップ メニューで、 **[挿入]**  >  **[テーブル]** の順に選択します。 [テーブルの作成] ボックスで、 **[先頭行をテーブルの見出しとして使用する]** を選択し、 **[OK]** を選択します。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="[テーブルの作成] ダイアログを示す例" :::
1. トップ メニューで、 **[挿入]**  >  **[ピボット テーブル]** の順に選択し、 **[OK]** を選択します。 この操作により、ファイル内に新しいシートが作成され、シートの右側にあるピボット テーブル領域に移動します。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" alt-text="調整ファイルの [ピボット テーブルのフィールド] 領域を示す例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" :::
1. 次に、 **[MeteredResourceID]** を **[行]** 領域に追加し、 **[Quantity]** を **[値]** に追加します。 結果には、全体的な使用状況の情報が表示されます。 追加の詳細については、 **[UsageEndDateTime]** を **[列]** 領域に配置します。  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" alt-text="全体的な使用状況の情報を示す例" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" :::
1. 全体的なレポートを表示するには、 **[Properties]** を、 **[行]** の **[MeteredResourceID]** の下に追加します。 これにより、使用状況の完全なダッシュボードが表示されます。
1. 特定のリソースでフィルター処理するには、 **[Properties]** を **[フィルター]** 領域に追加し、目的の使用状況を選択します。 リソース名を検出するには、[検索] を使用できます。
    リソースのコストを表示するには、合計使用済み数量を見つけて、その値に表示価格を乗算します。 表示価格は、各リソース GUID (MeteredResourceID) 固有です。 リソースが複数の MeteredResourceID を使用している場合、各 ID の合計値をメモしておく必要があります。

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>次のステップ

- [パートナー向け Cost Management の利用を開始する](../costs/get-started-partners.md)