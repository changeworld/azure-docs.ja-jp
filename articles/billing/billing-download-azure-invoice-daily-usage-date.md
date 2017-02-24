---
title: "Azure の請求書と毎日の使用状況データを取得する方法 | Microsoft Docs"
description: "Azure の請求書と毎日の使用状況データを取得する方法について説明します"
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
ms.date: 02/13/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 4d974590ee7677a4f3fcebd9e2474c99a974f663
ms.openlocfilehash: 60ac57fd22f237b7fe62773486fe46913aca8dd0


---
# <a name="how-to-get-your-azure-billing-invoice-and-daily-usage-data"></a>Azure の請求書と毎日の使用状況データを取得する方法
オプトインして、Azure の請求書を電子メールで受取る受信者を追加設定することができます。 請求書は [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) からダウンロードすることもできます。 さらに、[Azure アカウント センター](https://account.windowsazure.com) で使用状況の csv をダウンロードできます。 請求書と使用状況の情報にアクセスする権限があるのは、アカウント管理者のみです。 サブスクリプションのアカウント管理者を確認するには、「[Azure サブスクリプションの所有権の譲渡 - FAQ](billing-subscription-transfer.md#faq)」をご覧ください。

## <a name="get-your-invoice-over-email-pdf"></a>電子メールで請求書を取得する (.pdf)
1. [[サブスクリプション] ブレード](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)で、自分のサブスクリプションを選択します。 自分が所有するサブスクリプションごとにオプトインする必要があります。 **[請求書]** をクリックし、**[請求書を送信する]** をクリックします。 アカウント管理者でない場合、このオプションは表示されない可能性があります。

    ![オプトイン フローを示すスクリーン ショット](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. **[オプトイン]** をクリックして、条項に同意します。

    ![オプトイン フローを示すスクリーン ショット](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. 契約書に同意したら、追加の受信者を設定することができます。

    ![オプトイン フローを示すスクリーン ショット](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
毎月の明細書の通知メールに含まれるリンクから、このブレードにアクセスすることもできます。

### <a name="i-cant-access-the-email-settings-blade"></a>電子メール設定のブレードにアクセスできない
* この設定を行うにはアカウント管理者である必要があります。その理由が分からない場合は、 [こちら](billing-add-change-azure-subscription-administrator.md)を参照してください。
* 月次請求書があるのに電子メールを受信していない場合は、[連絡先メールが正しく設定されている](https://account.windowsazure.com/profile)かどうか確認してください。
* この機能は、サポート プラン、エンタープライズ契約、Azure イン オープン プランなどの特定のサブスクリプションでは利用できない場合があります。

## <a name="download-invoice-from-azure-portal-pdf"></a>Azure Portal から請求書をダウンロードする (.pdf)

1. [Azure Portal](https://portal.azure.com) にアカウント管理者としてサインインします。 
2. ハブ メニューで、**[サブスクリプション]**を選択します。 

    ![Screenshot that shows the Subscription option](./media/billing-download-azure-invoice-daily-usage-date/submenu.png) 

3. **[サブスクリプション]** ブレードで、表示するサブスクリプションを選択し、**[課金と使用量]** を選択します。 

    ![Screenshot that shows the Billing & usage option](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

4. **[課金と使用量]** ブレードで、**[請求書のダウンロード]** をクリックして請求書を PDF で表示します。 

    ![Screenshot that shows billing periods, the download option, and total charges for each billing period](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

5. 毎日の使用状況を表示するには、請求期間をクリックします。 

請求書の詳細については、「[Microsoft Azure の課金内容の確認](billing-understand-your-bill.md)」をご覧ください。

## <a name="download-usage-from-the-account-center-csv"></a>アカウント センターから使用状況をダウンロードする (.csv)
1. アカウント管理者として [Azure アカウント センター](https://account.windowsazure.com/subscriptions)にサインインします。
2. 請求書と使用状況を確認するサブスクリプションを選択します。
3. **[請求履歴]**を選択します。 

    ![Screenshot that shows billing history option](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. 過去&6; 回の請求期間と、現行の未請求期間の明細も確認できます。 

    ![Screenshot that shows billing periods, options to download invoice and daily usage, and total charges for each billing period](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. 見積もりを作成した時点での料金の見積もりを表示するには、**[現在の明細書の表示]** を選択します。 この情報は毎日更新されますが、すべての累計使用量が含まれていない可能性があります。 その月の実際の請求書は、この見積もりとは異なる場合があります。

    ![Screenshot that shows the View Current Statement option](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Screenshot that shows the estimate of current charges](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. CSV ファイル形式で毎日の使用状況データをダウンロードするには、**[利用状況のダウンロード]** を選択します。 バージョンが 2 つある場合は、バージョン 2 をダウンロードします。

    ![Screenshot that shows the Download Usage option](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

毎日の使用状況の詳細については、「[Microsoft Azure の課金内容の確認](billing-understand-your-bill.md)」をご覧ください。

## <a name="a-namenoinvoicea-why-dont-i-see-an-invoice-for-the-last-billing-period"></a><a name="noinvoice"></a> 前回の請求期間の請求書が表示されない理由
請求書が表示されない理由はいくつか考えられます。
- サブスクリプションの月単位のクレジット額がまだ残っているか、無料試用版がある場合。 負債額が発生しないと、請求書は生成されません。
- Azure のサブスクリプションを開始してから 30 日以上経過していない。
- 請求書がまだ生成されていない。 請求期間の終了までお待ちください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。




<!--HONumber=Feb17_HO3-->


