---
title: Azure の使用状況と料金を表示してダウンロードする
description: Azure の毎日の使用状況と料金をダウンロードまたは表示し、使用可能なその他のリソースを確認する方法について説明します。
keywords: 使用量の請求, 利用料金, 使用量のダウンロード, 使用量の表示, Azure 請求書, Azure 使用量
author: bandersmsft
ms.author: banders
ms.reviewer: adwise
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.date: 10/22/2021
ms.openlocfilehash: e7f4a5f12ec9e1be5c7129d12ed519bc4c781d61
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130255410"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Azure の使用量と料金の表示とダウンロード

Azure の利用状況と請求金額に対する日単位の内訳を Azure portal からダウンロードすることができます。 また、Azure CLI を使用して使用状況データを取得することもできます。 Azure 使用状況情報を取得するアクセス許可を持つのは、特定のロール (アカウント管理者やエンタープライズ管理者など) だけです。 課金情報へのアクセス権の取得に関する詳細については、[ロールを使用した Azure の課金へのアクセス管理](../manage/manage-billing-access.md)に関するページをご覧ください。

Microsoft 顧客契約 (MCA) を結んでいる場合、Azure の利用状況と請求金額を表示するには、課金プロファイルの所有者、共同作成者、閲覧者、または請求書管理者である必要があります。  Microsoft Partner Agreement (MPA) を結んでいる場合、Azure の利用状況と請求金額を表示してダウンロードできるのは、取引先組織である Microsoft の全体管理者ロールと管理者エージェント ロールだけです。

使用量と請求金額のダウンロード オプションは、使用するサブスクリプションの種類により異なります。

