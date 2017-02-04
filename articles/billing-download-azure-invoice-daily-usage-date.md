---
title: "Azure の請求書と毎日の使用状況データをダウンロードする方法 | Microsoft Docs"
description: "Azure の請求書と毎日の使用状況データをダウンロードする方法について説明します"
services: 
documentationcenter: 
author: genlin
manager: ruchic
editor: 
tags: billing
ms.assetid: 6d568d1d-3bd6-4348-97d0-1098b5fe0661
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/2/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 9b864a55d413ec7775e96fced4770b414f379a23
ms.openlocfilehash: ffc5cae2c4559dd8c7a9042dc676485966b976f4


---
# <a name="how-to-download-your-azure-billing-invoice-and-daily-usage-data"></a>Azure の請求書と毎日の使用状況データをダウンロードする方法
請求書と毎日の使用状況は [Azure アカウント センター](https://account.windowsazure.com/subscriptions)からダウンロードできます。 請求書と使用状況の情報にアクセスする権限があるのは、アカウント管理者のみです。 サブスクリプションのアカウント管理者を確認するには、「[Azure サブスクリプションの所有権の譲渡 - FAQ](billing-subscription-transfer.md#faq)」をご覧ください。

## <a name="get-invoice-and-usage-from-azure-account-center"></a>Azure アカウント センターから請求書と使用状況を取得する
1. アカウント管理者として [Azure アカウント センター](https://account.windowsazure.com/subscriptions) にサインインします。
2. 請求書と使用状況を確認するサブスクリプションを選択します。
3. **[請求履歴]**を選択します。 

    ![Screenshot that shows billing history option](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. 過去&6; 回の請求期間と、現行の未請求期間の明細も確認できます。 

    ![Screenshot that shows billing periods, options to download invoice and daily usage, and total charges for each billing period](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. 見積もりを作成した時点での料金の見積もりを表示するには、**[現在の明細書の表示]** を選択します。 この情報は毎日更新されますが、すべての累計使用量が含まれていない可能性があります。 その月の実際の請求書は、この見積もりとは異なる場合があります。

    ![Screenshot that shows the View Current Statement option](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Screenshot that shows the estimate of current charges](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. 最新の請求書を PDF で確認するには、**[請求書のダウンロード]** を選択します。 

    ![Screenshot that shows the Download Invoice option](./media/billing-download-azure-invoice-daily-usage-date/DLInvoice1.png)

7. CSV ファイル形式で毎日の使用状況データをダウンロードするには、**[利用状況のダウンロード]** を選択します。 バージョンが 2 つある場合は、バージョン 2 をダウンロードします。

    ![Screenshot that shows the Download Usage option](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

請求書と毎日の使用状況の詳細については、「[Microsoft Azure の課金内容の確認](./billing/billing-understand-your-bill.md)」をご覧ください。

## <a name="get-invoice-from-azure-portal"></a>Azure Portal から請求書を取得する
Azure Portal では毎日の使用状況を確認できますが、ダウンロードできるのは請求書のみです。

1. [Azure Portal](https://portal.azure.com) にアカウント管理者としてサインインします。 
2. ハブ メニューで、**[サブスクリプション]**を選択します。 

    ![Screenshot that shows the Subscription option](./media/billing-download-azure-invoice-daily-usage-date/submenu.png) 

3. **[サブスクリプション]** ブレードで、表示するサブスクリプションを選択し、**[課金と使用量]** を選択します。 

    ![Screenshot that shows the Billing & usage option](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

4. **[課金と使用量]** ブレードで、**[請求書のダウンロード]** をクリックして請求書を PDF で表示します。 

    ![Screenshot that shows billing periods, the download option, and total charges for each billing period](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

5. 毎日の使用状況を表示するには、請求期間をクリックします。 

請求書と毎日の使用状況の詳細については、「[Microsoft Azure の課金内容の確認](./billing/billing-understand-your-bill.md)」をご覧ください。

## <a name="a-namenoinvoicea-why-dont-i-see-an-invoice-for-the-last-billing-period"></a><a name="noinvoice"></a> 前回の請求期間の請求書が表示されない理由
請求書が表示されない理由はいくつか考えられます。
- サブスクリプションの月単位のクレジット額がまだ残っているか、無料試用版がある場合。 負債額が発生しないと、請求書は生成されません。
- Azure のサブスクリプションを開始してから 30 日以上経過していない。
- 請求書がまだ生成されていない。 請求期間の終了までお待ちください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。




<!--HONumber=Dec16_HO2-->


