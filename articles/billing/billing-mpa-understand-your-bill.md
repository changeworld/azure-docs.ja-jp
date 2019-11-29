---
title: Microsoft Partner Agreement の請求書の料金を理解する - Azure
description: 請求書の料金を読み取り、理解する方法について説明します。
author: jureid
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2019
ms.author: banders
ms.openlocfilehash: 9994ef6da379d0063930912a4d198b81623ddbe8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223709"
---
# <a name="understand-charges-on-your-microsoft-partner-agreement-invoice"></a>Microsoft Partner Agreement の請求書の料金を理解する

 Microsoft Partner Agreement の課金アカウントでは、課金プロファイルごとに毎月請求書が生成されます。 請求書には、顧客に対する前月からのすべての請求金額が含まれます。 Azure portal で個々の取引を分析することで、請求書の料金を理解できます。 Azure portal で請求書を表示することもできます。 詳細については、[Azure portal から請求書をダウンロードする方法](billing-download-azure-invoice.md)に関するページを参照してください。

この記事では、Microsoft Partner Agreement の課金アカウントについて説明します。 [Microsoft Partner Agreement にアクセスできるかどうかを確認してください](#check-access-to-a-microsoft-partner-agreement)。

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Azure portal で請求書のトランザクションを表示する

1. [Azure Portal](https://www.azure.com) にサインインします。

2. "*コスト管理 + 請求*" を検索します。

   <!--  ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. ページの左側から **[すべてのトランザクション]** を選択します。 アクセス方法によっては、課金アカウント、課金プロファイル、または顧客を選択してから、 **[すべてのトランザクション]** を選択することが必要な場合があります。

4. [すべてのトランザクション] ページには次の情報が表示されます。

    <!-- ![Screenshot that shows the billed transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-all-transactions.png) -->

    |列  |定義  |
    |---------|---------|
    |Date     | トランザクションの日付  |
    |請求書 ID     | トランザクションが課金される請求書の識別子。 サポート要求を送信する場合、ID を Azure サポートに知らせると、サポート要求が迅速に処理されます |
    |トランザクションの種類     |  購入、キャンセル、利用料金などのトランザクションの種類  |
    |製品ファミリ     | 仮想マシン用のコンピューターや Azure SQL データベース用のデータベースなどの製品のカテゴリ|
    |製品 SKU     | お使いの製品のインスタンスを識別する一意のコード |
    |金額     |  トランザクションの金額      |
    |請求プロファイル     | トランザクションは、この課金プロファイルの請求書に表示されます |

5. 請求書 ID で検索して、請求書のトランザクションをフィルター処理します。

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>保留中の料金を確認して次の請求書を見積もる

料金は、請求が行われるまで、見積もられたもので保留中であると見なされます。 Microsoft Partner Agreement の課金プロファイルでは、Azure portal で保留中の料金を確認して次の請求書を見積もることができます。 保留中の料金は見積もられたもので、税は含まれていません。 次の請求書の実際の料金は、保留中の料金とは異なるものになります。

### <a name="view-pending-transactions"></a>保留中のトランザクションを表示する

保留中の料金を確認するときに、その料金を発生させた個々のトランザクションを分析することで、料金のことを理解できます。 この時点では、[すべてのトランザクション] ページに保留中の利用料金は表示されません。 [Azure サブスクリプション] ページで、保留中の利用料金を確認できます。 詳細については、「[保留中の利用料金を表示する](#view-pending-usage-charges)」を参照してください

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. "*コスト管理 + 請求*" を検索します。

   <!-- ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. 課金プロファイルを選択します。 アクセス方法によっては、請求先アカウントを選択することが必要な場合があります。 請求先アカウントから **[課金プロファイル]** を選択し、課金プロファイルを選択します。

4. ページの左側から **[すべてのトランザクション]** を選択します。

5. *保留中*を検索します。 **Timespan** フィルターを使用して、当月または先月の保留中の料金を表示します。

   <!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>顧客ごとに保留中の料金を表示する

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. "*コスト管理 + 請求*" を検索します。

3. 課金プロファイルを選択します。 アクセス方法によっては、請求先アカウントを選択することが必要な場合があります。 請求先アカウントから **[課金プロファイル]** を選択し、課金プロファイルを選択します。

4. ページの左側にある **[顧客]** を選択します。

    <!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

5. [顧客] ページには、課金プロファイルに関連付けられている顧客ごとに今月と先月の料金が表示されます。 月度累計請求金額は、当月の保留中の料金であり、当月の請求書が作成されるときに請求されます。 先月の請求書がまた生成されていない場合、先月の料金も保留中です。

### <a name="view-pending-usage-charges"></a>保留中の利用料金を表示する

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. "*コスト管理 + 請求*" を検索します。

3. 課金プロファイルを選択します。 アクセス方法によっては、請求先アカウントを選択することが必要な場合があります。 請求先アカウントから **[課金プロファイル]** を選択し、課金プロファイルを選択します。

4. ページの左側の **[Azure サブスクリプション]** を選択します。

5. [Azure サブスクリプション] ページに、課金プロファイルのサブスクリプションごとに当月および先月の料金が表示されます。 月度累計請求金額は、当月の保留中の料金であり、当月の請求書が作成されるときに請求されます。 先月の請求書がまた生成されていない場合、先月の料金も保留中です。

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Azure 利用料金を分析する

Azure の使用状況と料金の CSV ファイルを使用して、使用状況ベースの料金を分析します。 [Azure の利用状況と請求金額を含む CSV を Azure portal からダウンロード](billing-download-azure-daily-usage.md)することができます。

Azure の利用状況と請求金額を含むファイルをフィルター処理して、請求書の PDF に記載されている各製品の使用料金を調整できます。 特定の製品の詳細な使用料金を表示するには、Azure の利用状況と請求金額を含む CSV ファイルの **product** 列にフィルターを適用して、該当する製品名のみを抽出します。

Azure の利用状況と請求金額を含む CSV ファイルの **customerName** 列にフィルターを適用して、顧客ごとに日単位の使用料金を表示することもできます。 Azure サブスクリプションごとに日単位の使用料金を表示したい場合は、**subscriptionName** 列にフィルターを適用します。


## <a name="pay-your-bill"></a>請求金額を支払う

請求金額を支払う指示が、請求書の底部に表示されます。 請求書の日付から 60 日以内に電信送金または小切手で支払うことができます。

請求金額を既に支払っている場合は、Azure portal の [請求書] ページで支払いの状態を確認できます。

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Microsoft Partner Agreement へのアクセスの確認
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次の手順

請求書および詳細な使用状況の詳細については、以下を参照してください。