## <a name="download-usage-from-the-azure-portal-csv"></a>Azure portal から利用状況をダウンロードする (.csv)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. "*コスト管理 + 請求*" を検索します。  
    ![Azure portal での [コストの管理と請求] の検索を示すスクリーンショット。](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. お持ちのアクセス権によっては、課金アカウントまたは課金プロファイルを選択する必要があります。
1. 左側のメニューの **[課金]** から **[請求書]** を選択します。
1. 請求書グリッドで、ダウンロードする使用量に対応する請求期間の行を探します。
1. 右側にある **ダウンロード アイコン** または省略記号 (`...`) を選択します。  
  ![ダウンロード オプションでの [コストの管理と請求] の [請求書] ページを示すスクリーンショット。](./media/download-azure-daily-usage/download-usage-others.png)  
1. 右側に [ダウンロード] ペインが開きます。 **[使用量の詳細]** セクションから **[ダウンロード]** を選択します。  

## <a name="download-usage-for-ea-customers"></a>EA のお客様の使用量のダウンロード

EA のお客様として使用量データを表示およびダウンロードするには、料金表示ポリシーが有効になっているエンタープライズ管理者、アカウント所有者、または部門管理者である必要があります。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. "*コスト管理 + 請求*" を検索します。  
    ![Azure portal の検索を示すスクリーンショット。](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. 複数の課金アカウントにアクセスできる場合、ご自分の EA 課金アカウントの課金スコープを選択します。
1. **[使用量 + 請求金額]** を選択します。
1. ダウンロードしたい月で、 **[ダウンロード]** を選択します。  
    ![EA のお客様の [コストの管理と請求] の [請求書] ページを示すスクリーンショット。](./media/download-azure-daily-usage/download-usage-ea.png)
1. [使用量と請求金額をダウンロードする] ページの [使用状況詳細] で、ダウンロードする請求の種類を一覧から選びます。 選択に応じて、RI (予約) 購入を含むすべての請求金額 (使用量と購入額) が CSV ファイルで提供されます。 または、予約購入を含む償却費 (使用量と購入額)。 
    :::image type="content" source="./media/download-azure-daily-usage/select-usage-detail-charge-type.png" alt-text="ダウンロードする使用量の詳細と請求の種類の選択を示すスクリーンショット。" :::
1. **[ドキュメントの準備]** を選びます。
1.  月間使用量によっては、Azure でダウンロードを準備するまでに時間がかかることがあります。 ダウンロードの準備ができたら、**[CSV のダウンロード]** を選択します。

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Microsoft 顧客契約に関する使用量のダウンロード

課金プロファイルの使用量データを表示およびダウンロードするには、課金プロファイルの所有者、共同作成者、閲覧者、または請求書管理者である必要があります。

### <a name="download-usage-for-billed-charges"></a>課金された料金の使用量のダウンロード

1. "**コスト管理 + 請求**" を検索します。
2. 課金プロファイルを選択します。
3. **[請求書]** を選択します。
4. 請求書グリッドで、ダウンロードする使用量に対応する請求書の行を探します。
5. 行の末尾にある省略記号 (`...`) をクリックします。
6. ダウンロードのコンテキスト メニューで **[Azure の利用状況と請求金額]** を選択します。

### <a name="download-usage-for-open-charges"></a>課金されていない料金の使用量のダウンロード

現在の請求期間の月度累計使用量 (料金がまだ課金されていないことを意味します) もダウンロードできます。

1. "**コスト管理 + 請求**" を検索します。
2. 課金プロファイルを選択します。
3. **[概要]** ブレードで **[Azure の利用状況と請求金額をダウンロードする]** をクリックします。

### <a name="download-usage-for-pending-charges"></a>保留中の料金の使用量のダウンロード

Microsoft 顧客契約を結んでいる場合は、現在の請求期間の月度累計使用量をダウンロードすることができます。 これらの料金はまだ請求されていません。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. "*コスト管理 + 請求*" を検索します。
3. 課金プロファイルを選択します。 お持ちのアクセス権によっては、最初に請求先アカウントを選択することが必要な場合があります。
4. **[概要]** 領域で、最近の請求金額の下にあるダウンロード リンクを見つけます。
5. **[使用状況と料金をダウンロードする]** を選択します。

## <a name="get-usage-data-with-azure-cli"></a>Azure CLI を使用して使用状況データを取得する

まず、Azure CLI の環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

サブスクリプションの月度累計使用量情報を照会するには、サインイン後、[az costmanagement query](/cli/azure/costmanagement#az_costmanagement_query) コマンドを使用します。

```azurecli
az costmanagement query --timeframe MonthToDate --type Usage --dataset-aggregation '{\"totalCost\":{\"name\":\"PreTaxCost\",\"function\":\"Sum\"}}' --dataset-grouping name="ResourceGroup" type="Dimension"
   --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

**--dataset-filter** などのパラメーターを使用してクエリを絞り込むこともできます。

```azurecli
'{\"totalCost\":{\"name\":\"PreTaxCost\",\"function\":\"Sum\"}}' --dataset-grouping name="ResourceGroup" type="Dimension"
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --dataset-filter "{\"and\":[{\"or\":[{\"dimension\":{\"name\":\"ResourceLocation\",\"operator\":\"In\",\"values\":[\"East US\",\"West Europe\"]}},{\"tag\":{\"name\":\"Environment\",\"operator\":\"In\",\"values\":[\"UAT\",\"Prod\"]}}]},{\"dimension\":{\"name\":\"ResourceGroup\",\"operator\":\"In\",\"values\":[\"API\"]}}]}"
```

**--dataset-filter** パラメーターには、JSON 文字列または `@json-file` を指定します。

[az costmanagement export](/cli/azure/costmanagement/export) コマンドを使用して、利用状況データを Azure ストレージ アカウントにエクスポートすることもできます。 そこからデータをダウンロードすることができます。

1. リソース グループを作成するか、または既存のリソース グループを使用します。 リソース グループを作成するには、[az group create](/cli/azure/group#az_group_create) コマンドを実行します。

   ```azurecli
   az group create --name TreyNetwork --location "East US"
   ```

1. エクスポートを受信するためのストレージ アカウントを作成するか、既存のストレージ アカウントを使用します。 アカウントを作成するには、[az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドを使用します。

   ```azurecli
   az storage account create --resource-group TreyNetwork --name cmdemo
   ```

1. [az costmanagement export create](/cli/azure/costmanagement/export#az_costmanagement_export_create) コマンドを実行して、エクスポートを作成します。

   ```azurecli
   az costmanagement export create --name DemoExport --type Usage \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --storage-directory demodirectory
   ```

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

請求書および使用状況の詳細については、以下を参照してください。

- [Microsoft Azure の詳細な使用状況の用語を理解する](understand-usage.md)
- [Microsoft Azure の課金内容の確認](review-individual-bill.md)
- [Microsoft Azure の請求書の表示とダウンロード](download-azure-invoice.md)
- [組織の Azure の価格の表示とダウンロード](../manage/ea-pricing.md)

Microsoft 顧客契約を結んでいる場合は、以下を参照してください。

- [Microsoft 顧客契約での Azure の詳細な使用状況の用語を理解する](mca-understand-your-usage.md)
- [Microsoft 顧客契約の請求書での料金を理解する](review-customer-agreement-bill.md)
- [Microsoft Azure の請求書の表示とダウンロード](download-azure-invoice.md)
- [Microsoft 顧客契約に関する税務書類の表示とダウンロード](mca-download-tax-document.md)
- [組織の Azure の価格の表示とダウンロード](../manage/ea-pricing.md)
