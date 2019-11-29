---
title: Microsoft Azure の請求書の表示とダウンロード
description: Microsoft Azure の請求書の表示とダウンロードの方法を説明します。
keywords: 請求書,請求書のダウンロード,Azure の請求書,Azure の使用状況
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 7d52a589ee7fcb8891f5ea839b2baeb5e4b30733
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224022"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Microsoft Azure の請求書の表示とダウンロード

ほとんどのサブスクリプションの場合は、請求書を [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) からダウンロードするか、メールで送信することができます。 エンタープライズ契約 (EA) の Azure のお客様 (EA のお客様) の場合、お客様の組織の請求書をダウンロードすることはできません。 請求書は、登録のために請求書を受信するよう設定されているユーザーに送信されます。

請求書を表示するアクセス許可があるのは、特定のロールだけです。 たとえば、アカウント管理者やエンタープライズ管理者が該当します。 課金情報へのアクセス権の取得に関する詳細については、[ロールを使用した Azure の課金へのアクセス管理](billing-manage-access.md)に関するページを参照してください。

Microsoft 顧客契約 (MCA) を結んでいる場合、請求書を取得するためには、次のいずれかのロールが必要です。

- 課金プロファイル所有者
- Contributor
- Reader
- 請求書マネージャー

Microsoft Partner Agreement (MPA) を結んでいる場合、Azure の請求書を表示してダウンロードするためには、取引先組織の全体管理者または管理者エージェントであることが必要です。 [課金アカウントの種類を確認](#check-your-billing-account-type)して、必要なアクセス許可を把握してください。

<!-- For more information about billing roles for Microsoft Customer Agreements, see [Billing profile roles and tasks](billing-understand-mca-roles.md#billing-profile-roles-and-tasks). -->

## <a name="noinvoice"></a> 請求書が表示されない理由

請求書が表示されない理由はいくつか考えられます。

- Azure のサブスクリプションを開始してから 30 日以上経過していない。

- Azure では請求期間の最後に課金される。 そのため、請求書がまだ生成されていない可能性があります。 請求期間の終了までお待ちください。

- 請求書を表示するアクセス許可がない。 MCA または MPA を結んでいる場合、課金プロファイルの所有者、共同作成者、閲覧者、または請求書管理者である必要があります。 その他のサブスクリプションでは、アカウント管理者でない場合は古い請求書が表示されないことがあります。 課金情報へのアクセス権の取得に関する詳細については、[ロールを使用した Azure の課金へのアクセス管理](billing-manage-access.md)に関するページをご覧ください。

- ご利用のサブスクリプションで無料試用版を使用している場合や月単位のクレジット額がある場合、請求書が表示されるのは、毎月のクレジット額を超えたときのみ。 Microsoft 顧客契約または Microsoft Partner Agreement を交わしている場合は、常に請求書が表示されます。

## <a name="download-invoices-in-the-azure-portal"></a>Azure portal で請求書をダウンロードする

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. "*コスト管理 + 請求*" を検索します。
1. お持ちのアクセス権によっては、課金アカウントまたは課金プロファイルを選択する必要があります。
1. 左側のメニューの **[課金]** から **[請求書]** を選択します。
1. 請求書グリッドで、ダウンロードする請求書の行を探します。
1. 行の末尾にあるダウンロード アイコンまたは省略記号 (`...`) をクリックします。
1. ダウンロード メニューで、 **[請求書]** を選択します。

請求書の詳細については、「[Microsoft Azure の課金内容の確認](billing-understand-your-bill.md)」をご覧ください。 コスト管理に関するヘルプについては、[Azure の課金とコスト管理で想定外の料金を防ぐ](billing-getting-started.md)方法に関するページを参照してください。

## <a name="get-your-subscriptions-invoices-in-email"></a>サブスクリプションの請求書をメールで受け取る

オプトインして、Azure の請求書を電子メールで受取る受信者を追加設定することができます。 この機能は、サポート オファー、エンタープライズ契約、Azure イン オープン プランなどの特定のサブスクリプションでは利用できない場合があります。

1. [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ページで、自分のサブスクリプションを選択します。 自分が所有するサブスクリプションごとにオプトインします。 **[請求書]** をクリックし、 **[請求書を送信する]** をクリックします。

    ![オプトイン フローを示すスクリーン ショット](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. **[オプトイン]** をクリックして、条項に同意します。

    ![手順 2 オプトイン フローを示すスクリーン ショット](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. 契約書に同意したら、追加の受信者を構成できます。 受信者を削除すると、その電子メール アドレスも削除されます。 変更する場合には、再度追加する必要があります。

    ![手順 3 オプトイン フローを示すスクリーン ショット](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

以上の手順を実行してもメールが届かない場合は、[プロファイルの通信設定](https://account.windowsazure.com/profile)のメール アドレスが正しいことを確認してください。

## <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>サブスクリプションの請求書のメールによる取得をオプトアウトする

請求書をメールで受け取らないようにするには、前述の手順に従って **[電子メールで送信した請求書からオプトアウトする]** をクリックします。 このオプションにより、メールで請求書を受信するよう設定されているすべてのメール アドレスが削除されます。 オプトアウトを解除する場合、受信者を再構成できます。

 ![オプトアウト フローを示すスクリーン ショット](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

<!-- Does following section apply to MPA too? -->
## <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Microsoft 顧客契約の請求書をメールで受け取る

Microsoft 顧客契約を結んでいる場合は、メールの請求書の取得をオプトインできます。 すべての課金プロファイルの所有者、共同作成者、閲覧者、および請求書管理者が、メールで請求書を取得します。 閲覧者は、メールの請求書の設定を更新できません。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. "**コスト管理 + 請求**" を検索します。
1. 課金プロファイルを選択します。 アクセス方法によっては、最初に請求先アカウントを選択することが必要な場合があります。
1. **[設定]** で **[プロパティ]** を選択します。
1. **[請求書を電子メールで送信]** で、 **[請求書を電子メールで送信の設定の更新]** を選択します。

    ![メールの請求書のプロパティを示すスクリーンショット](./media/billing-download-azure-invoice/billingprofile-email.png)

1. **[オプトイン]** を選択します。
1. **[Update]** をクリックします。

## <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Microsoft 顧客契約の請求書のメールによる取得をオプトアウトする

請求書をメールで受け取らないようにするには、前述の手順に従って **[オプトアウトする]** をクリックします。すべての所有者、共同作成者、閲覧者、および請求書管理者も、メールの請求書の取得からオプトアウトされます。 閲覧者の場合は、メールの請求書の基本設定を変更できません。

## <a name="check-your-billing-account-type"></a>課金アカウントの種類を確認する
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次の手順

請求書および料金の詳細については、以下を参照してください。

- [Microsoft Azure の使用量と料金の表示とダウンロード](billing-download-azure-daily-usage.md)
- [Microsoft Azure の課金内容の確認](billing-understand-your-bill.md)
- [Azure 請求書の用語について](billing-understand-your-invoice.md)
- [Microsoft Azure の詳細な使用状況の用語を理解する](billing-understand-your-usage.md)
- [組織の Azure の価格を表示する](billing-ea-pricing.md)

Microsoft 顧客契約を結んでいる場合は、以下を参照してください。

- [課金プロファイルの請求書の料金を理解する](billing-mca-understand-your-bill.md)
- [課金プロファイルの請求書の用語を理解する](billing-mca-understand-your-invoice.md)
- [課金プロファイルの Azure の使用量と料金のファイルを理解する](billing-mca-understand-your-usage.md)
- [課金プロファイルの税務書類を表示およびダウンロードする](billing-mca-download-tax-document.md)
- [組織の Azure の価格を表示する](billing-ea-pricing.md)
